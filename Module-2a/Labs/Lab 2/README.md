# JWT Based Security Excersice

*Duration : 30 mins*

*Persona : API Team/Security*

# Use case

You have an API that is consumed by trusted applications. You want to secure that API using the appropriate authentication mechanisms. 

# How can Apigee Edge help?

Apigee Edge quickly lets you secure your APIs using out of the box JWT/JWD policies. Both JWS and JWT are commonly used to share claims or assertions between connected applications. The JWS/JWT policies enables Edge API proxies to:

- Generate a signed JWT or JWS.
- Verify a signed JWT or JWS and claims within the JWS/JWT.
- Decode a signed JWT or JWS without validating the signature.

In the latter two cases, the policy also sets variables that allow additional policies, or the backend services themselves, to inspect the validated claims and to make decisions based on those claims.

When using the Verify JWS/JWT policy, an invalid JWS/JWT will be rejected and will result in an error condition. Similarly, when using the Decode JWS/JWT policy, a malformed JWS/JWT will result in an error condition.

# Instructions

As part of this lab, you will:
- Generate a JWT token 
- Create a Request using yoru REST Client (Postman, cUrl, Powershell or similar) with an authorization Header containig the JWT token
- Secure the sample Hipster Products API with an JWT token verification policy. 

## Generate a JWT token
A JSON Web Token is a proposed Internet standard for creating data with optional signature and/or optional encryption whose payload holds JSON that asserts some number of claims. The tokens are signed either using a private secret or a public/private key pair. for this labs we will be using a public/private key pair for verification purposes. We will be using a free online tool that will help us generate and sign a JWT token that is then going to be validated by our proxy.


1. Visit the following url https://dinochiesa.github.io/jwt/ 

![image alt text](./media/image_1.png)

2. <a name="claims"></a>Under the "Decoded Payload" panel you will see all the claims that this JWT asserts, feel free to add any custom claims if you wish. some of the claims in here are needed for your token to ve valid, so don't remove any of them unless you know what you are doing ;) 
3. Set the encoding algorithm to RS256.
4. Set the Expiry to 60 mins (to give your self some time to get trough the rest fo the steps).
5. Click on the Left pointing Arrow to generate an encoded token. 
6. <a name="publickey"></a>Take Note of the public key from the private/public pair as youw ill need it in the next steps.

## Secure Hipster API with a JWT Verification Policy

1. Select **Develop** → **API Proxies** in the side navigation menu:

![image alt text](./media/image_0.png)

2. Select the previously created **{{your initials}}_Hipster-Products-API** proxy:

![image alt text](./media/image_7.png)

3. Click on the **Develop** tab:

![image alt text](./media/image_8.png)

4. Ensure that "**PreFlow**" is selected in the “**Proxy Endpoints**” window, and then click the **+Step** button above the “**Request**” flow:

![image alt text](./media/image_9.png)

5. Select the **"Verify JWT"** security policy, leave the default name, and then click **Add**:

![image alt text](./media/image_10.png)

6. Select the **"Extract Variables"** mediation policy, leave the default name, and then click **Add**:

![image alt text](./media/image_10_a.png)

7. Drag and drop the Extract-Variables policy so it is before the spike arrest policy

![image alt text](./media/image_10_b.png)

8. Click the Extract Variable step 

![image alt text](./media/image_10_c.png)

9. Replace the poicy xml code with the following: 
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
   <Header name="Authorization">
      <Pattern ignoreCase="false">Bearer {jwt_token}</Pattern>
    </Header>
</ExtractVariables>
```
10. Drag and drop the VerifyJWT policy so it on the right of the Extract-Variables policy and before the spike arrest policy.

![image alt text](./media/image_11.png)

11. replace the policy xml code withthe following:

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <VerifyJWT name="Verify-JWT" enabled="true" continueOnError="false" async="false">
        <DisplayName>Verify JWT</DisplayName>
        <Algorithm>RS256</Algorithm>
        <Source>jwt_token</Source>
        <PublicKey>
            <Value>
    -----BEGIN PUBLIC KEY-----
    [public_key]
    -----END PUBLIC KEY-----
            </Value>
        </PublicKey>
    </VerifyJWT>
    ```
    replace `[public_key]` with the value of the public key in the public/private pair you used in the [first section](#publickey)  of this lab to generate the JWT token.

12. Cick on **Save**. After the proxy is saved, click the **Trace** tab in the upper right:

![image alt text](./media/image_11_a.png)

12. Click on the **Start Trace Session** button. Add /products to the end of the URL field.

![image alt text](./media/image_12.png)


## Test JWT Verification Policy 

#### Note: the following steps assume you are using postman as your rest client, different clients will have different ways of achieving the same result.

9. Open up your Rest Client 

11. Create a GET request  to the products endpoint of your Proxy.

12. Under the Authorization settings of your client (screenshot shows postman) include a Bearer Token and replace the value of the token with the one you generated on the first section of this lab. **Note: refresh your token if it has expired**

![image alt text](./media/image_12_a.png)

13. **Alternative 1 to Postman - Powershell** if you do not have access to Postman an alternative way to test this scenario is by using the following powershell script. 
```
$HEADERS = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$JWT_TOKEN = "<PASTE_YOUR_JWT_TOKEN_HERE>"
$API_RUNTIME_DOMAIN = "<YOUR_API_RUNTIME_DOMAIN"
$YOUR_PROXY_INITIALS = "<YOUR_PROXY_INITIALS"
$HEADERS.Add("Authorization", "Bearer $JWT_TOKEN")
$RESPONSE = Invoke-RestMethod 'http://$API_RUNTIME_DOMAIN/$YOUR_PROXY_INITIALS-hipster-products-api/products' -Method 'GET' -Headers $HEADERS
$RESPONSE | ConvertTo-Json
```

Copy the above code into a new file, you will need to replace the relevant parameters which are in angle brackets(<Values_To_Be_Replaced>) with the appropriate values.


Save this file with a .ps1 extendion and execute from a powershell console.

14. **Alternative 2 to Postman - Fiddler** If **Fiddler** is your prefrred http client, and you have following your instructions with it, 
* Coompose an http get request to the /products endpoint
* Add the header ``` Authorization: Bearer <REPLACE_THIS_TEXT_WITH_JWT_TOKEN> ``` to your fiddler request

15. Start an Apigee trace session for the proxy. **NOTE: Only start the trace, we will be sending the http request through powershell**

16. As you execute the requests you should see them being traced inside of Apigee's Trace Session Screen.

![image alt text](./media/image_12_b.png)

If everything goes well you should get a 200 response and a list of products in json format.Try to modify your token to make it invalid and in that way observe the policy rejecting the token.

## Bonus Points

* Try to add specific claims to your JWT Token and modify your JWT verification Policy to validate this claims. refer to [Step 2](#publickey) in the first section.
* The current proxy even though returns a status code of 401 still returns a product response. Use the Raise Fault policy to raise a fault when the VerifyJwt policy fails. This should not stop the request at the Raise Fault and not send the request to the backend.

* Full documentation on how the JWT-Verification pilicy works can be found [Here](https://docs.apigee.com/api-platform/reference/policies/verify-jwt-policy)
