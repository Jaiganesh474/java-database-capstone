# Teacher Workout Backend - MySQL Schema Design

This document describes the database schema for the Teacher Workout application.  
The system manages teachers, workouts, exercises, and workout plans.

---

## 1. Teachers Table

Stores registered teachers.

```sql
CREATE TABLE teachers (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(150) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 2. Workouts Table

Stores workout programs created by teachers.

```sql
CREATE TABLE workouts (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  teacher_id BIGINT NOT NULL,
  title VARCHAR(150) NOT NULL,
  description TEXT,
  difficulty_level ENUM('BEGINNER', 'INTERMEDIATE', 'ADVANCED') NOT NULL,
  duration_minutes INT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (teacher_id) REFERENCES teachers(id) ON DELETE CASCADE
);
```

---

## 3. Exercises Table

Stores individual exercises.

```sql
CREATE TABLE exercises (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(150) NOT NULL,
  description TEXT,
  calories_burned_per_minute DECIMAL(5,2),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 4. Workout_Exercises Table

Maps exercises to workouts (Many-to-Many relationship).

```sql
CREATE TABLE workout_exercises (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  workout_id BIGINT NOT NULL,
  exercise_id BIGINT NOT NULL,
  sets INT NOT NULL,
  reps INT,
  duration_minutes INT,
  FOREIGN KEY (workout_id) REFERENCES workouts(id) ON DELETE CASCADE,
  FOREIGN KEY (exercise_id) REFERENCES exercises(id) ON DELETE CASCADE
);
```

---

## 5. Workout_Plans Table

Stores scheduled workout plans for teachers.

```sql
CREATE TABLE workout_plans (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  teacher_id BIGINT NOT NULL,
  workout_id BIGINT NOT NULL,
  scheduled_date DATE NOT NULL,
  status ENUM('PLANNED', 'COMPLETED', 'SKIPPED') DEFAULT 'PLANNED',
  FOREIGN KEY (teacher_id) REFERENCES teachers(id) ON DELETE CASCADE,
  FOREIGN KEY (workout_id) REFERENCES workouts(id) ON DELETE CASCADE
);
```

---

## 6. Relationships Summary

- One **Teacher** → Many **Workouts**
- One **Workout** → Many **Exercises** (via `workout_exercises`)
- One **Teacher** → Many **Workout Plans**
- One **Workout** → Many **Workout Plans**

---

## 7. Indexes (Optional Performance Improvements)

```sql
CREATE INDEX idx_teacher_email ON teachers(email);
CREATE INDEX idx_workout_teacher ON workouts(teacher_id);
CREATE INDEX idx_plan_date ON workout_plans(scheduled_date);
```

---

## 8. Notes

- Passwords should be stored as hashed values.
- Use proper validation at the API level.
- ENUM values can be replaced with lookup tables if extensibility is required.
