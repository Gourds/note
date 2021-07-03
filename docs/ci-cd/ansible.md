

### 基本
#### ansible速度优化
```
#M0 开启执行速度记录插件
callback_whitelist = profile_tasks
	ANSIBLE_CALLBACK_WHITELIST = profile_tasks
 
#M1.1 如可以关闭gather_fact
 
#M1.2 设置fact缓存（支持jsonfile和redis）
设置facts缓存(缓存支持jsonfile和redis)
gathering: smart|implicit|explicit
smart表示默认收集facts，但facts已有的情况下不会收集，即使用缓存facts
implicit表示默认收集facts，要禁止收集，必须使用gather_facts: False
explicit则表示默认不收集，要显式收集，必须使用gather_facts: Ture
[defaults]
gathering = smart
fact_caching_timeout = 86400
fact_caching = jsonfile
fact_caching_connection = /path/to/cachedir
 
#M1.3 关闭秘钥检测
host_key_checking = False
	ANSIBLE_HOST_KEY_CHECKING = False
 
#M1.4 关闭OpenSSH服务的DNS PTR
sed  -i  '/^GSSAPI/s/yes/no/g；/UseDNS/d；/Protocol/aUseDNS no' /etc/ssh/sshd_config
 
#M2 采用ssh多路复用（长连接方式）
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
	ANSIBLE_SSH_ARGS = -C -o ControlMaster=auto -o ControlPersist=5d
 
#M3 采用Pipline 
#https://docs.ansible.com/ansible/latest/reference_appendices/config.html
	ANSIBLE_SSH_PIPELINING = True	cat /etc/sudoers | grep requiretty
 
#M4 修改task执行策略
#修改ansible执行策略，需要ansible2.0版本以上，修改默认的配置linear --> strategy
 
#M5 使用Mitogen的plugin插件
https://mitogen.networkgenomics.com/howitworks.html
wget https://files.pythonhosted.org/packages/source/m/mitogen/mitogen-0.2.7.tar.gz
tar axf mitogen-0.2.7.tar.gz -C /opt/
[defaults]
strategy_plugins = /opt/mitogen-0.2.7/ansible_mitogen/plugins/strategy
strategy = mitogen_linear
```
#### 变量优先级
> 以下为优先级从低到高
> **注意：**第一个不是命令行-e提供的变量， -e提供的是额外的变量是最后一个

```bash
command line values (eg “-u user”)
role defaults [1]
inventory file or script group vars [2]
inventory group_vars/all [3]
playbook group_vars/all [3]
inventory group_vars/* [3]
playbook group_vars/* [3]
inventory file or script host vars [2]
inventory host_vars/* [3]
playbook host_vars/* [3]
host facts / cached set_facts [4]
play vars
play vars_prompt
play vars_files
role vars (defined in role/vars/main.yml)
block vars (only for tasks in block)
task vars (only for the task)
include_vars
set_facts / registered vars
role (and include_role) params
include params
extra vars (always win precedence)
```
### 常见
#### 问题：Jinja2模板空白控制及换行
```shell
#Template like this 
solr.replication.master=
    {% if ansible_eth0.ipv4.address == servermaster.eth0 %}
        false
    {% else %}
        true
    {% endif %}

solr.replication.slave=false
#And the output should look like this:
solr.replication.master=true
solr.replication.slave=false
#What I am actually getting is:
solr.replication.master=truesolr.replication.slave=false

```
> 解决：
> 在jinja2模板第一行加上 `#jinja2: trim_blocks:False` 
> 参考
> - [https://www.kancloud.cn/manual/jinja2/70455](https://www.kancloud.cn/manual/jinja2/70455)
> - [stackoverflow](https://stackoverflow.com/questions/22350175/how-do-i-get-an-ansible-template-to-honor-new-lines-after-a-conditional) 



### 参考文档
> - [速度优化参考](https://dzone.com/articles/speed-up-ansible) 
> - [issues-Verbose output from ansible-playbook when using list of hashed variables](https://github.com/ansible/ansible/issues/5564) 
> - [Ansible特殊变量](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html) 



