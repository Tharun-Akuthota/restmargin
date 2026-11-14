# RestMargin

**Restaurant Financial Analytics & Profitability Management SaaS**

RestMargin is a restaurant financial analytics platform designed to help restaurant owners understand their actual business profitability.

Restaurant owners frequently receive sales reports from food delivery platforms such as Swiggy and Zomato. However, revenue alone does not tell them how much money their restaurant actually makes.

Platform commissions, discounts, taxes, ingredient costs, packaging expenses, and other deductions can significantly affect profitability.

RestMargin aims to simplify this process by transforming restaurant order data and dish costs into meaningful financial insights.

The ultimate goal is to help restaurant owners answer one important question:

**"After accounting for all relevant costs and deductions, how much money is my restaurant actually making?"**

---

## 1. Product Vision

Build an affordable, reliable, and easy-to-use financial analytics SaaS that helps small and independent restaurant owners make better business decisions.

RestMargin should make financial information accessible even to restaurant owners without accounting or technical expertise.

Instead of manually analyzing spreadsheets, owners should be able to upload their order reports, maintain dish costs, and understand their restaurant's financial performance through a centralized dashboard.

## 2. Target Audience

Initial target audience:

- Independent restaurant owners.
- Small and medium-sized restaurants.
- Cloud kitchen operators.
- Restaurant managers.
- Businesses selling through food delivery platforms.

**Initial market:** India.

The product should support Indian currency (INR), Indian restaurant terminology, and platform-specific order reports.

The initial experience should prioritize small businesses rather than enterprise restaurant chains.

## 3. Core Problems We Solve

Restaurant owners often struggle with:

1. Understanding actual profit instead of just revenue.
2. Tracking platform commissions and deductions.
3. Understanding the cost of preparing individual dishes.
4. Identifying profitable and loss-making menu items.
5. Comparing business performance across delivery platforms.
6. Understanding weekly and monthly financial trends.
7. Manually processing multiple CSV reports.

RestMargin aims to bring these insights together in one place.

---

## 4. Core Features

### 4.1 Authentication and Restaurant Management

- User registration.
- Secure login.
- JWT-based authentication.
- Restaurant account association.
- Protected API endpoints.
- Restaurant-specific data isolation.

The platform is designed around a multi-tenant architecture.

Each restaurant must only access its own users, uploads, orders, dish costs, and analytics.

### 4.2 CSV Upload and Order Management

Restaurant owners should be able to upload CSV reports exported from supported delivery platforms.

Initial platform support:

- Swiggy.
- Zomato.

Expected workflow:

1. Upload a CSV file.
2. Validate its structure.
3. Parse platform-specific data.
4. Normalize records into a common order format.
5. Persist valid order records.
6. Track upload and processing status.
7. Make imported data available for analytics.

Future improvements:

- CSV validation and error reporting.
- Duplicate detection.
- Import summaries.
- Upload history.
- Large-file processing.
- Additional platform adapters.

### 4.3 Dish Cost Management

Restaurant owners need to maintain the cost associated with preparing their dishes.

Initial functionality:

- Create dish cost records.
- View dish costs.
- Update dish costs.
- Search by dish name.
- Paginate records.
- Sort records.

Potential future improvements:

- Ingredient-level cost breakdowns.
- Recipe management.
- Ingredient price history.
- Packaging cost configuration.
- Historical profitability calculations.

### 4.4 Financial Analytics Dashboard

The dashboard should eventually provide:

- Total revenue.
- Net revenue.
- Platform commissions.
- Discounts.
- Applicable taxes.
- Ingredient costs.
- Packaging costs.
- Actual profit.
- Profit margin.
- Average order value.
- Total orders.
- Profitable dishes.
- Loss-making dishes.

Additional analytics:

- Revenue trends.
- Profit trends.
- Weekly summaries.
- Date-range filtering.
- Platform comparisons.
- Dish-level profitability.

All financial calculations must be based on verified source data and documented business rules.

### 4.5 Reports and Insights

Planned reporting capabilities:

- Weekly business summaries.
- Best-performing dishes.
- Loss-making dishes.
- Platform performance comparisons.
- Revenue and profit trends.
- Cost breakdowns.

---

## 5. Technology Stack

The project is designed with a separate frontend and backend.

### Backend

| Technology | Purpose |
|---|---|
| Node.js | Runtime |
| Express.js | Backend framework |
| TypeScript | Type safety |
| PostgreSQL | Relational database |
| Prisma | ORM |
| JWT | Authentication |
| bcrypt | Password hashing |
| Multer | File uploads |
| Pino | Structured logging |

