# Custom Hosted Reset Password Page

Auth0 doesn't have a public API for creating a custom password reset.  However, the existing Auth0ChangePassword widget does a From POST to the Auth0 `/lo/reset` endpoint. This project is a recreation of that widget to provide more control over the look and feel of the application.

> The Auth0 `/lo/reset` endpoint is considered a private API and is subject to change at any time and is not  documented API.  This demo was built using Chrome Developer Tools to analyze the traffic to achieve the desired result.

## The API

Auth0 uses a public face endpoint for resetting the password.  This endpoint takes five parameters.  Below is an example of how the API works at the time of this document:

### Request
> **Url:** https://{your-tenant}.auth0.com/lo/reset  
**Method:** POST  
**Content-Type:** application/x-www-form-url-encoded  
**Body:** _csrf={{csrf_token}}&ticket={{ticket}}&email={{email}}&newPassword={{your-new-Password}}&confirmNewPassword={{your-new-password-confirmed}}

### Response:
200 - successful password reset  
400 - failed due to password policy defined on the connection
403 - failed due to CSRF token being invalid
500 - failed password reset due to endpoint contract (required fields, etc)


400 - PasswordStrengthError

```json
{
  "name": "PasswordStrengthError",
  "message": "Password is too weak",
  "code": "invalid_password",
  "description": {
    "rules": [{
      "message": "At least %d characters in length",
      "format": [10],
      "code": "lengthAtLeast",
      "verified": true
    }, {
      "message": "Contain at least %d of the following %d types of characters:",
      "code": "containsAtLeast",
      "format": [3, 4],
      "items": [{
        "message": "lower case letters (a-z)",
        "code": "lowerCase",
        "verified": true
      }, {
        "message": "upper case letters (A-Z)",
        "code": "upperCase",
        "verified": false
      }, {
        "message": "numbers (i.e. 0-9)",
        "code": "numbers",
        "verified": false
      }, {
        "message": "special characters (e.g. !@#$%^&*)",
        "code": "specialCharacters",
        "verified": true
      }],
      "verified": false
    }, {
      "message": "No more than %d identical characters in a row (e.g., \"%s\" not allowed)",
      "code": "identicalChars",
      "format": [2, "aaa"],
      "verified": true
    }],
    "verified": false
  },
  "policy": "* At least 10 characters in length\n* Contain at least 3 of the following 4 types of characters:\n * lower case letters (a-z)\n * upper case letters (A-Z)\n * numbers (i.e. 0-9)\n * special characters (e.g. !@#$%^&*)\n* No more than 2 identical characters in a row (e.g., \"aaa\" not allowed)",
  "statusCode": 400
}
```

400 - PasswordHistoryError

```json
{
  "name": "PasswordHistoryError",
  "message": "Password has previously been used",
  "code": "invalid_password",
  "description": "You may not reuse any of the last 5 passwords. This password was used a minute ago.",
  "statusCode": 400
}
```

400 - PasswordDictError

```json
{
  "name": "PasswordDictionaryError",
  "message": "Password is too common",
  "code": "invalid_password",
  "description": "Password is not allowed, it might be too common.",
  "statusCode": 400
}
```

400 - PasswordNoUserInfoError

```json
{
  "name": "PasswordNoUserInfoError",
  "message": "Password contains user information",
  "code": "invalid_password",
  "description": "Password is based on user information",
  "statusCode": 400
}
```

403 - CSRF Invalid Token Error

```json
{
  "name": "CsrfInvalidTokenError",
  "message": "Invalid CSRF token",
  "code": "invalid_csrf_token",
  "statusCode": 403
}
```

500 - Internal Server Error

- Did you pass in the ticket?
- Did you pass in matching new and confirmed passwords?

## The Hosted Page

The password_reset.html file contains a sample UI for creating a custom change password page.  This page does not contain any client side validation, but does demonstrate server side validation and submission.

This form uses three template fields that are mandatory:

**_csrf:**  
this is th CSRF token.  It protects against CSRF.  Be sure to include this a a hidden field

**email:**  
email is the email address of the person we are change passwords. 

**ticket:**  
this is the change password ticket used to tie the email address to the password change.  It will prevent the user from changing the email addres maliciously.

And one field that is useful:

**password-policy**:  
This is the password policy defined on the connection.  It specifies the password complexity for the given user.  This is handy to use when this password reset page is used for many connections containing different passowrd policies.

## Installing

Navigate to the [password reset page](https://manage.auth0.com/#/password_reset). And copy the contents of password_reset.html to this and hit save.  You are all done and ready to start using a custom reset password page.


