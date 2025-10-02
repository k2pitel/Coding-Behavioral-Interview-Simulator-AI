# MVP Architecture

## System Overview

The AI Interview Simulator platform is built on a modern microservices architecture designed for scalability, reliability, and performance. The system separates concerns into distinct services that communicate through well-defined APIs.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Client Layer                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   Web App    │  │  Mobile App  │  │  Admin Panel │          │
│  │ (React/TS)   │  │(React Native)│  │   (React)    │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                              │
                         HTTPS/WSS
                              │
┌─────────────────────────────────────────────────────────────────┐
│                      API Gateway Layer                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  NGINX / API Gateway                                     │   │
│  │  - Load Balancing                                        │   │
│  │  - Rate Limiting                                         │   │
│  │  - SSL Termination                                       │   │
│  │  - Request Routing                                       │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼────────┐   ┌────────▼──────┐   ┌─────────▼────────┐
│   Backend API  │   │  AI Service   │   │  Code Executor   │
│   (Node.js)    │   │   (Python)    │   │   (Docker)       │
│                │   │               │   │                  │
│  - GraphQL     │   │  - GPT-4 API  │   │  - Sandboxed     │
│  - REST API    │   │  - NLP Models │   │  - Multi-lang    │
│  - WebSockets  │   │  - Analysis   │   │  - Secure        │
│  - Auth        │   │  - Feedback   │   │                  │
└────────────────┘   └───────────────┘   └──────────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────────┐
│                        Data Layer                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │  PostgreSQL  │  │    Redis     │  │   AWS S3     │          │
│  │   (Primary)  │  │   (Cache)    │  │  (Storage)   │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

## Core Components

### 1. Frontend Application (React + TypeScript)

**Purpose**: User interface for all platform interactions

**Key Features**:
- Responsive design for all device sizes
- Real-time code editing with Monaco Editor
- Interview session management
- Progress visualization with charts
- WebSocket integration for live feedback

**Technology**:
- React 18 with TypeScript
- TailwindCSS for styling
- Monaco Editor for code editing
- Socket.io-client for WebSockets
- Recharts/Victory for data visualization
- React Router for navigation
- Zustand/Redux for state management

**Components Structure**:
```
src/
├── components/
│   ├── coding/
│   │   ├── CodeEditor.tsx
│   │   ├── ProblemStatement.tsx
│   │   ├── TestCases.tsx
│   │   └── AIFeedback.tsx
│   ├── behavioral/
│   │   ├── QuestionCard.tsx
│   │   ├── ResponseRecorder.tsx
│   │   ├── STARAnalysis.tsx
│   │   └── FeedbackPanel.tsx
│   ├── dashboard/
│   │   ├── ProgressChart.tsx
│   │   ├── StatsCard.tsx
│   │   └── RecentActivity.tsx
│   └── shared/
│       ├── Header.tsx
│       ├── Sidebar.tsx
│       └── Modal.tsx
├── pages/
│   ├── Dashboard.tsx
│   ├── CodingInterview.tsx
│   ├── BehavioralInterview.tsx
│   ├── Profile.tsx
│   └── Settings.tsx
├── hooks/
├── services/
├── utils/
└── types/
```

### 2. Backend API Service (Node.js + Express + GraphQL)

**Purpose**: Core application logic, authentication, and data management

**Key Responsibilities**:
- User authentication and authorization (JWT)
- Session management
- Data validation and sanitization
- Business logic orchestration
- Integration with AI services
- Real-time communication via WebSockets

**Technology**:
- Node.js v18+
- Express.js
- Apollo Server (GraphQL)
- Prisma ORM
- Socket.io for WebSockets
- JWT for authentication
- bcrypt for password hashing

**API Structure**:
```
src/
├── graphql/
│   ├── schema/
│   │   ├── user.graphql
│   │   ├── interview.graphql
│   │   ├── problem.graphql
│   │   └── analytics.graphql
│   ├── resolvers/
│   │   ├── user.resolver.ts
│   │   ├── interview.resolver.ts
│   │   ├── problem.resolver.ts
│   │   └── analytics.resolver.ts
│   └── directives/
│       └── auth.directive.ts
├── rest/
│   ├── routes/
│   │   ├── auth.routes.ts
│   │   ├── webhook.routes.ts
│   │   └── health.routes.ts
│   └── controllers/
├── services/
│   ├── auth.service.ts
│   ├── user.service.ts
│   ├── interview.service.ts
│   ├── problem.service.ts
│   └── analytics.service.ts
├── middleware/
│   ├── auth.middleware.ts
│   ├── rateLimit.middleware.ts
│   ├── validation.middleware.ts
│   └── error.middleware.ts
├── models/
├── utils/
└── config/
```

