# Tecpay integration 
## Request Payment from Android/IOS APP 

Please follow the steps to integrate payment request from your app

## Required Fields:
-  gateway endpoint : https://tecpay.in/checkout.php
-  order_id: your order id (it should be unique and atleast 10 character)
-  pid: given merchant id
-  purpose: purpose of payment (any string)
-  amt: amount to send
-  email: payer email id

## Procedure

-  Step 1: append required field except gateway endpoint as arguent string
-     example: order_id=your_order_id&pid=given_merchant_id&purpose=any_purpose&amt=your_amount&email=youremail@example.com
-   Step 2: base64 encode the argumet string
-     example: b3JkZXJfaWQ9eW91cl9vcmRlcl9pZCZwaWQ9Z2l2ZW5fbWVyY2hhbnRfaWQmcHVycG9zZT1hbnlfcHVycG9zZSZhbXQ9eW91cl9hbW91bnQmZW1haWw9eW91cmVtYWlsQGV4YW1wbGUuY29t
-   Step 3: Append encoded arguments into tecpay end point
-     example: https://tecpay.in/checkout.php?code=b3JkZXJfaWQ9eW91cl9vcmRlcl9pZCZwaWQ9Z2l2ZW5fbWVyY2hhbnRfaWQmcHVycG9zZT1hbnlfcHVycG9zZSZhbXQ9eW91cl9hbW91bnQmZW1haWw9eW91cmVtYWlsQGV4YW1wbGUuY29t
-    step 4: Launch this url from your app as LaunchMode.externalApplication
```sh
##Kotlin Example: 
val webIntent: Intent = Uri.parse('https://tecpay.in/checkout.php?code=b3JkZXJfaWQ9eW91cl9vcmRlcl9pZCZwaWQ9Z2l2ZW5fbWVyY2hhbnRfaWQmcHVycG9zZT1hbnlfcHVycG9zZSZhbXQ9eW91cl9hbW91bnQmZW1haWw9eW91cmVtYWlsQGV4YW1wbGUuY29t').let { webpage ->Intent(Intent.ACTION_VIEW, webpage)}
```
```sh    
##Java Example: 
Uri webpage = Uri.parse('https://tecpay.in/checkout.php?code=b3JkZXJfaWQ9eW91cl9vcmRlcl9pZCZwaWQ9Z2l2ZW5fbWVyY2hhbnRfaWQmcHVycG9zZT1hbnlfcHVycG9zZSZhbXQ9eW91cl9hbW91bnQmZW1haWw9eW91cmVtYWlsQGV4YW1wbGUuY29t');
Intent webIntent = new Intent(Intent.ACTION_VIEW,webpage);
```
```sh 
##Flutter Example: 
canLaunchUrl(Uri.parse('https://tecpay.in/checkout.php?code=b3JkZXJfaWQ9eW91cl9vcmRlcl9pZCZwaWQ9Z2l2ZW5fbWVyY2hhbnRfaWQmcHVycG9zZT1hbnlfcHVycG9zZSZhbXQ9eW91cl9hbW91bnQmZW1haWw9eW91cmVtYWlsQGV4YW1wbGUuY29t')).then((result) => {   
if(result==true)  
{ launchUrl(Uri.parse(url),mode: LaunchMode.externalApplication) }
else  
{   throw "Could not launch $url"  }   
    
});
```

- Step 5 : Its done  
 

### WebHook Integration 

WebHook has to integrate on your server at some secret location, where our api confirm you the status of payment
#### Required Fields
```sh
secret_key = given secret key;
```
In the POST request you will get following values
```sh 
order_id
amount
status
post_hash
```
- step 1:  compute hash (use md5 128 bit hashing algorithm to generate hash)
```sh
// Compute the payment hash locally In (PHP Example)
$local_hash = md5($order_id.$amount.$status.$secret_key);  
```
- step 2: verify hash (Compare hash given at request and local hash)
```sh 
if ($post_hash == $local_hash)
{
  // Mark the transaction as success & process the order
  // You can write code process the order herer
  $hash_status = "Hash Matched";
    
}
  else
  {
      // Verification failed
  }
```
Step 3 : Acknowledge Back payment gateway (You should  Acknowledge back payment gateway that you logged the status of payment , otherwise you will get multiple acknowledge)
```sh
$data['hash_status']=$hash_status; // Hash Matched or Hash Mismatch 
$data['acknowledge']=$acknowledge; // yes or no
header('Content-Type: application/json; charset=utf-8');
echo json_encode($data); // output as a json file
```

# PHP WebHook Sample
```sh
<?php
// Merchant secret key
$secret_key = "your_secret_key";

// Data received from gateway
$order_id = $_POST['order_id'];
$amount = $_POST['amount'];
$status = $_POST['status'];
$post_hash = $_POST['post_hash'];

// Compute the payment hash locally
$local_hash = md5($order_id.$amount.$status.$secret_key);

if ($post_hash == $local_hash) {
  // Mark the transaction as success & process the order
  // You can write code process the order herer
  $hash_status = "Hash Matched";
  $acknowledge='yes';
}
else {
  // Suspicious payment, dont process this payment.
  $hash_status = "Hash Mismatch";
  $acknowledge='no';
}

$data['hash_status']=$hash_status; 
$data['acknowledge']=$acknowledge;
header('Content-Type: application/json; charset=utf-8');
echo json_encode($data);  
?>

```

