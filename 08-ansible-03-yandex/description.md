## Clichouse , Vector and Ligthouse  Ansible-Playbook
Данный playbook скачивает и устанавливает Clichouse и Vector на указанное в файле inventory окружения для 

## Версия ОС рабочих станций
Centos7
## Версия ПО и Теги
| Имя | Тег | Версия | 
| :-----:| :-----:|:-----:|
|Clickhouse |сlickhouse |22.3.3.44 |
|Vector|vector|0.21.1|
|LightHouse |ligthouse| laster |
|Nginx|nginx | laster|

Возможно указать нужную версию в дерриктории group_vars



## Работа Ansible-Playbook (Task)
### Clickhouse 
Download  distr  - скачивает дистрибутив clickhouse 
Install clickhouse packages - устанавилвает пакеты для клиента и сервара 
Create database - создает базу данных с таблицей 

### Vector  
Install Vector - устанавилвает дистрибутив  Vector
Config template - настраивает vector используя файл конфигурации jinja2 из папки templates


### LightHouse
Install Lighthouse - устанавливает дистрибутив LightHouse из репозитория git указанного в переменных в group_vars
Lighthouse | Create ligthouse config - настроивает конфигурацию Lighthouse  по шаблону template файла lighthouse.conf.j2 

### Nginx
Install nginx - устанавилвает последнюю версию nginx 
NGINX | Create general config - настраивает конфигурацию Nginx по шаблону template файла nginx.conf.j2
