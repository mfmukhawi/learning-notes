# Hibernate & Spring Data JPA Notes

## 🔹 What is Hibernate?
- ORM (Object-Relational Mapping) framework for Java
- Maps Java objects to DB tables
- Features: caching, transactions, lazy loading, HQL

## 🔹 What is Spring Data JPA?
- Simplifies DB access (abstraction over SQL)
- Built on top of JPA, works with Hibernate
- not good idea for batch operations
- allow us to focus on business logic

## 🔹 GenerationType
- `TABLE`: Simulated ID table
- `SEQUENCE`: DB sequence
- `IDENTITY`: DB auto-increment
- `UUID`: Unique random ID
- `AUTO`: Let JPA decide
---
## 🔹 How to Define Primary Key with Sequence
```java
@Id
@SequenceGenerator(
    name = "your_generator_name",   // Used in @GeneratedValue
    sequenceName = "your_sequence", // DB sequence name
    allocationSize = 1,             // Increment step
    initialValue = 43900001         // Starting value
)
@GeneratedValue(
    strategy = GenerationType.SEQUENCE,
    generator = "your_generator_name"
)
private Long id;

```
---
## 🔹 How to Add Unique Constraint to a Column

To set a unique constraint with a custom name,
use `@UniqueConstraint` in the `@Table` annotation, and remove `unique = true` from `@Column`.

### ✅ Example:
```java
@Entity
@Table(
        uniqueConstraints = {
                @UniqueConstraint(
                        name = "student_email_unique",
                        columnNames = "email"
                )
        }
)
public class Student {

    @Column(name = "email", nullable = false)
    private String email;
}
```





[//]: # (## 🔹 `@Column` Annotation)

[//]: # ()
[//]: # (Defines column-specific details when mapping Java fields to database columns.)

[//]: # ()
[//]: # (```java)

[//]: # (public @interface Column {)

[//]: # (    String name&#40;&#41; default "";           // Custom column name)

[//]: # (    boolean unique&#40;&#41; default false;     // Whether the column is unique)

[//]: # (    boolean nullable&#40;&#41; default true;    // Allow null values)

[//]: # (    boolean insertable&#40;&#41; default true;  // Include in SQL INSERT)

[//]: # (    boolean updatable&#40;&#41; default true;   // Include in SQL UPDATE)

[//]: # (    String columnDefinition&#40;&#41; default "";// Custom SQL column definition)

[//]: # (    String table&#40;&#41; default "";          // Table name if different from entity)

[//]: # (    int length&#40;&#41; default 255;           // Length for String columns)

[//]: # (    int precision&#40;&#41; default 0;          // Precision for decimal numbers)

[//]: # (    int scale&#40;&#41; default 0;              // Scale for decimal numbers)

[//]: # (})

[//]: # (```)

[//]: # ()
[//]: # (---)

[//]: # ()
[//]: # (## 💡 Example Usage)

[//]: # (```java)

[//]: # (@Column&#40;name = "user_name", nullable = false, length = 100, unique = true&#41;)

[//]: # (private String username;)

[//]: # (```)


---
## 🔹 What is a Repository (data access layer )?

A **repository** is used to manage data access. It provides methods to get,
save, and update data without dealing directly with the database.

the goal of spring data repository abstraction is to significantly reduce the amount of boilerplate code 
required ti implement data access layers for various persistence stores

we need crate repository extend this :
. JPARepository (extend) --> PagingAndSortingRepository (extend) --> CrudRepository 
- JPARepository --> has jpa methods flush and batch 
- PagingAndSortingRepository --> has pagination and sorting 
- CrudRepository --> has crud functions

- It separates database logic from the rest of the application.
- Makes data access easier and cleaner.

## 🔹 What is a Repository (Data Access Layer)?
- manages data access. It provides methods to get, save, and update data without directly dealing with the database.
- (goal) : spring data repositories help reduce boilerplate code for data access.

To create a repository, extend one of these:
- **JPARepository**: Includes JPA methods like `flush` and `batch`.
- **PagingAndSortingRepository**: Supports `pagination` and `sorting`.
- **CrudRepository**: Provides basic `CRUD operations`.

Repositories separate database logic from the rest of the app and make data access easier.

### 💡 Example:
```java
public interface StudentRepository extends JpaRepository<Student, Long> {
    // Add custom queries here
}

```
---
## 🔹 How to Write Queries and JPQL

Spring Data JPA allows you to create queries just by defining method names using special keywords.

---

### 🔸 Query Keywords

| Keyword               | Description                               |
|-----------------------|-------------------------------------------|
| `findBy...`           | Start of method name for queries          |
| `And` / `Or`          | Combine multiple conditions               |
| `IgnoreCase`          | Case-insensitive match                    |
| `IsAfter` / `IsBefore`| Date comparisons                          |
| `IsBetween`           | Value is between two values               |
| `IsEmpty` / `IsNotEmpty` | Collection is empty / not empty        |
| `IsNull` / `IsNotNull`| Check for null / not null                 |
| `Like` / `NotLike`    | Pattern matching                          |
| `Containing` / `NotContaining` | LIKE %value% / NOT LIKE %value% |
| `StartingWith` / `EndingWith` | LIKE value% / %value              |
| `In` / `NotIn`        | Check if value is (not) in a list         |
| `True` / `False`      | Boolean checks                            |
| `GreaterThan` / `LessThan` | Comparisons                        |
| `GreaterThanEqual` / `LessThanEqual` | Inclusive comparisons     |
| `OrderBy...Asc` / `OrderBy...Desc` | Sorting results              |
---

### 💡 Example Query Methods

```java
List<User> findByUsernameIgnoreCase(String username);
List<Event> findByDateIsAfter(LocalDate date);
List<Product> findByPriceIsBetween(BigDecimal min, BigDecimal max);
List<Customer> findByEmailIsNull();
```

🔸 JPQL (Java Persistence Query Language)
- JPQL is used for more complex custom queries.

- 📌 Parameter Binding Styles : 
  - By position
  - By named parameter
  - By object property (SpEL)
```java

@Query("SELECT s FROM Student s WHERE s.email = ?1")
Optional<Student> findByEmail(String email);

@Query("SELECT s from Student s where s.email = :email")
Optional<Student> findByEmail(@Param("email") String email);

@Query("UPDATE Student s SET s.firstName = :#{#student.firstName}, s.age = :#{#student.age} WHERE s.id = :#{#student.id}")
int updateById(@Param("student") Student student);
```

## 🔹 When to Use @Modifying and @Transactional?

- Use `@Modifying` for update/delete queries in repositories.
- Use `@Transactional` to wrap write operations in a transaction.
- Both are needed for update/delete queries to work properly.
- Best practice: put `@Transactional` in the service layer.


