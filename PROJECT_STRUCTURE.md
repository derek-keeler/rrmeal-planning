# Project Structure - Detailed Breakdown

This document provides a detailed breakdown of the planned project structure for the Family Meal Planning Application.

## Directory Structure

```
rrmeal-planning/
│
├── README.md                      # Project overview and getting started
├── DESIGN.md                      # Comprehensive architecture design
├── PROJECT_STRUCTURE.md           # This file - detailed structure guide
├── LICENSE                        # Project license
├── .gitignore                     # Git ignore patterns
├── .env.example                   # Example environment configuration
│
├── pyproject.toml                 # Python project metadata (Poetry)
├── requirements.txt               # Alternative: pip requirements
├── setup.py                       # Alternative: setuptools config
│
├── Dockerfile                     # Container image definition
├── docker-compose.yml             # Multi-container orchestration
├── alembic.ini                    # Database migration config
│
├── .github/
│   └── workflows/
│       ├── ci.yml                 # Continuous integration pipeline
│       └── release.yml            # Release automation pipeline
│
├── app/                           # Main application code
│   ├── __init__.py
│   │
│   ├── domain/                    # Domain layer (business logic)
│   │   ├── __init__.py
│   │   │
│   │   ├── entities/              # Domain entities
│   │   │   ├── __init__.py
│   │   │   ├── family_member.py   # FamilyMember entity
│   │   │   ├── recipe.py          # Recipe entity
│   │   │   ├── ingredient.py      # Ingredient entity
│   │   │   ├── recipe_ingredient.py # RecipeIngredient junction
│   │   │   ├── meal.py            # Meal entity
│   │   │   ├── meal_plan_week.py  # MealPlanWeek entity
│   │   │   ├── meal_rating.py     # MealRating entity
│   │   │   ├── external_food_entry.py # ExternalFoodEntry entity
│   │   │   └── llm_generation.py  # LLMGeneration entity
│   │   │
│   │   ├── value_objects/         # Immutable value objects
│   │   │   ├── __init__.py
│   │   │   ├── nutrition_info.py  # Nutrition data value object
│   │   │   ├── date_range.py      # Date range value object
│   │   │   └── quantity.py        # Quantity + unit value object
│   │   │
│   │   └── services/              # Domain services (pure logic)
│   │       ├── __init__.py
│   │       ├── recipe_rating_service.py      # Aggregate ratings
│   │       ├── recipe_variety_service.py     # Track recipe usage
│   │       └── nutrition_calculation_service.py # Nutrition math
│   │
│   ├── application/               # Application layer (use cases)
│   │   ├── __init__.py
│   │   │
│   │   ├── use_cases/             # Use case implementations
│   │   │   ├── __init__.py
│   │   │   ├── create_recipe_use_case.py
│   │   │   ├── update_recipe_use_case.py
│   │   │   ├── delete_recipe_use_case.py
│   │   │   ├── list_recipes_use_case.py
│   │   │   ├── get_recipe_use_case.py
│   │   │   ├── generate_meal_plan_use_case.py
│   │   │   ├── get_meal_plan_week_use_case.py
│   │   │   ├── regenerate_meal_plan_use_case.py
│   │   │   ├── update_meal_use_case.py
│   │   │   ├── rate_meal_use_case.py
│   │   │   ├── import_myfitnesspal_use_case.py
│   │   │   ├── generate_llm_recipe_use_case.py
│   │   │   ├── create_family_member_use_case.py
│   │   │   └── create_ingredient_use_case.py
│   │   │
│   │   ├── dtos/                  # Data Transfer Objects
│   │   │   ├── __init__.py
│   │   │   ├── recipe_dto.py      # Recipe DTOs (input/output)
│   │   │   ├── meal_plan_dto.py   # Meal plan DTOs
│   │   │   ├── meal_dto.py        # Meal DTOs
│   │   │   ├── rating_dto.py      # Rating DTOs
│   │   │   ├── import_dto.py      # Import result DTOs
│   │   │   └── common_dto.py      # Shared DTOs (pagination, etc.)
│   │   │
│   │   └── interfaces/            # Interface definitions (protocols)
│   │       ├── __init__.py
│   │       ├── repositories.py    # Repository protocols
│   │       ├── external_clients.py # External client protocols
│   │       └── services.py        # Service protocols
│   │
│   ├── infrastructure/            # Infrastructure layer (external concerns)
│   │   ├── __init__.py
│   │   │
│   │   ├── database/              # Database implementation
│   │   │   ├── __init__.py
│   │   │   ├── connection.py      # DB connection management
│   │   │   ├── session.py         # Session factory
│   │   │   ├── models.py          # SQLAlchemy models
│   │   │   └── migrations/        # Alembic migrations
│   │   │       ├── env.py
│   │   │       ├── script.py.mako
│   │   │       └── versions/      # Migration versions
│   │   │           └── 001_initial_schema.py
│   │   │
│   │   ├── repositories/          # Repository implementations
│   │   │   ├── __init__.py
│   │   │   ├── base_repository.py # Base repository class
│   │   │   ├── sqlalchemy_recipe_repository.py
│   │   │   ├── sqlalchemy_ingredient_repository.py
│   │   │   ├── sqlalchemy_meal_plan_repository.py
│   │   │   ├── sqlalchemy_meal_repository.py
│   │   │   ├── sqlalchemy_meal_rating_repository.py
│   │   │   ├── sqlalchemy_family_member_repository.py
│   │   │   ├── sqlalchemy_external_food_entry_repository.py
│   │   │   └── sqlalchemy_llm_generation_repository.py
│   │   │
│   │   ├── myfitnesspal/          # MyFitnessPal integration
│   │   │   ├── __init__.py
│   │   │   ├── base_client.py     # Base client interface
│   │   │   ├── csv_client.py      # CSV parser implementation
│   │   │   ├── json_client.py     # JSON parser implementation
│   │   │   ├── mock_client.py     # Mock for testing
│   │   │   └── parsers/           # Data parsers
│   │   │       ├── __init__.py
│   │   │       ├── csv_parser.py
│   │   │       └── json_parser.py
│   │   │
│   │   ├── llm/                   # LLM integration
│   │   │   ├── __init__.py
│   │   │   ├── base_generator.py  # Base generator interface
│   │   │   ├── openai_generator.py     # OpenAI implementation
│   │   │   ├── anthropic_generator.py  # Anthropic implementation
│   │   │   ├── noop_generator.py       # No-op fallback
│   │   │   └── prompts/           # Prompt templates
│   │   │       ├── __init__.py
│   │   │       ├── recipe_generation.py
│   │   │       └── recipe_adaptation.py
│   │   │
│   │   └── config/                # Configuration management
│   │       ├── __init__.py
│   │       ├── settings.py        # Pydantic settings model
│   │       ├── dependency_container.py # DI container
│   │       └── logging_config.py  # Logging setup
│   │
│   ├── api/                       # API layer (HTTP interface)
│   │   ├── __init__.py
│   │   ├── main.py                # FastAPI app initialization
│   │   │
│   │   ├── routers/               # API route handlers
│   │   │   ├── __init__.py
│   │   │   ├── recipes.py         # Recipe endpoints
│   │   │   ├── meal_plans.py      # Meal plan endpoints
│   │   │   ├── meals.py           # Meal endpoints
│   │   │   ├── ratings.py         # Rating endpoints
│   │   │   ├── family_members.py  # Family member endpoints
│   │   │   ├── ingredients.py     # Ingredient endpoints
│   │   │   ├── imports.py         # Import endpoints
│   │   │   └── health.py          # Health check endpoints
│   │   │
│   │   ├── models/                # Pydantic models for API
│   │   │   ├── __init__.py
│   │   │   ├── requests/          # Request models
│   │   │   │   ├── __init__.py
│   │   │   │   ├── recipe_requests.py
│   │   │   │   ├── meal_plan_requests.py
│   │   │   │   ├── meal_requests.py
│   │   │   │   ├── rating_requests.py
│   │   │   │   └── import_requests.py
│   │   │   │
│   │   │   └── responses/         # Response models
│   │   │       ├── __init__.py
│   │   │       ├── recipe_responses.py
│   │   │       ├── meal_plan_responses.py
│   │   │       ├── meal_responses.py
│   │   │       ├── rating_responses.py
│   │   │       ├── import_responses.py
│   │   │       └── common_responses.py
│   │   │
│   │   ├── middleware/            # API middleware
│   │   │   ├── __init__.py
│   │   │   ├── error_handler.py   # Global error handling
│   │   │   ├── logging_middleware.py # Request/response logging
│   │   │   └── cors_middleware.py # CORS configuration
│   │   │
│   │   └── dependencies.py        # FastAPI dependency injection
│   │
│   └── cli/                       # CLI interface (optional)
│       ├── __init__.py
│       ├── main.py                # CLI entry point
│       └── commands/              # CLI commands
│           ├── __init__.py
│           ├── recipe_commands.py
│           ├── meal_plan_commands.py
│           └── import_commands.py
│
├── tests/                         # Test suite
│   ├── __init__.py
│   ├── conftest.py                # Pytest configuration and fixtures
│   │
│   ├── unit/                      # Unit tests (fast, isolated)
│   │   ├── __init__.py
│   │   │
│   │   ├── domain/                # Domain layer tests
│   │   │   ├── __init__.py
│   │   │   ├── entities/
│   │   │   │   ├── test_recipe.py
│   │   │   │   ├── test_meal.py
│   │   │   │   └── test_meal_plan_week.py
│   │   │   │
│   │   │   └── services/
│   │   │       ├── test_recipe_rating_service.py
│   │   │       ├── test_recipe_variety_service.py
│   │   │       └── test_nutrition_calculation_service.py
│   │   │
│   │   ├── application/           # Application layer tests
│   │   │   ├── __init__.py
│   │   │   ├── test_create_recipe_use_case.py
│   │   │   ├── test_generate_meal_plan_use_case.py
│   │   │   ├── test_rate_meal_use_case.py
│   │   │   └── test_import_myfitnesspal_use_case.py
│   │   │
│   │   └── infrastructure/        # Infrastructure unit tests
│   │       ├── __init__.py
│   │       ├── test_myfitnesspal_csv_client.py
│   │       ├── test_myfitnesspal_json_client.py
│   │       ├── test_openai_generator.py
│   │       └── test_anthropic_generator.py
│   │
│   ├── integration/               # Integration tests (DB, external APIs)
│   │   ├── __init__.py
│   │   ├── test_recipe_repository.py
│   │   ├── test_meal_plan_repository.py
│   │   ├── test_meal_repository.py
│   │   ├── test_database_connection.py
│   │   └── test_use_case_integration.py
│   │
│   ├── e2e/                       # End-to-end tests (full API)
│   │   ├── __init__.py
│   │   ├── test_recipe_endpoints.py
│   │   ├── test_meal_plan_endpoints.py
│   │   ├── test_rating_endpoints.py
│   │   └── test_import_endpoints.py
│   │
│   └── fixtures/                  # Test data and fixtures
│       ├── __init__.py
│       ├── recipes.json           # Sample recipe data
│       ├── myfitnesspal_export.csv # Sample MFP data
│       └── factory.py             # Test data factories
│
├── docs/                          # Documentation
│   ├── api/                       # API documentation
│   │   ├── openapi.yaml           # OpenAPI spec (auto-generated)
│   │   └── endpoints.md           # Endpoint descriptions
│   │
│   ├── architecture/              # Architecture docs
│   │   ├── diagrams/
│   │   │   ├── architecture.png
│   │   │   ├── entity_relationships.png
│   │   │   └── workflows.png
│   │   │
│   │   └── decisions/             # Architecture Decision Records
│   │       ├── 001_layered_architecture.md
│   │       ├── 002_sqlalchemy_orm.md
│   │       ├── 003_fastapi_framework.md
│   │       └── 004_llm_integration.md
│   │
│   ├── deployment/                # Deployment guides
│   │   ├── docker_deployment.md
│   │   ├── kubernetes_deployment.md
│   │   └── configuration_guide.md
│   │
│   └── development/               # Development guides
│       ├── getting_started.md
│       ├── contributing.md
│       ├── testing_guide.md
│       └── code_style_guide.md
│
└── scripts/                       # Utility scripts
    ├── seed_database.py           # Populate DB with sample data
    ├── export_data.py             # Export data for backup
    ├── run_migrations.sh          # Run Alembic migrations
    ├── generate_test_data.py      # Generate test fixtures
    └── check_code_quality.sh      # Run all linters and checks
```

