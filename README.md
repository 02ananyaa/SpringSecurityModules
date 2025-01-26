# SpringSecurityModules

Overview
The Spring Security Modules project is designed to demonstrate and implement all major functionalities of Spring Security. This includes user authentication, authorization, password management, role-based access control, CSRF protection, OAuth2 integration, and more.

This project serves as a learning tool and reference for integrating Spring Security features into Spring Boot applications.

# **Method 1: Default Authentication Behavior**

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
```

# Method 2: Custom Authentication Using `application.properties`

Spring Security allows customization of the default username and password by simply defining properties in the `application.properties` file.

### Steps to Set Custom Authentication:

1. **Add Custom Username and Password**  
   Add the following lines to your `application.properties` file:
   ```properties
   spring.security.user.name=customUser
   spring.security.user.password=customPassword
   ```
2.** How It Works **

Spring Security will use the username and password defined in the application.properties file instead of the default ones.
Use the credentials:
Username: customUser
Password: customPassword
Testing Custom Credentials

3.** Run the application. **
Access any endpoint in your application, and you'll be prompted for login credentials.
Enter the username and password from application.properties.

# ** Method 3: Custom Authentication Using Security Filter Chain or IN-MEMORY AUTHENTICATION **
For more control over authentication, you can configure a SecurityFilterChain in your application. This method allows you to define custom users, passwords, roles, and other advanced authentication logic.

### Steps to Set Custom Authentication:
1. **Create a Security Configuration Class **
Define a SecurityConfig class in your project:
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeHttpRequests(auth -> auth
                .anyRequest().authenticated()
            )
            .formLogin();
        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}

2. ** Add a Custom User: **
Configure an in-memory user with a custom username and password:
``` xml
import org.springframework.context.annotation.Bean;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;

@Bean
public UserDetailsService userDetailsService() {
    UserDetails user = User.withUsername("customUser")
                           .password(passwordEncoder().encode("customPassword"))
                           .roles("USER")
                           .build();
    return new InMemoryUserDetailsManager(user);
}
```
3. **How It Works**

Spring Security uses the custom user defined in the UserDetailsService.
The securityFilterChain secures all requests by default, and a login form is displayed when accessing any endpoint.
Testing Custom Authentication

4. **Run the application.**
Access the /login page.
Use the following credentials:
Username: customUser
Password: customPassword


# Method 4:  User Authentication from Database

# Spring Security: Configuring Users from a Database

This guide explains how to configure Spring Security to authenticate users from a database.

---

## **1. Add Dependencies**

Add the required dependencies to your `pom.xml` for Spring Security and Spring Data JPA:

```xml
<dependencies>
    <!-- Spring Security -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <!-- Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Database Driver (Example: MySQL) -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```
### 2. Configure the Application Properties:
``` xml
spring.datasource.url=jdbc:mysql://localhost:3306/your_database_name
spring.datasource.username=your_database_user
spring.datasource.password=your_database_password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

 ### 3. Create a User Entity:
``` xml
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class User {
    @Id
    private Long id;
    private String username;
    private String password;
    private String role;

    // Getters and Setters
}
```
 ### 4. Create a Repository for Users
Define a repository interface for accessing user data:
``` xml
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    User findByUsername(String username);
}
```
 ### 5. Implement a Custom UserDetailsService
Create a service to load user details from the database:

import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class CustomUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    public CustomUserDetailsService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username);
        if (user == null) {
            throw new UsernameNotFoundException("User not found: " + username);
        }
        return org.springframework.security.core.userdetails.User.builder()
                .username(user.getUsername())
                .password(user.getPassword())
                .roles(user.getRole())
                .build();
    }
}
### 6. Configure Security with SecurityFilterChain 
Set up Spring Security to use the custom UserDetailsService:
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.dao.DaoAuthenticationProvider;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    private final CustomUserDetailsService customUserDetailsService;

    public SecurityConfig(CustomUserDetailsService customUserDetailsService) {
        this.customUserDetailsService = customUserDetailsService;
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeHttpRequests(auth -> auth
                .anyRequest().authenticated()
            )
            .formLogin();
        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public DaoAuthenticationProvider authenticationProvider() {
        DaoAuthenticationProvider authProvider = new DaoAuthenticationProvider();
        authProvider.setUserDetailsService(customUserDetailsService);
        authProvider.setPasswordEncoder(passwordEncoder());
        return authProvider;
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}

 ### 7. Test the Setup 
1.Run the application.
2.Try accessing any secured endpoint.
3.Enter the credentials for the user stored in the database:
Username: user
Password: The hashed password you inserted into the database.

