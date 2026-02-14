# Design Document
## The Talented Trove - Skill Barter Exchange Platform

### 1. System Architecture Overview

**Architecture Pattern:** Three-tier architecture (Presentation, Application, Data)

**Technology Stack:**
- Frontend: HTML5, CSS3, Vanilla JavaScript
- Backend: Node.js with Express.js
- Database: MongoDB with Mongoose ODM
- File Storage: Base64 encoding in MongoDB

**Communication:** RESTful API with JSON data format

---

### 2. System Components

#### 2.1 Frontend Layer

**Component Structure:**
```
Skill Barter Project/
├── index.html              # Landing page
├── register.html           # User registration
├── signup.html             # User login
├── dashboard.html          # User dashboard
├── profiles.html           # Browse profiles
├── profile.html            # Individual profile view
├── match.html              # Skill matching
├── sessions.html           # Session management
├── messages.html           # Messaging interface
├── calendar.html           # Session booking
├── feedback.html           # Feedback form
├── help.html               # Help page
├── contact.html            # Contact page
├── members.html            # Member directory
├── style.css               # Global styles
├── script.js               # Global utilities
├── dashboard.js            # Dashboard logic
├── match.js                # Matching logic
├── messages.js             # Messaging logic
├── profile.js              # Profile logic
├── session.js              # Session logic
├── feedback.js             # Feedback logic
├── form.js                 # Form handling
└── header.js               # Header component
```

#### 2.2 Backend Layer

**Component Structure:**
```
backend/
├── server.js               # Main server file
├── models/
│   └── User.js             # User model (alternative schema)
├── routes/
│   └── auth.js             # Authentication routes
├── middleware/             # Custom middleware
├── uploads/                # File upload directory
├── .env                    # Environment variables
└── package.json            # Dependencies
```


---

### 3. Database Design

#### 3.1 Data Models

**User Collection:**
```javascript
{
  _id: ObjectId,
  name: String (required),
  email: String (required, unique),
  password: String (required),
  teachSkill: String (required),
  learnSkill: String (required),
  skillDetails: String,
  certificate: String (base64),
  location: String,
  createdAt: Date (default: Date.now)
}
```

**Session Collection:**
```javascript
{
  _id: ObjectId,
  requester: ObjectId (ref: User),
  recipient: ObjectId (ref: User),
  type: String (enum: ['online', 'offline']),
  date: Date (required),
  location: String,
  notes: String,
  status: String (enum: ['pending', 'accepted', 'declined', 'completed']),
  requestedSkill: String (required),
  createdAt: Date (default: Date.now)
}
```

**Message Collection:**
```javascript
{
  _id: ObjectId,
  sender: ObjectId (ref: User),
  recipient: ObjectId (ref: User),
  content: String (required),
  timestamp: Date (default: Date.now),
  read: Boolean (default: false)
}
```

#### 3.2 Relationships

- User → Session: One-to-Many (as requester)
- User → Session: One-to-Many (as recipient)
- User → Message: One-to-Many (as sender)
- User → Message: One-to-Many (as recipient)

#### 3.3 Indexes

**Recommended Indexes:**
- User.email (unique)
- Session.requester
- Session.recipient
- Session.status
- Message.sender
- Message.recipient
- Message.read


---

### 4. API Design

#### 4.1 Authentication Endpoints

**POST /register**
- Purpose: Register new user
- Request Body:
  ```json
  {
    "name": "string",
    "email": "string",
    "password": "string",
    "teachSkill": "string",
    "learnSkill": "string",
    "skillDetails": "string",
    "location": "string",
    "certificate": "file"
  }
  ```
- Response: User object with success message
- Status Codes: 200 (success), 400 (validation error), 500 (server error)

**POST /login**
- Purpose: Authenticate user
- Request Body:
  ```json
  {
    "email": "string",
    "password": "string"
  }
  ```
- Response: User object with success message
- Status Codes: 200 (success), 400 (invalid credentials), 500 (server error)

#### 4.2 User Endpoints

**GET /users**
- Purpose: Retrieve all users
- Query Parameters: None
- Response: Array of user objects (passwords excluded)
- Status Codes: 200 (success), 500 (server error)

**GET /match**
- Purpose: Get AI-matched skill pairs
- Query Parameters: None
- Response: Array of match objects
- Match Object Structure:
  ```json
  {
    "person": "string",
    "personEmail": "string",
    "learns": "string",
    "partner": "string",
    "partnerEmail": "string",
    "teaches": "string",
    "partnerLocation": "string"
  }
  ```
