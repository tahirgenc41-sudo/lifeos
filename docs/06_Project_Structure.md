# Project Structure
# LifeOS - Life Management Operating System

**Version:** 1.0  
**Last Updated:** October 8, 2025  
**Status:** Draft

---

## 📁 Directory Structure

```
lifeos/
├── .github/                      # GitHub configuration
│   └── workflows/                # GitHub Actions CI/CD
│       ├── ci.yml               # Continuous Integration
│       └── deploy.yml           # Deployment workflow
│
├── docs/                         # Project documentation
│   ├── 01_PRD_Product_Requirements_Document.md
│   ├── 02_Technical_Architecture_Document.md
│   ├── 03_Database_Schema_Design.md
│   ├── 04_API_Endpoints_Specification.md
│   ├── 05_Design_System_Guide.md
│   └── 06_Project_Structure.md
│
├── prisma/                       # Database schema and migrations
│   ├── schema.prisma            # Prisma schema definition
│   ├── migrations/              # Database migrations
│   └── seed.ts                  # Seed data
│
├── public/                       # Static assets
│   ├── fonts/                   # Custom fonts
│   ├── images/                  # Images
│   └── icons/                   # Icons, favicons
│
├── src/                          # Source code
│   ├── app/                     # Next.js app directory
│   │   ├── (auth)/              # Authentication routes (grouped)
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   └── layout.tsx       # Auth layout
│   │   │
│   │   ├── (dashboard)/         # Dashboard routes (grouped)
│   │   │   ├── layout.tsx       # Dashboard layout
│   │   │   ├── page.tsx         # Dashboard home
│   │   │   ├── inputs/
│   │   │   │   └── page.tsx
│   │   │   ├── layers/
│   │   │   │   ├── page.tsx
│   │   │   │   └── [layerId]/
│   │   │   │       ├── page.tsx
│   │   │   │       ├── tasks/
│   │   │   │       ├── strategy/
│   │   │   │       └── projects/
│   │   │   │           └── [projectId]/
│   │   │   │               └── page.tsx
│   │   │   ├── habits/
│   │   │   │   └── page.tsx
│   │   │   ├── life-cycle/
│   │   │   │   └── page.tsx
│   │   │   └── outputs/
│   │   │       └── page.tsx
│   │   │
│   │   ├── api/                 # API routes
│   │   │   ├── auth/
│   │   │   │   ├── register/
│   │   │   │   ├── login/
│   │   │   │   ├── logout/
│   │   │   │   └── session/
│   │   │   ├── users/
│   │   │   │   └── me/
│   │   │   ├── inputs/
│   │   │   │   ├── route.ts     # GET, POST /inputs
│   │   │   │   └── [id]/
│   │   │   │       ├── route.ts # GET, PATCH, DELETE
│   │   │   │       └── move/
│   │   │   ├── layers/
│   │   │   │   ├── route.ts
│   │   │   │   ├── [id]/
│   │   │   │   └── reorder/
│   │   │   ├── projects/
│   │   │   ├── tasks/
│   │   │   ├── strategies/
│   │   │   ├── habits/
│   │   │   ├── life-cycle/
│   │   │   ├── outputs/
│   │   │   └── archives/
│   │   │
│   │   ├── globals.css          # Global styles
│   │   ├── layout.tsx           # Root layout
│   │   └── page.tsx             # Landing page
│   │
│   ├── components/              # React components
│   │   ├── ui/                  # UI primitives (shadcn/ui)
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── card.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── badge.tsx
│   │   │   └── ...
│   │   │
│   │   ├── layout/              # Layout components
│   │   │   ├── Sidebar.tsx
│   │   │   ├── TopBar.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── DashboardLayout.tsx
│   │   │
│   │   ├── auth/                # Authentication components
│   │   │   ├── LoginForm.tsx
│   │   │   ├── RegisterForm.tsx
│   │   │   └── ProtectedRoute.tsx
│   │   │
│   │   ├── inputs/              # Input-related components
│   │   │   ├── InputList.tsx
│   │   │   ├── InputCard.tsx
│   │   │   ├── CreateInputModal.tsx
│   │   │   └── MoveInputDialog.tsx
│   │   │
│   │   ├── layers/              # Layer components
│   │   │   ├── LayerList.tsx
│   │   │   ├── LayerCard.tsx
│   │   │   ├── CreateLayerModal.tsx
│   │   │   └── ReorderLayers.tsx
│   │   │
│   │   ├── tasks/               # Task components
│   │   │   ├── KanbanBoard.tsx
│   │   │   ├── KanbanColumn.tsx
│   │   │   ├── TaskCard.tsx
│   │   │   ├── CreateTaskModal.tsx
│   │   │   ├── TaskDetailModal.tsx
│   │   │   └── CompleteTaskDialog.tsx
│   │   │
│   │   ├── projects/            # Project components
│   │   │   ├── ProjectList.tsx
│   │   │   ├── ProjectCard.tsx
│   │   │   ├── CreateProjectModal.tsx
│   │   │   └── ProjectProgress.tsx
│   │   │
│   │   ├── habits/              # Habit components
│   │   │   ├── HabitList.tsx
│   │   │   ├── HabitCard.tsx
│   │   │   ├── HabitTracker.tsx
│   │   │   ├── HabitStreakDisplay.tsx
│   │   │   └── CreateHabitModal.tsx
│   │   │
│   │   ├── life-cycle/          # Life cycle components
│   │   │   ├── LifeCycleTimeline.tsx
│   │   │   ├── TimeBlock.tsx
│   │   │   ├── TimeBlockEditor.tsx
│   │   │   └── DayNavigator.tsx
│   │   │
│   │   ├── dashboard/           # Dashboard components
│   │   │   ├── StatsCard.tsx
│   │   │   ├── QuickActions.tsx
│   │   │   ├── RecentTasks.tsx
│   │   │   └── ProgressCharts.tsx
│   │   │
│   │   └── shared/              # Shared/common components
│   │       ├── LoadingSpinner.tsx
│   │       ├── ErrorBoundary.tsx
│   │       ├── EmptyState.tsx
│   │       ├── ConfirmDialog.tsx
│   │       └── Pagination.tsx
│   │
│   ├── features/                # Feature modules (alternative organization)
│   │   ├── auth/
│   │   │   ├── components/
│   │   │   ├── hooks/
│   │   │   ├── api/
│   │   │   └── types/
│   │   ├── inputs/
│   │   ├── layers/
│   │   ├── tasks/
│   │   ├── projects/
│   │   ├── habits/
│   │   └── life-cycle/
│   │
│   ├── lib/                     # Utility libraries
│   │   ├── prisma.ts           # Prisma client instance
│   │   ├── supabase.ts         # Supabase client
│   │   ├── auth.ts             # NextAuth configuration
│   │   ├── utils.ts            # Utility functions
│   │   ├── constants.ts        # App constants
│   │   └── validators.ts       # Zod schemas
│   │
│   ├── hooks/                   # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useUser.ts
│   │   ├── useInputs.ts
│   │   ├── useLayers.ts
│   │   ├── useTasks.ts
│   │   ├── useProjects.ts
│   │   ├── useHabits.ts
│   │   └── useDebounce.ts
│   │
│   ├── store/                   # State management (Zustand)
│   │   ├── authStore.ts
│   │   ├── uiStore.ts
│   │   └── preferencesStore.ts
│   │
│   ├── services/                # Business logic services
│   │   ├── inputService.ts
│   │   ├── layerService.ts
│   │   ├── taskService.ts
│   │   ├── projectService.ts
│   │   ├── habitService.ts
│   │   └── lifeCycleService.ts
│   │
│   ├── repositories/            # Data access layer
│   │   ├── inputRepository.ts
│   │   ├── layerRepository.ts
│   │   ├── taskRepository.ts
│   │   ├── projectRepository.ts
│   │   └── habitRepository.ts
│   │
│   ├── middleware/              # API middleware
│   │   ├── auth.ts             # Authentication middleware
│   │   ├── rateLimit.ts        # Rate limiting
│   │   ├── cors.ts             # CORS configuration
│   │   ├── errorHandler.ts     # Error handling
│   │   └── logger.ts           # Request logging
│   │
│   ├── types/                   # TypeScript type definitions
│   │   ├── auth.ts
│   │   ├── user.ts
│   │   ├── input.ts
│   │   ├── layer.ts
│   │   ├── task.ts
│   │   ├── project.ts
│   │   ├── habit.ts
│   │   ├── api.ts
│   │   └── index.ts            # Re-exports
│   │
│   └── utils/                   # Utility functions
│       ├── date.ts             # Date manipulation
│       ├── format.ts           # Formatting helpers
│       ├── validation.ts       # Validation helpers
│       └── color.ts            # Color utilities
│
├── tests/                        # Test files
│   ├── unit/                    # Unit tests
│   │   ├── services/
│   │   ├── repositories/
│   │   └── utils/
│   ├── integration/             # Integration tests
│   │   └── api/
│   └── e2e/                     # End-to-end tests (Playwright)
│       ├── auth.spec.ts
│       ├── tasks.spec.ts
│       └── habits.spec.ts
│
├── .env.example                 # Environment variables template
├── .env.local                   # Local environment variables (gitignored)
├── .eslintrc.json              # ESLint configuration
├── .gitignore                   # Git ignore rules
├── .prettierrc                  # Prettier configuration
├── next.config.js              # Next.js configuration
├── package.json                # Dependencies and scripts
├── tsconfig.json               # TypeScript configuration
├── tailwind.config.ts          # Tailwind CSS configuration
├── postcss.config.js           # PostCSS configuration
├── components.json             # shadcn/ui configuration
└── README.md                    # Project documentation
```

