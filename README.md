# Подготовка к выполнению
- Установите molecule: pip3 install "molecule==3.5.2" и драйвера pip3 install molecule_docker molecule_podman.
- Выполните docker pull aragast/netology:latest — это образ с podman, tox и несколькими пайтонами (3.7 и 3.9) внутри.
```
molecule --version
molecule 4.0.4 using python 3.8
    ansible:2.13.12
    delegated:4.0.4 from molecule
    docker:2.1.0 from molecule_docker requiring collections: community.docker>=3.0.2 ansible.posix>=1.4.0
    podman:2.0.3 from molecule_podman requiring collections: containers.podman>=1.7.0 ansible.posix>=1.3.0
docker image ls
REPOSITORY         TAG       IMAGE ID       CREATED         SIZE
hello-world        latest    9c7a54a9a43c   4 months ago    13.3kB
aragast/netology   latest    b453a84e3f7a   11 months ago   2.46GB
```
## Работа над ошибками
  - установила sudo pip3 install molecule-docker
```
pip3 install "molecule==4.0.0"
Successfully installed molecule-4.0.0
root@65798104bbab:/# molecule --version
molecule 6.0.2 using python 3.10
    ansible:2.15.4
    default:6.0.2 from molecule
    docker:2.1.0 from molecule_docker requiring collections: community.docker>=3.0.2 ansible.posix>=1.4.0
molecule init scenario centos8 --driver-name docker
INFO     Initializing new scenario centos8...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector/molecule/centos8 successfully.
molecule init scenario ubuntu_latest --driver-name docker
INFO     Initializing new scenario ubuntu_latest...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector/molecule/ubuntu_latest successfully.
```
# Основная часть
- Ваша цель — настроить тестирование ваших ролей.
- Задача — сделать сценарии тестирования для vector.
- Ожидаемый результат — все сценарии успешно проходят тестирование ролей.
 
## Molecule
- Запустите molecule test -s centos_7 внутри корневой директории clickhouse-role, посмотрите на вывод команды. Данная команда может отработать с ошибками, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу.
- Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи molecule init scenario --driver-name docker.
```
ansible-galaxy role init myrole
- Role myrole was created successfully
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing$ ls
ansible-04  example  myrole  README.md  vector
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing$ cd myrole
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole$  molecule init role clickhouse --driver-name docker
INFO     Initializing new role clickhouse...
Using /etc/ansible/ansible.cfg as config file
- Role clickhouse was created successfully
localhost | CHANGED => {"backup": "","changed": true,"msg": "line added"}
INFO     Initialized role in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse successfully.
molecule init role ev.vector1 --driver-name docker
INFO     Initializing new role vector...
Using /etc/ansible/ansible.cfg as config file
- Role vector was created successfully
localhost | CHANGED => {"backup": "","changed": true,"msg": "line added"}
INFO     Initialized role in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector successfully.
```
- Добавьте несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
```
molecule init scenario centos7 --driver-name docker
INFO     Initializing new scenario centos_7...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse/molecule/centos_7 successfully

```
- Добавьте несколько assert в verify.yml-файл для проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.)
  