- Status Codes: 200 (success), 500 (server error)


#### 4.3 Session Endpoints

**POST /sessions**
- Purpose: Create session request
- Request Body:
  ```json
  {
    "requesterEmail": "string",
    "recipientEmail": "string",
    "type": "online|offline",
    "date": "ISO date string",
    "location": "string (optional)",
    "notes": "string",
    "requestedSkill": "string"
  }
  ```
- Response: Session object with success message
- Status Codes: 201 (created), 404 (user not found), 500 (server error)

**GET /sessions**
- Purpose: Get sessions for a user
- Query Parameters: `email` (required)
- Response: Array of session objects with populated user data
- Status Codes: 200 (success), 400 (missing email), 404 (user not found), 500 (server error)

**PUT /sessions/:id/status**
- Purpose: Update session status
- URL Parameters: `id` (session ID)
- Request Body:
  ```json
  {
    "status": "pending|accepted|declined|completed"
  }
  ```
- Response: Updated session object
- Status Codes: 200 (success), 404 (session not found), 500 (server error)

#### 4.4 Message Endpoints

**POST /messages**
- Purpose: Send a message
- Request Body:
  ```json
  {
    "senderEmail": "string",
    "recipientEmail": "string",
    "content": "string"
  }
  ```
- Response: Message object with success message
- Status Codes: 201 (created), 404 (user not found), 500 (server error)

**GET /messages**
- Purpose: Get messages between two users
- Query Parameters: `userEmail`, `otherUserEmail` (both required)
- Response: Array of message objects with populated user data
- Side Effect: Marks messages as read
- Status Codes: 200 (success), 404 (user not found), 500 (server error)

**GET /messages/unread-count**
- Purpose: Get unread message count
- Query Parameters: `userEmail` (required)
- Response: `{ "count": number }`
- Status Codes: 200 (success), 404 (user not found), 500 (server error)

**GET /conversations**
- Purpose: Get all conversations for a user
- Query Parameters: `userEmail` (required)
- Response: Array of conversation objects
- Conversation Object Structure:
  ```json
  {
    "user": { "name": "string", "email": "string" },
    "lastMessage": "string",
    "lastTimestamp": "date",
    "unreadCount": number
  }
  ```
- Status Codes: 200 (success), 400 (missing email), 404 (user not found), 500 (server error)


---

### 5. Frontend Design

#### 5.1 Design System

**Color Palette:**
- Primary Gradient: `#48eb07` to `#342be7` (Green to Blue)
- Secondary Gradient: `#667eea` to `#764ba2` (Purple)
- Success: `#4CAF50`
- Error: `#f44336`
- Info: `#2196F3`
- Warning: `#ff9800`
- Background: `#e0f7fa` (Light cyan)
- Card Background: `#ffffff`
- Text Primary: `#222`, `#333`
- Text Secondary: `#555`, `#666`

**Typography:**
- Font Family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif
- Heading Sizes:
  - H1: 1.8em - 3.5em (context-dependent)
  - H2: 2.5em - 2.8em
  - H3: 1.4em
  - H4: 1.1em
- Body: 1em (16px base)
- Small Text: 0.8em - 0.9em

**Spacing:**
- Container Max Width: 1200px
- Container Padding: 40px 20px
- Card Padding: 20px - 30px
- Grid Gap: 20px
- Element Margins: 10px - 30px

**Border Radius:**
- Small: 6px - 8px
- Medium: 10px - 12px
- Large: 16px - 20px
- Circular: 50%

**Shadows:**
- Light: `0 2px 5px rgba(0,0,0,0.1)`
- Medium: `0 4px 12px rgba(0,0,0,0.1)`
- Heavy: `0 8px 32px rgba(0,0,0,0.1)`


#### 5.2 Component Design

**Button Component:**
```css
.btn {
  padding: 12px 24px;
  border-radius: 8px;
  font-weight: 600;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn.primary {
  background-color: #4CAF50;
  color: #fff;
}

.btn.secondary {
  background-color: #2196F3;
  color: #fff;
}
```

**Card Component:**
```css
.card {
  background: white;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}
```

**Modal Component:**
```css
.modal {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.5);
  z-index: 1000;
}

.modal-content {
  background-color: white;
  border-radius: 16px;
  width: 90%;
  max-width: 600px;
  max-height: 90vh;
  overflow-y: auto;
}
```

