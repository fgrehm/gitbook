# Using Handler API

HandlerAPI is any custom struct which has an `*iris.Context` field and known methods signatures.

Before continuing I'd like you to note that this method is slower than `iris.Get, Post..., Handle, HandleFunc`.

It might sound awful, I'm not using it myself, I did it because there developers used to frameworks 
with the 'MVC' pattern, so think of it like the 'Controller'. 
If you don't care about routing performance(~ms) and you like to spend some code time, you're free to use it.

Instead of writing Handlers/HandlerFuncs for each API routes, you can use the `iris.API` function.

```go
API(path string, api HandlerAPI, middleware ...HandlerFunc) error
```

**For example**, for a user API you need some of these routes:

* GET `/users` , for selecting all
* GET`/users/:id` , for selecting specific
* PUT `/users` , for inserting
* POST `/users/:id` , for updating
* DELETE `/users/:id` , for deleting

Normally, with HandlerFuncs you should do something like this:

```go
iris.Get("/users", func(ctx *iris.Context){})
iris.Get("/users/:id", func(ctx *iris.Context){ id := ctx.Param("id) })

iris.Put("/users",...)

iris.Post("/users/:id", ...)

iris.Delete("/users/:id", ...)
```

**But** with API you can do this instead:

```go
package main

import (
    "github.com/kataras/iris"
)

type UserAPI struct {
    *iris.Context
}

// GET /users
func (u UserAPI) Get() {
    u.Write("Get from /users")
    // u.JSON(iris.StatusOK, myDb.AllUsers())
}

// GET /:param1 param1's value is the 'id' param
func (u UserAPI) GetBy(id string) { // id equals to u.Param("param1")
    u.Write("Get from /users/%s", id)
    // u.JSON(iris.StatusOK, myDb.GetUserById(id))
}

// PUT /users
func (u UserAPI) Put() {
    name := u.FormValue("name")
    // myDb.InsertUser(...)
    println(string(name))
    println("Put from /users")
}

// POST /users/:param1
func (u UserAPI) PostBy(id string) {
    name := u.FormValue("name") // you can still use the whole Context's features!
    // myDb.UpdateUser(...)
    println(string(name))
    println("Post from /users/" + id)
}

// DELETE /users/:param1
func (u UserAPI) DeleteBy(id string) {
    // myDb.DeleteUser(id)
    println("Delete from /" + id)
}

func main() {
    iris.API("/users", UserAPI{})
    iris.Listen(":8080")
}
```

As you saw you can still get other request values via the \*iris.Context, API has all the  flexibility of handler\/handlerfunc.

If you want to use **more than one named parameter**, simply do this:

```go
// users/:param1/:param2
func (u UserAPI) GetBy(id string, otherParameter string) {}
```

API receives a third parameter which are the middlewares, is optional parameter:

```go
func main() {
    iris.API("/users", UserAPI{}, myUsersMiddleware1, myUsersMiddleware2)
    iris.Listen(":8080")
}

func myUsersMiddleware1(ctx *iris.Context) {
    println("From users middleware 1 ")
    ctx.Next()
}
func myUsersMiddleware2(ctx *iris.Context) {
    println("From users middleware 2 ")
    ctx.Next()
}

```

Available methods: "GET", "POST", "PUT", "DELETE", "CONNECT", "HEAD", "PATCH", "OPTIONS", "TRACE" should use this **naming convention**:  **Get/GetBy, Post/PostBy, Put/PutBy** and so on...

