# Order Book

> Requires Docker, Docker Compose, and recent versions of Maven & JDK (tested with [3.8.7](https://packages.debian.org/bookworm/maven) & [17.0.15](https://packages.debian.org/bookworm/default-jdk) on Debian)

## Usage
> 1. Replace <YOUR_API_KEY> in [GoogleMapsConfig.java](https://github.com/kk9uk/Order_Book/blob/main/app/src/main/java/kk9uk/OrderBook/config/GoogleMapsConfig.java) with your actual Google Maps API key (which supports Distance Matrix API)

> 2. Let start.sh cook

    ./start.sh

## API Specification

### Place order
- Method: `POST`
- URL path: `/orders`
- Request body:

  ```
  {
      "origin": ["START_LATITUDE", "START_LONGITUDE"],
      "destination": ["END_LATITUDE", "END_LONGITUDE"]
  }
  ```

- Response:

  - Header: `HTTP 200`
  - Body:
  
    ```
    {
        "id": <order_id>,
        "distance": <total_distance>,
        "status": "UNASSIGNED"
    }
    ```
    
  or  

  - Header: `HTTP 400`
  - Body:

    ```
    {
        "error": "ERROR_DESCRIPTION"
    }
    ```

- Remarks:

    - Coordinates in request must be an array of exactly **two** strings. The type shall only be strings, not integers or floats.
    - The latitude and longitude value of coordinates will be validated.
    - Order id in response is unique. It is an auto-incremental integer.
    - Distance in response is integer in meters.

### Take order
- Method: `PATCH`
- URL path: `/orders/:id`
- Request body:

  ```
  {
      "status": "TAKEN"
  }
  ```
  
- Response:

  - Header: `HTTP 200`
  - Body:
  
    ```
    {
        "status": "SUCCESS"
    }
    ```
    
  or

  - Header: `HTTP 400`
  - Body:
  
    ```
    {
        "error": "ERROR_DESCRIPTION"
    }
    ```

- Remarks:

    - An order can only be taken once.
    - When there are concurrent requests to take a same order, only one can take the order while the other will fail.

### Order list
- Method: `GET`
- Url path: `/orders?page=:page&limit=:limit`
- Response:

  - Header: `HTTP 200`
  - Body:
  
    ```
    [
        {
            "id": <order_id>,
            "distance": <total_distance>,
            "status": <ORDER_STATUS>
        },
        ...
    ]
    ```

  or

  - Header: `HTTP 400`
  - Body:

  ```
  {
      "error": "ERROR_DESCRIPTION"
  }
  ```

- Remarks:

    - Page number starts with 1.
    - The page and limit value will be validated.
    - If there is no result, an empty array json is returned as the response body.
