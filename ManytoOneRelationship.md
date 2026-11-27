# ManytoOne – Hibernate One-to-Many / Many-to-One Mapping (Course → Students)

## 1. Project Overview

This project demonstrates a **bidirectional One-to-Many / Many-to-One** relationship using Hibernate ORM and JPA annotations.

One **Course** can have many **Students**, and each **Student** belongs to one **Course**.

### Technologies Used

- Java 21
- Hibernate ORM 6.1.5.Final
- JPA (Jakarta Persistence) Annotations
- Maven
- MySQL
- Eclipse IDE

### Project Structure

```text
ManytoOne
│
├── src/main/java
│   │
│   ├── com.hibernate.entity
│   │   ├── Course.java
│   │   └── Student.java
│   │
│   ├── com.hibernate.MainApp
│   │   └── Main.java
│   │
│   └── com.hibernate.util
│       └── HibernateUtil.java
│
├── src/main/resources
│   └── hibernate.cfg.xml
│
├── src/test/java
├── Maven Dependencies
├── target
└── pom.xml
```

---

## 2. Relationship Explained

### Real-World Example

- Java Course → prasanna
- Java Course → tharun
- Java Course → ramu

### Relationship Diagram

```text
               COURSE
          ------------------
          courseId (PK)
          courseName
          ------------------
                  |
             One-to-Many
                  |
        ---------------------
        |         |         |
     Student   Student   Student
        |         |         |
    prasanna   tharun     ramu
```

### Database Tables

**Course Table**

| courseId | courseName |
|---|---|
| 1 | java |

**Student Table**

| stuId | stuName | courseId_courseId (FK) |
|---|---|---|
| 1 | prasanna | 1 |
| 2 | tharun | 1 |
| 3 | ramu | 1 |

Here:

- `courseId` is the primary key in Course.
- `stuId` is the primary key in Student.
- The foreign key lives in the **Student** table and points to Course.

> **Note:** Because `@JoinColumn` has no `name`, Hibernate's default column name is `<fieldName>_<referencedPK>`, which gives `courseId_courseId`. See the review notes for how to give it a cleaner name.

---

## 3. Course.java (Parent / Inverse Side)

### Explanation

Course is the **parent entity** and the **inverse (non-owning) side** of the relationship.

| Annotation | Purpose |
|---|---|
| `@Entity` | Maps the class to a database table |
| `@Id` | Marks the primary key |
| `@GeneratedValue(strategy = IDENTITY)` | MySQL auto-increment primary key |
| `@Column` | Maps the field to a column |
| `@OneToMany` | One course has many students |

### Important Annotation

```java
@OneToMany(mappedBy = "courseId", cascade = CascadeType.ALL)
List<Student> Students;
```

**mappedBy = "courseId"**

Refers to the field named `courseId` inside `Student.java` (the `Course` object). That field owns the relationship, so the foreign key column is created in the Student table, not the Course table.

**cascade = CascadeType.ALL**

Saving, updating, or deleting a Course is automatically applied to its students. This is why `session.save(course1)` also inserts all three students.

### Complete Code – Course.java

```java
package com.hibernate.entity;

import jakarta.persistence.*;

import java.util.*;

@Entity
public class Course
{

    @Id
    @Column
    @GeneratedValue(strategy = GenerationType.IDENTITY)

    private int courseId;

    @Column
    private String courseName;

    @OneToMany(mappedBy ="courseId" , cascade = CascadeType.ALL)
    List<Student> Students;

    public int getCourseId() {
        return courseId;
    }

    public void setCourseId(int courseId) {
        this.courseId = courseId;
    }

    public String getCourseName() {
        return courseName;
    }

    public void setCourseName(String courseName) {
        this.courseName = courseName;
    }

    public List<Student> getStudents() {
        return Students;
    }

    public void setStudents(List<Student> students) {
        Students = students;
    }
}
```

---

## 4. Student.java (Child / Owning Side)

### Explanation

Student is the **child entity** and the **owning side** of the relationship, because it holds the foreign key.

| Annotation | Purpose |
|---|---|
| `@Entity` | Maps the class to a database table |
| `@Id` | Marks the primary key |
| `@GeneratedValue(strategy = IDENTITY)` | Auto-increment primary key |
| `@Column` | Maps the field to a column |
| `@ManyToOne` | Many students belong to one course |
| `@JoinColumn` | Creates the foreign key column |

### Important Annotation