**Notification Component:**
```css
.notification {
  position: fixed;
  top: 20px;
  right: 20px;
  padding: 15px 25px;
  border-radius: 12px;
  color: white;
  font-weight: 600;
  z-index: 1000;
  animation: slideIn 0.3s ease-out;
}
```


#### 5.3 Page Layouts

**Landing Page (index.html):**
- Hero section with gradient background
- Animated floating shapes
- Feature cards (3 columns)
- Testimonials section
- Call-to-action buttons
- Sticky header with navigation

**Dashboard (dashboard.html):**
- Background image with overlay
- Statistics cards (3 columns):
  - Total Members
  - Pending Matches
  - Upcoming Sessions
- Quick links grid
- User profile summary card

**Match Page (match.html):**
- Match cards with:
  - Match header with badge
  - Two-column layout (person vs partner)
  - Request session button
- Session request modal:
  - Session type selector (online/offline)
  - Dynamic form based on type
  - Map integration for offline sessions

**Messages Page (messages.html):**
- Two-column layout:
  - Conversations sidebar (350px)
  - Chat area (flexible)
- Conversation items with:
  - Avatar
  - Name and preview
  - Timestamp
  - Unread badge
- Chat interface with:
  - Header with user info
  - Message list (scrollable)
  - Input field with send button

**Profile Page (profile.html):**
- Gradient header with avatar
- Statistics display (3 columns)
- Profile sections:
  - Skills I Teach
  - Skills I Want to Learn
  - About Me
  - Certificate display

**Sessions Page (sessions.html):**
- Session cards with:
  - Partner information
  - Session details (type, date, location)
  - Status indicator
  - Action buttons (accept/decline)


#### 5.4 Responsive Design

**Breakpoints:**
- Desktop: > 1200px
- Tablet: 768px - 1200px
- Mobile: < 768px

**Grid System:**
```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}
```

**Responsive Considerations:**
- Flexible container widths: `min(1200px, 92%)`
- Auto-fill grid columns
- Flexible font sizes (em units)
- Stacked layouts on mobile
- Touch-friendly button sizes (minimum 44px)

---

### 6. User Interface Patterns

#### 6.1 Navigation Pattern

**Header Navigation:**
- Logo on left
- Navigation links on right
- Sticky positioning
- White background with shadow
- Consistent across all pages

**Navigation Structure:**
```
Landing → Login/Register
Dashboard → Profiles, Sessions, Match, Feedback, Members
Profiles → Individual Profile, Match
Match → Session Request Modal
Sessions → Accept/Decline Actions
Messages → Conversation List, Chat Interface
```

#### 6.2 Form Patterns

**Registration Form:**
- Multi-section layout
- Fieldsets for skill information
- File upload for certificates
- Validation on submit
- Redirect to login on success

**Login Form:**
- Simple two-field form
- Remember credentials (localStorage)
- Redirect to dashboard on success
- Link to registration

**Session Request Form:**
- Modal-based
- Type selection (online/offline)
- Conditional fields based on type
- Map integration for offline
- Validation before submit


#### 6.3 Interaction Patterns

**Hover Effects:**
- Cards: Translate up + shadow increase
- Buttons: Background color change
- Links: Color change
- Transition duration: 0.3s

**Click Actions:**
- Match card → Open session modal
- Conversation item → Load chat
- Session accept/decline → Update status + reload
- Send message → Add to chat + clear input

**Loading States:**
- Fetch data on page load
- Display error messages on failure
- Show "No data" messages when empty
- Auto-refresh for real-time features (messages)

**Animations:**
- Fade in/down for hero content
- Slide in for notifications
- Float animation for decorative shapes
- Smooth scrolling

---

### 7. Matching Algorithm Design

#### 7.1 Algorithm Logic

**Matching Criteria:**
1. User A wants to learn skill X
2. User B teaches skill X
3. Create match: A learns from B

**Implementation:**
```javascript
for (const user of users) {
  const partners = users.filter(u => 
    u.email !== user.email && 
    u.teachSkill.toLowerCase().trim() === user.learnSkill.toLowerCase().trim()
  );
  
  for (const partner of partners) {
    matches.push({
      person: user.name,
      personEmail: user.email,
      learns: user.learnSkill,
      partner: partner.name,
      partnerEmail: partner.email,
      teaches: partner.teachSkill,
      partnerLocation: partner.location
    });
  }
}
```

