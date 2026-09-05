# Java 6-Week Onboarding: Daily Hands‑On Plan (5h/day)

> Purpose: Turn theory into practical project skills by building a production‑like Spring Boot blog with authentication, persistence, async processing, caching, CI/CD and deployment.

This document is a step‑by‑step daily handbook you can follow. Each day ≈5 hours: 1h reading, 3.5h coding/practice, 0.5h commit/diary.

If you want a Word (.docx) version, follow the "Export to Word" section at the end — you can convert this Markdown to .docx with pandoc or I can later add a .docx file to the repo on request.

---

## Quick Setup (do once)
- Install JDK 17+, set JAVA_HOME
- Install IntelliJ IDEA (Community/Ultimate)
- Install Maven or Gradle
- Install Docker & Docker Compose
- Install Git
- (Optional) Install Postman or httpie

Useful commands:
- git config --global user.name "Your Name"
- git config --global user.email "you@example.com"
- java -version
- mvn -v
- docker --version

Create local project folder and init git:

```bash
mkdir blog-project && cd blog-project
git init
echo "# Blog Project" > README.md
```

---

## Project choice
This handbook follows Project B: a blog system with authentication (JWT), articles, comments, caching and async notifications. If you'd rather do Todo (A) or Orders (C/D), tell me and I will adapt steps.

---

## Daily Schedule Template
- 1.0h: Read docs / watch a short tutorial / plan
- 3.5h: Implement feature / write tests / debug
- 0.5h: Commit, push, write short daily diary (WIP + blockers)

Push small PRs: aim for small independent PRs, self-review, then merge.

---

# Week 1 — Project Init & Basic CRUD (User + Article)