- Запустите тестирование роли повторно и проверьте, что оно прошло успешно.
```  
  vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse$ molecule test -s centos7
INFO     centos_7 scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Set ANSIBLE_LIBRARY=/home/vagrant/.cache/ansible-compat/7e099f/modules:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/7e099f/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/7e099f/roles:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO     Using /home/vagrant/.cache/ansible-compat/7e099f/roles/ev.clickhouse symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Running centos_7 > dependency
INFO     Running from /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse : ansible-galaxy collection install -vvv community.docker:>=3.0.2
INFO     Running from /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse : ansible-galaxy collection install -vvv ansible.posix:>=1.4.0
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running centos_7 > lint
INFO     Lint is disabled.
INFO     Running centos_7 > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running centos_7 > destroy
INFO     Sanity checks: 'docker'

PLAY [Destroy] *****************************************************************
TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]
TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) deletion to complete (300 retries left).
ok: [localhost] => (item=instance)
TASK [Delete docker networks(s)] ***********************************************
PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
INFO     Running centos_7 > syntax
playbook: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse/molecule/centos_7/converge.yml
INFO     Running centos_7 > create
PLAY [Create] ******************************************************************
TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]
TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost]
TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})
TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})
TASK [Synchronization the context] *********************************************
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})
TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/centos:7)
TASK [Create docker network(s)] ************************************************
TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})
TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': '46089250857.31817', 'results_file': '/home/vagrant/.ansible_async/46089250857.31817', 'changed': True, 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=2    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
INFO     Running centos_7 > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running centos_7 > converge
PLAY [Converge] ****************************************************************
TASK [Gathering Facts] *********************************************************
ok: [instance]
TASK [Include clickhouse] ******************************************************
PLAY RECAP *********************************************************************
instance                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
INFO     Running centos_7 > idempotence
PLAY [Converge] ****************************************************************
TASK [Gathering Facts] *********************************************************
ok: [instance]
TASK [Include clickhouse] ******************************************************
PLAY RECAP *********************************************************************
instance                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
INFO     Idempotence completed successfully.
INFO     Running centos_7 > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running centos_7 > verify
INFO     Running Ansible Verifier
PLAY [Verify] ******************************************************************
TASK [Example assertion] *******************************************************
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
PLAY RECAP *********************************************************************
instance                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
INFO     Verifier completed successfully.
INFO     Running centos_7 > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running centos_7 > destroy
PLAY [Destroy] *****************************************************************
TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]
TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=instance)
TASK [Delete docker networks(s)] ***********************************************
PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
INFO     Pruning extra files from scenario ephemeral directory
```
```
molecule test -s centos8 --destroy always
DEBUG    Validating schema /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/molecule.yml.
INFO     centos8 scenario test matrix: destroy, create, converge, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Set ANSIBLE_LIBRARY=/home/vagrant/.cache/ansible-compat/4d713b/modules:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO     Using /home/vagrant/.cache/ansible-compat/4d713b/roles/ev.vector1 symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Running centos8 > destroy
INFO     Sanity checks: 'docker'
DEBUG: ANSIBLE ENVIRONMENT:
ANSIBLE_COLLECTIONS_PATH: /home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections
ANSIBLE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg
ANSIBLE_FILTER_PLUGINS: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter
ANSIBLE_FORCE_COLOR: '1'
ANSIBLE_LIBRARY: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
ANSIBLE_ROLES_PATH: /home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles

DEBUG: MOLECULE ENVIRONMENT:
MOLECULE_DEBUG: 'True'
MOLECULE_DEPENDENCY_NAME: galaxy
MOLECULE_DRIVER_NAME: docker
MOLECULE_ENV_FILE: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml
MOLECULE_EPHEMERAL_DIRECTORY: /home/vagrant/.cache/molecule/vector1/centos8
MOLECULE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/molecule.yml
MOLECULE_INSTANCE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml
MOLECULE_INVENTORY_FILE: /home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml
MOLECULE_PROJECT_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1
MOLECULE_PROVISIONER_NAME: ansible
MOLECULE_SCENARIO_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8
MOLECULE_SCENARIO_NAME: centos8
MOLECULE_STATE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/state.yml
MOLECULE_VERIFIER_NAME: ansible
MOLECULE_VERIFIER_TEST_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests

DEBUG: SHELL REPLAY:
ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections ANSIBLE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg ANSIBLE_FILTER_PLUGINS=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter ANSIBLE_FORCE_COLOR=1 ANSIBLE_LIBRARY=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles MOLECULE_DEBUG=True MOLECULE_DEPENDENCY_NAME=galaxy MOLECULE_DRIVER_NAME=docker MOLECULE_ENV_FILE=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml MOLECULE_EPHEMERAL_DIRECTORY=/home/vagrant/.cache/molecule/vector1/centos8 MOLECULE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/molecule.yml MOLECULE_INSTANCE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml MOLECULE_INVENTORY_FILE=/home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml MOLECULE_PROJECT_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1 MOLECULE_PROVISIONER_NAME=ansible MOLECULE_SCENARIO_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8 MOLECULE_SCENARIO_NAME=centos8 MOLECULE_STATE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/state.yml MOLECULE_VERIFIER_NAME=ansible MOLECULE_VERIFIER_TEST_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests
PLAY [Destroy] *****************************************************************
TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]
TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=centos)
TASK [Wait for instance(s) deletion to complete] *******************************
ok: [localhost] => (item=centos)
TASK [Delete docker networks(s)] ***********************************************
PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
INFO     Running centos8 > create
DEBUG: ANSIBLE ENVIRONMENT:
ANSIBLE_COLLECTIONS_PATH: /home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections
ANSIBLE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg
ANSIBLE_FILTER_PLUGINS: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter
ANSIBLE_FORCE_COLOR: '1'
ANSIBLE_LIBRARY: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
ANSIBLE_ROLES_PATH: /home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
DEBUG: MOLECULE ENVIRONMENT:
MOLECULE_DEBUG: 'True'
MOLECULE_DEPENDENCY_NAME: galaxy
MOLECULE_DRIVER_NAME: docker
MOLECULE_ENV_FILE: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml
MOLECULE_EPHEMERAL_DIRECTORY: /home/vagrant/.cache/molecule/vector1/centos8
MOLECULE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/molecule.yml
MOLECULE_INSTANCE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml
MOLECULE_INVENTORY_FILE: /home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml
MOLECULE_PROJECT_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1
MOLECULE_PROVISIONER_NAME: ansible
MOLECULE_SCENARIO_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8
MOLECULE_SCENARIO_NAME: centos8
MOLECULE_STATE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/state.yml
MOLECULE_VERIFIER_NAME: ansible
MOLECULE_VERIFIER_TEST_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests
DEBUG: SHELL REPLAY:
ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections ANSIBLE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg ANSIBLE_FILTER_PLUGINS=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter ANSIBLE_FORCE_COLOR=1 ANSIBLE_LIBRARY=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles MOLECULE_DEBUG=True MOLECULE_DEPENDENCY_NAME=galaxy MOLECULE_DRIVER_NAME=docker MOLECULE_ENV_FILE=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml MOLECULE_EPHEMERAL_DIRECTORY=/home/vagrant/.cache/molecule/vector1/centos8 MOLECULE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/molecule.yml MOLECULE_INSTANCE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml MOLECULE_INVENTORY_FILE=/home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml MOLECULE_PROJECT_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1 MOLECULE_PROVISIONER_NAME=ansible MOLECULE_SCENARIO_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8 MOLECULE_SCENARIO_NAME=centos8 MOLECULE_STATE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/state.yml MOLECULE_VERIFIER_NAME=ansible MOLECULE_VERIFIER_TEST_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests
PLAY [Create] ******************************************************************
TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]
TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost]
TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'centos:8', 'name': 'centos', 'pre_build_image': True})
TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'centos:8', 'name': 'centos', 'pre_build_image': True})
TASK [Synchronization the context] *********************************************
skipping: [localhost] => (item={'image': 'centos:8', 'name': 'centos', 'pre_build_image': True})
TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'centos:8', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/centos:8)
TASK [Create docker network(s)] ************************************************
TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'centos:8', 'name': 'centos', 'pre_build_image': True})
TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=centos)
TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (300 retries left).
--- before
+++ after
@@ -1,4 +1,4 @@
 {
-    "exists": false,
-    "running": false
+    "exists": true,
+    "running": true
 }

changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': '230476154835.123871', 'results_file': '/home/vagrant/.ansible_async/230476154835.123871', 'changed': True, 'item': {'image': 'centos:8', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=2    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
INFO     Running centos8 > converge
DEBUG: ANSIBLE ENVIRONMENT:
ANSIBLE_COLLECTIONS_PATH: /home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections
ANSIBLE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg
ANSIBLE_FILTER_PLUGINS: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter
ANSIBLE_FORCE_COLOR: '1'
ANSIBLE_LIBRARY: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
ANSIBLE_ROLES_PATH: /home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles

DEBUG: MOLECULE ENVIRONMENT:
MOLECULE_DEBUG: 'True'
MOLECULE_DEPENDENCY_NAME: galaxy
MOLECULE_DRIVER_NAME: docker
MOLECULE_ENV_FILE: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml
MOLECULE_EPHEMERAL_DIRECTORY: /home/vagrant/.cache/molecule/vector1/centos8
MOLECULE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/molecule.yml
MOLECULE_INSTANCE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml
MOLECULE_INVENTORY_FILE: /home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml
MOLECULE_PROJECT_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1
MOLECULE_PROVISIONER_NAME: ansible
MOLECULE_SCENARIO_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8
MOLECULE_SCENARIO_NAME: centos8
MOLECULE_STATE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/state.yml
MOLECULE_VERIFIER_NAME: ansible
MOLECULE_VERIFIER_TEST_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests

DEBUG: SHELL REPLAY:
ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections ANSIBLE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg ANSIBLE_FILTER_PLUGINS=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter ANSIBLE_FORCE_COLOR=1 ANSIBLE_LIBRARY=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles MOLECULE_DEBUG=True MOLECULE_DEPENDENCY_NAME=galaxy MOLECULE_DRIVER_NAME=docker MOLECULE_ENV_FILE=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml MOLECULE_EPHEMERAL_DIRECTORY=/home/vagrant/.cache/molecule/vector1/centos8 MOLECULE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/molecule.yml MOLECULE_INSTANCE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml MOLECULE_INVENTORY_FILE=/home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml MOLECULE_PROJECT_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1 MOLECULE_PROVISIONER_NAME=ansible MOLECULE_SCENARIO_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8 MOLECULE_SCENARIO_NAME=centos8 MOLECULE_STATE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/state.yml MOLECULE_VERIFIER_NAME=ansible MOLECULE_VERIFIER_TEST_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests
PLAY [Converge] ****************************************************************
TASK [Gathering Facts] *********************************************************
ok: [centos]
TASK [Include vector] **********************************************************
PLAY RECAP *********************************************************************
centos                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
INFO     Running centos8 > destroy
DEBUG: ANSIBLE ENVIRONMENT:
ANSIBLE_COLLECTIONS_PATH: /home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections
ANSIBLE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg
ANSIBLE_FILTER_PLUGINS: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter
ANSIBLE_FORCE_COLOR: '1'
ANSIBLE_LIBRARY: /home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
ANSIBLE_ROLES_PATH: /home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles

DEBUG: MOLECULE ENVIRONMENT:
MOLECULE_DEBUG: 'True'
MOLECULE_DEPENDENCY_NAME: galaxy
MOLECULE_DRIVER_NAME: docker
MOLECULE_ENV_FILE: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml
MOLECULE_EPHEMERAL_DIRECTORY: /home/vagrant/.cache/molecule/vector1/centos8
MOLECULE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/molecule.yml
MOLECULE_INSTANCE_CONFIG: /home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml
MOLECULE_INVENTORY_FILE: /home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml
MOLECULE_PROJECT_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1
MOLECULE_PROVISIONER_NAME: ansible
MOLECULE_SCENARIO_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8
MOLECULE_SCENARIO_NAME: centos8
MOLECULE_STATE_FILE: /home/vagrant/.cache/molecule/vector1/centos8/state.yml
MOLECULE_VERIFIER_NAME: ansible
MOLECULE_VERIFIER_TEST_DIRECTORY: /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests

DEBUG: SHELL REPLAY:
ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/4d713b/collections:/home/vagrant/.cache/molecule/vector1/centos8/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections:/etc/ansible/collections ANSIBLE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/ansible.cfg ANSIBLE_FILTER_PLUGINS=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/filter:/home/vagrant/.cache/molecule/vector1/centos8/plugins/filter:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/plugins/filter:/home/vagrant/.ansible/plugins/filter:/usr/share/ansible/plugins/filter ANSIBLE_FORCE_COLOR=1 ANSIBLE_LIBRARY=/home/vagrant/.local/lib/python3.8/site-packages/molecule/provisioner/ansible/plugins/modules:/home/vagrant/.cache/molecule/vector1/centos8/library:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/library:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/4d713b/roles:/home/vagrant/.cache/molecule/vector1/centos8/roles:/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles MOLECULE_DEBUG=True MOLECULE_DEPENDENCY_NAME=galaxy MOLECULE_DRIVER_NAME=docker MOLECULE_ENV_FILE=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/.env.yml MOLECULE_EPHEMERAL_DIRECTORY=/home/vagrant/.cache/molecule/vector1/centos8 MOLECULE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/molecule.yml MOLECULE_INSTANCE_CONFIG=/home/vagrant/.cache/molecule/vector1/centos8/instance_config.yml MOLECULE_INVENTORY_FILE=/home/vagrant/.cache/molecule/vector1/centos8/inventory/ansible_inventory.yml MOLECULE_PROJECT_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1 MOLECULE_PROVISIONER_NAME=ansible MOLECULE_SCENARIO_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8 MOLECULE_SCENARIO_NAME=centos8 MOLECULE_STATE_FILE=/home/vagrant/.cache/molecule/vector1/centos8/state.yml MOLECULE_VERIFIER_NAME=ansible MOLECULE_VERIFIER_TEST_DIRECTORY=/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector1/molecule/centos8/tests
PLAY [Destroy] *****************************************************************
TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]
TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=centos)
TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) deletion to complete (300 retries left).
--- before
+++ after
@@ -1,4 +1,4 @@
 {
-    "exists": true,
-    "running": true
+    "exists": false,
+    "running": false
 }

changed: [localhost] => (item=centos)
TASK [Delete docker networks(s)] ***********************************************
PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
INFO     Pruning extra files from scenario ephemeral directory
```
- Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.
## Tox
- Добавьте в директорию с vector-role файлы из директории.
- Запустите docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash, где path_to_repo — путь до корня репозитория с vector-role на вашей файловой системе.
```
docker run --privileged=True -v  ~/mnt-homeworks/08-ansible-05-testing/myrole/vector1:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
sudo docker run --privileged=True -v  ~/mnt-homeworks/08-ansible-05-testing/myrole/vector1:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
[root@03d106e090c6 vector-role]#
```
- Внутри контейнера выполните команду tox, посмотрите на вывод.
```
[root@190529bae871 vector-role]# tox
py38-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==3.0.2,ansible-core==2.13.12,ansible-lint==5.1.3,arrow==1.3.0,attrs==23.1.0,binaryornot==0.4.4,bracex==2.4,certifi==2023.7.22,cffi==1.16.0,chardet==5.2.0,charset-normalizer==3.3.0,click==8.1.7,click-help-colors==0.9.2,cookiecutter==2.4.0,cryptography==41.0.4,distro==1.8.0,docker==5.0.0,enrich==1.2.7,idna==3.4,importlib-resources==6.1.0,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.19.1,jsonschema-specifications==2023.7.1,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==4.0.4,molecule-docker==0.2.4,molecule-podman==2.0.3,packaging==23.2,pathspec==0.11.2,pkgutil_resolve_name==1.3.10,pluggy==1.3.0,pycparser==2.21,Pygments==2.16.1,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==6.0.1,referencing==0.30.2,requests==2.31.0,resolvelib==0.8.1,rich==13.6.0,rpds-py==0.10.3,ruamel.yaml==0.17.33,ruamel.yaml.clib==0.2.7,selinux==0.2.1,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,types-python-dateutil==2.8.19.14,typing_extensions==4.8.0,urllib3==2.0.6,wcmatch==8.5,websocket-client==1.6.3,yamllint==1.26.3,zipp==3.17.0
py38-ansible210 run-test-pre: PYTHONHASHSEED='485492344'
py38-ansible210 run-test: commands[0] | molecule test -s centos8 --destroy always
Failed to load driver entry point Traceback (most recent call last):
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/api.py", line 53, in drivers
    pm.load_setuptools_entrypoints("molecule.driver")
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/pluggy/_manager.py", line 398, in load_setuptools_entrypoints
    plugin = ep.load()
  File "/usr/local/lib/python3.8/importlib/metadata.py", line 77, in load
    module = import_module(match.group('module'))
  File "/usr/local/lib/python3.8/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
  File "<frozen importlib._bootstrap>", line 991, in _find_and_load
  File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 783, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule_docker/driver.py", line 28, in <module>
    from molecule.util import lru_cache, sysexit_with_message
ImportError: cannot import name 'lru_cache' from 'molecule.util' (/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/util.py)
ERROR    Failed to load driver entry point Traceback (most recent call last):
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/api.py", line 53, in drivers
    pm.load_setuptools_entrypoints("molecule.driver")
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/pluggy/_manager.py", line 398, in load_setuptools_entrypoints
    plugin = ep.load()
  File "/usr/local/lib/python3.8/importlib/metadata.py", line 77, in load
    module = import_module(match.group('module'))
  File "/usr/local/lib/python3.8/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name, package, level)
  File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
  File "<frozen importlib._bootstrap>", line 991, in _find_and_load
  File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 783, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule_docker/driver.py", line 28, in <module>
    from molecule.util import lru_cache, sysexit_with_message
ImportError: cannot import name 'lru_cache' from 'molecule.util' (/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/util.py)
Traceback (most recent call last):
  File "/opt/vector-role/.tox/py38-ansible210/bin/molecule", line 8, in <module>
    sys.exit(main())
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/click/core.py", line 1157, in __call__
    return self.main(*args, **kwargs)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/click/core.py", line 1078, in main
    rv = self.invoke(ctx)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/click/core.py", line 1688, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/click/core.py", line 1434, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/click/core.py", line 783, in invoke
    return __callback(*args, **kwargs)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/click/decorators.py", line 33, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/command/test.py", line 176, in test
    base.execute_cmdline_scenarios(scenario_name, args, command_args, ansible_args)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/command/base.py", line 97, in execute_cmdline_scenarios
    get_configs(args, command_args, ansible_args, glob_str), scenario_name
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/command/base.py", line 189, in get_configs
    configs = [
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/command/base.py", line 190, in <listcomp>
    config.Config(
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/config.py", line 64, in __call__
    obj.after_init()
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/config.py", line 112, in after_init
    self.config = self._reget_config()
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/config.py", line 308, in _reget_config
    env = util.merge_dicts(os.environ, self.env)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/config.py", line 197, in env
    "MOLECULE_INSTANCE_CONFIG": self.driver.instance_config,
  File "/usr/local/lib/python3.8/functools.py", line 967, in __get__
    val = self.func(instance)
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/config.py", line 181, in driver
    driver = api.drivers(config=self)[driver_name]
  File "/opt/vector-role/.tox/py38-ansible210/lib/python3.8/site-packages/molecule/api.py", line 29, in __getitem__
    return self.__dict__[i]
KeyError: 'docker'
ERROR: InvocationError for command /opt/vector-role/.tox/py38-ansible210/bin/molecule test -s centos8 --destroy always (exited with code 1)
py38-ansible30 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==3.0.2,ansible-core==2.13.12,ansible-lint==5.1.3,arrow==1.3.0,attrs==23.1.0,binaryornot==0.4.4,bracex==2.4,certifi==2023.7.22,cffi==1.16.0,chardet==5.2.0,charset-normalizer==3.3.0,click==8.1.7,click-help-colors==0.9.2,cookiecutter==2.4.0,cryptography==41.0.4,distro==1.8.0,docker==5.0.0,enrich==1.2.7,idna==3.4,importlib-resources==6.1.0,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.19.1,jsonschema-specifications==2023.7.1,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==4.0.4,molecule-docker==0.2.4,molecule-podman==2.0.3,packaging==23.2,pathspec==0.11.2,pkgutil_resolve_name==1.3.10,pluggy==1.3.0,pycparser==2.21,Pygments==2.16.1,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==6.0.1,referencing==0.30.2,requests==2.31.0,resolvelib==0.8.1,rich==13.6.0,rpds-py==0.10.3,ruamel.yaml==0.17.33,ruamel.yaml.clib==0.2.7,selinux==0.2.1,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,types-python-dateutil==2.8.19.14,typing_extensions==4.8.0,urllib3==2.0.6,wcmatch==8.5,websocket-client==1.6.3,yamllint==1.26.3,zipp==3.17.0
py38-ansible30 run-test-pre: PYTHONHASHSEED='485492344'
py38-ansible30 run-test: commands[0] | molecule test -s centos8 --destroy always
Failed to load driver entry point Traceback (most recent call last):
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/api.py", line 53, in drivers
    pm.load_setuptools_entrypoints("molecule.driver")
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/pluggy/_manager.py", line 398, in load_setuptools_entrypoints
    plugin = ep.load()
  File "/usr/local/lib/python3.8/importlib/metadata.py", line 77, in load
    module = import_module(match.group('module'))
  File "/usr/local/lib/python3.8/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
  File "<frozen importlib._bootstrap>", line 991, in _find_and_load
  File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 783, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule_docker/driver.py", line 28, in <module>
    from molecule.util import lru_cache, sysexit_with_message
ImportError: cannot import name 'lru_cache' from 'molecule.util' (/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/util.py)

ERROR    Failed to load driver entry point Traceback (most recent call last):
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/api.py", line 53, in drivers
    pm.load_setuptools_entrypoints("molecule.driver")
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/pluggy/_manager.py", line 398, in load_setuptools_entrypoints
    plugin = ep.load()
  File "/usr/local/lib/python3.8/importlib/metadata.py", line 77, in load
    module = import_module(match.group('module'))
  File "/usr/local/lib/python3.8/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name, package, level)
  File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
  File "<frozen importlib._bootstrap>", line 991, in _find_and_load
  File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 783, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule_docker/driver.py", line 28, in <module>
    from molecule.util import lru_cache, sysexit_with_message
ImportError: cannot import name 'lru_cache' from 'molecule.util' (/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/util.py)

Traceback (most recent call last):
  File "/opt/vector-role/.tox/py38-ansible30/bin/molecule", line 8, in <module>
    sys.exit(main())
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1157, in __call__
    return self.main(*args, **kwargs)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1078, in main
    rv = self.invoke(ctx)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1688, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1434, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 783, in invoke
    return __callback(*args, **kwargs)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/decorators.py", line 33, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/test.py", line 176, in test
    base.execute_cmdline_scenarios(scenario_name, args, command_args, ansible_args)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/base.py", line 97, in execute_cmdline_scenarios
    get_configs(args, command_args, ansible_args, glob_str), scenario_name
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/base.py", line 189, in get_configs
    configs = [
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/base.py", line 190, in <listcomp>
    config.Config(
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 64, in __call__
    obj.after_init()
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 112, in after_init
    self.config = self._reget_config()
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 308, in _reget_config
    env = util.merge_dicts(os.environ, self.env)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 197, in env
    "MOLECULE_INSTANCE_CONFIG": self.driver.instance_config,
  File "/usr/local/lib/python3.8/functools.py", line 967, in __get__
    val = self.func(instance)
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 181, in driver
    driver = api.drivers(config=self)[driver_name]
  File "/opt/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/api.py", line 29, in __getitem__
    return self.__dict__[i]
KeyError: 'docker'
ERROR: InvocationError for command /opt/vector-role/.tox/py38-ansible30/bin/molecule test -s centos8 --destroy always (exited with code 1)
_______________________________________________________________________________ summary ________________________________________________________________________________
ERROR:   py38-ansible210: commands failed
ERROR:   py38-ansible30: commands failed
```
- Создайте облегчённый сценарий для molecule с драйвером molecule_podman. Проверьте его на исполнимость.
```
  molecule init scenario molecule_podman --driver-name=docker
INFO     Initializing new scenario molecule_podman...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector/molecule/molecule_podman successfully.
molecule matrix -s molecule_podman test
INFO     Test matrix
---
molecule_podman:
  - destroy
  - create
  - converge
  - destroy
```
- Пропишите правильную команду в tox.ini, чтобы запускался облегчённый сценарий.
- Запустите команду tox. Убедитесь, что всё отработало успешно.
```
sudo docker run --privileged=True -v  ~/mnt-homeworks/08-ansible-05-testing/myrole/vector:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
[root@37f9096efc0b vector-role]# tox
py39-ansible210 create: /opt/vector-role/.tox/py39-ansible210
py39-ansible210 installdeps: -rrequirements.txt, ansible<3.0
py39-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==4.1.10,ansible-core==2.15.4,ansible-lint==5.1.3,arrow==1.2.3,attrs==23.1.0,bcrypt==4.0.1,binaryornot==0.4.4,bracex==2.4,Cerberus==1.3.5,certifi==2023.7.22,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.2.0,click==8.1.7,click-help-colors==0.9.2,cookiecutter==2.3.1,cryptography==41.0.4,distro==1.8.0,enrich==1.2.7,idna==3.4,importlib-resources==5.0.7,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.19.1,jsonschema-specifications==2023.7.1,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==2.0.0,packaging==23.1,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.3.0,pycparser==2.21,Pygments==2.16.1,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,referencing==0.30.2,requests==2.31.0,resolvelib==1.0.1,rich==13.5.3,rpds-py==0.10.3,ruamel.yaml==0.17.32,ruamel.yaml.clib==0.2.7,selinux==0.3.0,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.8.0,urllib3==2.0.5,wcmatch==8.5,yamllint==1.26.3
py39-ansible210 run-test-pre: PYTHONHASHSEED='2493523296'
py39-ansible210 run-test: commands[0] | molecule test -s molecule_podman --destroy always
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - image: docker.io/pycontribs/centos:7
    name: centos7
    pre_build_image: true
provisioner:
  name: ansible
scenario:
  name: molecule_podman
  test_sequence:
    - destroy
    - create
    - converge
    - destroy
verifier:
  name: ansible

CRITICAL Failed to pre-validate.
{'driver': [{'name': ['unallowed value docker']}]}
ERROR: InvocationError for command /opt/vector-role/.tox/py39-ansible210/bin/molecule test -s molecule_podman --destroy always (exited with code 1)
py39-ansible30 create: /opt/vector-role/.tox/py39-ansible30
py39-ansible30 installdeps: -rrequirements.txt, ansible<3.1
py39-ansible30 installed: ansible==3.0.0,ansible-base==2.10.17,ansible-compat==4.1.10,ansible-core==2.15.4,ansible-lint==5.1.3,arrow==1.2.3,attrs==23.1.0,bcrypt==4.0.1,binaryornot==0.4.4,bracex==2.4,Cerberus==1.3.5,certifi==2023.7.22,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.2.0,click==8.1.7,click-help-colors==0.9.2,cookiecutter==2.3.1,cryptography==41.0.4,distro==1.8.0,enrich==1.2.7,idna==3.4,importlib-resources==5.0.7,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.19.1,jsonschema-specifications==2023.7.1,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==2.0.0,packaging==23.1,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.3.0,pycparser==2.21,Pygments==2.16.1,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,referencing==0.30.2,requests==2.31.0,resolvelib==1.0.1,rich==13.5.3,rpds-py==0.10.3,ruamel.yaml==0.17.32,ruamel.yaml.clib==0.2.7,selinux==0.3.0,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.8.0,urllib3==2.0.5,wcmatch==8.5,yamllint==1.26.3
py39-ansible30 run-test-pre: PYTHONHASHSEED='2493523296'
py39-ansible30 run-test: commands[0] | molecule test -s molecule_podman --destroy always
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - image: docker.io/pycontribs/centos:7
    name: centos7
    pre_build_image: true
provisioner:
  name: ansible
scenario:
  name: molecule_podman
  test_sequence:
    - destroy
    - create
    - converge
    - destroy
verifier:
  name: ansible

CRITICAL Failed to pre-validate.
{'driver': [{'name': ['unallowed value docker']}]}
ERROR: InvocationError for command /opt/vector-role/.tox/py39-ansible30/bin/molecule test -s molecule_podman --destroy always (exited with code 1)
_______________________________________________________________________________ summary ________________________________________________________________________________
ERROR:   py39-ansible210: commands failed
ERROR:   py39-ansible30: commands failed
sudo docker run --privileged=True -v  ~/mnt-homeworks/08-ansible-05-testing/myrole/vector1:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
[root@aad510d2f218 vector-role]# tox
py38-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==3.0.2,ansible-core==2.13.12,ansible-lint==5.1.3,arrow==1.2.3,attrs==23.1.0,bcrypt==4.0.1,binaryornot==0.4.4,bracex==2.4,Cerberus==1.3.5,certifi==2023.7.22,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.2.0,click==8.1.7,click-help-colors==0.9.2,cookiecutter==2.3.1,cryptography==41.0.4,distro==1.8.0,enrich==1.2.7,idna==3.4,importlib-resources==6.1.0,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.19.1,jsonschema-specifications==2023.7.1,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==2.0.0,packaging==23.1,paramiko==2.12.0,pathspec==0.11.2,pkgutil_resolve_name==1.3.10,pluggy==1.3.0,pycparser==2.21,Pygments==2.16.1,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,referencing==0.30.2,requests==2.31.0,resolvelib==0.8.1,rich==13.5.3,rpds-py==0.10.3,ruamel.yaml==0.17.32,ruamel.yaml.clib==0.2.7,selinux==0.2.1,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.8.0,urllib3==2.0.5,wcmatch==8.5,yamllint==1.26.3,zipp==3.17.0
py38-ansible210 run-test-pre: PYTHONHASHSEED='2881249750'
py38-ansible210 run-test: commands[0] | molecule test -s centos8 --destroy always
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - image: centos:8
    name: centos8
    pre_build_image: true
provisioner:
  ansible_ver: '2.10'
  name: ansible
scenario:
  name: centos8
  test_sequence:
    - destroy
    - create
    - converge
    - destroy
verifier:
  name: ansible

CRITICAL Failed to pre-validate.

{'driver': [{'name': ['unallowed value docker']}]}
ERROR: InvocationError for command /opt/vector-role/.tox/py38-ansible210/bin/molecule test -s centos8 --destroy always (exited with code 1)
py38-ansible30 installed: ansible==3.0.0,ansible-base==2.10.17,ansible-compat==3.0.2,ansible-core==2.13.12,ansible-lint==5.1.3,arrow==1.2.3,attrs==23.1.0,bcrypt==4.0.1,binaryornot==0.4.4,bracex==2.4,Cerberus==1.3.5,certifi==2023.7.22,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.2.0,click==8.1.7,click-help-colors==0.9.2,cookiecutter==2.3.1,cryptography==41.0.4,distro==1.8.0,enrich==1.2.7,idna==3.4,importlib-resources==6.1.0,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.19.1,jsonschema-specifications==2023.7.1,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==2.0.0,packaging==23.1,paramiko==2.12.0,pathspec==0.11.2,pkgutil_resolve_name==1.3.10,pluggy==1.3.0,pycparser==2.21,Pygments==2.16.1,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,referencing==0.30.2,requests==2.31.0,resolvelib==0.8.1,rich==13.5.3,rpds-py==0.10.3,ruamel.yaml==0.17.33,ruamel.yaml.clib==0.2.7,selinux==0.2.1,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.8.0,urllib3==2.0.5,wcmatch==8.5,yamllint==1.26.3,zipp==3.17.0
py38-ansible30 run-test-pre: PYTHONHASHSEED='2881249750'
py38-ansible30 run-test: commands[0] | molecule test -s centos8 --destroy always
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - image: centos:8
    name: centos8
    pre_build_image: true
provisioner:
  ansible_ver: '2.10'
  name: ansible
scenario:
  name: centos8
  test_sequence:
    - destroy
    - create
    - converge
    - destroy
verifier:
  name: ansible

CRITICAL Failed to pre-validate.

{'driver': [{'name': ['unallowed value docker']}]}
ERROR: InvocationError for command /opt/vector-role/.tox/py38-ansible30/bin/molecule test -s centos8 --destroy always (exited with code 1)
_______________________________________________________________________________ summary ________________________________________________________________________________
ERROR:   py38-ansible210: commands failed
ERROR:   py38-ansible30: commands failed
[root@aad510d2f218 vector-role]# exit
```
- Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.
- После выполнения у вас должно получится два сценария molecule и один tox.ini файл в репозитории. Не забудьте указать в ответе теги решений Tox и Molecule заданий. В качестве решения пришлите ссылку на ваш репозиторий и скриншоты этапов выполнения задания.
 
