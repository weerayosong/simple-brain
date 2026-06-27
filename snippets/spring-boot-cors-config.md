---
title: "Spring Boot Global CORS Configuration"
type: wiki-entry
project: "clinical-triage"
createdAt: 2026-06-28T02:50:00
modifiedAt: 2026-06-28T02:50:00
target_agents:
  - backend
related_tools:
  - spring-boot
tags:
  - snippet
  - cors
  - security
---

# Spring Boot Global CORS Configuration

> **🤖 Agent Routing:** Backend agents should read this to understand how to configure Global CORS in a Spring Boot 3.x application to allow frontend apps (like Angular on Vercel) to consume the API.

> **🧠 LLM Context:** This snippet extracts `CorsConfig.java`. It implements `WebMvcConfigurer` to define a global policy that allows all methods (`GET`, `POST`, etc.) from any origin. Note: This specific configuration disables credentials (`allowCredentials(false)`), making it suitable for APIs that do not rely on HTTP-Only cookies.

## Code Snippet

```java
package com.weerayosong.clinical_triage_backend;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.lang.NonNull;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(@NonNull CorsRegistry registry) {
                registry.addMapping("/**") // Apply to all endpoints
                        .allowedOriginPatterns("*") // Allow all origins (e.g. localhost:4200, vercel.app)
                        .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS") // Allowed HTTP methods
                        .allowedHeaders("*") // Allow all headers
                        .allowCredentials(false); // Disallow cookies (Use 'true' if implementing JWT-in-cookie)
            }
        };
    }
}
```

## ✨ Implementation Details
- `@Configuration`: Tells Spring Boot that this class contains Bean definitions that need to be processed during startup.
- `@NonNull`: Added to the `addCorsMappings` method signature to comply with strict null-safety standards in newer Spring Boot versions.
- **Why not `@CrossOrigin`?**: While you can annotate individual controllers with `@CrossOrigin`, doing it globally via `WebMvcConfigurer` ensures you don't accidentally forget to add it to a new controller later.

## 🔗 Related Context
- [[clinical-triage-architecture]]
