## HTTP CLIENT CHEAT SHEET

this personal cheatsheet

### Available HTTP Methods

| HTTP Method | CRUD operation | Description |
| ----------- | -------------- | ----------- |
| GET         | Read           |             |
| POST        | create         |             |
| PATCH       |                |             |
| DELETE      | delete         |             |
| PUT         | Update/Replace |             |

Below is a table summarizing HTTP methods by its idempotency and safety:

| HTTP Method | Idempotent | Safe |
| ----------- | ---------- | ---- |
| OPTIONS     | yes        | yes  |
| GET         | yes        | yes  |
| HEAD        | yes        | yes  |
| PUT         | yes        | no   |
| POST        | no         | no   |
| DELETE      | yes        | no   |
| PATCH       | no         | no   |

### HTTP Methods Status Codes

#### successfull

| HTTP code | message    | description                                                                                                                                                                                                    |
| --------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200       | OK         | In the response to successful GET, PATCH or DELETE.                                                                                                                                                            |
| 201       | Created    | In the response to POST that results in creation. When this status is received, the request body contains the description of the newly created entity in the JSON format (similar to the regular GET request). |
| 204       | No Content | In the response to a successful request that won’t return a body (like a DELETE request)                                                                                                                       |

#### error / client error

| HTTP code | message               | description                                                                                                                                     |
| --------- | --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| 400       | Bad Request           | The request is malformed, such as if the body of the request contains misformatted JSON.                                                        |
| 401       | Unauthorized          | No or invalid authentication details are provided. This code can be used to trigger an authentication pop-up if the API is used from a browser. |
| 403       | Forbidden             | Authentication succeeded but authenticated user does not have access to the resource.                                                           |
| 404       | Not Found             | A non-existent resource is requested.                                                                                                           |
| 500       | Internal Server Error | The server encountered an unexpected condition which prevented it from fulfilling the request.                                                  |

#### example error message schema

```json
{
  "errors": [
    {
      "status": "404",
      "title": "not found http exception",
      "detail": "An entity with the requested identifier does not exist."
    }
  ]
}
```

#### example body response message schema

```json
{
   "date" : "Date: Mon, 19 Sep 2016 17:52:34 GMT",
   "description" : "get all data from xyz",
   "status" : 200,
   "data": {
      "type": "users",
      "id": "1",
      "attributes": {
         "title": null,
         "email": "admin@local.com",
         "firstName": "John",
         "enabled": true,
         "lastLogin": "2016-09-19T11:01:31Z",
      },
      "relationships": {
         "owner": {
            "data":
            {
               "type": "businessunits",
                "id": "1"
                }
            },
         "businessUnits": {
            "data": [
               {
                  "type": "businessunits",
                   "id": "1"
                   }
               ]
            },
      }
}

```

if you used http client from vscode extention and file file like rest.http
you can send request without POSTMAN

# REST CLIENT

### Header

example send request header

```
POST https://example.com/v1/comments HTTP/1.1
content-type: application/json
User-Agent: rest-client
Accept-Language: en-GB,en-US;q=0.8,en;q=0.6,zh-CN;q=0.4
```

### Body

```
{
    "name": "sample",
    "time": "Wed, 21 Oct 2015 18:27:50 GMT"
}
```

### Combine Header and Request

_POST_

```http
POST https://example.com/v1/comments HTTP/1.1
content-type: application/json
User-Agent: rest-client
Accept-Language: en-GB,en-US;q=0.8,en;q=0.6,zh-CN;q=0.4

{
    "name": "sample",
    "time": "Wed, 21 Oct 2015 18:27:50 GMT"
}
```

for more example check below ..

## Example

_GET_

```http
GET http://127.0.0.1:8000/api/v1/posts/all HTTP/1.1
Content-Type: application/json
Accept: application/json

```

_POST_

```http
POST http://127.0.0.1:8000/api/v1/posts/store  HTTP/1.1
Content-Type: application/json
Accept: application/json

{
   "title": "this is title 4",
   "author": "nagara",
   "content": "this is my first content 4"
}

```

_PUT_

```http
PUT http://127.0.0.1:8000/api/v1/posts/update HTTP/1.1
Content-Type: application/json
Accept: application/json

{
   "id" : 2,
   "title": "this is title 4",
   "author": "nagara",
   "content": "this is my first content 4"
}
```

_DELETE_

```http
DELETE http://127.0.0.1:8000/api/v1/posts/delete HTTP/1.1
Content-Type: application/json
Accept: application/json

{
   "id" : 2,
}
```

## Example Laravel for rest

### controller

```php
<?php

namespace App\Http\Controllers;

use App\Model\Posts;
use Illuminate\Support\Str;
use Illuminate\Http\Request;

class PostController extends Controller
{
    //
    public function index()
    {
        $blog = new Posts;
        return json_encode($blog->all());
    }

    public function store(Request $request)
    {
        $blog = new Posts;
        $blog->title = $request->title;
        $blog->slug = Str::slug($request->title, "-");
        $blog->author = $request->author;
        $blog->content = $request->content;
        $blog->save();
        return response('sukses', 201);
    }

    public function update(Request $request)
    {

        try {
            $blog = Posts::findOrFail($request->id);
            $blog->title = $request->title;
            $blog->slug = Str::slug($request->title, "-");
            $blog->author = $request->author;
            $blog->content = $request->content;
            $blog->save();

            return json_encode($blog);
        } catch (\Throwable $th) {
            // throw $th;
            return json_encode("data tidak ada");
        }
    }

    public function destroy(Request $request)
    {
        try {
            $blog = Posts::findOrFail($request->id);
            $blog->delete();

            return json_encode("sukses delete");
        } catch (\Throwable $th) {
            // throw $th;
            return json_encode("data tidak ada");
        }
    }
}

```

### route

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::get('/v1/posts/query', 'PostController@show');
Route::post('/v1/posts/search', 'PostController@search');

Route::get('/v1/posts/all', 'PostController@index');
Route::post('/v1/posts/store', 'PostController@store');
Route::put('/v1/posts/update', 'PostController@update');
Route::delete('/v1/posts/delete', 'PostController@destroy');
```

## Example NODEJS

### controller

```javascrip
coming soon

```

### router

```javscript
coming soon
```

## Example Golang

### controller

```go
coming soon

```

### router

```go
coming soon
```