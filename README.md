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
```

## Method 2: Custom Authentication Using `application.properties`

Spring Security allows customization of the default username and password by simply defining properties in the `application.properties` file.

### Steps to Set Custom Authentication:

1. **Add Custom Username and Password**  
   Add the following lines to your `application.properties` file:
   ```properties
   spring.security.user.name=customUser
   spring.security.user.password=customPassword
   ```
2. How It Works

Spring Security will use the username and password defined in the application.properties file instead of the default ones.
Use the credentials:
Username: customUser
Password: customPassword
Testing Custom Credentials

3. Run the application.
Access any endpoint in your application, and you'll be prompted for login credentials.
Enter the username and password from application.properties.

### Method 3: Custom Authentication Using Security Filter Chain
For more control over authentication, you can configure a SecurityFilterChain in your application. This method allows you to define custom users, passwords, roles, and other advanced authentication logic.

### Steps to Set Custom Authentication:
1.Create a Security Configuration Class
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

2. Add a Custom User
Configure an in-memory user with a custom username and password:
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

3. How It Works

Spring Security uses the custom user defined in the UserDetailsService.
The securityFilterChain secures all requests by default, and a login form is displayed when accessing any endpoint.
Testing Custom Authentication

4. Run the application.
Access the /login page.
Use the following credentials:
Username: customUser
Password: customPassword
