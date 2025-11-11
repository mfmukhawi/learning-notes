# 🧾 Guide to Using Projections and Specifications in Spring Data JPA

## 🚀 Overview


- Spring Data JPA is a powerful framework that supports dynamic, type-safe queries using Specifications and Projections. 
- However, combining both Specifications for filtering and Projections for returning DTOs — isn't supported out of the box.
- This guide introduces approach to use them together via a utility class called `ProjectionQueryBuilder`.
---
## 🛠️ Problem Statement
Spring Data JPA supports:
- ✅ Specifications for dynamic filtering.
- ✅ Projections for selective field fetching (via @Query, interfaces, or constructor expressions).
- ❌ Combining Specifications and Projections dynamically in a single query is not directly supported by Spring Data JPA.
---

## 🎯 Goal

- ✅ Enable use of both filtering with JPA Specifications and DTO projections 
- ✅ Pagination and sorting
- ✅ Custom queries using Criteria API

---


## 🧠 Solution Approach

📌 We solve the problem by using the ProjectionQueryBuilder class. This utility allows us to:
- Dynamically apply Specifications for filtering.
- Map the results to a custom DTO using JPA Criteria API.
- Support joins, pagination, and sorting in a type-safe manner.

---
### 🧩 Key Components

| Component | Purpose |
|----------|---------|
| `ProjectionQueryBuilder` | A reusable utility for building projection queries using the JPA Criteria API |
| `CustomStudentRepository` | A custom repository interface for dynamic projections |
| `CustomStudentRepositoryImpl` | The implementation that uses the ProjectionQueryBuilder to handle dynamic projections and filtering. |
| `StudentSimpleProjection` | A DTO class for mapping the result of a projection query |

### 📊 Method Descriptions

| Method                                | Purpose                                                                                          |
|--------------------------------------|--------------------------------------------------------------------------------------------------|
| `.withSpecification(spec)`           | Applies dynamic filtering using a `Specification<T>`.                                            |
| `.withProjection(this::buildStudentProjection)` | Transforms entities into DTOs using a constructor expression.                                  |
| `.withPageable(pageable)`            | Applies pagination and sorting using a `Pageable` object.                                        |
| `.getResultPage()`                   | Executes the query and returns a paged result (`Page<T>`) of projected DTOs.                     |
| `.getResultList()`                   | Executes the query without pagination and returns a `List<T>` of projected DTOs.                 |
| `buildStudentProjection`             | Maps `Student` and `StudentIdCard` data to `StudentSimpleProjection` DTO using Criteria API.     |
| `.withJoin(joinFunction)	`                   | Adds JOIN operations to the query using a function that defines the join path.|
| `..withResultTransformer(projection -> new CustomWrapper(projection))`             |Applies post-processing transformation to the projection results. |

 ### ⚠️ Notes
 - Hibernate 5 does not support nested projections. Use the withResultTransformer method to handle this if needed.
---

## 📎 Summary

The ProjectionQueryBuilder utility provides a clean, maintainable solution for 
combining Specifications and DTO Projections in Spring Data JPA.
<br/>
<br/>
When to Use : 
- When you need dynamic filtering combined with DTO projections.
- When you want to avoid the complexity of native queries or JPQL.
- When you need flexible, paginated, and sorted query results with type safety.

By following this pattern, your queries become flexible, efficient, and aligned with clean coding practices.


---
# 🛠 Implementation Guide (Full Code)
## (1) ProjectionQueryBuilder Class

- use to build and execute the query with the projection and specification:

