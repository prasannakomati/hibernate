
# OneToManyPractice – Hibernate One-to-Many Mapping

## 1. Project Overview

This project demonstrates the One-to-Many relationship using Hibernate ORM and JPA annotations.

### Technologies Used
- Java
- Hibernate ORM 6.1.5.Final
- JPA Annotations
- Maven
- MySQL
- Eclipse IDE

### Project Structure

```text
OneToManyPractice
│
├── src/main/java
│   │
│   ├── com.hibernate.entity
│   │   ├── Department.java
│   │   └── Employee.java
│   │
│   ├── com.hibernate.Main
│   │   ├── MainApp.java
│   │   └── MainApp2.java
│   │
│   └── com.hibernate.util
│       └── HibernateUtil.java
│
├── src/main/resources
│   └── hibernate.cfg.xml
│
├── src/test/java
├── src/test/resources
├── Maven Dependencies
├── target
└── pom.xml
```

## 2. One-to-Many Relationship

A One-to-Many relationship means one record in a table can be associated with multiple records in another table.

### Real-World Example

One department can have multiple employees.

For example:

- HR Department → Tharu
- HR Department → Ramu
- HR Department → Raju

### Relationship Diagram

```text
             DEPARTMENT
          -----------------
          dep_id (PK)
          dep_name
          -----------------
                 |
                 |
            One-to-Many
                 |
          -----------------
          |       |       |
          |       |       |
       Employee Employee Employee
          |       |       |
        Tharu    Ramu    Raju
```

### Database Relationship

**Department Table**

| dep_id | dep_name |
|---|---|
| 1 | HR Department |
| 2 | IT Department |

**Employee Table**

| emp_id | emp_name | salary | dep_id |
|---|---|---|---|
| 1 | Tharu | 1000000 | 1 |
| 2 | Ramu | 300000 | 1 |
| 3 | Raju | 90000 | 1 |

Here:

- `dep_id` is the primary key in Department.
- `emp_id` is the primary key in Employee.
- `dep_id` is the foreign key in Employee.

---

## 3. Department.java

### Explanation

Department is the Parent Entity.

Annotations used:

1. `@Entity` – Converts the Java class into a database entity.
2. `@Id` – Specifies the primary key.
3. `@GeneratedValue` – Automatically generates the primary key.
4. `@Column` – Maps the Java field to a database column.
5. `@OneToMany` – Defines the One-to-Many relationship.

### Important Annotation

```java
@OneToMany(
    mappedBy="department",
    cascade=CascadeType.ALL,
    fetch=FetchType.EAGER
)
private List<Employee> employees;
```

**mappedBy = "department"**

The department field in Employee.java owns the relationship.

**cascade = CascadeType.ALL**

Propagates entity operations from Department to its associated employees.

**fetch = FetchType.EAGER**

Loads the employee collection when the department is fetched.

### Complete Code – Department.java

```java
package com.hibernate.entity;

import java.util.List;

import jakarta.persistence.*;

@Entity
public class Department
{
    @Id
    @Column
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private int dep_id;

    @Column
    private String dep_name;

    @OneToMany( mappedBy="department", cascade=CascadeType.ALL, fetch=FetchType.EAGER)
    //@OneToMany(mappedBy = "department" , cascade = CascadeType.All ,
    private List<Employee> employees;

    public Department()
    {
    }

    public Department(String deptName) {
        dep_name = deptName;
    }

    public int getDep_id() {
        return dep_id;
    }

    public void setDep_id(int dep_id) {
        this.dep_id = dep_id;
    }

    public String getDep_name() {
        return dep_name;
    }

    public void setDep_name(String dep_name) {
        this.dep_name = dep_name;
    }

    public List<Employee> getEmployes() {
        return employees;
    }

    public void setEmployes(List<Employee> employes) {
        employees = employes;
    }

    @Override
    public String toString()
    {
        return "Department [depid "+dep_id+",deptName "+dep_name+",employees"+employees+"]";
    }
}
```

---

## 4. Employee.java

### Explanation

Employee is the Child Entity.

Annotations used:

1. `@Entity` – Maps Employee class to the database.
2. `@Id` – Specifies the primary key.
3. `@GeneratedValue` – Generates the employee ID.
4. `@Column` – Maps fields to database columns.
5. `@ManyToOne` – Many employees belong to one department.
6. `@JoinColumn` – Specifies the foreign key column.

### Important Annotation

```java
@ManyToOne(cascade=CascadeType.ALL)
@JoinColumn(name="dep_id")
private Department department;
```

