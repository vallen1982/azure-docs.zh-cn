---
title: Upgrade from Bing Web Search API v5 to v7 | Microsoft Docs
description: Identifies the parts of your application that you need to update to use version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.technology: bing-web-search
ms.topic: article
ms.date: 0151/2017
ms.author: scottwhi
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 88bbfa1f546800d44c801835c56bcf6698af13e3
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017

---

# <a name="web-search-api-upgrade-guide"></a>Web Search API Upgrade Guide

> [!NOTE]
> Version 7 is a Preview release. All aspects of the API and documentation are subject to change. Use only for testing in a non-production environment.

This upgrade guide identifies the changes between version 5 and version 7 of the Bing Web Search API. Use this guide to help you identify the parts of your application that you need to update to use version 7.

## <a name="subscription-keys"></a>Subscription Keys

- Your version 5 paid and free trial subscription key will continue working with version 7.

## <a name="breaking-changes"></a>Breaking Changes

### <a name="endpoints"></a>Endpoints

- The endpoint's version number changed from v5 to v7. For example, https://api.cognitive.microsoft.com/bing/\*\*v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Error response objects and error codes

- All failed requests should now include an `ErrorResponse` object in the response body.

- Added the following fields to the `Error` object.  
  - `subCode`&mdash;Partitions the error code into discrete buckets, if possible
  - `moreDetails`&mdash;Additional information about the error described in the `message` field
   

- Replaced the v5 error codes with the following possible `code` and `subCode` values.

|Code|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returns ServerError whenever any of the sub-code conditions occur. The response will include these errors if the HTTP status code is 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blocked|Bing returns InvalidRequest whenever any part of the request is not valid. For example, a required parameter is missing or a parameter value is not valid.<br/><br/>If the error is ParameterMissing or ParameterInvalidValue, the HTTP status code is 400.<br/><br/>If the error is HttpNotAllowed, the HTTP status code 410.
|RateLimitExceeded||Bing returns RateLimitExceeded whenever you exceed your queries per second (QPS) or queries per month (QPM) quota.<br/><br/>Bing returns HTTP status code 429 if you exceeded QPS and 403 if you exceeded QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returns InvalidAuthorization when Bing cannot authenticate the caller. For example, the `Ocp-Apim-Subscription-Key` header is missing or the subscription key is not valid.<br/><br/>Redundancy occurs if you specify more than one authentication method.<br/><br/>If the error is InvalidAuthorization, the HTTP status code is 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returns InsufficientAuthorization when the caller does not have permissions to access the resource. This can occur if the subscription key has been disabled or has expired. <br/><br/>If the error is InsufficientAuthorization, the HTTP status code is 403.

- The following maps the previous error codes to the new codes. If you've taken a dependency on v5 error codes, update your code accordingly.

|Version 5 code|Version 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blocked|InvalidRequest.Blocked


## <a name="non-breaking-changes"></a>Non-breaking Changes  

### <a name="query-parameters"></a>Query parameters

- Added the [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) query parameter. Use this parameter to specify the number of answers that you want the response to include. The answers are chosen based on ranking. For example, if you set this parameter to three (3), the response includes the top three ranked answers.  
  
- Added the [promote](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) query parameter. Use this parameter along with `answerCount` to explicitly include one or more answer types, regardless of their ranking. For example, to promote videos and images into the response, you’d set promote to *videos,images*. The list of answers that you want to promote does not count against the `answerCount` limit. For example, if `answerCount` is 2 and `promote` is set to *videos,images*, the response could include webpages, entities, videos, and images.

### <a name="object-changes"></a>Object changes

- Added the `someResultsRemoved` field to the [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) object. The field contains a Boolean value that indicates whether the response excluded some results from the web answer.  

