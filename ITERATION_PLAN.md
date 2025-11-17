# Implementation Roadmap - Iteration Planning

This document outlines the detailed implementation plan for each iteration of the Family Meal Planning Application.

## Overview

The project will be developed in **5 major iterations**, each building on the previous one. This allows for incremental delivery, continuous testing, and early feedback.

---

## Iteration 1: Design Phase ✅ COMPLETE

**Duration**: 1-2 days  
**Status**: ✅ Complete

### Objectives
- Define complete architecture
- Specify all component interfaces
- Document data model and relationships
- Plan project structure

### Deliverables
- ✅ DESIGN.md (comprehensive architecture)
- ✅ README.md (project overview)
- ✅ PROJECT_STRUCTURE.md (file organization)
- ✅ CONTRACTS.md (interface specifications)
- ✅ ITERATION_PLAN.md (this document)

### Key Decisions Made
- Hexagonal/layered architecture
- Python 3.11+ with type hints
- FastAPI for REST API
- SQLAlchemy 2.0+ for ORM
- SOLID principles throughout
- 80/20 repeat/new meal strategy
- Thumbs up/down rating system (>3 = highly rated)

---

## Iteration 2: Core Domain Implementation

**Duration**: 5-7 days  
**Status**: 🔜 Next

### Objectives
- Implement domain entities as SQLAlchemy models
- Create repository implementations
- Set up database infrastructure
- Implement domain services
- Establish testing patterns

### Tasks

#### 2.1: Project Setup (Day 1)
- [ ] Initialize Python project with Poetry/pip
- [ ] Set up pyproject.toml with dependencies
- [ ] Configure black, isort, mypy, ruff
- [ ] Set up pytest with coverage
- [ ] Create .env.example with DATABASE_URL
- [ ] Set up pre-commit hooks
- [ ] Create basic .gitignore

**Dependencies**:
```toml
[tool.poetry.dependencies]
python = "^3.11"
fastapi = "^0.100.0"
uvicorn = {extras = ["standard"], version = "^0.23.0"}
sqlalchemy = "^2.0.0"
alembic = "^1.11.0"
pydantic = "^2.0.0"
pydantic-settings = "^2.0.0"
python-dotenv = "^1.0.0"

[tool.poetry.dev-dependencies]
pytest = "^7.4.0"
pytest-cov = "^4.1.0"
pytest-asyncio = "^0.21.0"
black = "^23.7.0"
isort = "^5.12.0"
mypy = "^1.4.0"
ruff = "^0.0.280"
```

#### 2.2: Database Infrastructure (Day 1-2)
- [ ] Create app/infrastructure/database/connection.py
- [ ] Create app/infrastructure/database/session.py
- [ ] Implement database configuration (SQLite/PostgreSQL support)
- [ ] Set up Alembic for migrations
- [ ] Write initial migration: 001_initial_schema.py
- [ ] Create database initialization script

**Key Files**:
- `app/infrastructure/database/connection.py`: Engine creation
- `app/infrastructure/database/session.py`: Session factory
- `alembic.ini`: Alembic configuration
- `app/infrastructure/database/migrations/env.py`: Migration environment

#### 2.3: Domain Entities (Day 2-3)
Implement SQLAlchemy models for all entities:

- [ ] Create app/domain/entities/family_member.py
- [ ] Create app/domain/entities/ingredient.py
- [ ] Create app/domain/entities/recipe.py
- [ ] Create app/domain/entities/recipe_ingredient.py
- [ ] Create app/domain/entities/meal_plan_week.py
- [ ] Create app/domain/entities/meal.py
- [ ] Create app/domain/entities/meal_rating.py
- [ ] Create app/domain/entities/external_food_entry.py
- [ ] Create app/domain/entities/llm_generation.py

**Implementation Notes**:
- Use SQLAlchemy 2.0 declarative style
- Include all foreign keys and constraints
- Add indexes for common queries
- Use UUID for primary keys
- Include created_at/updated_at timestamps
- Follow CONTRACTS.md specifications exactly

#### 2.4: Domain Services (Day 3-4)
- [ ] Create app/domain/services/recipe_rating_service.py
  - Implement get_aggregate_rating()
  - Unit tests with mock repositories
- [ ] Create app/domain/services/recipe_variety_service.py
  - Implement get_usage_stats()
  - Unit tests with mock repositories
- [ ] Create app/domain/services/nutrition_calculation_service.py
  - Implement calculate_nutrition()
  - Unit tests with mock data

