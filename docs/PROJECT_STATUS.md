# RestMargin — Project Status

**Document type:** Development tracking  
**Project:** RestMargin  
**Last historical handoff update:** 2026-11-14  
**Status:** Backend foundation developed; frontend development and full project audit pending.

> This document records the known development history from previous engineering sessions. All statuses must be verified against the current repository before being treated as confirmed implementation status.

---

# 1. Overall Project Status

| Area | Historical status | Next action |
|---|---|---|
| Product vision | Defined | Maintain |
| Backend architecture | Foundation developed | Audit |
| Authentication | Implemented foundation | Verify |
| Error handling | Implemented foundation | Verify |
| Logging | Implemented foundation | Verify |
| Transactions | Introduced | Audit |
| CSV upload | Basic upload tested | Audit processing |
| Order ingestion | Unverified | Inspect |
| Dish cost management | Implemented foundation | Verify |
| Pagination | Utility implemented | Verify integration |
| Sorting | Utility implemented | Verify integration |
| Filtering | Utility implemented | Verify integration |
| Analytics | Previously developed | Audit calculations |
| Frontend | Not yet confirmed | Inspect/create |
| Automated testing | Unverified | Audit |
| Caching | Planned | Not started/verify |
| Production deployment | Planned | Not started/verify |

---

# 2. Product Scope

RestMargin aims to provide restaurant owners with:

- Secure restaurant accounts.
- CSV-based order imports.
- Order management.
- Dish cost management.
- Revenue analytics.
- Actual profitability calculations.
- Platform comparisons.
- Weekly and date-based reports.
- Identification of profitable and loss-making dishes.

Initial platform focus:

- Swiggy.
- Zomato.

Initial market:

- India.
- INR currency.

---

# 3. Backend Progress

## 3.1 Authentication

**Historical status: Implemented foundation — verify current code.**

Previously developed:

- User registration.
- User login.
- JWT authentication.
- bcrypt password hashing.
- Restaurant creation during registration.
- User-to-restaurant association.
- Protected routes.
- Typed authentication request support.

Previously discussed token payload:

```ts
{
  userId: string;
  restaurantId: string;
}
```

Verification required:

- Token expiry.
- Invalid token handling.
- Password validation.
- Restaurant isolation.
- Unauthorized access.
- Sensitive response filtering.

## 3.2 Error Handling

**Historical status: Implemented foundation — verify current code.**

Previously developed:

- `AppError`.
- `asyncHandler`.
- Centralized error middleware.

Expected behavior:

- Controllers use `asyncHandler`.
- Services throw appropriate application errors.
- Global middleware formats error responses.

Verification required:

- Correct HTTP status codes.
- Validation errors.
- Prisma error handling.
- Production-safe error messages.

## 3.3 Logging

**Historical status: Implemented foundation — verify current code.**

Previously developed:

- Pino.
- Structured logging.
- Request IDs.
- Request lifecycle logging.

Future improvements:

- Request duration metrics.
- Better error context.
- Database operation timing.
- Background job observability.

## 3.4 Transactions

**Historical status: Introduced — audit implementation.**

Transactions were used or discussed for:

- Registration involving restaurant and user creation.
- CSV processing involving multiple database writes.

Verify transaction boundaries and failure behavior.

## 3.5 CSV Upload

**Historical status: Basic upload functionality tested.**

Previously confirmed:

```http
POST /api/upload
```

Multer was used for local file uploads.

An upload record with `UPLOADED` status was previously confirmed in the database.

Remaining verification:

- CSV parsing.
- Platform identification.
- Column validation.
- Data normalization.
- Order insertion.
- Duplicate handling.
- Failed import handling.
- Upload status transitions.
- File cleanup.
- Large-file behavior.

Do not mark CSV-to-order ingestion complete until these are verified.

## 3.6 Order Management

**Historical status: Requires repository verification.**

Inspect:

- Order Prisma model.
- Order creation flow.
- CSV-to-order relationship.
- Restaurant ownership.
- Upload association.
- Duplicate handling.
- Order listing endpoints.
- Date and platform filtering.

## 3.7 Dish Cost Management

**Historical status: Developed and refactored.**

Previously developed:

- Dish cost controller.
- Dish cost service.
- Create operation.
- Update operation.
- Listing operation.
- Restaurant association.
- Pagination integration.

Previously reviewed architectural improvements:

- Thin controllers.
- `asyncHandler`.
- `sendSuccess`.
- Service-level orchestration.
- Parallel `findMany` and `count` queries.
- Pagination metadata.

The listing implementation was the first major consumer of reusable pagination infrastructure.

Remaining verification:

- Actual API routes.
- Input validation.
- Duplicate dish handling.
- Tenant isolation.
- Database constraints.
- Complete filter/sort integration.
- Automated tests.

## 3.8 API Response Standardization

**Historical status: Utility implemented.**

The selected success response structure is:

```json
{
  "success": true,
  "data": {},
  "meta": {}
}
```

The `message` field is optional.

Previously developed helper:

