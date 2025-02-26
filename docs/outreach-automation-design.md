# TikTok Shop Creator Outreach Automation Tool - MVP Design Document

> **The contents of this document belong to TJ Maxwell, and are not to be used or shared without his explicit permission.**

## Overview

> **CRITICAL DEPENDENCY**: This project relies on access to TikTok's Creator Marketplace API, which is currently in closed beta and requires approval. Securing this access should be our first priority before significant development begins.

The TikTok Shop Creator Outreach Automation Tool enables users to link their TikTok Business accounts, discover relevant influencers based on filters, automate messaging campaigns, and track message statuses. The platform is built using Next.js and Supabase for speed and ease of development.

## 1. Core Features & User Flow

### A. User Authentication & TikTok Account Linking

- Users sign up and log in via Supabase Auth
- Users connect their TikTok Business or Creator account using TikTok OAuth
- The system stores the following details upon successful authentication:
  - TikTok Account ID
  - Access Token & Refresh Token
  - Business details (name, description, etc.)

**Notes:**

- The OAuth token has a limited lifespan, requiring a refresh mechanism
- Securely store and manage tokens

### B. Dashboard Overview

- Display pertinent account information (Tiktok Shop details)
- Show simple campaign metrics:
  - Total messages sent
  - Response rates
  - Active campaigns

**Notes:**

- Implement API rate monitoring to prevent overuse
- Implement fallback/retry strategies for API calls

### C. Creator Discovery

- Integrate the TikTok Creator Marketplace API to search creators based on:
  - Follower count
  - Engagement rate
  - Average video views
  - Audience age
  - Content category
- Allow users to apply search filters

**Notes:**

- The Tiktok Creator Marketplace API is in closed beta
- The API may have rate limits; caching strategies should be implemented
- Filtering logic should allow dynamic refinement of results

### D. Outreach Automation & Message Scheduling

- Users create campaigns by:
  - Writing personalized message templates with placeholders (e.g., `{creator_name}`)
  - Setting message timing window if desired
  - Selecting creators to target based on filters
- The system queues and schedules messages based on TikTok's API constraints
- After the campaign is created, a user can view the campaign status and results in the dashboard

**Notes:**

- **TikTok Messaging Limits:**
  - Exact rate limits for messaging need verification from TikTok's API documentation
- **Automation Approach:**
  - AWS Lambda to handle scheduled message dispatch.

### E. Message Delivery & Status Tracking

- Messages are sent via the TikTok Business Messaging API.
- The system tracks message statuses through periodic API polling:
  - **Sent**
  - **Delivered**
  - **Seen**
  - **Replied**
- If a creator responds, the system updates the status on the next poll cycle.

**Notes:**

- Store status changes in Supabase for tracking

---

## 2. Technical Breakdown

### Frontend (Next.js)

- **Authentication**: Supabase Auth with TikTok OAuth integration
- **Dashboard UI**: React components displaying user data, campaigns, and basic metrics
- **TikTok OAuth Flow**: Redirect-based authentication and token management
- **Creator Search & Selection UI**: Filtering TikTok creators via Tiktok API
- **Campaign Builder & Messaging Interface**: Form-based campaign creation

**Notes:**

- The frontend will be built using Next.js, Tailwind CSS, and Material UI
- The site will be hosted on Vercel
- Responsive and mobile-friendly

### Backend (Supabase + APIs)

- **User Authentication & Session Management**
  - Supabase Auth
  - TikTok OAuth
- **Database Schema**:

  - `users`: User account information and OAuth tokens
  - `creators`: Creator data fetched from TikTok
  - `campaigns`: Campaign configuration
  - `campaigns_launched`: Campaigns that have been launched and their status
  - `message_templates`: Reusable message templates
  - `campaign_creators`: Mapping between campaigns and targeted creators
  - `messages`: Individual messages with their status and tracking

