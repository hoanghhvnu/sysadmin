# ES manipulate
## 1. Index
Insert an element.
Using own **id**:
```
PUT /megacorp/employee/1
{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing",
    "interests": [ "sports", "music" ]
}
```
Auto generate **id**:
Change PUT to POST, leave empty in **id** field:
```
POST /website/blog/
{
  "title": "My second blog entry",
  "text":  "Still trying this out...",
  "date":  "2014/01/01"
}
```
### Insert multiple with bulk
The REST API endpoint is /_bulk, and it expects the following JSON structure:
```
action_and_meta_data\n
optional_source\n
action_and_meta_data\n
optional_source\n
....
action_and_meta_data\n
optional_source\n
```
NOTE: the final line of data must end with a newline character \n.
Example:
```
$ cat requests
{ "index" : { "_index" : "test", "_type" : "type1", "_id" : "1" } }
{ "field1" : "value1" }
$ curl -s -XPOST localhost:9200/_bulk --data-binary @requests;
{ "index" : { "_index" : "test", "_type" : "type1", "_id" : "1" } }
{ "field1" : "value1" }
{ "delete" : { "_index" : "test", "_type" : "type1", "_id" : "2" } }
{ "create" : { "_index" : "test", "_type" : "type1", "_id" : "3" } }
{ "field1" : "value3" }
{ "update" : {"_id" : "1", "_type" : "type1", "_index" : "index1"} }
{ "doc" : {"field2" : "value2"} }
```
## 2. Search
#### a) Get an document
```
GET /megacorp/employee/1?pretty
```
Result:
```
{
  "_index" :   "megacorp",
  "_type" :    "employee",
  "_id" :      "1",
  "_version" : 1,
  "found" :    true,
  "_source" :  {
      "first_name" :  "John",
      "last_name" :   "Smith",
      "age" :         25,
      "about" :       "I love to go rock climbing",
      "interests":  [ "sports", "music" ]
  }
}
```
"pretty" phrase make easy to read.
#### b) Get all
```
GET /megacorp/employee/_search
```
#### c) Get only some field (SELECT)
```
GET /website/blog/123?_source=title,text
```
#### d) Get only source (without metadata)
```
GET /website/blog/123/_source
```
#### d) Get specific condition (WHERE clause)
Search lite:
```
GET /megacorp/employee/_search?q=last_name:Smith
```
Or with *query dsl*:
```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
```
#### e) LIMIT clause
Have **from**, and **size** instead.
```
GET /_search?size=5&from=10
```
#### e) Check if document exist
```
curl -i -XHEAD http://localhost:9200/website/blog/123
```
If exist:
```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=UTF-8
Content-Length: 0
```

If not:
```
HTTP/1.1 404 Not Found
Content-Type: text/plain; charset=UTF-8
Content-Length: 0
```
## 3. Update
#### a) Whole document
Similar Insert, but id of old document
```
PUT /website/blog/123
```
With id = 123 already exist
Then
```
{
  "_index" :   "website",
  "_type" :    "blog",
  "_id" :      "123",
  "_version" : 2,
  "created":   false
}
```
*_version* = 2 and *create* = *false*
## 3. Delete
```
DELETE /website/blog/123
```
