## Clichouse&Vector  Ansible-Playbook
Этот playbook скачивает и устанавливает Clichouse и Vector на окружение указанное в файле inventory

## Версия ОС рабочих станций
Centos7

## Версия ПО
- Clickhouse 
версия 22.3.3.44 
указывается в group_vars\сlickhouse\vars.yml 

- Vector 
версия 0.21.1 
указывается в group_vars\vector\vars.yml 

## Работа Ansible-Playbook
- Clickhouse 
Download  distr  - скачивает дистрибутивы clickhouse 
Install clickhouse packages - устанавилвает пакеты для клиента и сервара 
Create database - создает БД с таблицей 

- Vector  
Install Vector - устанавилвает дистрибутив  Vector
Config template - настраивает vector используя файл конфигурации jinja2 из каталога templates
