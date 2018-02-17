---
title: "Http Response in Golang"
date: 2018-02-17T11:49:41+05:30
categories: ["golang", "go", "http", "server", "template", "json"]
author: "Vivek Singh"
---

In this blog post, I am going to talk about HTTP server and how to respond with different types of HTTP response(text, JSON, template) in Go.

Creating a web server in Go is very simple and we can do it by writing just a few lines of code. To create an HTTP server in go you need to use `net/http` package. This is how a simple HTTP server code looks like in Go.

`server.go`
```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8081", nil)
}

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello World")
}
```

Once you run the file with command `go run server.go`, you will have a web server listening on port `8081`. Open a browser and access `http://localhost:8081/` you will get plain text response as `Hello world`. Above web server is very simple and it will respond with `Hello word` no matter what path you type after `/`.

Nowadays most of the web services use JSON to communicate with clients and this one web service can be used for different clients like Web Application, Android Application or iOs Application. Now we will see how to return a JSON response in Go.

At first, I will create `User` struct which will store user information. We will use the same struct to return user information as JSON response.

```go

type User struct  {
	Id int       `json:"id"`
	Name string  `json:"name"`
	Email string `json:"email"`
	Phone string `json:"phone"`
}

```

Let's create a handler name `jsonHandler` which will handle `/json` and return user information as JSON response.

To send JSON response, we need to encode the user information using JSON encoder and set the response header `Content-Type` to `application/json` while sending the response.


```go
func jsonHandler(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	user := User{Id: 1,
				Name: "John Doe",
				Email: "johndoe@gmail.com",
				Phone: "000099999"}
	json.NewEncoder(w).Encode(user)
}
```

Now `server.go` file should look as below
```go
package main

import (
	"fmt"
	"net/http"
	"encoding/json"
)

type User struct  {
	Id int       `json:"id"`
	Name string  `json:"name"`
	Email string `json:"email"`
	Phone string `json:"phone"`
}

func main() {
	http.HandleFunc("/", handler)
	http.HandleFunc("/json", jsonHandler)
	http.ListenAndServe(":8081", nil)
}

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello World")
}


func jsonHandler(w http.ResponseWriter, r *http.Request) {
  w.Header().Set("Content-Type", "application/json")
	user := User{Id: 1,
				Name: "John Doe",
				Email: "johndoe@gmail.com",
				Phone: "000099999"}
	json.NewEncoder(w).Encode(user)
}
```
Run this file and access `http://localhost:8081/json` in browser, you should get a json response as below

```json
{
  "id": 1,
  "name": "John Doe",
  "email": "johndoe@gmail.com",
  "phone": "000099999"
}
```

Now We will add a new handler `templateHandler` which will handle `/template` and respond with HTML file (template) as a response.
Let's create a template file as `template.html` which will display user information and will be used by `templateHandler`.

```html
<html>
    <head>
    </head>
    <body>
        <h3>Name : {{.Name}}</h3>
        <h3>Email : {{.Email}}</h3>
        <h3>Phone : {{.Phone}}</h3>
    </body>
</html>
```

New handler will use `html/template` package to parse and execute template files. Also, we need to set `Content-Type` header to `text/html; charset=utf-8` otherwise template will be returned as plain text.

```go
func templateHandler(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "text/html; charset=utf-8")
	t, err := template.ParseFiles("template.html")
	if err != nil {
		fmt.Fprintf(w, "Unable to load template")
	}

	user := User{Id: 1,
		Name: "John Doe",
		Email: "johndoe@gmail.com",
		Phone: "000099999"}
	t.Execute(w, user)
}
```

After adding new handler, `server.go` file should look as below

```go
package main

import (
	"fmt"
	"net/http"
	"encoding/json"
	"html/template"
)

type User struct  {
	Id int       `json:"id"`
	Name string	 `json:"name"`
	Email string `json:"email"`
	Phone string `json:"phone"`
}

func main() {
	http.HandleFunc("/", handler)
	http.HandleFunc("/json", jsonHandler)
	http.HandleFunc("/template", templateHandler)
	http.ListenAndServe(":8081", nil)
}

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello World")
}


func jsonHandler(w http.ResponseWriter, r *http.Request) {
	user := User{Id: 1,
				Name: "John Doe",
				Email: "johndoe@gmail.com",
				Phone: "000099999"}
	json.NewEncoder(w).Encode(user)
}

func templateHandler(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "text/html; charset=utf-8")
	t, err := template.ParseFiles("template.html")
	if err != nil {
		fmt.Fprintf(w, "Unable to load template")
	}

	user := User{Id: 1,
		Name: "John Doe",
		Email: "johndoe@gmail.com",
		Phone: "000099999"}
	t.Execute(w, user)
}
```

Once you run this file and access `http://localhost:8081/template` in the browser, you should be able to see user details rendered with the template.

All code snippets for this blog post can be found [here](https://gist.github.com/viveksyngh/b78f78556532df0a72c4b1182bc68101).
