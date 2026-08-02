---
name: Look up and update a repair order
description: Search repair orders, fetch one by id, and update its customer/advisor details over the TruVideo Platform API.
api: openapi/truvideo-openapi.yml
operations:
  - authLogin
  - searchRepairOrders
  - getRepairOrder
  - updateRepairOrder
---

# Look up and update a repair order

## Steps

1. **Authenticate.** `authLogin` (`POST /api/v2/authentication/login`) → JWT;
   send `Authorization: Bearer <token>` thereafter.

2. **Search.** Call `searchRepairOrders`
   (`GET /api/v2/{accountId}/repair-order/search`) with `searchTerm` (matches
   customer first/last name, job service number, dealer name), optional
   `status`, and a `creationDateFrom`/`creationDateTo` epoch-millis window.

3. **Fetch one.** Call `getRepairOrder`
   (`GET /api/v2/{accountId}/repair-order/{orderId}`) to read the full order
   including nested `dealer`, `advisor`, and `customer`.

4. **Update.** Call `updateRepairOrder`
   (`PUT /api/v2/{accountId}/repair-order/{repairOrderId}`) with any of
   `customerName`, `customerLastName`, `mobileNumber`, `email`, `serviceAdvisor`.

## Conventions & rules

- **Auth:** JWT bearer (see `authentication/truvideo-authentication.yml`).
- **Account scoping:** `{accountId}` (dealer id) on every path.
- **PUT is a full update** — send the fields you intend to set.
- **Dates** in filters and on the order are epoch-millis integers.
- Data relationships: an order references a dealer, an advisor, and a customer
  (see `data-model/truvideo-data-model.yml`).
