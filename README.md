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
post.Path("/").Post(
    swag.AcceptJSON(CreatePost{}, func(a swag.JSONAcceptor) {
    	a.Field("title").Required(true)
    }),
    swag.ReturnJSON(Post{}, func(r swag.JSONReturner) {
    	// inline model - do not reference
    	r.Inline()
    }),
    swag.Responses(
        swag.Response(http.StatusOK),
        swag.Response(http.StatusUnauthorized),
        swag.Response(http.StatusBadRequest),
    ),
).Get(
    swag.ReturnJSON(Post{}, func(r swag.JSONReturner) {
    	r.Field("title").Required(true)
    }),
    swag.Responses(
        swag.Response(http.StatusOK),
        swag.Response(http.StatusNotFound),
    ),
).Delete(
    swag.Responses(
        swag.Response(http.StatusAccepted),
        swag.Response(http.StatusNotFound),
    ),
)
```
