```
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role$ python --version
Python 3.9.5
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role$ ansible --version
ansible 2.10.17
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/myenv/lib/python3.9/site-packages/ansible
  executable location = /home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/myenv/bin/ansible
  python version = 3.9.5 (default, Nov 23 2021, 15:27:38) [GCC 9.3.0]
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role$ docker --version
Docker version 24.0.6, build ed223bc
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role$ tox --version
4.11.3 from /home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/myenv/lib/python3.9/site-packages/tox/__init__.py
```
```
[tox]
minversion = 1.8
#basepython = python3.6
envlist = py39-ansible210, py39-ansible30
skipsdist = true

[testenv]
passenv = *
deps =
    -r requirements.txt
    ansible210: ansible==2.10
    ansible30: ansible==3.0
commands =
    {posargs:molecule test -s centos7 --destroy always}
```
```
requirements.txt
selinux==0.2.1
ansible-lint<=6.20.3
docker>=5.0.0
yamllint==1.26.3
molecule>=3.5.2,<5.0
molecule_podman<=2.0.3
jmespath
lxml
ansible<=3.0
```
```
# molecule.yml
dependency:
  name: galaxy
  options:
    requirements_file: requirements.txt

driver:
  name: docker

#lint:
#  ansible-lint .
#  yamllint .

platforms:
  - name: centos7
    image: centos:7
    pre_build_image: true
    command: /usr/bin/python3.9
provisioner:
  name: ansible
  config_options:
        defaults:
          ansible_version: 3.0
scenario:
  name: centos7
  test_sequence:
    - destroy
    - create
    - converge
    - destroy

verifier:
  name: ansible
  options:
    roles:
      - name: vector-role
    variables:
      vector_version: "0.32.1-1"
```
```
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role$ systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2023-10-22 10:54:31 UTC; 3h 53min ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 13580 (dockerd)
      Tasks: 10
     Memory: 43.7M
     CGroup: /system.slice/docker.service
             └─13580 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Oct 22 10:54:31 vagrant dockerd[13580]: time="2023-10-22T10:54:31.225322360Z" level=info msg="Loading containers: done."
Oct 22 10:54:31 vagrant dockerd[13580]: time="2023-10-22T10:54:31.378946512Z" level=warning msg="WARNING: No swap limit support"
Oct 22 10:54:31 vagrant dockerd[13580]: time="2023-10-22T10:54:31.379336977Z" level=info msg="Docker daemon" commit=1a79695 graphdriver=overlay2 version=24.0.6
Oct 22 10:54:31 vagrant dockerd[13580]: time="2023-10-22T10:54:31.383098939Z" level=info msg="Daemon has completed initialization"
Oct 22 10:54:31 vagrant dockerd[13580]: time="2023-10-22T10:54:31.498141587Z" level=info msg="API listen on /run/docker.sock"
Oct 22 10:54:31 vagrant systemd[1]: Started Docker Application Container Engine.
Oct 22 14:17:15 vagrant dockerd[13580]: time="2023-10-22T14:17:15.270431509Z" level=info msg="ignoring event" container=278c00fd02b1def19b9675bd87fd07b48519fa698df25d6>
Oct 22 14:17:15 vagrant dockerd[13580]: time="2023-10-22T14:17:15.374869047Z" level=warning msg="failed to close stdin: task 278c00fd02b1def19b9675bd87fd07b48519fa698d>
Oct 22 14:33:39 vagrant dockerd[13580]: time="2023-10-22T14:33:39.111172441Z" level=info msg="ignoring event" container=01e15980f6cb294a1918fe3324d44b29b307f7c8df40755>
Oct 22 14:33:39 vagrant dockerd[13580]: time="2023-10-22T14:33:39.372290580Z" level=warning msg="failed to close stdin: task 01e15980f6cb294a1918fe3324d44b29b307f7c8df>
```
```
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role$ source myenv/bin/activate
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role$ tox
py39-ansible210: install_deps> python -I -m pip install ansible==2.10 -r requirements.txt
py39-ansible210: commands[0]> molecule test -s centos7 --destroy always
WARNING  The scenario config file ('/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/molecule/centos7/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
Traceback (most recent call last):
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/bin/molecule", line 8, in <module>
    sys.exit(main())
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/click/core.py", line 1157, in __call__
    return self.main(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/click/core.py", line 1078, in main
    rv = self.invoke(ctx)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/click/core.py", line 1688, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/click/core.py", line 1434, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/click/core.py", line 783, in invoke
    return __callback(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/click/decorators.py", line 33, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/command/test.py", line 176, in test
    base.execute_cmdline_scenarios(scenario_name, args, command_args, ansible_args)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/command/base.py", line 97, in execute_cmdline_scenarios
    get_configs(args, command_args, ansible_args, glob_str), scenario_name
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/command/base.py", line 189, in get_configs
    configs = [
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/command/base.py", line 190, in <listcomp>
    config.Config(
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/config.py", line 64, in __call__
    obj.after_init()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/config.py", line 112, in after_init
    self.config = self._reget_config()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/config.py", line 308, in _reget_config
    env = util.merge_dicts(os.environ, self.env)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/config.py", line 197, in env
    "MOLECULE_INSTANCE_CONFIG": self.driver.instance_config,
  File "/usr/lib/python3.9/functools.py", line 969, in __get__
    val = self.func(instance)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/config.py", line 181, in driver
    driver = api.drivers(config=self)[driver_name]
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/molecule/api.py", line 29, in __getitem__
    return self.__dict__[i]
KeyError: 'docker'
py39-ansible210: exit 1 (3.91 seconds) /home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role> molecule test -s centos7 --destroy always pid=16631
py39-ansible210: FAIL ✖ in 1 minute 24.35 seconds
py39-ansible30: commands[0]> molecule test -s centos7 --destroy always
WARNING  The scenario config file ('/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/molecule/centos7/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
Traceback (most recent call last):
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/bin/molecule", line 8, in <module>
    sys.exit(main())
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/click/core.py", line 1157, in __call__
    return self.main(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/click/core.py", line 1078, in main
    rv = self.invoke(ctx)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/click/core.py", line 1688, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/click/core.py", line 1434, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/click/core.py", line 783, in invoke
    return __callback(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/click/decorators.py", line 33, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/command/test.py", line 176, in test
    base.execute_cmdline_scenarios(scenario_name, args, command_args, ansible_args)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/command/base.py", line 97, in execute_cmdline_scenarios
    get_configs(args, command_args, ansible_args, glob_str), scenario_name
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/command/base.py", line 189, in get_configs
    configs = [
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/command/base.py", line 190, in <listcomp>
    config.Config(
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/config.py", line 64, in __call__
    obj.after_init()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/config.py", line 112, in after_init
    self.config = self._reget_config()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/config.py", line 308, in _reget_config
    env = util.merge_dicts(os.environ, self.env)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/config.py", line 197, in env
    "MOLECULE_INSTANCE_CONFIG": self.driver.instance_config,
  File "/usr/lib/python3.9/functools.py", line 969, in __get__
    val = self.func(instance)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/config.py", line 181, in driver
    driver = api.drivers(config=self)[driver_name]
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/molecule/api.py", line 29, in __getitem__
    return self.__dict__[i]
KeyError: 'docker'
py39-ansible30: exit 1 (3.90 seconds) /home/vagrant/mnt-homeworks/08-ansible-05-testing/ansible-04/roles/vector-role> molecule test -s centos7 --destroy always pid=16658
  py39-ansible210: FAIL code 1 (84.35=setup[80.44]+cmd[3.91] seconds)
  py39-ansible30: FAIL code 1 (3.95=setup[0.06]+cmd[3.90] seconds)
  evaluation failed :( (88.75 seconds)
```
