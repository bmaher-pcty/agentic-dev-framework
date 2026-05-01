---
name: data-migrations
description: 'Safe schema evolution patterns including migration naming, rollback planning, data backfill, and zero-downtime considerations.'
---

# Data Migrations

## When to Use
- Adding, modifying, or removing data models or fields.
- Planning data backfill for new required columns.
- Reviewing migration safety before applying to shared environments.
- Rolling back a failed migration.

## Procedure
1. Design the schema change and assess impact on existing data.
2. Create migration with descriptive name: `{{MIGRATION_TOOL}} migrate --name <action>_<entity>_<detail>`.
3. Review generated SQL for safety (no implicit data loss).
4. Plan backfill strategy for new NOT NULL columns.
5. Test migration on a copy of production-like data.
6. Document rollback steps before applying.

## Migration Naming Convention
Format: `<action>_<entity>_<detail>`

| Action | Use for |
|--------|---------|
| `add` | New table or column |
| `remove` | Dropping table or column |
| `alter` | Changing type, constraint, or default |
| `rename` | Renaming table or column |
| `index` | Adding or removing indexes |
| `backfill` | Data migration (custom SQL) |

Examples: `add_report_execution_table`, `alter_user_add_role_column`, `index_issues_by_project_id`

## Safe Migration Patterns

### Adding a Required Column
```sql
-- Step 1: Add as nullable
ALTER TABLE "User" ADD COLUMN "role" TEXT;

-- Step 2: Backfill existing rows
UPDATE "User" SET "role" = 'viewer' WHERE "role" IS NULL;

-- Step 3: Add NOT NULL constraint
ALTER TABLE "User" ALTER COLUMN "role" SET NOT NULL;
```

### Removing a Column (Safe Two-Phase)
1. Remove all code references to the column first.
2. Deploy code change without the column drop.
3. Create migration to drop the column only after code is live without it.

### Renaming (Two-Phase)
1. Add new column, backfill, update code to write both.
2. Deploy and verify.
3. Remove old column in a subsequent migration.

## Rollback Planning
- Before applying: record the migration name and generated SQL.
- Test rollback locally before applying to shared environments.
- For data-destructive migrations: take database snapshot before applying.
- Keep rollback SQL ready: restore dropped columns or tables if needed.

## Checklist
- [ ] Migration has a descriptive name following the naming convention.
- [ ] Generated SQL reviewed for data safety (no implicit DROP without intent).
- [ ] New NOT NULL columns have backfill strategy for existing rows.
- [ ] Rollback steps documented before applying migration.
- [ ] Schema changes produce no type errors after applying.
- [ ] Application starts and passes health check after migration.
- [ ] No migration file left in the repository for a schema that was later redesigned.

<details>
<summary>{{ORM}}-specific commands reference</summary>

> **Note:** This `<details>` block intentionally contains Prisma-specific
> commands as a worked example. Substitute your ORM's equivalents (the
> `{{MIGRATION_TOOL}}` token captures the right invocation prefix for
> your project — `npx prisma`, `alembic`, `rails db:migrate`, etc.).

```bash
# Create migration (inside container)
{{CONTAINER_RUNTIME}} compose exec api npx prisma migrate dev --name <name>

# Apply pending migrations (production-safe)
{{CONTAINER_RUNTIME}} compose exec api npx prisma migrate deploy

# Check migration status
{{CONTAINER_RUNTIME}} compose exec api npx prisma migrate status

# Regenerate ORM client after schema change
{{CONTAINER_RUNTIME}} compose exec api npx prisma generate
```
</details>
