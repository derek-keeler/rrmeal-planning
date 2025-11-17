# Family Meal Planning Application

A Python-based backend system for intelligent family meal planning, designed to make weekly meal planning easy by learning from historical data and user preferences.

## Project Status

**Current Phase**: Design Phase (Iteration 1)

This project is in the initial design phase. The architecture and component specifications are complete, but implementation has not yet begun.

## Documentation

- **[DESIGN.md](DESIGN.md)** - Comprehensive architecture and design document covering:
  - Layered architecture (Domain, Application, Infrastructure, API)
  - Entity models and relationships
  - Use case specifications
  - Component contracts and interfaces
  - Technology stack decisions
  - CI/CD strategy

## Architecture Overview

The application follows a **hexagonal/layered architecture** with strict separation of concerns:

```
┌──────────────────────────────┐
│      API/Interface Layer     │  ← FastAPI REST endpoints
├──────────────────────────────┤
│     Application Layer        │  ← Use cases & orchestration
├──────────────────────────────┤
│       Domain Layer           │  ← Business entities & rules
├──────────────────────────────┤
│    Infrastructure Layer      │  ← DB, external integrations
└──────────────────────────────┘
```

## Core Features (Planned)

### 1. Intelligent Meal Planning
- Generate weekly meal plans automatically
- 80% repeat meals (highly-rated past meals)
- 20% new or novel meals (LLM-generated or unexplored recipes)
- Variety constraints to avoid repetition

### 2. Recipe Management
- Store and organize family recipes
- Import from MyFitnessPal historical data
- LLM-powered recipe generation based on preferences
- Nutritional information tracking

### 3. Family Preferences
- Track dietary restrictions and allergies per member
- Rate meals with thumbs up/down system
- Learn from historical preferences
- Accommodate multiple family members per meal

### 4. External Integrations
- **MyFitnessPal**: Import historical food data
- **LLM APIs**: Generate new recipe suggestions (OpenAI, Anthropic)
- Graceful fallback when external services unavailable

## Design Principles

The system is built following **SOLID principles**:

- **Single Responsibility**: Each component has one clear purpose
- **Open/Closed**: Extensible without modifying core code
- **Liskov Substitution**: Implementations are interchangeable
- **Interface Segregation**: Small, focused interfaces
- **Dependency Inversion**: Depend on abstractions, not concretions

## Technology Stack

- **Language**: Python 3.11+
- **Web Framework**: FastAPI
- **ORM**: SQLAlchemy 2.0+
- **Database**: SQLite (dev), PostgreSQL (prod)
- **Testing**: pytest
- **Code Quality**: black, isort, mypy, ruff
- **Containerization**: Docker + docker-compose

## Project Structure (Planned)

```
app/
  ├── domain/          # Business entities and domain logic
  ├── application/     # Use cases and orchestration
  ├── infrastructure/  # DB, external clients, config
  └── api/             # FastAPI endpoints and models
tests/
  ├── unit/            # Domain and application tests
  ├── integration/     # Infrastructure tests
  └── e2e/             # API endpoint tests
```

## Getting Started

### Prerequisites

- Python 3.11 or higher
- Docker (optional, for containerized deployment)

### Installation (Future)

```bash
# Clone the repository
git clone https://github.com/derek-keeler/rrmeal-planning.git
cd rrmeal-planning

# Install dependencies
pip install -r requirements.txt

# Set up database
alembic upgrade head

# Run the application
uvicorn app.api.main:app --reload
```

### Configuration (Future)

Environment variables:
- `DATABASE_URL` - Database connection string
- `LLM_PROVIDER` - Optional: "openai" or "anthropic"
- `LLM_API_KEY` - API key for LLM service
- `LLM_MODEL` - Model name (e.g., "gpt-4")

## Development Roadmap

### Iteration 1: Design Phase ✓ (Current)
- [x] Architecture design
- [x] Entity modeling
- [x] Use case specifications
- [x] Component contracts

### Iteration 2: Core Implementation (Next)
- [ ] Domain entities with SQLAlchemy
- [ ] Basic repositories
- [ ] Database migrations
- [ ] Domain service unit tests

### Iteration 3: Application Layer
- [ ] Use case implementations
- [ ] Dependency injection setup
- [ ] Integration tests
- [ ] Basic API endpoints

### Iteration 4: External Integrations
- [ ] MyFitnessPal client
- [ ] LLM integration
- [ ] Comprehensive error handling
- [ ] E2E tests

### Iteration 5: User Experience
- [ ] API documentation
- [ ] Simple web UI or CLI
- [ ] User acceptance testing

### Future Enhancements
- Multi-tenant support (multiple families)
- Mobile and desktop applications
- Shopping list generation
- Nutrition goal tracking
- Recipe sharing features
- Grocery delivery integration

## Contributing

This project is currently in the design phase. Contributions will be welcome once the initial implementation is complete.

## License

[To be determined]

## Contact

For questions or suggestions, please open an issue on GitHub.

---

**Note**: This is a design-phase project. Implementation will begin in subsequent iterations.