#### 2.5: Repository Implementations (Day 4-5)
- [ ] Create app/infrastructure/repositories/base_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_recipe_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_ingredient_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_meal_plan_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_meal_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_meal_rating_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_family_member_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_external_food_entry_repository.py
- [ ] Create app/infrastructure/repositories/sqlalchemy_llm_generation_repository.py

**Implementation Notes**:
- All repositories extend BaseRepository
- Implement protocol interfaces from CONTRACTS.md
- Use SQLAlchemy sessions correctly
- Handle transactions properly
- Add error handling for constraints

#### 2.6: Integration Tests (Day 5-6)
- [ ] Set up test database (SQLite in-memory)
- [ ] Create test fixtures and factories
- [ ] Write integration tests for each repository
- [ ] Test foreign key constraints
- [ ] Test cascade deletes
- [ ] Test unique constraints
- [ ] Test queries with filters and pagination

#### 2.7: Configuration & DI (Day 6-7)
- [ ] Create app/infrastructure/config/settings.py
- [ ] Create app/infrastructure/config/dependency_container.py
- [ ] Implement environment-based configuration
- [ ] Set up dependency injection for repositories
- [ ] Create database seed script

### Success Criteria
- ✅ All domain entities implemented with proper relationships
- ✅ All repositories pass integration tests
- ✅ Database migrations work correctly
- ✅ Code coverage > 80% for domain and repository layers
- ✅ All linters pass (black, isort, mypy, ruff)
- ✅ Can create/retrieve/update/delete all entities via repositories

### Deliverables
- Functional database layer
- Complete repository implementations
- Domain services with tests
- Database migrations
- Seed data script
- ~60-80 test cases

---

## Iteration 3: Application Layer & Basic API

**Duration**: 5-7 days  
**Status**: 📅 Planned

### Objectives
- Implement use cases
- Wire up dependency injection
- Create basic FastAPI application
- Implement core API endpoints
- Add comprehensive error handling

### Tasks

#### 3.1: Application Layer Setup (Day 1)
- [ ] Create app/application/interfaces/ with protocols
- [ ] Create app/application/dtos/ with DTOs
- [ ] Set up use case base patterns

#### 3.2: Use Case Implementations (Day 1-4)
- [ ] Implement CreateRecipeUseCase
- [ ] Implement ListRecipesUseCase
- [ ] Implement GetRecipeUseCase
- [ ] Implement UpdateRecipeUseCase
- [ ] Implement DeleteRecipeUseCase
- [ ] Implement GenerateMealPlanUseCase (without LLM)
- [ ] Implement GetMealPlanWeekUseCase
- [ ] Implement UpdateMealUseCase
- [ ] Implement RateMealUseCase
- [ ] Unit tests for each use case

**Focus**: Implement core meal planning logic:
- Query highly-rated recipes
- Check recipe variety
- Allocate 80% to repeats
- Allocate 20% to less-used recipes (no LLM yet)
- Handle locked meals

#### 3.3: FastAPI Application (Day 4-6)
- [ ] Create app/api/main.py
- [ ] Set up CORS middleware
- [ ] Add error handling middleware
- [ ] Implement app/api/routers/recipes.py
- [ ] Implement app/api/routers/meal_plans.py
- [ ] Implement app/api/routers/meals.py
- [ ] Implement app/api/routers/ratings.py
- [ ] Implement app/api/routers/health.py
- [ ] Create Pydantic request/response models
- [ ] Wire up dependency injection

#### 3.4: API Tests (Day 6-7)
- [ ] E2E tests for recipe endpoints
- [ ] E2E tests for meal plan endpoints
- [ ] E2E tests for rating endpoints
- [ ] Test error responses
- [ ] Test validation errors

### Success Criteria
- ✅ All use cases implemented and tested
- ✅ Basic API functional with all core endpoints
- ✅ Can create recipes via API
- ✅ Can generate meal plans via API
- ✅ Can rate meals via API
- ✅ Error handling works correctly
- ✅ Code coverage > 80%

### Deliverables
- Complete application layer
- Functional REST API
- OpenAPI documentation (auto-generated)
- ~80-100 test cases
- API can be run with `uvicorn`

---

## Iteration 4: External Integrations

**Duration**: 5-7 days  
**Status**: 📅 Planned

### Objectives
- Implement MyFitnessPal client
- Implement LLM integration
- Add import functionality
- Enhance meal planning with LLM
- Improve error handling and resilience

### Tasks

