---
name: Triage and reply to customer conversations
description: Find open customer conversations, read their message history, and reply over the TruVideo Platform API.
api: openapi/truvideo-openapi.yml
operations:
  - authLogin
  - searchConversations
  - getConversation
  - createMessage
---

# Triage and reply to customer conversations

Use this skill to work a queue of customer messaging conversations tied to
repair orders.

## Steps

1. **Authenticate.** `authLogin` (`POST /api/v2/authentication/login`) → JWT;
   send `Authorization: Bearer <token>` thereafter.

2. **Find conversations.** Call `searchConversations`
   (`GET /api/v2/{accountId}/messages/conversations`) filtering by `status`
   (`ACTIVE`, `RESPONDED`, `CLOSED`), `orderId`, `customerId`, or `searchTerm`.
   Page with `page` and `size`.

3. **Read the thread.** For a conversation of interest, call `getConversation`
   (`GET /api/v2/{accountId}/messages/conversations/{conversationId}`) to load
   the paged `messages.content` history and `lastTextMessage`.

4. **Reply.** Call `createMessage` (`POST /api/v2/{accountId}/messages`) with the
   existing `conversationId` set (non-null appends to that conversation),
   `mobile`, and `message`. Expect `203` with a `Location` header.

## Conventions & rules

- **Auth:** JWT bearer (see `authentication/truvideo-authentication.yml`).
- **Pagination:** Spring-style Page envelope; `page` is zero-based, `size`
  defaults to 25 (see `conventions/truvideo-conventions.yml`).
- **Message timestamps** are ISO-8601 date-time strings; `direction` is
  `inbound` or `outbound`.
- Set `conversationId` to `null` only to start a NEW conversation.
