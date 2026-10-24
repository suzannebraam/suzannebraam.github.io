# API Reference: GET /v2/properties/search

The `properties/search` endpoint retrieves a filtered list of active real estate property listings from the global database. 

Mobile apps and listing websites use this endpoint to populate property search grids, map views, and filterable housing catalog layouts.

---

## Request Protocol

### Endpoint URL
`https://prophub-saas.com`

### HTTP Method
`GET`

### Authentication
Include your platform API bearer token in your request header:
```http
Authorization: Bearer YOUR_ORGANIZATION_TOKEN
```

---

## Query Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `city` | String | **Yes** | The target city name for the search. Case-insensitive. (e.g., `Waterloo`). |
| `max_price` | Integer | No | The maximum listing price allowed in the results, expressed in USD/CAD. |
| `min_bedrooms` | Integer | No | The minimum number of bedrooms required. Accepted values: `1` to `10`. |
| `status` | String | No | Filter by listing status. Accepted values: `active`, `pending`, `sold`. *Default: `active`*. |

---

## Sample Request

This example requests all active property listings in Waterloo with a maximum budget of $1,000,000 and at least 3 bedrooms.

```bash
curl -X GET "https://prophub-saas.com?city=Waterloo&max_price=1000000&min_bedrooms=3&status=active" \
  -H "Authorization: Bearer ph_live_token882"
```

---

## Response Payloads

### Success Response (200 OK)
A successful request returns a JSON array containing matching properties, their pricing, and layout specifics.

```json
{
  "status": "success",
  "results_count": 1,
  "properties": [
    {
      "listing_id": "prop_99182A",
      "address": {
        "street": "123 King Street North",
        "city": "Waterloo",
        "state_province": "Ontario",
        "postal_code": "N2J 2E4"
      },
      "pricing": {
        "list_price": 799000,
        "currency": "CAD"
      },
      "features": {
        "bedrooms": 3,
        "bathrooms": 2.5,
        "square_feet": 1850,
        "has_garage": true
      }
    }
  ]
}
```

### Response Field Definitions

Within each object in the `properties` list:
* `listing_id` (String): The unique alphanumeric identifier assigned by the regional real estate board.
* `pricing.list_price` (Integer): The current asking price for the property, rounded to the nearest whole dollar.
* `features.bathrooms` (Float): The number of bathrooms. Fractional values indicate partial bathrooms (e.g., `2.5` represents two full baths and one half-bath).
* `features.has_garage` (Boolean): Indicates whether the property includes attached or detached parking structures (`true` or `false`).

---

## Error Handling

### Common Error Codes
* **400 Bad Request:** Missing the mandatory `city` parameter or submitting a text string into a numeric field (like `max_price=cheap`).

### Sample Error Response (400 Bad Request)
```json
{
  "error": {
    "code": "missing_required_parameter",
    "message": "The 'city' parameter is required. Please specify a geographic location to search.",
    "documentation_url": "https://prophub-saas.com"
  }
}
```
