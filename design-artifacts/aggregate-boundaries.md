# Aggregate Boundaries

### Authorization Microservice
Login
  - username
  - password
  - user_aggregate_ID
  
### @User_aggregate
  - fname
  - lname
  - address
  - email (username)
  - phone
  - current_location
##### @Driver_aggregate
   - license
   - available (boolean)
   - rating

### @Location_aggregate
  - user_aggregate_ID
  - location
  - date
  
### @Trip_aggregate
  - user_aggregate_ID
  - source (address)
  - destination (address)
  - completed | created | start
##### @Estimate_Invoice
  - cost
  - List<Driver>
  - miles
  - duration
##### @Actual_Invoice
  - cost
  - List<Driver>
  - miles
  - duration
  
### @Bill_aggregate
  - trip_aggregate_ID
  - paid (timestamp)
  - created (timestamp)
  - payment_method
  
### @Payment_aggregate

  - user_aggregate_ID
  - type
  - preferred
  
### @Rating

  - user_aggregate_ID
  - driver_aggregate_ID
  - rate
  
get_location(user)
iff current_loc == null
  store_path_loc_cur(User, path_loc)
end_trip_cmd(trip)