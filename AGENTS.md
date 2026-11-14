# AGENTS.md — RestMargin Engineering Instructions

This file contains persistent development instructions for all AI coding agents working on RestMargin.

## 1. Project Context

RestMargin is a restaurant financial analytics SaaS designed for Indian restaurant owners.

Its primary purpose is to help restaurant owners understand actual profitability using:

- Swiggy and Zomato CSV imports.
- Order data.
- Dish costs.
- Platform commissions.
- Discounts and taxes.
- Revenue and profitability analytics.

Read `README.md` for the product overview.

Read `docs/PROJECT_STATUS.md` before starting significant development work.

The actual repository implementation is the source of truth.

## 2. Mandatory Rules

### Inspect Before Modifying

Before implementing a feature:

1. Inspect relevant files.
2. Understand existing functionality.
3. Identify dependencies.
4. Check existing types and conventions.
5. Inspect related tests.
6. Identify potential breaking changes.

Never assume that an implementation described in documentation exists in the repository.

### Preserve Existing Functionality

- Do not rewrite working modules unnecessarily.
- Do not change API contracts without a reason.
- Do not introduce destructive database migrations casually.
- Preserve existing architectural decisions unless there is a clear improvement.
- Explain significant changes before implementing them.

### Avoid Overengineering

- Prefer straightforward solutions.
- Avoid unnecessary abstractions.
- Avoid introducing dependencies without a clear benefit.
- Do not introduce microservices without a concrete requirement.
- Keep business-specific logic domain-specific.
- Reuse utilities when they genuinely reduce duplication.

## 3. Backend Architecture

Follow the existing layered architecture:

```text
Routes
  ↓
Middleware
  ↓
Controllers
  ↓
Services
  ↓
Prisma
  ↓
PostgreSQL
```

### Routes

- Define endpoints.
- Apply middleware.
- Connect controllers.
- Contain no business logic.

### Controllers

- Handle HTTP requests.
- Extract and validate request data.
- Coordinate service calls.
- Return standardized responses.
- Avoid direct Prisma queries.

### Services

- Contain business logic.
- Coordinate database operations.
- Handle transactions where necessary.
- Return application data.
- Never depend on Express response objects.

### Utilities

- Keep utilities independent and focused.
- Avoid Express dependencies in general-purpose utilities.
- Avoid embedding domain-specific business rules in generic helpers.

### Middleware

Use centralized authentication, validation, logging, and error handling.

Avoid duplicating middleware functionality inside controllers.

## 4. TypeScript Standards

- Prefer explicit types and interfaces.
- Avoid `any`.
- Avoid unnecessary type assertions.
- Use generics when they provide meaningful reusability.
- Keep request DTOs separate from database models where appropriate.
- Use consistent imports and naming conventions.
- Respect the existing TypeScript configuration.

Do not weaken compiler settings just to make code compile.

## 5. Database and Prisma Standards

- PostgreSQL is the primary database.
- Prisma is the ORM.
- Inspect `schema.prisma` before changing models.
- Review existing migrations before generating new ones.
- Preserve existing data.
- Add indexes where justified by query patterns.
- Avoid unnecessary database queries.
- Use transactions for related operations.
- Avoid N+1 queries.
- Maintain correct tenant ownership.

### Multi-Tenant Security

Restaurant isolation is mandatory.

Every restaurant-specific query must enforce the authenticated restaurant scope.

Never trust a client-supplied restaurant ID when the authenticated context establishes the tenant.

This applies to:

- Orders.
- Uploads.
- Dish costs.
- Analytics.
- Reports.
- Any future restaurant-specific entity.

Do not rely on frontend authorization to protect data.

## 6. API Standards

Follow the existing standardized success response contract:

```json
{
  "success": true,
  "data": {},
  "meta": {}
}
```

Optional fields such as `message` should follow the existing helper implementation.

Use `sendSuccess` rather than manually constructing inconsistent success responses.

Use the centralized error-handling system.

Do not expose internal stack traces, secrets, or sensitive data to clients.

Document API contract changes.

## 7. Pagination, Filtering and Sorting

