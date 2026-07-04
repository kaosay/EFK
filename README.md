# EFK
How to use and deploy efk

## Set up password
```
docker exec -it efk-elasticsearch-1 bash
elasticsearch-setup-passwords auto
# Put password to compose.yaml

docker compose stop
docker compose up -d
```
## kibana创建k8s Index Template
```
方法一：使用 Kibana Dev Tools（最方便） ★推荐

打开你的 Kibana 页面（通常是 http://你的ES域名:5601）
左侧菜单找到 Management → Dev Tools（开发工具）
在左侧 Console 中粘贴以下完整命令，然后点击右上角的 ▶ 执行 按钮：
```

```
PUT _index_template/kubernetes-logs-template
{
  "index_patterns": ["kubernetes-*"],
  "priority": 999,
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    },
    "mappings": {
      "dynamic_templates": [
        {
          "kubernetes_labels": {
            "path_match": "kubernetes.labels.*",
            "mapping": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        {
          "kubernetes_labels_flattened": {
            "path_match": "kubernetes.labels",
            "mapping": {
              "type": "flattened"
            }
          }
        },
        {
          "kubernetes_annotations": {
            "path_match": "kubernetes.annotations",
            "mapping": {
              "type": "flattened"
            }
          }
        }
      ],
      "properties": {
        "@timestamp": { "type": "date" },
        "time": { "type": "date" },
        "ts": { "type": "date" },
        "kubernetes": {
          "properties": {
            "labels": { "type": "flattened" },
            "annotations": { "type": "flattened" }
          }
        }
      }
    }
  }
}
```
