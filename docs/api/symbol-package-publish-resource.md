---
title: Push Symbol Packages, NuGet API | Microsoft Docs
author:
- cristinamanu
- kraigb
ms.author:
- cristinamanu
- kraigb
manager: skofman
ms.date: 10/30/2018
ms.topic: reference
ms.prod: nuget
ms.technology: null
description: The publish service allows clients to publish new symbol packages.
keywords: NuGet API push symbol package
ms.reviewer:karann
---

# Push Symbol Packages

It is possible to push symbols packages ([snupkg](../create-packages/Symbol-Packages-V2.md)) using the NuGet V3 API.
These operations are based off of the `SymbolPackagePublish` resource found in the [service index](service-index.md).

## Versioning

The following `@type` value is used:

@type value          | Notes
-------------------- | -----
SymbolPackagePublish/4.9.0 | The initial release

## Base URL

The base URL for the following APIs is the value of the `@id` property of the `SymbolPackagePublish/4.9.0` resource in the
package source's [service index](service-index.md). For the documentation below, nuget.org's URL is used. Consider 
`https://www.nuget.org/api/v2/symbolpackage` as a placeholder for the `@id` value found in the service index.


## HTTP methods

The `PUT` HTTP method is supported by this resource. 

## Push a symbol package

nuget.org supports pushing new symbol packages format ([snupkg](../create-packages/Symbol-Packages-V2.md)) using the following API. 

    PUT https://www.nuget.org/api/v2/symbolpackage

Symbol packages with the same ID and version
can be submitted multiple times. A symbol package will be rejected in the following cases.
- A package with the same ID and version does not exist.
- A symbol package with the same ID and version was pushed but is not yet published.
- The symbol package ([snupkg](../create-packages/Symbol-Packages-V2.md)) is invalid (see [symbol package constraints](../create-packages/Symbol-Packages-V2.md)).

For snupkg constraints see [symbol package constraints](../create-packages/Symbol-Packages-V2.md).

### Request parameters

Name           | In     | Type   | Required | Notes
-------------- | ------ | ------ | -------- | -----
X-NuGet-ApiKey | Header | string | yes      | For example, `X-NuGet-ApiKey: {USER_API_KEY}`

The API key is an opaque string gotten from the package source by the user and configured into the client. No
particular string format is mandated but the length of the API key should not exceed a reasonable size for HTTP header
values.

### Request body

The request body must come in the following form:

#### Multipart form data

The request header `Content-Type` is `multipart/form-data` and the first item in the request body is the raw bytes of
the .snupkg being pushed. Subsequent items in the multipart body are ignored. The file name or any other headers of the
multipart items are ignored.

### Response

Status Code | Meaning
----------- | -------
201         | The symbol package was successfully pushed.
400         | The provided symbol package is invalid.
401         | The user is not authorized to perform this action.
404         | A coresponding package with the provided ID and version does not exist.
409         | A symbol package with the provided ID and version was pushed but it is not available yet.
413         | The package is too large.
