
# Hibernate One-to-One Relationship

## 1. Project Overview

**Project Name:** OneToOneRelationship

This project demonstrates Hibernate One-to-One Mapping using Java and MySQL.

In this project, two entity classes are associated:

1. Person
2. Passport

One Person is associated with one Passport.

### Technologies Used

- Java
- Hibernate ORM 6.1.5.Final
- Jakarta Persistence API
- MySQL Database
- Maven
- MySQL Connector/J 8.3.0

---

## 2. Project Structure

```text
OneToOneRelationship
│
├── src/main/java
│   ├── com.hibernate.entity
│   │   ├── Passport.java
│   │   └── Person.java
│   │
│   ├── com.hibernate.util
│   │   ├── hibernateUtil.java
│   │   └── MainApp.java
│   │
│   └── hibernate_configuration.xml
│
├── src/main/resources
├── src/test/java
├── src/test/resources
├── JRE System Library
├── Maven Dependencies
├── target
└── pom.xml
```

---

## 3. What is One-to-One Mapping?

One-to-One Mapping means one record in one table is associated with one record in another table.

### Real-World Example

A person has a passport.

- Person contains personal information.
- Passport contains passport information.
- One Person is associated with one Passport.

### Relationship Diagram

```text
       PERSON
  -----------------
  id
  name
  passport_id
  -----------------
        |
        | Foreign Key
        |
        v
      PASSPORT
  -----------------
  id
  number
  -----------------
```

In this project, Person is the owning side of the relationship.

---

## 4. Passport.java

**Package:** `com.hibernate.entity`

### Complete Code

```java
package com.hibernate.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;

@Entity
public class Passport
{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String number;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getNumber() {
        return number;
    }

    public void setNumber(String number) {
        this.number = number;
    }
}
```

### Explanation

**@Entity**

Marks the Passport class as a JPA entity.

**@Id**

Specifies the primary key.

**@GeneratedValue(strategy = GenerationType.IDENTITY)**

Automatically generates the primary key using the database identity strategy.

**private int id**

Stores the passport ID.

**private String number**

Stores the passport number.

**Getter and Setter Methods**

Getter methods retrieve values.

Setter methods assign values.

### Summary

The Passport class stores passport information.

---

## 5. Person.java

**Package:** `com.hibernate.entity`

### Complete Code

```java
package com.hibernate.entity;

import jakarta.persistence.*;

@Entity
public class Person
{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String name;

    @OneToOne(cascade = CascadeType.ALL)
    // cascade
    @JoinColumn(name ="passport_id")
    private Passport passport;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Passport getPassport() {
        return passport;
    }

    public void setPassport(Passport passport) {
        this.passport = passport;
    }
}
```

### Explanation

**@Entity**

Marks Person as a Hibernate entity.

**@Id**

Specifies the primary key.

**@GeneratedValue**

Automatically generates the ID.

**private String name**

Stores the person's name.

**@OneToOne(cascade = CascadeType.ALL)**

Defines a One-to-One relationship between Person and Passport.

**@JoinColumn(name = "passport_id")**

Specifies the foreign-key column in the Person table.

**private Passport passport**

Stores the associated Passport object.

### What is CascadeType.ALL?

CascadeType.ALL propagates all supported JPA cascade operations from Person to its associated Passport.

It includes:

1. PERSIST
2. MERGE
3. REMOVE
4. REFRESH
5. DETACH

**Example:**

When we save the Person object using persist(), the associated Passport object is also persisted because of CascadeType.ALL.

---

## 6. hibernateUtil.java

**Package:** `com.hibernate.util`

### Complete Code

```java
package com.hibernate.util;

import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class hibernateUtil
{
    private static SessionFactory sessionFactory;

    static {
        sessionFactory = new Configuration()
                .configure("hibernate_configuration.xml")
                .buildSessionFactory();
    }

    public static SessionFactory getSessionFactory()
    {
        return sessionFactory;
    }
}
```

### Explanation

**SessionFactory**

SessionFactory is used to create Hibernate Session objects.

**private static SessionFactory sessionFactory**

Creates a static SessionFactory reference.

**new Configuration()**

Creates Hibernate configuration.

**configure("hibernate_configuration.xml")**

Loads the Hibernate configuration XML file.

**buildSessionFactory()**

Builds the SessionFactory object.

**getSessionFactory()**

Returns the SessionFactory.

### Execution Flow

```text
hibernateUtil
     |
     v
Configuration
     |
     v
Load XML Configuration
     |
     v
Build SessionFactory
     |
     v
Return SessionFactory
```

---

## 7. MainApp.java

**Package:** `com.hibernate.util`

### Complete Code

```java
package com.hibernate.util;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;

import com.hibernate.entity.Passport;
import com.hibernate.entity.Person;

public class MainApp
{
    public static void main(String[] args) {
        SessionFactory sessionFactory = hibernateUtil.getSessionFactory();

        Session session = sessionFactory.openSession();

        Transaction tx = session.beginTransaction();

        Passport passport = new Passport();
        passport.setNumber("IND144");

        Person person = new Person();

        person.setName("Jay");
        person.setPassport(passport);

        // save
        session.persist(person);

        tx.commit();
        session.close();

        System.out.println("Data Saved Successfully!!");
    }
}
```

### Step-by-Step Explanation

**Step 1: Get SessionFactory**

