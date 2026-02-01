# Semester Plan: Backend Engineer (Industry-Ready)

## 📘 Semester Goal (non-negotiable)

By **May end**, you must have:

- 1 **production-grade backend service**
    
- Clear **system design stories**
    
- Resume ready for **₹12–20 LPA** roles
    
- Interviewing through **June → July**
## SEM 1: Backend Foundations (Weeks 1–4)

**Goal:** Stop being “API dev”, become backend-thinking engineer.

### Python (deep, not tutorial)

- Async vs sync (when async helps, when it doesn’t)
    
- Threading vs multiprocessing
    
- GIL reality
    
- Memory + object lifecycle
    
- Profiling (`cProfile`, `line_profiler`)
    

👉 Output: explain **why** something is slow, not guess.

---

### FastAPI (real usage)

- Dependency injection (properly)
    
- Background tasks vs Celery/RQ
    
- Request lifecycle
    
- Middleware (auth, logging, rate limit)
    
- Pydantic v2 internals
    

👉 Build:

- Auth (JWT)
    
- Rate-limited public endpoint
    
- Background worker
    

---

### Postgres (must-know)

- Indexes (btree, composite, partial)
    
- Query planning (`EXPLAIN ANALYZE`)
    
- Transactions & isolation
    
- Migrations (Alembic)
    
- Pagination (keyset vs offset)
    

👉 Build:

- Tables with **correct indexes**
    
- Show latency improvement via indexes
    

---

## SEM 2: Production Backend (Weeks 5–8)

**Goal:** Think like someone on call.

### Redis

- Cache vs datastore
    
- TTL strategies
    
- Idempotency keys
    
- Rate limiting patterns
    
- Pub/Sub basics
    

---

### Docker

- Multi-stage builds
    
- Env config
    
- Docker Compose (API + DB + Redis)
    
- Image size & cold start
    

---

### Reliability

- Logging (structured)
    
- Metrics (latency, error rate)
    
- Retry + backoff
    
- Circuit breakers
    
- Graceful shutdowns
    

👉 Build:

- Service that **doesn’t break under failure**
    
- Simulate Redis/DB failure
    

---

## SEM 3: System Design + Scale (Weeks 9–12)

**Goal:** Be interview-credible.

### System Design (backend focus)

- API design
    
- Stateless services
    
- Horizontal scaling
    
- Load balancers
    
- Queues vs streams
    
- Consistency tradeoffs
    

Practice designing:

- Webhook system
    
- Rate-limited public API
    
- ML inference service (preview of next step)
    

---

### Cloud Basics (only what matters)

Pick **one**: AWS or GCP.

Learn:

- VM vs container
    
- Object storage
    
- Managed DB
    
- Secrets
    
- Basic networking
    

👉 Deploy your service. Public URL. No excuses.

---

## SEM 4: Anchor Project + Interviews (Weeks 13–16)

**Goal:** Convert skills → offer.

### Anchor Project (MANDATORY)

One backend service with:

- Auth
    
- Postgres + Redis
    
- Async endpoints
    
- Background jobs
    
- Dockerized
    
- Deployed
    
- README with architecture diagram
    

Optional ML add-on (only if time permits):

- Simple sklearn model
    
- Batch training
    
- Inference API
    

---

### Resume + Interview Prep

- Rewrite resume around **impact**
    
- System design answers ready
    
- 50–70 DSA problems (only basics: arrays, hashmaps, strings, queues)
    

---

## Timeline Reality Check

- **May end** → project + resume done
    
- **June** → applications + interviews
    
- **July** → role switch (realistic if consistent)