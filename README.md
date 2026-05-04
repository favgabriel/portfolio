# Favour Aighangbe — Technical Portfolio

> **Software Engineer** | Mobile & Backend Development | Available for UK Opportunities

[![Email](https://img.shields.io/badge/Email-aighangbe%40gmail.com-blue)](mailto:aighangbe@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/favour-gabriel)
[![Location](https://img.shields.io/badge/Location-Available%20to%20relocate%20to%20UK-green)]()

---

## About This Portfolio

My production work at **SoloChat** and **Finddle** is in private repositories. This portfolio contains **architecture documentation, technical case studies, sanitized code patterns, and project demos** that demonstrate my approach to building reliable software systems.

**I am seeking software engineering roles in the UK and require Skilled Worker visa sponsorship.**

---

## Production Experience Documentation

### SoloChat — Real-Time Messaging Platform

**Role:** Mobile Software Engineer  
**Tech Stack:** Kotlin, Firebase (Auth, Firestore, Cloud Messaging, Cloud Functions), WebRTC, Google Maps API, Retrofit, Room Database

#### Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      SOLOCHAT ARCHITECTURE                   │
├─────────────────────────────────────────────────────────────┤
│  MOBILE LAYER                                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Kotlin     │  │   Kotlin     │  │   Kotlin     │     │
│  │   (User A)   │  │   (User B)   │  │   (User C)   │     │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘     │
│         │                 │                 │              │
│         └─────────────────┼─────────────────┘              │
│                           │                                │
│  ┌────────────────────────┴────────────────────────┐        │
│  │           FIREBASE BACKEND                     │        │
│  │  ┌────────────┐  ┌────────────┐  ┌─────────┐  │        │
│  │  │  Firestore │  │  Cloud     │  │  Cloud  │  │        │
│  │  │  (Real-time│  │  Functions│  │Messaging│  │        │
│  │  │   sync)    │  │  (Business │  │ (Push   │  │        │
│  │  │            │  │   logic)   │  │ notes)  │  │        │
│  │  └────────────┘  └────────────┘  └─────────┘  │        │
│  └─────────────────────────────────────────────────┘        │
│                           │                                │
│  ┌────────────────────────┴────────────────────────┐        │
│  │           WEBRTC SIGNALING                      │        │
│  │  (Peer-to-peer video calling coordination)      │        │
│  └─────────────────────────────────────────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

#### Key Technical Challenges Solved

**1. Real-Time Message Synchronization**
- **Problem:** Ensuring sub-second message delivery across unstable mobile networks
- **Solution:** Implemented Firestore real-time listeners with local caching via Room Database
- **Pattern:** Optimistic UI updates with server reconciliation
- **Result:** Messages appear instantly; conflicts resolved automatically on reconnection

**2. WebRTC Signaling in Unreliable Networks**
- **Problem:** Video call connections break when users switch between WiFi and mobile data
- **Solution:** Custom ICE candidate management with Firebase as signaling server
- **Pattern:** Connection state monitoring with automatic renegotiation
- **Result:** 95% successful call completion rate (up from 70%)

**3. Battery-Efficient Location Tracking**
- **Problem:** Background geolocation drained battery rapidly
- **Solution:** Geofencing with Google Maps API + Firebase; only active near boundaries
- **Pattern:** Foreground service for active tracking, passive receiver for geofence triggers
- **Result:** 60% reduction in battery consumption vs. continuous GPS polling

#### Sanitized Code Patterns

**Retrofit API Interface (Kotlin)**
```kotlin
interface SoloChatApi {
    @POST("auth/login")
    suspend fun login(
        @Body request: LoginRequest
    ): Response<AuthResponse>

    @GET("users/{userId}/profile")
    suspend fun getUserProfile(
        @Path("userId") userId: String,
        @Header("Authorization") token: String
    ): Response<UserProfile>

    @POST("messages/send")
    suspend fun sendMessage(
        @Body message: MessageRequest,
        @Header("Authorization") token: String
    ): Response<MessageResponse>
}
```

**Firebase Firestore Real-Time Listener (Kotlin)**
```kotlin
class ChatRepository(private val db: FirebaseFirestore) {

    fun getMessagesForChat(chatId: String): Flow<List<Message>> = 
        db.collection("chats")
          .document(chatId)
          .collection("messages")
          .orderBy("timestamp", Query.Direction.ASCENDING)
          .snapshotFlow()
          .map { snapshot ->
              snapshot.documents.map { doc ->
                  doc.toObject(Message::class.java)!!
              }
          }
          .catch { e ->
              Log.e("ChatRepository", "Error loading messages", e)
              emit(emptyList())
          }
}
```

---

### Finddle — Backend Services & Infrastructure

**Role:** Software Developer  
**Tech Stack:** Python, Flask, JavaScript, MySQL, Git, AWS, Monitoring Tools

#### Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    FINDDLE BACKEND                         │
├─────────────────────────────────────────────────────────────┤
│  API LAYER                                                  │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Python Flask REST API                              │   │
│  │  ├─ Authentication endpoints                        │   │
│  │  ├─ Core business logic                             │   │
│  │  ├─ Third-party integrations                        │   │
│  │  └─ Webhook handlers                                │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                │
│  ┌────────────────────────┴────────────────────────┐        │
│  │           DATA LAYER                             │        │
│  │  ┌────────────┐  ┌────────────┐  ┌─────────┐  │        │
│  │  │   MySQL    │  │   Redis    │  │  AWS    │  │        │
│  │  │  (Primary  │  │  (Caching  │  │  S3     │  │        │
│  │  │   data)    │  │   layer)   │  │ (Files) │  │        │
│  │  └────────────┘  └────────────┘  └─────────┘  │        │
│  └─────────────────────────────────────────────────┘        │
│                           │                                │
│  ┌────────────────────────┴────────────────────────┐        │
│  │           OBSERVABILITY LAYER                    │        │
│  │  ┌────────────┐  ┌────────────┐  ┌─────────┐  │        │
│  │  │ Structured │  │  Custom    │  │ Alerts  │  │        │
│  │  │  Logging   │  │  Metrics   │  │ (Pager  │  │        │
│  │  │  (ELK/     │  │  (Grafana) │  │ Duty)   │  │        │
│  │  │  CloudWatch)│  │            │  │         │  │        │
│  │  └────────────┘  └────────────┘  └─────────┘  │        │
│  └─────────────────────────────────────────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

#### Key Technical Challenges Solved

**1. Production Incident Response**
- **Problem:** Slow detection of API failures affecting mobile clients
- **Solution:** Implemented structured logging with correlation IDs and automated alerting
- **Pattern:** Request tracing from mobile to API to database; alerts on p95 latency thresholds
- **Result:** Reduced mean-time-to-detection from 30 minutes to 5 minutes

**2. Database Performance Under Load**
- **Problem:** MySQL query slowdown during peak usage
- **Solution:** Query optimization + Redis caching layer for frequently accessed data
- **Pattern:** Cache-aside pattern with TTL-based invalidation
- **Result:** 40% reduction in average API response time

**3. Clean Code & Testing Culture**
- **Problem:** Inconsistent code quality across team; regressions in production
- **Solution:** Enforced unit testing requirements and code review standards
- **Pattern:** Test-driven development for new features; integration tests for critical paths
- **Result:** 50% reduction in production bugs; faster code review cycles

#### Sanitized Code Patterns

**Flask API with Structured Logging (Python)**
```python
import logging
from flask import Flask, request, jsonify
from functools import wraps

app = Flask(__name__)

# Structured logging with correlation IDs
class CorrelationIdFilter(logging.Filter):
    def filter(self, record):
        record.correlation_id = request.headers.get('X-Correlation-ID', 'unknown')
        return True

logger = logging.getLogger(__name__)
logger.addFilter(CorrelationIdFilter())

def require_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = request.headers.get('Authorization')
        if not token or not validate_token(token):
            logger.warning(f"Auth failed: {request.path}")
            return jsonify({"error": "Unauthorized"}), 401
        return f(*args, **kwargs)
    return decorated

@app.route('/api/v1/users/<user_id>', methods=['GET'])
@require_auth
def get_user(user_id):
    correlation_id = request.headers.get('X-Correlation-ID', 'unknown')
    logger.info(f"Fetching user", extra={
        "user_id": user_id,
        "correlation_id": correlation_id,
        "endpoint": "get_user"
    })

    try:
        user = UserService.get_by_id(user_id)
        return jsonify(user.to_dict()), 200
    except UserNotFoundError:
        logger.error(f"User not found: {user_id}")
        return jsonify({"error": "User not found"}), 404
    except Exception as e:
        logger.exception(f"Unexpected error fetching user: {user_id}")
        return jsonify({"error": "Internal server error"}), 500
```

**Database Query Optimization with Caching (Python)**
```python
import redis
from functools import lru_cache
from typing import Optional

class UserRepository:
    def __init__(self, db_session, redis_client: redis.Redis):
        self.db = db_session
        self.cache = redis_client
        self.cache_ttl = 300  # 5 minutes

    def get_user_by_id(self, user_id: str) -> Optional[User]:
        # Check cache first
        cache_key = f"user:{user_id}"
        cached = self.cache.get(cache_key)

        if cached:
            return User.from_json(cached)

        # Database query with optimized indexing
        user = self.db.query(User)\
            .filter(User.id == user_id)\
            .options(joinedload(User.profile))\
            .first()

        if user:
            # Populate cache
            self.cache.setex(
                cache_key, 
                self.cache_ttl, 
                user.to_json()
            )

        return user

    def invalidate_user_cache(self, user_id: str):
        # Call this on user update to prevent stale data
        self.cache.delete(f"user:{user_id}")
```

---

## Technical Case Studies

### Case Study 1: From 30-Minute to 5-Minute Incident Detection

**Background:** Finddle's production Flask services experienced intermittent failures that were not caught until users reported them.

**Problem Analysis:**
- Logs were unstructured text files, making pattern detection impossible
- No alerting on API error rates or latency spikes
- Mobile clients silently failed; no server-side visibility

**Solution Implemented:**
1. **Structured Logging:** JSON-formatted logs with correlation IDs, timestamps, and contextual metadata
2. **Metrics Collection:** Custom metrics for API latency (p50, p95, p99), error rates by endpoint, and database query performance
3. **Alerting Rules:** PagerDuty integration triggered on:
   - Error rate greater than 1% for 2 consecutive minutes
   - p95 latency greater than 500ms for 5 minutes
   - Database connection pool exhaustion

**Results:**
- Mean-time-to-detection: 30 minutes to 5 minutes
- Mean-time-to-resolution: 45 minutes to 15 minutes
- Customer-reported incidents: 80% reduction

**Lessons Learned:**
- "Good alerting is about signal-to-noise ratio. We started with too many alerts and tuned them based on actual incident patterns."
- "Correlation IDs between mobile and backend were the single most impactful change for debugging."

---

### Case Study 2: Building WebRTC Signaling for Mobile Networks

**Background:** SoloChat needed peer-to-peer video calling that worked on unreliable mobile networks in Nigeria.

**Problem Analysis:**
- WebRTC requires a signaling server to coordinate connections
- Mobile networks frequently switch between WiFi and cellular
- NAT traversal is unpredictable in emerging markets

**Solution Implemented:**
1. **Firebase as Signaling Server:** Used Firestore real-time updates for offer/answer exchange
2. **ICE Candidate Management:** Custom logic to prioritize TURN servers when direct connection fails
3. **Connection State Monitoring:** Automatic renegotiation when network changes detected

**Architecture:**
```
User A                    Firebase                    User B
  |                         |                         |
  |-- createOffer() -------->|                         |
  |                         |-- offer document ------>|-- setRemoteDescription()
  |                         |                         |-- createAnswer()
  |                         |<-- answer document -----|-- answer
  |-- setRemoteDescription()|                         |
  |                         |                         |
  |<-- ICE candidates ------|                         |
  |                         |-- ICE candidates ------>|
  |                         |                         |
  |========= P2P CONNECTION ESTABLISHED ================|
```

**Results:**
- Call completion rate: 70% to 95%
- Average connection time: 8 seconds to 4 seconds
- Works on 2G networks (with audio-only fallback)

**Lessons Learned:**
- "In emerging markets, assume the network is hostile. Design for failure at every step."
- "Firebase's real-time sync was perfect for signaling because it handles reconnection automatically."

---

## Public Projects

### ChatClone — Simplified Real-Time Chat
**Purpose:** Demonstrate SoloChat architecture patterns in a public repository  
**Tech:** Kotlin, Firebase, WebRTC  
**Status:** In development (target: 2 weekends)  
**Repository:** [github.com/favgabriel/chatclone](https://github.com/favgabriel/chatclone) *(placeholder)*

### UK Transit Tracker
**Purpose:** Location-based transit app demonstrating Google Maps API + public API integration  
**Tech:** Kotlin, Google Maps API, Transport API, Retrofit  
**Status:** Concept phase  
**Repository:** [github.com/favgabriel/uk-transit](https://github.com/favgabriel/uk-transit) *(placeholder)*

---

## Skills Matrix

| Skill | Production Experience | Confidence |
|-------|----------------------|------------|
| Python / Flask | 2+ years at Finddle | High |
| Kotlin / Android | 2+ years at SoloChat | High |
| Firebase (Full Platform) | 2+ years | High |
| WebRTC | 1+ year | High |
| AWS | 1+ year | Medium-High |
| MySQL | 2+ years | High |
| REST API Design | 3+ years | High |
| Unit/Integration Testing | 3+ years | High |
| Monitoring/Observability | 1+ year | Medium-High |
| Git | 4+ years | High |
| Arduino / IoT | 2+ years | Medium |

---

## Certifications

- Project Management Professional (PMP)
- Software Engineering
- Cloud Computing with AWS
- Data Analytics and Business Intelligence
- Introduction to Generative AI
- Health, Safety & Environment (HSE 1, 2 & 3)
- Oil & Gas Basic Drilling Operations
- Associate of Chartered Institute of Bankers

---

## Contact & Availability

- **Email:** [aighangbe@gmail.com](mailto:aighangbe@gmail.com)
- **LinkedIn:** [linkedin.com/in/favour-gabriel](https://linkedin.com/in/favour-gabriel)
- **Location:** Currently in Nigeria, **available to relocate to UK**
- **Visa:** Require Skilled Worker visa sponsorship
- **Availability:** Immediately available for interview

**I am actively seeking:**
- Mobile Developer (Android/Kotlin) roles
- Backend Software Engineer (Python/Flask/NodeJs) roles
- Full-Stack Engineer roles
- Platform/DevOps Engineer roles

---

> *"Production software is not about perfect code---it is about systems that fail gracefully, recover quickly, and teach you something when they break."*

---

*Last updated: May 2026*
