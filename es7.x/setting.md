匿名用户权限设置

    xpack.security.enabled: true
    
    xpack.security.transport.ssl.enabled: true
    
    xpack.security.authc:
      anonymous:
        username: anonymous_user
        roles: kibana_dashboard_only_user, apm_system, watcher_admin, logstash_system, rollup_user, kibana_user, beats_admin, remote_monitoring_agent, rollup_admin, code_user, snapshot_user, monitoring_user, logstash_admin, machine_learning_user, machine_learning_admin, watcher_user, apm_user, beats_system, reporting_user, kibana_system, transport_client, remote_monitoring_collector, code_admin, superuser, ingest_admin
        authz_exception: true

roles没搞清楚

添加用户和权限

https://www.elastic.co/guide/en/elasticsearch/reference/7.1/users-command.html

    bin/elasticsearch-users useradd jacknich -p theshining -r network,monitoring
    
## SSL certificate API

https://www.elastic.co/guide/en/elasticsearch/reference/7.1/security-api-ssl.html


## 创建一个用户 xmj:123456
    bin/elasticsearch-users useradd xmj -p 123456 -r network,monitoring
    bin/elasticsearch-users useradd xmj2 -p 123456 -r network,monitoring
##  使用用户 xmj    创建索引
    curl -H "Content-Type: application/json" -u xmj:123456 -XPUT 'localhost:9200/qx1_follow_hotel_mid/guest_history/440882199912120001' -d '
    {
        "history_list_live1" : "75109999_1677771_902",
        "history_list_live3" : "75109999_139814_902",
        "history_list_live6" : "75109999_34953_902",
        "sex" : "1",
        "history_list_live2" : "75109999_838885_902",
        "id_card" : "440882199912120001",
        "history_list_live5" : "75109999_46604_902",
        "history_list_live4" : "75109999_69907_902",
        "compellation" : "马云"
    }'
    
    curl -H "Content-Type: application/json" -u xmj:123456 -XPOST 'localhost:9200/test/2' -d '{"name":"jack"}'
    
## 查看创建的索引
### xmj
    [elk@localhost elasticsearch-7.1.1]$ curl -u xmj:123456 127.0.0.1:9200/_cat/indices
    yellow open qx1_follow_hotel_mid U56-zeXOShGS678PzPLi2g 1 1 1 0 9kb 9kb
### 匿名用户没有权限
    [elk@localhost elasticsearch-7.1.1]$ curl 127.0.0.1:9200/qx1_follow_hotel_mid/_search?pretty
    {
      "error" : {
        "root_cause" : [
          {
            "type" : "security_exception",
            "reason" : "missing authentication credentials for REST request [/qx1_follow_hotel_mid/_search?pretty]",
            "header" : {
              "WWW-Authenticate" : "Basic realm=\"security\" charset=\"UTF-8\""
            }
          }
        ],
        "type" : "security_exception",
        "reason" : "missing authentication credentials for REST request [/qx1_follow_hotel_mid/_search?pretty]",
        "header" : {
          "WWW-Authenticate" : "Basic realm=\"security\" charset=\"UTF-8\""
        }
      },
      "status" : 401
    }

## 查看权限

    [elk@localhost elasticsearch-7.1.1]$ curl -u xmj:123456 localhost:9200/_security/_authenticate?pretty
    {
      "username" : "xmj",
      "roles" : [
        "superuser"
      ],
      "full_name" : null,
      "email" : null,
      "metadata" : { },
      "enabled" : true,
      "authentication_realm" : {
        "name" : "default_file",
        "type" : "file"
      },
      "lookup_realm" : {
        "name" : "default_file",
        "type" : "file"
      }
    }
## 用户全县映射    
     [elk@localhost elasticsearch-7.1.1]$ curl -u xmj:123456 -X POST "localhost:9200/_security/role_mapping/mapping1" -H 'Content-Type: application/json' -d'
    > {
    >   "roles": [ "user"],
    >   "enabled": true, 
    >   "rules": {
    >     "field" : { "username" : "*" }
    >   },
    >   "metadata" : { 
    >     "version" : 1
    >   }
    > }
    > '
#error
用户名出错：

    [elk@localhost elasticsearch-7.1.1]$ curl -u xmj:1234568 127.0.0.1:9200/_security/role/click_role
    {"error":{"root_cause":[{"type":"security_exception","reason":"unable to authenticate user [xmj] for REST request [/_security/role/click_role]","header":{"WWW-Authenticate":"Basic realm=\"security\" charset=\"UTF-8\""}}],"type":"security_exception","reason":"unable to authenticate user [xmj] for REST request [/_security/role/click_role]","header":{"WWW-Authenticate":"Basic realm=\"security\" charset=\"UTF-8\""}},"status":401}[elk@localhost elasticsearch-7.1.1]$
    
    
 创建一个角色
 
    curl  -u xmj:123456 -X POST "localhost:9200/_security/role/role3" -H 'Content-Type: application/json' -d'
    {
      "cluster": ["all"],
      "indices": [
        {
          "names": [ "test"],
          "privileges": ["all"],
          "field_security" : { 
            "grant" : [ "title", "body" ]
          },
          "query": "{\"match\": {\"title\": \"foo\"}}" 
        }
      ],
      "applications": [
        {
          "application": "myapp",
          "privileges": [ "admin", "read" ],
          "resources": [ "*" ]
        }
      ],
      "run_as": [ "other_user" ],
      "metadata" : { 
        "version" : 1
      }
    }
    '
 
 
 报错：基本班没有权限设置【当前许可证不兼容】
 
    {"error":{"root_cause":[{"type":"security_exception","reason":"current license is non-compliant for [field and document level security]","license.expired.feature":"field and document level security"}],"type":"security_exception","reason":"current license is non-compliant for [field and document level security]","license.expired.feature":"field and document level security"},"status":403}[elk@localhost elasticsearch-7.1.1]$
 - 解决方案：启动trial license（30天试用）
>curl -u xmj:123456 -X POST "localhost:9200/_xpack/license/start_trial?acknowledge=true"
 
 
 ## 定义role
 
 https://www.elastic.co/guide/en/elastic-stack-overview/7.1/defining-roles.html
 
 
 ## 使用api 启动trial license（30天试用）
 
    curl -u xmj:123456 -X POST "localhost:9200/_xpack/license/start_trial?acknowledge=true"
 
 
 