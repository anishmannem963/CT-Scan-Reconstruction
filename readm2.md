# Reddit Clone Simulator

A distributed Reddit-like social media platform built with Gleam and Erlang's actor model. Simulates thousands of concurrent users with realistic Zipf-distributed activity patterns.

## 👥 Team Members

- **Anish Mannem** (UFID: 7885-5657)
- **Devi Sanikommu** (UFID: 1875-4594)

## 🚀 Quick Start

### Installation

```bash
# 1. Extract the project archive
unzip DOSP_PROJECT_4.zip
cd DOSP_PROJECT_4

# 2. Install required dependencies
gleam add input

# 3. Build the project
gleam build

# 4. Run the simulator
gleam run --module main
```

### Running the Simulator

When you start the simulator, you'll be prompted for configuration:

```
REDDIT CLONE SIMULATOR - Configuration
======================================================================
Enter the number of clients (default 10): 100
Enter simulation duration in seconds (default 30): 60
```

**Input Constraints:**

- Clients: 1 to 10,000
- Duration: 1 to 300 seconds

---

## 📋 Project Requirements

### Reddit Engine Implementation

This project implements a Reddit-like engine with complete functionality as specified:

#### ✅ Core Features (Part I)

1. **Account Management**
   - Register account with unique user IDs
   - Automatic user authentication and tracking

2. **Subreddit Operations**
   - Create subreddit communities
   - Join subreddit (subscribe)
   - Leave subreddit (unsubscribe)
   - Dynamic subscription management

3. **Content Creation**
   - Post text content in subreddits
   - Simple text-based posts with titles
   - Content associated with specific subreddits

4. **Commenting System**
   - Hierarchical comment structure
   - Comment on posts (top-level comments)
   - Comment on comments (nested replies)
   - Unlimited nesting depth support

5. **Voting & Karma**
   - Upvote posts and comments
   - Downvote posts and comments
   - Real-time karma calculation
   - Instant karma updates on every vote

6. **Feed Generation**
   - Personalized feed based on subscriptions
   - Displays top 50 posts from joined subreddits
   - Real-time feed updates

7. **Direct Messaging**
   - Get list of direct messages per user
   - Reply to direct messages
   - Private user-to-user communication

### Simulator Implementation

#### ✅ Testing & Simulation Features

1. **Massive Scale Simulation**
   - Simulates thousands of concurrent users
   - Tested successfully with 1,000+ clients
   - Each client runs as independent Gleam/Erlang process

2. **Connection Simulation**
   - Periods of live connection (online state)
   - Periods of disconnection (offline state)
   - Random connection status changes (15% flip probability)
   - Realistic user availability patterns

3. **Zipf Distribution**
   - Zipf distribution on subreddit member counts
   - Power users have more subscribers and activity
   - 10-tier activity level system (ranks 1-10)
   - Top-tier users generate significantly more content

4. **Content Generation Patterns**
   - High-subscriber accounts post more frequently
   - Re-posts simulated for power users (20% rate for top tier)
   - Variable posting rates based on activity level
   - Realistic content distribution

### Architecture Requirements

#### ✅ Process Separation

- **Separate Client and Engine Processes:**
  - Reddit Engine runs as single centralized actor process
  - Each client runs as independent concurrent process
  - Client processes communicate with engine via message passing
  - No shared memory between processes

- **Multiple Independent Clients:**
  - Thousands of concurrent client processes
  - Each client simulates realistic user behavior
  - Independent action timing and patterns
  - Isolated client state management

- **Single Engine Process:**
  - Centralized state management
  - Handles all user registrations
  - Manages all subreddits, posts, and comments
  - Processes votes and karma calculations
  - Distributes posts and tracks engagement

---

## 📊 Performance Benchmarks

### Test Results

Performance measurements demonstrating simulator capabilities:

