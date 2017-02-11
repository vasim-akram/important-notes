###### ES queries

```
curl -XGET http://localhost:9200/
// to show index
curl -XGET http://localhost:9200/_cat/indices?v
// to import json
curl -XPOST http://localhost:9200/_bulk  --data-binary @samle.json
// filter by multiple value on same field
curl -XGET http://localhost:9200/user-*/admin/_search?pretty=true -d '
{
   "size":2,
   "query":{
          "terms":{
                "name":["sadia","hasina","vasim"]
            }
      },
    "sort":[{"name":"asc"},{ "_score": "asc" }]
}'

// to enable timestamp in ES
$curl -XPOST localhost:9200/ -d '{
"mappings" : {
    "_default_":{
        "_timestamp" : {
            "enabled" : true,
            "store" : true
        }
    }
  }
}'
// aggregation in ES
curl -XGET http://localhost:9200/user-*/admin/_search?pretty=true -d  '{
  "size": 0,
  "query": {
    "filtered": {
      "filter": {
        "term": {
          "user": "vasim"      
        }
      }
    }
  },
  "aggs":{
       "sources":{
           "terms":{
                 "field": "_index"
             },
            "aggs":{
                "latest":{
                    "top_hits":{
                          "_source":[
                               "name","age"
                            ]
                      }
                  }
            }
        }
   }
}'
```
###### ES Inspiration

http://stackoverflow.com/questions/31665142/query-the-latest-document-of-each-type-on-elasticsearch
http://stackoverflow.com/questions/40913326/elasticsearch-get-the-latest-documents-grouped-by-multiple-fields
http://stackoverflow.com/questions/28958882/elasticsearch-filtered-query-vs-filter