**GraphQL Schema Example**:
```graphql
type User {
  id: ID!
  email: String!
  name: String!
  subscription: SubscriptionTier!
  stats: UserStats!
  sessions: [InterviewSession!]!
}

type InterviewSession {
  id: ID!
  type: SessionType!
  status: SessionStatus!
  startedAt: DateTime!
  completedAt: DateTime
  score: Float
  feedback: Feedback
}

type Query {
  me: User!
  getSession(id: ID!): InterviewSession
  getProblem(id: ID!): CodingProblem
  getAnalytics(timeRange: TimeRange!): Analytics
}

type Mutation {
  startSession(type: SessionType!): InterviewSession!
  submitCode(sessionId: ID!, code: String!): CodeSubmission!
  submitBehavioralResponse(sessionId: ID!, response: String!): BehavioralSubmission!
}

type Subscription {
  feedbackUpdate(sessionId: ID!): Feedback!
}
```

### 3. AI Service (Python + FastAPI)

**Purpose**: AI-powered analysis and feedback generation

**Key Responsibilities**:
- Code analysis and optimization suggestions
- Behavioral response evaluation
- STAR method structure analysis
- Sentiment and tone analysis
- Personalized feedback generation

**Technology**:
- Python 3.10+
- FastAPI framework
- OpenAI GPT-4 API
- Custom NLP models (spaCy, transformers)
- AST parsing for code analysis
- Sentiment analysis libraries

**Service Structure**:
```
ai_service/
├── api/
│   ├── routes/
│   │   ├── code_analysis.py
│   │   ├── behavioral_analysis.py
│   │   └── feedback.py
│   └── dependencies.py
├── services/
│   ├── code_analyzer.py
│   ├── behavioral_analyzer.py
│   ├── star_detector.py
│   ├── sentiment_analyzer.py
│   └── feedback_generator.py
├── models/
│   ├── custom_nlp/
│   └── schemas.py
├── utils/
│   ├── ast_parser.py
│   ├── complexity_calculator.py
│   └── openai_client.py
└── config/
```

**Key AI Capabilities**:

1. **Code Analysis**:
   - Time/space complexity detection
   - Code style and best practices
   - Optimization opportunities
   - Bug detection
   - Test case generation

2. **Behavioral Analysis**:
   - STAR method component identification
   - Communication clarity scoring
   - Tone and sentiment analysis
   - Key point extraction
   - Improvement suggestions

### 4. Code Execution Service (Docker)

**Purpose**: Secure and isolated code execution

**Key Features**:
- Sandboxed execution environment
- Multi-language support
- Resource limits (CPU, memory, time)
- Security isolation
- Test case validation

**Technology**:
- Docker containers
- Language-specific images
- Resource constraints
- Timeout mechanisms

**Supported Languages (MVP)**:
- Python 3.10
- JavaScript (Node.js)
- Java 11

**Execution Flow**:
```
1. Receive code submission
2. Validate input
3. Create isolated Docker container
4. Copy code and test cases
5. Execute with resource limits
6. Capture output and errors
7. Destroy container
8. Return results
```

**Security Measures**:
- Network isolation
- File system restrictions
- Resource limits (1 CPU, 512MB RAM, 10s timeout)
- No external access
- Read-only file system
- Restricted system calls

### 5. Database Layer

#### PostgreSQL (Primary Database)

**Schema Design**:

```sql
-- Users table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255) NOT NULL,
  subscription_tier VARCHAR(50) DEFAULT 'free',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Interview Sessions
CREATE TABLE interview_sessions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  session_type VARCHAR(50) NOT NULL, -- 'coding' or 'behavioral'
  status VARCHAR(50) NOT NULL, -- 'active', 'completed', 'abandoned'
  interviewer_persona VARCHAR(100),
  started_at TIMESTAMP DEFAULT NOW(),
  completed_at TIMESTAMP,
  score DECIMAL(5,2),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Coding Problems
CREATE TABLE coding_problems (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title VARCHAR(255) NOT NULL,
  description TEXT NOT NULL,
  difficulty VARCHAR(50) NOT NULL, -- 'easy', 'medium', 'hard'
  category VARCHAR(100),
  starter_code JSONB, -- {language: code}
  test_cases JSONB,
  optimal_solution JSONB,
  time_complexity VARCHAR(50),
  space_complexity VARCHAR(50),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Code Submissions
CREATE TABLE code_submissions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  session_id UUID REFERENCES interview_sessions(id),
  problem_id UUID REFERENCES coding_problems(id),
  code TEXT NOT NULL,
  language VARCHAR(50) NOT NULL,
  status VARCHAR(50), -- 'pending', 'passed', 'failed'
  execution_time_ms INTEGER,
  memory_used_kb INTEGER,
  test_results JSONB,
  ai_feedback JSONB,
  submitted_at TIMESTAMP DEFAULT NOW()
);

-- Behavioral Questions
CREATE TABLE behavioral_questions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  question TEXT NOT NULL,
  category VARCHAR(100), -- 'leadership', 'teamwork', 'conflict', etc.
  difficulty VARCHAR(50),
  ideal_components JSONB, -- STAR components to look for
  created_at TIMESTAMP DEFAULT NOW()
);

-- Behavioral Responses
CREATE TABLE behavioral_responses (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  session_id UUID REFERENCES interview_sessions(id),
  question_id UUID REFERENCES behavioral_questions(id),
  response TEXT NOT NULL,
  response_type VARCHAR(50), -- 'text' or 'voice'
  star_analysis JSONB,
  sentiment_score DECIMAL(3,2),
  clarity_score DECIMAL(3,2),
  ai_feedback JSONB,
  submitted_at TIMESTAMP DEFAULT NOW()
);

-- User Analytics
CREATE TABLE user_analytics (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  metric_name VARCHAR(100) NOT NULL,
  metric_value DECIMAL(10,2),
  metadata JSONB,
  recorded_at TIMESTAMP DEFAULT NOW()
);

-- Indexes for performance
CREATE INDEX idx_sessions_user ON interview_sessions(user_id);
CREATE INDEX idx_submissions_session ON code_submissions(session_id);
CREATE INDEX idx_responses_session ON behavioral_responses(session_id);
CREATE INDEX idx_analytics_user_date ON user_analytics(user_id, recorded_at);
```

