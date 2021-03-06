---
description: Use HttpClient and the rest of the Windows.Web.Http namespace API to send and receive information using the HTTP 2.0 and HTTP 1.1 protocols.
title: HttpClient
ms.assetid: EC9820D3-3A46-474F-8A01-AE1C27442750
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
---

# HttpClient

**Important APIs**

-   [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639)
-   [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692)
-   [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631)

Use [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) and the rest of the [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) namespace API to send and receive information using the HTTP 2.0 and HTTP 1.1 protocols.

## Overview of HttpClient and the Windows.Web.Http namespace

The classes in the [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) namespace and the related [**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) and [**Windows.Web.Http.Filters**](https://msdn.microsoft.com/library/windows/apps/dn298623) namespaces provide a programming interface for Universal Windows Platform (UWP) apps that act as an HTTP client to perform basic GET requests or implement more advanced HTTP functionality listed below.

-   Methods for common verbs (**DELETE**, **GET**, **PUT**, and **POST**). Each of these requests are sent as an asynchronous operation.

-   Support for common authentication settings and patterns.

-   Access to Secure Sockets Layer (SSL) details on the transport.

-   Ability to include customized filters in advanced apps.

-   Ability to get, set, and delete cookies.

-   HTTP Request progress info available on asynchronous methods.

The [**Windows.Web.Http.HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) class represents an HTTP request message sent by [**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639). The [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) class represents an HTTP response message received from an HTTP request. HTTP messages are defined in [RFC 2616](https://go.microsoft.com/fwlink/p/?linkid=241642) by the IETF.

The [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) namespace represents HTTP content as the HTTP entity body and headers including cookies. HTTP content can be associated with an HTTP request or an HTTP response. The **Windows.Web.Http** namespace provides a number of different classes to represent HTTP content.

-   [**HttpBufferContent**](https://msdn.microsoft.com/library/windows/apps/dn298625). Content as a buffer
-   [**HttpFormUrlEncodedContent**](https://msdn.microsoft.com/library/windows/apps/dn298685). Content as name and value tuples encoded with the **application/x-www-form-urlencoded** MIME type
-   [**HttpMultipartContent**](https://msdn.microsoft.com/library/windows/apps/dn298708). Content in the form of the **multipart/\*** MIME type.
-   [**HttpMultipartFormDataContent**](https://msdn.microsoft.com/library/windows/apps/dn279596). Content that is encoded as the **multipart/form-data** MIME type.
-   [**HttpStreamContent**](https://msdn.microsoft.com/library/windows/apps/dn279649). Content as a stream (the internal type is used by the HTTP GET method to receive data and the HTTP POST method to upload data)
-   [**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661). Content as a string.
-   [**IHttpContent**](https://msdn.microsoft.com/library/windows/apps/dn279684) - A base interface for developers to create their own content objects

The code snippet in the "Send a simple GET request over HTTP" section uses the [**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661) class to represent the HTTP response from an HTTP GET request as a string.

The [**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) namespace supports creation of HTTP headers and cookies, which are then associated as properties with [**HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) and [**HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) objects.

## Send a simple GET request over HTTP

As mentioned earlier in this article, the [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) namespace allows UWP apps to send GET requests. The following code snippet demonstrates how to send a GET request to http://www.contoso.com using the [**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) class and the [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) class to read the response from the GET request.

```csharp
//Create an HTTP client object
Windows.Web.Http.HttpClient httpClient = new Windows.Web.Http.HttpClient();

//Add a user-agent header to the GET request. 
var headers = httpClient.DefaultRequestHeaders;

//The safe way to add a header value is to use the TryParseAdd method and verify the return value is true,
//especially if the header value is coming from user input.
string header = "ie";
if (!headers.UserAgent.TryParseAdd(header))
{
    throw new Exception("Invalid header value: " + header);
}

header = "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)";
if (!headers.UserAgent.TryParseAdd(header))
{
    throw new Exception("Invalid header value: " + header);
}

Uri requestUri = new Uri("http://www.contoso.com");

//Send the GET request asynchronously and retrieve the response as a string.
Windows.Web.Http.HttpResponseMessage httpResponse = new Windows.Web.Http.HttpResponseMessage();
string httpResponseBody = "";

try
{
    //Send the GET request
    httpResponse = await httpClient.GetAsync(requestUri);
    httpResponse.EnsureSuccessStatusCode();
    httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
}
catch (Exception ex)
{
    httpResponseBody = "Error: " + ex.HResult.ToString("X") + " Message: " + ex.Message;
}
```

```cppwinrt
// pch.h
#pragma once
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Http.Headers.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
#include <iostream>
using namespace winrt;
using namespace Windows::Foundation;

int main()
{
    init_apartment();

    // Create an HttpClient object.
    Windows::Web::Http::HttpClient httpClient;

    // Add a user-agent header to the GET request.
    auto headers{ httpClient.DefaultRequestHeaders() };

    // The safe way to add a header value is to use the TryParseAdd method, and verify the return value is true.
    // This is especially important if the header value is coming from user input.
    std::wstring header{ L"ie" };
    if (!headers.UserAgent().TryParseAdd(header))
    {
        throw L"Invalid header value: " + header;
    }

    header = L"Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)";
    if (!headers.UserAgent().TryParseAdd(header))
    {
        throw L"Invalid header value: " + header;
    }

    Uri requestUri{ L"http://www.contoso.com" };

    // Send the GET request asynchronously, and retrieve the response as a string.
    Windows::Web::Http::HttpResponseMessage httpResponseMessage;
    std::wstring httpResponseBody;

    try
    {
        // Send the GET request.
        httpResponseMessage = httpClient.GetAsync(requestUri).get();
        httpResponseMessage.EnsureSuccessStatusCode();
        httpResponseBody = httpResponseMessage.Content().ReadAsStringAsync().get();
    }
    catch (winrt::hresult_error const& ex)
    {
        httpResponseBody = ex.message();
    }
    std::wcout << httpResponseBody;
}
```

## POST binary data over HTTP

The [C++/WinRT](/windows/uwp/cpp-and-winrt-apis) code example below illustrates using form data and a POST request to send a small amount of binary data as a file upload to a web server. The code uses the [**HttpBufferContent**](/uwp/api/windows.web.http.httpbuffercontent) class to represent the binary data, and the [**HttpMultipartFormDataContent**](/uwp/api/windows.web.http.httpmultipartformdatacontent) class to represent the multi-part form data.

> [!NOTE]
> Calling **get** (as seen in the code example below) isn't appropriate for a UI thread. For the correct technique to use in that case, see [Concurrency and asynchronous operations with C++/WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency).

```cppwinrt
// pch.h
#pragma once
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Security.Cryptography.h>
#include <winrt/Windows.Storage.Streams.h>
#include <winrt/Windows.Web.Http.Headers.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
#include <iostream>
#include <sstream>
using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Storage::Streams;

int main()
{
    init_apartment();

    auto buffer{
        Windows::Security::Cryptography::CryptographicBuffer::ConvertStringToBinary(
            L"A sentence of text to encode into binary to serve as sample data.",
            Windows::Security::Cryptography::BinaryStringEncoding::Utf8
        )
    };
    Windows::Web::Http::HttpBufferContent binaryContent{ buffer };
    // You can use the 'image/jpeg' content type to represent any binary data;
    // it's not necessarily an image file.
    binaryContent.Headers().Append(L"Content-Type", L"image/jpeg");

    Windows::Web::Http::Headers::HttpContentDispositionHeaderValue disposition{ L"form-data" };
    binaryContent.Headers().ContentDisposition(disposition);
    // The 'name' directive contains the name of the form field representing the data.
    disposition.Name(L"fileForUpload");
    // Here, the 'filename' directive is used to indicate to the server a file name
    // to use to save the uploaded data.
    disposition.FileName(L"file.dat");

    Windows::Web::Http::HttpMultipartFormDataContent postContent;
    postContent.Add(binaryContent); // Add the binary data content as a part of the form data content.

    // Send the POST request asynchronously, and retrieve the response as a string.
    Windows::Web::Http::HttpResponseMessage httpResponseMessage;
    std::wstring httpResponseBody;

    try
    {
        // Send the POST request.
        Uri requestUri{ L"https://www.contoso.com/post" };
        Windows::Web::Http::HttpClient httpClient;
        httpResponseMessage = httpClient.PostAsync(requestUri, postContent).get();
        httpResponseMessage.EnsureSuccessStatusCode();
        httpResponseBody = httpResponseMessage.Content().ReadAsStringAsync().get();
    }
    catch (winrt::hresult_error const& ex)
    {
        httpResponseBody = ex.message();
    }
    std::wcout << httpResponseBody;
}
```

To POST the contents of an actual binary file (rather than the explicit binary data used above), you'll find it easier to use an [HttpStreamContent](/uwp/api/windows.web.http.httpstreamcontent) object. Construct one and, as the argument to its constructor, pass the value returned from a call to [StorageFile.OpenReadAsync](/uwp/api/windows.storage.storagefile.openreadasync). That method returns a stream for the data inside your binary file.

Also, if you're uploading a large file (larger than about 10MB), then we recommend that you use the Windows Runtime [Background Transfer](/uwp/api/windows.networking.backgroundtransfer) APIs.

## Exceptions in Windows.Web.Http

An exception is thrown when an invalid string for a the Uniform Resource Identifier (URI) is passed to the constructor for the [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) object.

**.NET:**  The [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) type appears as [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) in C# and VB.

In C# and Visual Basic, this error can be avoided by using the [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) class in the .NET 4.5 and one of the [**System.Uri.TryCreate**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.trycreate.aspx) methods to test the string received from a user before the URI is constructed.

In C++, there is no method to try and parse a string to a URI. If an app gets input from the user for the [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998), the constructor should be in a try/catch block. If an exception is thrown, the app can notify the user and request a new hostname.

The [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) lacks a convenience function. So an app using [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) and other classes in this namespace needs to use the **HRESULT** value.

In apps using the .NET Framework 4.5 in C#, VB.NET, the [System.Exception](https://msdn.microsoft.com/library/system.exception.aspx) represents an error during app execution when an exception occurs. The [System.Exception.HResult](https://msdn.microsoft.com/library/system.exception.hresult.aspx) property returns the **HRESULT** assigned to the specific exception. The [System.Exception.Message](https://msdn.microsoft.com/library/system.exception.message.aspx) property returns the message that describes the exception. Possible **HRESULT** values are listed in the *Winerror.h* header file. An app can filter on specific **HRESULT** values to modify app behavior depending on the cause of the exception.

In apps using managed C++, the [Platform::Exception](https://msdn.microsoft.com/library/windows/apps/hh755825.aspx) represents an error during app execution when an exception occurs. The [Platform::Exception::HResult](https://msdn.microsoft.com/library/windows/apps/hh763371.aspx) property returns the **HRESULT** assigned to the specific exception. The [Platform::Exception::Message](https://msdn.microsoft.com/library/windows/apps/hh763375.aspx) property returns the system-provided string that is associated with the **HRESULT** value. Possible **HRESULT** values are listed in the *Winerror.h* header file. An app can filter on specific **HRESULT** values to modify app behavior depending on the cause of the exception.

For most parameter validation errors, the **HRESULT** returned is **E\_INVALIDARG**. For some illegal method calls, the **HRESULT** returned is **E\_ILLEGAL\_METHOD\_CALL**.