---

## 🗂️ Folder Explanations

### `/src/app` - Next.js App Router

**Purpose:** Page routes and API endpoints

**Conventions:**
- `(auth)`, `(dashboard)`: Route groups (don't affect URL)
- `[id]`: Dynamic route segments
- `layout.tsx`: Shared layout for route group
- `page.tsx`: Page component
- `loading.tsx`: Loading UI
- `error.tsx`: Error UI

---

### `/src/components` - React Components

**Organization Strategy:** By feature/domain

**Types:**
1. **ui/**: Base UI components (buttons, inputs)
2. **layout/**: Layout components (sidebar, header)
3. **[feature]/**: Feature-specific components

**Component Naming:**
- PascalCase: `TaskCard.tsx`
- Props interface: `TaskCardProps`
- Default export: `export default TaskCard`

---

### `/src/lib` - Core Libraries

**Purpose:** Shared utilities, configurations, clients

**Files:**
- `prisma.ts`: Singleton Prisma client
- `supabase.ts`: Supabase client configuration
- `auth.ts`: NextAuth.js configuration
- `utils.ts`: Common utility functions
- `validators.ts`: Zod validation schemas

---

### `/src/services` - Business Logic

**Purpose:** Business logic separated from API routes

**Pattern:**
```typescript
// taskService.ts
export class TaskService {
  constructor(private taskRepo: TaskRepository) {}
  
  async createTask(userId: string, data: CreateTaskDTO) {
    // Validation, business rules
    const task = await this.taskRepo.create({ ...data, userId })
    // Side effects (notifications, etc.)
    return task
  }
}
```

---

### `/src/repositories` - Data Access

**Purpose:** Database queries abstracted from business logic

**Pattern:**
```typescript
// taskRepository.ts
export class TaskRepository {
  async findById(id: string): Promise<Task | null> {
    return prisma.task.findUnique({ where: { id } })
  }
  
  async findByUserId(userId: string): Promise<Task[]> {
    return prisma.task.findMany({ 
      where: { userId, deletedAt: null }
    })
  }
}
```

---

### `/src/hooks` - Custom React Hooks

**Purpose:** Reusable stateful logic

**Example:**
```typescript
// useTasks.ts
export function useTasks(layerId?: string) {
  const { data, isLoading, error } = useQuery({
    queryKey: ['tasks', layerId],
    queryFn: () => fetchTasks(layerId),
  })
  
  return { tasks: data, isLoading, error }
}
```

---

### `/src/store` - Global State

**Purpose:** Client-side global state (Zustand)

**Example:**
```typescript
// uiStore.ts
export const useUIStore = create<UIStore>((set) => ({
  sidebarCollapsed: false,
  toggleSidebar: () => set((state) => ({ 
    sidebarCollapsed: !state.sidebarCollapsed 
  })),
}))
```

---

### `/src/types` - Type Definitions

**Purpose:** Centralized TypeScript types

**Organization:**
```typescript
// task.ts
export interface Task {
  id: string
  title: string
  status: TaskStatus
  // ...
}

export type CreateTaskDTO = Omit<Task, 'id' | 'createdAt'>
export type UpdateTaskDTO = Partial<CreateTaskDTO>
```

---

### `/tests` - Test Files

**Organization:**
- `unit/`: Unit tests (services, utils)
- `integration/`: API integration tests
- `e2e/`: End-to-end UI tests (Playwright)

**Naming:**
- `*.test.ts`: Unit/integration tests (Jest)
- `*.spec.ts`: E2E tests (Playwright)

---

## 📝 File Naming Conventions

### Components
```
PascalCase.tsx
TaskCard.tsx
CreateTaskModal.tsx
```

### Utilities, Services, Hooks
```
camelCase.ts
taskService.ts
useAuth.ts
formatDate.ts
```

### API Routes
```
route.ts (Next.js convention)
```

### Types
```
camelCase.ts or PascalCase.ts
task.ts or Task.ts
```

---

## 🎯 Import Order

Standardized import order for clean code:

```typescript
// 1. React/Next.js
import React from 'react'
import { useRouter } from 'next/navigation'

// 2. Third-party libraries
import { useQuery } from '@tanstack/react-query'
import { z } from 'zod'

// 3. Internal components
import { Button } from '@/components/ui/button'
import { TaskCard } from '@/components/tasks/TaskCard'

// 4. Hooks
import { useTasks } from '@/hooks/useTasks'

// 5. Types
import type { Task } from '@/types/task'

// 6. Utils
import { formatDate } from '@/utils/date'

// 7. Styles (if any)
import styles from './Component.module.css'
```

---

## 🛠️ NPM Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "lint:fix": "next lint --fix",
    "type-check": "tsc --noEmit",
    "format": "prettier --write \"**/*.{ts,tsx,md,json}\"",
    "format:check": "prettier --check \"**/*.{ts,tsx,md,json}\"",
    
    "db:generate": "prisma generate",
    "db:push": "prisma db push",
    "db:migrate": "prisma migrate dev",
    "db:migrate:deploy": "prisma migrate deploy",
    "db:seed": "tsx prisma/seed.ts",
    "db:studio": "prisma studio",
    
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    
    "prepare": "husky install"
  }
}
```

---

## 🔧 Environment Variables

**File:** `.env.local`

```bash
# Database
DATABASE_URL="<your_postgresql_connection_string>"

