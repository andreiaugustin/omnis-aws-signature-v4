# omnis-aws-signature-v4
A library to create AWS Signature V4 compliant requests for AWS APIs in Omnis Studio


# aws_signature library

The `aws_signature` is a JSON export of the library that implements the requirements for AWS Signature V4.

# aws_api library

The `aws_api` is a JSON export of the library that provides a familiar interface to AWS APIs that require AWS Signature V4. It requires the `aws_signature` library to work.

# How to use

First, import the `aws_signature` library. Secondly, import the `aws_api` library.

---

In your library, do:

```
Do $root.$modes.$getapiobject("aws_api") Returns aws_api
```

To get an object reference to the API object from the `aws_api` library.

---

To execute the request, use the `$init` method on the returned object reference from the previous command, for example:

```
Do aws_api.$init(con(endpoint,uri),'POST',payload,access_key,secret_key,host,uri)
```

`endpoint` is your main AWS API endpoint.

`uri` parameter is the URI of the specific API you wish to call.

`payload` is a binary variable obtained from `OJSON.$listorrowtojson` - therefore it contains JSON formatted data that we're sending to the AWS API.

`access_key` is a character variable containing your AWS access key, e.g. `AKIA...`.

`secret_key` is a character variable containig your AWS secret key, e.g. `g1OCh...`.

`host` is a character variable containing your AWS host, e.g. `pl48...execute-api.eu-west-2.amazonaws.com`.

For example, if we had an API endpoint with the URI `/list_ec2` that is meant to list our EC2 instances, we would call it like so:

```
Do OJSON.$listorrowtojson(row(pRegion,pSubnet)) Returns payload

Calculate uri as '/list_ec2'

Do aws_api.$init(con(endpoint,uri),'POST',payload,access_key,secret_key,host,uri)
Quit method aws_api.$run()
```

---

Because things like `access_key` and `secret_key` and `host` are unlikely to change, you could implement an extra layer of abstraction with an object which `$init` method can save the access and secret keys and host as instance variables. Then, you could have specific methods for each one of your APIs, e.g. `$list_ec2` would then only need two parameters: the region and the subnet, since all the other parameters for the `aws_api` library are stored in your abstracted object.

---

Note: we use `$run` inside the `aws_api` library so that we can return the response back to the caller. If you wish to execute the request in a background thread, you will need to implement your own mechanism of retrieving the data after the `$completed` callback.

# Alternatives

Omnis Studio 11 offers a JS Worker and a Python Worker. AWS offers libraries for both nodejs and python to interact with their APIs.

We found the experience of building AWS Lambda APIs and then expose them through the AWS API Gateway and consume them on the client side with node throguh the [@aws-cdk/client-lambda](https://www.npmjs.com/package/@aws-sdk/client-lambda) much easier and perhaps a better way since you do not have to maintain the AWS Signature V4 mechanism yourself: especially smart if we're going to see updates to the signature mechanism in the future. 

Once you build your API consumer in JS, you can use Omnis' JS Worker to call. Otherwise, you could try implementing it in Python!