```java
@ManyToOne
@JoinColumn
private Course courseId;
```

### Complete Code – Student.java

```java
package com.hibernate.entity;

import jakarta.persistence.*;

@Entity

public class Student
{
    @Id
    @Column
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int stuId;

    @Column
    private String stuName;

    @ManyToOne

    @JoinColumn
    private Course courseId;

    public int getStuId() {
        return stuId;
    }

    public void setStuId(int stuId) {
        this.stuId = stuId;
    }

    public String getStuName() {
        return stuName;
    }

    public void setStuName(String stuName) {
        this.stuName = stuName;
    }

    public Course getCourseId() {
        return courseId;
    }

    public void setCourseId(Course courseId) {
        this.courseId = courseId;
    }
}
```

---

## 5. HibernateUtil.java

### Explanation

A utility class that builds the `SessionFactory` **once** (in a `static` block) and exposes it through `getSessionFactory()`. Building it once is the recommended approach because a `SessionFactory` is expensive to create.

### Complete Code – HibernateUtil.java

```java
package com.hibernate.util;

import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class HibernateUtil

{

    private static SessionFactory sessionfactory;

    static {

        Configuration configuration = new Configuration();
        configuration.configure("hibernate.cfg.xml");

        sessionfactory = configuration.buildSessionFactory();
        }


    public static SessionFactory getSessionFactory()
    {
        return sessionfactory;


    }


}
```

---

## 6. Main.java

### Explanation

Main creates one course with three students and saves them in a single call.

### Insertion Process

```text
Get SessionFactory
        |
Open Session
        |
Begin Transaction
        |
Create Course ("java")
        |
Create 3 Students, set course on each
        |
Add students to course's list
        |
session.save(course)   ← cascades to students
        |
Commit Transaction
        |
Close Session
```

### Complete Code – Main.java

```java
package com.hibernate.MainApp;

import java.util.ArrayList;
import java.util.List;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;


import com.hibernate.entity.Course;
import com.hibernate.entity.Student;
import com.hibernate.util.HibernateUtil;

public class Main

{

    public static void main(String[] args) {

        SessionFactory sessionfactory = HibernateUtil.getSessionFactory();

        Session session = sessionfactory.openSession();

        Transaction transaction = session.beginTransaction();

        Course course1 = new Course();
        course1.setCourseName("java");


        Student stu1 = new Student();
        stu1.setStuName("prasanna");
        stu1.setCourseId(course1);

        Student stu2 = new Student();
        stu2.setStuName("tharun");
        stu2.setCourseId(course1);

        Student stu3 = new Student();
        stu3.setStuName("ramu");
        stu3.setCourseId(course1);

        List<Student> students = new  ArrayList<>();

        students.add(stu1);
        students.add(stu2);
        students.add(stu3);
        course1.setStudents(students);

        session.save(course1);
        System.out.println("sucessfully inserted");
        transaction.commit();
        session.close();
    }
}
```

### Why both sides are set

```java
stu1.setCourseId(course1);        // owning side → writes the foreign key
course1.setStudents(students);    // inverse side → needed for cascade to reach the students
```

- The **owning side** (`Student.courseId`) decides what goes into the foreign key column.
- The **inverse side** (`Course.Students`) with `cascade = ALL` is what makes Hibernate save the students automatically.

---

## 7. hibernate.cfg.xml

### Explanation

| Property | Explanation |
|---|---|
| `hibernate.connection.driver_class` | MySQL JDBC driver |
| `hibernate.connection.username` | Database username |
| `hibernate.connection.password` | Database password |
| `hibernate.connection.url` | Database URL (`manytoone` database) |
| `hibernate.dialect` | SQL dialect for MySQL |
| `hibernate.hbm2ddl.auto` | `update` creates/updates tables automatically |
| `mapping class` | Registers the entity classes |

### Complete Code – hibernate.cfg.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>

    <session-factory>

        <property name="hibernate.connection.driver_class">com.mysql.cj.jdbc.Driver</property>

        <property name="hibernate.connection.username">root</property>

        <!-- Replace with your own password. Never commit a real password. -->
        <property name="hibernate.connection.password">your_password</property>

        <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/manytoone</property>

        <property name="hibernate.dialect">org.hibernate.dialect.MySQL8Dialect</property>

        <property name="hibernate.hbm2ddl.auto">update</property>


        <mapping class="com.hibernate.entity.Student"/>
        <mapping class="com.hibernate.entity.Course"/>

    </session-factory>

