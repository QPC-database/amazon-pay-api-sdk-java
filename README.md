[![Maven Central](https://maven-badges.herokuapp.com/maven-central/software.amazon.pay/amazon-pay-sdk-v2-java/badge.svg)](https://maven-badges.herokuapp.com/maven-central/software.amazon.pay/amazon-pay-sdk-v2-java)

### Amazon Pay Java V2 SDK

### Requirements

* Amazon Pay - [Register here](https://pay.amazon.com/signup)
* Java 1.8 or higher
* [Json-lib](http://www.java2s.com/Code/Jar/j/Downloadjsonlib222jdk15jar.htm)
* [BouncyCastle](https://www.bouncycastle.org/download/bcprov-jdk15on-159.jar)
* [Apache Commons Lang](https://mvnrepository.com/artifact/commons-lang/commons-lang/2.6)
* [Apache Commons Logging](https://mvnrepository.com/artifact/commons-logging/commons-logging/1.1.1)
* [Apache Commons Collections](https://mvnrepository.com/artifact/commons-collections/commons-collections/3.2.1)
* [Apache Commons Beanutils](https://mvnrepository.com/artifact/commons-beanutils/commons-beanutils/1.9.2)
* [EZMorph](http://www.java2s.com/Code/Jar/e/Downloadezmorph105jar.htm)

Amazon Pay V2 Integration

Please note the Amazon Pay V2 SDK can only be used for V2-specific API calls
(e.g., Alexa Delivery Trackers, In-Store API, API V2, etc.)

Please contact your Amazon Pay Account Manager before using the In-Store API calls in a Production environment to obtain a copy of the In-Store Integration Guide.

Public and Private Keys
MWS access keys, MWS secret keys, and MWS authorization tokens from previous MWS or Amazon Pay V1 integrations cannot be used with this SDK.

You will need to generate your own public/private key pair to make API calls with this SDK.
In Windows 10 this can be done with ssh-keygen commands:

    ssh-keygen -t rsa -b 2048 -f private.pem
    ssh-keygen -f private.pem -e -m PKCS8 > public.pub

In Linux or macOS this can be done using openssl commands:

    openssl genrsa -out private.txt 2048
    openssl rsa -in private.txt -pubout > public.txt

The first command above generates a private key and the second line uses the private key to generate a public key.

To associate the key with your account, send an email to amazon-pay-delivery-notifications@amazon.com that includes (1) your public key and (2) your Merchant ID. Do not send your private key to Amazon (or anyone else) under any circumstance!

In your Seller Central account, within 1-2 business days, the account administrator will receive a message that includes the public_key_id you will need to use the SDK.
These methods are available in the InstoreClient

Four quick steps are needed to make an API call:

Step 1. Construct a AmazonPayClient (using the previously defined PayConfiguration Array).

        ```java
        AmazonPayClient client = new AmazonPayClient(payConfiguration);
                 // -or-
        WebstoreClient webstoreClient = new WebstoreClient(payConfiguration);
                 // -or-
        InstoreClient instoreClient = new InstoreClient(payConfiguration);
  		 ```

 Step 2. Generate the payload.

  		 ```java
  		 JSONObject payload = new JSONObject();
         payload.put("scanData", "UKhrmatMeKdlfY6b");
         payload.put("scanReferenceId", "0b8fb271-2ae2-49a5-b35d890");
         payload.put("merchantCOE", "DE");
         payload.put("ledgerCurrency", "EUR");

  		 ```

 Step 3. Execute the call.

  		 ```java
  		 AmazonPayResponse response = instoreClient.merchantScan(payload);
  		 ```

  Step 4. Check the result.

  		 requestResult will be an object with the following properties:

  		 * '**status**' - int HTTP status code (200, 201, etc.)
  		 * '**response**' - the JSONObject response body
  		 * '**rawResponse**' - the String type Response
  		 * '**requestId**' - the Request ID from Amazon API gateway
  		 * '**url**' - the URL for the REST call the SDK calls, for troubleshooting purposes
  		 * '**method** - POST, GET, PATCH, or DELETE
  		 * '**headers**' - an array containing the various headers generated by the SDK, for troubleshooting purposes
  		 * '**rawRequest**' - the String type Request
  		 * '**retries**' - usually 0, but reflects the number of times a request was retried due to throttling or other server-side issue
  		 * '**duration**' - duration in milliseconds of SDK function call
  		 * '**success**' - true/false indication of HTTP Status

  		 The first two items (status, response) are critical.  The remaining items are useful in troubleshooting situations.

  		 If you are a Solution Provider and need to make an API call on behalf of a different merchant account, you will need to pass along an extra authentication token parameter into the API call.

```java
import com.amazon.pay.api.PayConfiguration;
import com.amazon.pay.api.types.Region;
import com.amazon.pay.api.AmazonPayClient;
import com.amazon.pay.api.InstoreClient;
import com.amazon.pay.api.WebstoreClient;
import net.sf.json.JSONObject;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
```

You will need your public key id and the file path to your private key
Environment.SANDBOX (for Sandbox Mode)
Environment.LIVE (for Production Mode)

```java
PayConfiguration payConfiguration = null;
try {
    payConfiguration = new PayConfiguration()
                .setPublicKeyId("YOUR_PUBLIC_KEY_ID")
                .setRegion(Region.YOUR_REGION_CODE)
                .setPrivateKey("YOUR_PRIVATE_KEY_STRING")
                .setEnvironment(Environment.SANDBOX);
}catch (AmazonPayClientException e) {
    e.printStackTrace();
}

// If you have your private key in a file, you can set it in the payConfiguration in the following way:

try {
    payConfiguration = new PayConfiguration()
                   .setPublicKeyId("YOUR_PUBLIC_KEY_ID")
                   .setRegion(Region.YOUR_REGION_CODE)
                   .setPrivateKey(new String(Files.readAllBytes(Paths.get("private.pem"))))
                   .setEnvironment(Environment.SANDBOX);
}catch (AmazonPayClientException e) {
     e.printStackTrace();
}

// You can also set your private key as a java.security.PrivateKey object

try {
    payConfiguration = new PayConfiguration()
                .setPublicKeyId("YOUR_PUBLIC_KEY_ID")
                .setRegion(Region.YOUR_REGION_CODE)
                .setPrivateKey("private.pem")
                .setEnvironment(Environment.SANDBOX);
}catch (AmazonPayClientException e) {
     e.printStackTrace();
}

```

### Making a merchantScan request

```java
JSONObject scanPayload = new JSONObject();
scanPayload.put("scanData", "UKhrmatMeKdlfY6b");
scanPayload.put("scanReferenceId", "0b8fb271-2ae2-49a5-b35d890");
scanPayload.put("merchantCOE", "DE");
scanPayload.put("ledgerCurrency", "EUR");

AmazonPayResponse response = null;
JSONObject scanResponse = null;
```

Using the SDK only to sign the request
(You will have to create the request, execute it and process the response)

```java
RequestSigner requestSigner = null;
try {
    requestSigner = new RequestSigner(payConfiguration);
} catch (AmazonPayClientException e) {
   e.printStackTrace();
}

URI scanUri = new URI("https://pay-api.amazon.com/sandbox/in-store/v1/merchantScan");

Map<String, List<String>> queryParametersMap = new HashMap<>();

Map<String, String> header = new HashMap<String, String>();

Map<String, String> postSignedHeaders = null;
try {
    postSignedHeaders = requestSigner.signRequest(scanUri, "POST", queryParametersMap, scanPayload.toString(), header);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

HttpURLConnection conn = (HttpURLConnection)scanUri.toURL().openConnection();

for (Map.Entry<String, String> entry : postSignedHeaders.entrySet()) {
    conn.setRequestProperty(entry.getKey(), entry.getValue());
}

conn.setDoOutput(true);
conn.setRequestMethod("POST");
OutputStreamWriter out = new OutputStreamWriter(conn.getOutputStream());
out.write(scanPayload.toString());
out.close();

int responseCode = conn.getResponseCode();

BufferedReader in;
if (responseCode != 200) {
    in = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
} else {
    in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
}
String inputLine;
StringBuffer response = new StringBuffer();
while ((inputLine = in.readLine()) != null) {
    response.append(inputLine).append("\n");
}

String chargePermissionId = JSONObject.fromObject(response.toString()).getString("chargePermissionId");
```

Using all the functionality of the SDK
(The SDK will create the request, execute it and process the response)

```java

AmazonPayClient client = new AmazonPayClient(payConfiguration);
InstoreClient instoreClient = new InstoreClient(payConfiguration);
WebstoreClient webstoreClient = new WebstoreClient(payConfiguration);

try {
    client = new AmazonPayClient(payConfiguration);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}
try {
    response = instoreClient.merchantScan(scanPayload);
    scanResponse = response.getResponse();
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

String chargePermissionId = scanResponse.getString("chargePermissionId");

```

### Making a charge request

```java
JSONObject chargePayload = new JSONObject();
JSONObject chargeTotal = new JSONObject();
chargeTotal.put("currencyCode", "EUR");
chargeTotal.put("amount", 2);
chargePayload.put("chargeTotal", chargeTotal);
chargePayload.put("chargePermissionId", "S02-8295796-8107357");
chargePayload.put("chargeReferenceId", "chargeReferenceId-2");
chargePayload.put("softDescriptor", "amzn-store");

JSONObject chargeResponse = null;
try {
    chargeResponse = instoreClient.charge(scanPayload).getResponse();
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

String chargeId = chargeResponse.getString("chargeId");

```

### Making a merchantScan request with the auth token

```java
JSONObject scanPayload = new JSONObject();
scanPayload.put("scanData", "UKhrmatMeKdlfY6b");
scanPayload.put("scanReferenceId", "0b8fb271-2ae2-49a5-b35d890");
scanPayload.put("merchantCOE", "DE");
scanPayload.put("ledgerCurrency", "EUR");

JSONObject scanResponse = null;
try {
    scanResponse = instoreClient.merchantScan(scanPayload).getResponse();
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

String chargePermissionId = scanResponse.getString("chargePermissionId");

```

These methods are available in Webstore Client.

### Making a createCheckoutSession request

```java
JSONObject payload = new JSONObject();
JSONObject webCheckoutDetail = new JSONObject();
webCheckoutDetail.put("checkoutReviewReturnUrl", "https://localhost/store/checkout_review");
payload.put("webCheckoutDetail", webCheckoutDetail);
payload.put("storeId", "amzn1.application-oa2-client.4c46698afa4d4b23b645d05762fc78fa");
AmazonPayResponse response = new AmazonPayResponse();;
String checkoutSessionId = null;

Map<String,String> header = new HashMap<String,String>();
header.put("x-amz-pay-idempotency-key", "23GGJ2GB664378");
try {
     response = webstoreClient.createCheckoutSession(payload, header);
     checkoutSessionId = response.getResponse().getString("checkoutSessionId");
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```
### Making a getCheckoutSession request

```java

AmazonPayResponse response = new AmazonPayResponse();

try {
     response = webstoreClient.getCheckoutSession(checkoutSessionId);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making an updateCheckoutSession request

```java

AmazonPayResponse response = new AmazonPayResponse();
JSONObject payload = new JSONObject();
JSONObject updateWebCheckoutDetail = new JSONObject();
updateWebCheckoutDetail.put("checkoutResultReturnUrl", "https://localhost/store/checkout_return");
payload.put("webCheckoutDetail", updateWebCheckoutDetail);

JSONObject paymentDetail = new JSONObject();
paymentDetail.put("paymentIntent" , "Authorize");
paymentDetail.put("canHandlePendingAuthorization", false);
JSONObject chargeAmount = new JSONObject();
chargeAmount.put("amount", "12.34");
chargeAmount.put("currencyCode", "USD");
paymentDetail.put("chargeAmount", chargeAmount);
payload.put("paymentDetail", paymentDetail);

JSONObject merchantMetadata = new JSONObject();
merchantMetadata.put("merchantReferenceId", "2019-0001");
merchantMetadata.put("merchantStoreName", "AmazonTestStoreFront");
merchantMetadata.put("noteToBuyer", "noteToBuyer");
merchantMetadata.put("customInformation", "custom information goes here");
payload.put("merchantMetadata", merchantMetadata);

try {
     response = webstoreClient.updateCheckoutSession(checkoutSessionId, payload);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making an getChargePermissions request

```java

AmazonPayResponse response = new AmazonPayResponse();

try {
     response = webstoreClient.getChargePermissions(chargePermissionId);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making a updateChargePermission request

```java

AmazonPayResponse response = new AmazonPayResponse();
JSONObject payload = new JSONObject();
JSONObject merchantMetadata = new JSONObject();
merchantMetadata.put("merchantReferenceId", "32-41-323141");
merchantMetadata.put("merchantStoreName", "AmazonTestStoreName");
merchantMetadata.put("noteToBuyer", "Some note to buyer");
merchantMetadata.put("customInformation", "This is custom info");
payload.put("merchantMetadata", merchantMetadata);

try {
     response = webstoreClient.updateChargePermission(chargePermissionId, payload);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making a closeChargePermission request

```java

AmazonPayResponse response = new AmazonPayResponse();
JSONObject payload = new JSONObject();
payload.put("closureReason", "Specify the reason here");
payload.put("cancelPendingCharges", "false");

try {
     response = webstoreClient.closeChargePermission(chargePermissionId, payload);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making a getCharge request

```java

AmazonPayResponse response = new AmazonPayResponse();

try {
     response = webstoreClient.getCharge(chargesId);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making a createCharge request

```java

AmazonPayResponse response = new AmazonPayResponse();

JSONObject payload = new JSONObject();
JSONObject chargeAmount = new JSONObject();
chargeAmount.put("amount", "1.23");
chargeAmount.put("currencyCode", "USD");

payload.put("chargePermissionId", "S01-3152594-4330637");
payload.put("chargeAmount", chargeAmount);
payload.put("captureNow", false);
// if payload.put("captureNow", true);
// then provide
// payload.put("softDescriptor", "My Soft Descriptor");
payload.put("canHandlePendingAuthorization", true);

String chargeId = null;

Map<String, String> header = new HashMap<String, String>();
header.put("x-amz-pay-idempotency-key", "23GGPHGB668378");

try {
     response = webstoreClient.createCharge(payload, header);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}
chargeId = response.getResponse().getString("chargeId");

```

### Making a captureCharge request

```java

AmazonPayResponse response = new AmazonPayResponse();

JSONObject payload = new JSONObject();
JSONObject captureAmount = new JSONObject();
captureAmount.put("amount", "1.23");
captureAmount.put("currencyCode", "USD");
payload.put("captureAmount", captureAmount);
payload.put("softDescriptor", "My Soft Descriptor");

Map<String, String> header = new HashMap<String, String>();
header.put("x-amz-pay-idempotency-key", "23GGJHGB668370");

try {
     response = webstoreClient.captureCharge(chargesId, payload, header);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making a cancelCharge request

```java

AmazonPayResponse response = new AmazonPayResponse();

JSONObject payload = new JSONObject();
payload.put("cancellationReason", "Buyer changed their mind");

try {
     response = webstoreClient.cancelCharge(chargeId, payload);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making a createRefund request

```java

AmazonPayResponse response = new AmazonPayResponse();

JSONObject payload = new JSONObject();
JSONObject refundAmount = new JSONObject();
refundAmount.put("amount", "0.01");
refundAmount.put("currencyCode", "USD");
payload.put("chargeId", chargeId);
payload.put("refundAmount", refundAmount);
payload.put("softDescriptor", "AMZ*soft");

Map<String,String> header = new HashMap<String,String>();
header.put("x-amz-pay-idempotency-key","23GGJHGB668344");
String refundId = null;

try {
     response = webstoreClient.createRefund(payload,header);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}
refundId = response.getResponse().getString("refundId");

```

### Making a getRefund request

```java

AmazonPayResponse response = new AmazonPayResponse();

try {
     response = webstoreClient.getRefund(refundId);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```

### Making a deliveryTracker request (only in live environment)
Please note that your merchant account must be whitelisted to use the [Delivery Trackers API](https://developer.amazon.com/docs/amazon-pay-onetime/delivery-order-notifications.html).
This method is available in the base client ("AmazonPayClient")

```java

AmazonPayResponse response = new AmazonPayResponse();

JSONObject payload = new JSONObject();
JSONObject deliveryDetails = new JSONObject();
JSONArray deliveryDetailsArray = new JSONArray();
deliveryDetails.put("trackingNumber", "0430955041235");
deliveryDetails.put("carrierCode", "FEDEX");
deliveryDetailsArray.add(deliveryDetails);
payload.put("amazonOrderReferenceId", "P01-8845762-6072995");
payload.put("deliveryDetails", deliveryDetailsArray);

try {
     response = client.deliveryTracker(payload);
} catch (AmazonPayClientException e) {
    e.printStackTrace();
}

```