**@ManyToOne**

Multiple employees can belong to one department.

**@JoinColumn(name="dep_id")**

Creates the foreign key column in the Employee table.

### Complete Code – Employee.java

```java
package com.hibernate.entity;

import jakarta.persistence.*;

@Entity

public class Employee
{
    @Id
    @Column
    @GeneratedValue(strategy =GenerationType.IDENTITY)
    private int emp_id;

    @Column
    private String emp_name;

    @Column
    private double salary;

    @ManyToOne(cascade=CascadeType.ALL)
    @JoinColumn(name="dep_id")
    private Department department;

    public int getEmp_id() {
        return emp_id;
    }

    public void setEmp_id(int emp_id) {
        this.emp_id = emp_id;
    }

    public String getEmp_name() {
        return emp_name;
    }

    public void setEmp_name(String emp_name) {
        this.emp_name = emp_name;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    public Department getDepartment() {
        return department;
    }

    public void setDepartment(Department department) {
        this.department = department;
    }

    public Employee()
    {
    }

    public Employee(String empName , double empsalary)
    {
        emp_name= empName;
        salary=empsalary;
    }

    @Override
    public String toString()
    {
        return " Employee [empid"+emp_id+",empNmae"+emp_name+",emp_salary"+salary+"]";
    }
}
```

---

## 5. MainApp.java

### Explanation

MainApp demonstrates basic insertion and fetching operations.

It contains three methods:

- `main()` – Calls insertion and fetching.
- `saveData()` – Inserts department and employee records.
- `fatchData()` – Fetches department and employee records.

### Insertion Process

```text
Create Department
       |
Create Employee Objects
       |
Assign Department to Employees
       |
Save Department
       |
Save Employees
       |
Commit Transaction
```

### Fetching Process

```text
Open Session
     |
Fetch Department Using ID
     |
Get Department Name
     |
Get Employee List
     |
Display Employee Details
     |
Close Session
```

### Complete Code – MainApp.java

```java
package com.hibernate.Main;

import org.hibernate.Session;
import org.hibernate.Transaction;

import com.hibernate.entity.Department;
import com.hibernate.entity.Employee;
import com.hibernate.util.HibernateUtil;

public class MainApp
{

    public static void main(String args[])
    {
        saveData();
        fatchData();
    }

    static void saveData()
    {
        HibernateUtil h = new HibernateUtil();
        Session session = h.getSessionFactory().openSession();

        Transaction transaction = session.beginTransaction();

        Department dept = new Department("HR Department");

        Employee emp1 = new Employee("tharu",1000000);
        Employee emp2 = new Employee("ramu",300000);
        Employee emp3 = new Employee("raju",90000);

        emp1.setDepartment(dept);
        emp2.setDepartment(dept);
        emp3.setDepartment(dept);

        session.save(dept);

        session.save(emp1);

        session.save(emp2);

        session.save(emp3);

        transaction.commit();
    }

    static void fatchData()
    {
        HibernateUtil h2 = new HibernateUtil();

        Session session = h2.getSessionFactory().openSession();

        Department dept = session.get(Department.class, 2);

        System.out.println("department name= "+dept.getDep_name());

        System.out.println("employees in this Department ");

        for(Employee emp : dept.getEmployes())
        {
            System.out.println("emp_name ="+emp.getEmp_name());
            System.out.println("emp_salary="+emp.getSalary());

        }

        session.close();
    }
}
```

---

## 6. MainApp2.java – Employee Management System

### Explanation

MainApp2 is a menu-driven Hibernate CRUD application.

It performs the following operations:

1. Insertion
2. Fetching
3. Updating
4. Deleting
5. Exit

### Menu Structure

```text
===== EMPLOYEE MANAGEMENT =====

1. Insertion
2. Fetching
3. Updating
4. Delete
5. Exit

Enter your choice:
```

### 6.1 Input Helper Methods

**readInt()**

Reads integer input from the user.

**readDouble()**

Reads salary and checks that it is not negative.

**readName()**

Reads a name and prevents empty input.

### 6.2 Insertion

Insertion supports two operations:

- Department insertion
- Employee insertion

For inserting an employee:

1. Display available departments.
2. Enter employee name.
3. Enter salary.
4. Enter department ID.
5. Fetch the selected department.
6. Assign the department to the employee.
7. Save the employee.
8. Commit the transaction.

### 6.3 Fetching

1. Display available departments.
2. Enter department ID.
3. Fetch the department.
4. Display department name.
5. Display all employees belonging to that department.

