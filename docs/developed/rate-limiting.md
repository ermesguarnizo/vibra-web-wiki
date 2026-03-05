---
id: rate-limiting
title: Rate Limiting
sidebar_label: Rate Limiting
---

# Rate Limiting in Dashboard API

## Description

This documentation describes the implementation of the rate limiting mechanism in Vibra API to protect endpoints against abuse and brute force attacks.

## Implementation

Rate limiting has been implemented using the `@nestjs/throttler` package which integrates perfectly with NestJS. The current configuration limits requests by IP address.

### Current Configuration

- **Request limit**: 20 requests
- **Time period**: 60 seconds (1 minute)

This means that each IP address can make a maximum of 20 requests per minute to any protected endpoint.

## File Structure

- `src/infrastructure/throttler/throttler.module.ts`: Contains the ThrottlerModule configuration.
- `src/infrastructure/throttler/throttler.guard.ts`: Implements the custom guard that applies rate limiting and handles exceptions.

## Client Response

When a client exceeds the request limit, they will receive a response with:

- **HTTP status code**: 429 (Too Many Requests)
- **Message**: "Too many requests, please try again later."
- **Timestamp**: Date and time when the limitation occurred

## Exclusions

Some endpoints are excluded from rate limiting:

- `/api`: Swagger documentation
- `/health`: Application health checks

## Customization

To modify the rate limiting configuration, edit the following values in `throttler.module.ts`:

```typescript
ThrottlerModule.forRoot([
  {
    ttl: 60000, // time in milliseconds (1 minute)
    limit: 20, // maximum number of requests in the ttl period
  },
]),
```

## Logging

When the request limit is exceeded, a warning message is logged with the IP address and the requested URL in the application logs.