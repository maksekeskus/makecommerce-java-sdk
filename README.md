# MakeCommerce Java SDK
## Table of Contents
1. [Introduction and overview](#introduction)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Adding JAR to Your Project](#adding-jar)
5. [Platform information](#platform)
6. [Simple example](#example)
7. [Documentation](#documentation)
    - [Get shop configuration](#get-shop-configuration)
    - [Get shop data](#get-shop-data)
    - [Get shop fees](#get-shop-fees)
    - [Get shop Account Statement](#get-shop-account-statement)
    - [Create transaction](#create-transaction)
    - [Get transaction](#get-transaction)
    - [Get transaction statement](#get-transaction-statement)
    - [Get transaction list](#get-transaction-list)
    - [Create payment](#create-payment)
    - [Create refund](#create-refund)
    - [Get refund](#get-refund)
    - [Get refunds list](#get-refunds-list)
    - [Get payment methods](#get-payment-methods)
    - [Add Merchant Data to transaction](#add-merchant-data)
    - [Get shipment destinations](#get-shipment-destinations)
    - [Create shipments](#create-shipments)
    - [Get label formats](#get-label-formats)
    - [Create labels](#create-labels)
8. [Contact us](#contact)

## Introduction and overview <a name="introduction"></a>
Welcome! In this page you can find a quick overview of the MakeCommerce Java SDK.

## Adding JAR to Your Project <a name="adding-jar"></a>
To include a JAR file in your project, follow these steps:

- Add the JAR file to a directory in the root of your project. We suggest using a libs directory for this purpose, but you can choose any other directory that suits your project structure.

- In your project's pom.xml file, add a new <dependency> inside the <dependencies> block:

```
<dependency>
    <groupId>ee.makecommerce</groupId>
    <artifactId>makecommerce-java</artifactId>
    <version>1.0.0</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/libs/makecommerce-java-1.0.0.jar</systemPath>
</dependency>
```

In this configuration, ${project.basedir} is a placeholder that Maven replaces with the path to the root of your 
project, and `/libs/makecommerce-java-1.0.0.jar` is the path to the JAR file relative to the root of your project.

Please adjust the path according to the actual location of your JAR file. For example, if you decide to put the JAR 
in a custom directory, the path should look like `${project.basedir}/custom/makecommerce-java-1.0.0.jar`.

## Platform information <a name="platform"></a>
Before proceeding, you need to create and populate the makecommerce-module.properties file located in the src/resources/ 
directory of your project. This file should specify the name and version of your platform/store.

Here is a template for the `makecommerce-module.properties` file:
```
platform.name=YourStoreName
platform.version=YourStoreVersion
```
Please replace YourStoreName and YourStoreVersion with your actual store's name and version respectively.

To help visualize the file structure, below is a diagram representing the placement of the JAR file, the `pom.xml`, 
and the `makecommerce-module.properties` file in your project:
```
YourProject/
│   pom.xml
│
└───libs/
│   │   makecommerce-java-1.0.0.jar
│   
└───src/
    └───main/
        └───java/
        └───resources/
            │   makecommerce-module.properties

```

## Simple example <a name="example"></a>
Create transaction
```
	Configuration testShop = new Configuration(
				"f7741ab2-7445-45f9-9af4-0d0408ef1e4c",
				() -> "pfOsGD9oPaFEILwqFLHEHkPf7vZz4j3t36nAcufP1abqT9l99koyuC1IWAOcBeqt",
				Environment.TEST,
				true);

	MakeCommerceService makeCommerceService = new MakeCommerceService(testShop);

	Customer customer = new Customer();
	customer.setCountry("ee");
	customer.setEmail("test@mail.com");
	customer.setIp("123.123.123.123");
	customer.setLocale("et");

	Transaction transaction = new Transaction();
	transaction.setAmount("75.95");
	transaction.setCurrency("EUR");
	transaction.setReference("123abc");

	CreateTransactionRequest request = new CreateTransactionRequest(transaction, customer);

	TransactionResource response = makeCommerceService.createTransaction(request);
```

## Documentation <a name="documentation"></a>
For an overview of the various components of Makecommerce Java SDK, see Our developer portal at https://developer.maksekeskus.ee/

### Get shop configuration <a name="get-shop-configuration"></a>
HTTP REQUEST `GET /v1/shop/configuration`

Method: `public ShopConfigurationResponse getShopConfiguration()`

Retrieves configuration of the Shop a json array that could be handy to cache on e-shop server side for the integration 
module. You could refresh this i.e. each hour or once a day.
Right now the json contains:
Payment methods available for the shop:
Use this to render selection of payment methods in checkout dialog. The list may change over time based on the 
commercial agreement and when payment channels are added or removed by MakeCommerce.
Channels are ordered by country, so you can use the order for UI.
We intend to make logos for the channels to be available on our server.

As input, we ask you to provide description of the integration environment - info about e-commerce platform and 
integration module.
We need this information for better problem resolution capability and for estimating impact of changes in our API.

### Get shop data <a name="get-shop-data"></a>
HTTP REQUEST `GET /v1/shop`

Method: `public GetShopResponse getShopInfo()`

The getShopInfo method is an API endpoint that retrieves the information for a specific shop. 
Upon receiving a GET request at the /v1/shop endpoint, this method processes the request and 
returns the data as a GetShopResponse object.

The `GetShopResponse` object encapsulates the shop's information and includes several fields:
- `contact`
   - `email`
   - `phone`
- `created_at`
- `id`
- `modified_at`
- `name`
- `notifications`
  - `email`
  - `method`
  - `url`
- `object`
- `return`
    - `method`
    - `url`
- `status`

### Get shop fees <a name="get-shop-fees"></a>
HTTP REQUEST `GET /v1/transactions`

Method: `public List<Fee> getShopFees(ShopFeesRequest request)`

The getShopFees method retrieves the list of fees associated with a shop within a specified date range. 
This is performed by sending a GET request that includes parameters specifying the start and end dates 
(created_since and created_until), the page number for paginated results (page), 
and the number of results per page (per_page). The method then returns a list of Fee objects, 
each containing information about the fee's category (object), amount (amount), value-added tax (vat), 
unique identifier (id), and creation timestamp (created_at).

The response object `Fee`: 
- `object`
- `amount`
- `vat`
- `id`
- `created_at`


### Get shop account statement <a name="get-shop-account-statement"></a>
HTTP REQUEST `GET /v1/shop/accountstatements`

Method: `public List<ShopAccountStatementResponse> getShopAccountStatement(ShopAccountStatementRequest request)`

Returns all movements on your account, including service fees, payouts etc.

Parameters 'since' and 'until' can be expressed as date or timestamp. If no timezone is indicated then UTC is applied

Parameter 'payout id' can be expressed as an integer and is optional.

The response object `ShopAccountStatementResponse`: 
- `created`
- `amount`
- `type`
- `transaction`
- `channel`
- `balance_before`
- `balance_after`
- `transaction_id`
- `merchant_reference`
- `original_amount`
- `exchange_rate`

### Create transaction <a name="create-transaction"></a>
HTTP REQUEST `POST /v1/transactions`

Method: `public CreateTransactionResponse createTransaction(CreateTransactionRequest createTransactionRequest)`

CreateTransactionRequest object

|Field|Required|Description|
|---|---|---|
|country|No(Default will be chosen if not provided)|ISO 3166 country code List of ISO 3166 country codes|
|email|No|Customer email|
|locale|No(Default will be chosen if not provided)|ISO 639-1 locale code List of ISO 3166 country codes|
|ip|Yes|Customer IP address|


To create a new transaction simply provide a JSON object containing the required attributes for the new transaction. 
When creating a new transaction, an embedded payment_methods list is returned, containing the available payment 
methods for making a payment for this transaction. Transaction amount and currency are required. Transaction 
reference max-length is 20 characters.Customer ip is required. Customer country must be passed as an alpha-2 
code according to ISO 3166-1. Customer locale must be passed as an alpha-2 code according to ISO 639-1. 
Transaction_url - optionalEach shop has its set of urls manageable in Merchant Portal under the API settings. 
You can optionally over-ride these urls when creating the transaction. If specified then full set must be 
provided (return_url, cancel_url, notification_url)

### Get transaction <a name="get-transaction"></a>
HTTP REQUEST `GET /v1/transactions/{transactionId}`

Method: `public TransactionResource getTransaction(String transactionId)`

|Field|Description|
|---|---|
|transactionId|Transaction ID you get from here: Create transaction|

### Get transaction statement <a name="get-transaction-statement"></a>
HTTP REQUEST `GET /v1/transactions/{transactionId}/statement`

Method: `public List<TransactionResource> getTransactionStatementResponse(String transactionId)`

|Field|Description|
|---|---|
|transactionId|Transaction ID you get from here: Create transaction|

### Get transaction list <a name="get-transaction-list"></a>
HTTP REQUEST `GET /v1/transactions`

Method: `public List<TransactionResource> getTransactionsList()`

### Create payment <a name="create-payment"></a>
HTTP REQUEST `POST /transactions/{id}/payments`

Method: `public CreatePaymentResponse createPayment(DepositRequest depositRequest, String transactionId)`

This method is purposed for handling recurring credit card payments. It isn't applicable to bank-link or 
single credit card transactions. The operation requires a payment token, found in a DepositRequest object, and a transaction ID.


### Create refund <a name="create-refund"></a>
HTTP REQUEST `POST /transactions/{id}/refunds`

Method: `public CreateRefundResponse createRefund(MakeCommerceRefund makeCommerceRefund, String transactionId)`

MakeCommerceRefund object 

| Field          | Required                                 | Description                                  |
|----------------|------------------------------------------|----------------------------------------------|
| transaction ID | Yes                                      | The ID of the transaction                    |
| amount         | Yes                                      | Amount of the refund                         |
| comment        | Yes                                      | Comment for the refund                       |
| email          | Required for anonymous gift card refunds | Email of the original payer                  |
| app_info       | Yes                                      | Provides data about the module and platform  |

Note that you should verify state of the refund in the response. You may be returned 200 response (resource created) 
but its state may be FAILED. While the refund is in state SENT its amount is reserved on shop's account. 
You need sufficient balance on the account to make refunds.

### Get refund description <a name="get-refund"></a>
HTTP REQUEST `POST /v1/refunds/{refundId}`

Method: `public RefundResponse getRefund(String refundId)`

The getRefund method retrieves the details of a specific refund using its unique identifier (refundId). 
The method sends a POST request to the /v1/refunds/{refundId} endpoint. The method then returns a 
RefundResponse object containing the refund details.

### Get refund list <a name="get-refunds-list"></a>
HTTP REQUEST `GET /v1/refunds`

Method: `public List<RefundResponse> getRefundList(RefundListRequest request)`

The `getRefundList` method retrieves a list of refunds within a specified date range and status. 
This method processes the GET request to the `/v1/refunds` endpoint with parameters specifying the 
start and end dates (`since` and `until`), the status of the refunds, the page number for paginated 
results (`page`), and the number of results per page (`perPage`).

`RefundListRequest`
- `since`: The start date of the period for which refunds are to be retrieved.
- `until`: The end date of the period for which refunds are to be retrieved.
- `status`: The status of the refunds to be retrieved.
- `page`: The page number for paginated results.
- `perPage`: The number of results per page.

### Get payment methods <a name="get-payment-methods"></a>
HTTP REQUEST `GET /v1/methods`

Method: `public PaymentMethods getPaymentMethods(PaymentMethodsRequest request)`

The `getPaymentMethods` method retrieves a list of available payment methods based on the provided 
request parameters. This method processes the GET request to the `/v1/methods` endpoint with parameters 
specifying the transaction ID, the amount, the currency, and the country.

`PaymentMethodsRequest`
- `transactionId`: The ID of the transaction for which payment methods are to be retrieved.
- `amount`: The amount for the transaction.
- `currency`: The currency for the transaction.
- `country`:  If a country is specified, the retrieved payment methods will include options available in that country 
- or methods that are not country-restricted, such as credit card options.

### Add Merchant Data <a name="add-merchant-data"></a>
HTTP REQUEST `POST /v1/transactions/{transactionId}/addMeta`

Method: `public TransactionResource addMerchantDataToTransaction(String transactionId, AddMerchantDataRequest request)`

The `addMerchantDataToTransaction` method allows the addition of merchant data to a specific transaction.
This method sends a POST request to the `/v1/transactions/{transactionId}/addMeta` endpoint, 
where `{transactionId}` is replaced by the actual ID of the transaction. The merchant data to be 
added is passed in the request body.

#### Request Fields
- `transactionId`: The unique identifier for the transaction (path parameter).
- `merchantData`: The merchant data to be added to the transaction.

### Get shipment destinations <a name="get-shipment-destinations"></a>
HTTP REQUEST `POST /v1/shipments/destinations`

Method: `public List<GetDestinationsResponse> getDestinations(GetDestinationsRequest request)`

Returns a list of possible delivery destinations based on the filter set with the fields below.

GetDestinationsRequest object 

|Field|Required|Description|
|---|---|---|
|carries|No|List of carries, if blank shows all. OMNIVA, (SMARTPOST, DPD, OMNIVA_LV, PASTA_LV, OMNIVA_LT)|
|countries|No|List of ISO 3166 country codes|
|Types|No|APT = Automated parcel terminal, PO = Post office, PUP = Pickup point|

### Create shipments <a name="create-shipments"></a>
HTTP REQUEST `POST /v1/shipments`

Method: `public List<CreateShipmentResponse> createShipment(Shipment shipment)`

Generates Shipments in the carrier systems (Itella, Omniva, DPD..) Requires your own credentials at these systems
provided as input. Returns shipmentId(s).

Shipment object

|Field|Required|
|---|---|
|credentials|Yes|
|orders|Yes|

Credentials object

|Field|Required|Description|
|---|---|---|
|carrier|Yes|Potential values: OMNIVA, SMARTPOST, DPD, OMNIVA_LV, PASTA_LV, OMNIVA_LT|
|username|Yes|Your carrier username|
|password|Yes|Your carrier password|

Orders object

|Field|Required|Description|
|---|---|---|
|orderId|Yes|Potential values: OMNIVA, SMARTPOST, DPD, OMNIVA_LV, PASTA_LV, OMNIVA_LT|
|carrier|Yes|Your carrier username|
|destination(postalCode, country, county, city, street)|Use this or destinationId|Destinations postal code, country(List of ISO 3166 country codes), county, city and street.|
|Recipient(name, phone, email)|Yes|Recipient name, phone number and email address|
|Sender(name, phone, email, postalCode)|Yes|Senders name, phone number, email address and postalcode|

### Get parcel label formats <a name="get-label-formats"></a>
HTTP REQUEST `GET /v1/shipments/labels/formats`

Method: `public List<String> getLabelFormats()`

Returns an array of all possible label print variants / formats

A4 - 4 per page
A4_FULL_PAGE = 1 per page
A5 - 2 per page
A6_FULL_PAGE -> 1 per page

* LP Express currently only supports A4 - 3 per page. printFormat is ignored for create labels request

### Create labels <a name="create-labels"></a>
HTTP REQUEST `POST /v1/shipments/createlabels`

Method: `public CreateLabelResponse createLabels(Shipment request)`

Generates Parcel Labels based on Shipments created in the carrier systems (Itella, Omniva, DPD..) Requires your own
credentials at these systems provided as input. Returns url to generated label(s) in .pdf format.

Credentials object

|Field|Required|Description|
|---|---|---|
|carrier|Yes|Potential values: OMNIVA, SMARTPOST, DPD, OMNIVA_LV, PASTA_LV, OMNIVA_LT|
|username|Yes|Your carrier username|
|password|Yes|Your carrier password|

Orders object

|Field|Required|Description|
|---|---|---|
|orderId|Yes|Potential values: OMNIVA, SMARTPOST, DPD, OMNIVA_LV, PASTA_LV, OMNIVA_LT|
|carrier|Yes|Your carrier username|
|destination(postalCode, country, county, city, street)|Use this or destinationId|Destinations postal code, country(List of ISO 3166 country codes), county, city and street.|
|Recipient(name, phone, email)|Yes|Recipient name, phone number and email address|
|Sender(name, phone, email, postalCode)|Yes|Senders name, phone number, email address and postalcode|
|shipmentId|Yes|shipmentId from Create shipments|
|printFormat|Yes|	Page size|

## Contact us <a name="contact"></a>
You can contact support via email [Support (support@maksekeskus.ee)](mailto:support@maksekeskus.ee) if you have any questions.

