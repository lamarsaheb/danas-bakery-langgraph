# Dana's Bakery — LangGraph Technical Specification

## 1. Objective
Build a fully functional bakery order-processing system using LangGraph in a Google Colab notebook.

The system must process customer orders, validate inputs, check inventory, suggest alternatives, calculate discounts and delivery charges, and generate friendly customer messages.

Use deterministic Python only. Do not use LLMs, API calls, API keys, or external AI services.

## 2. Input
The graph must accept orders using:

```python
result = graph.invoke({
    "item": "babka",
    "quantity": 2,
    "day": "Thursday",
    "city": "Jerusalem"
})
```

## 3. Product Catalog

| Product | Price (₪) | Initial Stock |
|---|---:|---:|
| Challah | 18 | 10 |
| Babka | 45 | 0 |
| Rugelach | 30 | 3 |
| Sourdough | 25 | 4 |

Croissants are discontinued and must be rejected.

## 4. Validation
- Accept only products in the active catalog.
- Quantity must be a positive integer.
- Reject zero, negative, invalid, or missing quantities.
- Reject unknown products.
- Reject malformed orders politely.
- Validate required input fields before processing.

## 5. Inventory and Substitution
Check whether the requested quantity is available.

If stock is insufficient, suggest the next product in this order:

1. Babka → Rugelach
2. Rugelach → Sourdough
3. Sourdough → Challah

Allow at most two alternative suggestions.

Each alternative must have enough stock to satisfy the entire requested quantity.

Use the same quantity and the alternative product's price.

If no acceptable alternative is available within two suggestions, reject and cancel the order.

If challah is unavailable, reject the order because no further alternative is specified.

Do not invent additional substitution rules.

## 6. Discounts
Use Avi's corrected pricing rule, which overrides Dana's earlier statement.

- Apply a 15% discount when the baked-goods subtotal is strictly greater than ₪150.
- Never apply a discount on Friday.
- Never discount delivery charges.
- Do not use the outdated 10% discount above ₪200 rule.

## 7. Delivery
- If the city is empty, use free pickup.
- If the city is Jerusalem, use delivery for ₪20.
- If the city is outside Jerusalem, change the order to free pickup and inform the customer.

## 8. Order Processing
Calculate:

1. Actual product sold
2. Quantity
3. Baked-goods subtotal
4. Applicable discount
5. Delivery charge
6. Final total

Use accurate monetary calculations and return a numeric total.

## 9. Customer Messages
Generate friendly, warm, personalized messages using deterministic Python templates.

Messages should explain relevant substitutions, delivery changes, discounts, successful orders, or rejections.

Do not call an AI model because the assignment explicitly prohibits LLM calls.

## 10. LangGraph Architecture
Use StateGraph with a TypedDict state.

Include nodes for:
- Input validation
- Inventory checking
- Substitution handling
- Discount calculation
- Delivery decision
- Final total calculation
- Customer message generation
- Rejection handling

Use conditional edges for validation, stock availability, substitution outcomes, and delivery decisions.

Maintain an ordered `steps` list recording the nodes and decisions traversed.

## 11. Required Output
Every invocation must return these six keys:

- `status`: "accepted" or "rejected"
- `item`: actual product sold
- `delivery`: "pickup" or "delivery"
- `total`: final payable amount including delivery
- `message`: customer-facing response
- `steps`: ordered processing history

For rejected orders, use `item = None`, `delivery = "pickup"`, and `total = 0`.

## 12. Graph Visualization
Use:

```python
display(Image(graph.get_graph().draw_mermaid_png()))
```

Display the graph in the Colab notebook.

## 13. Testing
Include at least six test orders, with enough additional cases to cover every branch.

Test:
- Valid available product
- Invalid product
- Invalid quantity
- Successful first substitution
- Successful second substitution
- Cancelled order after failed alternatives
- Discount above ₪150
- No discount at exactly ₪150
- Friday discount exclusion
- Jerusalem delivery
- Empty-city pickup
- Outside-Jerusalem pickup conversion

Use assertions to verify results and ensure that every output contains the six required keys.

## 14. Notebook Requirements
Create a clean, well-organized Google Colab notebook with:

1. Project introduction
2. Dependency installation
3. State definition
4. LangGraph nodes
5. Conditional routing
6. Graph compilation
7. Mermaid graph visualization
8. Test orders and results
9. Automated assertions
10. Short conclusion

The notebook must run from a fresh Colab session without API credentials.

## 15. Scope and Assumptions
- Ignore Instagram integration.
- Ignore unrelated personal details in the story.
- Treat the given inventory as the starting stock for independent test orders.
- Do not permanently reduce inventory between test invocations.
- Do not introduce customer confirmation or additional fields not provided by the form.
- Keep the implementation readable and suitable for an AI Agents course assignment.

## 16. Deliverable
Generate a complete `.ipynb` notebook suitable for Google Colab, implementing the specification above.
