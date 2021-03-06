---
title: API Reference

language_tabs:
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the GJH Mobile App API! 

This API serves both the Android and iOS mobile app, and is responsible for serving the products, customer authentication/ creation and payment logic.

# Authentication

GJH uses a secret token to allow access to the API. This secret token is meant to be hardcoded in the client app, and passed in the HTTP Headers with every request.

GJH expects for the secret token to be included in all API requests to the server in a header that looks like the following:

`X-APP-SECRET: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal secret token.
</aside>

Also, some endpoints require an `auth_token` from a signed in customer.

For the most part, an anonymous user can browse the products and even add items to their cart. But the checkout flow will require an authenticated user with the `auth_token` parameter like so:

`auth_token: gwuy1hj21b5hjb3jkbjk6n1k`

<aside class="notice">
You can get an <code>auth_token</code> for a customer by making a POST request to the /sessions endpoint which creates a new session and auth token for the customer. This will be explained in detail below.
</aside>

# Segmentation by Cities

You must pass in a `city_id` parameter for most requests.

When the app is first launched, you should prompt the user to select their current city by retrieving the values of all valid cities and their ids from the `/countries` endpoint.

Depending on the `city_id`, the app will display different categories, subcategories and products all together. The user can change the current city they are in from the settings panel.

A good idea is to cache this `city_id` locally to send with every request as a default parameter, and only update it when the user changes the city they are in.t.

GJH expects for the `city_id` to be included in all API requests to the server in a query param that looks like the following:

`city_id=1`

<aside class="notice">
The only API endpoint which does not require the `city_id` parameter is the /countries endpoint, as it's from there that you get all the ids for the different cities.
</aside>

# Countries

## Get All Countries


```shell
curl "http://gjh-app.herokuapp.com/api/v2/countries"
  -H "X-APP-SECRET: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
    "countries": [
        {
            "id": 1,
            "name": "Australia",
            "cities": [
                {
                    "id": 1,
                    "name": "Perth"
                },
                {
                    "id": 2,
                    "name": "Melbourne"
                }
            ]
        },
        {
          "id": 2,
          "name": "Indonesia",
          "cities": [
              {
                "id": 3,
                "name": "Jakarta"
              },
              {
                "id": 4,
                "name": "Bandung"
              }
          ]
        }
    ]
}
```

This endpoint retrieves all countries, and their cities. A city belongs to a country.

### HTTP Request

`GET http://gjh-app.herokuapp.com/api/v2/countries`


<aside class="success">
This endpoint doesn't require the customer to be authenticated, neither does it require the `city_id` query parameter. <br><br>However, it still requires the `X-APP-SECRET` header to prove that the request comes from the mobile app.
</aside>

# Launch

## Get All Information To Display On Launch

```shell
curl "http://gjh-app.herokuapp.com/api/v2/launch?city_id=1"
  -H "X-APP-SECRET: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
    "result": {
        "categories": [
            {
                "id": 1,
                "name": "Clothing",
                "is_active": true,
                "subcategories": [
                    {
                        "id": 1,
                        "name": "Tops",
                        "category_id": 1,
                        "is_active": true
                    },
                    {
                        "id": 2,
                        "name": "Socks",
                        "category_id": 1,
                        "is_active": true
                    }
                ]
            },
            {
                "id": 2,
                "name": "Gadgets",
                "is_active": true,
                "subcategories": [
                    {
                        "id": 3,
                        "name": "Smart Phones",
                        "category_id": 2,
                        "is_active": true
                    },
                    {
                        "id": 4,
                        "name": "Rice Cookers",
                        "category_id": 2,
                        "is_active": true
                    }
                ]
            }
        ],
        "collections": [
            {
                "id": 1,
                "name": "Latest Summer Fashion",
                "city_id": 1,
                "collection_image": "http://s3.amazonaws.com/summer.png"
            }
        ]
    }
}
```

This endpoint retrieves all the information required to show on the home page when the user first enters the app after selecting their city.

You have to pass in the `city_id` parameter for the launch API to return the relevant categories and collections for the relevant city.

All the products are lazy loaded, and only displayed after the user selects them on the sidebar, so you only have to load the categories, sub-categories and collections on first launch.