#### 4.1: MyFitnessPal Integration (Day 1-2)
- [ ] Create app/infrastructure/myfitnesspal/csv_client.py
- [ ] Create app/infrastructure/myfitnesspal/json_client.py
- [ ] Create app/infrastructure/myfitnesspal/mock_client.py
- [ ] Implement CSV parser
- [ ] Implement JSON parser
- [ ] Create fuzzy recipe matching logic
- [ ] Unit tests for parsers

#### 4.2: Import Use Case (Day 2-3)
- [ ] Implement ImportMyFitnessPalDataUseCase
- [ ] Add import API endpoint
- [ ] Handle file uploads
- [ ] Test with sample MFP data
- [ ] Integration tests

#### 4.3: LLM Integration (Day 3-5)
- [ ] Create app/infrastructure/llm/base_generator.py
- [ ] Create app/infrastructure/llm/openai_generator.py
- [ ] Create app/infrastructure/llm/anthropic_generator.py
- [ ] Create app/infrastructure/llm/noop_generator.py
- [ ] Design prompt templates
- [ ] Implement recipe generation
- [ ] Implement recipe adaptation
- [ ] Store prompts/responses in LLMGeneration table
- [ ] Unit tests with mocked LLM responses

#### 4.4: LLM-Enhanced Meal Planning (Day 5-6)
- [ ] Update GenerateMealPlanUseCase to use LLM
- [ ] Implement GenerateLLMRecipeUseCase
- [ ] Add LLM recipe generation endpoint
- [ ] Test with real LLM API (OpenAI/Anthropic)
- [ ] Test fallback when LLM unavailable
- [ ] Integration tests

#### 4.5: Error Handling & Resilience (Day 6-7)
- [ ] Add retry logic for LLM calls
- [ ] Improve error messages
- [ ] Add logging throughout
- [ ] Test failure scenarios
- [ ] Document external service configuration

### Success Criteria
- ✅ Can import MyFitnessPal CSV data
- ✅ Can generate recipes with LLM
- ✅ Meal planning uses LLM for 20% new meals
- ✅ System works without LLM (graceful fallback)
- ✅ All external calls have error handling
- ✅ Code coverage > 80%

### Deliverables
- MyFitnessPal import functionality
- LLM recipe generation
- Enhanced meal planning
- ~60-80 additional test cases

---

## Iteration 5: Polish & Deployment

**Duration**: 5-7 days  
**Status**: 📅 Planned

### Objectives
- Improve API documentation
- Add CLI interface (optional)
- Create Docker setup
- Set up CI/CD
- Security hardening
- Performance optimization
- User acceptance testing

### Tasks

#### 5.1: API Documentation (Day 1)
- [ ] Enhance OpenAPI descriptions
- [ ] Add examples to all endpoints
- [ ] Create docs/api_documentation.md
- [ ] Add API usage examples

#### 5.2: CLI Interface (Day 1-2, Optional)
- [ ] Create app/cli/main.py
- [ ] Add recipe management commands
- [ ] Add meal plan generation commands
- [ ] Add import commands
- [ ] CLI tests

#### 5.3: Docker & Deployment (Day 2-3)
- [ ] Create production Dockerfile
- [ ] Create docker-compose.yml
- [ ] Add PostgreSQL container
- [ ] Test containerized deployment
- [ ] Create deployment documentation

#### 5.4: CI/CD Pipeline (Day 3-4)
- [ ] Create .github/workflows/ci.yml
  - Linting job
  - Testing job (matrix: Python 3.11, 3.12)
  - Build job
  - Security scanning
- [ ] Create .github/workflows/release.yml
  - Build and tag Docker image
  - Create GitHub release
  - Push to container registry
- [ ] Test CI/CD pipeline

#### 5.5: Security & Performance (Day 4-5)
- [ ] Run bandit security scan
- [ ] Run safety dependency scan
- [ ] Add rate limiting
- [ ] Add request validation
- [ ] Optimize database queries
- [ ] Add database indexes
- [ ] Performance testing

#### 5.6: UAT & Documentation (Day 5-7)
- [ ] User acceptance testing
- [ ] Bug fixes
- [ ] Complete README with setup instructions
- [ ] Add deployment guide
- [ ] Add troubleshooting guide
- [ ] Record demo video

### Success Criteria
- ✅ Docker deployment works smoothly
- ✅ CI/CD pipeline passes all checks
- ✅ Security scans show no critical issues
- ✅ Performance meets targets (< 500ms API response)
- ✅ Documentation is complete and accurate
- ✅ UAT feedback is positive

