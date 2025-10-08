# API Endpoints Specification
# LifeOS - Life Management Operating System

**Version:** 1.0  
**Last Updated:** October 8, 2025  
**Base URL:** `/api/v1`  
**Status:** Draft

---

## 📋 Table of Contents

1. [API Overview](#api-overview)
2. [Authentication Endpoints](#authentication-endpoints)
3. [User Endpoints](#user-endpoints)
4. [Input Endpoints](#input-endpoints)
5. [Layer Endpoints](#layer-endpoints)
6. [Project Endpoints](#project-endpoints)
7. [Task Endpoints](#task-endpoints)
8. [Strategy Document Endpoints](#strategy-document-endpoints)
9. [Habit Endpoints](#habit-endpoints)
10. [Life Cycle Endpoints](#life-cycle-endpoints)
11. [Output Endpoints](#output-endpoints)
12. [Archive Endpoints](#archive-endpoints)
13. [Common Patterns](#common-patterns)

---

## 🎯 API Overview

### Base Configuration

- **Base URL:** `https://traderdevlabs.com/lifeos/api/v1`
- **Protocol:** HTTPS only
- **Authentication:** JWT Bearer tokens in `Authorization` header
- **Content-Type:** `application/json`
- **Rate Limiting:** 1000 requests/hour (authenticated users)

### Standard Response Format

**Success Response:**
```json
{
  "data": { /* response data */ },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": "2025-10-08T12:34:56Z"
  }
}
```

**Error Response:**
```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": [ /* optional error details */ ]
  },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": "2025-10-08T12:34:56Z"
  }
}
```

### HTTP Status Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 200 | OK | Successful GET, PATCH, DELETE |
| 201 | Created | Successful POST (resource created) |
| 204 | No Content | Successful DELETE (no response body) |
| 400 | Bad Request | Invalid request data |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Resource conflict (e.g., duplicate) |
| 422 | Unprocessable Entity | Validation errors |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server-side error |

---

## 🔐 Authentication Endpoints

### Register User

Create a new user account.

**Endpoint:** `POST /auth/register`

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "<your_password>",
  "name": "John Doe"
}
```

**Validation:**
- `email`: Valid email format, not already registered
- `password`: Min 8 chars, 1 uppercase, 1 number, 1 special char
- `name`: Optional, max 255 chars

**Response:** `201 Created`
```json
{
  "data": {
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "name": "John Doe",
      "emailVerified": false,
      "role": "user",
      "createdAt": "2025-10-08T12:34:56Z"
    },
    "message": "Verification email sent to user@example.com"
  }
}
```

---

### Login

Authenticate and receive access token.

**Endpoint:** `POST /auth/login`

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "<your_password>"
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "name": "John Doe",
      "avatarUrl": "https://...",
      "role": "user"
    },
    "accessToken": "<jwt_access_token>",
    "refreshToken": "<jwt_refresh_token>",
    "expiresIn": 3600
  }
}
```

**Errors:**
- `401`: Invalid credentials
- `403`: Email not verified

---

### Logout

Invalidate current session.

**Endpoint:** `POST /auth/logout`

**Headers:**
```
Authorization: Bearer {accessToken}
```

**Response:** `204 No Content`

---

### Get Current Session

Get authenticated user's session.

**Endpoint:** `GET /auth/session`

**Headers:**
```
Authorization: Bearer {accessToken}
```

**Response:** `200 OK`
```json
{
  "data": {
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "name": "John Doe",
      "avatarUrl": "https://...",
      "role": "user",
      "emailVerified": true
    },
    "expiresAt": "2025-10-08T13:34:56Z"
  }
}
```

---

### Reset Password

Request password reset email.

**Endpoint:** `POST /auth/reset-password`

**Request Body:**
```json
{
  "email": "user@example.com"
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "message": "If the email exists, a reset link has been sent."
  }
}
```

---

## 👤 User Endpoints

### Get Current User Profile

**Endpoint:** `GET /users/me`

**Headers:**
```
Authorization: Bearer {accessToken}
```

**Response:** `200 OK`
```json
{
  "data": {
    "id": "user_123",
    "email": "user@example.com",
    "name": "John Doe",
    "avatarUrl": "https://...",
    "role": "user",
    "emailVerified": true,
    "preferences": {
      "theme": "dark",
      "language": "en",
      "timezone": "Europe/Istanbul"
    },
    "createdAt": "2025-01-15T10:00:00Z",
    "lastLoginAt": "2025-10-08T12:34:56Z"
  }
}
```

---

### Update User Profile

**Endpoint:** `PATCH /users/me`

**Request Body:**
```json
{
  "name": "John Smith",
  "avatarUrl": "https://...",
  "preferences": {
    "theme": "light",
    "language": "tr"
  }
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "id": "user_123",
    "email": "user@example.com",
    "name": "John Smith",
    "avatarUrl": "https://...",
    "preferences": {
      "theme": "light",
      "language": "tr",
      "timezone": "Europe/Istanbul"
    },
    "updatedAt": "2025-10-08T12:35:00Z"
  }
}
```

---

### Delete User Account

**Endpoint:** `DELETE /users/me`

**Response:** `204 No Content`

---

## 📥 Input Endpoints

### List Inputs

Get all inputs for the authenticated user.

**Endpoint:** `GET /inputs`

**Query Parameters:**
- `processed`: `true` | `false` - Filter by processed status
- `category`: `string` - Filter by category
- `priority`: `low` | `medium` | `high`
- `limit`: `number` (default: 50, max: 100)
- `cursor`: `string` - Pagination cursor

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "input_123",
      "userId": "user_123",
      "title": "Build trading journal module",
      "description": "Need to track all my trades systematically...",
      "category": "Trading",
      "priority": "high",
      "processed": false,
      "processedAt": null,
      "movedToLayerId": null,
      "movedToProjectId": null,
      "createdAt": "2025-10-07T15:30:00Z",
      "updatedAt": "2025-10-07T15:30:00Z"
    }
  ],
  "pagination": {
    "nextCursor": "input_150",
    "hasMore": true,
    "total": 234
  }
}
```

---

### Create Input

**Endpoint:** `POST /inputs`

**Request Body:**
```json
{
  "title": "Learn Prisma ORM",
  "description": "Need to understand database migrations and queries",
  "category": "Learning",
  "priority": "medium"
}
```

**Response:** `201 Created`
```json
{
  "data": {
    "id": "input_124",
    "userId": "user_123",
    "title": "Learn Prisma ORM",
    "description": "Need to understand database migrations and queries",
    "category": "Learning",
    "priority": "medium",
    "processed": false,
    "createdAt": "2025-10-08T12:40:00Z",
    "updatedAt": "2025-10-08T12:40:00Z"
  }
}
```

---

### Update Input

**Endpoint:** `PATCH /inputs/:id`

**Request Body:**
```json
{
  "title": "Master Prisma ORM",
  "priority": "high"
}
```

**Response:** `200 OK`

---

### Delete Input

**Endpoint:** `DELETE /inputs/:id`

**Response:** `204 No Content`

---

### Move Input to Layer/Project

**Endpoint:** `POST /inputs/:id/move`

**Request Body:**
```json
{
  "movedToLayerId": "layer_123",
  "movedToProjectId": null
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "id": "input_124",
    "processed": true,
    "processedAt": "2025-10-08T12:45:00Z",
    "movedToLayerId": "layer_123",
    "movedToProjectId": null
  }
}
```

---

## 🏗️ Layer Endpoints

### List Layers

Get all layers for authenticated user.

**Endpoint:** `GET /layers`

**Query Parameters:**
- `includeDeleted`: `boolean` - Include soft-deleted layers

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "layer_001",
      "userId": "user_123",
      "name": "Ben (Self)",
      "description": "Core personal management",
      "layerType": "layer0",
      "layerNumber": 0,
      "color": "#2563EB",
      "icon": "user",
      "isDefault": true,
      "position": 0,
      "createdAt": "2025-01-15T10:01:00Z",
      "updatedAt": "2025-01-15T10:01:00Z"
    },
    {
      "id": "layer_002",
      "userId": "user_123",
      "name": "Sağlık ve Güvenlik",
      "description": "Health and safety management",
      "layerType": "custom",
      "layerNumber": 1,
      "color": "#10B981",
      "icon": "heart",
      "isDefault": false,
      "position": 1,
      "createdAt": "2025-01-20T14:00:00Z",
      "updatedAt": "2025-01-20T14:00:00Z"
    }
  ]
}
```

---

### Create Layer

**Endpoint:** `POST /layers`

**Request Body:**
```json
{
  "name": "Finansal Güvenlik",
  "description": "Financial planning and investment tracking",
  "layerNumber": 2,
  "color": "#F59E0B",
  "icon": "dollar-sign"
}
```

**Response:** `201 Created`

---

### Get Layer by ID

**Endpoint:** `GET /layers/:id`

**Response:** `200 OK`

---

### Update Layer

**Endpoint:** `PATCH /layers/:id`

**Request Body:**
```json
{
  "name": "Finansal Yönetim ve Güvenlik",
  "description": "Updated description",
  "color": "#EF4444"
}
```

**Response:** `200 OK`

---

### Delete Layer

Soft delete a layer.

**Endpoint:** `DELETE /layers/:id`

**Response:** `204 No Content`

**Note:** Cannot delete Layer 0 (Ben)

---

### Reorder Layers

**Endpoint:** `PATCH /layers/reorder`

**Request Body:**
```json
{
  "layerIds": [
    "layer_001",
    "layer_002",
    "layer_003"
  ]
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "updated": 3,
    "layers": [
      { "id": "layer_001", "position": 0 },
      { "id": "layer_002", "position": 1 },
      { "id": "layer_003", "position": 2 }
    ]
  }
}
```

---

## 📂 Project Endpoints

### List Projects

**Endpoint:** `GET /projects`

**Query Parameters:**
- `layerId`: `string` - Filter by layer
- `status`: `not_started` | `in_progress` | `completed` | `on_hold`
- `priority`: `low` | `medium` | `high`

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "project_123",
      "userId": "user_123",
      "layerId": "layer_002",
      "name": "TraderDevLabs Brand Building",
      "description": "Build personal trading brand",
      "status": "in_progress",
      "priority": "high",
      "startDate": "2025-10-01",
      "targetEndDate": "2025-12-31",
      "actualEndDate": null,
      "progressPercentage": 35,
      "color": "#8B5CF6",
      "createdAt": "2025-10-01T08:00:00Z",
      "updatedAt": "2025-10-08T12:00:00Z"
    }
  ]
}
```

---

### Create Project

**Endpoint:** `POST /projects`

**Request Body:**
```json
{
  "layerId": "layer_002",
  "name": "Trading Journal System",
  "description": "Build comprehensive trading journal",
  "priority": "high",
  "startDate": "2025-10-15",
  "targetEndDate": "2025-11-30",
  "color": "#3B82F6"
}
```

**Response:** `201 Created`

---

### Get Project by ID

**Endpoint:** `GET /projects/:id`

**Query Parameters:**
- `include`: `tasks,strategy` - Include related data

**Response:** `200 OK`
```json
{
  "data": {
    "id": "project_123",
    "name": "Trading Journal System",
    "status": "in_progress",
    "progressPercentage": 35,
    "tasksCount": {
      "total": 20,
      "completed": 7,
      "inProgress": 5,
      "todo": 8
    },
    "strategy": {
      "id": "strategy_456",
      "content": "Project strategy content...",
      "version": 2
    }
  }
}
```

---

### Update Project

**Endpoint:** `PATCH /projects/:id`

**Request Body:**
```json
{
  "status": "completed",
  "actualEndDate": "2025-10-25"
}
```

**Response:** `200 OK`

---

### Delete Project

**Endpoint:** `DELETE /projects/:id`

**Response:** `204 No Content`

---

## ✅ Task Endpoints

### List Tasks

**Endpoint:** `GET /tasks`

**Query Parameters:**
- `layerId`: `string` - Filter by layer
- `projectId`: `string` - Filter by project
- `status`: `todo` | `in_progress` | `completed`
- `priority`: `low` | `medium` | `high`
- `dueDate`: `YYYY-MM-DD` - Filter by due date
- `tags`: `string[]` - Filter by tags

**Response:** `200 OK`
```json
{
  "data": {
    "todo": [
      {
        "id": "task_123",
        "userId": "user_123",
        "layerId": "layer_001",
        "projectId": null,
        "title": "Complete PRD document",
        "description": "Finish product requirements",
        "status": "todo",
        "priority": "high",
        "dueDate": "2025-10-10T23:59:59Z",
        "tags": ["documentation", "planning"],
        "position": 0,
        "createdAt": "2025-10-08T09:00:00Z"
      }
    ],
    "in_progress": [],
    "completed": []
  }
}
```

---

### Create Task

**Endpoint:** `POST /tasks`

**Request Body:**
```json
{
  "layerId": "layer_001",
  "title": "Setup development environment",
  "description": "Install Node.js, setup Git, configure VSCode",
  "priority": "high",
  "dueDate": "2025-10-09T18:00:00Z",
  "estimatedHours": 2,
  "tags": ["setup", "development"]
}
```

**Response:** `201 Created`

---

### Get Task by ID

**Endpoint:** `GET /tasks/:id`

**Response:** `200 OK`

---

### Update Task

**Endpoint:** `PATCH /tasks/:id`

**Request Body:**
```json
{
  "title": "Setup complete development environment",
  "status": "in_progress",
  "actualHours": 1.5
}
```

**Response:** `200 OK`

---

### Move Task (Kanban)

Move task between Kanban columns.

**Endpoint:** `PATCH /tasks/:id/move`

**Request Body:**
```json
{
  "status": "in_progress",
  "position": 2
}
```

**Response:** `200 OK`

---

### Complete Task

Mark task as completed and add notes.

**Endpoint:** `POST /tasks/:id/complete`

**Request Body:**
```json
{
  "completionNotes": "Successfully set up environment. Learned about pnpm workspaces.",
  "actualHours": 2.5
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "task": {
      "id": "task_123",
      "status": "completed",
      "completedAt": "2025-10-08T16:30:00Z",
      "completionNotes": "Successfully set up environment...",
      "actualHours": 2.5
    },
    "output": {
      "id": "output_789",
      "taskId": "task_123",
      "title": "Setup development environment",
      "completionNotes": "Successfully set up environment...",
      "createdAt": "2025-10-08T16:30:00Z"
    }
  }
}
```

---

### Delete Task

**Endpoint:** `DELETE /tasks/:id`

**Response:** `204 No Content`

---

## 📄 Strategy Document Endpoints

### Get Strategy Document

**Endpoint:** `GET /strategies/:type/:id`

**Path Parameters:**
- `type`: `layer` | `project`
- `id`: Layer ID or Project ID

**Query Parameters:**
- `version`: `number` - Get specific version (default: current)

**Response:** `200 OK`
```json
{
  "data": {
    "id": "strategy_456",
    "userId": "user_123",
    "entityType": "layer",
    "entityId": "layer_001",
    "content": "<h1>Master Life Strategy</h1><p>Current State (Point A)...</p>",
    "version": 3,
    "isCurrentVersion": true,
    "createdAt": "2025-10-01T10:00:00Z",
    "updatedAt": "2025-10-08T14:00:00Z"
  }
}
```

---

### Update Strategy Document

**Endpoint:** `PATCH /strategies/:type/:id`

**Request Body:**
```json
{
  "content": "<h1>Updated Strategy</h1><p>New content...</p>"
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "id": "strategy_457",
    "version": 4,
    "isCurrentVersion": true,
    "previousVersionId": "strategy_456",
    "updatedAt": "2025-10-08T15:00:00Z"
  }
}
```

---

### Get Strategy Versions

**Endpoint:** `GET /strategies/:type/:id/versions`

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "strategy_457",
      "version": 4,
      "isCurrentVersion": true,
      "updatedAt": "2025-10-08T15:00:00Z"
    },
    {
      "id": "strategy_456",
      "version": 3,
      "isCurrentVersion": false,
      "updatedAt": "2025-10-08T14:00:00Z"
    }
  ]
}
```

---

## 🎯 Habit Endpoints

### List Habits

**Endpoint:** `GET /habits`

**Query Parameters:**
- `layerId`: `string` - Filter by layer
- `isActive`: `boolean` - Filter by active status
- `habitType`: `daily` | `weekly` | `monthly` | `one_time`

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "habit_123",
      "userId": "user_123",
      "layerId": "layer_001",
      "name": "Morning Reading",
      "description": "Read 25 pages every morning",
      "habitType": "daily",
      "targetValue": 25,
      "targetUnit": "pages",
      "trackingMethod": "numeric",
      "reminderEnabled": true,
      "reminderTime": "07:00:00",
      "isActive": true,
      "currentStreak": 12,
      "longestStreak": 45,
      "createdAt": "2025-09-01T08:00:00Z"
    }
  ]
}
```

---

### Create Habit

**Endpoint:** `POST /habits`

**Request Body:**
```json
{
  "layerId": "layer_001",
  "name": "Exercise",
  "description": "Daily workout routine",
  "habitType": "daily",
  "targetValue": 45,
  "targetUnit": "minutes",
  "trackingMethod": "duration",
  "reminderEnabled": true,
  "reminderTime": "06:00:00",
  "startDate": "2025-10-09"
}
```

**Response:** `201 Created`

---

### Update Habit

**Endpoint:** `PATCH /habits/:id`

**Response:** `200 OK`

---

### Delete Habit

**Endpoint:** `DELETE /habits/:id`

**Response:** `204 No Content`

---

### Log Habit Completion

**Endpoint:** `POST /habits/:id/log`

**Request Body:**
```json
{
  "logDate": "2025-10-08",
  "completed": true,
  "value": 30,
  "notes": "Great workout session today!"
}
```

**Response:** `201 Created`
```json
{
  "data": {
    "habitLog": {
      "id": "log_789",
      "habitId": "habit_123",
      "logDate": "2025-10-08",
      "completed": true,
      "value": 30,
      "notes": "Great workout session today!",
      "loggedAt": "2025-10-08T18:30:00Z"
    },
    "habit": {
      "id": "habit_123",
      "currentStreak": 13,
      "longestStreak": 45
    }
  }
}
```

---

## 📅 Life Cycle Endpoints

### Get Life Cycle Schedule

Get 24-hour schedule for a specific date.

**Endpoint:** `GET /life-cycle/:date`

**Path Parameters:**
- `date`: `YYYY-MM-DD`

**Response:** `200 OK`
```json
{
  "data": {
    "date": "2025-10-08",
    "blocks": [
      {
        "id": "block_123",
        "blockType": "sleep",
        "startTime": "23:00:00",
        "endTime": "07:00:00",
        "title": "Sleep",
        "isLocked": true,
        "color": "#6366F1"
      },
      {
        "id": "block_124",
        "blockType": "work",
        "startTime": "08:00:00",
        "endTime": "19:00:00",
        "title": "Work - Police Duty",
        "isLocked": true,
        "color": "#EF4444"
      },
      {
        "id": "block_125",
        "blockType": "habit",
        "startTime": "19:30:00",
        "endTime": "20:15:00",
        "title": "Exercise",
        "relatedHabitId": "habit_123",
        "isLocked": false,
        "color": "#10B981"
      },
      {
        "id": "block_126",
        "blockType": "project",
        "startTime": "20:30:00",
        "endTime": "22:30:00",
        "title": "TraderDevLabs Work",
        "relatedProjectId": "project_123",
        "isLocked": false,
        "color": "#8B5CF6"
      }
    ],
    "statistics": {
      "totalScheduled": 22.5,
      "freeTime": 1.5,
      "workHours": 11,
      "sleepHours": 8,
      "habitHours": 0.75,
      "projectHours": 2
    }
  }
}
```

---

### Update Life Cycle Schedule

**Endpoint:** `PATCH /life-cycle/:date`

**Request Body:**
```json
{
  "blocks": [
    {
      "id": "block_125",
      "startTime": "19:00:00",
      "endTime": "19:45:00"
    },
    {
      "blockType": "free",
      "startTime": "22:30:00",
      "endTime": "23:00:00",
      "title": "Free time"
    }
  ]
}
```

**Response:** `200 OK`

---

## 📊 Output Endpoints

### List Outputs

**Endpoint:** `GET /outputs`

**Query Parameters:**
- `layerId`: `string`
- `projectId`: `string`
- `startDate`: `YYYY-MM-DD`
- `endDate`: `YYYY-MM-DD`
- `limit`: `number`
- `cursor`: `string`

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "output_789",
      "userId": "user_123",
      "taskId": "task_123",
      "layerId": "layer_001",
      "title": "Setup development environment",
      "completionNotes": "Successfully configured...",
      "lessonsLearned": "Learned about pnpm workspaces",
      "completedAt": "2025-10-08T16:30:00Z",
      "timeSpentHours": 2.5
    }
  ],
  "pagination": {
    "nextCursor": "output_800",
    "hasMore": true
  }
}
```