### HTTP Request

`GET http://gjh-app.herokuapp.com/api/v2/launch?city_id={:id}`

<aside class="success">
This endpoint doesn't require the customer to be authenticated.
</aside>


# Subcategories

## Get All Products For A Particular Subcategory

```shell
curl "http://gjh-app.herokuapp.com/api/v2/subcategories/1?city_id=1"
  -H "X-APP-SECRET: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
    "subcategory": {
        "id": 1,
        "name": "Tops",
        "products": [
            {
                "id": 1,
                "country_id": 1,
                "city_id": 1,
                "subcategory_id": 1,
                "price": "80.0",
                "description": "This is a top for crazy people. haha",
                "sku": "CrazyTop001",
                "name": "Crazy Top",
                "current_inventory": 23,
                "is_active": true,
                "thumbnail_image": "http://s3.amazonaws.com/image.png",
                "product_images":[
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png"
                ]
            },
            {
                "id": 2,
                "country_id": 1,
                "city_id": 1,
                "subcategory_id": 1,
                "price": "80.0",
                "description": "This is a top for funky people. haha",
                "sku": "FunkyTop002",
                "name": "Funky Top",
                "current_inventory": 10,
                "is_active": true,
                "thumbnail_image": "http://s3.amazonaws.com/image.png",
                "product_images":[
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png"
                ]
            }
        ]
    }
}

```

This endpoint retrieves all the products for a selected subcategory, and is normally triggered when the user selects a subcategory from the sidebar.

### HTTP Request

`GET http://gjh-app.herokuapp.com/api/v2/subcategories/{:subcategory_id}?city_id={:city_id}`

<aside class="success">
This endpoint doesn't require the customer to be authenticated.
</aside>

# Collections

## Get All Products For A Particular Collection

```shell
curl "http://gjh-app.herokuapp.com/api/v2/collections/1?city_id=1"
  -H "X-APP-SECRET: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
    "collection": {
        "id": 1,
        "name": "Latest Summer Fashion",
        "collection_image": "http://s3.amazonaws.com/summer.png",
        "products": [
            {
                "id": 1,
                "city": { "id": 1, "name": "Perth", "country_id": 1, "description": "Perth has pearls", "is_active":true},
                "subcategory_id": 1,
                "price": "80.0",
                "description": "This is a top for crazy people. haha",
                "sku": "CrazyTop001",
                "name": "Crazy Top",
                "current_inventory": 23,
                "is_active": true,
                "thumbnail_image": "http://s3.amazonaws.com/image.png",
                "product_images":[
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png"
                ]
            },
            {
                "id": 2,
                "city": { "id": 1, "name": "Perth", "country_id": 1, "description": "Perth has pearls", "is_active":true},
                "subcategory_id": 1,
                "price": "80.0",
                "description": "This is a top for funky people. haha",
                "sku": "FunkyTop002",
                "name": "Funky Top",
                "current_inventory": 10,
                "is_active": true,
                "thumbnail_image": "http://s3.amazonaws.com/image.png",
                "product_images":[
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png"
                ]
            }
        ]
    }
}

```

This endpoint retrieves all the products for a selected collection, and is normally triggered when the user selects a collection from the main app view.

### HTTP Request

`GET http://gjh-app.herokuapp.com/api/v2/collections/{:collection_id}?city_id={:city_id}`

<aside class="success">
This endpoint doesn't require the customer to be authenticated.
</aside>



# Products

## Get A Single Product

```shell
curl "http://gjh-app.herokuapp.com/api/v2/products/1?city_id=1"
  -H "X-APP-SECRET: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
    "product": {
        "id": 1,
        "name": "Crazy Top",
        "sku": "CrazyTop001",
        "description": "This is a top for crazy people. haha",
        "price": "20.0",
        "is_active": true,
        "current_inventory": 0,
        "city": {
            "id": 1,
            "name": "Perth",
            "country_id": 1,
            "description": "Perth has pearls",
            "is_active": true,
            "created_at": "2015-05-21T16:19:45.262+08:00",
            "updated_at": "2015-05-21T16:19:45.262+08:00"
        },
        "thumbnail_image": "http://s3.amazonaws.com/image.png",
        "product_image":[
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png",
                  "http://s3.amazonaws.com/image.png"
        ],
        "created_at": "2015-05-21T16:31:37.323+08:00"
    }
}

```