```java
SessionFactory sessionFactory = hibernateUtil.getSessionFactory();
```

Gets SessionFactory from the utility class.

**Step 2: Open Session**

```java
Session session = sessionFactory.openSession();
```

Opens a Hibernate Session.

**Step 3: Begin Transaction**

```java
Transaction tx = session.beginTransaction();
```

Starts a database transaction.

**Step 4: Create Passport Object**

```java
Passport passport = new Passport();
passport.setNumber("IND144");
```

Creates a Passport object and assigns its number.

**Step 5: Create Person Object**

```java
Person person = new Person();
person.setName("Jay");
```

Creates a Person object and assigns the name.

**Step 6: Establish Relationship**

```java
person.setPassport(passport);
```

Associates Passport with Person.

**Step 7: Save Data**

```java
session.persist(person);
```

Persists the Person object.

Because of CascadeType.ALL, the associated Passport is also persisted.

**Step 8: Commit Transaction**

```java
tx.commit();
```

Commits the transaction.

**Step 9: Close Session**

```java
session.close();
```

Closes the Hibernate Session.

**Step 10: Print Message**

```java
System.out.println("Data Saved Successfully!!");
```

Displays the success message.

---

## 8. hibernate_configuration.xml

This file contains database connection details and Hibernate configuration properties.

### Configuration Code

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
    <session-factory>

        <property name="hibernate.connection.driver_class">
            com.mysql.cj.jdbc.Driver
        </property>

        <property name="hibernate.connection.url">
            jdbc:mysql://localhost:3306/hibernate_DB2
        </property>

        <property name="hibernate.connection.username">root</property>

        <property name="hibernate.connection.password">YOUR_DATABASE_PASSWORD</property>

        <property name="hibernate.dialect">
            org.hibernate.dialect.MySQL8Dialect
        </property>

        <property name="hibernate.show_sql">true</property>

        <property name="hibernate.format_sql">true</property>

        <property name="hibernate.hbm2ddl.auto">update</property>

        <mapping class="com.hibernate.entity.Person"/>
        <mapping class="com.hibernate.entity.Passport"/>

    </session-factory>
</hibernate-configuration>
```

### Explanation

| Property | Description |
|---|---|
| connection.driver_class | MySQL JDBC driver |
| connection.url | Database connection URL |
| connection.username | MySQL username |
| connection.password | MySQL password |
| hibernate.dialect | SQL dialect |
| hibernate.show_sql | Displays SQL queries |
| hibernate.format_sql | Formats SQL queries |
| hibernate.hbm2ddl.auto | Updates database schema |
| mapping | Registers entity classes |

**Database Name:** hibernate_DB2

Note: Replace YOUR_DATABASE_PASSWORD with your local MySQL password. Never publish real credentials in a public repository.

---

## 9. pom.xml

The pom.xml file manages Maven dependencies.

### Complete Code

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>Relations</groupId>
    <artifactId>OneToOneRelationship</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <dependencies>

        <!-- Hibernate Core -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>6.1.5.Final</version>
        </dependency>

        <!-- MySQL Connector/J -->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>8.3.0</version>
        </dependency>

    </dependencies>

</project>
```

### Maven Dependencies Explanation

**Hibernate Core**

Version: 6.1.5.Final

Provides Hibernate ORM functionality.

**MySQL Connector/J**

Version: 8.3.0

Provides JDBC connectivity between Java and MySQL.

---

## 10. Complete Project Execution Flow

```text
             START
               |
               v
       Get SessionFactory
               |
               v
          Open Session
               |
               v
       Begin Transaction
               |
               v
       Create Passport
       number = "IND144"
               |
               v
        Create Person
          name = "Jay"
               |
               v
    Set Passport inside Person
               |
               v
        session.persist()
               |
               v
   CascadeType.ALL propagates
     persist operation to Passport
               |
               v
       Commit Transaction
               |
               v
          Close Session
               |
               v
       Print Success Message
               |
               v
              END
```

---

## 11. Expected Output

If the database connection and Hibernate configuration are correct:

```text
Data Saved Successfully!!
```

### Data Representation

**Person Table**

| id | name | passport_id |
|---|---|---|
| Generated | Jay | Generated Passport ID |

**Passport Table**

| id | number |
|---|---|
| Generated | IND144 |

The actual ID values are generated by the database.

---

## 12. Important Hibernate Concepts

| Concept | Explanation |
|---|---|
| Entity | Java class mapped to a database table |
| Primary Key | Uniquely identifies a record |
| Foreign Key | References another table's record |
| @OneToOne | Defines One-to-One relationship |
| @JoinColumn | Specifies foreign-key column |
| CascadeType.ALL | Propagates all JPA cascade operations |
| SessionFactory | Creates Hibernate Sessions |
| Session | Interacts with the database |
| Transaction | Manages database operations |
| persist() | Makes a new entity persistent |

---

## 13. Conclusion

This project demonstrates Hibernate One-to-One Mapping using two entity classes: Person and Passport.

The @OneToOne annotation defines the relationship between the entities.

The @JoinColumn annotation specifies the foreign-key column.

The CascadeType.ALL option propagates persistence operations from Person to Passport.

Finally, MainApp creates both objects, establishes the relationship, and saves them into the MySQL database using Hibernate.

**Project:** OneToOneRelationship

**Relationship:** One-to-One

**ORM Framework:** Hibernate

**Database:** MySQL

**Language:** Java
