# swag

Simple swagger API.

```go

// instantiate new swag
s := swag.New(func(s swag.Swagger) {
	s.Title("hello world")
	s.Description("Awesome api")

    // Define all available headers
    s.Headers(
        swag.Header("X-Rate-Limit-Limit", func(h swag.Headerer) {
            h.Description("The number of allowed requests in the current period")
            h.Type(swag.Integer)	
        })
    ),
})

// create api path prefix
api := s.Path("/api/v1")

// post api
post := api.Path("/post")

// define post endpoints
post.Path("/").Get(
	
	// prepare all available responses
    swag.Responses(
        swag.Response(http.StatusOk, func(r swag.Responder) {
            r.Description("list of posts")
            r.Returns([]Post{})	
        })
        swag.Response(http.StatusNotFound, func(r swag.Responder) {
            r.Description("no posts found")	
        }),
    )
).Post(
    // now define what http.POST accepts
    swag.Accepts(CreatePost{}, func(r swag.Returner) {
    	r.Field("title").Required(true)
    }),
    
    // Define all available headers
    swag.Headers(
        swag.Header("X-Rate-Limit-Limit", func(h swag.Headerer) {
            h.Description("The number of allowed requests in the current period")
            h.Type(swag.Integer)	
        })
    ),

    // provide all available responses
    swag.Responses(
        swag.Response(http.StatusOK, func(r swag.Responder) {
        	r.Returns(Post{})
        }),
        swag.Response(http.StatusBadRequest),
    )
)

// new path, variable `id` should be automatically added
post.Path("/{id}").Get( 
    // provide all available responses
    swag.Responses(
        swag.Response(http.StatusOK, func(r swag.Responder) {
        	r.Returns(Post{})
        }),
        swag.Response(http.StatusNotFound),
    ) 	
).Post(
	// define what we accept (simple)
    swag.Accepts(UpdatePost{}),
    
    // define all responses
    swag.Responses(
        swag.Response(http.StatusOK, func(r swag.Responder) {
            r.Description("thank you")
            r.Returns(Post{}, func(r swag.Returner) {
            	r.Inline()
            })
        }),
        swag.Response(http.StatusUnauthorized, func(r swag.Responder) {
            r.Description("Please login"),
        }),
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
