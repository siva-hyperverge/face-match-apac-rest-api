# HyperVerge Face Match API for APAC - Documentation

## Overview

This documentation describes the Face Match API v1 for APAC. The postman collection can be found at this [link](https://www.getpostman.com/collections/d59b71f44697eb89eb0a).

## Table Of Contents

- [HyperVerge Face Matching - API Documentation](#hyperverge-face-matching-api-documentation)
	- [Overview](#overview)
	- [Schema](#schema)
	- [Endpoint](#endpoint)
	- [Authentication](#authentication)
	- [Media Types](#media-types)
	- [Sample Code](#sample-code)
	- [API Call Structure](#api-call-structure)
	- [Response Structure](#response-structure)
	- [Data logging and clientId](#data-logging-and-clientid)

## Schema

Currently, HTTP and HTTPS are supported. All data is received as JSON, and all image uploads are to be performed as form-data (POST request).
It is recommended that HTTPS is used for all API calls. For HTTPS, only TLS v1.2 is supported to ensure better security.

## Endpoint
A `GET` request can be issued to the root endpoint to check for successful connection. The `plain/text` reponse of `Hello!` should be received.

Endpoint: https://apac.faceid.hyperverge.co/v1/photo/verifyPair


## Authentication

- Currently, an appId, appKey combination is passed in the request header. The appId and appKey are provided on request by the HyperVerge team. If you would like to try the API, please reach out to contact@hyperverge.co

- On failed attempt with invalid credentials or unauthorized access the HTTP response would have a status code : 401

- Please do not expose the appId and appKey on browser applications. In case of a browser application, set up the API calls from the server side.

## Media Types

Currently, `jpeg, png and tiff` images are supported by the API.

## Sample Code

    curl -X POST https://apac.faceid.hyperverge.co/v1/photo/verifyPair \
		  -H 'appid: xxx' \
		  -H 'appkey: yyyy' \
		  -H 'content-type: multipart/form-data;\
             -F 'type=id'\
		  -F 'image1=@image_1_path.png' \
		  -F 'image2=@image_2_path.png'

    
## API Call Structure

The API call is used to determine if 2 face images belong to the same person

* **Endpoint**
		
	https://apac.faceid.hyperverge.co/v1/photo/verifyPair

* **Method:**

    `POST`

* **Header**

	- content-type : 'formdata'
	- appid
	- appkey

* **Request Body**

    - type : use '*id*' for Selfie to ID card photo matching and '*selfie*' for Selfie to Selfie matching
	- image1, image2

## Response Structure

* **Success Response** 
	* If the API call was successful and face match was performed, the response would have the following schema

			{
				"status" : "success",
				"statusCode" : "200",
				"result" : {
	                "match" : "yes/no",
	                "match-score" : 95, // 0-100
	                "to-be-reviewed": "yes/no",
				}
			}

	* If API Call was successful but face could not be detected in the image, the response would have the following schema

		```
		{
			"status" : "success",
			"statusCode" : "200",
			"result" : {
				"error" : "Face not detected in one or more images"
			}
		}
		```


* **Request Errors:** Please note that these errors are applicable only for API integration and don't happen when the SDK is used.

	There are 3 types of request errors and `HTTP Status Code 400` is returned in all 3 cases:

	- No Image or only one image input

			{
			    "status": "failure",
			    "statusCode": "400",
			    "error": "image1 and image2 are required files"
			}

	- 'type' not specified

			{
			    "status": "failure",
			    "statusCode": "400",
			    "error": "type is a required parameter, supported types are ['id', 'selfie']"
			}

	- Larger than allowed image input

			{
			  "status": "failure",
			  "statusCode": "400",
			  "error": "image size cannot be greater than 6MB"
			}

All error messages follow the same syntax with the statusCode, status and error(string) being part of the body.

* **Server Errors**
We try our best to avoid these errors, but if by chance they do occur the response code will be 5xx.

	- Form parse error or bad form-data. If the following happens, please make sure the form data is correctly set.
		 
		  {
		       "status": "failure",
		       "statusCode": "500",
		       "error": "upload error"
		   }



## Data logging and clientId

These optional params are used to facilitate better debugging of the system. 

`clientId` is a unique identifier that is assigned to the end customer by the API user. This would need to be passed in the request body. And the parameter, would be the same for the different API calls made for the same customer.

By default, the input images are not stored by HyperVerge systems, however, if the user sets the optional parameter `dataLogging` to string value "yes", then the images will be stored and the requestId can be 
provided to HyperVerge to check the uploaded image incase of an inaccurate extraction.