# Supabase
NEXT_PUBLIC_SUPABASE_URL="<your_supabase_project_url>"
NEXT_PUBLIC_SUPABASE_ANON_KEY="<your_supabase_anon_key>"
SUPABASE_SERVICE_ROLE_KEY="<your_supabase_service_role_key>"

# NextAuth
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="<generate_random_string>"

# OAuth Providers
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"
APPLE_CLIENT_ID="your-apple-client-id"
APPLE_CLIENT_SECRET="your-apple-client-secret"

# Monitoring
SENTRY_DSN="your-sentry-dsn"
NEXT_PUBLIC_VERCEL_ANALYTICS_ID="your-analytics-id"

# Feature Flags
NEXT_PUBLIC_ENABLE_DARK_MODE="true"
NEXT_PUBLIC_ENABLE_ANALYTICS="true"
```

---

## 📦 Package.json Structure

**Key Dependencies:**

```json
{
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    
    "@prisma/client": "^5.0.0",
    "@supabase/supabase-js": "^2.38.0",
    "next-auth": "^4.24.0",
    
    "@tanstack/react-query": "^5.0.0",
    "zustand": "^4.4.0",
    
    "@radix-ui/react-dialog": "^1.0.0",
    "@radix-ui/react-dropdown-menu": "^2.0.0",
    "tailwindcss": "^3.3.0",
    
    "zod": "^3.22.0",
    "date-fns": "^3.0.0",
    "lucide-react": "^0.292.0"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "@types/react": "^18.2.0",
    "@types/node": "^20.0.0",
    
    "prisma": "^5.0.0",
    "eslint": "^8.0.0",
    "prettier": "^3.0.0",
    "husky": "^8.0.0",
    "lint-staged": "^15.0.0",
    
    "jest": "^29.7.0",
    "@testing-library/react": "^14.0.0",
    "playwright": "^1.40.0"
  }
}
```

---

## 🚀 Getting Started

```bash
# 1. Clone repository
git clone https://github.com/tahirgenc41-sudo/lifeos.git
cd lifeos

# 2. Install dependencies
npm install

# 3. Setup environment
cp .env.example .env.local
# Edit .env.local with your credentials

# 4. Setup database
npm run db:migrate
npm run db:seed

# 5. Start development server
npm run dev
```

Visit: `http://localhost:3000`

---

**Document Control:**
- Version 1.0: Initial Project Structure (October 8, 2025)
- Next Review: After project scaffolding
