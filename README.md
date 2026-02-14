Attachment AWS AI FOR BHARAT.pdf added.Conversation opened. 11 messages. All messages read.

Skip to content
Using Mepco Schlenk Engineering College, Sivakasi Mail with screen readers

Conversations
 
Program Policies
Powered by Google
Last account activity: 12 minutes ago
Details
# Requirements Document
## The Talented Trove - Skill Barter Exchange Platform

### 1. Project Overview

**Project Name:** The Talented Trove

**Purpose:** A web-based platform that enables users to exchange skills through a barter system, connecting individuals who want to teach skills with those who want to learn them.

**Target Users:** Individuals seeking to learn new skills while sharing their own expertise without monetary transactions.

---

### 2. Functional Requirements

#### 2.1 User Authentication & Registration

**FR-1.1:** Users must be able to register with the following information:
- Name (required)
- Email address (required, unique)
- Password (required)
- Skill to teach (required)
- Skill to learn (required)
- Skill details/description (optional)
- Location (optional)
- Certificate upload (optional, image file)

**FR-1.2:** Users must be able to log in using email and password credentials.

**FR-1.3:** System must validate unique email addresses during registration.

**FR-1.4:** User sessions must persist using local storage for authenticated access.

**FR-1.5:** Users must be able to log out, clearing session data.

#### 2.2 User Profile Management

**FR-2.1:** Users must be able to view their own profile displaying:
- Name and email
- Skills they teach
- Skills they want to learn
- Location
- Uploaded certificates
- Profile statistics (sessions, matches, community size)

**FR-2.2:** System must generate profile avatars with user initials.

**FR-2.3:** Users must be able to view other members' profiles.

**FR-2.4:** System must display all registered members in a browsable directory.

#### 2.3 Skill Matching System

**FR-3.1:** System must implement an AI-based matching algorithm that:
- Matches users based on complementary skills
- Finds users who can teach what another user wants to learn
- Displays bidirectional skill exchange opportunities

**FR-3.2:** Match results must display:
- User name and email
- Skill being taught
- Skill being learned
- Partner information
- Partner location

**FR-3.3:** Users must be able to view all potential matches from the match page.

#### 2.4 Session Scheduling

**FR-4.1:** Users must be able to request sessions with matched partners.

**FR-4.2:** System must support two session types:
- Online sessions (video call)
- Offline sessions (in-person meetings)

**FR-4.3:** Session requests must include:
- Session type (online/offline)
- Date and time
- Location (for offline sessions)
- Session notes/description
- Requested skill

**FR-4.4:** Session status management:
- Pending (initial state)
- Accepted (recipient approves)
- Declined (recipient rejects)
- Completed (session finished)

**FR-4.5:** Recipients must be able to accept or decline pending session requests.

**FR-4.6:** Users must be able to view all their sessions (as requester or recipient).

**FR-4.7:** System must display upcoming sessions count on dashboard.

#### 2.5 Messaging System

**FR-5.1:** Users must be able to send direct messages to other users.

**FR-5.2:** System must display conversation history between two users.

**FR-5.3:** Messages must include:
- Sender and recipient information
- Message content
- Timestamp
- Read/unread status

**FR-5.4:** System must track unread message counts.

**FR-5.5:** Users must be able to view all their conversations in a sidebar.

**FR-5.6:** System must mark messages as read when viewed.

**FR-5.7:** Conversations must display:
- Contact name
- Last message preview
- Timestamp
- Unread badge (if applicable)

#### 2.6 Dashboard & Analytics

**FR-6.1:** Dashboard must display:
- Welcome message with user name
- Total members count
- Pending matches count
- Upcoming sessions count
- User profile summary

**FR-6.2:** Dashboard must provide quick links to:
- View profiles
- All members
- My sessions
- Find matches
- Feedback
- Book session

#### 2.7 Feedback System

**FR-7.1:** Users must be able to submit feedback about the platform.

**FR-7.2:** Feedback form must collect user experiences and suggestions.

#### 2.8 Calendar & Booking