## Key File Descriptions

### Root Level

- **README.md**: Project overview, quick start guide
- **DESIGN.md**: Comprehensive architecture and design specification
- **pyproject.toml**: Python project configuration (Poetry)
- **Dockerfile**: Multi-stage Docker build for production
- **docker-compose.yml**: Local development environment setup
- **alembic.ini**: Database migration tool configuration

### Application Structure

#### Domain Layer (`app/domain/`)
Contains pure business logic with no external dependencies.

- **entities/**: Core business objects (Recipe, Meal, etc.)
- **value_objects/**: Immutable value types (NutritionInfo, DateRange)
- **services/**: Domain services for complex business rules

#### Application Layer (`app/application/`)
Orchestrates business workflows and use cases.

- **use_cases/**: Each use case is a single class with `execute()` method
- **dtos/**: Data Transfer Objects for inter-layer communication
- **interfaces/**: Protocol definitions for dependency injection

#### Infrastructure Layer (`app/infrastructure/`)
Implements external concerns and integrations.

- **database/**: SQLAlchemy models, connections, migrations
- **repositories/**: Concrete implementations of repository interfaces
- **myfitnesspal/**: MyFitnessPal data import clients
- **llm/**: LLM provider integrations (OpenAI, Anthropic)
- **config/**: Configuration management and dependency container

#### API Layer (`app/api/`)
HTTP interface for external clients.

- **routers/**: FastAPI route handlers grouped by resource
- **models/**: Pydantic models for request/response validation
- **middleware/**: Error handling, logging, CORS
- **dependencies.py**: FastAPI dependency injection setup

### Test Structure

- **unit/**: Fast, isolated tests with mocked dependencies
- **integration/**: Tests with real database and external services
- **e2e/**: Full API tests simulating user interactions
- **fixtures/**: Shared test data and factory functions

### Documentation Structure

- **api/**: API documentation and OpenAPI specs
- **architecture/**: System design docs and ADRs
- **deployment/**: Deployment and operations guides
- **development/**: Developer onboarding and guidelines

## Naming Conventions

### Python Files
- **Modules**: `snake_case.py`
- **Classes**: `PascalCase`
- **Functions**: `snake_case()`
- **Constants**: `UPPER_SNAKE_CASE`

### Database Tables
- `snake_case` naming
- Plural nouns (e.g., `recipes`, `meals`)
- Junction tables: `entity1_entity2` (e.g., `meal_participants`)

### API Endpoints
- `/api/v1/resource-name` (kebab-case)
- RESTful conventions (GET, POST, PUT, DELETE)
- Nested resources: `/api/v1/meal-plans/{id}/meals`

## Configuration Files

### Required at Root
- `.env` (local, gitignored)
- `.env.example` (template, committed)
- `.gitignore`
- `.dockerignore`

### Optional at Root
- `.pre-commit-config.yaml` (pre-commit hooks)
- `.editorconfig` (editor configuration)
- `Makefile` (common development tasks)

## Future Extensions

As the project grows, additional directories may be added:

- **app/integrations/**: Additional external integrations (grocery APIs, etc.)
- **app/background/**: Background jobs and task queues
- **app/websockets/**: Real-time communication layer
- **frontend/**: Future web frontend (React, Vue, etc.)
- **mobile/**: Future mobile apps (React Native, Flutter)
- **deploy/**: Deployment configurations (Kubernetes, Terraform)

---

This structure supports the layered architecture and SOLID principles outlined in the design document, ensuring clear separation of concerns and ease of maintenance.
