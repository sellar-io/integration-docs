# Sellar Integrations API

The API specification can be found here: [Sellar Integration API](https://sellar-io.github.io/integration-docs/).

## Authentication

All requests need to include the `Bearer` auth header which includes an activate Supplier integration token.

```
Authorization: Bearer SLR.RmUyNi4yKipk...
```

Sellar API tokens start with `SLR.xyz` and can be activated from a Supliers settings page.

We use this token to determine the permissions for the operation you are making the request on where we return a `401` if the action is forbidden for that Supplier.

#### Token Invalidation

API keys have a long life span up to 10 years but in some cases tokens can be invalidated and re-activated to cover security cases where a token needs to be refreshed.

In those instances the previous token will no longer work and most operations will start to return a `401` error. To remedy this the Supplier will need to go back through the integration steps with you to store their refreshed token.

## Operations

Let's dig into some of the core operations that are supported through integrations and what to be mindful of.

### Orders

Keeping orders in sync can be done through order updates and reading individual orders.

##### Order State Transitions

Orders generally follow a transition of states once placed:

- Confirmed
- Paid
- Dispatched
- Delivered

Once an order has been confirmed it can marked as paid, dispatched and delivered.

Orders can also be:

- Cancelled
- Voided

These states signify an order cancellation state.
### Webhooks

Webhooks event types currently supported:

```
order.created
order.updated
```

Any URLs subscribed to these events will recieve events as they occur.


