# Perspective App - Detailed Implementation Plan

## Project Overview
**Project Name:** Perspective
**Description:** Mobile app that combats cognitive biases by measuring and enhancing cognitive flexibility through structured exercises and personalized feedback
**Timeline:** 3 months for MVP (Phase 1)
**Platform:** Mobile-first using React Native

## Technical Architecture

### Stack Selection
- **Frontend:** React Native for cross-platform mobile development
- **Backend:** Node.js with Express.js
- **Database:** MongoDB with Mongoose ODM
- **AI Services:** GPT-4o API for NLP scoring and scenario generation
- **Authentication:** Firebase Authentication
- **Analytics:** Firebase Analytics + Custom event tracking
- **Notifications:** Firebase Cloud Messaging
- **Payment Processing:** RevenueCat for subscription management

### System Architecture Diagram
```
┌─────────────────┐       ┌──────────────────────┐       ┌────────────────┐
│                 │       │                      │       │                │
│  Mobile Client  │◄─────►│ API/Backend Services │◄─────►│ MongoDB Atlas  │
│  (React Native) │       │     (Node.js)        │       │   Database     │
│                 │       │                      │       │                │
└─────────────────┘       └──────────────────────┘       └────────────────┘
        ▲                           ▲                            ▲
        │                           │                            │
        ▼                           ▼                            ▼
┌─────────────────┐       ┌──────────────────────┐       ┌────────────────┐
│                 │       │                      │       │                │
│    Firebase     │       │     GPT-4o API       │       │ Admins/Content │
│  (Auth, FCM,    │       │  (NLP & Scenarios)   │       │   Creators     │
│   Analytics)    │       │                      │       │                │
└─────────────────┘       └──────────────────────┘       └────────────────┘
```

## Data Models

### User Schema
```javascript
const UserSchema = new Schema({
  uid: { type: String, required: true, unique: true }, // Firebase UID
  email: { type: String, required: true, unique: true },
  displayName: { type: String },
  createdAt: { type: Date, default: Date.now },
  lastActive: { type: Date, default: Date.now },
  subscription: {
    status: { type: String, enum: ['free', 'trial', 'pro'], default: 'free' },
    startDate: { type: Date },
    endDate: { type: Date },
    platform: { type: String, enum: ['ios', 'android', 'web'] },
  },
  echoScore: {
    current: { type: Number, default: 0 }, // 0-100
    history: [{ score: Number, date: Date }],
    lastUpdated: { type: Date },
  },
  progress: {
    level: { type: Number, default: 1 },
    experience: { type: Number, default: 0 },
    streak: { type: Number, default: 0 },
    lastStreak: { type: Date },
    completedScenarios: [{ type: Schema.Types.ObjectId, ref: 'Scenario' }],
  },
  preferences: {
    topics: [{ type: String }], // politics, science, ethics, etc.
    difficulty: { type: String, enum: ['beginner', 'intermediate', 'advanced'], default: 'beginner' },
    notifications: { type: Boolean, default: true },
  },
  badges: [{ 
    id: { type: String }, 
    name: { type: String },
    earnedAt: { type: Date, default: Date.now }
  }],
});
```

### Scenario Schema
```javascript
const ScenarioSchema = new Schema({
  title: { type: String, required: true },
  description: { type: String, required: true },
  difficulty: { type: String, enum: ['beginner', 'intermediate', 'advanced'], required: true },
  topic: { type: String, required: true }, // politics, science, etc.
  viewpoints: [{
    perspective: { type: String, required: true }, // e.g., "position A", "position B"
    summary: { type: String, required: true },
    keyPoints: [{ type: String }],
    seedArguments: [{ type: String }], // Pre-seeded arguments for tiered approach
  }],
  guideQuestions: [{ type: String }], // To help users form their responses
  scoringRubric: {
    coherence: { type: Number, default: 25 }, // Weight percentages
    evidence: { type: Number, default: 25 },
    perspective: { type: Number, default: 25 },
    flexibility: { type: Number, default: 25 },
  },
  active: { type: Boolean, default: true },
  createdAt: { type: Date, default: Date.now },
});
```