```ts
sendSuccess(res, {
  statusCode,
  message,
  data,
  meta
});
```

The helper supports generic response data and metadata.

Verify that all relevant controllers use this helper consistently.

## 3.9 Pagination

**Historical status: Utilities implemented.**

Previously created:

```text
src/types/pagination.ts
src/utils/pagination.ts
src/utils/paginationMeta.ts
```

Types:

- `PaginationQuery`
- `PaginationParams`
- `PaginationMeta`

Previously discussed defaults:

```ts
DEFAULT_PAGE = 1;
DEFAULT_LIMIT = 20;
MAX_LIMIT = 100;
```

Expected functionality:

- Validate page.
- Validate limit.
- Calculate skip.
- Calculate take.
- Cap maximum limit.
- Generate pagination metadata.

Verify behavior and integration.

## 3.10 Sorting

**Historical status: Generic utility developed.**

Previously created:

```text
src/types/sorting.ts
src/utils/sorting.ts
```

Shared type:

```ts
type SortOrder = "asc" | "desc";
```

Utility concept:

```ts
buildSorting({
  sortBy,
  sortOrder,
  defaultSortBy,
  sortableFields
});
```

The user completed revisions to the generic sorting implementation.

Verify:

- Current implementation.
- Prisma compatibility.
- Invalid field fallback.
- Invalid direction fallback.
- Default sorting.
- Integration with dish cost listing.

## 3.11 Filtering

**Historical status: Domain-specific filter builder developed.**

Previously discussed:

```text
src/utils/filters/dishCostFilters.ts
```

Initial filter:

```ts
interface DishCostFilters {
  dishName?: string;
}
```

Architectural decision:

Use domain-specific filter builders rather than a universal generic filtering engine.

Verify:

- Current filter implementation.
- Whitespace handling.
- Prisma query typing.
- Restaurant scoping.
- Service integration.

## 3.12 Analytics

**Historical status: Previously developed, exact functionality requires audit.**

Inspect:

- Analytics routes.
- Controllers.
- Services.
- Filter builders.
- Prisma aggregation queries.
- Financial calculation logic.
- Response contracts.

Identify which metrics are fully implemented and which are incomplete.

Document every financial formula.

Do not assume the dashboard metrics are accurate until the underlying calculation logic has been reviewed and tested.

---

# 4. Phase 9 — Pagination, Filtering and Sorting

**Historical status: Core utilities developed; final integration requires verification.**

## Completed development work

- Pagination types.
- Pagination utility.
- Pagination metadata utility.
- Generic sorting types.
- Generic sorting utility.
- Dish cost filter builder.
- Dish cost listing refactor was developed and reviewed.

## Remaining verification

1. Confirm final service integration.
2. Confirm final controller integration.
3. Verify typed query DTOs.
4. Verify Prisma query compatibility.
5. Verify total count uses the same filters.
6. Verify tenant isolation.
7. Execute the complete query test matrix.

## Phase 9 acceptance tests

```http
GET /dish-costs
GET /dish-costs?dishName=biryani
GET /dish-costs?sortBy=dishName&sortOrder=desc
GET /dish-costs?page=2&limit=5
GET /dish-costs?dishName=rice&page=2&sortOrder=desc
GET /dish-costs?sortBy=invalid
GET /dish-costs?dishName=%20%20%20
GET /dish-costs?limit=500
```

Expected:

- Correct defaults.
- Correct filtering.
- Correct sorting.
- Correct pagination.
- Safe invalid-input fallback.
- Correct metadata.
- Restaurant-level isolation.

**Do not mark Phase 9 fully complete until the actual code and tests confirm these requirements.**

---

# 5. Frontend Progress

**Historical status: No completed frontend implementation confirmed.**

The frontend is the next major development track.

Planned stack:

- React.
- TypeScript.
- Vite.
- Tailwind CSS.
- TanStack Query.
- Recharts.

## Planned modules

### Authentication

- Login.
- Registration.
- Protected routes.
- Logout.
- Session handling.

### Dashboard

- Revenue.
- Costs.
- Actual profit.
- Profit margin.
- Total orders.
- Average order value.
- Revenue trends.
- Profit trends.
- Platform comparisons.

### Uploads

- CSV file selection.
- Drag and drop.
- Upload progress.
- Upload status.
- Upload history.
- Error feedback.

### Orders

- Order listing.
- Search.
- Filtering.
- Sorting.
- Pagination.
- Order details.

### Dish Costs

- Listing.
- Create.
- Update.
- Search.
- Sorting.
- Pagination.

### Analytics

- Date-range filtering.
- Revenue analytics.
- Profit analytics.
- Dish profitability.
- Platform comparisons.

These are planned capabilities. Check backend endpoint availability before frontend integration.

---

# 6. Database Status

**Database:** PostgreSQL  
**ORM:** Prisma

Known domain concepts:

- Restaurant.
- User.
- Upload.
- Order.
- DishCost.

The exact schema must be obtained from:

```text
prisma/schema.prisma
```

