# swag

Simple swagger for APIs.

```go

// instantiate new swag
s := swag.New(func(sw swag.Swagger) {
	sw.Title("hello world")
	sw.Description("Awesome api")

    // Define all available headers
    sw.Headers(
        swag.Header("X-Rate-Limit-Limit", func(h swag.Headerer) {
            h.Description("The number of allowed requests in the current period")
            h.Type(swag.TypeInteger)	
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
        }),
        swag.Response(http.StatusNotFound, func(r swag.Responder) {
            r.Description("no posts found")	
        }),
    )
).Post(
    // now define what http.POST accepts
    swag.Accepts(CreatePost{}, func(a swag.Acceptor) {
    	
    	// if you use pointer, required would be set automatically to false
    	a.Field("title").Required(false)
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
    ),
)

// new path, variable `id` should be automatically added
post.Path("/{id}", func(p swag.Pather) {
	// we can define for all actions that id is an integer (yay)
	p.PathArgument("id").Type(swag.TypeInteger)
}).Get( 
    // provide all available responses
    swag.Responses(
        swag.Response(http.StatusOK, func(r swag.Responder) {
        	r.Returns(Post{})
        }),
        swag.Response(http.StatusNotFound),
    ),
).Post(
	// define what we accept (simple)
    swag.Accepts(UpdatePost{}),
    
    // do not accept headers from top level
    swag.Headers(swag.NoHeaders())
    
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

// swagger `s` satisfies http.Handler so you can directly use it in http server
// swagger `s` has Error method that returns error if something goes wrong

// check for error (simplified)
if err := s.Error(); err != nil {
	panic(err)
}
```

# Author

Peter Vrba <phonkee@phonkee.eu>