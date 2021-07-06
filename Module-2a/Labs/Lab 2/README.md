# API Security : DSG Security and Authentication Worlflow

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
- Generate a Sample JWT token 
- Create a Request using yoru REST Client (Postman, Curl or similar) with an authorization Header containig the JWT token
- Secure the sample Hipster Products API with an JWT token verification policy. 

## Generate a JWT token
https://dinochiesa.github.io/jwt/

## Secure Hipster API with a JWT Verification Policy

TO-DO: Add steps and screenshots

## Test JWT Verification Policy 

TO-DO: Add screenshots of postman and tacing 


## Bonus Points

Try to add specific claims to your JWT Token and modify your JWT verificatin Policy to validate this claims.

to-do add screenshots showing how to add extra claims

This session would be a presentation of the proposed authentication workflow via the relevant Identity Provider and Authentication services.

The demo adds authentication steps to the proxy you have build in the previous Labs.Optionally you can have a look at this proxy that shows the end result of the workflow being created for the purposes of this demo.

https://github.com/ajaymahale/apijam/blob/master/Module-2a/Resources/Hipster-Products-API-AUTH.zip
