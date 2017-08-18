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
400 - failed password reset

This API is not an JSON/RESTful endpoint.  As such you cannot expect to use the body for anything meaningful.  This endpoint will return HTML used by Auth0ConfirmPassword to display the result.  Instead you should look at the error codes to determin success or error and rely on javascript validation for better UX.  I would recommend treating any non-200 status code the same (as an error).  If you JavaScript validation is cirumvented you can still have server side validation, but it won't give you meaningful errors.  You should instead treat this as a generic error.

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