Inspect migration history before making schema changes.

Required database audit:

- Model definitions.
- Relations.
- Foreign keys.
- Indexes.
- Unique constraints.
- Enums.
- Nullable fields.
- Tenant ownership.
- Cascading behavior.

Do not invent or assume field definitions based on this document.

---

# 7. Testing Status

**Historical status: Automated testing coverage not confirmed.**

Testing requirements:

## Unit tests

- Pagination.
- Pagination metadata.
- Sorting.
- Dish cost filtering.
- CSV normalization.
- Financial calculations.

## Integration tests

- Registration.
- Login.
- Protected routes.
- Dish cost CRUD.
- Filtering.
- Sorting.
- Pagination.
- CSV upload.
- CSV processing.
- Analytics.

## Tenant isolation

Test at least two separate restaurants.

Verify that one restaurant cannot access or modify another restaurant's:

- Orders.
- Uploads.
- Dish costs.
- Analytics.

## Frontend tests

- Authentication.
- Form validation.
- Protected routes.
- API loading states.
- Error handling.
- Empty states.
- Upload workflow.
- Dish cost CRUD.
- Dashboard.

---

# 8. Phase 10 — Performance and Production Engineering

**Historical status: Planned.**

Proposed milestones:

## Database performance

- Query optimization.
- Index review.
- N+1 query prevention.
- Analytics optimization.
- Query profiling.

## Caching

Redis was proposed for:

- Dashboard data.
- Analytics.
- Expensive aggregate queries.

Requirements:

- Cache-aside.
- TTL.
- Cache invalidation.
- Tenant-aware keys.
- Date-range-aware keys.
- Data consistency.

## Rate limiting

- Authentication endpoints.
- Upload endpoints.
- General API.
- Expensive analytics.

## Observability

- Request duration.
- Health endpoints.
- Readiness checks.
- Structured error context.

## Production readiness

- Environment validation.
- Graceful shutdown.
- Security headers.
- CORS.
- Compression.
- File upload restrictions.
- Database connection management.
- Docker.
- Deployment.

---

# 9. Immediate Priorities

The next development sequence should be determined after the repository audit.

Suggested order:

1. Audit the existing backend.
2. Verify and close Phase 9.
3. Complete CSV parsing and order ingestion.
4. Verify dish cost functionality.
5. Audit and complete financial analytics.
6. Establish frontend architecture.
7. Build authentication.
8. Build dashboard.
9. Build upload management.
10. Build dish cost management.
11. Build orders and analytics pages.
12. Add complete testing coverage.
13. Improve performance and production readiness.
14. Deploy and launch.

Adjust the sequence based on actual dependencies and implementation gaps.

---

# 10. Current Risks and Open Questions

The following require investigation before production:

- Is CSV processing fully implemented or only upload registration?
- Are Swiggy and Zomato CSV formats both supported?
- How are duplicate orders detected?
- Are failed imports recoverable?
- Are all restaurant-specific queries tenant-scoped?
- Are financial formulas documented?
- Are GST and discounts handled correctly?
- Are dish costs historically tracked?
- What happens to historical profit when dish costs change?
- Are analytics queries performant?
- Does the backend have automated integration tests?
- Is a frontend repository already available?
- Is file storage local or cloud-based?
- Is production authentication configured securely?

Do not make assumptions about these questions.

---

# 11. Development Tracking

Update this section after each meaningful milestone.

## Current milestone

Project discovery and repository audit.

## Current activity

Preparing the project for continued development using Codex.

## Next milestone

Complete repository audit and establish a verified implementation roadmap.

## Milestone completion log

| Milestone | Status | Date | Notes |
|---|---|---|---|
| Authentication foundation | Historically implemented | Verify | Inspect current implementation |
| Error handling | Historically implemented | Verify | Inspect middleware |
| Logging | Historically implemented | Verify | Inspect Pino configuration |
| Transaction foundation | Historically introduced | Verify | Inspect transaction usage |
| CSV upload foundation | Previously tested | Verify | Confirm full processing |
| Dish cost foundation | Historically implemented | Verify | Inspect current endpoints |
| Response standardization | Utility developed | Verify | Check controller adoption |
| Pagination | Utility developed | Verify | Check integration |
| Sorting | Utility developed | Verify | Check integration |
| Filtering | Utility developed | Verify | Check integration |
| Phase 9 completion | Pending verification | — | Execute acceptance tests |
| Frontend | Pending | — | Repository inspection |
| Phase 10 | Planned | — | Performance and production engineering |

---

# 12. Maintenance Instructions

After each development milestone, update:

- Overall project status.
- Feature implementation status.
- API changes.
- Database changes.
- Test results.
- Known issues.
- Next milestone.
- Completion log.

Only mark a feature complete when its implementation has been verified.

Record important architectural decisions.

Keep this document concise enough to remain useful, but detailed enough for a new development session to resume without reconstructing the entire project history.

**Source of truth:** The actual source code, Prisma schema, migrations, tests, and verified API behavior.