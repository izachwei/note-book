## 查询设置index分片信息

### 创建指定分片数量、副本数量的索引
```
PUT /job_idx_shard_temp
{
    "mappings":{
        "properties":{
            "id":{"type":"long","store":true},
            "area":{"type":"keyword","store":true},
            "exp":{"type":"keyword","store":true},
            "edu":{"type":"keyword","store":true},
            "salary":{"type":"keyword","store":true},
            "job_type":{"type":"keyword","store":true},
            "cmp":{"type":"keyword","store":true},
            "pv":{"type":"keyword","store":true},
            "title":{"type":"text","store":true},
            "jd":{"type":"text"}

        }
    },
    # 如下配置会有9个分片，三个主分片六个副本分片
    "settings":{
        # 指定三个主分片
        "number_of_shards":3,
        # 指定一个主分片会有两个副本分片
        "number_of_replicas":2
    }
}
```

### 查看分片、主分片、副本分片
GET /_cat/indices?v

### 查看索引的分片信息

`GET /report_synchronizer_*/_search_shards`