This endpoint retrieves a single product, and can be used when checking the inventory of the product. By right, all of the details are returned in the subcategories endpoint, so you do not need to hit this endpoint separately to retrieve product details when entering the product page.

### HTTP Request

`GET http://gjh-app.herokuapp.com/api/v2/products/{:product_id}?city_id={:city_id}`

<aside class="success">
This endpoint doesn't require the customer to be authenticated.
</aside>


# Sessions

## Sign In For Customer

This endpoint returns an authentication token used to identify the signed in Customer. This authentication token must be used when requesting for the Customer's addresses, and to complete the checkout process.

The response returns the customer details, and more importantly, the `auth_token`, which must cached locally and used for any requests requiring customer authentication until the customer logs out.

### HTTP Request

`POST http://gjh-app.herokuapp.com/api/v2/sessions`

### URL Parameters

Parameter | Description
--------- | -----------
session["email"] | The email of the customer
session["password"] | The password of customer

<aside class="success">
This endpoint doesn't require the customer to be authenticated.
</aside>

# Customers

## Sign Up For Customer

This endpoint allows a new user to sign up.

Upon successful sign-up, the response returns the customer details, and automatically logs the customer in, and returns the `auth_token`, which must cached locally and used for any requests requiring customer authentication until the customer logs out.

### HTTP Request

`POST http://gjh-app.herokuapp.com/api/v2/customers`

### URL Parameters

Parameter | Description
--------- | -----------
customer["email"] | Email address. Required. `string`
customer["password"] | Password. Required. `string`
customer["password_confirmation"] | Password confirmation. Required. `string`
customer["first_name"] | First name. Required. `string`
customer["last_name"] | Last name. Required. `string`
customer["gender"] | 0 is for Male, 1 is for Female. Required. `integer`
customer["birthday"] | The birthday in YYYY-MM-DD format. Required.`date`
customer["mobile_number"] | The mobile number with country code. Required. `string`
customer["nationality"] | Their nationality. Required. `string`
customer["country"] | Their country. Required. `string`
customer["city"] | Their city. Required. `string`
customer["postal_code"] | Their postal/ZIP code. Required. `string`
customer["address"] | Their full address. Required. `string`
customer["unit_number"] | Unit number, if any. Optional. `string`
customer["telephone_number"] | Telephone number, if any. Optional. `string`

<aside class="success">
This endpoint doesn't require the customer to be authenticated.
</aside>


# Customer Shipping Address

## Retrieve All Customer Shipping Addresses

This endpoint retrieves all stored shipping addresses for a customer.

You can show all the shipping addresses in the Select Shipping Address page. By default, upon creating an account, the customer will have 1 default shipping address which is populated from their original sign up details. They can also have the option to add more shipping addresses using the CREATE endpoint.

### HTTP Request

`GET http://gjh-app.herokuapp.com/api/v2/customer_shipping_addresses`


<aside class="warning">
This endpoint requires the customer to be authenticated. You must thus pass the <code>auth_token</code> together with your request.
</aside>

## Create New Customer Shipping Addresses

This endpoint allows the customer to create a new shipping address.


### HTTP Request

`POST http://gjh-app.herokuapp.com/api/v2/customer_shipping_addresses`

### URL Parameters

Parameter | Description
--------- | -----------
customer_shipping_address["first_name"] | First name. Required. `string`
customer_shipping_address["last_name"] | Last name. Required. `string`
customer_shipping_address["mobile_number"] | The mobile number with country code. Required. `string`
customer_shipping_address["postal_code"] | Their postal/ZIP code. Required. `string`
customer_shipping_address["address"] | Their full address. Required. `string`
customer_shipping_address["unit_number"] | Unit number, if any. Optional. `string`
customer_shipping_address["telephone_number"] | Telephone number, if any. Optional. `string`


<aside class="warning">
This endpoint requires the customer to be authenticated. You must thus pass the <code>auth_token</code> together with your request.
</aside>

