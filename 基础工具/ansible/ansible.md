# ansible简介

![ansible-logo](ansible-logo.jpg)

Ansilbe是一个部署一群远程主机的工具。远程的主机可以是本地或者远程的虚拟机，也可以是远程的物理机。
此名取自Ansible作者最喜爱的《安德的游戏》小说，ansible是虚构的超光速通讯装置。

# ansible架构

ansible部署简单，只需要在控制主机上部署ansible环境，被控制端上只要求安装ssh和python环境即可。

# ansible.cfg

在Ansible中，它的配置文件是一个名为`ansible.cfg`的配置文件，在运行Ansible命令时，Ansible将会按照预先设定的顺序查找配置文件，检查到哪个就用哪个。Ansible预先设定的优先级顺序如下：

1. **ANSIBLE_CFG**：首先，Ansible命令会先检查环境变量，及这个环境变量将指向的配置文件。
2. **`./ansible.cfg`**：其次，将会检查当前目录下的`ansible.cfg`配置文件。
3. **`~/.ansible.cfg`**：再次，将会检查当前用户home目录下的`ansible.cfg`配置文件。
4. **`/etc/ansible/ansible.cfg`**：最后，将会检查在安装Ansible时自动生产的配置文件。

defaults配置（查看配置可以用ansible-config list命令）：

| 配置项 | 说明 | 默认值 |
| :---: | :---: | :---: |
| inventory | ansible inventory文件路径 | /etc/ansible/hosts |
| remote_user | ansible执行远程登录用户 | 没有规定，一般是当前用户 |
| ask_pass | 运行ansible是否提示输入密码 | false |
| host_key_checking | ansible第一次连接客户端时是否要检查ssh密钥 | true |

# 命令行模式

Ansible提供了一个命令行工具，在官方文档中起给命令行起了一个名字叫Ad-Hoc Commands。ansible命令的格式是：

```sh
ansible <host-pattern> [options]
```

例如：`ansible all -m ping`，这里假设可以用ssh密钥访问，并且远端机器也有当前的用户名。`-m`表示要用的模块名，如果模块需要参数需要用`-a`传进参数。

# 常用模块

可以用`ansible-doc -l`命令查看模块的详细说明。

- **ping模块**：

ping是测试远程节点的SSH连接是否就绪的常用模块，但是它并不像Linux命令那样简单地ping一下远程节点，而是先检查能否通过SSH登陆远程节点，再检查其Python版本能否满足要求，如果都满足则会返回pong，表示成功。使用方式如下：

```sh
ansible all -m ping
```

ping无须任何参数。上述命令输出结果如下所示：

```sh
192.168.30.109 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

# playbook模式

只有脚本才可以重用，避免总敲重复的代码。Ansible脚本的名字叫Playbook，使用的是YAML的格式。
Ansible提供了一个单独的命令：ansible-playbook命令，我们可以通过这个命令来执行yaml脚本：

```sh
ansible-playbook demo.yaml
```

最基本的playbook脚本分为三个部分：

1. 在哪些机器上以什么身份执行。
2. 执行的任务有哪些。
3. 善后任务有哪些。

比如下面的playbook（在单一playbook文件中，可以用“---”区分多个play。还有“...”用来表示play的结尾，也可省略）：

```yaml
---
- hosts: server1
  user: root
  vars:
    http_port: 80
    max_clients: 200

  tasks:
    - name: Write apache config file
      template: src=/home/test1/httpd.j2 dest=/home/test2/httpd.conf
      notify:
        - restart apache
    - name: Ensure apache is running
      service: name=httpd state=started

  handlers:
    - name: restart apache
      service: name=httpd state=restarted