- **API Integrations**:
  - Fetch creator data via TikTok Creator Marketplace API (closed beta)
  - Send messages via TikTok Business Messaging API (requires approval)

### Automation Approach

- **AWS Lambda**:
  - Handle scheduled message dispatch
  - Process queued messages based on rate limits
  - Periodically poll TikTok API for message status updates

---

## 3. Additional Considerations

### Compliance & API Constraints

- Ensure adherence to TikTok's Business Messaging guidelines
- Implement anti-spam measures (e.g., message frequency limits).
- Require user verification before enabling outreach campaigns after creation

### Performance Monitoring

- Track API usage to avoid hitting rate limits and to monitor performance
- Error handling, logging, and monitoring
- If rate limits are too low or if demand is too high, implement queuing to handle the load

### Future Expansion

#### High Priority

- Continuous Integration/Deployment Pipeline
- Automated follow-up messages if no response is received
- Creator lists and saved filters

#### Medium Priority

- Webhook implementation for real-time status updates
- Advanced dashboard metrics and analytics
- Custom fields and tagging for creators
- Multiple outreach modes and campaign types
- CRM-style pipeline for influencer management
- Payment processing (e.g. Stripe integration)
- AI-assisted message personalization
- Store fetched creator data in the database for additional analysis

---

## 4. Timeline/Roadmap

> This timeline is preliminary. Numerous factors will affect the actual development time. As development progresses, the timeline will become more concrete. Any foreseen changes will be communicated as soon as possible.

### Phase 1: Research & Setup (Weeks 1-2)

- **Week 1**: TikTok API research and project initialization
  - Research TikTok API documentation and requirements
  - Submit API access applications
  - Set up infrastructure (AWS, Supabase, Vercel)
  - Set up Next.js project and Supabase connection
  - Design initial database schema
- **Week 2**: Environment setup and planning
  - Configure development workflows
  - Create wireframes for core UI components
  - Set up initial project structure: repos for frontend/backend (NextJS) automation (AWS Lambda), and shared resources

### Phase 2: Core Authentication & Dashboard (Weeks 3-5)

- **Week 3**: Authentication implementation
  - Implement Supabase Auth
  - Set up TikTok OAuth flow
  - Create user profile management
- **Week 4-5**: Basic dashboard and navigation
  - Implement responsive layout structure
  - Create simple dashboard components
  - Build navigation and basic UI elements
  - Implement token refresh mechanism

### Phase 3: Creator Discovery & Campaign Creation (Weeks 6-10)

- **Week 6-7**: Creator discovery implementation
  - Build filter UI and search functionality
  - Connect to TikTok Creator API
  - Implement creator search results display
  - Re-evaluate timeline based on speed of work up to this point
- **Week 8-10**: Campaign creation functionality
  - Implement message template editor
  - Build campaign configuration screens
  - Create creator selection interface
  - Set up campaign storage in database

### Phase 4: Automation & Message Tracking (Weeks 11-14)

- **Week 11-12**: AWS Lambda implementation
  - Create Lambda functions for message dispatch
  - Implement scheduling logic
  - Set up basic monitoring
- **Week 13-14**: Message handling and status tracking
  - Build status polling mechanism
  - Implement message status display and updates
  - Create basic notification system

### Phase 5: Refinement & Launch (Week 15-16)

- **Week 15-16**: Testing and bug fixes
  - Set up test environment for end-to-end testing
  - Create automated end-to-end tests
  - Identify and Fix critical bugs and edge cases
  - Optimize performance (load times for frontend, database optimizations, proper pagination, etc.)
- **Week 17-18**: Final polishing and documentation
  - Address UI/UX improvements
  - Create user documentation
  - Prepare for deployment

**Total Timeline: 4 months (18 weeks)**

### Key Assumptions:

1. Approximately 20 hours per week of development time
2. TikTok API access approval within the first 2-3 weeks
3. No major changes to TikTok API functionality during development
4. Prioritizing functionality over design refinement for MVP
