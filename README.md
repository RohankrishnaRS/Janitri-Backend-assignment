package com.janitri.assignment;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class JanitriBackendApplication {
    public static void main(String[] args) {
        SpringApplication.run(JanitriBackendApplication.class, args);
    }
}

package com.janitri.assignment.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import javax.sql.DataSource;
import org.springframework.context.annotation.Bean;

@Configuration
public class DatabaseConfig {
    @Value("${spring.datasource.url}")
    private String dbUrl;

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource dataSource() {
        return DataSourceBuilder.create().url(dbUrl).build();
    }
}

package com.janitri.assignment.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public String handleException(Exception e) {
        return e.getMessage();
    }
}

package com.janitri.assignment.model;

import jakarta.persistence.*;
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import java.util.List;

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Email
    @NotBlank
    private String email;
    
    @NotBlank
    private String password;
    
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Patient> patients;
    
    // Getters and Setters
}

@Entity
public class Patient {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private int age;
    
    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
    
    @OneToMany(mappedBy = "patient", cascade = CascadeType.ALL)
    private List<HeartRate> heartRates;
    
    // Getters and Setters
}

@Entity
public class HeartRate {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private int bpm;
    private String timestamp;
    
    @ManyToOne
    @JoinColumn(name = "patient_id")
    private Patient patient;
    
    // Getters and Setters
}

package com.janitri.assignment.service;

import com.janitri.assignment.model.Patient;
import com.janitri.assignment.repository.PatientRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class PatientService {
    @Autowired
    private PatientRepository patientRepository;

    public Patient addPatient(Patient patient) {
        return patientRepository.save(patient);
    }

    public Patient getPatientById(Long id) {
        return patientRepository.findById(id).orElse(null);
    }
}

package com.janitri.assignment.service;

import com.janitri.assignment.model.HeartRate;
import com.janitri.assignment.repository.HeartRateRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class HeartRateService {
    @Autowired
    private HeartRateRepository heartRateRepository;

    public HeartRate recordHeartRate(HeartRate heartRate) {
        return heartRateRepository.save(heartRate);
    }
}

// README.md (Instructions to be pushed to GitHub)
/*
# Janitri Backend API

## Setup Instructions
1. Clone this repository: `git clone <repository-url>`
2. Navigate to the project directory: `cd janitri-backend`
3. Run the application: `mvn spring-boot:run`
4. API Documentation available at: `http://localhost:8080/swagger-ui.html`

## API Endpoints
- `POST /users/register` - Register a new user
- `POST /users/login` - User login
- `POST /patients/add` - Add a new patient
- `GET /patients/{id}` - Get patient details
- `POST /heart-rate/record` - Record heart rate

## Push to GitHub
```sh
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <repository-url>
git push -u origin main
```
*/