### UserResponse Schema
```javascript
const UserResponseSchema = new Schema({
  userId: { type: Schema.Types.ObjectId, ref: 'User', required: true },
  scenarioId: { type: Schema.Types.ObjectId, ref: 'Scenario', required: true },
  responses: [{
    viewpoint: { type: String, required: true }, // "position A" or "position B"
    content: { type: String, required: true },
    createdAt: { type: Date, default: Date.now },
  }],
  scores: {
    coherence: { type: Number }, // 1-10
    evidence: { type: Number }, // 1-10
    perspective: { type: Number }, // 1-10
    flexibility: { type: Number }, // 1-10
    total: { type: Number }, // calculated from weighted scores
    feedback: { type: String }, // AI-generated feedback
  },
  completed: { type: Boolean, default: false },
  timeSpent: { type: Number }, // in seconds
  createdAt: { type: Date, default: Date.now },
});
```

## Development Phases and Timeline

### Phase 0: Project Setup (1 week)
- Set up development environment
- Initialize Git repository with branching strategy
- Configure CI/CD pipeline
- Set up project management tools
- Create initial project documentation

### Phase 1: Core Infrastructure (2 weeks)

#### Week 1: Backend Foundation
- Set up Node.js with Express backend
- Configure MongoDB connection
- Implement user authentication with Firebase
- Create basic API structure for core endpoints
- Set up testing framework

#### Week 2: Mobile App Foundation
- Initialize React Native project
- Configure navigation system
- Create common UI components
- Implement authentication flow
- Set up state management (Redux or Context API)

### Phase 2: MVP Features (6 weeks)

#### Week 3-4: Onboarding & Echo Score
- Develop onboarding screens and flow
- Create the Echo Score initial assessment quiz
- Implement quiz scoring algorithm
- Design and implement user profile screens

#### Week 5-6: Scenario Engine Foundation
- Create scenario presentation interface
- Develop the tiered engagement system (pre-seeded arguments)
- Set up GPT-4o API integration
- Build admin interface for scenario management

#### Week 7-8: Response Handling & Scoring
- Implement user response submission flow
- Create NLP scoring system using GPT-4o
- Build feedback generation system
- Implement progress tracking

#### Week 9: Motivation & Gamification
- Develop user level progression system
- Implement streak tracking
- Create achievements and badges system
- Design and implement notifications system

### Phase 3: Refinement & Enhancement (4 weeks)

#### Week 10: Subscription & Payment
- Implement RevenueCat integration
- Create subscription tiers
- Develop paywall screens
- Implement feature flagging based on subscription

#### Week 11: Content Expansion
- Create additional scenarios (reaching 50 total)
- Refine NLP scoring system
- Implement content recommendation algorithm
- Add topic filtering and preferences

#### Week 12: Testing & Optimization
- Conduct thorough testing (unit, integration, UI)
- Implement analytics tracking
- Optimize performance
- Fix bugs and refine UX

#### Week 13: Deployment Preparation
- Prepare app for App Store and Google Play submission
- Create marketing assets
- Implement TestFlight distribution
- Finalize documentation

## Feature Implementation Details

### 1. Echo Score System

#### Initial Assessment Quiz
- 10-15 questions assessing:
  - News consumption habits
  - Source diversity
  - Opinion diversity exposure
  - Self-awareness of biases
- Weighted scoring algorithm producing a 0-100 score

```javascript
// Example Echo Score calculation
function calculateInitialEchoScore(responses) {
  const weights = {
    sourceAwareness: 0.3,
    exposureDiversity: 0.3,
    viewpointEngagement: 0.2,
    biasAwareness: 0.2
  };
  
  let score = 0;
  
  // Calculate source awareness component
  const sourceScore = calculateSourceScore(responses.sourceQuestions);
  score += sourceScore * weights.sourceAwareness;
  
  // Calculate exposure diversity component
  const exposureScore = calculateExposureScore(responses.exposureQuestions);
  score += exposureScore * weights.exposureDiversity;
  
  // Calculate other components...
  
  return Math.round(score);
}
```

#### Progress Tracking
- Weekly re-assessment option
- Historical data visualization
- Improvement tips based on weakest areas

### 2. Forked Scenarios Engine

#### Scenario Presentation
- Two-phase approach:
  1. Present scenario and context
  2. Request user response for each viewpoint

#### Tiered Engagement Levels
1. **Level 1 (Beginner)**: Arrange pre-seeded arguments in order of strength
2. **Level 2 (Intermediate)**: Select pre-seeded arguments and expand on them
3. **Level 3 (Advanced)**: Write custom responses from scratch

