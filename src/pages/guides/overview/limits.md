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
 - Cloud Storage authentication and authorization
 - Rate Limits
 - Throttling
contributors:
- https://github.com/michael-hodgson
layout: none
---

# Rate Limits

The Adobe Cloud Storage and Collaboration API places default rate limits on the volume and frequency of API calls.

## Summary of Rate Limits

Our API imposes the following rate limits per client ID/API key:

- 25 requests per minute (RPM)

## What to Do If You Run Into Issues

When the client or global access limit is reached, further calls fail with HTTP error status 429 Too Many Requests. The Retry-After header is included in the 429 response, and provides the minimum amount of time that the client should wait until retrying. See [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-7.1.3) for full information. If you encounter this error, consider any of the following solutions:

- Review your usage and reduce unnecessary requests.
- Implement retry logic via a [retry-after](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Retry-After) [HTTP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Retry-After) or an [exponential backoff strategy](https://en.wikipedia.org/wiki/Exponential_backoff).

## Why do API rate limits exist?

Rate limits are standard practice that serve several important purposes, including:

- Preventing abuse: Protects APIs from being overwhelmed by excessive requests.
- Protecting against downtime: Reduces the risk of service interruptions.
- Controlling costs: Helps manage resource consumption and associated expenses.
