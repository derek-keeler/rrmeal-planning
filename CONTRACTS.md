# Component Contracts & Interface Specifications

This document provides detailed **input/output contracts** for all major components in the Family Meal Planning Application. These contracts serve as the implementation specification for Iteration 2 and beyond.

## Table of Contents

1. [Domain Services](#domain-services)
2. [Repository Interfaces](#repository-interfaces)
3. [Use Case Contracts](#use-case-contracts)
4. [External Client Interfaces](#external-client-interfaces)
5. [API Endpoint Contracts](#api-endpoint-contracts)

---

## Domain Services

Domain services contain pure business logic with no external dependencies.

### RecipeRatingService

**Purpose**: Aggregate ratings across all family members for a recipe.

**Method**: `get_aggregate_rating(recipe_id: UUID) -> RecipeRatingAggregate`

**Input**:
```python
recipe_id: UUID
```

**Output**:
```python
@dataclass
class RecipeRatingAggregate:
    recipe_id: UUID
    total_ratings: int
    thumbs_up_count: int
    thumbs_down_count: int
    is_highly_rated: bool  # True if thumbs_up_count > 3
    rating_breakdown: dict[UUID, int]  # family_member_id -> rating_value
    last_rated_at: Optional[datetime]
```

**Business Rules**:
- A recipe is "highly rated" if thumbs_up_count > 3
- Ratings are counted across all meals that used this recipe
- If no ratings exist, all counts are 0 and is_highly_rated is False

---

### RecipeVarietyService

**Purpose**: Track recipe usage to enforce variety in meal plans.

**Method**: `get_usage_stats(recipe_id: UUID, lookback_weeks: int = 4) -> RecipeUsageStats`

**Input**:
```python
recipe_id: UUID
lookback_weeks: int = 4  # Default 4 weeks
```

**Output**:
```python
@dataclass
class RecipeUsageStats:
    recipe_id: UUID
    times_used_recently: int  # Count in lookback period
    last_used_date: Optional[date]
    is_overused: bool  # True if used more than 2 times in lookback period
    usage_by_week: dict[date, int]  # week_start_date -> count
```

**Business Rules**:
- Lookback period is measured from today backward N weeks
- A recipe is "overused" if it appears more than 2 times in the lookback period
- Usage is counted by scheduled_date in Meal entities

---

### NutritionCalculationService

**Purpose**: Calculate nutritional totals for recipes based on ingredients.

**Method**: `calculate_nutrition(recipe_id: UUID) -> NutritionInfo`

**Input**:
```python
recipe_id: UUID
```

**Output**:
```python
@dataclass
class NutritionInfo:
    recipe_id: UUID
    total_calories: float
    total_protein_g: float
    total_carbs_g: float
    total_fat_g: float
    total_fiber_g: float
    per_serving_calories: float
    per_serving_protein_g: float
    per_serving_carbs_g: float
    per_serving_fat_g: float
    per_serving_fiber_g: float
    is_complete: bool  # True if all ingredients have nutrition data
```

**Business Rules**:
- Sum nutrition from all RecipeIngredient entries
- Multiply ingredient nutrition by quantity
- Divide totals by servings count for per-serving values
- If any ingredient lacks nutrition data, is_complete = False

---

## Repository Interfaces

All repositories follow a common pattern. Below are the complete interface specifications.

### Base Repository Interface

**Common Methods** (all repositories implement these):

```python
class BaseRepository(Protocol[T]):
    def create(self, entity: T) -> T
    def get_by_id(self, id: UUID) -> Optional[T]
    def list(self, filters: dict, pagination: Pagination, sort: Sort) -> list[T]
    def update(self, entity: T) -> T
    def delete(self, id: UUID) -> bool
    def exists(self, id: UUID) -> bool
```

**Common Input Types**:
```python
@dataclass
class Pagination:
    page: int = 1  # 1-indexed
    page_size: int = 20
    
@dataclass
class Sort:
    field: str = "created_at"
    order: str = "desc"  # "asc" or "desc"
```

---

### RecipeRepository Interface

**Additional Methods**:

```python
class RecipeRepository(BaseRepository[Recipe]):
    def get_highly_rated(
        self, 
        min_thumbs_up: int = 3, 
        limit: int = 100
    ) -> list[Recipe]
    
    def search(
        self, 
        search_text: str, 
        filters: RecipeFilters,
        pagination: Pagination
    ) -> list[Recipe]
    
    def get_by_tags(
        self, 
        tags: list[str], 
        match_all: bool = False
    ) -> list[Recipe]
```

**Filter Types**:
```python
@dataclass
class RecipeFilters:
    tags: Optional[list[str]] = None
    cuisine_type: Optional[str] = None
    meal_types: Optional[list[str]] = None
    created_by: Optional[str] = None  # "user", "system", "llm"
    min_prep_time: Optional[int] = None
    max_prep_time: Optional[int] = None
    min_servings: Optional[int] = None
    max_servings: Optional[int] = None
```

---

### MealPlanRepository Interface

**Additional Methods**:

```python
class MealPlanRepository(BaseRepository[MealPlanWeek]):
    def get_by_week_start(
        self, 
        week_start_date: date
    ) -> Optional[MealPlanWeek]
    
    def get_active_plans(self) -> list[MealPlanWeek]
    
    def archive_plan(self, id: UUID) -> bool
```

---

### MealRepository Interface

**Additional Methods**:

```python
class MealRepository(BaseRepository[Meal]):
    def list_by_meal_plan(
        self, 
        meal_plan_id: UUID
    ) -> list[Meal]
    
    def list_by_date_range(
        self, 
        start_date: date, 
        end_date: date
    ) -> list[Meal]
    
    def list_by_recipe(
        self, 
        recipe_id: UUID,
        start_date: Optional[date] = None,
        end_date: Optional[date] = None
    ) -> list[Meal]
    
    def create_bulk(
        self, 
        meals: list[Meal]
    ) -> list[Meal]
```

---

### MealRatingRepository Interface

**Additional Methods**:

```python
class MealRatingRepository(BaseRepository[MealRating]):
    def list_by_recipe(
        self, 
        recipe_id: UUID
    ) -> list[MealRating]
    
    def list_by_family_member(
        self, 
        family_member_id: UUID
    ) -> list[MealRating]
    
    def list_by_meal(
        self, 
        meal_id: UUID
    ) -> list[MealRating]
    
    def get_or_create(
        self, 
        meal_id: UUID, 
        family_member_id: UUID
    ) -> MealRating
```

---

## Use Case Contracts

Use cases orchestrate business workflows. Each use case has one `execute()` method.

### CreateRecipeUseCase

**Method**: `execute(input: CreateRecipeInput) -> CreateRecipeOutput`

**Input**:
```python
@dataclass
class CreateRecipeInput:
    title: str
    description: Optional[str] = None
    instructions: list[str]
    tags: list[str] = field(default_factory=list)
    cuisine_type: Optional[str] = None
    meal_types: list[str]
    prep_time_minutes: Optional[int] = None
    cook_time_minutes: Optional[int] = None
    servings: int
    ingredients: list[RecipeIngredientInput]
    created_by: str = "user"
    source: str = "user_input"
    
@dataclass
class RecipeIngredientInput:
    ingredient_name: str
    quantity: float
    unit: str
    notes: Optional[str] = None
```

**Output**:
```python
@dataclass
class CreateRecipeOutput:
    success: bool
    recipe_id: Optional[UUID]
    validation_errors: list[str]
    created_at: Optional[datetime]
```

**Validation Rules**:
- title must be non-empty (max 200 chars)
- servings must be positive integer
- meal_types must not be empty
- instructions must not be empty
- ingredient quantities must be positive
- ingredient names will be normalized (lowercase, trimmed)

**Workflow**:
1. Validate input data
2. Check if ingredients exist; create missing ones
3. Create Recipe entity
4. Create RecipeIngredient entities
5. Persist to repositories
6. Return recipe ID

---

### GenerateMealPlanUseCase

**Method**: `execute(input: GenerateMealPlanInput) -> GenerateMealPlanOutput`

**Input**:
```python
@dataclass
class GenerateMealPlanInput:
    week_start_date: date  # Must be a Monday
    family_member_ids: list[UUID]
    preferences: MealPlanPreferences
    locked_meals: list[LockedMeal] = field(default_factory=list)
    
@dataclass
class MealPlanPreferences:
    repeat_meal_percentage: float = 0.8  # 0.0-1.0
    variety_lookback_weeks: int = 4
    meal_types_per_day: list[str] = field(default_factory=lambda: ["dinner"])
    cuisine_preferences: list[str] = field(default_factory=list)
    dietary_restrictions: list[str] = field(default_factory=list)
    exclude_recipe_ids: list[UUID] = field(default_factory=list)
    
@dataclass
class LockedMeal:
    date: date
    meal_type: str
    recipe_id: UUID
```

**Output**:
```python
@dataclass
class GenerateMealPlanOutput:
    success: bool
    meal_plan_week_id: Optional[UUID]
    meals: list[MealSummary]
    generation_summary: GenerationSummary
    errors: list[str]
    
@dataclass
class MealSummary:
    meal_id: UUID
    recipe_id: UUID
    recipe_title: str
    scheduled_date: date
    meal_type: str
    is_repeat: bool
    is_new: bool
    is_locked: bool
    
@dataclass
class GenerationSummary:
    total_meals: int
    repeat_meal_count: int
    new_meal_count: int
    llm_generated_count: int
    locked_meal_count: int
```

**Validation Rules**:
- week_start_date must be a Monday
- family_member_ids must all exist
- repeat_meal_percentage must be 0.0-1.0
- locked_meals must have valid dates within the week

**Workflow**:
1. Validate week_start_date is Monday
2. Check for existing active plan for this week
3. Fetch highly-rated recipes (RecipeRatingService)
4. Fetch recipe variety data (RecipeVarietyService)
5. Calculate total meal slots (7 days × len(meal_types_per_day))
6. Allocate X% to repeats from highly-rated, non-overused recipes
7. Allocate (100-X)% to new meals (call LLM if available)
8. Insert locked meals
9. Create MealPlanWeek entity
10. Create Meal entities (bulk insert)
11. Persist to repositories
12. Return plan details

---

### RateMealUseCase

**Method**: `execute(input: RateMealInput) -> RateMealOutput`

**Input**:
```python
@dataclass
class RateMealInput:
    meal_id: UUID
    family_member_id: UUID
    thumbs_up: bool
    rating_value: int = 1  # 1-5 scale
    comment: Optional[str] = None
```

**Output**:
```python
@dataclass
class RateMealOutput:
    success: bool
    rating_id: Optional[UUID]
    aggregate_thumbs_up_count: int
    aggregate_thumbs_down_count: int
    is_highly_rated: bool
    errors: list[str]
```

**Validation Rules**:
- meal_id must exist
- family_member_id must exist
- rating_value must be 1-5
- comment max length 500 chars

**Workflow**:
1. Validate meal and family member exist
2. Get meal's recipe_id
3. Check for existing rating (meal_id + family_member_id)
4. Create or update MealRating entity
5. Persist to repository
6. Call RecipeRatingService to get updated aggregates
7. Return rating details

---

### ImportMyFitnessPalDataUseCase

**Method**: `execute(input: ImportMyFitnessPalInput) -> ImportMyFitnessPalOutput`

**Input**:
```python
@dataclass
class ImportMyFitnessPalInput:
    file_path: Optional[str] = None
    raw_data: Optional[str] = None
    data_format: str = "csv"  # "csv" or "json"
    start_date: date
    end_date: date
    map_to_recipes: bool = True
```

**Output**:
```python
@dataclass
class ImportMyFitnessPalOutput:
    success: bool
    imported_count: int
    mapped_recipe_count: int
    new_recipe_candidates: list[RecipeCandidateaddle]
    errors: list[str]
    
@dataclass
class RecipeCandidate:
    food_name: str
    frequency: int
    suggested_recipe: Optional[RecipeSummary]
```

**Validation Rules**:
- Either file_path or raw_data must be provided
- start_date must be before end_date
- data_format must be "csv" or "json"

**Workflow**:
1. Parse file/data with MyFitnessPalClient
2. Transform to ExternalFoodEntry entities
3. If map_to_recipes:
   a. Fuzzy match with existing recipes
   b. Set mapped_recipe_id where matches found
4. Bulk insert ExternalFoodEntry records
5. Aggregate frequent unmapped foods
6. Optionally call LLM for recipe suggestions
7. Return import statistics

---

## External Client Interfaces

### MyFitnessPalClient Interface

**Purpose**: Parse and import MyFitnessPal export data.

**Method**: `parse_export_file(file_path: str) -> list[ExternalFoodEntry]`

**Input**:
```python
file_path: str  # Path to CSV or JSON file
```

**Output**:
```python
list[ExternalFoodEntry]  # Parsed entries
```

**Method**: `parse_export_data(raw_data: str, format: str) -> list[ExternalFoodEntry]`

**Input**:
```python
raw_data: str  # CSV or JSON content
format: str  # "csv" or "json"
```

**Output**:
```python
list[ExternalFoodEntry]
```

**CSV Format Expected**:
```
Date,Meal,Food,Quantity,Unit,Calories,Protein,Carbs,Fat
2024-01-15,Breakfast,Oatmeal,1,cup,150,5,27,3
```

**JSON Format Expected**:
```json
{
  "entries": [
    {
      "date": "2024-01-15",
      "meal": "Breakfast",
      "food": "Oatmeal",
      "quantity": 1,
      "unit": "cup",
      "calories": 150,
      "protein": 5,
      "carbs": 27,
      "fat": 3
    }
  ]
}
```

---

### LLMRecipeGenerator Interface

**Purpose**: Generate new recipes using LLM.

**Method**: `generate_new_recipes(preferences: dict, context: dict, count: int) -> list[GeneratedRecipe]`

**Input**:
```python
preferences: dict = {
    "cuisine_type": Optional[str],
    "meal_type": list[str],
    "dietary_restrictions": list[str],
    "include_ingredients": list[str],
    "exclude_ingredients": list[str]
}
context: dict = {
    "favorite_recipes": list[UUID],  # For inspiration
    "recent_external_foods": list[str]  # From MFP imports
}
count: int = 1  # Number of recipes to generate
```

**Output**:
```python
@dataclass
class GeneratedRecipe:
    title: str
    description: str
    instructions: list[str]
    ingredients: list[dict]  # {name, quantity, unit}
    tags: list[str]
    cuisine_type: Optional[str]
    meal_types: list[str]
    prep_time_minutes: Optional[int]
    cook_time_minutes: Optional[int]
    servings: int
    generation_metadata: dict  # Model, prompt, etc.
```

**Method**: `suggest_weekly_plan_additions(existing_meals: list[Meal], target_count: int, preferences: dict) -> list[GeneratedRecipe]`

**Input**:
```python
existing_meals: list[Meal]  # Already planned meals
target_count: int  # How many new recipes needed
preferences: dict  # Same as above
```

**Output**:
```python
list[GeneratedRecipe]
```

**Error Handling**:
- If LLM unavailable, return empty list (don't raise exception)
- If LLM returns invalid format, log error and return empty list
- Store all prompts/responses in LLMGeneration table

---

## API Endpoint Contracts

### Recipe Endpoints

#### POST /api/v1/recipes

**Request**:
```json
{
  "title": "Chicken Alfredo",
  "description": "Creamy pasta with chicken",
  "instructions": [
    "Cook pasta",
    "Prepare sauce",
    "Combine and serve"
  ],
  "tags": ["italian", "pasta", "dinner"],
  "cuisine_type": "Italian",
  "meal_types": ["dinner"],
  "prep_time_minutes": 15,
  "cook_time_minutes": 20,
  "servings": 4,
  "ingredients": [
    {
      "ingredient_name": "pasta",
      "quantity": 1,
      "unit": "lb",
      "notes": "fettuccine"
    },
    {
      "ingredient_name": "chicken breast",
      "quantity": 1,
      "unit": "lb",
      "notes": "diced"
    }
  ]
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "title": "Chicken Alfredo",
  "description": "Creamy pasta with chicken",
  "cuisine_type": "Italian",
  "meal_types": ["dinner"],
  "prep_time_minutes": 15,
  "cook_time_minutes": 20,
  "servings": 4,
  "tags": ["italian", "pasta", "dinner"],
  "ingredients": [
    {
      "id": "uuid",
      "ingredient": {
        "id": "uuid",
        "name": "pasta"
      },
      "quantity": 1,
      "unit": "lb",
      "notes": "fettuccine"
    }
  ],
  "instructions": [...],
  "created_by": "user",
  "created_at": "2024-01-15T10:00:00Z",
  "aggregate_rating": null
}
```

---

#### GET /api/v1/recipes?tags=italian&meal_type=dinner&page=1&page_size=20

**Response** (200 OK):
```json
{
  "recipes": [
    {
      "id": "uuid",
      "title": "Chicken Alfredo",
      "cuisine_type": "Italian",
      "meal_types": ["dinner"],
      "tags": ["italian", "pasta", "dinner"],
      "servings": 4,
      "prep_time_minutes": 15,
      "cook_time_minutes": 20,
      "aggregate_rating": {
        "thumbs_up_count": 5,
        "thumbs_down_count": 1,
        "is_highly_rated": true
      }
    }
  ],
  "pagination": {
    "page": 1,
    "page_size": 20,
    "total_count": 45,
    "total_pages": 3
  }
}
```

---

### Meal Plan Endpoints

#### POST /api/v1/meal-plans

**Request**:
```json
{
  "week_start_date": "2024-01-15",
  "family_member_ids": ["uuid1", "uuid2"],
  "preferences": {
    "repeat_meal_percentage": 0.8,
    "variety_lookback_weeks": 4,
    "meal_types_per_day": ["dinner"],
    "dietary_restrictions": ["vegetarian"]
  },
  "locked_meals": [
    {
      "date": "2024-01-16",
      "meal_type": "dinner",
      "recipe_id": "uuid"
    }
  ]
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "week_start_date": "2024-01-15",
  "week_end_date": "2024-01-21",
  "status": "active",
  "meals": [
    {
      "id": "uuid",
      "recipe": {
        "id": "uuid",
        "title": "Chicken Alfredo",
        "cuisine_type": "Italian"
      },
      "scheduled_date": "2024-01-15",
      "meal_type": "dinner",
      "is_locked": false,
      "is_repeat": true,
      "is_new": false,
      "participants": ["uuid1", "uuid2"]
    }
  ],
  "generation_summary": {
    "total_meals": 7,
    "repeat_meal_count": 6,
    "new_meal_count": 1,
    "llm_generated_count": 1,
    "locked_meal_count": 1
  }
}
```

---

### Rating Endpoints

#### POST /api/v1/ratings

**Request**:
```json
{
  "meal_id": "uuid",
  "family_member_id": "uuid",
  "thumbs_up": true,
  "rating_value": 5,
  "comment": "Delicious!"
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "meal_id": "uuid",
  "recipe_id": "uuid",
  "family_member_id": "uuid",
  "thumbs_up": true,
  "rating_value": 5,
  "comment": "Delicious!",
  "rated_at": "2024-01-15T19:30:00Z",
  "aggregate_stats": {
    "thumbs_up_count": 4,
    "thumbs_down_count": 0,
    "is_highly_rated": true
  }
}
```

---

## Validation Error Format

All endpoints return validation errors in this format:

**Response** (400 Bad Request or 422 Unprocessable Entity):
```json
{
  "detail": [
    {
      "loc": ["body", "title"],
      "msg": "Title must not be empty",
      "type": "value_error"
    },
    {
      "loc": ["body", "servings"],
      "msg": "Servings must be a positive integer",
      "type": "value_error"
    }
  ]
}
```

---

## Summary

This contracts document provides:
- **Complete type signatures** for all major components
- **Input/output specifications** for use cases and services
- **API request/response formats** with examples
- **Validation rules** for all inputs
- **Business logic rules** for domain services

These contracts will guide implementation in Iteration 2 and ensure consistency across the codebase.
