---
name: database-changes
description: Design, review, and implement database schema and data changes safely. Focus on backward compatibility, zero-downtime deployments, performance, and data integrity.
---

---

name: database-changes

description: Design, review, and implement database schema and data changes safely. Focus on backward compatibility, zero-downtime deployments, performance, and data integrity.

---

# Database Changes

## Goal

Implement database changes safely while preserving data integrity, application compatibility, and production availability.

Database changes are often irreversible and carry higher operational risk than application code.

Favor additive, backward-compatible migrations whenever possible.

---

# When to Use

Use this skill whenever:

- Creating migrations

- Modifying database schemas

- Renaming tables or columns

- Adding indexes

- Changing constraints

- Performing data migrations

- Optimizing queries

- Reviewing pull requests involving persistence

- Planning production deployments

---

# Core Principles

- Preserve existing data.

- Prefer additive changes.

- Maintain backward compatibility.

- Minimize locking.

- Design for zero downtime.

- Always have a rollback strategy.

---

# Migration Workflow

## Step 1 — Understand the Change

Determine:

- What business requirement drives this change?

- Is this schema, data, or both?

- Which applications consume this data?

- What is the deployment order?

---

## Step 2 — Classify the Change

Examples:

Schema Changes

- Add table

- Add column

- Rename column

- Remove column

- Modify data type

- Add constraint

- Remove constraint

Data Changes

- Backfill data

- Cleanup data

- Normalize values

- Migrate existing records

Performance Changes

- Add indexes

- Remove indexes

- Partition tables

- Optimize queries

---

## Step 3 — Review Compatibility

Prefer:

✅ Add nullable column

✅ Add new table

✅ Add new index

Avoid:

❌ Drop columns immediately

❌ Rename columns without compatibility

❌ Change data types without migration

❌ Remove constraints without understanding downstream impact

---

# Zero-Downtime Strategy

For breaking changes, prefer multi-step deployments.

Example:

Deployment 1

- Add new column

- Write to both old and new columns

- Read from old column

Deployment 2

- Read from new column

- Continue dual writes

Deployment 3

- Stop writing old column

Deployment 4

- Remove old column

Never combine all steps into one deployment.

---

# Schema Review

Review:

- Primary keys

- Foreign keys

- Constraints

- Defaults

- Nullability

- Unique indexes

- Data types

Avoid unnecessary nullable columns.

---

# Index Review

Consider indexes when:

- Queries filter frequently

- Columns participate in joins

- Sorting occurs regularly

- Foreign keys exist

Review:

- Index selectivity

- Composite indexes

- Covering indexes

- Write overhead

Do not add indexes without evidence.

---

# Query Review

Evaluate:

- Execution plan

- Full table scans

- N+1 queries

- Join strategy

- Pagination

- Lock contention

Optimize queries before scaling infrastructure.

---

# Transaction Review

Verify:

- Transaction boundaries

- Isolation level

- Lock duration

- Deadlock potential

- Retry strategy

Keep transactions as short as possible.

---

# Spring Boot Guidelines

Review:

- JPA mappings

- Cascade settings

- Fetch strategies

- Lazy loading

- Entity relationships

- Optimistic locking

- Batch operations

Avoid excessive eager loading.

---

# Liquibase / Flyway

Migration scripts should:

- Be repeatable

- Be deterministic

- Be reviewed

- Be tested

- Be version controlled

Never modify an already executed migration.

Create a new migration instead.

---

# Data Migration

Large migrations should:

- Run in batches

- Be resumable

- Log progress

- Support retries

- Minimize locking

Avoid migrating millions of rows in one transaction.

---

# Rollback Strategy

Every migration should consider rollback.

Determine:

- Can the schema be reverted?

- Can data be restored?

- Is rollback destructive?

- Are backups required?

Not every migration is reversible.

Document irreversible operations.

---

# Common Mistakes

Avoid:

- Dropping columns immediately

- Long-running transactions

- Missing indexes

- Locking entire tables

- Renaming columns without compatibility

- Updating millions of rows in one transaction

- Assuming migrations are instantaneous

- Skipping rollback planning

---

# Review Checklist

Verify:

- Backward compatibility

- Migration ordering

- Zero-downtime approach

- Index review

- Query performance

- Transaction safety

- Rollback strategy

- Migration testing

- Data integrity

- Documentation

---

# Deliverable

## Migration Summary

Describe the proposed database change.

## Schema Changes

List tables, columns, constraints, and indexes.

## Compatibility Review

Identify any breaking changes and mitigation strategy.

## Performance Review

Evaluate query and indexing impact.

## Rollback Plan

Describe how the change can be safely reverted.

## Risks

Highlight operational or data integrity concerns.

## Recommendations

Provide prioritized improvements.

---

# Guiding Principle

Database migrations should be boring.

A successful migration is one that users never notice, operators can monitor confidently, and engineers can deploy without fear.