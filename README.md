# LifeOS - Integrated Life Management Platform

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Next.js](https://img.shields.io/badge/Next.js-14-black)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue)](https://www.typescriptlang.org/)
[![Supabase](https://img.shields.io/badge/Supabase-Latest-green)](https://supabase.com/)

LifeOS is a comprehensive life management platform that helps users organize and manage all aspects of their daily lives. From note-taking to habit tracking, from project management to financial planning, LifeOS provides an integrated solution for personal productivity and life organization.

## 🌟 Vision

To create a unified platform that consolidates personal information management, productivity tools, health tracking, and life planning into a single, secure, and user-friendly application.

## 📋 Features

### MVP Phase 1 (Weeks 1-8)
- **Authentication & User Management**: Secure user registration and login
- **Note-Taking System**: Rich text editor with tags and categories
- **Task Management**: Create, organize, and track tasks with priorities
- **Basic Settings**: Profile management and preferences

### MVP Phase 2 (Weeks 9-16)
- **Habit Tracking**: Daily habit tracking with streaks and statistics
- **Journal System**: Daily journaling with templates and mood tracking
- **Dashboard**: Comprehensive overview with widgets
- **Search & Filter**: Advanced search across all content

### MVP Phase 3 (Weeks 17-24)
- **Project Management**: Multi-level project organization
- **Goals & Objectives**: Goal setting with progress tracking
- **Financial Tracking**: Budget management and expense tracking
- **Data Import/Export**: Backup and migration tools

## 🏗️ Tech Stack

- **Frontend**: Next.js 14 with App Router, React 18, TypeScript
- **Styling**: Tailwind CSS with shadcn/ui components
- **Backend**: Next.js API Routes, Supabase
- **Database**: PostgreSQL (via Supabase)
- **ORM**: Prisma
- **Authentication**: Supabase Auth
- **Deployment**: Vercel

## 📚 Documentation

Comprehensive technical documentation is available in the `/docs` directory:

1. **[Product Requirements Document](docs/01_PRD_Product_Requirements_Document.md)** - Complete product vision, features, and roadmap
2. **[Technical Architecture](docs/02_Technical_Architecture_Document.md)** - System architecture and technical decisions
3. **[Database Schema](docs/03_Database_Schema_Design.md)** - Complete database design with relationships
4. **[API Specification](docs/04_API_Endpoints_Specification.md)** - REST API endpoints documentation
5. **[Design System Guide](docs/05_Design_System_Guide.md)** - UI/UX design system and components
6. **[Project Structure](docs/06_Project_Structure.md)** - Folder structure and organization

## 🚀 Getting Started

### Prerequisites

- Node.js 18.17 or later
- npm, yarn, or pnpm
- Supabase account (for database and authentication)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/tahirgenc41-sudo/lifeos.git
   cd lifeos
   ```

2. **Install dependencies**
   ```bash
   npm install
   # or
   yarn install
   # or
   pnpm install
   ```

3. **Environment Setup**
   
   Create a `.env.local` file in the root directory:
   ```env
   NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
   SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
   DATABASE_URL=your_database_url
   NEXTAUTH_SECRET=your_nextauth_secret
   NEXTAUTH_URL=http://localhost:3000
   ```

4. **Database Setup**
   
   Run Prisma migrations:
   ```bash
   npx prisma generate
   npx prisma db push
   ```

5. **Run the development server**
   ```bash
   npm run dev
   # or
   yarn dev
   # or
   pnpm dev
   ```

6. **Open your browser**
   
   Navigate to [http://localhost:3000](http://localhost:3000)

## 📁 Project Structure

```
lifeos/
├── app/                    # Next.js App Router
│   ├── (auth)/            # Authentication pages
│   ├── (dashboard)/       # Main application pages
│   ├── api/               # API routes
│   └── layout.tsx         # Root layout
├── components/            # React components
│   ├── ui/               # shadcn/ui components
│   ├── features/         # Feature-specific components
│   └── shared/           # Shared components
├── lib/                   # Utility functions and configurations
│   ├── db/               # Database utilities
│   ├── utils/            # Helper functions
│   └── hooks/            # Custom React hooks
├── prisma/               # Prisma schema and migrations
├── public/               # Static assets
├── docs/                 # Technical documentation
└── types/                # TypeScript type definitions
```

## 🧪 Development Guidelines

### Code Style

- TypeScript strict mode enabled
- ESLint for code quality
- Prettier for code formatting
- Follow Next.js and React best practices

### Git Workflow

1. Create feature branches from `main`
2. Use conventional commit messages
3. Submit pull requests for review
4. Ensure all tests pass before merging

### Testing

```bash
# Run unit tests
npm test

# Run e2e tests
npm run test:e2e

# Run type checking
npm run type-check

# Run linting
npm run lint
```

## 🔒 Security

- All API routes protected with authentication
- Row Level Security (RLS) enabled in Supabase
- Environment variables for sensitive data
- HTTPS required in production
- Regular security audits and updates

## 🌍 Internationalization

- Multi-language support planned
- Initially supporting English and Turkish
- Using i18n for translations

## 📈 Performance

- Server-side rendering (SSR) for optimal performance
- Image optimization with Next.js Image component
- Code splitting and lazy loading
- API response caching
- Database query optimization

## 🤝 Contributing

Contributions are welcome! Please read our contributing guidelines before submitting pull requests.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👥 Team

- **Product Owner**: Tahir Genctog
- **Development**: In Progress
- **Documentation**: Complete

## 📞 Contact

- GitHub: [@tahirgenc41-sudo](https://github.com/tahirgenc41-sudo)
- Project Link: [https://github.com/tahirgenc41-sudo/lifeos](https://github.com/tahirgenc41-sudo/lifeos)

## 🗺️ Roadmap

- [x] Complete technical documentation
- [ ] MVP Phase 1: Core functionality (Weeks 1-8)
- [ ] MVP Phase 2: Enhanced features (Weeks 9-16)
- [ ] MVP Phase 3: Advanced features (Weeks 17-24)
- [ ] Public beta release
- [ ] Mobile application (React Native)
- [ ] Desktop application (Electron)

## 🙏 Acknowledgments

- Next.js team for the amazing framework
- Vercel for hosting and deployment
- Supabase for backend infrastructure
- shadcn/ui for beautiful UI components
- The open-source community

---

**Made with ❤️ for better life organization**