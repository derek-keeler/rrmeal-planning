# Quick Start Guide - Developer Onboarding

Welcome to the Family Meal Planning Application project! This guide will help you get oriented quickly.

## 📖 Documentation Structure

Start here based on your role:

### For Project Managers / Stakeholders
1. **[README.md](../README.md)** - Project overview, features, timeline
2. **[ITERATION_PLAN.md](../ITERATION_PLAN.md)** - Development roadmap and milestones

### For Architects / Tech Leads
1. **[DESIGN.md](../DESIGN.md)** - Complete architecture, patterns, decisions
2. **[PROJECT_STRUCTURE.md](../PROJECT_STRUCTURE.md)** - File organization and naming conventions

### For Developers (Implementation)
1. **[CONTRACTS.md](../CONTRACTS.md)** - Interface specifications (START HERE for coding)
2. **[ITERATION_PLAN.md](../ITERATION_PLAN.md)** - Task breakdown by iteration
3. **[DESIGN.md](../DESIGN.md)** - Reference for architecture questions

### For QA / Testing
1. **[CONTRACTS.md](../CONTRACTS.md)** - Expected behaviors and validation rules
2. **[ITERATION_PLAN.md](../ITERATION_PLAN.md)** - Success criteria per iteration

---

## 🎯 Project Overview (30 Second Version)

**What**: Python backend for intelligent family meal planning

**Key Features**:
- Generate weekly meal plans (80% repeats, 20% new)
- Import MyFitnessPal data
- LLM-powered recipe generation
- Family member preferences & dietary restrictions

**Architecture**: Hexagonal/layered (Domain → Application → Infrastructure → API)

**Tech Stack**: Python 3.11+, FastAPI, SQLAlchemy, Docker

**Timeline**: 5 iterations, ~4-6 weeks total

---

## 🚀 Current Status

**Iteration 1: Design Phase** ✅ COMPLETE

All architectural decisions made. Ready to begin implementation.

**Next: Iteration 2** (5-7 days)
- Set up Python project
- Implement domain entities
- Create repositories
- Write tests

---

## 📋 Quick Reference

### Core Entities (9)
1. **FamilyMember** - Household members with preferences
2. **Ingredient** - Food items with nutrition
3. **Recipe** - Complete recipes with instructions
4. **RecipeIngredient** - Junction with quantities
5. **MealPlanWeek** - Weekly plan container
6. **Meal** - Specific recipe instance on a date
7. **MealRating** - Thumbs up/down ratings
8. **ExternalFoodEntry** - Imported MyFitnessPal data
9. **LLMGeneration** - LLM prompt/response audit log

### Key Use Cases (7)
1. **CreateRecipeUseCase** - Add new recipes
2. **GenerateMealPlanUseCase** - Create weekly plan
3. **RateMealUseCase** - Rate meals
4. **ImportMyFitnessPalDataUseCase** - Import external data
5. **GenerateLLMRecipeUseCase** - Generate recipes with AI
6. **ListRecipesUseCase** - Query recipes with filters
7. **GetMealPlanWeekUseCase** - Retrieve plan details

### API Endpoints
- `POST /api/v1/recipes` - Create recipe
- `GET /api/v1/recipes` - List recipes
- `POST /api/v1/meal-plans` - Generate plan
- `GET /api/v1/meal-plans/{date}` - Get plan
- `POST /api/v1/ratings` - Rate meal
- `POST /api/v1/imports/myfitnesspal` - Import data

---

## 💡 Key Design Decisions

### 1. Meal Planning Algorithm
- **80% Repeat Meals**: From recipes with >3 thumbs up
- **20% New Meals**: LLM-generated or less-used recipes
- **Variety Constraint**: No recipe more than 2x in 4 weeks

### 2. Rating System
- Thumbs up/down per family member
- Recipe is "highly rated" if >3 total thumbs up
- Used to select repeat meals

### 3. External Services
- **MyFitnessPal**: Import historical food data (CSV/JSON)
- **LLM**: OpenAI or Anthropic for recipe generation
- **Graceful Fallback**: System works without LLM

### 4. Database
- SQLAlchemy ORM with type hints
- SQLite for dev, PostgreSQL for prod
- Alembic for migrations

---

## 📚 Document Map

| Document | Size | Purpose | Read When |
|----------|------|---------|-----------|
| README.md | 180 lines | Project overview | First time |
| DESIGN.md | 828 lines | Full architecture | Understanding system |
| CONTRACTS.md | 861 lines | Interface specs | Before implementing |
| PROJECT_STRUCTURE.md | 392 lines | File organization | Setting up files |
| ITERATION_PLAN.md | 552 lines | Implementation roadmap | Planning work |

**Total**: 2,813 lines of documentation

---

**Welcome aboard! Let's build something great! 🚀**