### 6.4 Updating

1. Display employees.
2. Enter employee ID.
3. Display departments.
4. Enter new employee name.
5. Enter new salary.
6. Enter department ID.
7. Update employee details.
8. Commit transaction.

### 6.5 Deleting

1. Display departments.
2. Enter department ID.
3. Fetch department.
4. Remove department.
5. Commit transaction.

### Complete Code – MainApp2.java

```java
package com.hibernate.Main;

import java.util.List;
import java.util.Scanner;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;

import com.hibernate.entity.Department;
import com.hibernate.entity.Employee;
import com.hibernate.util.HibernateUtil;

public class MainApp2
{
    static Scanner sc = new Scanner(System.in);
    static SessionFactory factory;

    public static void main(String[] args)
    {
        HibernateUtil h1 = new HibernateUtil();
        factory = h1.getSessionFactory();

        while (true)
        {
            System.out.println("\n===== EMPLOYEE MANAGEMENT =====");
            System.out.println("1. Insertion");
            System.out.println("2. Fetching");
            System.out.println("3. Updating");
            System.out.println("4. Delete");
            System.out.println("5. Exit");

            int op = readInt("Enter your choice:");

            switch (op)
            {
                case 1:
                    insert();
                    break;

                case 2:
                    fetch();
                    break;

                case 3:
                    update();
                    break;

                case 4:
                    delete();
                    break;

                case 5:
                    factory.close();
                    sc.close();
                    System.out.println("Application closed");
                    return;

                default:
                    System.out.println("Invalid choice");
            }
        }
    }

    // ---------- INPUT HELPERS ----------

    static int readInt(String msg)
    {
        while (true)
        {
            System.out.println(msg);
            try
            {
                return Integer.parseInt(sc.nextLine().trim());
            }
            catch (NumberFormatException e)
            {
                System.out.println("Please enter a valid whole number.");
            }
        }
    }

    static double readDouble(String msg)
    {
        while (true)
        {
            System.out.println(msg);
            try
            {
                double d = Double.parseDouble(sc.nextLine().trim());
                if (d < 0)
                {
                    System.out.println("Salary cannot be negative.");
                    continue;
                }
                return d;
            }
            catch (NumberFormatException e)
            {
                System.out.println("Please enter a valid number.");
            }
        }
    }

    static String readName(String msg)
    {
        while (true)
        {
            System.out.println(msg);
            String s = sc.nextLine().trim();
            if (!s.isEmpty())
            {
                return s;
            }
            System.out.println("Name cannot be empty.");
        }
    }

    // ---------- INSERTION ----------

    static void insert()
    {
        int ch = readInt("1. Department\n2. Employee");

        if (ch != 1 && ch != 2)
        {
            System.out.println("Invalid input");
            return;
        }

        // collect all input BEFORE opening a transaction
        String name;
        double salary = 0;
        int deptId = 0;

        if (ch == 1)
        {
            name = readName("Enter new department name:");
        }
        else
        {
            if (!listDepartments())
            {
                System.out.println("Please add a department first.");
                return;
            }
            name = readName("Enter new employee name:");
            salary = readDouble("Enter employee salary:");
            deptId = readInt("Enter department id:");
        }

        Transaction tx = null;

        try (Session session = factory.openSession())
        {
            tx = session.beginTransaction();

            if (ch == 1)
            {
                Department dept = new Department();
                dept.setDep_name(name);
                session.persist(dept);

                tx.commit();
                System.out.println("Department inserted successfully");
            }
            else
            {
                Department dept = session.get(Department.class, deptId);

                if (dept == null)
                {
                    tx.rollback();
                    System.out.println("Department not found");
                    return;
                }

                Employee emp = new Employee(name, salary);
                emp.setDepartment(dept);
                session.persist(emp);

                tx.commit();
                System.out.println("Employee inserted successfully");
            }
        }
        catch (Exception e)
        {
            rollbackQuietly(tx);
            System.out.println("Insert failed: " + e.getMessage());
        }
    }

    // ---------- FETCHING ----------

    static void fetch()
    {
        if (!listDepartments())
        {
            return;
        }
        int id = readInt("Enter department id to fetch:");

        // new session = fresh data, and lazy collection can be loaded safely
        try (Session session = factory.openSession())
        {
            Department dept = session.get(Department.class, id);

            if (dept == null)
            {
                System.out.println("Department not found");
                return;
            }

            System.out.println("Department Name: " + dept.getDep_name());
            System.out.println("Employees:");
            System.out.println(dept.getEmployes()); // must be read while session is open
        }
        catch (Exception e)
        {
            System.out.println("Fetch failed: " + e.getMessage());
        }
    }

    // ---------- UPDATING ----------

    static void update()
    {
        if (!listEmployees())
        {
            return;
        }
        int id = readInt("Enter employee id to update:");
        if (!listDepartments())
        {
            return;
        }
        String name = readName("Enter new employee name:");
        double salary = readDouble("Enter new salary:");
        int depId = readInt("Enter department id:");

        Transaction tx = null;

        try (Session session = factory.openSession())
        {
            tx = session.beginTransaction();

            Employee emp = session.get(Employee.class, id);

            if (emp == null)
            {
                tx.rollback();
                System.out.println("Employee not found");
                return;
            }

            Department dept = session.get(Department.class, depId);

            if (dept == null)
            {
                tx.rollback();
                System.out.println("Department not found");
                return;
            }

            // only modify the entity after every check has passed
            emp.setEmp_name(name);
            emp.setSalary(salary);
            emp.setDepartment(dept);

            tx.commit();
            System.out.println("Employee updated successfully");
        }
        catch (Exception e)
        {
            rollbackQuietly(tx);
            System.out.println("Update failed: " + e.getMessage());
        }
    }

    // ---------- DELETE ----------

    static void delete()
    {
        if (!listDepartments())
        {
            return;
        }
        int id = readInt("Enter department id to delete:");

        Transaction tx = null;

        try (Session session = factory.openSession())
        {
            tx = session.beginTransaction();

            Department dep = session.get(Department.class, id);

            if (dep == null)
            {
                tx.rollback();
                System.out.println("Department not found");
                return;
            }

            session.remove(dep);
            tx.commit();
            System.out.println("Department deleted successfully");
        }
        catch (Exception e)
        {
            rollbackQuietly(tx);
            System.out.println("Delete failed (the department may still have employees): "
                    + e.getMessage());
        }
    }

    // ---------- LIST HELPERS (so the user can see the ids) ----------

    static boolean listDepartments()
    {
        try (Session session = factory.openSession())
        {
            List<Object[]> rows = session
                    .createQuery("select d.id, d.dep_name from Department d order by d.id", Object[].class)
                    .list();

            if (rows.isEmpty())
            {
                System.out.println("No departments found.");
                return false;
            }

            System.out.println("\n--- Departments (ID | Name) ---");
            for (Object[] r : rows)
            {
                System.out.println(r[0] + " | " + r[1]);
            }
            System.out.println("------------------------------");
            return true;
        }
        catch (Exception e)
        {
            System.out.println("Could not load departments: " + e.getMessage());
            return false;
        }
    }

    static boolean listEmployees()
    {
        try (Session session = factory.openSession())
        {
            List<Object[]> rows = session
                    .createQuery("select e.id, e.emp_name, e.salary from Employee e order by e.id", Object[].class)
                    .list();

            if (rows.isEmpty())
            {
                System.out.println("No employees found.");
                return false;
            }

            System.out.println("\n--- Employees (ID | Name | Salary) ---");
            for (Object[] r : rows)
            {
                System.out.println(r[0] + " | " + r[1] + " | " + r[2]);
            }
            System.out.println("---------------------------------------");
            return true;
        }
        catch (Exception e)
        {
            System.out.println("Could not load employees: " + e.getMessage());
            return false;
        }
    }

    // ---------- UTIL ----------

    static void rollbackQuietly(Transaction tx)
    {
        try
        {
            if (tx != null && tx.isActive())
            {
                tx.rollback();
            }
        }
        catch (Exception ignored)
        {
        }
    }
}
```

