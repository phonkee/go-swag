# swag

Simple swagger interface

```go

// instantiate new swag
s := swag.New()

// create api path prefix
api := s.PathPrefix("/api/v1")

// post api
post := api.PathPrefix("/post")

// define post endpoints
post.Path("/").Get(
    swag.Responses(
        swag.Response(http.StatusOk).Description("list of posts").Returns([]Post{}),
        swag.Response(http.StatusNotFound).Description("no posts found"),
    )
).Post(
    // now define what http.POST accepts
    swag.Accepts(Post{}, func(r swag.Returner) {
    	r.Field("title").Required(true)
    }),
    
    // Define all available headers
    swag.Headers(
        swag.Header("X-Rate-Limit-Limit").Description("The number of allowed requests in the current period").Type("integer"),
    ),

    // provide all available responses
    swag.Responses(
        swag.Response(http.StatusOK),
        swag.Response(http.StatusNotFound),
    )
)

// new path, variable `id` should be automatically added
post.Path("/{id}").Post(
	// define what we accept (simple)
    swag.Accepts(UpdatePost{}),
    
    // define all responses
    swag.Responses(
        swag.Response(http.StatusOK).Description("thank you").Returns(Post{}, func(r swag.Returner) {
        	r.Inline()
        }),
        swag.Response(http.StatusUnauthorized).Description("Please login"),
        swag.Response(http.StatusBadRequest),
    ),
).Delete(
	// responses for delete
    swag.Responses(
        swag.Response(http.StatusAccepted),
        swag.Response(http.StatusNotFound),
    ),
)

```
