# SpringSecurityModules

Overview
The Spring Security Modules project is designed to demonstrate and implement all major functionalities of Spring Security. This includes user authentication, authorization, password management, role-based access control, CSRF protection, OAuth2 integration, and more.

This project serves as a learning tool and reference for integrating Spring Security features into Spring Boot applications.

### **Method 1: Default Authentication Behavior**

When you add **Spring Security** to your Spring Boot project, it automatically configures basic authentication with a default user. This is the easiest way to secure your application without needing to configure anything manually.

### **How Spring Security Works by Default**

1. **Default Username and Password:**
   - **Username:** `user`
   - **Password:** A randomly generated password, which is displayed in the console when the application starts.

2. **How is the Password Generated?**
   - The password is generated automatically by Spring Security when you run your application. The generated password is unique each time the application is started, and it is logged in the console.
   
   Example:
Using generated security password: 2f6d8ab1-f17a-4ec8-aad0-34f964da7aeb
   
3. **Accessing Secured Endpoints:**
- By default, Spring Security will secure all your endpoints.
- When you try to access any URL in your application, you will be prompted to enter the **username** and **password**.
- You can log in using the default **username** `user` and the **password** that is logged in the console (e.g., `2f6d8ab1-f17a-4ec8-aad0-34f964da7aeb`).

### **Steps to Reproduce Default Authentication Behavior:**

1. **Step 1: Add the Spring Security Dependency**
- Add the Spring Security dependency to your `pom.xml` (or `build.gradle`) file.

Example for Maven:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
