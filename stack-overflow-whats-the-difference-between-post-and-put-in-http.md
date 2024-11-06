# whats-the-difference-between-using-PUT-and-POST-in-HTTP

https://stackoverflow.com/questions/630453/what-is-the-difference-between-post-and-put-in-http

**use `PUT` if the client is responsible for creating the object's unique ID**
**use `POST` if the server is responsible for creating the object's unique ID**

-  `PUT` is defined to assume idempotency
-  using `PUT` on the same object twice should result in the same state for
  the object, **i.e. use the `PUT` endpoint for both object creation and deletion**

examples:

when the use `POST`:

-  used to modify or update a resource
```
POST /questions/<existing_question> HTTP/1.1
```

-  used to create a resource where the server is responsible for creating the
unique id

```
POST /questions HTTP/1.1
```

when to use `PUT`:

-  used to modify or update a resource
```
POST /questions/<existing_question> HTTP/1.1
```

-  used to create a resource where the server is responsible for creating the
unique id

```
POST /questions/<new_question> HTTP/1.1
```

**NOTE: how url's are the same, this means that the server logic for the
endpoint needs to handle both UPDATE and CREATE**
