# Домашнее задание к занятию 2 «Работа с Playbook»

## Основная часть

1. Подготовьте свой inventory-файл `prod.yml`.
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev). Конфигурация vector должна деплоиться через template файл jinja2. От вас не требуется использовать все возможности шаблонизатора, просто вставьте стандартный конфиг в template файл. Информация по шаблонам по [ссылке](https://www.dmosk.ru/instruktions.php?object=ansible-nginx-install).
3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.
4. Tasks должны: скачать дистрибутив нужной версии, выполнить распаковку в выбранную директорию, установить vector.
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги. Пример качественной документации ansible playbook по [ссылке](https://github.com/opensearch-project/ansible-playbook).
10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ предоставьте ссылку на него.

---

## Ответы

1. 
```
clickhouse:
  hosts:
    clickhouse:
      ansible_connection: docker
vector:  
  hosts:
    vector:
      ansible_connection: docker
```
2.
```
- name: Install Vector
    hosts: vector
      handlers: 
      - name: restart vector service
         become: true
         ansible.builtin.service:
          name: vector
          state: restarted
  tasks:
    - name: Get Vector distrib
      ansible.builtin.get_url:
        url: "https://packages.timber.io/vector/{{ vector_version }}/vector-{{ vector_version }}-1.x86_64.rpm"
        dest: "./vector-{{ vector_version }}-1.x86_64.rpm"
        mode: 0644"
    - name: Install vector packages
      become: true
      ansible.builtin.yum:
        name:
          - vector-{{ vector_version }}-1.x86_64.rpm
        state: present
    - name: Config template
      ansible.builtin.template:
        src: vector.j2
        dest: vector.yml
        mode: "0644"
        validate: vector validate --no-environment --config-yaml %s      
```
3,4,5.
```
root@linuxmint203:/home/linuxmint/devops-ansible/08-ansible-02-playbook/playbook# ansible-lint site.yml
WARNING  Listing 8 violation(s) that are fatal
name[missing]: All tasks should be named.
site.yml:11 Task/Handler: block/always/rescue 

risky-file-permissions: File permissions unset or incorrect.
site.yml:12 Task/Handler: Get clickhouse distrib

risky-file-permissions: File permissions unset or incorrect.
site.yml:18 Task/Handler: Get clickhouse distrib

fqcn[action-core]: Use FQCN for builtin module actions (meta).
site.yml:30 Use `ansible.builtin.meta` or `ansible.legacy.meta` instead.

jinja[spacing]: Jinja2 spacing could be improved: create_db.rc != 0 and create_db.rc !=82 -> create_db.rc != 0 and create_db.rc != 82 (warning)
site.yml:32 Jinja2 template rewrite recommendation: `create_db.rc != 0 and create_db.rc != 82`.

yaml[empty-lines]: Too many blank lines (3 > 2)
site.yml:39

yaml[new-line-at-end-of-file]: No new line character at the end of file
site.yml:65

yaml[trailing-spaces]: Trailing spaces
site.yml:65

Read documentation for instructions on how to ignore specific rule violations.

                       Rule Violation Summary                        
 count tag                           profile    rule associated tags 
     1 jinja[spacing]                basic      formatting (warning) 
     1 name[missing]                 basic      idiom                
     1 yaml[empty-lines]             basic      formatting, yaml     
     1 yaml[new-line-at-end-of-file] basic      formatting, yaml     
     1 yaml[trailing-spaces]         basic      formatting, yaml     
     2 risky-file-permissions        safety     unpredictability     
     1 fqcn[action-core]             production formatting           

Failed: 7 failure(s), 1 warning(s) on 1 files. Last profile that met the validation criteria was 'min'.
```
- После исправления ошибок
```
root@linuxmint203:/home/linuxmint/devops-ansible/08-ansible-02-playbook/playbook# ansible-lint site.yml
WARNING  Listing 1 violation(s) that are fatal
jinja[spacing]: Jinja2 spacing could be improved: create_db.rc != 0 and create_db.rc !=82 -> create_db.rc != 0 and create_db.rc != 82 (warning)
site.yml:35 Jinja2 template rewrite recommendation: `create_db.rc != 0 and create_db.rc != 82`.

Read documentation for instructions on how to ignore specific rule violations.

              Rule Violation Summary               
 count tag            profile rule associated tags 
     1 jinja[spacing] basic   formatting (warning) 

Passed: 0 failure(s), 1 warning(s) on 1 files. Last profile that met the validation criteria was 'min'.
```
6.
```
root@linuxmint203:/home/linuxmint/devops-ansible/08-ansible-02-playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff
[WARNING]: Found both group and host with same name: vector
[WARNING]: Found both group and host with same name: clickhouse
[WARNING]: While constructing a mapping from /home/linuxmint/devops-ansible/08-ansible-02-playbook/playbook/site.yml, line 13, column 11, found a duplicate dict key (ansible.builtin.get_url).
Using last defined value only.

PLAY [Install Clickhouse] **********************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************
ok: [clickhouse] => (item=clickhouse-client)
ok: [clickhouse] => (item=clickhouse-server)
ok: [clickhouse] => (item=clickhouse-common-static)

TASK [Install clickhouse packages] *************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Flush handlers] **************************************************************************************************************************************************************************

TASK [Create database] *************************************************************************************************************************************************************************
ok: [clickhouse]

PLAY [Install Vector] **************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
ok: [vector]

TASK [Get Vector distrib] **********************************************************************************************************************************************************************
ok: [vector]

TASK [Install vector packages] *****************************************************************************************************************************************************************
ok: [vector]

TASK [Config template] *************************************************************************************************************************************************************************
ok: [vector]

PLAY RECAP *************************************************************************************************************************************************************************************
clickhouse                 : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector                     : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

root@linuxmint203:/home/linuxmint/devops-ansible/08-ansible-02-playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --check
[WARNING]: Found both group and host with same name: clickhouse
[WARNING]: Found both group and host with same name: vector

PLAY [Install Clickhouse] **********************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************
ok: [clickhouse] => (item=clickhouse-client)
ok: [clickhouse] => (item=clickhouse-server)
ok: [clickhouse] => (item=clickhouse-common-static)

TASK [Install clickhouse packages] *************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Flush handlers] **************************************************************************************************************************************************************************

TASK [Create database] *************************************************************************************************************************************************************************
skipping: [clickhouse]

PLAY [Install Vector] **************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
ok: [vector]

TASK [Get Vector distrib] **********************************************************************************************************************************************************************
ok: [vector]

TASK [Install vector packages] *****************************************************************************************************************************************************************
ok: [vector]

TASK [Config template] *************************************************************************************************************************************************************************
ok: [vector]

PLAY RECAP *************************************************************************************************************************************************************************************
clickhouse                 : ok=3    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
vector                     : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
7,8.
```
root@linuxmint203:/home/linuxmint/devops-ansible/08-ansible-02-playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff
[WARNING]: Found both group and host with same name: vector
[WARNING]: Found both group and host with same name: clickhouse

PLAY [Install Clickhouse] **********************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************
ok: [clickhouse] => (item=clickhouse-client)
ok: [clickhouse] => (item=clickhouse-server)
ok: [clickhouse] => (item=clickhouse-common-static)

TASK [Install clickhouse packages] *************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Flush handlers] **************************************************************************************************************************************************************************

TASK [Create database] *************************************************************************************************************************************************************************
ok: [clickhouse]

PLAY [Install Vector] **************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
ok: [vector]

TASK [Get Vector distrib] **********************************************************************************************************************************************************************
ok: [vector]

TASK [Install vector packages] *****************************************************************************************************************************************************************
ok: [vector]

TASK [Config template] *************************************************************************************************************************************************************************
ok: [vector]

PLAY RECAP *************************************************************************************************************************************************************************************
clickhouse                 : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector                     : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
9. 
Файл about.md