### Deliverables
- Production-ready Docker image
- CI/CD pipeline
- Complete documentation
- Demo/tutorial materials

---

## Post-MVP Enhancements

After completing the 5 core iterations, consider these enhancements:

### Phase 2 Features
- [ ] Multi-tenant support (multiple families)
- [ ] Shopping list generation from meal plans
- [ ] Nutrition goal tracking
- [ ] Recipe scaling (adjust servings)
- [ ] Leftover tracking

### Phase 3 Features
- [ ] Web frontend (React/Vue)
- [ ] Mobile apps (React Native)
- [ ] Desktop app (Electron/Tauri)
- [ ] Recipe sharing between users
- [ ] Social features (comments, likes)

### Phase 4 Features
- [ ] Integration with grocery delivery services
- [ ] Smart pantry inventory
- [ ] Voice assistant integration (Alexa/Google)
- [ ] Meal prep batch cooking support
- [ ] Advanced nutrition analysis

---

## Development Best Practices

Throughout all iterations:

### Code Quality
- ✅ Run linters before every commit (pre-commit hooks)
- ✅ Maintain > 80% code coverage
- ✅ Write docstrings for all public methods
- ✅ Follow type hints consistently
- ✅ Keep functions small and focused

### Testing Strategy
- **Unit Tests**: Fast, isolated, mocked dependencies
- **Integration Tests**: Real database, test transactions
- **E2E Tests**: Full API requests, test user workflows
- **Test Coverage**: Aim for 85%+ overall

### Git Workflow
- Create feature branches for each task
- Write descriptive commit messages
- Keep commits atomic and logical
- Squash related commits before merging
- Update CHANGELOG.md for each iteration

### Documentation
- Keep README.md updated
- Document API changes immediately
- Add inline comments for complex logic
- Update DESIGN.md if architecture changes

---

## Tracking Progress

Use this checklist to track iteration progress:

### Iteration 2: Core Domain
- [ ] Project setup complete
- [ ] Database infrastructure working
- [ ] All entities implemented
- [ ] All domain services implemented
- [ ] All repositories implemented
- [ ] Integration tests pass
- [ ] Code coverage > 80%
- [ ] All linters pass

### Iteration 3: Application Layer
- [ ] All use cases implemented
- [ ] FastAPI application running
- [ ] All API endpoints working
- [ ] E2E tests pass
- [ ] OpenAPI docs generated
- [ ] Code coverage > 80%

### Iteration 4: Integrations
- [ ] MyFitnessPal import working
- [ ] LLM integration working
- [ ] Enhanced meal planning
- [ ] Graceful LLM fallback
- [ ] All tests pass
- [ ] Code coverage > 80%

### Iteration 5: Deployment
- [ ] Docker deployment working
- [ ] CI/CD pipeline passing
- [ ] Security scans clean
- [ ] Performance acceptable
- [ ] Documentation complete
- [ ] UAT complete

---

## Timeline Summary

| Iteration | Duration | Focus | Key Deliverables |
|-----------|----------|-------|------------------|
| 1: Design | 1-2 days | Architecture & Planning | Design docs, contracts |
| 2: Core Domain | 5-7 days | Entities & Repositories | Database layer, domain services |
| 3: Application | 5-7 days | Use Cases & API | REST API, core endpoints |
| 4: Integrations | 5-7 days | External Services | MFP import, LLM generation |
| 5: Deployment | 5-7 days | Polish & Ship | Docker, CI/CD, docs |

**Total Estimated Time**: 22-30 days (4-6 weeks)

---

## Success Metrics

At the end of all iterations:

### Technical Metrics
- ✅ 85%+ code coverage
- ✅ 0 critical security vulnerabilities
- ✅ < 500ms average API response time
- ✅ All CI/CD checks passing
- ✅ 100% type coverage (mypy strict mode)

### Functional Metrics
- ✅ Can create and manage recipes
- ✅ Can generate weekly meal plans
- ✅ Can rate meals and track preferences
- ✅ Can import MyFitnessPal data
- ✅ Can generate new recipes with LLM
- ✅ System works offline (without LLM)

### Quality Metrics
- ✅ Clean code (linters pass)
- ✅ Well-documented (README, API docs)
- ✅ Easy to deploy (Docker)
- ✅ Easy to extend (SOLID principles)
- ✅ Easy to test (high coverage)

---

**This roadmap provides a clear path from design to deployment. Each iteration builds on the previous one, allowing for early feedback and course correction.**