```java

/**
 * Generic builder for type-safe projection queries using JPA Criteria API.
 *
 * Features:
 * - Dynamic filtering via Specification
 * - DTO-based projections
 * - Sorting, pagination, and joins
 * - Optional post-processing transformation
 *
 * @param <EntityType>     the entity type to query
 * @param <ProjectionType> the DTO to map results into
 */
public final class ProjectionQueryBuilder<EntityType, ProjectionType> {

    // === Required fields ===
    private final EntityManager entityManager;
    private final Class<EntityType> entityClass;
    private final Class<ProjectionType> projectionClass;

    // === Optional configuration ===
    private Specification<EntityType> specification;
    private Sort sort;
    private Pageable pageable;
    private Function<Root<EntityType>, Selection<? extends ProjectionType>> projectionBuilder;
    private final List<Function<Root<EntityType>, Join<?, ?>>> joins = new ArrayList<>();
    private Function<ProjectionType, ?> resultTransformer;

    // === Private constructor ===
    private ProjectionQueryBuilder(
            @NonNull EntityManager entityManager,
            @NonNull Class<EntityType> entityClass,
            @NonNull Class<ProjectionType> projectionClass
    ) {
        this.entityManager = Objects.requireNonNull(entityManager, "EntityManager must not be null");
        this.entityClass = Objects.requireNonNull(entityClass, "Entity class must not be null");
        this.projectionClass = Objects.requireNonNull(projectionClass, "Projection class must not be null");
    }

    // === Static factory method ===

    public static <EntityType, ProjectionType> ProjectionQueryBuilder<EntityType, ProjectionType> create(
            @NonNull EntityManager entityManager,
            @NonNull Class<EntityType> entityClass,
            @NonNull Class<ProjectionType> projectionClass
    ) {
        return new ProjectionQueryBuilder<>(entityManager, entityClass, projectionClass);
    }

    // === Fluent configuration methods ===

    public ProjectionQueryBuilder<EntityType, ProjectionType> withSpecification(Specification<EntityType> specification) {
        this.specification = specification;
        return this;
    }

    public ProjectionQueryBuilder<EntityType, ProjectionType> withSort(Sort sort) {
        this.sort = sort;
        return this;
    }

    public ProjectionQueryBuilder<EntityType, ProjectionType> withPageable(Pageable pageable) {
        this.pageable = pageable;
        if (pageable.getSort().isSorted()) {
            this.sort = pageable.getSort(); // Use sort from pageable if provided
        }
        return this;
    }

    public ProjectionQueryBuilder<EntityType, ProjectionType> withProjection(
            @NonNull Function<Root<EntityType>, Selection<? extends ProjectionType>> projectionBuilder) {
        this.projectionBuilder = Objects.requireNonNull(projectionBuilder, "Projection builder must not be null");
        return this;
    }

    public ProjectionQueryBuilder<EntityType, ProjectionType> withJoin(
            @NonNull Function<Root<EntityType>, Join<?, ?>> joinFunction) {
        this.joins.add(Objects.requireNonNull(joinFunction, "Join function must not be null"));
        return this;
    }

    public <ResultType> ProjectionQueryBuilder<EntityType, ProjectionType> withResultTransformer(
            @NonNull Function<ProjectionType, ResultType> transformer) {
        this.resultTransformer = Objects.requireNonNull(transformer, "Transformer must not be null");
        return this;
    }

    // === Execution methods ===

    /**
     * Executes the query and returns the full result list (non-paginated).
     */
    public List<ProjectionType> getResultList() {
        try {
            CriteriaQuery<ProjectionType> query = buildBaseQuery(sort);
            return entityManager.createQuery(query).getResultList();
        } catch (Exception e) {
            throw new ProjectionQueryException("Failed to execute projection query", e);
        }
    }

    /**
     * Executes a paginated projection query and returns a Page of results.
     */
    public Page<ProjectionType> getResultPage() {
        validatePageable(this.pageable);
        try {
            List<ProjectionType> content = fetchPageContent(this.pageable);
            long total = fetchTotalCount(content, this.pageable);
            return new PageImpl<>(content, this.pageable, total);
        } catch (Exception e) {
            throw new ProjectionQueryException("Failed to execute paginated query", e);
        }
    }

    /**
     * Optimized count query for paginated requests.
     */
    public long fetchTotalCount(List<ProjectionType> content, Pageable pageable) {
        // Skip count query if we can infer total from first page result size
        if ((pageable.getOffset() == 0 && content.size() < pageable.getPageSize()) || content.isEmpty()) {
            return content.size();
        }

        try {
            CriteriaBuilder cb = entityManager.getCriteriaBuilder();
            CriteriaQuery<Long> countQuery = cb.createQuery(Long.class);
            Root<EntityType> root = countQuery.from(entityClass);

            applyJoins(root);
            countQuery.select(cb.count(root));
            applySpecification(cb, countQuery, root);

            return entityManager.createQuery(countQuery).getSingleResult();
        } catch (Exception e) {
            throw new ProjectionQueryException("Failed to execute count query", e);
        }
    }

    // === Internal helpers ===

    /**
     * Builds the base CriteriaQuery with optional joins, projection, filtering, and sorting.
     */
    private CriteriaQuery<ProjectionType> buildBaseQuery(Sort sort) {
        CriteriaBuilder cb = entityManager.getCriteriaBuilder();
        CriteriaQuery<ProjectionType> query = cb.createQuery(projectionClass);
        Root<EntityType> root = query.from(entityClass);

        applyJoins(root);
        query.select(Objects.requireNonNull(projectionBuilder, "Projection builder must be set").apply(root));
        applySpecification(cb, query, root);
        applySorting(cb, query, root, sort);

        return query;
    }

    /**
     * Fetches paged content using JPA's firstResult/maxResults.
     */
    private List<ProjectionType> fetchPageContent(Pageable pageable) {
        CriteriaQuery<ProjectionType> query = buildBaseQuery(pageable.getSort());
        return entityManager.createQuery(query)
                .setFirstResult((int) pageable.getOffset())
                .setMaxResults(pageable.getPageSize())
                .getResultList();
    }

    /**
     * Applies all configured join functions to the query root.
     */
    private void applyJoins(Root<EntityType> root) {
        joins.forEach(join -> join.apply(root));
    }

    /**
     * Applies the specification filter predicate if set.
     */
    private void applySpecification(CriteriaBuilder cb, CriteriaQuery<?> query, Root<EntityType> root) {
        if (specification != null) {
            Predicate predicate = specification.toPredicate(root, query, cb);
            if (predicate != null) {
                query.where(predicate);
            }
        }
    }

    /**
     * Applies sorting to the CriteriaQuery using property paths.
     */
    private void applySorting(CriteriaBuilder cb, CriteriaQuery<?> query, Root<EntityType> root, Sort sort) {
        if (sort == null || sort.isUnsorted()){
            return;
        }

        List<Order> orders = sort.stream()
                .map(order -> {
                    Path<?> path = resolvePropertyPath(root, order.getProperty());
                    return order.isAscending() ? cb.asc(path) : cb.desc(path);
                })
                .collect(Collectors.toList());

        query.orderBy(orders);
    }

    /**
     * Resolves nested property paths like "user.address.city".
     */
    private Path<?> resolvePropertyPath(From<?, ?> root, String propertyPath) {
        String[] parts = propertyPath.split("\\.");
        Path<?> path = root.get(parts[0]);
        for (int i = 1; i < parts.length; i++) {
            path = path.get(parts[i]);
        }
        return path;
    }

    /**
     * Validates that the given pageable is well-formed.
     */
    private void validatePageable(Pageable pageable) {
        if (pageable == null) {
            throw new IllegalStateException("Pageable must be set using withPageable()");
        }
        if (pageable.getPageSize() <= 0) {
            throw new IllegalArgumentException("Page size must be positive");
        }
        if (pageable.getOffset() < 0) {
            throw new IllegalArgumentException("Offset must not be negative");
        }
    }

    // === Custom exception ===

    /**
     * Runtime exception wrapper for query-related errors.
     */
    public static final class ProjectionQueryException extends RuntimeException {
        public ProjectionQueryException(String message, Throwable cause) {
            super(message, cause);
        }
    }
}
```