#### NLP Scoring Implementation
```javascript
async function scoreUserResponse(scenarioId, userId, responses) {
  // Get scenario details
  const scenario = await Scenario.findById(scenarioId);
  
  // Prepare prompt for GPT-4o
  const prompt = generateScoringPrompt(scenario, responses);
  
  // Call GPT-4o API
  const result = await callGPT4API(prompt);
  
  // Parse results
  const scores = parseNLPResults(result);
  
  // Calculate weighted total
  const totalScore = calculateWeightedScore(scores, scenario.scoringRubric);
  
  // Generate feedback
  const feedback = generateFeedback(scores, scenario);
  
  // Save results
  await saveUserScores(userId, scenarioId, scores, feedback);
  
  return { scores, feedback };
}
```

### 3. Motivation Loop

#### Streaks and Daily Challenges
- Push notification system for daily reminders
- Streak tracking with visual rewards
- "Don't break the chain" motivation

#### Level Progression System
```javascript
function calculateExperiencePoints(scores, timeSpent) {
  // Base XP for completion
  let xp = 100;
  
  // Bonus XP for high scores
  if (scores.total > 8) xp += 50;
  if (scores.total > 9) xp += 50;
  
  // Efficiency bonus (if completed quickly)
  if (timeSpent < 180) xp += 25; // Under 3 minutes
  
  // Streak bonus
  const streak = getUserStreak(userId);
  if (streak > 5) xp += 25;
  if (streak > 10) xp += 25;
  
  return xp;
}

function updateUserLevel(userId, xpGained) {
  const user = await User.findById(userId);
  user.progress.experience += xpGained;
  
  // Check for level up
  const newLevel = calculateLevelFromXP(user.progress.experience);
  if (newLevel > user.progress.level) {
    user.progress.level = newLevel;
    triggerLevelUpReward(userId, newLevel);
  }
  
  await user.save();
}
```

#### Achievements and Badges
- First completion
- 7-day streak
- 30-day streak
- Perfect score
- Topic mastery (complete all scenarios in a topic)
- Cognitive flexibility milestones

### 4. Subscription System

#### Tier Management
- Free: 3 scenarios/week, basic Echo Score
- Pro ($6.99/mo): Unlimited daily scenarios, full history, AI debate coach

#### Implementation with RevenueCat
```javascript
// Initialize RevenueCat
function initializeRevenueCat() {
  Purchases.setDebugLogsEnabled(true);
  Purchases.configure({
    apiKey: REVENUECAT_API_KEY,
    appUserID: userId // Optional
  });
}

// Purchase subscription
async function purchaseSubscription(packageId) {
  try {
    const { purchaserInfo } = await Purchases.purchasePackage(packageId);
    const customerInfo = await Purchases.getCustomerInfo();
    
    // Update user subscription status
    await updateUserSubscription(userId, customerInfo);
    
    return { success: true, info: customerInfo };
  } catch (error) {
    console.error('Purchase error:', error);
    return { success: false, error };
  }
}

// Check subscription status
async function checkSubscriptionStatus(userId) {
  try {
    const customerInfo = await Purchases.getCustomerInfo();
    const isPro = customerInfo.entitlements.active.pro !== undefined;
    
    // Update user subscription status in database
    await User.findByIdAndUpdate(userId, {
      'subscription.status': isPro ? 'pro' : 'free',
      'subscription.endDate': isPro ? new Date(customerInfo.entitlements.active.pro.expirationDate) : null
    });
    
    return { isPro };
  } catch (error) {
    console.error('Subscription check error:', error);
    return { isPro: false, error };
  }
}
```

## API Endpoints

### Authentication
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - Login user
- `GET /api/auth/me` - Get current user

### User
- `GET /api/users/profile` - Get user profile
- `PUT /api/users/profile` - Update user profile
- `GET /api/users/progress` - Get user progress
- `GET /api/users/echo-score` - Get user Echo Score
- `PUT /api/users/preferences` - Update user preferences

### Scenarios
- `GET /api/scenarios` - Get scenarios list (paginated)
- `GET /api/scenarios/:id` - Get scenario details
- `POST /api/scenarios/:id/responses` - Submit user responses
- `GET /api/scenarios/daily` - Get daily challenge
- `GET /api/scenarios/recommended` - Get recommended scenarios

