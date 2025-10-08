# Database Schema Design
# LifeOS - Life Management Operating System

**Version:** 1.0  
**Last Updated:** October 8, 2025  
**Database:** PostgreSQL 15+ (via Supabase)  
**ORM:** Prisma 5.x  
**Status:** Draft

---

## 📋 Table of Contents

1. [Schema Overview](#schema-overview)
2. [Core Entities](#core-entities)
3. [Prisma Schema Definition](#prisma-schema-definition)
4. [Indexes & Performance](#indexes--performance)
5. [Row Level Security (RLS)](#row-level-security-rls)
6. [Data Migrations](#data-migrations)
7. [Seed Data](#seed-data)

---

## 🎯 Schema Overview

### Entity Relationship Diagram (ERD)

```
┌─────────────┐
│    users    │
└──────┬──────┘
       │
       ├──────────────────────────────────────────────────┐
       │                                                   │
       ▼                                                   ▼
┌─────────────┐                                    ┌─────────────┐
│   layers    │◄───┐                               │   inputs    │
└──────┬──────┘    │                               └─────────────┘
       │           │
       ├───────────┼───────────────┐
       │           │               │
       ▼           │               ▼
┌─────────────┐   │        ┌──────────────────┐
│  projects   │◄──┘        │ strategy_documents│
└──────┬──────┘            └──────────────────┘
       │
       ├───────────────────────────┐
       │                           │
       ▼                           ▼
┌─────────────┐            ┌─────────────┐
│    tasks    │            │   habits    │
└──────┬──────┘            └──────┬──────┘
       │                          │
       ▼                          ▼
┌─────────────┐            ┌─────────────┐
│   outputs   │            │ habit_logs  │
└─────────────┘            └─────────────┘
       │
       ▼
┌──────────────────┐
│ life_cycle_blocks│
└──────────────────┘
       │
       ▼
┌─────────────┐
│  archives   │
└─────────────┘
```

### Key Design Principles

1. **Normalization**: 3NF (Third Normal Form) to reduce redundancy
2. **Soft Deletes**: Use `deleted_at` timestamp instead of hard deletes
3. **Audit Trail**: Track `created_at`, `updated_at`, `created_by`
4. **UUID Primary Keys**: For security and distributed systems
5. **Foreign Key Constraints**: Maintain referential integrity
6. **Indexes**: Strategic indexing for performance
7. **JSON Fields**: For flexible, schema-less data (metadata, custom fields)

---

## 🗂️ Core Entities

### 1. **users** - User Accounts

Stores user authentication and profile information.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique user identifier |
| email | VARCHAR(255) | UNIQUE, NOT NULL | User email address |
| name | VARCHAR(255) | NULL | User display name |
| password_hash | VARCHAR(255) | NULL | Hashed password (null for OAuth users) |
| avatar_url | TEXT | NULL | Profile picture URL |
| role | ENUM | NOT NULL, DEFAULT 'user' | user, premium, admin |
| email_verified | BOOLEAN | NOT NULL, DEFAULT false | Email verification status |
| preferences | JSONB | NULL | User preferences (theme, language, etc.) |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Account creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| last_login_at | TIMESTAMPTZ | NULL | Last login timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_users_email` on `email`
- `idx_users_role` on `role`
- `idx_users_deleted_at` on `deleted_at`

---

### 2. **layers** - Life Management Layers

Represents life areas (Layer 0, Layer 1, Layer 2, etc.)

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique layer identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner of the layer |
| name | VARCHAR(255) | NOT NULL | Layer name (e.g., "Health & Safety") |
| description | TEXT | NULL | Layer description |
| layer_type | VARCHAR(50) | NOT NULL | "layer0", "custom" |
| layer_number | INTEGER | NOT NULL | 0, 1, 2, 3... for ordering |
| color | VARCHAR(7) | NULL | Hex color code (#2563EB) |
| icon | VARCHAR(50) | NULL | Icon identifier |
| is_default | BOOLEAN | NOT NULL, DEFAULT false | System-created layers |
| position | INTEGER | NOT NULL | Display order (drag to reorder) |
| metadata | JSONB | NULL | Additional custom data |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_layers_user_id` on `user_id`
- `idx_layers_user_id_position` on `(user_id, position)`
- `idx_layers_deleted_at` on `deleted_at`

**Unique Constraints:**
- `unique_layer_number_per_user` on `(user_id, layer_number)` WHERE `deleted_at IS NULL`

---

### 3. **projects** - Projects within Layers

Projects are containers for focused work within a layer.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique project identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner of the project |
| layer_id | UUID | FK(layers.id), NOT NULL | Parent layer |
| name | VARCHAR(255) | NOT NULL | Project name |
| description | TEXT | NULL | Project description |
| status | ENUM | NOT NULL, DEFAULT 'not_started' | not_started, in_progress, completed, on_hold |
| priority | ENUM | NOT NULL, DEFAULT 'medium' | low, medium, high |
| start_date | DATE | NULL | Project start date |
| target_end_date | DATE | NULL | Target completion date |
| actual_end_date | DATE | NULL | Actual completion date |
| progress_percentage | INTEGER | DEFAULT 0 | Auto-calculated: (completed_tasks / total_tasks) * 100 |
| color | VARCHAR(7) | NULL | Hex color code |
| metadata | JSONB | NULL | Custom fields, modules data |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_projects_user_id` on `user_id`
- `idx_projects_layer_id` on `layer_id`
- `idx_projects_status` on `status`
- `idx_projects_deleted_at` on `deleted_at`

---

### 4. **inputs** - Captured Ideas (Catching)

Quick capture of thoughts, ideas, and notes.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique input identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner |
| title | VARCHAR(200) | NOT NULL | Input title |
| description | TEXT | NULL | Rich text content |
| category | VARCHAR(100) | NULL | Category/area tag |
| priority | ENUM | DEFAULT 'medium' | low, medium, high |
| processed | BOOLEAN | DEFAULT false | Has been moved/processed |
| processed_at | TIMESTAMPTZ | NULL | When it was processed |
| moved_to_layer_id | UUID | FK(layers.id), NULL | Destination layer (if moved) |
| moved_to_project_id | UUID | FK(projects.id), NULL | Destination project (if moved) |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Capture timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_inputs_user_id` on `user_id`
- `idx_inputs_user_id_processed` on `(user_id, processed)`
- `idx_inputs_created_at` on `created_at` (for FIFO ordering)
- `idx_inputs_deleted_at` on `deleted_at`

---

### 5. **tasks** - Action Items (Kanban Cards)

Tasks can belong to either a layer or a project.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique task identifier |
| user_id | UUID | FK(users.id), NOT NULL | Task owner |
| layer_id | UUID | FK(layers.id), NULL | Parent layer (if not in project) |
| project_id | UUID | FK(projects.id), NULL | Parent project |
| title | VARCHAR(200) | NOT NULL | Task title |
| description | TEXT | NULL | Rich text description |
| status | ENUM | NOT NULL, DEFAULT 'todo' | todo, in_progress, completed |
| priority | ENUM | NOT NULL, DEFAULT 'medium' | low, medium, high |
| due_date | TIMESTAMPTZ | NULL | Deadline |
| completed_at | TIMESTAMPTZ | NULL | Completion timestamp |
| completion_notes | TEXT | NULL | Notes when marked complete |
| estimated_hours | DECIMAL(5,2) | NULL | Estimated effort (hours) |
| actual_hours | DECIMAL(5,2) | NULL | Actual time spent |
| tags | TEXT[] | DEFAULT '{}' | Array of tags |
| position | INTEGER | NOT NULL | Position in Kanban column |
| metadata | JSONB | NULL | Additional data |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_tasks_user_id` on `user_id`
- `idx_tasks_layer_id` on `layer_id`
- `idx_tasks_project_id` on `project_id`
- `idx_tasks_status` on `status`
- `idx_tasks_user_id_status_position` on `(user_id, status, position)` (for Kanban)
- `idx_tasks_due_date` on `due_date`
- `idx_tasks_deleted_at` on `deleted_at`

**Check Constraints:**
- `check_task_parent` CHECK `((layer_id IS NOT NULL AND project_id IS NULL) OR (layer_id IS NULL AND project_id IS NOT NULL))`
  - Ensures task belongs to either layer OR project, not both, not neither

---

### 6. **strategy_documents** - Strategic Planning Documents

Rich text documents for strategy planning (Layer or Project level).

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique document identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner |
| entity_type | ENUM | NOT NULL | "layer", "project" |
| entity_id | UUID | NOT NULL | ID of layer or project |
| content | TEXT | NULL | Rich text content (JSON or HTML) |
| version | INTEGER | NOT NULL, DEFAULT 1 | Version number |
| is_current_version | BOOLEAN | NOT NULL, DEFAULT true | Current version flag |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_strategy_user_id` on `user_id`
- `idx_strategy_entity` on `(entity_type, entity_id)`
- `idx_strategy_entity_current` on `(entity_type, entity_id, is_current_version)`
- `idx_strategy_deleted_at` on `deleted_at`

---

### 7. **habits** - Daily Habits & Routines

Tracks user habits (daily, periodic, one-time tasks).

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique habit identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner |
| layer_id | UUID | FK(layers.id), NULL | Associated layer |
| name | VARCHAR(255) | NOT NULL | Habit name |
| description | TEXT | NULL | Habit description |
| habit_type | ENUM | NOT NULL | daily, weekly, monthly, one_time |
| frequency | JSONB | NULL | Frequency details (days of week, time) |
| target_value | DECIMAL(10,2) | NULL | Target (e.g., 25 pages, 45 minutes) |
| target_unit | VARCHAR(50) | NULL | Unit (pages, minutes, reps) |
| tracking_method | ENUM | NOT NULL | checkbox, numeric, duration |
| reminder_enabled | BOOLEAN | DEFAULT false | Enable reminders |
| reminder_time | TIME | NULL | Daily reminder time |
| is_active | BOOLEAN | NOT NULL, DEFAULT true | Active status |
| start_date | DATE | NULL | Habit start date |
| end_date | DATE | NULL | Habit end date (null = indefinite) |
| current_streak | INTEGER | DEFAULT 0 | Current consecutive days |
| longest_streak | INTEGER | DEFAULT 0 | Longest streak achieved |
| metadata | JSONB | NULL | Additional data |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_habits_user_id` on `user_id`
- `idx_habits_layer_id` on `layer_id`
- `idx_habits_is_active` on `is_active`
- `idx_habits_deleted_at` on `deleted_at`

---

### 8. **habit_logs** - Habit Tracking Entries

Daily logs of habit completion.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique log identifier |
| habit_id | UUID | FK(habits.id), NOT NULL | Associated habit |
| user_id | UUID | FK(users.id), NOT NULL | Owner |
| log_date | DATE | NOT NULL | Date of the log |
| completed | BOOLEAN | NOT NULL, DEFAULT false | Completed or not |
| value | DECIMAL(10,2) | NULL | Actual value (e.g., 30 pages read) |
| notes | TEXT | NULL | Optional notes |
| logged_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | When logged |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |

**Indexes:**
- `idx_habit_logs_habit_id` on `habit_id`
- `idx_habit_logs_user_id_date` on `(user_id, log_date)`
- `idx_habit_logs_date` on `log_date`

**Unique Constraints:**
- `unique_habit_log_per_day` on `(habit_id, log_date)`

---

### 9. **life_cycle_blocks** - Daily Time Blocks

24-hour timeline blocks for daily planning.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique block identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner |
| block_date | DATE | NOT NULL | Date of the block |
| block_type | ENUM | NOT NULL | work, sleep, habit, project, free, custom |
| start_time | TIME | NOT NULL | Block start time (e.g., 08:00) |
| end_time | TIME | NOT NULL | Block end time (e.g., 19:00) |
| title | VARCHAR(255) | NULL | Block title |
| description | TEXT | NULL | Block description |
| related_layer_id | UUID | FK(layers.id), NULL | Related layer |
| related_project_id | UUID | FK(projects.id), NULL | Related project |
| related_habit_id | UUID | FK(habits.id), NULL | Related habit |
| related_task_id | UUID | FK(tasks.id), NULL | Related task |
| is_locked | BOOLEAN | DEFAULT false | Cannot be moved (e.g., work hours) |
| color | VARCHAR(7) | NULL | Display color |
| metadata | JSONB | NULL | Additional data |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |

**Indexes:**
- `idx_life_cycle_user_date` on `(user_id, block_date)`
- `idx_life_cycle_user_date_start` on `(user_id, block_date, start_time)`

**Check Constraints:**
- `check_time_order` CHECK `(start_time < end_time)`

---

### 10. **outputs** - Completed Tasks & Reflections

Store completed tasks with user reflections for learning.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique output identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner |
| task_id | UUID | FK(tasks.id), NULL | Original task (if from task) |
| layer_id | UUID | FK(layers.id), NULL | Associated layer |
| project_id | UUID | FK(projects.id), NULL | Associated project |
| title | VARCHAR(255) | NOT NULL | Output title |
| description | TEXT | NULL | Output description |
| completion_notes | TEXT | NULL | User reflections and notes |
| lessons_learned | TEXT | NULL | What was learned |
| completed_at | TIMESTAMPTZ | NOT NULL | Completion timestamp |
| time_spent_hours | DECIMAL(5,2) | NULL | Actual time spent |
| metadata | JSONB | NULL | Additional data |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Last update timestamp |
| deleted_at | TIMESTAMPTZ | NULL | Soft delete timestamp |

**Indexes:**
- `idx_outputs_user_id` on `user_id`
- `idx_outputs_layer_id` on `layer_id`
- `idx_outputs_project_id` on `project_id`
- `idx_outputs_completed_at` on `completed_at`
- `idx_outputs_deleted_at` on `deleted_at`

---

### 11. **archives** - Archived Items

Soft-archived items from any entity.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique archive identifier |
| user_id | UUID | FK(users.id), NOT NULL | Owner |
| entity_type | ENUM | NOT NULL | "task", "project", "layer", "input", "habit" |
| entity_id | UUID | NOT NULL | ID of archived entity |
| entity_data | JSONB | NOT NULL | Full snapshot of entity at archive time |
| reason | TEXT | NULL | Reason for archiving |
| archived_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Archive timestamp |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | Creation timestamp |

**Indexes:**
- `idx_archives_user_id` on `user_id`
- `idx_archives_entity` on `(entity_type, entity_id)`
- `idx_archives_archived_at` on `archived_at`

---

## 📝 Prisma Schema Definition

**File:** `prisma/schema.prisma`

```prisma
// This is your Prisma schema file

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// Enums
enum UserRole {
  user
  premium
  admin
}

enum TaskStatus {
  todo
  in_progress
  completed
}

enum ProjectStatus {
  not_started
  in_progress
  completed
  on_hold
}

enum Priority {
  low
  medium
  high
}

enum HabitType {
  daily
  weekly
  monthly
  one_time
}

enum TrackingMethod {
  checkbox
  numeric
  duration
}

enum BlockType {
  work
  sleep
  habit
  project
  free
  custom
}

enum EntityType {
  layer
  project
  task
  input
  habit
}

// Models
model User {
  id              String    @id @default(uuid()) @db.Uuid
  email           String    @unique @db.VarChar(255)
  name            String?   @db.VarChar(255)
  passwordHash    String?   @map("password_hash") @db.VarChar(255)
  avatarUrl       String?   @map("avatar_url") @db.Text
  role            UserRole  @default(user)
  emailVerified   Boolean   @default(false) @map("email_verified")
  preferences     Json?     @db.JsonB
  createdAt       DateTime  @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt       DateTime  @updatedAt @map("updated_at") @db.Timestamptz(6)
  lastLoginAt     DateTime? @map("last_login_at") @db.Timestamptz(6)
  deletedAt       DateTime? @map("deleted_at") @db.Timestamptz(6)

  // Relations
  layers          Layer[]
  projects        Project[]
  inputs          Input[]
  tasks           Task[]
  strategies      StrategyDocument[]
  habits          Habit[]
  habitLogs       HabitLog[]
  lifeCycleBlocks LifeCycleBlock[]
  outputs         Output[]
  archives        Archive[]

  @@index([email])
  @@index([role])
  @@index([deletedAt])
  @@map("users")
}

model Layer {
  id          String    @id @default(uuid()) @db.Uuid
  userId      String    @map("user_id") @db.Uuid
  name        String    @db.VarChar(255)
  description String?   @db.Text
  layerType   String    @map("layer_type") @db.VarChar(50)
  layerNumber Int       @map("layer_number")
  color       String?   @db.VarChar(7)
  icon        String?   @db.VarChar(50)
  isDefault   Boolean   @default(false) @map("is_default")
  position    Int
  metadata    Json?     @db.JsonB
  createdAt   DateTime  @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt   DateTime  @updatedAt @map("updated_at") @db.Timestamptz(6)
  deletedAt   DateTime? @map("deleted_at") @db.Timestamptz(6)

  // Relations
  user             User                @relation(fields: [userId], references: [id], onDelete: Cascade)
  projects         Project[]
  tasks            Task[]
  strategies       StrategyDocument[]
  habits           Habit[]
  lifeCycleBlocks  LifeCycleBlock[]
  inputsMoved      Input[]             @relation("InputsMovedToLayer")
  outputs          Output[]

  @@unique([userId, layerNumber], name: "unique_layer_number_per_user")
  @@index([userId])
  @@index([userId, position])
  @@index([deletedAt])
  @@map("layers")
}

model Project {
  id                 String         @id @default(uuid()) @db.Uuid
  userId             String         @map("user_id") @db.Uuid
  layerId            String         @map("layer_id") @db.Uuid
  name               String         @db.VarChar(255)
  description        String?        @db.Text
  status             ProjectStatus  @default(not_started)
  priority           Priority       @default(medium)
  startDate          DateTime?      @map("start_date") @db.Date
  targetEndDate      DateTime?      @map("target_end_date") @db.Date
  actualEndDate      DateTime?      @map("actual_end_date") @db.Date
  progressPercentage Int            @default(0) @map("progress_percentage")
  color              String?        @db.VarChar(7)
  metadata           Json?          @db.JsonB
  createdAt          DateTime       @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt          DateTime       @updatedAt @map("updated_at") @db.Timestamptz(6)
  deletedAt          DateTime?      @map("deleted_at") @db.Timestamptz(6)

  // Relations
  user             User                @relation(fields: [userId], references: [id], onDelete: Cascade)
  layer            Layer               @relation(fields: [layerId], references: [id], onDelete: Cascade)
  tasks            Task[]
  strategies       StrategyDocument[]
  lifeCycleBlocks  LifeCycleBlock[]
  inputsMoved      Input[]             @relation("InputsMovedToProject")
  outputs          Output[]

  @@index([userId])
  @@index([layerId])
  @@index([status])
  @@index([deletedAt])
  @@map("projects")
}

model Input {
  id                String    @id @default(uuid()) @db.Uuid
  userId            String    @map("user_id") @db.Uuid
  title             String    @db.VarChar(200)
  description       String?   @db.Text
  category          String?   @db.VarChar(100)
  priority          Priority  @default(medium)
  processed         Boolean   @default(false)
  processedAt       DateTime? @map("processed_at") @db.Timestamptz(6)
  movedToLayerId    String?   @map("moved_to_layer_id") @db.Uuid
  movedToProjectId  String?   @map("moved_to_project_id") @db.Uuid
  createdAt         DateTime  @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt         DateTime  @updatedAt @map("updated_at") @db.Timestamptz(6)
  deletedAt         DateTime? @map("deleted_at") @db.Timestamptz(6)

  // Relations
  user           User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  movedToLayer   Layer?    @relation("InputsMovedToLayer", fields: [movedToLayerId], references: [id])
  movedToProject Project?  @relation("InputsMovedToProject", fields: [movedToProjectId], references: [id])

  @@index([userId])
  @@index([userId, processed])
  @@index([createdAt])
  @@index([deletedAt])
  @@map("inputs")
}

model Task {
  id              String      @id @default(uuid()) @db.Uuid
  userId          String      @map("user_id") @db.Uuid
  layerId         String?     @map("layer_id") @db.Uuid
  projectId       String?     @map("project_id") @db.Uuid
  title           String      @db.VarChar(200)
  description     String?     @db.Text
  status          TaskStatus  @default(todo)
  priority        Priority    @default(medium)
  dueDate         DateTime?   @map("due_date") @db.Timestamptz(6)
  completedAt     DateTime?   @map("completed_at") @db.Timestamptz(6)
  completionNotes String?     @map("completion_notes") @db.Text
  estimatedHours  Decimal?    @map("estimated_hours") @db.Decimal(5, 2)
  actualHours     Decimal?    @map("actual_hours") @db.Decimal(5, 2)
  tags            String[]    @default([])
  position        Int
  metadata        Json?       @db.JsonB
  createdAt       DateTime    @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt       DateTime    @updatedAt @map("updated_at") @db.Timestamptz(6)
  deletedAt       DateTime?   @map("deleted_at") @db.Timestamptz(6)

  // Relations
  user             User              @relation(fields: [userId], references: [id], onDelete: Cascade)
  layer            Layer?            @relation(fields: [layerId], references: [id], onDelete: SetNull)
  project          Project?          @relation(fields: [projectId], references: [id], onDelete: Cascade)
  lifeCycleBlocks  LifeCycleBlock[]
  outputs          Output[]

  @@index([userId])
  @@index([layerId])
  @@index([projectId])
  @@index([status])
  @@index([userId, status, position])
  @@index([dueDate])
  @@index([deletedAt])
  @@map("tasks")
}

model StrategyDocument {
  id               String     @id @default(uuid()) @db.Uuid
  userId           String     @map("user_id") @db.Uuid
  entityType       EntityType @map("entity_type")
  entityId         String     @map("entity_id") @db.Uuid
  content          String?    @db.Text
  version          Int        @default(1)
  isCurrentVersion Boolean    @default(true) @map("is_current_version")
  createdAt        DateTime   @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt        DateTime   @updatedAt @map("updated_at") @db.Timestamptz(6)
  deletedAt        DateTime?  @map("deleted_at") @db.Timestamptz(6)

  // Relations
  user    User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  layer   Layer?   @relation(fields: [entityId], references: [id], onDelete: Cascade)
  project Project? @relation(fields: [entityId], references: [id], onDelete: Cascade)

  @@index([userId])
  @@index([entityType, entityId])
  @@index([entityType, entityId, isCurrentVersion])
  @@index([deletedAt])
  @@map("strategy_documents")
}

model Habit {
  id              String          @id @default(uuid()) @db.Uuid
  userId          String          @map("user_id") @db.Uuid
  layerId         String?         @map("layer_id") @db.Uuid
  name            String          @db.VarChar(255)
  description     String?         @db.Text
  habitType       HabitType       @map("habit_type")
  frequency       Json?           @db.JsonB
  targetValue     Decimal?        @map("target_value") @db.Decimal(10, 2)
  targetUnit      String?         @map("target_unit") @db.VarChar(50)
  trackingMethod  TrackingMethod  @map("tracking_method")
  reminderEnabled Boolean         @default(false) @map("reminder_enabled")
  reminderTime    DateTime?       @map("reminder_time") @db.Time(6)
  isActive        Boolean         @default(true) @map("is_active")
  startDate       DateTime?       @map("start_date") @db.Date
  endDate         DateTime?       @map("end_date") @db.Date
  currentStreak   Int             @default(0) @map("current_streak")
  longestStreak   Int             @default(0) @map("longest_streak")
  metadata        Json?           @db.JsonB
  createdAt       DateTime        @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt       DateTime        @updatedAt @map("updated_at") @db.Timestamptz(6)
  deletedAt       DateTime?       @map("deleted_at") @db.Timestamptz(6)

  // Relations
  user             User              @relation(fields: [userId], references: [id], onDelete: Cascade)
  layer            Layer?            @relation(fields: [layerId], references: [id], onDelete: SetNull)
  habitLogs        HabitLog[]
  lifeCycleBlocks  LifeCycleBlock[]

  @@index([userId])
  @@index([layerId])
  @@index([isActive])
  @@index([deletedAt])
  @@map("habits")
}

model HabitLog {
  id        String   @id @default(uuid()) @db.Uuid
  habitId   String   @map("habit_id") @db.Uuid
  userId    String   @map("user_id") @db.Uuid
  logDate   DateTime @map("log_date") @db.Date
  completed Boolean  @default(false)
  value     Decimal? @db.Decimal(10, 2)
  notes     String?  @db.Text
  loggedAt  DateTime @default(now()) @map("logged_at") @db.Timestamptz(6)
  createdAt DateTime @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt DateTime @updatedAt @map("updated_at") @db.Timestamptz(6)

  // Relations
  habit Habit @relation(fields: [habitId], references: [id], onDelete: Cascade)
  user  User  @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([habitId, logDate], name: "unique_habit_log_per_day")
  @@index([habitId])
  @@index([userId, logDate])
  @@index([logDate])
  @@map("habit_logs")
}

model LifeCycleBlock {
  id               String    @id @default(uuid()) @db.Uuid
  userId           String    @map("user_id") @db.Uuid
  blockDate        DateTime  @map("block_date") @db.Date
  blockType        BlockType @map("block_type")
  startTime        DateTime  @map("start_time") @db.Time(6)
  endTime          DateTime  @map("end_time") @db.Time(6)
  title            String?   @db.VarChar(255)
  description      String?   @db.Text
  relatedLayerId   String?   @map("related_layer_id") @db.Uuid
  relatedProjectId String?   @map("related_project_id") @db.Uuid
  relatedHabitId   String?   @map("related_habit_id") @db.Uuid
  relatedTaskId    String?   @map("related_task_id") @db.Uuid
  isLocked         Boolean   @default(false) @map("is_locked")
  color            String?   @db.VarChar(7)
  metadata         Json?     @db.JsonB
  createdAt        DateTime  @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt        DateTime  @updatedAt @map("updated_at") @db.Timestamptz(6)

  // Relations
  user           User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  relatedLayer   Layer?   @relation(fields: [relatedLayerId], references: [id], onDelete: SetNull)
  relatedProject Project? @relation(fields: [relatedProjectId], references: [id], onDelete: SetNull)
  relatedHabit   Habit?   @relation(fields: [relatedHabitId], references: [id], onDelete: SetNull)
  relatedTask    Task?    @relation(fields: [relatedTaskId], references: [id], onDelete: SetNull)

  @@index([userId, blockDate])
  @@index([userId, blockDate, startTime])
  @@map("life_cycle_blocks")
}

model Output {
  id               String    @id @default(uuid()) @db.Uuid
  userId           String    @map("user_id") @db.Uuid
  taskId           String?   @map("task_id") @db.Uuid
  layerId          String?   @map("layer_id") @db.Uuid
  projectId        String?   @map("project_id") @db.Uuid
  title            String    @db.VarChar(255)
  description      String?   @db.Text
  completionNotes  String?   @map("completion_notes") @db.Text
  lessonsLearned   String?   @map("lessons_learned") @db.Text
  completedAt      DateTime  @map("completed_at") @db.Timestamptz(6)
  timeSpentHours   Decimal?  @map("time_spent_hours") @db.Decimal(5, 2)
  metadata         Json?     @db.JsonB
  createdAt        DateTime  @default(now()) @map("created_at") @db.Timestamptz(6)
  updatedAt        DateTime  @updatedAt @map("updated_at") @db.Timestamptz(6)
  deletedAt        DateTime? @map("deleted_at") @db.Timestamptz(6)

  // Relations
  user    User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  task    Task?    @relation(fields: [taskId], references: [id], onDelete: SetNull)
  layer   Layer?   @relation(fields: [layerId], references: [id], onDelete: SetNull)
  project Project? @relation(fields: [projectId], references: [id], onDelete: SetNull)

  @@index([userId])
  @@index([layerId])
  @@index([projectId])
  @@index([completedAt])
  @@index([deletedAt])
  @@map("outputs")
}

model Archive {
  id         String     @id @default(uuid()) @db.Uuid
  userId     String     @map("user_id") @db.Uuid
  entityType EntityType @map("entity_type")
  entityId   String     @map("entity_id") @db.Uuid
  entityData Json       @map("entity_data") @db.JsonB
  reason     String?    @db.Text
  archivedAt DateTime   @default(now()) @map("archived_at") @db.Timestamptz(6)
  createdAt  DateTime   @default(now()) @map("created_at") @db.Timestamptz(6)

  // Relations
  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId])
  @@index([entityType, entityId])
  @@index([archivedAt])
  @@map("archives")
}
```

---

## 🚀 Indexes & Performance

### Index Strategy

1. **Primary Keys**: Automatic B-tree indexes
2. **Foreign Keys**: Index all foreign key columns
3. **Frequently Filtered Columns**: `user_id`, `status`, `deleted_at`
4. **Sorting Columns**: `created_at`, `position`, `due_date`
5. **Composite Indexes**: For multi-column queries (e.g., `user_id` + `status`)

### Query Performance Tips

```sql
-- Good: Uses index on (user_id, status, position)
SELECT * FROM tasks 
WHERE user_id = 'xxx' AND status = 'todo'
ORDER BY position;

-- Bad: Full table scan
SELECT * FROM tasks 
WHERE LOWER(title) LIKE '%keyword%';

-- Better: Use full-text search (future enhancement)
SELECT * FROM tasks 
WHERE to_tsvector('english', title) @@ to_tsquery('keyword');
```

---

## 🔒 Row Level Security (RLS)

Supabase RLS policies ensure users can only access their own data.

**Example Policies:**

```sql
-- Users can only view their own layers
CREATE POLICY "Users can view own layers"
ON layers FOR SELECT
USING (auth.uid() = user_id);

-- Users can only insert layers for themselves
CREATE POLICY "Users can insert own layers"
ON layers FOR INSERT
WITH CHECK (auth.uid() = user_id);

-- Users can only update their own tasks
CREATE POLICY "Users can update own tasks"
ON tasks FOR UPDATE
USING (auth.uid() = user_id);

-- Users can only delete their own inputs
CREATE POLICY "Users can delete own inputs"
ON inputs FOR DELETE
USING (auth.uid() = user_id);
```

---

## 📦 Data Migrations

### Migration Workflow

```bash
# Create migration
npx prisma migrate dev --name init_database

# Apply migration to production
npx prisma migrate deploy

# Generate Prisma Client
npx prisma generate

# Reset database (development only)
npx prisma migrate reset
```

### Migration Best Practices

1. **Test on staging first**
2. **Backup before migrating production**
3. **Keep migrations small and focused**
4. **Never edit applied migrations**
5. **Use transactions for data migrations**

---

## 🌱 Seed Data

**File:** `prisma/seed.ts`

```typescript
import { PrismaClient } from '@prisma/client'
import { hash } from 'bcrypt'

const prisma = new PrismaClient()

async function main() {
  // Create test user
  const hashedPassword = await hash('password123', 10)
  
  const user = await prisma.user.upsert({
    where: { email: 'test@lifeos.com' },
    update: {},
    create: {
      email: 'test@lifeos.com',
      name: 'Test User',
      passwordHash: hashedPassword,
      emailVerified: true,
      role: 'user',
    },
  })

  // Create Layer 0 (Ben)
  const layer0 = await prisma.layer.create({
    data: {
      userId: user.id,
      name: 'Ben (Self)',
      description: 'Core personal management layer',
      layerType: 'layer0',
      layerNumber: 0,
      color: '#2563EB',
      icon: 'user',
      isDefault: true,
      position: 0,
    },
  })

  // Create sample tasks
  await prisma.task.createMany({
    data: [
      {
        userId: user.id,
        layerId: layer0.id,
        title: 'Complete project setup',
        status: 'in_progress',
        priority: 'high',
        position: 0,
      },
      {
        userId: user.id,
        layerId: layer0.id,
        title: 'Read documentation',
        status: 'todo',
        priority: 'medium',
        position: 1,
      },
    ],
  })

  console.log('✅ Database seeded successfully')
}

main()
  .catch((e) => {
    console.error(e)
    process.exit(1)
  })
  .finally(async () => {
    await prisma.$disconnect()
  })
```

**Run seed:**
```bash
npx prisma db seed
```

---

## ✅ Summary

This database schema provides:
- **Scalability**: UUID primary keys, indexed queries
- **Flexibility**: JSON fields for custom data
- **Integrity**: Foreign key constraints, soft deletes
- **Security**: Row Level Security policies
- **Performance**: Strategic indexing
- **Auditability**: Created/updated timestamps

**Next Steps:**
1. Review schema with stakeholders
2. Create initial migration
3. Apply to development database
4. Implement RLS policies in Supabase
5. Build API layer on top of schema

---

**Document Control:**
- Version 1.0: Initial Database Schema Design (October 8, 2025)
- Next Review: After MVP testing