**FR-8.1:** System must provide a calendar interface for session booking.

**FR-8.2:** Users must be able to select dates and times for sessions.

---

### 3. Non-Functional Requirements

#### 3.1 Performance

**NFR-1.1:** Page load time must not exceed 3 seconds under normal network conditions.

**NFR-1.2:** API response time must be under 2 seconds for standard queries.

**NFR-1.3:** System must support at least 100 concurrent users.

#### 3.2 Security

**NFR-2.1:** User passwords must be stored securely (currently plain text - requires encryption).

**NFR-2.2:** All API endpoints must validate user authentication.

**NFR-2.3:** File uploads must be validated for type and size (max 10MB).

**NFR-2.4:** System must prevent SQL injection and XSS attacks.

**NFR-2.5:** CORS must be properly configured for cross-origin requests.

#### 3.3 Usability

**NFR-3.1:** Interface must be intuitive and require minimal training.

**NFR-3.2:** System must provide clear error messages and feedback.

**NFR-3.3:** Navigation must be consistent across all pages.

**NFR-3.4:** Design must be visually appealing with modern UI/UX principles.

#### 3.4 Compatibility

**NFR-4.1:** System must work on modern web browsers (Chrome, Firefox, Safari, Edge).

**NFR-4.2:** Interface must be responsive for desktop and tablet devices.

**NFR-4.3:** System must support common image formats for certificate uploads (JPEG, PNG, PDF).

#### 3.5 Reliability

**NFR-5.1:** System uptime must be at least 99%.

**NFR-5.2:** Database must have backup and recovery mechanisms.

**NFR-5.3:** System must handle errors gracefully without crashing.

#### 3.6 Maintainability

**NFR-6.1:** Code must follow consistent naming conventions and structure.

**NFR-6.2:** System must use modular architecture for easy updates.

**NFR-6.3:** API endpoints must be well-documented.

---

### 4. System Architecture

#### 4.1 Technology Stack

**Frontend:**
- HTML5, CSS3, JavaScript (Vanilla)
- Font Awesome for icons
- Responsive design with custom CSS

**Backend:**
- Node.js with Express.js framework
- RESTful API architecture

**Database:**
- MongoDB (NoSQL database)
- Mongoose ODM for data modeling

**File Handling:**
- Multer for file uploads
- Base64 encoding for certificate storage

#### 4.2 Data Models

**User Schema:**
```
- name: String (required)
- email: String (required, unique)
- password: String (required)
- teachSkill: String (required)
- learnSkill: String (required)
- skillDetails: String
- certificate: String (base64)
- location: String
- createdAt: Date
```

**Session Schema:**
```
- requester: ObjectId (ref: User)
- recipient: ObjectId (ref: User)
- type: String (online/offline)
- date: Date
- location: String
- notes: String
- status: String (pending/accepted/declined/completed)
- requestedSkill: String
- createdAt: Date
```

**Message Schema:**
```
- sender: ObjectId (ref: User)
- recipient: ObjectId (ref: User)
- content: String
- timestamp: Date
- read: Boolean
```

---

### 5. API Endpoints

#### 5.1 Authentication
- `POST /register` - Register new user
- `POST /login` - User login

#### 5.2 Users
- `GET /users` - Get all users
- `GET /match` - Get skill matches

#### 5.3 Sessions
- `POST /sessions` - Create session request
- `GET /sessions?email={email}` - Get user sessions
- `PUT /sessions/:id/status` - Update session status

#### 5.4 Messages
- `POST /messages` - Send message
- `GET /messages?userEmail={email}&otherUserEmail={email}` - Get conversation
- `GET /messages/unread-count?userEmail={email}` - Get unread count
- `GET /conversations?userEmail={email}` - Get all conversations

---

### 6. User Interface Requirements

#### 6.1 Pages

1. **Landing Page (index.html)**
   - Hero section with call-to-action
   - Feature highlights
   - Testimonials
   - Navigation to login/register