Reuse the existing pagination and sorting utilities.

Known infrastructure:

- `buildPagination()`
- `buildPaginationMeta()`
- `buildSorting()`
- Domain-specific filter builders.

Follow the existing naming and typing conventions.

Use domain-specific filtering:

```text
Dish Costs → dish cost filter builder
Orders → order filter builder
Analytics → analytics-specific filters
```

Do not introduce a generic filter engine without a strong reason.

Always scope total counts to the same tenant and filters as the corresponding listing query.

Validate sortable fields against an explicit allowlist.

## 8. Authentication and Security

- Use the existing authentication architecture.
- Hash passwords securely.
- Never expose password hashes.
- Never log credentials or tokens.
- Enforce authorization on the backend.
- Validate untrusted input.
- Restrict upload file types and sizes.
- Avoid exposing sensitive internal errors.
- Keep secrets in environment variables.
- Never commit `.env` files or credentials.

Evaluate token storage and cookie configuration carefully before changing authentication behavior.

## 9. CSV and Financial Data

CSV imports are a critical business feature.

Do not invent platform column mappings.

Inspect actual sample files and existing parsers before implementing changes.

Keep platform-specific parsing separate from normalized internal data structures.

Financial calculations must be:

- Explicit.
- Documented.
- Testable.
- Consistent.
- Based on verified source fields.

Do not assume discounts, taxes, commissions, or refunds are represented identically across platforms.

Do not use careless floating-point arithmetic for monetary calculations.

Use appropriate decimal precision and rounding rules.

When financial requirements are unclear, ask before implementing assumptions.

## 10. Frontend Standards

The planned frontend stack is:

- React.
- TypeScript.
- Vite.
- Tailwind CSS.
- TanStack Query.
- Recharts.

Inspect the actual frontend repository before assuming its architecture.

Guidelines:

- Keep features organized by domain.
- Use reusable UI components.
- Centralize API communication.
- Use TanStack Query for server state.
- Use local React state for local UI state.
- Avoid duplicating backend financial calculations.
- Handle loading, error, and empty states.
- Build responsive layouts.
- Maintain accessibility.
- Avoid fake data in production-facing features.

Frontend authorization is not a substitute for backend authorization.

## 11. Testing Standards

Before completing a feature:

1. Identify affected behavior.
2. Add or update relevant tests.
3. Run the relevant test suite.
4. Report actual results.
5. Explain any failing tests.

Prioritize tests for:

- Authentication.
- Tenant isolation.
- CSV parsing.
- Order persistence.
- Dish cost CRUD.
- Pagination.
- Filtering.
- Sorting.
- Financial calculations.
- Analytics.

Never claim that tests passed unless they were actually executed.

## 12. Development Workflow

For each milestone:

1. Understand the requirement.
2. Inspect relevant files.
3. Explain the proposed approach.
4. Identify affected files.
5. Implement focused changes.
6. Run relevant tests.
7. Review for regressions.
8. Update project documentation.
9. Update `docs/PROJECT_STATUS.md`.

Do not make large unrelated changes in one task.

Do not silently change database schema, financial formulas, API contracts, or authentication behavior.

## 13. Communication Expectations

Work as a senior engineering partner.

When reviewing code:

- Identify actual bugs.
- Separate required fixes from optional improvements.
- Explain trade-offs.
- Avoid rewriting good code unnecessarily.

When implementing features:

- Explain the approach.
- Keep changes focused.
- Explain important decisions.
- Provide testing instructions.

When requirements are ambiguous:

- Identify the ambiguity.
- Recommend an approach.
- Ask for clarification when business behavior or data integrity is affected.

## 14. Definition of Done

A feature is complete only when:

- Requirements are understood.
- Implementation is complete.
- Validation is correct.
- Error handling is appropriate.
- Tenant isolation is preserved.
- Tests have been executed.
- Existing functionality is not unintentionally broken.
- Documentation is updated where necessary.

For financial functionality, formulas and expected results must also be documented and tested.

**Primary objective: Build RestMargin into a reliable, maintainable, financially accurate SaaS without unnecessary complexity.**