</hibernate-configuration>
```

> The MySQL database `manytoone` must exist before you run the project (`CREATE DATABASE manytoone;`). Hibernate creates the tables but not the database itself.

---

## 8. pom.xml

### Explanation

Dependencies used:

1. **mysql-connector-j** – MySQL JDBC driver (the current artifact name).
2. **hibernate-core** – Hibernate ORM (also brings in the Jakarta Persistence API).

### Complete Code – pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>hibernate</groupId>
  <artifactId>ManytoOne</artifactId>
  <version>0.0.1-SNAPSHOT</version>

  <dependencies>

    <dependency>
      <groupId>com.mysql</groupId>
      <artifactId>mysql-connector-j</artifactId>
      <version>9.1.0</version>
    </dependency>

    <dependency>
      <groupId>org.hibernate</groupId>
      <artifactId>hibernate-core</artifactId>
      <version>6.1.5.Final</version>
    </dependency>

  </dependencies>
</project>
```

---

## 9. Important Concepts

| Concept | Meaning |
|---|---|
| Entity | Java class mapped to a database table |
| `@Id` | Primary key |
| `@GeneratedValue` | Auto-generates the ID |
| `@OneToMany` | One parent has many children (inverse side here) |
| `@ManyToOne` | Many children belong to one parent (owning side here) |
| `mappedBy` | Names the field on the other entity that owns the relationship |
| `@JoinColumn` | Defines the foreign key column |
| `cascade` | Applies operations on the parent to its children |
| Owning side | The side that holds the foreign key |
| Inverse side | The side that uses `mappedBy` |
| Session | Talks to the database through Hibernate |
| SessionFactory | Creates sessions; built once per application |
| Transaction | Groups database operations; `commit()` saves, `rollback()` cancels |

---

## 10. Important Notes

1. Course is the parent entity; Student is the child entity.
2. The foreign key is stored in the **Student** table.
3. `@OneToMany` is on Course and uses `mappedBy`.
4. `@ManyToOne` is on Student and owns the relationship.
5. `cascade = CascadeType.ALL` lets saving a Course also save its Students.
6. Always set the course on each student (`setCourseId`) or the foreign key will be `NULL`.
7. Always commit the transaction and close the session.

---

## 11. Code Review Notes

The original code is preserved above. These are suggested improvements:

1. **Naming:** `Student.courseId` holds a `Course` object, not an id, so `course` is a clearer name. If you rename it, also change `mappedBy = "courseId"` to `mappedBy = "course"`. Similarly, rename the `Students` field to `students`.
2. **Foreign key column name:** `@JoinColumn` with no name creates `courseId_courseId`. Use `@JoinColumn(name = "course_id")` for a cleaner column.
3. **`session.save()`:** it is deprecated in Hibernate 6 (it still works in 6.1.5). Prefer `session.persist(course1)`.
4. **Error handling:** wrap the work in `try/catch` with `rollback()`, and use try-with-resources so the session always closes:

   ```java
   try (Session session = sessionfactory.openSession()) {
       Transaction tx = session.beginTransaction();
       // ... create objects ...
       session.persist(course1);
       tx.commit();
   }
   ```
5. **Dialect:** `MySQL8Dialect` is deprecated in Hibernate 6. Use `org.hibernate.dialect.MySQLDialect` or remove the property.
6. **Debugging:** add `hibernate.show_sql` and `hibernate.format_sql` set to `true` to see the generated SQL.
7. **Package naming:** `com.hibernate.MainApp` should follow Java convention and be lowercase (`com.hibernate.mainapp`).
8. **Closing the factory:** call `HibernateUtil.getSessionFactory().close()` at the end of `main` so the JVM exits cleanly.

**Security:** The original `hibernate.cfg.xml` contained a real database password. It has been replaced with a placeholder here. If that file was ever pushed to a public repository, change the password and keep credentials in environment variables or a git-ignored file.

---

## Project Conclusion

This project shows how to map a bidirectional One-to-Many / Many-to-One relationship in Hibernate, where a single Course contains multiple Students and each Student belongs to one Course.

The application demonstrates:

- Entity mapping
- Primary key and foreign key
- One-to-Many and Many-to-One relationships
- Owning vs. inverse side (`mappedBy`)
- Cascade save
- Hibernate Session and Transaction
- MySQL connectivity

**Project Name:** ManytoOne

**Author:** Code with Komati