## Step (2) - Define a Projection DTO to represent the data you want to retrieve
```java
@Data
@AllArgsConstructor
@Builder
public class StudentSimpleProjection {
    private Long id;
    private String firstName;
    private String email;
    private Integer age;
    private StudentIdCardProjection StudentIdCard;

  @Data
    @AllArgsConstructor
    public static class StudentIdCardProjection {
        private Long id;
        private String cardNumber;
    }
}

```

## (3) - Define a Custom Repository Interface +  Implement the Custom Repository

```java
public interface CustomStudentRepository {

    List<StudentSimpleProjection> findProjectedStudentsBySpec(Specification<Student> spec) ;
    List<StudentSimpleProjection> findProjectedStudentsBySpecAndSortedBy(Specification<Student> spec , Sort sort) ;
    Page<StudentSimpleProjection> findProjectedStudentsBySpec(Specification<Student> spec, Pageable pageable);
}
@Repository
public class CustomStudentRepositoryImpl implements CustomStudentRepository {

  @PersistenceContext
  private EntityManager entityManager;

  @Override
  public List<StudentSimpleProjection> findProjectedStudentsBySpec(Specification<Student> spec) {
    return ProjectionQueryBuilder.create(entityManager, Student.class, StudentSimpleProjection.class)
            .withSpecification(spec)
            .withProjection(this::buildStudentProjection)
            .getResultList();
  }
  @Override
  public List<StudentSimpleProjection> findProjectedStudentsBySpecAndSortedBy(Specification<Student> spec, Sort sort) {
    return ProjectionQueryBuilder.create(entityManager, Student.class, StudentSimpleProjection.class)
            .withSpecification(spec)
            .withSort(sort)
            .withProjection(this::buildStudentProjection)
            .getResultList();
  }

  @Override
  public Page<StudentSimpleProjection> findProjectedStudentsBySpec(Specification<Student> spec, Pageable pageable) {
    return ProjectionQueryBuilder.create(entityManager, Student.class, StudentSimpleProjection.class)
            .withSpecification(spec)
            .withProjection(this::buildStudentProjection)
            .withPageable(pageable)
            .getResultPage();
  }

  private Selection<StudentSimpleProjection> buildStudentProjection(Root<Student> root) {
    CriteriaBuilder cb = entityManager.getCriteriaBuilder();
    Join<Student, StudentIdCard> cardJoin = root.join(Student_.STUDENT_ID_CARD, JoinType.LEFT);
    

    return cb.construct(
            StudentSimpleProjection.class,
            root.get(Student_.ID),
            root.get(Student_.FIRST_NAME),
            root.get(Student_.EMAIL),
            root.get(Student_.AGE),
            // nested not suport for hibernet 5 use flat  just use in hibernet 6 
            cb.construct(
                    StudentSimpleProjection.StudentIdCardProjection.class,
                    cardJoin.get(StudentIdCard_.ID),
                    cardJoin.get(StudentIdCard_.CARD_NUMBER)
            )
    );
  }
}

```

## (4) - Add CustomStudentRepository to Main Repository

```java

public interface StudentRepository extends 
        JpaRepository<Student, Long> ,
        JpaSpecificationExecutor<Student>,
        CustomStudentRepository {
    
}
```
## (5) -  Use Main Repository in the Service Layer

```java

    public Page<StudentSimpleProjection> search(StudentSearchDto dto) {
      Specification<Student> spec = Specification.
              where(StudentSpecification.hasFirstName(dto.getFirstName()))
              .or(StudentSpecification.hasEmail(dto.getEmail()))
              .or(StudentSpecification.isOlderThan(dto.getAge()));

      Sort sort = SortingUtils.buildSort(dto.getDirection(),dto.getSortedBy());

      return studentRepository.findProjectedStudentsBySpecAndSortedBy(spec,sort);
    }
```