### Subscriptions
- `GET /api/subscriptions/status` - Check subscription status
- `GET /api/subscriptions/offerings` - Get available subscriptions
- `POST /api/subscriptions/verify` - Verify subscription purchase

## Mobile App Screens

### Onboarding Flow
1. Welcome Screen
2. Echo Score Assessment (Quiz)
3. Results & Explanation
4. Goal Setting
5. Topic Selection

### Main Application Flow
1. Home Screen (Daily Challenge + Progress Overview)
2. Scenario Browser (Filterable List)
3. Scenario Details Screen
4. Viewpoint Response Screen
5. Results & Feedback Screen
6. Profile & Progress Screen
7. Echo Score Dashboard
8. Settings Screen
9. Subscription Screen

## Testing Strategy

### Unit Testing
- Backend: Jest for API endpoints, services, and utilities
- Frontend: Jest + React Testing Library for components and hooks

### Integration Testing
- API integration tests using Supertest
- Authentication flow testing
- Scenario submission and scoring flow

### User Testing
- Initial TestFlight beta with 50 users
- Usability testing for onboarding flow
- A/B testing for subscription models

## Deployment Strategy

### CI/CD Pipeline
- GitHub Actions for automated testing
- Automated deployments to testing environments
- Version management and release tagging

### App Store & Google Play
- Prepare screenshots and marketing materials
- Write compelling App Store descriptions
- Set up TestFlight for iOS beta testing
- Set up Google Play internal testing tracks

### Backend Deployment
- Deploy Node.js backend to AWS Elastic Beanstalk
- Set up MongoDB Atlas for database hosting
- Configure CloudWatch for monitoring
- Implement logging and error tracking

## Resource Requirements

### Development Team
- 1 Project Manager
- 2 React Native Developers
- 1 Backend Developer
- 1 UI/UX Designer
- 1 QA Engineer (part-time)

### External Services
- Firebase ($25/month - Spark plan)
- MongoDB Atlas ($57/month - M10 cluster)
- GPT-4o API (~$500/month estimated usage)
- RevenueCat ($199/month - Growth plan)
- AWS hosting ($100-200/month estimated)

## Risk Management

### Technical Risks
- **NLP Scoring Accuracy**: Implement human review of AI scoring samples
- **API Rate Limits**: Implement caching and queue system for GPT-4o requests
- **Performance Issues**: Regular performance testing, especially for mobile app

### Business Risks
- **Low Conversion Rate**: Test different pricing tiers and subscription offers
- **High Churn**: Implement engagement tracking and retention strategies
- **Content Scalability**: Develop semi-automated scenario generation system

## Post-MVP Roadmap (Phase 2 & 3)

### Phase 2 (Months 4-9)
- Browser extension for feed analysis
- Enhanced scoring algorithms
- Limited community features
- B2B dashboard prototype

### Phase 3 (Months 10-21)
- Full social features
- API for research partnerships
- Advanced AI coaching
- Content partnership integrations

## Success Metrics & Monitoring

### User Engagement
- DAU/MAU ratio target: 30%+
- Average session time target: 5+ minutes
- 7-day retention target: 30%+
- 30-day retention target: 15%+

### Business Performance
- Free-to-paid conversion target: 5-10%
- Customer acquisition cost (CAC) target: < $15
- Lifetime value (LTV) target: > $50

### Impact Measurement
- Average Echo Score improvement: 10+ points over 3 months
- Cognitive flexibility assessment improvement: 20%+
- User-reported perspective change: 50%+ positive responses

## Documentation

### Developer Documentation
- API documentation with Swagger
- Code style guide
- Component library documentation
- Test coverage requirements

### User Documentation
- In-app help center
- Onboarding tutorials
- FAQ section
- Video guides

## Launch Plan

### Pre-Launch
- Closed beta testing with 50 users
- Collect and implement feedback
- Final performance optimization
- Marketing material preparation

### Launch
- Phased rollout (iOS first, then Android)
- Initial marketing push
- Influencer outreach
- Press release

### Post-Launch
- Monitor user feedback and metrics
- Weekly updates for first month
- Implement critical fixes
- Begin work on Phase 2 features
