---
name: Send a repair-order video to a customer
description: Create a repair order, attach a technician video, and notify the customer over the TruVideo Platform API.
api: openapi/truvideo-openapi.yml
operations:
  - authLogin
  - createRepairOrder
  - uploadFile
  - createVideo
  - createMessage
---

# Send a repair-order video to a customer

This skill covers the marquee TruVideo flow: a service advisor captures a
technician video for a repair order and shares it with the customer.

## Prerequisites

- A TruVideo account and an `accountId` (dealer id).
- Credentials (email + password) for `authLogin`.

## Steps

1. **Authenticate.** Call `authLogin` (`POST /api/v2/authentication/login`) with
   `email` and `password`. Store the returned JWT and send it as
   `Authorization: Bearer <token>` on every subsequent request.

2. **Create the repair order.** Call `createRepairOrder`
   (`POST /api/v2/{accountId}/repair-order`) with `number`, `customerName`,
   `customerLastName`, `mobileNumber`, `email`, and `sendNotifications`. Capture
   the returned order `id`.

3. **Upload the video file.** Call `uploadFile` (`POST /files`) as a multipart
   request. Capture the returned `uuid` and `url`.

4. **Create the video record.** Call `createVideo`
   (`POST /api/v2/{accountId}/videos`) with `title`, `description`, the `url`
   from step 3, `thumbnail`, `length`, `uploaderId`, and the `orderId` from
   step 2.

5. **Notify the customer.** Call `createMessage`
   (`POST /api/v2/{accountId}/messages`) with `conversationId: null` (to start a
   new conversation), `source: CONVERSATION`, `order` = the order id, `mobile` =
   the customer mobile, and a `message`. Expect `203` with a `Location` header
   pointing to the conversation.

## Conventions & rules

- **Auth:** JWT bearer on all calls except `authLogin` (see
  `authentication/truvideo-authentication.yml`).
- **Account scoping:** every path carries `{accountId}`.
- **No idempotency keys** are supported — do not retry `createRepairOrder` or
  `createVideo` blindly; look up first if a retry is needed.
- **Timestamps** on order/video objects are epoch-millis integers.
