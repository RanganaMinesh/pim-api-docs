# How to check the request signature?

Akeneo PIM uses the **secret** to create a hash **signature** of the API event.

The signature is forwarded through the request header `X-Akeneo-Request-Signature`, and allows you to verify that the API event request was sent by the PIM and not by a malicious third-party.

You can verify the signature by following these steps:

1. Get the original signature and timestamp from the request headers:
   `X-Akeneo-Request-Signature` and `X-Akeneo-Request-Timestamp`.
2. Prepare the event payload by concatenating the timestamp and the request body with a dot `.`.
3. Generate a hash signature (HMAC SHA256) of the event payload with the help of the _secret_.
4. Compare the original and generated signature.

Example of signature validation with PHP

```php

$secret = "3ha6eonoa9icsckw8kccos084w0c0000g08g40oo4kww0gc8w4";

// X-Akeneo-Request-Signature
$originalSignature =
  "d4582dd13a7b654c212d70b4c705e0ab0c0e6eb7f6524080f4d39121f88e6061";
// X-Akeneo-Request-Timestamp
$timestamp = "1602565368";

// The whole request body.
$requestBody = '{"events":[{"action": "product.created", ...}]}';

// Prepare the event payload.
$signedPayload = $timestamp . "." . $requestBody;

// Generate a hash signature.
$generatedSignature = hash_hmac("sha256", $signedPayload, $secret);

// Compare the original and generated signature.
if (false === hash_equals($originalSignature, $generatedSignature)) {
  throw new \Exception("Invalid signature");
}
```

::: info
You are encouraged to use a string comparison function that is safe to timing attack, like [hash_equals](https://www.php.net/manual/en/function.hash-equals.php) in PHP.
:::

You can also use the `X-Akeneo-Request-Timestamp` to validate that the event request is not too old.
