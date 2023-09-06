# REST API's BEST PRACTICES

While there is no hard and fast rule to make rest apis, there are some standard conventions that must be followed while making the Rest Api's. Otherwise, we create problems for clients that use our APIs, which isn’t pleasant and detracts people from using our API. If we don’t follow commonly accepted conventions, then we confuse the maintainers of the API and the clients that use them since it’s different from what everyone expects.

* [Must accept and respond with json](#must-accept-and-respond-with-json)
* [Endpoint Paths Should Be Nouns](#endpoint-paths-should-be-nouns)
* [Url Composition](#url-composition)
* [Handle errors gracefully and return standard error codes](#handle-errors-gracefully-and-return-standard-error-codes)
* [Don't nest resources](#don't-nest-resources)
* [Versioning our APIs](#versioning-our-apis)
* [Add Cache Data](#add-cache-data)
* [Best Responses](#best-responses)
* [Api Rate Limits](#api-rate-limits)

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

```sh
GET: 200 OK
POST: 201 Created
PUT: 200 OK
PATCH: 200 OK
DELETE: 204 No Content
```

```sh 
400 Bad Request - This means that client-side input fails validation.
401 Unauthorized - This means the user isn't not authorized to access a resource. It usually returns when the user isn't authenticated.
403 Forbidden - This means the user is authenticated, but it's not allowed to access a resource.
404 Not Found - This indicates that a resource is not found.
500 Internal server error - This is a generic server error. It probably shouldn't be thrown explicitly.
502 Bad Gateway - This indicates an invalid response from an upstream server.
503 Service Unavailable - This indicates that something unexpected happened on server side (It can be anything like server overload, some parts of the system failed, etc.).
```


## Don't nest resources

Nesting resources in REST APIs is not recommended because it can lead to a complex and difficult-to-maintain API structure. It can also make it harder for developers to understand the API and make requests. Additionally, it can lead to increased latency and decreased performance due to the need to traverse multiple levels of nesting. It is better to keep the API structure as flat as possible and use query parameters to filter and sort data. This makes the API simpler to understand and use and can improve performance.

Instead of
`GET: /books/7/authors/`
Use
`GET: /books?authorId=7`
Which means get books by author with Id=7


## Versioning our APIs

We should have different versions of API if we're making any changes to them that may break clients. The versioning can be done according to semantic version (for example, 2.0.6 to indicate major version 2 and the sixth patch) like most apps do nowadays.

This way, we can gradually phase out old endpoints instead of forcing everyone to move to the new API at the same time. The v1 endpoint can stay active for people who don’t want to change, while the v2, with its shiny new features, can serve those who are ready to upgrade. This is especially important if our API is public. We should version them so that we won't break third party apps that use our APIs.

Versioning is usually done with /v1/, /v2/, etc. added at the start of the API path.

## Add Cache Data

We can add caching to return data from the local memory cache instead of querying the database to get the data every time we want to retrieve some data that users request. The good thing about caching is that users can get data faster. However, the data that users get may be outdated. This may also lead to issues when debugging in production environments when something goes wrong as we keep seeing old data.

## Best Responses

When crafting responses for various situations, it's important to provide clear, concise, and appropriate messages to the user or client. The "best" response can vary depending on the context and the type of application you're building, but here are some general guidelines for different scenarios:

1. **Success Response**:
   - Status Code: 200 OK
   - Message: "Success" or a brief description of the successful operation.
   - Data: Include relevant data in the response body, if applicable.
   - Example:
     ```json
     {
       "status": "success",
       "message": "Record updated successfully.",
       "data": { /* Your data here */ }
     }
     ```

2. **Resource Created**:
   - Status Code: 201 Created
   - Message: "Resource created successfully" or similar.
   - Location Header: Include the URL of the newly created resource.
   - Example:
     ```json
     {
       "status": "success",
       "message": "Resource created successfully.",
       "data": { /* Newly created resource data */ }
     }
     ```

3. **Client Error (e.g., Validation Error)**:
   - Status Code: 400 Bad Request
   - Message: A descriptive error message explaining the issue.
   - Data: Include details about validation errors, if applicable.
   - Example:
     ```json
     {
       "status": "error",
       "message": "Validation failed. Please check the submitted data.",
       "errors": { /* Validation errors */ }
     }
     ```

4. **Unauthorized Access**:
   - Status Code: 401 Unauthorized
   - Message: "Unauthorized. Please log in or provide valid credentials."
   - Optionally, include a link or instructions for authentication.
   - Example:
     ```json
     {
       "status": "error",
       "message": "Unauthorized. Please log in or provide valid credentials."
     }
     ```

5. **Forbidden Access**:
   - Status Code: 403 Forbidden
   - Message: "Access to this resource is forbidden."
   - Provide an explanation if possible.
   - Example:
     ```json
     {
       "status": "error",
       "message": "Access to this resource is forbidden."
     }
     ```


6. **Resource Not Found**:
   - Status Code: 404 Not Found
   - Message: "Resource not found" or a similar message.
   - Example:
     ```json
     {
       "status": "error",
       "message": "Resource not found."
     }
     ```

7. **Server Error**:
   - Status Code: 500 Internal Server Error
   - Message: "Internal Server Error" or a generic message.
   - Avoid exposing sensitive information in error responses.
   - Log detailed error information on the server for debugging but do not expose it to clients.
   - Example:
     ```json
     {
       "status": "error",
       "message": "Internal Server Error."
     }
     ```

8. **Custom Error Messages**:
   - For application-specific errors, provide meaningful and detailed error messages that help users understand the issue and potential solutions.

## Api Rate Limits

The basic principle of API rate limiting is fairly simple:  if access to the API is unlimited, anyone (or anything) can use the API as much as they want at any time, potentially preventing other legitimate users from accessing the API.

API rate limiting is, in a nutshell, limiting access for people (and bots) to access the API based on the rules/policies set by the API’s operator or owner.

We can think of rate limiting as a form of both security and quality control. This is why rate limiting is integral for any API product’s growth and scalability.

### Why is API rate limiting necessary?
API rate limiting can be used as a defensive security measure for the API, and also a quality control method. As a shared service, the API must protect itself from excessive use to encourage an optimal experience for anyone using the API.

Rate limiting on both server-side and client-side is extremely important for maximizing reliability and minimizing latency, and the larger the systems/APIs, the more crucial rate limiting will be.

### What are my options for implementing API rate limits?
* Hard Stop: This means an API consumer will get an Error 429 when they call your API if they are over their limit.
* Soft Stop: In this case, you might have a grace period where calls can continue to be made for a short period after the limit is reached.
* Throttled Stop: You might just want to enforce a slowdown on calls made over the limit. This way users can continue to make calls, but they will just be slower because they are over the limit.
* Billable Stop: Here you might just charge the API consumer for calls made over their limit. Obviously, this would only work for authenticated API users but can be a valid solution.

### Different Methods of Rate Limiting
As discussed above, we can actually use various methods in performing API rate limiting, but there are three most common methods:

1. Throttling
Throttling is performed by setting up a temporary state within the API, so the API can properly assess all requests. Based on certain rules, a specific type of request will be throttled during this temporary state; when throttled, a user may either be slowed considerably (by reducing the bandwidth service) or completely disconnected from the API.

We can implement throttling at the API level, user level, and application level, making it a versatile method for rate limiting.

2. Request Queues
Another popular method of rate limiting is “requests queues”, which limits the number of requests in any given period of time. For example, we can set the rate limit at three requests per second.

3. Algorithm-Based
In this approach, we are using algorithms to implement the API rate limit, and there are actually various ready-to-use algorithms we can use to implement rate limiting:

- Fixed Window
In this method, we use a “fixed” number as a limit, and we use a simple incremental counter to count the number of requests. If this fixed window limit is reached in a set period of time (i.e. 3,000 per hour), then additional requests will be blocked temporarily.

- Leaky Bucket
Here the requests are put in a FIFO (first in first out) queue, so the first user that enters the queue will get the first service from the API.

- Sliding Log
In this method, a time-stamped log is used to identify different user logs. With each new request, the total number of the logs is calculated, and when logs exceed a certain rate limit, they will be discarded.

- Sliding Window
Essentially combining fixed window and sliding log algorithms, with this approach both a counter and a log are used to determine a faster rate limiting process. The small number of data needed to assess each request allows a faster calculation process, making it ideal for processing a large number of requests.