Additional technologies may be introduced as requirements evolve.

### Frontend

Planned technology stack:

| Technology | Purpose |
|---|---|
| React | UI development |
| TypeScript | Type safety |
| Vite | Build tooling |
| Tailwind CSS | Styling |
| TanStack Query | Server-state management |
| Recharts | Financial charts |

The frontend implementation status must be checked in the repository.

### Infrastructure

Potential future infrastructure:

- AWS S3 for file storage.
- Redis for caching.
- Docker for containerization.
- Cloud deployment.
- Automated testing and CI/CD.

These are planned technologies, not necessarily current dependencies.

---

## 6. Architecture

RestMargin follows a layered backend architecture.

```text
Client
  |
  v
Routes
  |
  v
Middleware
  |
  v
Controllers
  |
  v
Services
  |
  v
Prisma ORM
  |
  v
PostgreSQL
```

### Responsibilities

**Routes**
- Define API endpoints.
- Apply middleware.
- Connect endpoints to controllers.

**Middleware**
- Authentication.
- Request validation.
- Request logging.
- Error handling.

**Controllers**
- Handle HTTP requests.
- Extract request parameters.
- Coordinate services.
- Format HTTP responses.

**Services**
- Execute business logic.
- Coordinate database operations.
- Enforce business rules.
- Handle transactions.

**Utilities**
- Reusable pagination.
- Sorting.
- Filtering.
- Authentication helpers.
- Shared functionality.

**Types**
- Domain types.
- Request DTOs.
- Response types.
- Shared infrastructure types.

The frontend should consume backend APIs rather than duplicate financial business logic.

---

## 7. Backend Engineering Principles

The backend has been developed with the following architectural goals:

- Thin controllers.
- Dedicated service layer.
- Centralized error handling.
- Structured logging.
- Reusable utilities.
- Typed API responses.
- Transaction safety.
- Restaurant-level data isolation.

### Standardized Success Response

The selected API response structure is:

```json
{
  "success": true,
  "data": {},
  "meta": {}
}
```

The `message` and `meta` fields are optional depending on the endpoint.

### Pagination

Listing endpoints should support reusable pagination infrastructure.

Example:

```http
GET /dish-costs?page=1&limit=20
```

### Filtering

Filtering should be domain-specific.

Examples:

- Dish cost filters.
- Order filters.
- Analytics filters.

Avoid forcing all entities into one universal filter engine.

### Sorting

Sorting should use a reusable utility with explicitly allowed sortable fields.

Clients must not be able to select arbitrary database fields.

---

## 8. Product Principles

RestMargin should follow these principles:

1. Financial accuracy comes before visual presentation.
2. Simplicity is more important than unnecessary complexity.
3. Every restaurant's data must remain isolated.
4. CSV data must be validated and normalized.
5. Financial calculations must be documented.
6. The frontend must use real backend data.
7. Features should be built incrementally.
8. Reusable infrastructure should be introduced when it provides genuine value.
9. Existing working functionality must be preserved.
10. The product should solve real restaurant-owner problems.

---

## 9. Planned Development Roadmap

### Backend

- Complete authentication and authorization.
- Complete CSV processing.
- Complete order ingestion.
- Complete dish cost management.
- Complete pagination, sorting, and filtering integration.
- Complete financial analytics.
- Add integration and unit tests.
- Improve database performance.
- Introduce caching where beneficial.
- Improve production readiness.

### Frontend

- Application foundation.
- Authentication pages.
- Responsive application layout.
- Dashboard.
- CSV upload management.
- Orders management.
- Dish cost management.
- Analytics and reporting.
- Settings.

### Production

- Security review.
- Performance optimization.
- Automated tests.
- Cloud storage.
- Deployment.
- Monitoring.
- Production launch.

Refer to `docs/PROJECT_STATUS.md` for the actual implementation status and next milestone.

---

## 10. Long-Term Goal

Transform RestMargin from an early-stage backend project into a complete, reliable, production-ready SaaS used by real restaurant owners.

A restaurant owner should eventually be able to:

1. Register their restaurant.
2. Log in securely.
3. Upload Swiggy or Zomato order reports.
4. Import and manage orders.
5. Configure dish costs.
6. Understand revenue and actual profitability.
7. Identify profitable and loss-making dishes.
8. Compare platform performance.
9. Track financial performance over time.
10. Make better business decisions.

**RestMargin — Understand your restaurant's real margins.**