---

### Get Output Statistics

**Endpoint:** `GET /outputs/stats`

**Query Parameters:**
- `period`: `day` | `week` | `month` | `year`
- `startDate`: `YYYY-MM-DD`
- `endDate`: `YYYY-MM-DD`

**Response:** `200 OK`
```json
{
  "data": {
    "totalOutputs": 147,
    "totalTimeSpent": 352.5,
    "averageTimePerTask": 2.4,
    "outputsByLayer": [
      {
        "layerId": "layer_001",
        "layerName": "Ben (Self)",
        "count": 87,
        "timeSpent": 210.5
      }
    ],
    "outputsByPriority": {
      "high": 45,
      "medium": 78,
      "low": 24
    },
    "trends": [
      {
        "date": "2025-10-01",
        "count": 5,
        "timeSpent": 12.5
      },
      {
        "date": "2025-10-02",
        "count": 7,
        "timeSpent": 16.0
      }
    ]
  }
}
```

---

## 🗄️ Archive Endpoints

### List Archives

**Endpoint:** `GET /archives`

**Query Parameters:**
- `entityType`: `task` | `project` | `layer` | `input` | `habit`
- `startDate`: `YYYY-MM-DD`
- `endDate`: `YYYY-MM-DD`

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "archive_123",
      "userId": "user_123",
      "entityType": "project",
      "entityId": "project_old_123",
      "entityData": {
        "name": "Old Project",
        "status": "completed"
      },
      "reason": "Project completed and archived",
      "archivedAt": "2025-09-30T12:00:00Z"
    }
  ]
}
```

---

### Archive Item

**Endpoint:** `POST /archives`

**Request Body:**
```json
{
  "entityType": "project",
  "entityId": "project_123",
  "reason": "Project completed successfully"
}
```

**Response:** `201 Created`

---

### Get Archive by ID

**Endpoint:** `GET /archives/:id`

**Response:** `200 OK`

---

### Delete Archive (Permanent)

**Endpoint:** `DELETE /archives/:id`

**Response:** `204 No Content`

---

## 🔄 Common Patterns

### Pagination

All list endpoints support cursor-based pagination:

**Request:**
```
GET /tasks?limit=20&cursor=task_100
```

**Response:**
```json
{
  "data": [...],
  "pagination": {
    "nextCursor": "task_120",
    "hasMore": true,
    "total": 500
  }
}
```

---

### Filtering

Use query parameters for filtering:

```
GET /tasks?status=in_progress&priority=high&layerId=layer_001
```

---

### Sorting

Use `sortBy` and `sortOrder`:

```
GET /tasks?sortBy=dueDate&sortOrder=asc
```

---

### Including Relations

Use `include` parameter:

```
GET /projects/123?include=tasks,strategy
```

---

### Rate Limit Headers

All responses include rate limit headers:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 945
X-RateLimit-Reset: 1696780800
```

---

## 🔒 Authentication

All endpoints (except `/auth/*`) require authentication.

**Header:**
```
Authorization: Bearer <your_access_token>
```

**Error Response:**
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing or invalid authentication token"
  }
}
```

---

## ✅ Validation Errors

Validation errors return structured details:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format",
        "value": "not-an-email"
      },
      {
        "field": "password",
        "message": "Password must be at least 8 characters",
        "value": "***"
      }
    ]
  }
}
```

---

## 📚 OpenAPI/Swagger

Full interactive API documentation available at:

```
https://traderdevlabs.com/lifeos/api/docs
```

---

**Document Control:**
- Version 1.0: Initial API Specification (October 8, 2025)
- Next Review: After MVP implementation