2. **Registration Page (register.html)**
   - Registration form with all required fields
   - Certificate upload functionality

3. **Login Page (signup.html)**
   - Email and password fields
   - Link to registration

4. **Dashboard (dashboard.html)**
   - Statistics overview
   - Quick links
   - User profile summary

5. **Profiles Page (profiles.html)**
   - Browse all user profiles
   - View individual profile details

6. **Match Page (match.html)**
   - Display AI-matched skill pairs
   - Session request modal
   - Online/offline session options

7. **Sessions Page (sessions.html)**
   - List of all user sessions
   - Accept/decline functionality
   - Session details

8. **Messages Page (messages.html)**
   - Conversation sidebar
   - Chat interface
   - Real-time messaging

9. **Profile Page (profile.html)**
   - Personal profile view
   - Statistics display
   - Certificate display

10. **Calendar Page (calendar.html)**
    - Date/time selection
    - Session booking interface

11. **Feedback Page (feedback.html)**
    - Feedback submission form

12. **Help Page (help.html)**
    - Platform guidance
    - FAQ section

13. **Contact Page (contact.html)**
    - Contact information
    - Support form

14. **Members Page (members.html)**
    - Complete member directory

#### 6.2 Design Requirements

**Color Scheme:**
- Primary: Blue gradient (#48eb07 to #342be7)
- Secondary: Purple gradient (#667eea to #764ba2)
- Background: White with transparency effects
- Text: Dark gray (#333) on light backgrounds

**Typography:**
- Clean, modern sans-serif fonts
- Hierarchical heading sizes
- Readable body text (minimum 16px)

**Visual Effects:**
- Gradient backgrounds
- Glassmorphism (backdrop blur)
- Smooth transitions and animations
- Hover effects on interactive elements
- Card-based layouts
- Rounded corners (border-radius)

---

### 7. Constraints & Assumptions

#### 7.1 Constraints

- System requires active internet connection
- MongoDB must be running locally or remotely
- Backend server must run on port 5000
- File uploads limited to 10MB
- No mobile app version (web-only)

#### 7.2 Assumptions

- Users have basic computer literacy
- Users will provide accurate skill information
- Users will honor scheduled sessions
- Internet bandwidth sufficient for video calls (online sessions)
- Users consent to data storage and processing

---

### 8. Future Enhancements

**FE-1:** Implement password hashing (bcrypt) for security

**FE-2:** Add JWT-based authentication tokens

**FE-3:** Implement real-time messaging with WebSockets

**FE-4:** Add video call integration for online sessions

**FE-5:** Implement skill rating and review system

**FE-6:** Add notification system for messages and session requests

**FE-7:** Implement search and filter functionality for users

**FE-8:** Add skill categories and tags

**FE-9:** Implement user reputation/trust score

**FE-10:** Add mobile responsive design improvements

**FE-11:** Implement email verification

**FE-12:** Add password reset functionality

**FE-13:** Implement advanced matching algorithm with ML

**FE-14:** Add multi-language support

**FE-15:** Implement analytics dashboard for administrators

---

### 9. Success Criteria

**SC-1:** Users can successfully register and log in

**SC-2:** Matching algorithm correctly identifies complementary skills

**SC-3:** Users can schedule and manage sessions

**SC-4:** Messaging system enables communication between users

**SC-5:** System maintains 99% uptime

**SC-6:** User satisfaction rating above 4/5

**SC-7:** Average session booking time under 2 minutes

**SC-8:** Zero critical security vulnerabilities

---

### 10. Glossary

- **Skill Barter:** Exchange of skills without monetary transaction
- **Session:** Scheduled meeting between two users for skill exchange
- **Match:** Two users with complementary skills (one teaches what the other wants to learn)
- **Requester:** User who initiates a session request
- **Recipient:** User who receives a session request
- **Online Session:** Virtual meeting via video call
- **Offline Session:** In-person meeting at a physical location

---

**Document Version:** 1.0  
**Last Updated:** February 14, 2026  
**Status:** Active
requirements.md
Displaying requirements.md. 