Day 1 — Init project & environment
- Read: Spring Boot Quickstart (https://spring.io/guides/gs/spring-boot/)
- Actions:
  - Create project with Spring Initializr (Maven): dependencies: Web, Data JPA, H2, Validation, DevTools, springdoc-openapi
  - mvn clean package
  - mvn spring-boot:run
  - Create branch `feat/init`, commit, push
- Deliverable: Running Spring Boot starter project

Day 2 — User entity & CRUD
- Read: Controller / Service / Repository pattern
- Actions:
  - Create packages: controller, service, repository, entity, dto, config
  - Implement User entity, UserRepository, UserService, UserController (basic CRUD)
  - Add JUnit tests (create + find)
- Deliverable: User CRUD endpoints + tests

Day 3 — Article entity & association
- Read: JPA associations (ManyToOne, OneToMany)
- Actions:
  - Implement Article entity (author relation), ArticleRepository, ArticleService, ArticleController
  - Add paging support (Pageable)
- Deliverable: Article CRUD + paging

Day 4 — DTO & Validation
- Read: Hibernate Validator, @Valid
- Actions:
  - Add DTO classes (CreateArticleRequest, ArticleDto)
  - Add validation annotations and global @ControllerAdvice for uniform errors
- Deliverable: Validated create/update endpoints with clear error responses

Day 5 — Swagger & Integration tests
- Read: springdoc-openapi usage
- Actions:
  - Add dependency springdoc-openapi-ui
  - Verify Swagger UI at /swagger-ui/index.html
  - Add @SpringBootTest integration tests (3 tests)
- Deliverable: Swagger + integration tests

**Week 1 Acceptance**
- Project starts, Swagger available
- User and Article CRUD works with tests
- README lists main APIs

---

# Week 2 — Real DB, Migrations, Tests

Day 6 — MySQL + Flyway
- Read: Flyway basics
- Actions:
  - Add MySQL driver; configure application-dev.yml for MySQL
  - Start MySQL: `docker run --name blog-mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=blog -p 3306:3306 -d mysql:8`
  - Add Flyway and create `resources/db/migration/V1__init.sql` with schema
- Deliverable: Flyway migration applies automatically

Day 7 — Testcontainers
- Read: Testcontainers usage for integration tests
- Actions:
  - Add Testcontainers dependency for MySQL
  - Convert integration tests to run with Testcontainers
- Deliverable: Integration tests run with container DB

Day 8 — Search & Repo query optimization
- Read: JPA query methods, @Query, fetch joins
- Actions:
  - Implement search by author/title/content with pagination
  - Avoid N+1 using fetch joins or @EntityGraph
- Deliverable: Search endpoints + tests

Day 9 — Transactions
- Read: @Transactional, propagation, rollback rules
- Actions:
  - Add transactions to write methods; simulate exceptions to test rollback
- Deliverable: Transactional behavior tested

Day 10 — Docs & profiles
- Actions:
  - Add application-dev.yml and application-prod.yml
  - Update README with DB setup steps and env variables
  - Tag release v0.1
- Deliverable: DB migration and profiles documented

**Week 2 Acceptance**
- App runs against MySQL
- Flyway migrations applied
- Integration tests pass using Testcontainers

---

# Week 3 — Security: Spring Security + JWT

Day 11 — Spring Security basics
- Read: filter chain, UserDetailsService
- Actions:
  - Add starter-security dependency
  - Create SecurityConfig: disable default form login; allow swagger and actuator endpoints
  - Implement UserDetailsService backed by UserRepository
- Deliverable: security skeleton

Day 12 — Registration & password encoding
- Read: BCryptPasswordEncoder
- Actions:
  - Implement registration endpoint; encode password with BCrypt
  - Return 400 for duplicate username
- Deliverable: Registration endpoint with tests

Day 13 — JWT implementation
- Read: JWT concepts
- Actions:
  - Add jjwt or nimbus dependency
  - Implement JwtProvider to sign/verify tokens
  - Add AuthenticationFilter to populate SecurityContext from token
  - Implement login endpoint
- Deliverable: Login endpoint returns JWT

Day 14 — Refresh tokens & role control
- Read: refresh token strategies
- Actions:
  - (Optional) Implement refresh token stored in DB
  - Add @PreAuthorize examples for role-based access
  - Add tests for 401/403 scenarios
- Deliverable: Role-protected endpoints

Day 15 — Secrets management & review
- Actions:
  - Move JWT secret to env var / GitHub Secret
  - Verify no secrets in repo
  - Update README for env configuration
- Deliverable: Secure configuration and docs

**Week 3 Acceptance**
- Register/login works; JWT-protected endpoints enforce auth
- Secrets not hardcoded

---

# Week 4 — Async tasks & Redis cache

Day 16 — Redis setup
- Read: Redis common usages
- Actions:
  - Start Redis: `docker run --name redis -p 6379:6379 -d redis:7`
  - Add spring-boot-starter-data-redis
  - Configure Redis connection
- Deliverable: Redis connected

Day 17 — Async task executor
- Read: @Async, ThreadPoolTaskExecutor
- Actions:
  - Enable @EnableAsync and define executor
  - Create async notification task triggered on article publish (simulate email via log)
- Deliverable: Async notification works

Day 18 — Cache with @Cacheable
- Read: cache eviction TTL and patterns
- Actions:
  - Use @Cacheable to cache popular articles list
  - Use @CacheEvict on article update/delete
- Deliverable: Caching works and invalidates on update

Day 19 — Message queue (optional)
- Read: RabbitMQ vs Kafka
- Actions:
  - If chosen, run RabbitMQ and use spring-boot-starter-amqp to publish/consume article events
- Deliverable: MQ-based async flow (if implemented)

Day 20 — Integrate & document
- Actions:
  - End-to-end verification: publish article -> cache updated/evicted -> async notification triggered
  - Update README with cache/async design notes
- Deliverable: Async + cache integrated

**Week 4 Acceptance**
- Async tasks run reliably
- Redis cache configured and tested

---

# Week 5 — Docker, CI/CD & Deployment

Day 21 — Dockerfile
- Read: Docker best practices for Java
- Actions:
  - Create Dockerfile:

```dockerfile
FROM eclipse-temurin:17-jre
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

  - Build image: `mvn -DskipTests package && docker build -t yourname/blog:0.1 .`
- Deliverable: Docker image builds

Day 22 — docker-compose
- Actions:
  - Create docker-compose.yml with db, redis, app
  - Run `docker-compose up --build`
- Deliverable: Local multi-service stack runs

Day 23 — GitHub Actions CI
- Actions:
  - Create `.github/workflows/ci.yml` (build/test/docker steps)
  - Add necessary GitHub Secrets (DOCKERHUB_USERNAME, DOCKERHUB_TOKEN, JWT_SECRET)

Example CI workflow snippet:

```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-java@v3
        with:
          java-version: '17'
      - run: mvn -B -DskipTests package
      - run: mvn test
      # optional: docker build/push steps
```

- Deliverable: CI runs tests on PRs

Day 24 — Deploy to cloud
- Actions:
  - Choose Render/Cloud Run/Heroku; push image to Docker Hub/GitHub Packages
  - Configure env vars and deploy
- Deliverable: Online service URL

Day 25 — Monitoring & actuator
- Actions:
  - Add spring-boot-starter-actuator and micrometer-registry-prometheus
  - Expose /actuator/health and /actuator/metrics
  - (Optional) run Prometheus & Grafana locally via docker-compose
- Deliverable: Health & basic metrics available

**Week 5 Acceptance**
- CI passes
- Service deployed and reachable
- Health and logs accessible

---

# Week 6 — Profiling, refactor & capstone

Day 26 — Load test & profiling
- Read: hey or wrk tool usage
- Actions:
  - Run: `hey -n 10000 -c 100 http://<service>/api/articles`
  - Use VisualVM or async-profiler to capture CPU/heap hotspots
- Deliverable: Profiling data

Day 27 — Fix hotspots
- Actions:
  - Resolve N+1 problems using fetch join or @EntityGraph
  - Add DB indexes for slow queries
  - Optimize serialization issues
- Deliverable: Performance improvements validated

Day 28 — Refactor & tests
- Actions:
  - Code cleanup: extract services, remove duplication, improve naming
  - Increase test coverage of critical paths to >=70%
  - Run static analysis (SpotBugs/Checkstyle)
- Deliverable: Cleaner codebase and improved tests

Day 29 — Documentation & demo
- Actions:
  - Create ARCHITECTURE.md (diagram + tech choices + tradeoffs)
  - Update README with run/test/deploy steps
  - Prepare 5-minute demo script or record screencast
- Deliverable: Documentation & demo

Day 30 — Release & mock interview
- Actions:
  - Create release tag `git tag -a v1.0 -m "capstone"`
  - Simulate interview: 10–15 minute project walkthrough + Q/A
- Deliverable: Release + interview readiness

**Week 6 Acceptance (Capstone)**
- Deployed app,
- Tests & CI in place,
- README/ARCHITECTURE/demos ready,
- You can explain core design decisions in 5 minutes

---

## PR Checklist (use on every PR)
- [ ] Small, focused change (prefer <300 lines)
- [ ] Unit tests added/updated
- [ ] Integration tests where applicable
- [ ] README/APIs updated if changed
- [ ] No secrets in repo
- [ ] CI passes

---

## Debugging & Troubleshooting Flow
1. Reproduce locally with same env
2. Inspect logs and stacktrace
3. Re-run failing test in IDE with breakpoints
4. For perf issues: collect profiler/heapdump -> analyze
5. For memory leaks: generate heap dump & analyze with Eclipse MAT

---

## Useful Templates
Docker Compose example (`docker-compose.yml`):

```yaml
version: '3'
services:
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: blog
    ports: ['3306:3306']
  redis:
    image: redis:7
    ports: ['6379:6379']
  app:
    image: yourname/blog:0.1
    depends_on: ['db','redis']
    ports: ['8080:8080']
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/blog
      SPRING_REDIS_HOST: redis
```

GitHub Actions CI snippet (`.github/workflows/ci.yml`):

```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
      - name: Build
        run: mvn -B -DskipTests package
      - name: Run tests
        run: mvn test
```

---

## Export to Word (.docx)
Option A (locally): convert this Markdown to .docx with pandoc:

```bash
pandoc docs/Java_6week_onboarding.md -o Java_6week_onboarding.docx
```

Option B (I can also add a .docx file to this repo on request). For now I will add this Markdown file to the repo. You can convert it locally or let me know if you want me to also commit a .docx.

---

## Next steps (pick one)
- I will commit this handbook to `docs/Java_6week_onboarding.md` in `bbyzch/bby_study` now. (Recommended)
- I can also create a starter GitHub repo skeleton (entities, controllers, Dockerfile, CI) to clone and start coding.
- Or I can immediately create a .docx version and commit it as `docs/Java_6week_onboarding.docx` — say the word and I will add it.

---

Good luck — follow the schedule day by day, push small PRs, and post daily short notes in the repo. If you want, I can now:
1) Commit this markdown into bbyzch/bby_study/docs/Java_6week_onboarding.md (I will do that now), or
2) Commit both markdown and docx (I need confirm before creating a binary .docx).

Which do you want me to commit now? I will proceed to push docs/Java_6week_onboarding.md into your repo.
