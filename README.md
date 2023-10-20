# Домашнее задание к занятию "`Введение в Ansible`" - `Никулин Михаил Сергеевич`



---

### Задание 1

1. Попробуйте запустить playbook на окружении из test.yml, зафиксируйте значение, которое имеет факт some_fact для указанного хоста при выполнении playbook.
```
nikulinn@nikulin:~/Documents/mnt-homeworks/08-ansible-01-base/playbook$ ansible-playbook -i ./inventory/test.yml site.yml 

PLAY [Print os facts] **************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Print OS] ********************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Debian"
}

TASK [Print fact] ******************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP *************************************************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```
![task1_1.png](img%2Ftask1_1.png)

some_fact=12

2. Найдите файл с переменными (group_vars), в котором задаётся найденное в первом пункте значение, и поменяйте его на all default fact.

Путь до файла ```08-ansible-01-base/playbook/group_vars/all/examp.yml```. Меняем его содержимое и запускаем повторно:
![task2_1.png](img%2Ftask2_1.png)

3. Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.
```
root@nikulin:~# docker ps
CONTAINER ID   IMAGE                 COMMAND            CREATED          STATUS          PORTS     NAMES
2e682737fd7c   pycontribs/ubuntu     "sleep 65000000"   12 seconds ago   Up 6 seconds              ubuntu
b2efe4671b21   pycontribs/centos:7   "sleep 36000000"   54 seconds ago   Up 49 seconds             centos7

```

4. Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.

Добавим пользователя в группу ```docker``` и запустим playbook:
![task4_1.png](img%2Ftask4_1.png)

Значение ```some_fact``` для контейнера centos7: ```el```

Значение ```some_fact``` для контейнера ubuntu: ```deb```

5. Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились значения: для deb — deb default fact, для el — el default fact.

Изменим данные файлов:
![task5_1.png](img%2Ftask5_1.png)
![task5_2.png](img%2Ftask5_2.png)

6. Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.
![task6_1.png](img%2Ftask6_1.png)

7. При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.
```
nikulinn@nikulin:~/Documents/mnt-homeworks/08-ansible-01-base/playbook$ ansible-vault encrypt group_vars/deb/examp.yml 
New Vault password: 
Confirm New Vault password: 
Encryption successful
nikulinn@nikulin:~/Documents/mnt-homeworks/08-ansible-01-base/playbook$ ansible-vault encrypt group_vars/el/examp.yml 
New Vault password: 
Confirm New Vault password: 
Encryption successful
```

8. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.
![task8_1.png](img%2Ftask8_1.png)

9. Посмотрите при помощи ansible-doc список плагинов для подключения. Выберите подходящий для работы на control node.
![task9_1.png](img%2Ftask9_1.png)
Можно использовать ```ansible.builtin.local```

10. В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.
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
      test-netology:
        ansible_connection: local
```

11. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь, что факты some_fact для каждого из хостов определены из верных group_vars.
![task11_1.png](img%2Ftask11_1.png)

13. Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым playbook и заполненным README.md.


---

### Задание 2

1. При помощи ansible-vault расшифруйте все зашифрованные файлы с переменными.
```
nikulinn@nikulin:~/Documents/mnt-homeworks/08-ansible-01-base/playbook$ ansible-vault decrypt group_vars/el/examp.yml 
Vault password: 
Decryption successful
nikulinn@nikulin:~/Documents/mnt-homeworks/08-ansible-01-base/playbook$ ansible-vault decrypt group_vars/deb/examp.yml 
Vault password: 
Decryption successful
```

2. Зашифруйте отдельное значение PaSSw0rd для переменной some_fact паролем netology. Добавьте полученное значение в group_vars/all/exmp.yml.
```
nikulinn@nikulin:~/Documents/mnt-homeworks/08-ansible-01-base/playbook$ ansible-vault encrypt_string PaSSw0rd
New Vault password: 
Confirm New Vault password: 
Encryption successful
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          30303637653630643938616533623931353439333630333363373862623035633566346261646666
          3863356462366265316531363435666334353233616634350a313362643636353231373234386238
          63313338383861356330633333623835653032396335333064306565633432623463386531633561
          6661343135386439320a623331316664373964396366356435663531386639633537323763626131
          6564
```
Добавим полученное значение в ```group_vars/all/exmp.yml```
```
---
  some_fact: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          30303637653630643938616533623931353439333630333363373862623035633566346261646666
          3863356462366265316531363435666334353233616634350a313362643636353231373234386238
          63313338383861356330633333623835653032396335333064306565633432623463386531633561
          6661343135386439320a623331316664373964396366356435663531386639633537323763626131
          6564
```

3. Запустите playbook, убедитесь, что для нужных хостов применился новый fact.
![task_ex2_1.png](img%2Ftask_ex2_1.png)

4. Добавьте новую группу хостов fedora, самостоятельно придумайте для неё переменную. В качестве образа можно использовать этот вариант.
```
root@nikulin:~# docker ps
CONTAINER ID   IMAGE                 COMMAND            CREATED          STATUS          PORTS     NAMES
0b2c24482a6a   pycontribs/fedora     "sleep 65000000"   8 seconds ago    Up 6 seconds              fedora
2e682737fd7c   pycontribs/ubuntu     "sleep 65000000"   55 minutes ago   Up 55 minutes             ubuntu
b2efe4671b21   pycontribs/centos:7   "sleep 36000000"   55 minutes ago   Up 55 minutes             centos7
```
Отредактируем файл ```prod.yml```
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
      test-netology:
        ansible_connection: local

  fed:
    hosts:
      fedora:
        ansible_connection: docker 
```
Добавляем файл с переменными для группы ```fedora``` ```/group_vars/fed/examp.yml```
```
---
  some_fact: "Fedora default fact"
```
![task_ex4_2.png](img%2Ftask_ex4_2.png)

5. Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.

6. Все изменения должны быть зафиксированы и отправлены в ваш личный репозиторий.


---

