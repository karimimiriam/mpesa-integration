# 1. Use NestJS with pnpm for the backend

## Status
Accepted

## Context
This project needs to integrate with Safaricom's Daraja API to handle
multiple money flows (STK Push, C2B, B2C, B2B), each with asynchronous
webhook callbacks, an idempotent transaction ledger, and a reconciliation
job. The codebase needed enough structure to keep these concerns
separated as it grows, rather than becoming a flat pile of route handlers.

## Decision
- Backend framework: NestJS (TypeScript), chosen over a lighter framework
  like Express/Fastify because it provides modules, dependency injection,
  and built-in validation out of the box structure that pays off given
  the number of distinct concerns this project has (auth, STK Push,
  callbacks, ledger, reconciliation). Also builds on prior hands-on
  experience with the framework.
- Package manager: pnpm, chosen over npm for its stricter, non-flat
  dependency resolution (catches "phantom dependency" bugs npm allows)
  and lower disk usage via its content-addressable store.

## Consequences
- pnpm's strict linking broke `nest new`'s automatic schematics resolution
  (`Collection "@nestjs/schematics" cannot be resolved"`), because the
  Nest CLI expects to reach `@nestjs/schematics` via npm-style hoisting
  that pnpm doesn't do. Worked around by scaffolding once with
  `npx @nestjs/cli new` (npm-based, so resolution worked), then
  continuing all dependency management with pnpm from there.
- pnpm also blocks postinstall build scripts by default for security
  (supply-chain protection) confirmed via `pnpm approve-builds`.