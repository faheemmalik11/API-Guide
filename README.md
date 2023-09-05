# REST API's BEST PRACTICES

While there is no hard and fast rule to make rest apis, there are some standard conventions that must be followed while making the Rest Api's. Otherwise, we create problems for clients that use our APIs, which isn’t pleasant and detracts people from using our API. If we don’t follow commonly accepted conventions, then we confuse the maintainers of the API and the clients that use them since it’s different from what everyone expects.

* [Must accept and respond with json](#must-accept-and-respond-with-json)
* [Endpoint Paths Should Be Nouns](#endpoint-paths-should-be-nouns)
* [Url Composition](#url-composition)
* [Handle errors gracefully and return standard error codes](#handle-errors-gracefully-and-return-standard-error-codes)
* [Versioning our APIs](#versioning-our-apis)
* [Add Cache Data](#add-cache-data)

## Must accept and respond with json

It is a common practice that APIs should accept JSON requests as the payload and also send responses back. JSON is a open and standardized format for data transfer. 

## Endpoint Paths Should Be Nouns

Our HTTP request method already has the verb. Having verbs in our API endpoint paths isn’t useful and it makes it unnecessarily long since it doesn’t convey any new information. The chosen verbs could vary by the developer’s whim. For instance, some like ‘get’ and some like ‘retrieve’, so it’s just better to let the HTTP GET verb tell us what and endpoint does.

## Url Composition

Here are few basic guidelines which will help keep in line with our naming conventions:

1. All names in the url should be lowercase (query string parameters are defined in Variables below)
2. Dashes (-) should not be used in urls (ie. don't have /academics/quarter-calendar)
3. Service names should be plural, use /courses instead of /course.
4. Separate services shouldn't be nested under each other.
    For example, this is a recommended design for two separate services:
        `/students` (contains basic student information: email, name, etc)
        `/courses`
    For example, this is not a recommended design for two separate services:
        `/students` (contains basic student information: email, name, etc)
        `/students/courses`

## Handle errors gracefully and return standard error codes

To eliminate confusion for API users when an error occurs, we should handle errors gracefully and return HTTP response codes that indicate what kind of error occurred. This gives maintainers of the API enough information to understand the problem that’s occurred. 

Common error HTTP status codes include:

400 Bad Request - This means that client-side input fails validation.
401 Unauthorized - This means the user isn't not authorized to access a resource. It usually returns when the user isn't authenticated.
403 Forbidden - This means the user is authenticated, but it's not allowed to access a resource.
404 Not Found - This indicates that a resource is not found.
500 Internal server error - This is a generic server error. It probably shouldn't be thrown explicitly.
502 Bad Gateway - This indicates an invalid response from an upstream server.
503 Service Unavailable - This indicates that something unexpected happened on server side (It can be anything like server overload, some parts of the system failed, etc.).

## Versioning our APIs

We should have different versions of API if we're making any changes to them that may break clients. The versioning can be done according to semantic version (for example, 2.0.6 to indicate major version 2 and the sixth patch) like most apps do nowadays.

This way, we can gradually phase out old endpoints instead of forcing everyone to move to the new API at the same time. The v1 endpoint can stay active for people who don’t want to change, while the v2, with its shiny new features, can serve those who are ready to upgrade. This is especially important if our API is public. We should version them so that we won't break third party apps that use our APIs.

Versioning is usually done with /v1/, /v2/, etc. added at the start of the API path.

## Add Cache Data

We can add caching to return data from the local memory cache instead of querying the database to get the data every time we want to retrieve some data that users request. The good thing about caching is that users can get data faster. However, the data that users get may be outdated. This may also lead to issues when debugging in production environments when something goes wrong as we keep seeing old data.