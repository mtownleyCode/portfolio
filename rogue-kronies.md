---
layout: default
title: Rogue Kronies E-Commerce Platform
permalink: /rogue-kronies/
---

# Case Study: Rogue Kronies E-Commerce Platform

[← Back to all projects](/portfolio/)

## Overview

Rogue Kronies is a small e-commerce business selling custom-art merchandise. I designed and built the business's e-commerce platform from the ground up, and have recently extended it using AI-assisted development. Made-to-order tumblers are live in production today; sized apparel (starting with t-shirts) is fully built and tested, pending the business's physical inventory arriving.

## The Challenge

The business needed a real e-commerce platform: a public storefront, secure payment processing, and (as the product line grew beyond tumblers into sized apparel) a proper product and inventory system — without managing servers directly.

## Architecture

- Frontend: React (Vite)
- Backend: ASP.NET Core 8 Web API, containerized (Docker), deployed on AWS App Runner
- Database: DynamoDB (single-table design)
- Payments: Stripe (Checkout + webhooks)
- Infrastructure: AWS (ECR, S3, Secrets Manager, Route 53)
- Deployment: Automated CI/CD (GitHub Actions, OIDC-federated AWS auth — no long-lived credentials)

## My Role vs. AI's Role

I manually designed and built the platform's original foundation — the frontend, the backend, the database structure, and the payment integration. More recently, I've used AI-assisted development tools (Claude Code) to expand the platform, including a new product/inventory system (supporting size, color, and design variants) and setting up the automated CI/CD deployment pipeline.

The architectural decisions, data design, and technical judgment throughout are mine — every data model choice, the shipping cost model, and security tradeoffs were made directly, with AI implementing to explicit direction. The tooling has accelerated how fast I can build and iterate, not replaced the underlying engineering work.

## Technical Highlights

- **Atomic order fulfillment:** checkout is a single all-or-nothing DynamoDB transaction that decrements inventory and records the order together. If stock runs out mid-transaction (a real race condition once two customers can buy the last unit simultaneously), the order is flagged for manual review and an alert fires automatically — instead of either silently overselling or leaving a paid order in limbo.
- **Single-table DynamoDB schema** supporting three related entity types (products, size/color variants, and orders) through a shared partition-key pattern, with per-variant price overrides that fall back to the base product price when unset — avoiding data duplication across a growing product catalog. The product/variant model is designed to generalize across different product types without a schema redesign — some products carry variants like size or color, others (like a sticker) carry none at all.
- **CI/CD authenticates to AWS via GitHub's native OIDC identity provider** rather than long-lived access keys stored as GitHub secrets — each deploy is scoped to a narrowly-permissioned, short-lived role, and every production deploy is traceable to the exact commit that triggered it.

## Status

Tumblers are live and selling. The apparel product/inventory system is complete and tested; full rollout is pending the business's physical merchandise inventory arriving.
