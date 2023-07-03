# Домашнее задание к занятию 6 «Создание собственных модулей»

Создал файл `my_own_module.py`.
Создал плейбук для запуска модуля:
```
---
- name: Write a file
  hosts: localhost
  tasks:
    - name: Call my_own_module
      my_own_module:
        path: /root/tmp.txt
        content: 123
```
Результат:
```
root@ubnt2004:~/root/my_own_collection/ansible# ansible-playbook -v site.yml 
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features under development. This is a rapidly changing source
of code and can become unstable at any point.
Using /etc/ansible/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Write a file] ***********************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Call my_own_module] *****************************************************************************************************************************************************************************************************
changed: [localhost] => {"changed": true, "message": "File is created"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
```
root@ubnt2004:~/root/repo/my_own_collection/ansible# ansible-playbook -v site.yml
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features under development. This is a rapidly changing source
of code and can become unstable at any point.
Using /root/ansible/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Write a file] ***********************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Call my_own_module] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {"changed": false, "message": "File exists"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
Плейбук идемпотентен.

Инициализировал новую collection: 
```
ansible-galaxy collection init my_own_namespace.yandex_cloud_elk
```
Перенёс модуль в созданную collection, преобразовал в роль. Плейбук для роли:

```
---
- hosts: localhost
  collections:
    - my_own_namespace.yandex_cloud_elk

  tasks:
    - import_role:
        name: role_for_module

    - my_own_module:
        path: /root/tmp.txt
        content: 234
```

Создал тарбол для коллекции:
```
root@ubnt2004:~/root/my_own_collection/my_own_namespace/yandex_cloud_elk#  ansible-galaxy collection build
Created collection for my_own_namespace.yandex_cloud_elk at /root/my_own_collection/my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz

```

Установил коллекцию из тарбола:

```
root@ubnt2004:~/root/my_own_collection/test_tarball# ansible-galaxy collection install my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Installing 'my_own_namespace.yandex_cloud_elk:1.0.0' to '/root/ansible/.ansible/collections/ansible_collections/my_own_namespace/yandex_cloud_elk'
my_own_namespace.yandex_cloud_elk:1.0.0 was installed successfully
```

Проверил работу плейбука:

```
root@ubnt2004:~/root/my_own_collection/test_tarball# ansible-playbook -v site.yml 
Using /root/ansible/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] **************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [my_own_module] **********************************************************************************************************************************************************************************************************
changed: [localhost] => {"changed": true, "message": "File is created"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
```
root@ubnt2004:~/root/my_own_collection/test_tarball# ansible-playbook -v site.yml 
Using /root/ansible/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] **************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [my_own_module] **********************************************************************************************************************************************************************************************************
ok: [localhost] => {"changed": false, "message": "File exists"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

1) [Коллекция](https://github.com/bigorado/my_own_collection/tree/main/my_own_namespace)
2) [Тарбол](https://github.com/bigorado/my_own_collection/blob/main/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz)