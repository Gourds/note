### 授权
```bash
#正则示例
jws1.*
IT-jws1(/merge(/.*)?)?
IT-jws1(/register(/(merge.*|register-etcd))?)?
```
###
### DSL相关
#### 注意-新版本任务名称改变
```bash
#Old version
workflowJob(jobname){
    description('xxx')

    parameters {
        choiceParam('SPEED', ['0.5', '1.0', '1.5'], 'xxx')
    }
}
#New version 2.150.1
pipelineJob(jobname){
    description('xxx')

    parameters {
        choiceParam('SPEED', ['0.5', '1.0', '1.5'], 'xxx')
    }
}
```

#### 配置任务的重试机制
添加重试并连续失败后发送邮件
```groovy
publishers {
    retryBuild {
      rerunIfUnstable()
      retryLimit(3)
      progressiveDelay(60, 600)
      }
    extendedEmail {
        recipientList('gourds@yeah.net')
        defaultSubject('$DEFAULT_SUBJECT')
        replyToList('$DEFAULT_REPLYTO')
        defaultContent('${SCRIPT, template="groovy-html.template"}')
        contentType('text/html')
        triggers {
            stillFailing {
                recipientList('gourds@yeah.net')
            }
        }
    }
}
```

#### 邮件模板

使用：Download template and put /var/lib/jenkins/email-templates

Example:
```bash
ls /var/lib/jenkins/email-templates/groovy-html.template
```

#### Jenkins之Groovy个人示例
##### 判断关联变量关系后返回
```groovy
#AssignDatabase return Yes or No
import groovy.json.JsonSlurper
if (AssignDatabase.contains('Yes')){
  def redis_ip = new URL("http://10.4.0.198:2379/v2/keys/SL210/210/6201/gm/redis").text
                           "http://10.4.0.198:2379/v2/keys/SL210/210/6201/gm/redis"
  def slurper = new JsonSlurper()
  def result = slurper.parseText(redis_ip)
  return [result.node.value.split(':')[0]]
}else{
    return ['Auto']
}
```
##### 对关联变量进行截取取值
```groovy
#ACID:210:6201:ad52a590-7db1-41e5-93e8-a25ae83ad275
import groovy.json.JsonSlurper
String gid = ACID.split(':')[0]
String shardid = ACID.split(':')[1]
etcd_url = 'http://10.4.0.198:2379' + 'v2/keys' + '/SL210/'  + gid + '/' + shardid + '/gm/redis'
def etcd_data = new URL(etcd_url).text
def slurper = new JsonSlurper()
def result = slurper.parseText(etcd_data)
return etcd_url
```
##### 权限控制相关
> 可以使用以下插件
> - `role-based strategy` 
> -  `Folder Authorization Strategy` 

```xml
#对于目录下的任务授权类似（参考https://issues.jenkins.io/browse/JENKINS-24767）或直接使用Folder这个插件
scratch-parent(/scratch-child(/.*)?)?
```
