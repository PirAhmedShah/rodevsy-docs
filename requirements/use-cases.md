# 📖 Use Case Specifications

---

## UC-001: [Use Case Name, e.g., "Submit Purchase Order"]

- **Use Case ID:** UC-001
- **Primary Actor:** [e.g., "Purchasing Manager"]
- **Goal:** (What the actor wants to achieve)
- **Preconditions:** (What must be true *before* this use case starts)
  - 1. Actor is logged in.
  - 2. Actor has "Purchaser" permissions.
- **Trigger:** (The event that initiates the use case)

### Main Success Scenario (Basic Flow)
1. Actor navigates to the "New Order" page.
2. System displays the order form.
3. Actor fills in "Supplier," "Line Items," and "Delivery Date."
4. Actor clicks "Submit Order."
5. System validates the order details.
6. System creates the Purchase Order with a "Pending Approval" status.
7. System displays a "Success" message with the new PO number.

### Extensions (Alternate Flows)
- **3a. Actor adds a new supplier:**
  - 1. Actor clicks "Add New Supplier."
  - 2. ...
- **5a. Validation Fails:**
  - 1. System detects missing "Delivery Date."
  - 2. System displays an error message: "Delivery Date is required."
  - 3. The use case returns to Step 3.

### Exceptions (Error Flows)
- **6a. System database connection fails:**
  - 1. System logs the critical error.
  - 2. System displays a "An unexpected error occurred. Please try again later." message.
  - 3. The use case terminates.

---

## UC-002: [Use Case Name]
...
