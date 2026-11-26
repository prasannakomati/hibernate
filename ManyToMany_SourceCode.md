# ManyToMany (Hibernate) – Complete Source Code

## 1. Project Overview

This is a small **Maven + Hibernate** project that demonstrates a **Many-to-Many relationship** between two entities: `Student` and `Course`. One student can join many courses, and one course can have many students. Hibernate stores this link in a separate join table named `Student_Course` in a MySQL database.

**How the pieces fit together:**

- **`pom.xml`** brings in the Hibernate and MySQL libraries.
- **`hiberante.cfg.xml`** holds the database connection and Hibernate settings.
- **`Student.java` and `Course.java`** are the entity classes that Hibernate maps to database tables.

---

## 2. Project Structure

```text
ManyToMany/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── hiberante.cfg.xml
│   │   │   └── com/
│   │   │       └── hiberanate/
│   │   │           └── entity/
│   │   │               ├── Course.java
│   │   │               └── Student.java
│   │   └── resources/          (empty)
│   └── test/
│       ├── java/               (empty)
│       └── resources/          (empty)
├── target/                     (Maven build output – not included below)
├── .classpath, .project        (Eclipse project files – not included below)
└── .settings/                  (Eclipse settings – not included below)
```

> Only source and configuration files are included below. Compiled `.class` files and Maven output in `target/`, and Eclipse IDE files (`.classpath`, `.project`, `.settings/`), are not source code, so they are left out. The `target/classes/hiberante.cfg.xml` file is an identical copy of the one in `src/main/java`.

---

## 3. File Index

1. `pom.xml`
2. `src/main/java/hiberante.cfg.xml`
3. `src/main/java/com/hiberanate/entity/Student.java`
4. `src/main/java/com/hiberanate/entity/Course.java`

---

## 4. Source Code


### Build Configuration

#### 1. `pom.xml`

**What it does:** The Maven build file. It names the project (`Hibernate:ManyToMany`, version `0.0.1-SNAPSHOT`) and lists the two libraries the project needs: the MySQL JDBC driver (`mysql-connector-j` 9.1.0) and Hibernate (`hibernate-core` 6.1.5.Final). Maven downloads these automatically.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>Hibernate</groupId>
  <artifactId>ManyToMany</artifactId>
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
        <version>6.1.5.Final</version> <!-- Use a recent version -->
    </dependency>

  </dependencies>
</project>
```


### Hibernate Configuration

#### 2. `src/main/java/hiberante.cfg.xml`

**What it does:** The Hibernate settings file. It tells Hibernate how to connect to the MySQL database `manytomany` (driver, URL, username, password), which SQL dialect to use, and to automatically create or update tables (`hbm2ddl.auto = update`). It also turns on printing of SQL queries to the console and lists the entity classes to map.

```xml
<?xml version="1.0" encoding="UTF-8"?>


    <!DOCTYPE hibernate-configuration PUBLIC
	"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
    
    <hibernate-configuration>
    <session-factory>
    <property name ="hibernate.connection.driver-class">com.mysql.cj.jdbc.Driver</property>
    <property name ="hibernate.connection.url" >jdbc:mysql://localhost:3306/manytomany</property>
    
    <property name ="hibernate.connection.username" > root</property>
    
    <property name ="hibernate.connection.password" >Prasanna@2007 </property>
    
    <property name ="hibernate.dialect" >org.hibernate.dialect.MySQLDialect </property>
    
    <property name ="hibernate.hbm2ddl.auto">update </property>
    
    <property name ="hibernate.show_sql" >true</property>
    
    <property name ="hibernate.formate_sql"> true</property>
    
    <mapping class = "com.hibernate.entity.Employee" />
    <mapping class ="com.hibernate.entity.Department" />
    </session-factory>
    
    </hibernate-configuration>
```


### Entity Classes

#### 3. `src/main/java/com/hiberanate/entity/Student.java`

**What it does:** The `Student` entity (it becomes a database table). It has an `id` and a `name`, and a set of `Course` objects. The `@ManyToMany` and `@JoinTable` annotations create a third table called `Student_Course` that links students to courses, so one student can take many courses and one course can have many students. It also has getters and setters.

```java
package com.hiberanate.entity;

import java.util.HashSet;
import java.util.List;
import java.util.Set;

import jakarta.persistence.*;

@Entity
public class Student
{

	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	@Column
	private String name;
	
	 @ManyToMany( cascade = CascadeType.ALL)
	 @JoinTable( 
			 name ="Student_Course" ,
			 joinColumns= @JoinColumn(name="Student_id" ),
			 inverseJoinColumns= @JoinColumn(name = "course_id"))
			 
			 
			 
	 private Set<Course> course = new HashSet<>();

	 public int getId() {
		 return id;
	 }

	 public void setId(int id) 
	 {
		 this.id = id;
	 }

	 public String getName()
	 {
		 return name;
	 }

	 public void setName(String name) 
	 {
		 this.name = name;
	 }

	 public Set<Course> getCourse() {
		 return course;
	 }

	 public void setCourse(Set<Course> course) {
		 this.course = course;
	 };
	
	
	
}
```

#### 4. `src/main/java/com/hiberanate/entity/Course.java`

**What it does:** The `Course` entity (it becomes a database table). It has a `courseId` and a `courseName`. It is the other side of the Many-to-Many relationship with `Student`.

```java
package com.hiberanate.entity;

import jakarta.persistence.*;

@Entity
public class Course 
{
	
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int courseId;
	
	@Column
	private String courseName;
	
	
	

}
```