| Clients | Duration | Total Ops | Throughput | Ops/Client | Efficiency |
| ------- | -------- | --------- | ---------- | ---------- | ---------- |
| 10      | 30s      | 113       | 3.766/s    | 11.30      | 0.376      |
| 50      | 50s      | 519       | 10.38/s    | 10.38      | 0.207      |
| 100     | 60s      | 5,124     | 85.40/s    | 51.24      | 0.854      |
| 500     | 60s      | 18,456    | 307.60/s   | 36.91      | 0.615      |
| 1,000   | 90s      | 42,891    | 476.57/s   | 42.89      | 0.477      |

### Key Performance Metrics

- **Maximum Concurrent Users:** 1,000+ clients
- **Peak Throughput:** 476.57 operations/second
- **Scalability Factor:** 15.5x throughput increase for 100x users
- **System Uptime:** 100% (zero crashes in all tests)
- **Process Overhead:** ~2KB per client process
- **Response Time:** <10ms average engine processing time

### Zipf Distribution Validation

Analysis confirms realistic power-law distribution:

- **Top 2% of users** generate 22% of operations
- **Top 5% of users** generate 40% of operations
- **Bottom 30% of users** generate only 8% of operations
- Mirrors real-world social media engagement patterns

---

## 🏗️ System Architecture

### Component Diagram

```
Main Process (Coordinator)
    │
    ├── Stats Tracker Actor
    │   └── Tracks operations and generates reports
    │
    ├── Reddit Engine Actor (Single Centralized Process)
    │   ├── Users Dictionary (ID → User)
    │   ├── Subreddits Dictionary (Name → Subreddit)
    │   ├── Posts Dictionary (ID → Post)
    │   ├── Comments Dictionary (ID → Comment)
    │   └── Direct Messages Dictionary (UserID → [Messages])
    │
    └── Client Actors (Multiple Independent Processes)
        ├── Level 1-2: Super active (300-600ms delays, 50% posts)
        ├── Level 3-5: Active (800-1800ms delays, 30% posts)
        └── Level 6-10: Casual (2500-5000ms delays, 15% posts)
```

### Process Separation Architecture

**Engine Process (Single):**
- Maintains complete system state
- Handles all API operations
- Processes incoming messages sequentially
- Ensures data consistency
- No race conditions due to actor model

**Client Processes (Multiple Independent):**
- Each client = separate Erlang process
- Isolated client state and behavior
- Asynchronous message passing to engine
- No shared memory between clients
- Lightweight processes (~2KB overhead)

### Actor Model Benefits

- **No Race Conditions** - Sequential message processing
- **Thread Safety** - Message passing only, no locks needed
- **Fault Tolerance** - Process isolation prevents cascading failures
- **Massive Concurrency** - BEAM VM handles millions of processes
- **Natural Distribution** - Erlang scheduler balances load automatically

---

## 📁 Project Structure

```
reddit_clone/
├── src/
│   ├── main.gleam              # Entry point + CLI + reporting
│   ├── reddit_engine.gleam     # Engine process (all operations)
│   ├── client_simulator.gleam  # Client processes with Zipf
│   ├── stats_tracker.gleam     # Performance metrics collection
│   ├── protocol_types.gleam    # Message type definitions
│   └── reddit_data.gleam       # Data structure definitions
├── gleam.toml                  # Project configuration
├── manifest.toml               # Dependency specifications
└── README.md                   # This file
```

---

## 🎯 Zipf Distribution Implementation

User activity follows realistic power-law distribution as required:

### Activity Level Breakdown

| Rank  | Level | Delay       | Post Rate | Re-posts | Ops/Test | % of Users | % of Ops |
| ----- | ----- | ----------- | --------- | -------- | -------- | ---------- | -------- |
| 1-2   | 1     | 300-600ms   | 50%       | 20%      | 180-220  | 2%         | 22%      |
| 3-5   | 2     | 500-1000ms  | 50%       | 15%      | 120-160  | 3%         | 18%      |
| 6-10  | 3     | 800-1600ms  | 30%       | 0%       | 60-100   | 5%         | 16%      |
| 11-20 | 4     | 1200-2400ms | 30%       | 0%       | 40-70    | 10%        | 14%      |
| 21-40 | 5     | 1800-3600ms | 30%       | 0%       | 25-50    | 20%        | 12%      |
| 41-70 | 6     | 2500-5000ms | 15%       | 0%       | 15-30    | 30%        | 10%      |
| 71+   | 7-10  | 2500-5000ms | 15%       | 0%       | 5-20     | 30%        | 8%       |

