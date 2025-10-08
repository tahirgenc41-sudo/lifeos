# Design System Guide
# LifeOS - Life Management Operating System

**Version:** 1.0  
**Last Updated:** October 8, 2025  
**Status:** Draft

---

## 📋 Table of Contents

1. [Design Philosophy](#design-philosophy)
2. [Color System](#color-system)
3. [Typography](#typography)
4. [Spacing & Layout](#spacing--layout)
5. [Components](#components)
6. [Icons](#icons)
7. [Animations](#animations)
8. [Accessibility](#accessibility)

---

## 🎨 Design Philosophy

### Core Principles

1. **Clarity First**: Every interface element should have a clear purpose
2. **Consistent**: Predictable patterns across the entire application
3. **Accessible**: WCAG 2.1 AA compliance minimum
4. **Performance**: Fast, responsive, optimized for all devices
5. **Beautiful**: Aesthetic excellence without compromising functionality

### Design Language

**LifeOS** adopts a modern, clean, and professional aesthetic that conveys:
- **Trust**: Reliable system for important life management
- **Focus**: Distraction-free interface for productivity
- **Energy**: Vibrant colors that motivate action
- **Calm**: Balanced design that doesn't overwhelm

---

## 🎨 Color System

### Primary Colors

Used for primary actions, navigation, and branding.

| Color | Hex | Usage |
|-------|-----|-------|
| **Primary Blue** | `#2563EB` | Primary buttons, links, Layer 0 |
| **Primary Blue Hover** | `#1D4ED8` | Hover state |
| **Primary Blue Light** | `#DBEAFE` | Backgrounds, badges |
| **Primary Blue Dark** | `#1E40AF` | Active state |

**CSS Variables:**
```css
--color-primary: #2563EB;
--color-primary-hover: #1D4ED8;
--color-primary-light: #DBEAFE;
--color-primary-dark: #1E40AF;
```

---

### Secondary Colors

Used for secondary actions and layer differentiation.

| Color | Hex | Usage |
|-------|-----|-------|
| **Green** | `#10B981` | Success, growth, Layer 1 (Health) |
| **Purple** | `#8B5CF6` | Projects, creativity |
| **Orange** | `#F59E0B` | Warning, attention, Layer 2 (Finance) |
| **Pink** | `#EC4899` | Relationships, Layer 3 |
| **Indigo** | `#6366F1` | Learning, Layer 4+ |
| **Teal** | `#14B8A6` | Habits, routines |

---

### Neutral Colors

Base colors for text, backgrounds, and borders.

| Color | Hex | Usage |
|-------|-----|-------|
| **Gray 50** | `#F9FAFB` | Page background |
| **Gray 100** | `#F3F4F6` | Card background |
| **Gray 200** | `#E5E7EB` | Borders, dividers |
| **Gray 300** | `#D1D5DB` | Disabled states |
| **Gray 400** | `#9CA3AF` | Placeholder text |
| **Gray 500** | `#6B7280` | Secondary text |
| **Gray 600** | `#4B5563` | Body text |
| **Gray 700** | `#374151` | Headings |
| **Gray 800** | `#1F2937` | Strong emphasis |
| **Gray 900** | `#111827` | Black text |

---

### Semantic Colors

Convey meaning and status.

| Color | Hex | Usage |
|-------|-----|-------|
| **Success** | `#10B981` | Success messages, completed tasks |
| **Warning** | `#F59E0B` | Warnings, due soon |
| **Error** | `#EF4444` | Errors, overdue, destructive actions |
| **Info** | `#3B82F6` | Information, tips |

---

### Dark Mode

**Background Colors:**
- **BG Primary**: `#0F172A` (slate-900)
- **BG Secondary**: `#1E293B` (slate-800)
- **BG Tertiary**: `#334155` (slate-700)

**Text Colors:**
- **Text Primary**: `#F1F5F9` (slate-100)
- **Text Secondary**: `#CBD5E1` (slate-300)
- **Text Tertiary**: `#94A3B8` (slate-400)

---

## ✍️ Typography

### Font Family

**Primary Font: Inter**
- Excellent readability
- Wide character set
- Variable font support
- Open source

**Monospace Font: JetBrains Mono**
- Code snippets
- Data display (dates, numbers)

**Import:**
```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
```

---

### Type Scale

| Element | Size | Weight | Line Height | Usage |
|---------|------|--------|-------------|-------|
| **Heading 1** | 36px / 2.25rem | 700 | 1.2 | Page titles |
| **Heading 2** | 30px / 1.875rem | 600 | 1.3 | Section titles |
| **Heading 3** | 24px / 1.5rem | 600 | 1.4 | Subsection titles |
| **Heading 4** | 20px / 1.25rem | 600 | 1.4 | Card titles |
| **Body Large** | 18px / 1.125rem | 400 | 1.6 | Important body text |
| **Body** | 16px / 1rem | 400 | 1.5 | Default body text |
| **Body Small** | 14px / 0.875rem | 400 | 1.5 | Secondary text |
| **Caption** | 12px / 0.75rem | 400 | 1.4 | Labels, metadata |

**CSS Classes:**
```css
.text-h1 { font-size: 2.25rem; font-weight: 700; line-height: 1.2; }
.text-h2 { font-size: 1.875rem; font-weight: 600; line-height: 1.3; }
.text-h3 { font-size: 1.5rem; font-weight: 600; line-height: 1.4; }
.text-h4 { font-size: 1.25rem; font-weight: 600; line-height: 1.4; }
.text-body-lg { font-size: 1.125rem; font-weight: 400; line-height: 1.6; }
.text-body { font-size: 1rem; font-weight: 400; line-height: 1.5; }
.text-body-sm { font-size: 0.875rem; font-weight: 400; line-height: 1.5; }
.text-caption { font-size: 0.75rem; font-weight: 400; line-height: 1.4; }
```

---

## 📏 Spacing & Layout

### Spacing Scale

Based on 4px unit (0.25rem).

| Token | Value | Usage |
|-------|-------|-------|
| `space-0` | 0px | No spacing |
| `space-1` | 4px | Tight spacing |
| `space-2` | 8px | Small spacing |
| `space-3` | 12px | Medium-small spacing |
| `space-4` | 16px | Default spacing |
| `space-5` | 20px | Medium spacing |
| `space-6` | 24px | Medium-large spacing |
| `space-8` | 32px | Large spacing |
| `space-10` | 40px | Extra large spacing |
| `space-12` | 48px | Section spacing |
| `space-16` | 64px | Page section spacing |

**Tailwind Utility Classes:**
```
p-1, p-2, p-3, p-4, p-6, p-8, p-10, p-12, p-16
m-1, m-2, m-3, m-4, m-6, m-8, m-10, m-12, m-16
gap-1, gap-2, gap-3, gap-4, gap-6, gap-8
```

---

### Grid System

**Container Widths:**
- **Mobile**: 100% (max 640px)
- **Tablet**: 100% (max 768px)
- **Desktop**: 100% (max 1280px)
- **Wide**: 100% (max 1536px)

**Grid Columns:**
- 12-column grid system
- Responsive breakpoints: `sm`, `md`, `lg`, `xl`, `2xl`

---

### Breakpoints

| Breakpoint | Value | Usage |
|------------|-------|-------|
| `sm` | 640px | Small devices (mobile landscape) |
| `md` | 768px | Tablets |
| `lg` | 1024px | Laptops |
| `xl` | 1280px | Desktops |
| `2xl` | 1536px | Large desktops |

---

## 🧱 Components

### Buttons

#### Primary Button

**Purpose:** Main call-to-action

**Style:**
```css
background: #2563EB;
color: white;
padding: 12px 24px;
border-radius: 6px;
font-weight: 600;
box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);

hover: background: #1D4ED8;
active: background: #1E40AF;
disabled: background: #D1D5DB; cursor: not-allowed;
```

**Component:**
```jsx
<button className="btn btn-primary">
  Save Changes
</button>
```

---

#### Secondary Button

**Purpose:** Secondary actions

**Style:**
```css
background: white;
color: #374151;
border: 1px solid #E5E7EB;
padding: 12px 24px;
border-radius: 6px;
font-weight: 600;

hover: background: #F9FAFB;
active: background: #F3F4F6;
```

---

#### Destructive Button

**Purpose:** Dangerous actions (delete, remove)

**Style:**
```css
background: #EF4444;
color: white;
padding: 12px 24px;
border-radius: 6px;
font-weight: 600;

hover: background: #DC2626;
```

---

### Input Fields

#### Text Input

**Style:**
```css
background: white;
border: 1px solid #E5E7EB;
border-radius: 6px;
padding: 10px 14px;
font-size: 16px;

focus: border-color: #2563EB; outline: 2px solid #DBEAFE;
error: border-color: #EF4444;
disabled: background: #F9FAFB; cursor: not-allowed;
```

**Component:**
```jsx
<input 
  type="text"
  className="input"
  placeholder="Enter task title..."
/>
```

---

### Cards

**Purpose:** Container for related content

**Style:**
```css
background: white;
border: 1px solid #E5E7EB;
border-radius: 8px;
padding: 24px;
box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);

hover: box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
```

**Component:**
```jsx
<div className="card">
  <h3>Card Title</h3>
  <p>Card content...</p>
</div>
```

---

### Badges

**Purpose:** Status indicators, tags

**Variants:**

| Variant | Background | Text | Usage |
|---------|------------|------|-------|
| **Primary** | `#DBEAFE` | `#1E40AF` | Default badge |
| **Success** | `#D1FAE5` | `#065F46` | Completed, success |
| **Warning** | `#FEF3C7` | `#92400E` | Due soon, warning |
| **Error** | `#FEE2E2` | `#991B1B` | Overdue, error |
| **Gray** | `#F3F4F6` | `#374151` | Neutral |

**Style:**
```css
padding: 4px 12px;
border-radius: 9999px;
font-size: 12px;
font-weight: 600;
```

---

### Modal/Dialog

**Purpose:** Focused task completion, confirmations

**Style:**
```css
background: white;
border-radius: 12px;
max-width: 500px;
padding: 24px;
box-shadow: 0 20px 25px rgba(0, 0, 0, 0.15);

backdrop: background: rgba(0, 0, 0, 0.5); backdrop-filter: blur(4px);
```

---

### Kanban Card

**Purpose:** Task card in Kanban board

**Style:**
```css
background: white;
border: 1px solid #E5E7EB;
border-radius: 6px;
padding: 16px;
cursor: grab;

hover: box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
dragging: opacity: 0.5; cursor: grabbing;
```

**Visual Hierarchy:**
- Priority indicator (colored left border)
- Title (bold, 16px)
- Description (gray, 14px, truncated)
- Metadata (due date, tags, 12px)

---

## 🎭 Icons

### Icon Library: Lucide React

**Why Lucide:**
- Beautiful, consistent design
- Extensive library (1000+ icons)
- Tree-shakeable
- Excellent React support

**Installation:**
```bash
npm install lucide-react
```

**Usage:**
```jsx
import { Check, X, Plus, Trash2 } from 'lucide-react'

<Check className="w-5 h-5 text-green-600" />
```

---

### Icon Sizes

| Size | Value | Usage |
|------|-------|-------|
| **XS** | 12px | Dense UI, inline with text |
| **SM** | 16px | Form inputs, buttons |
| **MD** | 20px | Default size |
| **LG** | 24px | Section headers |
| **XL** | 32px | Empty states, illustrations |

---

### Common Icons

| Icon | Name | Usage |
|------|------|-------|
| ✅ | `Check` | Completion, success |
| ❌ | `X` | Close, cancel, error |
| ➕ | `Plus` | Add, create new |
| 🗑️ | `Trash2` | Delete, remove |
| ✏️ | `Edit` | Edit, modify |
| 👁️ | `Eye` | View, show |
| 🔍 | `Search` | Search functionality |
| ⚙️ | `Settings` | Settings, configuration |
| 👤 | `User` | User profile, Layer 0 |
| ❤️ | `Heart` | Health, favorites |
| 💰 | `DollarSign` | Finance, money |
| 🎯 | `Target` | Goals, objectives |
| 📅 | `Calendar` | Dates, scheduling |
| 🕐 | `Clock` | Time, duration |

---

## 🎬 Animations

### Animation Principles

1. **Purposeful**: Animations guide attention and provide feedback
2. **Fast**: Duration 150-300ms for most interactions
3. **Smooth**: Easing functions for natural motion
4. **Subtle**: Don't distract from content

---

### Transition Durations

| Duration | Value | Usage |
|----------|-------|-------|
| **Fast** | 150ms | Hover, focus states |
| **Normal** | 200ms | Default transitions |
| **Slow** | 300ms | Modal open/close |
| **Slower** | 500ms | Page transitions |

---

### Easing Functions

```css
--ease-in: cubic-bezier(0.4, 0, 1, 1);
--ease-out: cubic-bezier(0, 0, 0.2, 1);
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
```

---

### Common Animations

**Fade In:**
```css
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
```

**Slide Up:**
```css
@keyframes slideUp {
  from { transform: translateY(10px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}
```

**Scale:**
```css
transition: transform 200ms ease-out;
hover: transform: scale(1.02);
```

---

## ♿ Accessibility

### WCAG 2.1 AA Compliance

**Requirements:**
1. **Color Contrast**: Minimum 4.5:1 for text, 3:1 for large text
2. **Keyboard Navigation**: All interactive elements accessible via keyboard
3. **Screen Readers**: Proper ARIA labels and semantic HTML
4. **Focus Indicators**: Visible focus states (outline or ring)
5. **Alternative Text**: Images have descriptive alt text

---

### Focus States

**Default Focus:**
```css
focus: outline: 2px solid #2563EB; outline-offset: 2px;
```

**Focus Ring (Tailwind):**
```jsx
<button className="focus:ring-2 focus:ring-blue-600 focus:ring-offset-2">
  Button
</button>
```

---

### Color Contrast

**Text Colors (Light Mode):**
- Body text: `#374151` on `#FFFFFF` → 10.3:1 ✅
- Secondary text: `#6B7280` on `#FFFFFF` → 5.8:1 ✅
- Primary button: `#FFFFFF` on `#2563EB` → 8.6:1 ✅

**Text Colors (Dark Mode):**
- Body text: `#F1F5F9` on `#0F172A` → 13.2:1 ✅
- Secondary text: `#CBD5E1` on `#0F172A` → 9.7:1 ✅

---

### Keyboard Navigation

**Tab Order:**
1. Logo/Home
2. Main navigation
3. Primary content
4. Secondary content
5. Footer

**Shortcuts:**
- `Cmd/Ctrl + K`: Global search
- `Cmd/Ctrl + N`: New task
- `Esc`: Close modal
- `Tab`: Navigate forward
- `Shift + Tab`: Navigate backward

---

### Screen Reader Support

**Semantic HTML:**
```html
<header>
  <nav aria-label="Main navigation">
    <ul>
      <li><a href="/">Home</a></li>
    </ul>
  </nav>
</header>

<main>
  <h1>Page Title</h1>
  <article>Content</article>
</main>
```

**ARIA Labels:**
```jsx
<button aria-label="Delete task">
  <Trash2 />
</button>

<input 
  type="text"
  aria-describedby="email-help"
  aria-invalid="true"
/>
<span id="email-help">Enter your email address</span>
```

---

## 📦 Component Library

### Implementation: shadcn/ui

**Why shadcn/ui:**
- Copy-paste components (you own the code)
- Built on Radix UI (accessible primitives)
- Tailwind CSS styling
- Fully customizable

**Installation:**
```bash
npx shadcn-ui@latest init
```

**Add Components:**
```bash
npx shadcn-ui@latest add button
npx shadcn-ui@latest add input
npx shadcn-ui@latest add dialog
npx shadcn-ui@latest add card
```

---

## 🎨 Design Tokens (CSS Variables)

```css
:root {
  /* Colors */
  --color-primary: #2563EB;
  --color-success: #10B981;
  --color-warning: #F59E0B;
  --color-error: #EF4444;
  
  /* Spacing */
  --space-1: 4px;
  --space-2: 8px;
  --space-4: 16px;
  --space-6: 24px;
  --space-8: 32px;
  
  /* Border Radius */
  --radius-sm: 4px;
  --radius-md: 6px;
  --radius-lg: 8px;
  --radius-xl: 12px;
  --radius-full: 9999px;
  
  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
}
```

---

## ✅ Design Checklist

Before implementing a new feature:

- [ ] Uses design system colors
- [ ] Follows spacing scale
- [ ] Typography matches type scale
- [ ] Responsive on mobile, tablet, desktop
- [ ] Dark mode support
- [ ] Meets WCAG AA contrast ratios
- [ ] Keyboard accessible
- [ ] Screen reader tested
- [ ] Focus states visible
- [ ] Loading states defined
- [ ] Error states defined
- [ ] Empty states designed

---

**Document Control:**
- Version 1.0: Initial Design System Guide (October 8, 2025)
- Next Review: After MVP design implementation