---

## 7. hibernate.cfg.xml

### Explanation

This file contains Hibernate configuration settings.

| Property | Explanation |
|---|---|
| connection.driver_class | MySQL JDBC driver |
| connection.url | Database URL |
| connection.username | Database username |
| connection.password | Database password |
| hibernate.dialect | SQL dialect |
| hibernate.show_sql | Displays SQL queries |
| hibernate.format_sql | Formats SQL queries |
| hibernate.hbm2ddl.auto | Updates database schema |
| mapping class | Registers entity classes |

### Complete Code – hibernate.cfg.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>

    <session-factory>

        <!-- Database Driver -->
        <property name="hibernate.connection.driver_class">
            com.mysql.cj.jdbc.Driver
        </property>

        <!-- Database URL -->
        <property name="hibernate.connection.url">
            jdbc:mysql://localhost:3306/OneToManyHibernate
        </property>

        <!-- Username -->
        <property name="hibernate.connection.username">
            root
        </property>

        <!-- Password -->
        <property name="hibernate.connection.password">
            Prasanna@2007
        </property>

        <!-- SQL Dialect -->
        <property name="hibernate.dialect">
            org.hibernate.dialect.MySQL8Dialect
        </property>

        <!-- Show SQL Queries -->
        <property name="hibernate.show_sql">
            true
        </property>

        <!-- Format SQL -->
        <property name="hibernate.format_sql">
            true
        </property>

        <!-- Auto Table Creation -->
        <property name="hibernate.hbm2ddl.auto">
            update
        </property>

        <!-- Mapping Class -->
        <mapping class="com.hibernate.entity.Department"/>
        <mapping class="com.hibernate.entity.Employee"/>

    </session-factory>

