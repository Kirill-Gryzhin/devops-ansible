## Основная часть

1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте значение, которое имеет факт `some_fact` для указанного хоста при выполнении playbook.
2. Найдите файл с переменными (group_vars), в котором задаётся найденное в первом пункте значение, и поменяйте его на `all default fact`.
3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.
5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились значения: для `deb` — `deb default fact`, для `el` — `el default fact`.
6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.
7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.
8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.
9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.
10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.
11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь, что факты `some_fact` для каждого из хостов определены из верных `group_vars`.
12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.

## Ответы
1.
```
ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ********************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the
meaning of that path. See https://docs.ansible.com/ansible-core/2.13/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]

TASK [Print OS] **************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Linux Mint"
}

TASK [Print fact] ************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP *******************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
2.
```
root@linuxmint203:/home/linuxmint/devops-ansible/playbook# ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ********************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the
meaning of that path. See https://docs.ansible.com/ansible-core/2.13/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]

TASK [Print OS] **************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Linux Mint"
}

TASK [Print fact] ************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}

PLAY RECAP *******************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
3,4.
```
root@linuxmint203:/home/linuxmint/devops-ansible/playbook# ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] ********************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP *******************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
5,6.
```
root@linuxmint203:/home/linuxmint/devops-ansible/playbook# ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] ********************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *******************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
7,8.
```
root@linuxmint203:/home/linuxmint/devops-ansible/playbook# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] ********************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **************************************************************************************************************************************************************************
ok: [ubuntu] => {
    "msg": "Ubuntu"
}
ok: [centos7] => {
    "msg": "CentOS"
}

TASK [Print fact] ************************************************************************************************************************************************************************
ok: [ubuntu] => {
    "msg": "deb default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}

PLAY RECAP *******************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
9.
```
root@linuxmint203:/home/linuxmint/devops-ansible/playbook# ansible-doc -t connection -l
ansible.netcommon.grpc         Provides a persistent connection using the gRPC protocol                                                                                                         
ansible.netcommon.httpapi      Use httpapi to run command on network appliances                                                                                                                 
ansible.netcommon.libssh       Run tasks using libssh for ssh connection                                                                                                                        
ansible.netcommon.napalm       Provides persistent connection using NAPALM                                                                                                                      
ansible.netcommon.netconf      Provides a persistent connection using the netconf protocol                                                                                                      
ansible.netcommon.network_cli  Use network_cli to run command on network appliances                                                                                                             
ansible.netcommon.persistent   Use a persistent unix socket for connection                                                                                                                      
community.aws.aws_ssm          execute via AWS Systems Manager                                                                                                                                  
community.docker.docker        Run tasks in docker containers                                                                                                                                   
community.docker.docker_api    Run tasks in docker containers                                                                                                                                   
community.docker.nsenter       execute on host running controller container                                                                                                                     
community.general.chroot       Interact with local chroot                                                                                                                                       
:...skipping...
ansible.netcommon.grpc         Provides a persistent connection using the gRPC protocol                                                                                                         
ansible.netcommon.httpapi      Use httpapi to run command on network appliances                                                                                                                 
ansible.netcommon.libssh       Run tasks using libssh for ssh connection                                                                                                                        
ansible.netcommon.napalm       Provides persistent connection using NAPALM                                                                                                                      
ansible.netcommon.netconf      Provides a persistent connection using the netconf protocol                                                                                                      
ansible.netcommon.network_cli  Use network_cli to run command on network appliances                                                                                                             
ansible.netcommon.persistent   Use a persistent unix socket for connection                                                                                                                      
community.aws.aws_ssm          execute via AWS Systems Manager                                                                                                                                  
community.docker.docker        Run tasks in docker containers                                                                                                                                   
community.docker.docker_api    Run tasks in docker containers                                                                                                                                   
community.docker.nsenter       execute on host running controller container                                                                                                                     
community.general.chroot       Interact with local chroot                                                                                                                                       
community.general.funcd        Use funcd to connect to target                                                                                                                                   
community.general.iocage       Run tasks in iocage jails                                                                                                                                        
community.general.jail         Run tasks in jails                                                                                                                                               
community.general.lxc          Run tasks in lxc containers via lxc python library                                                                                                               
community.general.lxd          Run tasks in lxc containers via lxc CLI                                                                                                                          
community.general.qubes        Interact with an existing QubesOS AppVM                                                                                                                          
community.general.saltstack    Allow ansible to piggyback on salt minions                                                                                                                       
community.general.zone         Run tasks in a zone instance                                                                                                                                     
community.libvirt.libvirt_lxc  Run tasks in lxc containers via libvirt                                                                                                                          
community.libvirt.libvirt_qemu Run tasks on libvirt/qemu virtual machines                                                                                                                       
community.okd.oc               Execute tasks in pods running on OpenShift                                                                                                                       
community.vmware.vmware_tools  Execute tasks inside a VM via VMware Tools                                                                                                                       
containers.podman.buildah      Interact with an existing buildah container                                                                                                                      
containers.podman.podman       Interact with an existing podman container                                                                                                                       
kubernetes.core.kubectl        Execute tasks in pods running on Kubernetes                                                                                                                      
local                          execute on controller                                                                                                                                            
paramiko_ssh                   Run tasks via python ssh (paramiko)                                                                                                                              
psrp                           Run tasks over Microsoft PowerShell Remoting Protocol                                                                                                            
ssh                            connect via SSH client binary                                                                                                                                    
winrm                          Run tasks over Microsoft's WinRM                                                                                                                                 
(END)
```
нам подходит community.docker.docker        Run tasks in docker containers                                         

10.
```
---
  el:
    hosts:
      centos7:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker
  local:
    hosts:
      localhost:
        ansible_connection: local
```
11.
```
root@linuxmint203:/home/linuxmint/devops-ansible/playbook# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] ****************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the
meaning of that path. See https://docs.ansible.com/ansible-core/2.13/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [localhost] => {
    "msg": "Linux Mint"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ********************************************************************************************************************************************************************
ok: [ubuntu] => {
    "msg": "deb default fact"
}
ok: [localhost] => {
    "msg": "all default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```