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