## Edit Customer Shipping Addresses

This endpoint allows a new user to sign up.

Upon successful sign-up, the response returns the customer details, and automatically logs the customer in, and returns the `auth_token`, which must cached locally and used for any requests requiring customer authentication until the customer logs out.

### HTTP Request

`PUT http://gjh-app.herokuapp.com/api/v2/customer_shipping_addresses/{:customer_shipping_address_id}`

### URL Parameters

Parameter | Description
--------- | -----------
customer_shipping_address["first_name"] | First name. Required. `string`
customer_shipping_address["last_name"] | Last name. Required. `string`
customer_shipping_address["mobile_number"] | The mobile number with country code. Required. `string`
customer_shipping_address["postal_code"] | Their postal/ZIP code. Required. `string`
customer_shipping_address["address"] | Their full address. Required. `string`
customer_shipping_address["unit_number"] | Unit number, if any. Optional. `string`
customer_shipping_address["telephone_number"] | Telephone number, if any. Optional. `string`


<aside class="warning">
This endpoint requires the customer to be authenticated. You must thus pass the <code>auth_token</code> together with your request.
</aside>

## Destroy Existing Customer Shipping Addresses

This endpoint allows a new user to sign up.

Upon successful sign-up, the response returns the customer details, and automatically logs the customer in, and returns the `auth_token`, which must cached locally and used for any requests requiring customer authentication until the customer logs out.

### HTTP Request

`DELETE http://gjh-app.herokuapp.com/api/v2/customer_shipping_addresses/{:customer_shipping_address_id}`


<aside class="warning">
This endpoint requires the customer to be authenticated. You must thus pass the <code>auth_token</code> together with your request.
</aside>



# Payments

## Request for a Braintree Payment Client Token

This endpoint requests a payment client token from the server, to be used for creating and instantiating the Braintree API instance.

You will receive a `client_token` hash from the server response.

This client_token is NOT meant to be cached and stored long term. You should regenerate it as much as possible without adversely affecting the user experience (requires 2-3 seconds to load).

A good idea is to request for the client token and store it locally temporarily, after a user logs in. Thereafter, if they are constantly logged in, you can also request for a new `client_token` each time they hit the Select Payment method page. 

This is important, because Braintree does not allow for reuse of client tokens, and will invalidate client tokens after a set amount of time, so it's important to keep them fresh.

Refer to - https://developers.braintreepayments.com/android+ruby/start/overview for more information. I can also explain more to you over the phone, and update this documentation to be more comprehensive when I have the time..

### HTTP Request

`POST http://gjh-app.herokuapp.com/api/v2/payments/client_token`

<aside class="warning">
This endpoint requires the customer to be authenticated. You must thus pass the <code>auth_token</code> together with your request.
</aside>


## Complete and Submit a Purchase

```shell
curl "http://gjh-app.herokuapp.com/api/v2/payments/purchases"
  -H "X-APP-SECRET: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
    "order_reference": "AU-0000001",
    "status": 1
}

```

This endpoint completes the purchase with Braintree.

It requires 3 parameters -

1. `payment_method_nonce` - a cryptographic hash representing the credit card info generated during the checkout process.
2. `shipping_address_id` - an integer, that represents the specific shipping address the customer has on file with us.
3. `products` - an array of hashes, with the product id and the quantity of each product.
4. `city_id` - the current city the customer is in.

If the purchase is successful, and the customer's credit card is successfully charged, you will receive an `order_reference` number in the server response. 

You can show this `order_reference` number in the order success/ receipt screen.

### HTTP Request

`POST http://gjh-app.herokuapp.com/api/v2/payments/purchases`

### URL Parameters

Parameter | Description
--------- | -----------
payment_method_nonce | A string that is generated from the Braintree client SDK after the customer has selected their payment method. `string`
shipping_address_id | The shipping address id that the customer selected in the previous screen. `integer`
products | An array of product_id's and the quantity. `array`
city_id | Current city the customer is in `integer`

<aside class="warning">
This endpoint requires the customer to be authenticated. You must thus pass the <code>auth_token</code> together with your request.
</aside>