#### Redis (Cache & Sessions)

**Use Cases**:
- User session storage
- Rate limiting counters
- API response caching
- Real-time leaderboards
- Temporary code execution results

**Key Patterns**:
```
# Session storage
session:{user_id} -> {session_data}
TTL: 24 hours

# Rate limiting
ratelimit:{user_id}:{endpoint} -> counter
TTL: 1 hour

# Cached responses
cache:problem:{problem_id} -> {problem_data}
TTL: 1 hour

# Active sessions
active:session:{session_id} -> {session_state}
TTL: 2 hours
```

## Communication Patterns

### 1. Synchronous Communication (REST/GraphQL)
- User authentication
- Data queries
- CRUD operations
- Analytics retrieval

### 2. Asynchronous Communication (WebSockets)
- Real-time AI feedback during coding
- Live interview session updates
- Instant notifications
- Progress updates

### 3. Event-Driven Architecture
- Code submission → AI analysis
- Session completion → Analytics update
- User signup → Welcome email
- Subscription change → Feature access update

## Security Architecture

### Authentication & Authorization
- JWT-based authentication
- Role-based access control (RBAC)
- OAuth2 integration (Google, GitHub)
- Multi-factor authentication (Phase 2)

### Data Security
- Encryption at rest (PostgreSQL)
- Encryption in transit (TLS/SSL)
- Password hashing (bcrypt)
- API key management
- Input validation and sanitization

### Infrastructure Security
- Container isolation for code execution
- Network segmentation
- DDoS protection
- Rate limiting
- Security headers (CORS, CSP, etc.)

## Scalability Considerations

### Horizontal Scaling
- Stateless API servers
- Load balancing across multiple instances
- Database read replicas
- Redis clustering for cache

### Vertical Scaling
- Database connection pooling
- Efficient query optimization
- Caching strategies
- CDN for static assets

### Performance Optimization
- Lazy loading of components
- Code splitting
- Database indexing
- Query optimization
- Response compression
- Asset minification

## Monitoring & Observability

### Metrics Collection
- Application performance metrics
- User behavior analytics
- Error tracking and logging
- Resource utilization

### Tools
- CloudWatch/Datadog for monitoring
- Sentry for error tracking
- LogRocket for session replay
- Google Analytics for user analytics

### Alerts
- Service downtime
- High error rates
- Resource exhaustion
- Security incidents

## Deployment Architecture

### Environment Strategy
- Development (local Docker Compose)
- Staging (AWS ECS)
- Production (AWS ECS with auto-scaling)

### CI/CD Pipeline
```
Code Push → GitHub Actions →
  ├── Lint & Format
  ├── Unit Tests
  ├── Integration Tests
  ├── Build Docker Images
  ├── Push to ECR
  └── Deploy to ECS
```

### Infrastructure as Code
- Terraform for AWS resources
- Docker Compose for local development
- Kubernetes manifests (Phase 3)

## Disaster Recovery

### Backup Strategy
- Daily automated PostgreSQL backups
- Point-in-time recovery capability
- S3 backup retention (30 days)
- Redis persistence (AOF + RDB)

### High Availability
- Multi-AZ database deployment
- Load balancer health checks
- Auto-scaling policies
- Failover mechanisms

## Future Architecture Enhancements (Phase 2-3)

1. **Microservices Decomposition**
   - Separate coding and behavioral services
   - Dedicated analytics service
   - User management service

2. **Event Streaming**
   - Apache Kafka for event bus
   - Real-time analytics pipeline
   - Event sourcing patterns

3. **Machine Learning Pipeline**
   - MLOps infrastructure
   - Model training and versioning
   - A/B testing framework

4. **Global Distribution**
   - Multi-region deployment
   - Edge caching with CloudFront
   - Geo-based routing

5. **Mobile Backend**
   - GraphQL BFF (Backend for Frontend)
   - Push notification service
   - Offline-first architecture