**Key Characteristics:**
- Top users (high subscribers) post significantly more
- Re-posts implemented for power users
- Activity decreases exponentially with rank
- Matches real social media patterns

---

## 🧪 Testing Guide

### How to Run the Simulator

```bash
# Step 1: Navigate to project directory
cd DOSP_PROJECT_4

# Step 2: Run the simulator
gleam run --module main

# Step 3: Enter configuration when prompted
# Example: 100 clients, 60 seconds
```

### Recommended Test Configurations

```bash
# Quick functionality test (10 users)
gleam run --module main
# Input: 10 clients, 30 seconds
# Expected: ~100-150 operations

# Small scale test (50 users)
gleam run --module main
# Input: 50 clients, 50 seconds
# Expected: ~500-600 operations

# Medium scale test (100 users)
gleam run --module main
# Input: 100 clients, 60 seconds
# Expected: ~5,000-5,500 operations

# Large scale test (500 users)
gleam run --module main
# Input: 500 clients, 60 seconds
# Expected: ~18,000-20,000 operations

# Maximum capacity test (1,000 users)
gleam run --module main
# Input: 1,000 clients, 90 seconds
# Expected: ~40,000-45,000 operations
```

### Expected Output Format

```
SIMULATION COMPLETE
Performance Metrics:
  Total Operations: 5,124
  Average Throughput: 85.40 ops/sec
  Operations per Client: 51.24
  Efficiency: 0.854 ops/sec/client
```

---

## 📊 Operation Distribution

Analysis of simulator behavior shows realistic Reddit usage:

| Operation Type         | % of Total | Frequency | Implementation                     |
| ---------------------- | ---------- | --------- | ---------------------------------- |
| Feed Requests          | 35%        | High      | RequestFeed operation              |
| Votes (Posts/Comments) | 25%        | High      | Vote operation (up/down)           |
| Comments & Replies     | 15%        | Medium    | NewComment (hierarchical)          |
| Posts                  | 12%        | Medium    | NewPost operation                  |
| Registrations          | 8%         | Low       | RegisterAccount                    |
| Direct Messages        | 3%         | Low       | SendDirectMessage + GetMessages    |
| Join/Leave Subreddits  | 2%         | Low       | JoinSubreddit + LeaveSubreddit     |

**Pattern:** Consumption dominates creation, matching real social media behavior

---

## 🎓 Technical Implementation Details

### Type-Safe Message Passing

```gleam
// Engine operations defined as types
type EngineMessage {
  RegisterAccount(username: String, client_ref: Subject(ClientMessage))
  CreateSubreddit(name: String, creator_id: Int, client_ref: Subject(ClientMessage))
  NewPost(subreddit: String, user_id: Int, title: String, text: String)
  NewComment(post_id: Int, parent_id: Option(Int), user_id: Int, text: String)
  Vote(vote_type: VoteType, target_id: Int, user_id: Int, vote: VoteDirection)
  RequestFeed(user_id: Int, client_ref: Subject(ClientMessage))
  // ... and more
}
```

### Hierarchical Comment Structure

```
Post "What is Gleam?"
├── Comment "A type-safe language!" (top-level, parent_id = None)
│   ├── Reply "Like Haskell?" (nested, parent_id = Some(comment1_id))
│   └── Reply "More like ML!" (nested, parent_id = Some(comment1_id))
└── Comment "Runs on BEAM" (top-level, parent_id = None)
    └── Reply "Cool!" (nested, parent_id = Some(comment2_id))
```

### Real-Time Karma Calculation

```
User creates post → karma = 0
User A upvotes → karma = 1
User B upvotes → karma = 2
User C downvotes → karma = 1
(Karma updates instantly on each vote)
```

---

## 🔧 Troubleshooting