```

- **主机和用户**：

| key | 含义 |
| :---: | :---: |
| hosts | 为主机的IP，或者主机组名，或者关键字all |
| remote_user | 以哪个身份登录远端 |
| become | 切换成其他用户身份执行，值为yes或者no |
| become_method | 与become一起使用，值可以为sudo/su等 |
| become_user | 要切换成的用户身份，默认为root |

为了安全一般是不允许用root登录ssh的，所以一般是先用普通用户登录ssh，然后再切换为root用户（sudo）。

- **Tasks任务列表**：

tasks是从上到下顺序执行，如果中间发生错误，那么整个playbook会中止。你可以改修文件后，再重新执行。每一个任务都是对模块的一次调用，只是使用不同的参数和变量而已。每一个任务最好有一个name属性，这样在执行yaml脚本时，可以看到执行进度信息。
task中每个action会调用一个module，在module中会去检查当前系统状态是否需要重新执行。

- **响应事件Handler**：

Handlers里面的每一个handler，也是对module的一次调用。而handlers与tasks不同，tasks会默认的按定义顺序执行每一个task，handlers则不会，它需要在tasks中被调用，才有可能被执行。
Tasks中的任务都是有状态的，changed或者ok。 在Ansible中，只在task的执行状态为changed的时候，才会执行该task调用的handler，这也是handler与普通的event机制不同的地方。
在所有的任务里表执行之后执行，如果有多个task notify同一个handler,那么只执行一次。
handlers是按照在handlers中定义个顺序执行的，而不是安装notify的顺序执行的。
下面的例子定义的顺序是1>2>3，notify的顺序是3>2>1，实际执行顺序：1>2>3：

```yaml
---
- hosts: lb
  remote_user: root
  gather_facts: no
  vars:
      random_number1: "{{ 10000 | random }}"
      random_number2: "{{ 10000000000 | random }}"
  tasks:
  - name: Copy the /etc/hosts to /tmp/hosts.{{ random_number1 }}
    copy: src=/etc/hosts dest=/tmp/hosts.{{ random_number1 }}
    notify:
      - define the 3nd handler
  - name: Copy the /etc/hosts to /tmp/hosts.{{ random_number2 }}
    copy: src=/etc/hosts dest=/tmp/hosts.{{ random_number2 }}
    notify:
      - define the 2nd handler
      - define the 1nd handler
  handlers:
  - name: define the 1nd handler
    debug: msg="define the 1nd handler"
  - name: define the 2nd handler
    debug: msg="define the 2nd handler"
  - name: define the 3nd handler
    debug: msg="define the 3nd handler"
```

# 变量优先级

下面是变量的优先级，由高到低：

- extra vars：通过命令行穿进去的变量，例如`-e "user=my_user"`。
- include params
- role (and include_role) params
- set_facts / registered vars
- include_vars
- task vars (only for the task)：在task中定义的变量。
- block vars (only for tasks in block)
- role vars (defined in role/vars/main.yml)
- play vars_files
- play vars_prompt
- play vars
- host facts / cached set_facts
- playbook host_vars/*playbook group_vars/all
- inventory host_vars/*
- inventory file or script host vars
- playbook group_vars/*
- inventory group_vars/*
- playbook group_vars/all
- inventory group_vars/all
- inventory file or script group vars
- role defaults (defined in role/defaults/main.yml)
- command line values (for example, -u my_user, these are not variables)

group_vars 存放的是组变量，group_vars/all.yml 表示所有主机有效，等同于[all:vars]，grous_vars/etcd.yml 表示etcd组主机有效，等同于[etcd:vars]。

# roles

roles 用于层次性、结构化地组织playbook。
在ansible中,通过遵循特定的目录结构,就可以实现对role的定义：

```sh
site.yml
roles/
├── myrole
    ├── tasks
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── defaults
    │   └── main.yml
    ├── vars
    │   └── main.yml
    ├── files
    ├── templates
    ├── README.md
    ├── meta
    │   └── main.yml
    └── tests
        ├── inventory
        └── test.yml
```

ansible并不要求role包含上述所有的目录及文件，根据role的功能需要加入对应的目录和文件。下面是每个目录和文件的功能：

- 如果`roles/x/tasks/main.yml`存在, 其中列出的 tasks 将被添加到 play 中，所以这个文件也可以视作role的入口文件，想看role做了什么操作，可以从此文件看起。
- 如果`roles/x/handlers/main.yml`存在, 其中列出的 handlers 将被添加到 play 中
- 如果`roles/x/vars/main.yml`存在, 其中列出的 variables 将被添加到 play 中
- 如果`roles/x/meta/main.yml`存在, 其中列出的 “角色依赖” 将被添加到 roles 列表中
- `roles/x/tasks/main.yml`中所有tasks，可以引用`roles/x/{files,templates,tasks}`中的文件，不需要指明文件的路径。

roles和tasks的执行顺序：

- pre_tasks > role > tasks > post_tasks


# 参考资料

- [Ansible自动化运维教程](https://www.w3cschool.cn/automate_with_ansible/)
- [Ansible入门](https://www.bookstack.cn/read/ansible-first-book/README.md)
- [ansible官方文档](https://docs.ansible.com/ansible/2.9/index.html)
- [Ansible常用模块基本操作](https://www.cnblogs.com/vipygd/p/11625142.html)
- [初窥Ansible playbook](https://www.cnblogs.com/vipygd/p/13034739.html)
- [ansible官网变量优先级文档](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)