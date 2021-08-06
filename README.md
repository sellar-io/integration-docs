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

These states signify an order cancellation state. Cancelled is when an order has been cancelled by the supplier. In practice this means two things it could either be denied or cancelled later on due to another reason. We register both as cancelled. FYI there is a deniedAt timestamp but it's not currently in use. Voided is only used on Sellar Pay Credit (Stripe) when order invoices are voided manually on Stripe.

#### Will an order always have payment taken via Sellar, or may this be handled directly between the buyer & seller?

Three different types of payment: 'checkout', 'stripe', 'manual'. Checkout means they pay before they place the order. Stripe means they get an invoice handled through Stripe. If its manual it usually means they've taken the payment processing outside of Sellar either by bank transfer or via a gateway they manage e.g Xero, PayPal etc. From your side when updating an order as paid: If its manual you can update the paidAt timestamp on the order and we will mark the order status as paid. The other two payment types (checkout and stripe) are handled by us so there will be no need to update the properties here - in practice we will throw an error if this is attempted as well.

#### What is difference between Dispatched and Delivered, should we update orders with tracking information?

Its fine to leave an order in a dispatched state (only updating the dispatchedAt timestamp) but if you do have extra information around delivery then we can hold that too.

#### How do deliveryPlannedAt and dispatchPlannedAt get set? 

The supplier sets that when they confirm the order. These timestamps can be changed after they are set initially e.g once they confirm the delivery dates with the courier.

#### Is the order price the total for all lines, plus delivery on the order?

The price includes all charges (products, delivery and VAT) but until the order is confirmed that price can change. Because the order can be edited in regards to the product and delivery charges by the supplier and retailer when it is pending before it has been confirmed by the supplier.

#### How does the product stock change after an order is placed?

When an order is placed, we reduce the stock level on the product but if the order is cancelled the stock gets put back into the available stock.
### Webhooks

Webhooks event types currently supported:

```
order.created
order.updated
```

Any URLs subscribed to these events will recieve events as they occur.