</hibernate-configuration>
```

---

## 8. pom.xml

### Explanation

Maven is used to manage project dependencies.

Dependencies used:

1. Hibernate Core – ORM functionality.
2. MySQL Connector – Database connectivity.

### Complete Code – pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>hibernate</groupId>
    <artifactId>OneToManyPractice</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <dependencies>

        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>6.1.5.Final</version>
        </dependency>

        <!-- Source: https://mvnrepository.com/artifact/org.hibernate/hibernate-core -->

        <!-- Source: https://mvnrepository.com/artifact/mysql/mysql-connector-java -->

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.33</version>
            <scope>compile</scope>
        </dependency>

    </dependencies>

</project>
```

---

## 9. Hibernate Important Concepts

| Concept | Meaning |
|---|---|
| Entity | Java class mapped to database table |
| @Id | Primary key |
| @GeneratedValue | Automatically generates ID |
| @OneToMany | One parent has multiple children |
| @ManyToOne | Many children belong to one parent |
| mappedBy | Identifies the owning relationship field |
| @JoinColumn | Defines foreign key column |
| Session | Communicates with database through Hibernate |
| SessionFactory | Creates Hibernate sessions |
| Transaction | Manages database operations |
| commit() | Saves transaction changes |
| rollback() | Cancels transaction changes |
| persist() | Makes an entity persistent |
| get() | Fetches an entity using its ID |
| remove() | Deletes an entity |

---

## 10. CRUD Operations Summary

| Operation | Method | Description |
|---|---|---|
| Create | insert() | Inserts Department or Employee |
| Read | fetch() | Fetches Department and Employees |
| Update | update() | Updates Employee details |
| Delete | delete() | Deletes Department |

### Complete Application Flow

```text
              MainApp2
                  |
             Display Menu
                  |
       ------------------------
       |       |      |       |
     Insert  Fetch  Update  Delete
       |       |      |       |
       ------------------------
                  |
            Hibernate Session
                  |
              Transaction
                  |
              MySQL Database
```

---

## 11. Important Notes

1. Department is the parent entity.
2. Employee is the child entity.
3. One department can have multiple employees.
4. Employee contains the foreign key `dep_id`.
5. `@OneToMany` is used in Department.
6. `@ManyToOne` is used in Employee.
7. `mappedBy="department"` refers to the Employee class field.
8. `@JoinColumn(name="dep_id")` defines the foreign key.
9. Always commit a transaction after successful database operations.
10. Close Hibernate sessions after completing database operations.

### Code Review Notes

The original code is preserved above. These points should be checked when running it:

- Your entity ID fields are `dep_id` and `emp_id`, but the HQL queries in `MainApp2` use `d.id` and `e.id`. HQL uses Java entity field names, so these queries need attention.
- `MainApp.java` fetches department ID `2`. That ID must exist in your database.
- Your `MainApp2` deletion operation should be tested carefully because cascade settings can affect associated entities.
- The implementation of `HibernateUtil.java` was not supplied, so it is not included here.

**Security:** Do not upload real database passwords to a public GitHub repository. Replace exposed credentials with environment-based configuration before publishing.

---

## Project Conclusion

This project demonstrates how to implement a One-to-Many relationship using Hibernate.

A single Department can contain multiple Employees, and each Employee belongs to one Department.

The application demonstrates:

- Entity mapping
- Primary key and foreign key
- One-to-Many relationship
- Many-to-One relationship
- Hibernate Session
- Hibernate Transaction
- CRUD operations
- MySQL database connectivity

**Project Name:** OneToManyPractice

**Author:** Code with Komati