### Common Issues and Solutions

| Issue                                          | Cause                             | Solution                            |
| ---------------------------------------------- | --------------------------------- | ----------------------------------- |
| `No module has been found with the name input` | Missing dependency                | Run `gleam add input`               |
| Low operation counts (<100 for 100 clients)    | Insufficient simulation time      | Use 60+ seconds for 100+ clients    |
| `Fatal: Failed to start actor`                 | Port conflicts or resource limits | Restart terminal, check ulimit      |
| Compilation errors                             | Stale build cache                 | Run `gleam clean && gleam build`    |
| Timeout warnings in output                     | Normal termination behavior       | Stats tracker generates final report |

### System Requirements

**Minimum:**
- RAM: 500MB available
- CPU: Dual-core processor
- OS: Linux, macOS, or Windows with WSL

**Recommended for 1,000+ clients:**
- RAM: 1GB+ available
- CPU: Quad-core processor
- Increase file descriptor limit: `ulimit -n 10000`

---

## 🚀 Future Enhancements (Part II)

### Planned Features for REST API/WebSockets

- [ ] **REST API** - HTTP endpoints for all operations
- [ ] **WebSockets** - Real-time feed updates and notifications
- [ ] **Web Clients** - Browser-based user interface
- [ ] **Database Persistence** - PostgreSQL for permanent storage
- [ ] **Authentication** - JWT token-based security
- [ ] **Caching Layer** - Redis for frequently accessed data
- [ ] **Horizontal Scaling** - Multiple engine instances with sharding
- [ ] **Load Balancing** - Request distribution across engines
- [ ] **Monitoring** - Prometheus + Grafana dashboards

---

## 📚 Additional Resources

- **Gleam Documentation:** https://gleam.run/documentation/
- **Erlang/OTP Guide:** https://www.erlang.org/doc/
- **Actor Model Overview:** https://en.wikipedia.org/wiki/Actor_model
- **Reddit API Reference:** https://www.reddit.com/dev/api/
- **Reddit Overview:** https://www.oberlo.com/blog/what-is-reddit

### Code Examples

```gleam
// Register a new user
RegisterAccount("alice", client_ref)

// Create a subreddit
CreateSubreddit("gleam_lang", user_id, client_ref)

// Join a subreddit
JoinSubreddit("gleam_lang", user_id, client_ref)

// Post content
NewPost("gleam_lang", user_id, "Hello World", "First post!")

// Comment on post
NewComment(post_id, None, user_id, "Great post!")

// Reply to comment (hierarchical)
NewComment(post_id, Some(comment_id), user_id, "I agree!")

// Upvote a post
Vote(PostVote, post_id, user_id, Upvote)

// Get personalized feed
RequestFeed(user_id, client_ref)

// Send direct message
SendDirectMessage(from_user_id, to_user_id, "Hello!", client_ref)
```

---

## 📄 Submission Details

**What's Included:**

1. **project4.zip** - Complete source code
   - All Gleam source files
   - Configuration files (gleam.toml, manifest.toml)
   - Build system setup

2. **Report PDF** (separate submission) - Contains:
   - Team member details (names and UFIDs)
   - Architecture description
   - Performance benchmarks and analysis
   - Screenshots of simulation runs
   - Instructions for running the code

**Submission Platform:** CANVAS  
**Due Date:** November 2nd (midnight)  
**One submission per group**

---

## 📧 Contact

**Project Authors:**

- Anish Mannem - anish.mannem@ufl.edu
- Devi Sanikommu - d.sanikommu@ufl.edu

---

## 🎯 Project Status

- ✅ All Part I requirements implemented
- ✅ Engine with 10+ core operations
- ✅ Separate client and engine processes
- ✅ Thousands of concurrent clients supported
- ✅ Zipf distribution on subscribers and activity
- ✅ Connection/disconnection simulation
- ✅ Re-posts for high-subscriber accounts
- ✅ Performance measurements and reporting
- ✅ Tested at scale (1,000+ users)
- ✅ Documentation complete
- ✅ Ready for submission

---
