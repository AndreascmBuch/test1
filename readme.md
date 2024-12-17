# Domain Model for Car Subscription Service

## Overview

The Car Subscription Service consists of multiple interconnected domains, each responsible for a specific aspect of the system. Below is the refined domain model for the service, organizing the key components and their interactions.

---

## Domain Models and Responsibilities

### 1. **Customer Domain**
- **Purpose**: Handles all customer-related operations, including managing customer profiles and subscriptions.
- **Entity**: Customer
  - **Attributes**: 
    - `id`: Unique identifier for the customer.
    - `name`: Customer's full name.
    - `address`: Residential address.
    - `contact_information`: Phone number and email.
    - `payment_info`: Billing information for transactions.
  - **Behaviors**:
    - `create_subscription()`: Create a subscription for the customer.
    - `update_customer_info()`: Modify the customer's profile details.
    - `get_customer_details()`: Retrieve customer information.
- **Service**: 
  - Customer Service manages customer data and interactions via APIs.

### 2. **Subscription Domain**
- **Purpose**: Manages subscription agreements, billing, and contracts.
- **Entity**: Subscription
  - **Attributes**: 
    - `subscription_id`: Unique identifier for the subscription.
    - `customer_id`: The associated customer ID.
    - `car_id`: The rented car's ID.
    - `price`: Cost of the subscription.
    - `start_date`: Start date of the subscription.
    - `end_date`: End date of the subscription.
  - **Behaviors**:
    - `calculate_subscription_cost()`: Compute the total cost of the subscription.
    - `check_active_status()`: Verify if the subscription is currently active.
- **Service**: 
  - Subscription Service manages subscriptions, contracts, and billing periods via APIs.

### 3. **Car Domain**
- **Purpose**: Tracks the car inventory, availability, and rental statuses.
- **Entity**: Car
  - **Attributes**: 
    - `id`: Unique identifier for the car.
    - `brand`: Car manufacturer (e.g., Toyota, Tesla).
    - `model`: Car model (e.g., Corolla, Model S).
    - `mileage`: Distance traveled by the car.
    - `is_rented`: Boolean indicating rental status.
    - `has_damage`: Boolean indicating if the car has reported damage.
  - **Behaviors**:
    - `mark_as_rented()`: Update the rental status of the car.
    - `report_damage()`: Record damage reported for the car.
    - `update_mileage()`: Update the car's mileage.
- **Service**: 
  - Car Service handles car details, availability, and associations with subscriptions via APIs.

### 4. **Damage Domain**
- **Purpose**: Manages damage reporting and cost calculation for cars.
- **Entity**: DamageReport
  - **Attributes**: 
    - `damage_id`: Unique identifier for the damage report.
    - `car_id`: The ID of the car associated with the report.
    - `date_reported`: Date the damage was reported.
    - `damage_types`: Specific types of damages (e.g., `tire_damage`, `engine_damage`).
  - **Behaviors**:
    - `record_damage()`: Log a new damage report for a car.
    - `get_damage_details()`: Retrieve details of a specific damage report.
    - `calculate_damage_cost()`: Compute repair costs based on damage.
- **Service**: 
  - Damage Service logs and manages damage entries via APIs.

### 5. **Damage Calculator Domain**
- **Purpose**: Aggregates data to compute the total cost for subscriptions and damages.
- **Entity**: DamageCalculator
  - **Attributes**: 
    - `id`: Unique identifier for the calculation instance.
    - `customer_id`: Associated customer's ID.
    - `car_id`: Associated car's ID.
    - `total_damage_cost`: Total cost of damages.
    - `total_subscription_cost`: Total subscription fee.
    - `total_price`: Combined cost of subscription and damages.
  - **Behaviors**:
    - `calculate_total_cost()`: Compute the overall cost.
    - `generate_report()`: Generate a detailed cost report.
- **Service**: 
  - Damage Calculator Service aggregates subscription and damage data to provide total cost breakdowns.

---

## Domain Interactions and Integration

### Interaction Flow
1. **Customer Service** creates and maintains customer profiles.
2. **Subscription Service** links cars from the Car Service to customers, creating subscriptions.
3. **Car Service** manages car availability, rental status, and reports damages to the Damage Service.
4. **Damage Service** logs damage reports and provides data for cost calculations.
5. **Damage Calculator Service** aggregates data from the Subscription Service and Damage Service to calculate total costs for customers.

### Events and Communication
- **When a Subscription is created**:
  - The Subscription Service notifies the Car Service to mark the car as rented.
- **When Damage is reported**:
  - The Damage Service triggers an event for the Damage Calculator Service to update total costs.
- **Periodic Reports**:
  - Damage Calculator Service generates cost summaries combining subscription and damage fees.

---

## Domain Model Diagram with Relations

```plaintext
+-------------------+       +-------------------+       +-------------------+
| Customer Domain   |       | Subscription      |       | Car Domain        |
|                   |       | Domain            |       |                   |
|-------------------|       |-------------------|       |-------------------|
| - id              |       | - subscription_id |       | - id              |
| - name            |       | - customer_id     |       | - brand           |
| - address         |       | - car_id          |       | - model           |
| - contact_info    |       | - price           |       | - mileage         |
| - payment_info    |       | - start_date      |       | - is_rented       |
|-------------------|       | - end_date        |       | - has_damage      |
| + create_sub()    |       |-------------------|       |-------------------|
| + update_info()   |       | + calc_cost()     |       | + mark_as_rented()|
| + get_details()   |       | + check_status()  |       | + report_damage() |
|-------------------|       |-------------------|       | + update_mileage()|
| Microservice:     |       | Microservice:     |       | Microservice:     |
| Manages customer  |       | Handles           |       | Manages car       |
| data and          |       | subscriptions.    |       | details and       |
| interactions.     |       |                   |       | availability.     |
+-------------------+       +-------------------+       +-------------------+
         |                            |                        |  
         |                            |                        |  
         |   Subscriptions link       |       Cars are rented  |  
         |   Customers to Cars        |       and updated      |  
         |                            |                        |  
         |                            |                        v
+-------------------+       +-------------------+       +-------------------+
| Damage Domain     |       | Damage Calculator |       | (Event Handler)  |
|                   |       | Domain            |       |                 |
|-------------------|       |-------------------|       |-----------------|
| - damage_id       |       | - id              |       | - event_id      |
| - car_id          |       | - customer_id     |       | - car_id        |
| - date_reported   |       | - car_id          |       | - event_type    |
| - damage_types    |       | - total_damage    |       |-----------------|
|-------------------|       | - total_sub_cost  |       | Handles events  |
| + record_damage() |       | - total_price     |       | between services|
| + get_details()   |       |-------------------|       |                 |
| + calc_cost()     |       | + calc_total_cost()|      |                 |
|-------------------|       | + generate_report()|      |                 |
| Microservice:     |       |-------------------|       |                 |
| Logs damage       |       | Aggregates costs  |       |                 |
| entries for cars. |       | for damages and   |       |                 |
|                   |       | subscriptions.    |       |                 |
+-------------------+       +-------------------+       +-------------------+
```

