```
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole$ ansible-galaxy role init vector-role
- Role vector-role was created successfully
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole$ ls
ansible-venv  clickhouse  clickhouse2  files  templates  vector  vector1  vector-role
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole$ cd vector-role
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ ls
defaults  files  handlers  meta  README.md  tasks  templates  tests  vars
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ molecule init scenario centos7
CRITICAL The default scenario not found.  Please create a scenario named 'default' first.
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ molecule init scenario default
INFO     Initializing new scenario default...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/molecule/default successfully.
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ molecule init scenario centos7
INFO     Initializing new scenario centos7...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/molecule/centos7 successfully.
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ molecule init scenario podman
INFO     Initializing new scenario podman...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/molecule/podman successfully.
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ source myenv/bin/activate
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ ansible --version
ansible [core 2.12.10]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.8.10 (default, May 26 2023, 14:05:08) [GCC 9.4.0]
  jinja version = 3.1.2
  libyaml = True
(myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ molecule --version
molecule 4.0.4 using python 3.8
    ansible:2.12.10
    delegated:4.0.4 from molecule
    docker:2.1.0 from molecule_docker requiring collections: community.docker>=3.0.2 ansible.posix>=1.4.0
    podman:2.0.3 from molecule_podman requiring collections: containers.podman>=1.7.0 ansible.posix>=1.3.0
--- (myenv) vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector-role$ tox
py38-ansible212: commands[0]> molecule test -s centos7 --destroy always
WARNING  The scenario config file ('/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/molecule/centos7/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
Traceback (most recent call last):
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/bin/molecule", line 8, in <module>
    sys.exit(main())
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/click/core.py", line 1157, in __call__
    return self.main(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/click/core.py", line 1078, in main
    rv = self.invoke(ctx)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/click/core.py", line 1688, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/click/core.py", line 1434, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/click/core.py", line 783, in invoke
    return __callback(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/click/decorators.py", line 33, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/command/test.py", line 176, in test
    base.execute_cmdline_scenarios(scenario_name, args, command_args, ansible_args)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/command/base.py", line 97, in execute_cmdline_scenarios
    get_configs(args, command_args, ansible_args, glob_str), scenario_name
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/command/base.py", line 189, in get_configs
    configs = [
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/command/base.py", line 190, in <listcomp>
    config.Config(
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/config.py", line 64, in __call__
    obj.after_init()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/config.py", line 112, in after_init
    self.config = self._reget_config()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/config.py", line 308, in _reget_config
    env = util.merge_dicts(os.environ, self.env)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/config.py", line 197, in env
    "MOLECULE_INSTANCE_CONFIG": self.driver.instance_config,
  File "/usr/lib/python3.8/functools.py", line 967, in __get__
    val = self.func(instance)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/config.py", line 181, in driver
    driver = api.drivers(config=self)[driver_name]
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible212/lib/python3.8/site-packages/molecule/api.py", line 29, in __getitem__
    return self.__dict__[i]
KeyError: 'docker'
py38-ansible212: exit 1 (3.49 seconds) /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role> molecule test -s centos7 --destroy always pid=179272
py38-ansible212: FAIL ✖ in 3.65 seconds
py38-ansible30: commands[0]> molecule test -s centos7 --destroy always
WARNING  The scenario config file ('/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/molecule/centos7/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
Traceback (most recent call last):
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/bin/molecule", line 8, in <module>
    sys.exit(main())
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1157, in __call__
    return self.main(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1078, in main
    rv = self.invoke(ctx)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1688, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 1434, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/core.py", line 783, in invoke
    return __callback(*args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/click/decorators.py", line 33, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/test.py", line 176, in test
    base.execute_cmdline_scenarios(scenario_name, args, command_args, ansible_args)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/base.py", line 97, in execute_cmdline_scenarios
    get_configs(args, command_args, ansible_args, glob_str), scenario_name
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/base.py", line 189, in get_configs
    configs = [
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/command/base.py", line 190, in <listcomp>
    config.Config(
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 64, in __call__
    obj.after_init()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 112, in after_init
    self.config = self._reget_config()
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 308, in _reget_config
    env = util.merge_dicts(os.environ, self.env)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 197, in env
    "MOLECULE_INSTANCE_CONFIG": self.driver.instance_config,
  File "/usr/lib/python3.8/functools.py", line 967, in __get__
    val = self.func(instance)
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/config.py", line 181, in driver
    driver = api.drivers(config=self)[driver_name]
  File "/home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role/.tox/py38-ansible30/lib/python3.8/site-packages/molecule/api.py", line 29, in __getitem__
    return self.__dict__[i]
KeyError: 'docker'
py38-ansible30: exit 1 (4.32 seconds) /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector-role> molecule test -s centos7 --destroy always pid=179312
  py38-ansible212: FAIL code 1 (3.64=setup[0.15]+cmd[3.49] seconds)
  py38-ansible30: FAIL code 1 (4.39=setup[0.07]+cmd[4.32] seconds)
  evaluation failed :( (8.48 seconds
```