**Matching Features:**
- Case-insensitive comparison
- Trim whitespace
- One-way matches (A→B doesn't require B→A)
- Location awareness (display partner location)


#### 7.2 Future Algorithm Enhancements

**Planned Improvements:**
- Skill similarity matching (e.g., "JavaScript" matches "JS")
- Multi-skill matching
- Location-based proximity matching
- Availability matching
- Rating-based matching
- Machine learning for better recommendations

---

### 8. Session Management Design

#### 8.1 Session Types

**Online Sessions:**
- Video call based
- No location required
- Redirect to messages after request
- Suitable for remote learning

**Offline Sessions:**
- In-person meetings
- Location required
- Map integration for location selection
- Geocoding via Nominatim API

#### 8.2 Session Workflow

**Request Flow:**
1. User views matches
2. Clicks "Request Session"
3. Selects session type
4. Fills form (date, time, notes, location)
5. Submits request
6. System creates session with "pending" status

**Approval Flow:**
1. Recipient views sessions
2. Sees pending requests
3. Clicks accept or decline
4. System updates status
5. Both users notified

**Session States:**
- Pending: Awaiting recipient response
- Accepted: Recipient approved
- Declined: Recipient rejected
- Completed: Session finished


#### 8.3 Map Integration

**Technology:** Leaflet.js with OpenStreetMap

**Features:**
- Interactive map display
- Location marker
- Geocoding for address search
- Pan and zoom controls
- No API key required

**Implementation:**
```javascript
map = L.map('map').setView([40.7128, -74.0060], 13);
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
marker = L.marker([lat, lon]).addTo(map).bindPopup('Meeting Location');
```

---

### 9. Messaging System Design

#### 9.1 Conversation Management

**Conversation List:**
- Sorted by most recent message
- Display last message preview
- Show unread count badge
- Avatar with user initial
- Timestamp formatting (Today, Yesterday, Date)

**Conversation Loading:**
- Fetch all conversations on page load
- Auto-refresh every 10 seconds
- Support URL parameter for direct conversation

#### 9.2 Chat Interface

**Message Display:**
- Sent messages: Right-aligned, gradient background
- Received messages: Left-aligned, white background
- Timestamp below each message
- Auto-scroll to bottom on new message

**Message Sending:**
- Input field with send button
- Enter key to send
- Clear input after send
- Immediate UI update

**Read Status:**
- Mark messages as read when conversation opened
- Update unread count in real-time


---

### 10. State Management

#### 10.1 Client-Side Storage

**LocalStorage Keys:**
- `current_user`: Logged-in user object
  ```json
  {
    "id": "string",
    "name": "string",
    "email": "string",
    "teachSkill": "string",
    "learnSkill": "string",
    "certificate": "string",
    "location": "string"
  }
  ```
- `auth_token`: Authentication token (future implementation)
- `talent_users`: Cached user list (fallback)

**Session Management:**
- Store user data on login
- Clear on logout
- Check authentication on protected pages
- Redirect to login if not authenticated

#### 10.2 State Synchronization

**Data Flow:**
1. User action (click, submit)
2. API request to backend
3. Backend processes and responds
4. Frontend updates UI
5. LocalStorage updated if needed

**Caching Strategy:**
- Cache user list for offline access
- Refresh on dashboard load
- Fallback to cache on network error

---

### 11. Security Design

#### 11.1 Current Security Measures

**CORS Configuration:**
```javascript
app.use(cors());
```
- Allows cross-origin requests
- Necessary for frontend-backend communication

**Input Validation:**
- Required field checks
- Email uniqueness validation
- File size limits (10MB)
- Content type validation

**Data Sanitization:**
- Mongoose schema validation
- Type checking on inputs


#### 11.2 Security Improvements Needed

**Critical:**
1. Password hashing with bcrypt
2. JWT-based authentication
3. HTTPS enforcement
4. Rate limiting
5. SQL injection prevention (already handled by Mongoose)
6. XSS protection

**Recommended:**
1. Email verification
2. Password reset functionality
3. Session timeout
4. CSRF protection
5. Input sanitization library
6. Secure file upload validation
7. Environment variable protection

---

### 12. Error Handling Design

#### 12.1 Backend Error Handling

**Error Response Format:**
```json
{
  "message": "Error description",
  "error": "Detailed error (development only)"
}
```

**HTTP Status Codes:**
- 200: Success
- 201: Created
- 400: Bad Request (validation error)
- 404: Not Found
- 500: Internal Server Error

**Error Logging:**
```javascript
console.error('Error description:', error);
```

#### 12.2 Frontend Error Handling

**Try-Catch Blocks:**
```javascript
try {
  const response = await fetch(url);
  if (!response.ok) throw new Error('Request failed');
  const data = await response.json();
} catch (error) {
  console.error('Error:', error);
  showNotification('Error message', 'error');
}
```

**User Feedback:**
- Notification system for errors
- Console logging for debugging
- Fallback UI for missing data
- Graceful degradation


---

### 13. Performance Optimization

#### 13.1 Frontend Optimization

**Asset Loading:**
- CDN for Font Awesome icons
- External libraries (Leaflet) loaded on demand
- Minimal JavaScript files
- CSS in single file

**Code Optimization:**
- Vanilla JavaScript (no framework overhead)
- Event delegation where possible
- Debouncing for search/input
- Lazy loading for images

**Caching:**
- LocalStorage for user data
- Browser caching for static assets
- Service worker (future implementation)

#### 13.2 Backend Optimization

**Database Queries:**
- Indexed fields for faster lookups
- Populate only necessary fields
- Limit query results when appropriate
- Aggregate queries for statistics

**API Optimization:**
- JSON response compression
- Efficient data structures
- Minimal data transfer
- Connection pooling (MongoDB default)

**Server Configuration:**
```javascript
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));
```

---

### 14. Testing Strategy

#### 14.1 Manual Testing

**Test Cases:**
1. User registration with all fields
2. User login with valid/invalid credentials
3. View all users
4. Match algorithm accuracy
5. Session request creation
6. Session status updates
7. Message sending and receiving
8. Conversation list updates
9. Profile display
10. Certificate upload and display


#### 14.2 Automated Testing (Future)

**Unit Tests:**
- Matching algorithm logic
- Form validation functions
- Data transformation utilities
- API endpoint handlers

**Integration Tests:**
- API endpoint workflows
- Database operations
- Authentication flow
- Session management

**End-to-End Tests:**
- Complete user registration flow
- Match and session request flow
- Messaging workflow
- Profile viewing

**Testing Tools (Recommended):**
- Jest for unit tests
- Supertest for API tests
- Cypress for E2E tests
- MongoDB Memory Server for test database

---

### 15. Deployment Architecture

#### 15.1 Development Environment

**Local Setup:**
- MongoDB running on localhost:27017
- Backend server on localhost:5000
- Frontend served via file:// or local server
- Environment variables in .env file

**Required Environment Variables:**
```
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/SKILLDB
```

#### 15.2 Production Environment (Recommended)

**Infrastructure:**
- Frontend: Static hosting (Netlify, Vercel, GitHub Pages)
- Backend: Node.js hosting (Heroku, Railway, DigitalOcean)
- Database: MongoDB Atlas (cloud)
- File Storage: Cloud storage (AWS S3, Cloudinary)

**Configuration:**
- HTTPS enabled
- Environment variables secured
- CORS restricted to frontend domain
- Database connection pooling
- Logging and monitoring


---

### 16. Scalability Considerations

#### 16.1 Current Limitations

- Single server instance
- No load balancing
- In-memory session storage
- No caching layer
- Synchronous message polling

#### 16.2 Scalability Improvements

**Horizontal Scaling:**
- Multiple backend instances
- Load balancer (Nginx, AWS ALB)
- Stateless authentication (JWT)
- Distributed session storage (Redis)

**Database Scaling:**
- MongoDB replica sets
- Sharding for large datasets
- Read replicas for queries
- Connection pooling

**Real-Time Features:**
- WebSocket implementation (Socket.io)
- Message queue (RabbitMQ, Redis)
- Push notifications
- Server-sent events

**Caching Layer:**
- Redis for session data
- CDN for static assets
- API response caching
- Database query caching

---

### 17. Accessibility Design

#### 17.1 Current Accessibility Features

**Semantic HTML:**
- Proper heading hierarchy
- Form labels
- Button elements
- Alt text for images (logo)

**Keyboard Navigation:**
- Focusable interactive elements
- Tab order follows visual order
- Enter key for form submission

**Visual Design:**
- High contrast text
- Readable font sizes
- Clear focus indicators
- Color not sole indicator
