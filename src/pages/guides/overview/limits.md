---
title: Rate Limits - Adobe Cloud Storage and Collaboration API
description: Adobe Cloud Storage and Collaboration API rate limits
keywords:
  - Adobe Cloud Storage and Collaboration
  - Adobe Cloud Storage and Collaboration API
  - Adobe Cloud Storage
  - Creative production workflows   
  - Adobe workflows
  - Cloud Storage endpoints
  - Cloud Storage authentication and autorization
  - Rate Limits
  - Throttling
contributors:
  - https://github.com/michael-hodgson
  - https://github.com/lmjose
layout: none
---

# Rate Limits

The Adobe Cloud Storage and Collaboration API enforces default rate limits on the volume and frequency of API calls.

## Summary of Rate Limits

Each client ID or API key is subject to the following limit: 20 requests per second (RPS).

## Purpose of Rate Limits

API rate limits are a standard practice designed to:

- **Prevent abuse:** Protects APIs from being overwhelmed by excessive requests.
- **Protect against downtime:** Reduces the risk of service interruptions.
- **Control costs:** Helps manage resource consumption and associated expenses.

## Handling Rate Limit Errors

When the client-specific or global access limit is exceeded, the API responds with an _HTTP 429 Too Many Requests_ error. This response includes a Retry-After header, which indicates the minimum time the client must wait before making another request. For detailed specifications, refer to [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-7.1.3).

To address this error, consider the following actions:

- Review your usage and reduce unnecessary requests.
- Implement retry logic using [retry-after](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Retry-After) [HTTP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Retry-After) or an [exponential backoff strategy](https://en.wikipedia.org/wiki/Exponential_backoff).
