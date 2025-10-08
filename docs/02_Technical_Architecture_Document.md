# Technical Architecture Document
# LifeOS - Life Management Operating System

**Version:** 1.0  
**Last Updated:** October 8, 2025  
**Document Owner:** Technical Team  
**Status:** Draft

---

## 📋 Table of Contents

1. [Executive Summary](#executive-summary)
2. [System Overview](#system-overview)
3. [Technology Stack](#technology-stack)
4. [Architecture Design](#architecture-design)
5. [Frontend Architecture](#frontend-architecture)
6. [Backend Architecture](#backend-architecture)
7. [Database Design](#database-design)
8. [API Design](#api-design)
9. [Security Architecture](#security-architecture)
10. [Performance & Scalability](#performance--scalability)
11. [Deployment Architecture](#deployment-architecture)
12. [Development Workflow](#development-workflow)
13. [Monitoring & Observability](#monitoring--observability)

---

## 🎯 Executive Summary

This document outlines the technical architecture for **LifeOS**, a comprehensive life management operating system. The architecture is designed to support:

- **Scalability**: Handle thousands of concurrent users
- **Performance**: Sub-2-second page loads, real-time updates
- **Maintainability**: Modular, well-documented codebase
- **Security**: Enterprise-grade security practices
- **Developer Experience**: Modern tooling, clear patterns

### Architecture Principles

1. **Separation of Concerns**: Clear boundaries between presentation, business logic, and data
2. **Modularity**: Features organized as independent, reusable modules
3. **API-First**: All functionality exposed through well-defined APIs
4. **Mobile-First**: Responsive design from the ground up
5. **Progressive Enhancement**: Core functionality works everywhere, enhanced features for modern browsers
6. **Data Integrity**: ACID-compliant database operations
7. **Performance by Default**: Optimization built into the architecture

---

## 🔭 System Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT TIER                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Web App    │  │  Mobile Web  │  │ Future: Apps │      │
│  │  (Next.js)   │  │  (Responsive)│  │  iOS/Android │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      API GATEWAY                             │
│              (Next.js API Routes / Edge)                     │
│    ┌──────────┐  ┌──────────┐  ┌──────────┐               │
│    │   Auth   │  │   REST   │  │ Real-time│               │
│    │Middleware│  │   APIs   │  │ WebSocket│               │
│    └──────────┘  └──────────┘  └──────────┘               │
└─────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  APPLICATION TIER                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Business   │  │   Service    │  │   External   │      │
│  │    Logic     │  │    Layer     │  │  Integrations│      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     DATA TIER                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  PostgreSQL  │  │     Redis    │  │   Storage    │      │
│  │  (Supabase)  │  │   (Cache)    │  │   (Files)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### Architecture Style

**Monolithic-First with Modular Design**
- Single Next.js application (easier to develop, deploy, and scale initially)
- Modular code organization (easy to extract microservices later if needed)
- Clear separation of concerns (frontend, API, business logic)

---

## 🛠️ Technology Stack

### Frontend

| Technology | Version | Purpose |
|------------|---------|---------|
| **Next.js** | 14.x | React framework with SSR, routing, API routes |
| **React** | 18.x | UI library for component-based architecture |
| **TypeScript** | 5.x | Type safety, better developer experience |
| **Tailwind CSS** | 3.x | Utility-first CSS framework |
| **Radix UI** | Latest | Accessible, unstyled UI primitives |
| **shadcn/ui** | Latest | Beautiful, customizable component library |
| **React Hook Form** | 7.x | Form state management and validation |
| **Zod** | 3.x | Schema validation |
| **Zustand** | 4.x | Lightweight state management |
| **TanStack Query** | 5.x | Server state management, caching |
| **dnd-kit** | 6.x | Drag-and-drop functionality (Kanban) |
| **date-fns** | 3.x | Date manipulation utilities |
| **recharts** | 2.x | Charts and data visualization |
| **Tiptap** | 2.x | Rich text editor |
| **Lucide React** | Latest | Icon library |

### Backend

| Technology | Version | Purpose |
|------------|---------|---------|
| **Next.js API Routes** | 14.x | Backend API endpoints |
| **Supabase** | Latest | Backend-as-a-Service (Auth, Database, Storage) |
| **PostgreSQL** | 15+ | Primary database (via Supabase) |
| **Prisma** | 5.x | Database ORM and migrations |
| **NextAuth.js** | 4.x | Authentication (email, Google, Apple) |
| **Zod** | 3.x | API input validation |
| **jose** | 5.x | JWT handling |

### Infrastructure & DevOps

| Technology | Purpose |
|------------|---------|
| **Vercel** | Hosting, deployment, edge functions |
| **Supabase Cloud** | Managed PostgreSQL, Auth, Storage |
| **GitHub Actions** | CI/CD pipelines |
| **Docker** | Local development containers |
| **Sentry** | Error tracking and monitoring |
| **Vercel Analytics** | Performance monitoring |

### Development Tools

| Tool | Purpose |
|------|---------|
| **ESLint** | Code linting |
| **Prettier** | Code formatting |
| **Husky** | Git hooks |
| **lint-staged** | Pre-commit linting |
| **Jest** | Unit testing |
| **Playwright** | End-to-end testing |
| **Storybook** | Component documentation |

---

## 🏛️ Architecture Design

### Architectural Patterns

#### 1. **Feature-Based Architecture**

Organize code by feature rather than technical layer:

```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api/
│   │   └── types/
│   ├── dashboard/
│   ├── inputs/
│   ├── tasks/
│   ├── layers/
│   └── projects/
```

#### 2. **Repository Pattern**

Abstraction layer between business logic and data access:

```typescript
// Data access layer
class TaskRepository {
  async findById(id: string): Promise<Task>
  async findByUserId(userId: string): Promise<Task[]>
  async create(task: CreateTaskDTO): Promise<Task>
  async update(id: string, data: UpdateTaskDTO): Promise<Task>
  async delete(id: string): Promise<void>
}
```

#### 3. **Service Layer Pattern**

Business logic separated from API routes:

```typescript
// Service layer
class TaskService {
  constructor(private taskRepo: TaskRepository) {}
  
  async createTask(userId: string, data: CreateTaskDTO) {
    // Business logic: validation, permissions, side effects
    const task = await this.taskRepo.create({...data, userId})
    await this.notificationService.scheduleReminder(task)
    return task
  }
}
```

#### 4. **API Route Pattern**

Thin controller layer that delegates to services:

```typescript
// API route: /api/tasks
export async function POST(request: Request) {
  const session = await getServerSession()
  const data = await request.json()
  const validated = CreateTaskSchema.parse(data)
  const task = await taskService.createTask(session.user.id, validated)
  return NextResponse.json(task, { status: 201 })
}
```

---

## 🎨 Frontend Architecture

### Component Architecture

#### Component Hierarchy

```
App (Layout)
├── Navigation
│   ├── Sidebar
│   │   ├── LayersList
│   │   └── QuickActions
│   └── TopBar
│       ├── Search
│       └── UserMenu
├── Page (Route-specific)
│   ├── PageHeader
│   ├── PageContent
│   │   ├── Feature Modules
│   │   │   ├── Smart Components (connected to data)
│   │   │   └── Presentation Components (pure UI)
│   │   └── Widgets
│   └── PageFooter
└── Modals/Dialogs
```

#### Component Types

1. **Layout Components**: Define page structure
2. **Feature Components**: Smart components with business logic
3. **UI Components**: Reusable, generic components (buttons, inputs)
4. **Widget Components**: Self-contained feature blocks

### State Management Strategy

#### Local State (useState, useReducer)
- Component-specific UI state
- Form state (React Hook Form)
- Temporary state (modals, toggles)

#### Global State (Zustand)
- User preferences (theme, sidebar collapsed)
- UI state (active layer, current view)
- Non-persistent app state

#### Server State (TanStack Query)
- All data from backend APIs
- Automatic caching, refetching, and synchronization
- Optimistic updates for better UX

```typescript
// Example: Server state with TanStack Query
const { data: tasks, isLoading } = useQuery({
  queryKey: ['tasks', layerId],
  queryFn: () => fetchTasks(layerId),
  staleTime: 5 * 60 * 1000, // 5 minutes
})

// Mutation with optimistic update
const { mutate: updateTask } = useMutation({
  mutationFn: updateTaskAPI,
  onMutate: async (newTask) => {
    // Optimistically update UI
    await queryClient.cancelQueries(['tasks'])
    const previous = queryClient.getQueryData(['tasks'])
    queryClient.setQueryData(['tasks'], (old) => 
      old.map(t => t.id === newTask.id ? newTask : t)
    )
    return { previous }
  },
  onError: (err, variables, context) => {
    // Rollback on error
    queryClient.setQueryData(['tasks'], context.previous)
  },
})
```

### Routing Strategy

**File-based Routing (Next.js App Router)**

```
app/
├── (auth)/
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
├── (dashboard)/
│   ├── layout.tsx (shared layout)
│   ├── page.tsx (dashboard home)
│   ├── inputs/
│   │   └── page.tsx
│   ├── layers/
│   │   ├── [layerId]/
│   │   │   ├── page.tsx
│   │   │   ├── tasks/
│   │   │   ├── strategy/
│   │   │   └── projects/
│   │   │       └── [projectId]/
│   │   │           └── page.tsx
│   └── life-cycle/
│       └── page.tsx
└── api/
    ├── auth/
    ├── tasks/
    ├── layers/
    └── projects/
```

### Performance Optimizations

1. **Code Splitting**: Automatic with Next.js dynamic imports
2. **Image Optimization**: Next.js Image component
3. **Lazy Loading**: React.lazy for heavy components
4. **Memoization**: React.memo, useMemo, useCallback
5. **Virtual Scrolling**: For large lists (react-virtual)
6. **Debouncing**: For search, autosave
7. **Prefetching**: Next.js Link prefetching

---

## ⚙️ Backend Architecture

### API Structure

**RESTful API Design**

```
Base URL: /api/v1

Auth:
POST   /auth/register
POST   /auth/login
POST   /auth/logout
GET    /auth/session
POST   /auth/reset-password

Users:
GET    /users/me
PATCH  /users/me
DELETE /users/me

Inputs:
GET    /inputs
POST   /inputs
GET    /inputs/:id
PATCH  /inputs/:id
DELETE /inputs/:id
POST   /inputs/:id/move (move to layer/project)

Layers:
GET    /layers
POST   /layers
GET    /layers/:id
PATCH  /layers/:id
DELETE /layers/:id
PATCH  /layers/reorder

Tasks:
GET    /tasks (query: layerId, projectId, status)
POST   /tasks
GET    /tasks/:id
PATCH  /tasks/:id
DELETE /tasks/:id
PATCH  /tasks/:id/move (between Kanban columns)
POST   /tasks/:id/complete (with notes)

Projects:
GET    /projects (query: layerId)
POST   /projects
GET    /projects/:id
PATCH  /projects/:id
DELETE /projects/:id

Strategy Documents:
GET    /strategies/:type/:id (type: layer/project)
PATCH  /strategies/:type/:id
GET    /strategies/:type/:id/versions

Habits:
GET    /habits
POST   /habits
PATCH  /habits/:id
DELETE /habits/:id
POST   /habits/:id/log (daily tracking)

Life Cycle:
GET    /life-cycle/:date
PATCH  /life-cycle/:date

Outputs:
GET    /outputs (query: layerId, projectId, dateRange)
GET    /outputs/stats

Archives:
GET    /archives
POST   /archives (archive item)
GET    /archives/:id
DELETE /archives/:id (permanent delete)
```

### Middleware Stack

**Request Processing Pipeline:**

```typescript
Request
  → CORS Middleware
  → Rate Limiting
  → Request Logging
  → Authentication (JWT validation)
  → Authorization (permissions check)
  → Input Validation (Zod schemas)
  → Route Handler (business logic)
  → Response Formatting
  → Error Handling
Response
```

### Error Handling

**Standardized Error Response:**

```typescript
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ],
    "timestamp": "2025-10-08T12:34:56Z",
    "requestId": "req_abc123"
  }
}
```

**Error Types:**
- `AUTHENTICATION_ERROR` (401)
- `AUTHORIZATION_ERROR` (403)
- `VALIDATION_ERROR` (400)
- `NOT_FOUND` (404)
- `CONFLICT` (409)
- `RATE_LIMIT_EXCEEDED` (429)
- `INTERNAL_ERROR` (500)

### Background Jobs

**Job Processing Strategy:**

Using Vercel Cron Jobs for scheduled tasks:

```typescript
// api/cron/daily-backup.ts
export async function GET(request: Request) {
  // Verify cron secret
  if (request.headers.get('authorization') !== `Bearer ${process.env.CRON_SECRET}`) {
    return new Response('Unauthorized', { status: 401 })
  }
  
  await performDailyBackup()
  return Response.json({ success: true })
}
```

**Scheduled Jobs:**
- Daily backups (3 AM UTC)
- Habit streak calculations (midnight per user timezone)
- Reminder notifications (user-scheduled times)
- Analytics aggregation (hourly)

---

## 🗄️ Database Design

### Database Technology: PostgreSQL (via Supabase)

**Why PostgreSQL?**
- ACID compliance for data integrity
- Rich feature set (JSON, arrays, full-text search)
- Excellent performance for complex queries
- Row Level Security (RLS) for data isolation
- Mature ecosystem and tooling

### ORM: Prisma

**Why Prisma?**
- Type-safe database access
- Automatic migrations
- Excellent TypeScript integration
- Query optimization
- Schema visualization

### Schema Overview

**Core Entities:**

1. **users** - User accounts
2. **layers** - Life management layers (Layer 0, 1, 2...)
3. **projects** - Projects within layers
4. **inputs** - Captured ideas/thoughts
5. **tasks** - Action items (Kanban cards)
6. **strategy_documents** - Strategic planning docs
7. **habits** - Daily habits and routines
8. **habit_logs** - Habit tracking entries
9. **life_cycle_blocks** - Time blocks in daily schedule
10. **outputs** - Completed tasks with reflections
11. **archives** - Archived items

See `03_Database_Schema_Design.md` for detailed schema.

### Data Relationships

```
User (1) ──→ (N) Layers
          └─→ (N) Inputs
          └─→ (N) Outputs

Layer (1) ──→ (N) Projects
           └─→ (N) Tasks
           └─→ (1) Strategy Document
           └─→ (N) Habits

Project (1) ──→ (N) Tasks
            └─→ (1) Strategy Document
            └─→ (N) Custom Modules

Task (N) ──→ (1) Layer OR Project
         └─→ (1) User (creator)

Habit (1) ──→ (N) Habit Logs
```

### Database Performance Strategies

1. **Indexing**:
   - Primary keys (automatic)
   - Foreign keys
   - Frequently queried fields (user_id, layer_id, status, created_at)
   - Composite indexes for common query patterns

2. **Partitioning** (future, for scale):
   - Partition `outputs` and `habit_logs` by date (monthly)

3. **Caching**:
   - Redis for frequently accessed data
   - User session cache
   - Dashboard stats cache

4. **Query Optimization**:
   - Use Prisma includes carefully
   - Pagination for large datasets
   - Eager loading vs lazy loading

---

## 🔌 API Design

### API Design Principles

1. **RESTful**: Follow REST conventions
2. **Versioned**: `/api/v1` for future-proofing
3. **Consistent**: Predictable patterns across endpoints
4. **Documented**: OpenAPI/Swagger documentation
5. **Secure**: Authentication and authorization on all endpoints
6. **Validated**: Input validation with Zod schemas
7. **Paginated**: Large datasets use cursor pagination

### Request/Response Format

**Request:**
```typescript
// POST /api/v1/tasks
{
  "title": "Complete PRD",
  "description": "Finish product requirements document",
  "priority": "high",
  "status": "in_progress",
  "layerId": "layer_123",
  "dueDate": "2025-10-15T12:00:00Z",
  "tags": ["documentation", "planning"]
}
```

**Success Response:**
```typescript
// 201 Created
{
  "data": {
    "id": "task_456",
    "title": "Complete PRD",
    "description": "Finish product requirements document",
    "priority": "high",
    "status": "in_progress",
    "layerId": "layer_123",
    "projectId": null,
    "dueDate": "2025-10-15T12:00:00Z",
    "tags": ["documentation", "planning"],
    "createdAt": "2025-10-08T10:30:00Z",
    "updatedAt": "2025-10-08T10:30:00Z",
    "userId": "user_789"
  },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": "2025-10-08T10:30:00Z"
  }
}
```

**Error Response:**
```typescript
// 400 Bad Request
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid task data",
    "details": [
      {
        "field": "priority",
        "message": "Priority must be one of: low, medium, high"
      }
    ]
  },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": "2025-10-08T10:30:00Z"
  }
}
```

### Pagination

**Cursor-based Pagination** (better for large datasets):

```typescript
// GET /api/v1/tasks?cursor=task_100&limit=20

{
  "data": [...],
  "pagination": {
    "nextCursor": "task_120",
    "hasMore": true,
    "total": 500
  }
}
```

### Rate Limiting

**Strategy**: Token bucket algorithm

- **Authenticated users**: 1000 requests/hour
- **Free tier**: 100 requests/hour
- **Premium tier**: 5000 requests/hour
- **Burst allowance**: 50 requests/minute

**Headers:**
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 945
X-RateLimit-Reset: 1696780800
```

---

## 🔒 Security Architecture

### Authentication

**Strategy: NextAuth.js + Supabase Auth**

**Supported Methods:**
1. Email/Password (with email verification)
2. Google OAuth
3. Apple Sign-In

**Flow:**
```
1. User submits credentials
2. NextAuth validates credentials (or OAuth flow)
3. Session created with JWT token
4. JWT stored in httpOnly cookie
5. Subsequent requests validated via middleware
```

**Session Structure:**
```typescript
{
  user: {
    id: "user_123",
    email: "user@example.com",
    name: "John Doe",
    image: "https://...",
    role: "user" | "premium" | "admin"
  },
  accessToken: "jwt_token",
  expires: "2025-10-15T12:00:00Z"
}
```

### Authorization

**Strategy: Row Level Security (RLS) + Middleware**

**Levels:**
1. **Route Protection**: Middleware checks authentication
2. **Resource Ownership**: Users can only access their own data
3. **Role-Based**: Admin features gated by role

**Supabase RLS Policies:**
```sql
-- Users can only read their own layers
CREATE POLICY "Users can view own layers"
ON layers FOR SELECT
USING (auth.uid() = user_id);

-- Users can only update their own tasks
CREATE POLICY "Users can update own tasks"
ON tasks FOR UPDATE
USING (auth.uid() = user_id);
```

### Data Security

1. **Encryption in Transit**: TLS 1.3
2. **Encryption at Rest**: AES-256 (Supabase default)
3. **Password Hashing**: bcrypt (10 rounds)
4. **JWT Signing**: HS256 with secret rotation
5. **API Keys**: Hashed and stored securely

### Input Validation

**All user inputs validated with Zod:**

```typescript
const CreateTaskSchema = z.object({
  title: z.string().min(1).max(200),
  description: z.string().max(5000).optional(),
  priority: z.enum(['low', 'medium', 'high']),
  layerId: z.string().uuid(),
  dueDate: z.string().datetime().optional(),
})
```

### CORS Configuration

```typescript
const corsOptions = {
  origin: [
    'https://traderdevlabs.com',
    'https://lifeos.traderdevlabs.com',
    process.env.NODE_ENV === 'development' && 'http://localhost:3000'
  ].filter(Boolean),
  credentials: true,
  methods: ['GET', 'POST', 'PATCH', 'DELETE'],
}
```

### Security Headers

```typescript
// Next.js config
headers: [
  {
    key: 'X-Frame-Options',
    value: 'DENY',
  },
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff',
  },
  {
    key: 'Referrer-Policy',
    value: 'strict-origin-when-cross-origin',
  },
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=()',
  },
]
```

---

## ⚡ Performance & Scalability

### Performance Targets

- **Time to First Byte (TTFB)**: < 200ms
- **First Contentful Paint (FCP)**: < 1s
- **Largest Contentful Paint (LCP)**: < 2s
- **Time to Interactive (TTI)**: < 3s
- **API Response Time**: < 500ms (p95)

### Frontend Performance

1. **Server-Side Rendering (SSR)**: Initial page load
2. **Static Generation**: Where possible
3. **Incremental Static Regeneration (ISR)**: For semi-dynamic content
4. **Image Optimization**: Next.js Image component
5. **Font Optimization**: Next.js Font optimization
6. **Bundle Optimization**: 
   - Tree shaking
   - Code splitting
   - Dynamic imports for heavy components

### Backend Performance

1. **Database Query Optimization**:
   - Proper indexing
   - Query result caching (Redis)
   - Connection pooling
   - Prepared statements (Prisma)

2. **API Caching**:
   - Redis for hot data (session, dashboard stats)
   - CDN for static assets
   - HTTP caching headers

3. **Horizontal Scaling**:
   - Stateless API design
   - Multiple Vercel instances (automatic)
   - Database read replicas (Supabase Pro)

### Caching Strategy

**Multi-Level Caching:**

```
1. Browser Cache (static assets)
   └→ CDN Cache (Vercel Edge Network)
      └→ Server Cache (Redis)
         └→ Database (PostgreSQL)
```

**Cache Invalidation:**
- Time-based (TTL)
- Event-based (on mutations)
- Manual (admin panel)

---

## 🚀 Deployment Architecture

### Infrastructure

**Hosting: Vercel**
- Automatic deployments from Git
- Edge network (global CDN)
- Serverless functions
- Environment variables management
- Preview deployments for branches

**Database: Supabase Cloud**
- Managed PostgreSQL
- Automatic backups
- Connection pooling
- Database dashboard

### Environments

1. **Development**: Local development
   - `http://localhost:3000`
   - Local Supabase or dev project
   - Hot module reloading

2. **Staging**: Testing environment
   - `https://lifeos-staging.vercel.app`
   - Staging Supabase project
   - Mirror of production

3. **Production**: Live application
   - `https://traderdevlabs.com/lifeos`
   - Production Supabase project
   - Monitoring and alerts

### CI/CD Pipeline

**GitHub Actions Workflow:**

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - Checkout code
      - Setup Node.js
      - Install dependencies
      - Run linter (ESLint)
      - Run type check (TypeScript)
      - Run unit tests (Jest)
      - Run E2E tests (Playwright)
  
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - Build application
      - Upload artifacts
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - Deploy to Vercel
      - Run smoke tests
      - Notify team
```

### Database Migrations

**Strategy: Prisma Migrate**

```bash
# Development
npm run prisma:migrate:dev

# Production
npm run prisma:migrate:deploy
```

**Migration Workflow:**
1. Create migration in development
2. Test migration on staging database
3. Review migration SQL
4. Apply to production (via CI/CD)
5. Rollback plan documented

---

## 🛠️ Development Workflow

### Local Development Setup

```bash
# 1. Clone repository
git clone https://github.com/tahirgenc41-sudo/lifeos.git
cd lifeos

# 2. Install dependencies
npm install

# 3. Setup environment variables
cp .env.example .env.local
# Edit .env.local with your Supabase credentials

# 4. Setup database
npm run prisma:migrate:dev
npm run prisma:seed

# 5. Start development server
npm run dev
```

### Git Workflow

**Branch Strategy: Git Flow**

```
main (production)
  ├── develop (staging)
      ├── feature/user-auth
      ├── feature/kanban-board
      ├── bugfix/login-error
      └── hotfix/critical-bug
```

**Commit Convention:**
```
<type>(<scope>): <subject>

Types: feat, fix, docs, style, refactor, test, chore
Examples:
  feat(auth): add Google OAuth login
  fix(tasks): resolve drag-and-drop bug
  docs(readme): update setup instructions
```

### Code Review Process

1. Create feature branch
2. Develop and test locally
3. Create pull request to `develop`
4. Automated checks run (CI)
5. Code review by peer
6. Address review comments
7. Merge to `develop`
8. Deploy to staging
9. QA testing
10. Merge to `main` (production)

---

## 📊 Monitoring & Observability

### Error Tracking: Sentry

```typescript
import * as Sentry from "@sentry/nextjs"

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1, // 10% of transactions
})
```

**Tracked Metrics:**
- JavaScript errors
- API errors
- Performance issues
- User sessions

### Performance Monitoring: Vercel Analytics

- Real User Monitoring (RUM)
- Core Web Vitals
- API endpoint performance
- Geographic distribution

### Application Metrics

**Key Metrics:**
- Active users (DAU, WAU, MAU)
- Task completion rate
- Feature usage
- API response times
- Error rates
- Database query performance

### Logging

**Structured Logging:**

```typescript
logger.info('Task created', {
  userId: user.id,
  taskId: task.id,
  layerId: task.layerId,
  timestamp: new Date().toISOString(),
})
```

**Log Levels:**
- ERROR: System errors, exceptions
- WARN: Potential issues
- INFO: Important events
- DEBUG: Detailed debugging info

---

## 🔄 Disaster Recovery

### Backup Strategy

**Database Backups:**
- Automatic daily backups (Supabase)
- Point-in-time recovery (7 days retention)
- Manual backup before major migrations

**Application Code:**
- Version control (Git)
- Tagged releases
- Docker images (future)

### Recovery Procedures

**Database Recovery:**
1. Identify issue and impact
2. Stop writes to database
3. Restore from latest backup
4. Validate data integrity
5. Resume normal operations
6. Post-mortem analysis

**Application Recovery:**
1. Identify failed deployment
2. Rollback to previous version (Vercel instant)
3. Investigate root cause
4. Fix and redeploy

---

## 📚 Appendix

### Technology Decision Records (TDRs)

**TDR-001: Why Next.js over separate React + Express?**
- Decision: Next.js full-stack framework
- Rationale: 
  - Single codebase for frontend and backend
  - Excellent performance (SSR, ISR)
  - Built-in API routes
  - Strong TypeScript support
  - Large ecosystem
  - Vercel deployment optimization

**TDR-002: Why Supabase over self-hosted PostgreSQL?**
- Decision: Supabase managed service
- Rationale:
  - Managed infrastructure (no DevOps overhead)
  - Built-in authentication
  - Row Level Security
  - Real-time subscriptions
  - Object storage
  - Cost-effective for MVP
  - Easy migration path to self-hosted if needed

**TDR-003: Why Prisma over raw SQL?**
- Decision: Prisma ORM
- Rationale:
  - Type safety
  - Automatic migrations
  - Better developer experience
  - Query optimization
  - Protection against SQL injection
  - Schema visualization

---

## ✅ Review & Approval

**Prepared by:** Droid (Technical Architect AI)  
**Reviewed by:** Tahir Genctog  
**Approved by:** [Pending]

**Approval Date:** [Pending]

---

**Document Control:**
- Version 1.0: Initial Technical Architecture (October 8, 2025)
- Next Review: After MVP implementation
