# elastic stack 使用方式

## 配合kibana使用

https://www.elastic.co/cn/blog/getting-started-with-elasticsearch-security

https://www.elastic.co/cn/blog/elasticsearch-security-configure-tls-ssl-pki-authentication

## elastic 单独使用

###使用api 启动trial license（30天试用）
 
    curl -u xmj:123456 -X POST "localhost:9200/_xpack/license/start_trial?acknowledge=true"
    
###使用破解版

https://blog.csdn.net/qq_36666651/article/details/83539103

关键字：x-pack破解