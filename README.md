# Hibernate One-To-Many Association Mapping with Spring Boot

## Overview

This project demonstrates how to implement **One-To-Many Association Mapping** using **Spring Boot**, **Spring Data JPA**, and **Hibernate**.

The project contains two entities:

* User (Parent Entity)
* Task (Child Entity)

A single user can have multiple tasks, while each task belongs to only one user.

The project also covers:

* Bidirectional One-To-Many Mapping
* Relationship Ownership
* Cascade Operations
* Lazy Loading
* Hibernate Session Management
* JPA Repository Operations

---

## Technologies Used

* Java 17+
* Spring Boot
* Spring Data JPA
* Hibernate ORM
* MySQL Database
* Maven

---

## Project Structure

text
src
 ├── entity
 │    ├── User.java
 │    └── Task.java
 │
 ├── repository
 │    ├── UserRepository.java
 │    └── TaskRepository.java
 │
 ├── service
 │
 └── test
```

---

## Entity Relationship

```text
User (1) -------------------- (*) Task

One User can have multiple Tasks
One Task belongs to one User
```

### Database Tables

#### USER

| Column |
| ------ |
| userid |
| name   |

#### TASK

| Column      |
| ----------- |
| taskid      |
| taskname    |
| userid (FK) |

---

## User Entity

```java
@Entity
public class User {

    @Id
    private Integer userid;

    private String name;

    @OneToMany(
            mappedBy = "user",
            cascade = CascadeType.ALL)
    private List<Task> taskList = new ArrayList<>();
}
```

---

## Task Entity

```java
@Entity
public class Task {

    @Id
    private Integer taskid;

    private String taskname;

    @ManyToOne
    @JoinColumn(name = "userid")
    private User user;
}
```

---

## Relationship Ownership

The `Task` entity owns the relationship because it contains the foreign key column.

```java
@ManyToOne
@JoinColumn(name = "userid")
private User user;
```

The `User` entity is the inverse side:

```java
@OneToMany(mappedBy = "user")
private List<Task> taskList;
```

---

## Saving Data

```java
User user = new User(1, "John");

Task t1 = new Task(101, "Learn Hibernate");
Task t2 = new Task(102, "Learn Spring Boot");

t1.setUser(user);
t2.setUser(user);

user.getTaskList().add(t1);
user.getTaskList().add(t2);

userRepository.save(user);
```

Because of:

```java
cascade = CascadeType.ALL
```

Saving the User automatically saves all associated Tasks.

---

## Lazy Loading

By default:

```java
@OneToMany
```

uses Lazy Loading.

Tasks are fetched only when:

```java
user.getTaskList()
```

is called.

---

## Common Exception

### LazyInitializationException

```text
Cannot lazily initialize collection
```

This occurs when Hibernate tries to load a lazy collection after the Session has been closed.

### Solution

```java
@Transactional
public void getUserTasks() {
    User user = userRepository.findById(1).get();

    user.getTaskList()
        .forEach(System.out::println);
}
```

The `@Transactional` annotation keeps the Hibernate Session open during method execution.

---

## Running the Project

### Clone Repository

```bash
git clone <repository-url>
```

### Navigate to Project

```bash
cd project-name
```

### Configure Database

Update:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/your_db
spring.datasource.username=root
spring.datasource.password=password
```

### Run Application

```bash
mvn spring-boot:run
```

---

## Learning Outcomes

* Understanding One-To-Many Mapping
* Understanding Relationship Ownership
* Working with Cascade Operations
* Understanding Lazy vs Eager Fetching
* Handling LazyInitializationException
* Working with Hibernate and Spring Data JPA

---

## Author

Mitali Yadav

Java | Spring Boot | Hibernate | Microservices Developer
