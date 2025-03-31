Create the following API

# API Routes:

## /authentication
Generates or renew tokens to be used in the API
- To generate the tokens you need to send user and password
- Token need a expiration time (1 HOUR)

## /house
CRUD for houses
DB Fields:
- Id
- Adress
- Type: STANDARD|ECONOMIC|INDUSTRY
- Country - EX: UK|ES
- created_at
- updated_at

## /consumption
Stores hourly energy consumption from the house  

### API body
```json
{
    "house_id" : 1,
    "consume_datetime" : "2025-03-31T22:00:00+00:00",
    "watts" : 5,
}
```

### Price calculation
- If the house type is ECONOMIC apply a 15% discount
- If the house type is INDUSTRY apply a 15% fee
- If the house country is ES apply a 15% fee
  - Beteween 00:00 - 11:00 watts cost is 1
  - Beteween 11:01 - 16:59 watts cost is 1.5
  - Beteween 17:01 - 23:59 watts cost is 4
- If the house country is UK apply a 10% fee
  - Beteween 01:00 - 12:00 watts cost is 1
  - Beteween 12:01 - 17:59 watts cost is 1.5
  - Beteween 18:01 - 00:59 watts cost is 4
- If `consume_datetime` is a daylight saving date apply a 10% discount else a 20% Fee
- `consume_datetime` is always in UTC, consider Timezone this when calculating date for other countries

## /data_view
Shows all metrics for selected date

### Input
```json
{
    "house_id" : 1,
    "start_date" : "2025-03-31T22:00:00+00:00",
    "end_date" : "2025-04-22T22:00:00+00:00",
}
```

### Expected Outupt
```json
{
    "house" : {
        "id":1,
        "country": "UK",
    },
    "consumption" : [
        {
        "consume_datetime" : "2025-03-31T22:00:00+00:00",
        "watts" : 5,
        "price" : 10,
        },
        {
        "consume_datetime" : "2025-03-31T22:00:00+00:00",
        "watts" : 5,
        "price" : 10,
        }
    ],
    "metrics": {
        "total_price": 555,
        "avg_price": "watts",
        "biggest_price": 5,
        "biggest_date_price": "2025-03-31T22:00:00+00:00",
    }
   
}
```

## General Flow
- Each request, except `/authentication` needs auth

# Notes
Use the correct Status code and REST verbs for each route