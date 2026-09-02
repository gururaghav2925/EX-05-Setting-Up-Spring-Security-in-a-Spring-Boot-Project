# EXP05-Setting-Up-Spring-Security-in-a-Spring-Boot-Project
## AIM:
To write a program for setting up Spring Security in a Spring Boot project to secure endpoints with basic authentication and role-based access control.

## ALGORITHM:
Create a Spring Boot Project with the following dependencies:

Spring Web

Spring Security

Spring Boot DevTools (optional)

Add Spring Security dependency in pom.xml (if not using Spring Initializr).

Create a configuration class extending WebSecurityConfigurerAdapter (or using SecurityFilterChain for newer Spring versions).

Define an in-memory user with username, password, and roles using UserDetailsService.

Secure your REST endpoints using annotations or in the security config class.

Run and test the app using a browser or Postman:

Secure endpoints will prompt for username and password.

## PROGRAM CODE:
### pom.xml (Dependencies)
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
```
## Ex5Application.java
```java
package com.example.ex5;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Ex5Application {

	public static void main(String[] args) {
		SpringApplication.run(Ex5Application.class, args);
	}

}

````
### SecurityConfig.java (Spring Boot 3.x / Spring Security 6+)
```java
package com.example.ex5;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.config.Customizer;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/", "/index.html", "/public", "/public.html", "/styles.css").permitAll()
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());
        return http.build();
    }

    @Bean
    public InMemoryUserDetailsManager userDetailsService() {
        UserDetails user = User.withDefaultPasswordEncoder()
            .username("user")
            .password("password")
            .roles("USER")
            .build();
        return new InMemoryUserDetailsManager(user);
    }
}

```
### HelloController.java
```java
package com.example.ex5;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

    @GetMapping("/")
    public String index() {
        return "redirect:/index.html";
    }

    @GetMapping("/public")
    public String publicEndpoint() {
        return "redirect:/public.html";
    }

    @GetMapping("/private")
    public String privateEndpoint() {
        return "redirect:/private.html";
    }
}




```




## Static      
# Index.html 
```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spring Security Demo</title>
    <link rel="stylesheet" href="/styles.css">
</head>
<body>
    <div class="glass-container">
        <div class="icon">🛡️</div>
        <h1>Security Demo</h1>
        <p>Explore the application to see Spring Security in action. Choose an endpoint below to proceed.</p>
        <a href="/public" class="btn btn-secondary">Public Area</a>
        <a href="/private" class="btn btn-primary">Private Area 🔒</a>
    </div>
</body>
</html>
```

# Public.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Public Endpoint</title>
    <link rel="stylesheet" href="/styles.css">
</head>
<body>
    <div class="glass-container">
        <div class="icon">🌍</div>
        <h1>Public Area</h1>
        <p>This page is open to everyone. No authentication is required to view this content.</p>
        <a href="/" class="btn btn-secondary">← Back to Home</a>
    </div>
</body>
</html>
```

# private.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Public Endpoint</title>
    <link rel="stylesheet" href="/styles.css">
</head>
<body>
    <div class="glass-container">
        <div class="icon">🌍</div>
        <h1>Public Area</h1>
        <p>This page is open to everyone. No authentication is required to view this content.</p>
        <a href="/" class="btn btn-secondary">← Back to Home</a>
    </div>
</body>
</html>
```

# style.css
```css
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600&display=swap');

body {
    margin: 0;
    padding: 0;
    font-family: 'Outfit', sans-serif;
    background: linear-gradient(135deg, #0f172a, #1e1b4b);
    color: #ffffff;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.glass-container {
    background: rgba(255, 255, 255, 0.05);
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 24px;
    padding: 50px 40px;
    text-align: center;
    box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
    max-width: 400px;
    width: 90%;
    animation: fadeIn 0.8s ease-out;
}

h1 {
    font-size: 2.2rem;
    margin-bottom: 15px;
    background: linear-gradient(to right, #ffffff, #a5b4fc);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}

p {
    font-weight: 300;
    margin-bottom: 35px;
    line-height: 1.6;
    color: #cbd5e1;
}

.btn {
    display: inline-block;
    padding: 12px 28px;
    margin: 10px 5px;
    border-radius: 12px;
    text-decoration: none;
    font-weight: 600;
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.btn-primary {
    background: linear-gradient(135deg, #6366f1, #4f46e5);
    color: white;
    box-shadow: 0 4px 15px rgba(99, 102, 241, 0.4);
}

.btn-primary:hover {
    transform: translateY(-3px);
    box-shadow: 0 6px 20px rgba(99, 102, 241, 0.6);
}

.btn-secondary {
    background: rgba(255, 255, 255, 0.05);
    color: white;
    border: 1px solid rgba(255, 255, 255, 0.1);
}

.btn-secondary:hover {
    background: rgba(255, 255, 255, 0.15);
    transform: translateY(-3px);
    border-color: rgba(255, 255, 255, 0.3);
}

.icon {
    font-size: 3.5rem;
    margin-bottom: 20px;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(30px) scale(0.95); }
    to { opacity: 1; transform: translateY(0) scale(1); }
}
```


## Output:

# Interface 

<img width="1916" height="1039" alt="image" src="https://github.com/user-attachments/assets/e6ac0116-1348-45e4-b17b-ee4106bfe4f9" />

## Public Interface 

<img width="1896" height="1029" alt="image" src="https://github.com/user-attachments/assets/d782ecdf-7bf3-4295-a21d-3a7a22c1a41a" />


## Private Interface 

<img width="1907" height="1029" alt="image" src="https://github.com/user-attachments/assets/6dae0fad-2b6b-4261-9a21-4e91c5303314" />
