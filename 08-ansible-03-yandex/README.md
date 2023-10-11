1. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает LightHouse.
```
tasks:
    - name: Lighthouse | Copy from git
      ansible.builtin.git:
        repo: "{{ lighthouse_vcs }}"
        version: master
        dest: "{{ lighthous_local_dir }}"
    - name: Lighthouse | Create ligthouse config
      become: true
      template:
        src: lighthouse.conf.j2
        dest: /etc/nginx/conf.d/defult.conf
        mode: "0644"
      notify: Reload-nginx
```
2. При создании tasks рекомендую использовать модули: `get_url`, `template`, `yum`, `apt`.
3. Tasks должны: скачать статику LightHouse, установить Nginx или любой другой веб-сервер, настроить его конфиг для открытия LightHouse, запустить веб-сервер.
```
tasks:
    - name: NGINX | Install epel-release
      become: true
      ansible.builtin.yum:
        name: epel-release
        state: present
    - name: NGINX | Install NGINX
      become: true
      ansible.builtin.yum:
        name: nginx
        state: present
      notify: Start-nginx
    - name: NGINX | Create general config
      become: true
      ansible.builtin.template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
        mode: "0644"
      notify: Reload-nginx
```
4. Подготовьте свой inventory-файл `prod.yml`.
```
clickhouse:
  hosts:
    clickhouse-01:
      ansible_host: 84.201.175.149
lighthouse:
  hosts:
    lighthouse-01:
      ansible_host: 158.160.100.74
vector:
  hosts:
    vector-01:
      ansible_host: 158.160.119.243
```
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
```
WARNING  Listing 9 violation(s) that are fatal
fqcn[action-core]: Use FQCN for builtin module actions (command).
site.yml:5 Use `ansible.builtin.command` or `ansible.legacy.command` instead.

no-changed-when: Commands should not change things if nothing needs doing.
site.yml:5 Task/Handler: Start ngingx

fqcn[action-core]: Use FQCN for builtin module actions (command).
site.yml:8 Use `ansible.builtin.command` or `ansible.legacy.command` instead.

name[casing]: All names should start with an uppercase letter.
site.yml:8 Task/Handler: reload-nginx

no-changed-when: Commands should not change things if nothing needs doing.
site.yml:8 Task/Handler: reload-nginx

fqcn[action-core]: Use FQCN for builtin module actions (command).
site.yml:35 Use `ansible.builtin.command` or `ansible.legacy.command` instead.

no-changed-when: Commands should not change things if nothing needs doing.
site.yml:35 Task/Handler: Reload-nginx

fqcn[action-core]: Use FQCN for builtin module actions (template).
site.yml:50 Use `ansible.builtin.template` or `ansible.legacy.template` instead.
```

после исправления 
```
no-changed-when: Commands should not change things if nothing needs doing.
site.yml:5 Task/Handler: Start ngingx

no-changed-when: Commands should not change things if nothing needs doing.
site.yml:8 Task/Handler: Reload-nginx

no-changed-when: Commands should not change things if nothing needs doing.
site.yml:35 Task/Handler: Reload-nginx

Read documentation for instructions on how to ignore specific rule violations.

                  Rule Violation Summary                  
 count tag             profile rule associated tags       
     3 no-changed-when shared  command-shell, idempotency 

Failed: 3 failure(s), 0 warning(s) on 1 files. Last profile that met the validation criteria was 'safety'. Rating: 3/5 star
root@linuxmint203:/home/linuxmint/devops-ansible/08-ansible-03-yandex/playbook$ 
```
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
```
PLAY RECAP ***************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
lighthouse-01              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
```
PLAY RECAP ***************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
lighthouse-01              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

(https://github.com/Kirill-Gryzhin/devops-ansible/blob/main/08-ansible-03-yandex/description.md)


10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-03-yandex` на фиксирующий коммит, в ответ предоставьте ссылку на него.

---
