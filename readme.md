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
molecule init role ev.vector --driver-name docker
INFO     Initializing new role vector...
Using /etc/ansible/ansible.cfg as config file
- Role vector was created successfully
localhost | CHANGED => {"backup": "","changed": true,"msg": "line added"}
INFO     Initialized role in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector successfully.
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole$ ls
clickhouse  defaults  files  handlers  meta  README.md  tasks  templates  tests  vars vector
инициируем новый сценарий
molecule init scenario centos_7 --driver-name docker
INFO     Initializing new scenario centos_7...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse/molecule/centos_7 successfully.

dependency:  
   name: galaxy
driver:
  name:  docker
platforms:
 - name: instance
   Image: quay.io/ centos/ centos:stream8 
  Pre_built_image: true
Provisioner:
 Name: ansible
Verifier:
Name: ansible
---
dependency:
  name: galaxy
driver:
  name: docker
lint:
 ansible-lint .
 yamllin .
platforms:
  - name: cenros8
    image: docker.io/pycontribs/centos:8
    pre_build_image: true
- name: cenros_7
    image: docker.io/pycontribs/centos:7
    pre_build_image: true
- name: Ubuntu_latest
    image: docker.io/pycontribs/ubuntu:latest
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible
verify.yml
- name: Verify
  hosts: all
  gather_facts: false
  vars:
    vector_config_path: /etc/vector/vector.yaml
  tasks:
  - name: Example asertion 
    ansible.builting.assert:
      that: true
  - name: Get Vector version
    ansible.builtin.command: "vector --version"
    changed_when: false
    register: vector_version
  - name: Assert Vector instalation
    assert:
      that: "'{{ vector_version.rc }}' == '0'"

  - name: Validation Vector configuration
    ansible.builtin.command: "vector validate --no-environment --config-yaml {{ vector_config_path }}"
    changed_when: false
    register: vector_validate
  - name: Assert Vector validate config
    assert:
      that: "'{{ vector_validate.rc }}' == '0'"


vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/clickhouse$ molecule test -s centos_7
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

3.
molecule init scenario --driver-name docker
INFO     Initializing new scenario default...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector/molecule/default successfully.
molecule init scenario centos8 --driver-name docker
INFO     Initializing new scenario centos8...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector/molecule/centos8 successfully.
molecule init scenario ubuntu_latest --driver-name docker
INFO     Initializing new scenario ubuntu_latest...
INFO     Initialized scenario in /home/vagrant/mnt-homeworks/08-ansible-05-testing/myrole/vector/molecule/ubuntu_latest successfully.
4.	 Добавьте несколько assert в verify.yml-файл для проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.).

5. molecule test -s centos8
INFO     centos8 scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Set ANSIBLE_LIBRARY=/home/vagrant/.cache/ansible-compat/b0d51c/modules:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/b0d51c/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/b0d51c/roles:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO     Using /home/vagrant/.cache/ansible-compat/b0d51c/roles/ev.vector symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Running centos8 > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running centos8 > lint
WARNING: ansible-lint is no longer tested under Python 3.8 and will soon require 3.9. Do not report bugs for this version.
Traceback (most recent call last):
  File "/usr/local/bin/ansible-lint", line 8, in <module>
    sys.exit(_run_cli_entrypoint())
  File "/usr/local/lib/python3.8/dist-packages/ansiblelint/__main__.py", line 359, in _run_cli_entrypoint
    sys.exit(main(sys.argv))
  File "/usr/local/lib/python3.8/dist-packages/ansiblelint/__main__.py", line 235, in main
    rules = RulesCollection(options.rulesdirs, profile_name=options.profile)
  File "/usr/local/lib/python3.8/dist-packages/ansiblelint/rules/__init__.py", line 388, in __init__
    for rule in load_plugins(rulesdirs):
  File "/usr/local/lib/python3.8/dist-packages/ansiblelint/rules/__init__.py", line 339, in load_plugins
    import_module(f"{f.stem}")
  File "/usr/lib/python3.8/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
  File "<frozen importlib._bootstrap>", line 991, in _find_and_load
  File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 848, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/usr/local/lib/python3.8/dist-packages/ansiblelint/rules/role_name.py", line 26, in <module>
    from functools import cache
ImportError: cannot import name 'cache' from 'functools' (/usr/lib/python3.8/functools.py)
WARNING  Retrying execution failure 1 of: a n s i b l e - l i n t   .   y a m l l i n t   .
CRITICAL Lint failed with error code 1
WARNING  An error occurred during the test sequence action: 'lint'. Cleaning up.
INFO     Running centos8 > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running centos8 > destroy
INFO     Sanity checks: 'docker'

PLAY [Destroy] *****************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=centos8)
changed: [localhost] => (item=centos_7)
changed: [localhost] => (item=ubuntu_latest)

TASK [Wait for instance(s) deletion to complete] *******************************
ok: [localhost] => (item=centos8)
ok: [localhost] => (item=centos_7)
ok: [localhost] => (item=ubuntu_latest)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory

Tox
1 install tox - sudo p Successfully installed cachetools-5.3.1 chardet-5.2.0 colorama-0.4.6 distlib-0.3.7 pyproject-api-1.6.1 tox-4.11.3 virtualenv-20.24.5ip3 install tox
vagrant@vagrant:~/mnt-homeworks/08-ansible-05-testing/myrole/vector$ ls
defaults  files  handlers  meta  molecule  README.md  tasks  templates  tests  tox.ini  tox-requirements.txt  vars
2. docker run --privileged=True -v  ~/mnt-homeworks/08-ansible-05-testing/myrole/vector:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
sudo docker run --privileged=True -v  ~/mnt-homeworks/08-ansible-05-testing/myrole/vector:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
[root@03d106e090c6 vector-role]#
3. [[root@03d106e090c6 vector-role]# tox
py37-ansible210 create: /opt/vector-role/.tox/py37-ansible210
py37-ansible210 installdeps: -rtox-requirements.txt, ansible<3.0
ERROR: invocation failed (exit code 2), logfile: /opt/vector-role/.tox/py37-ansible210/log/py37-ansible210-1.log
============================================================== log start ===============================================================Collecting ansible<3.0
  Downloading ansible-2.10.7.tar.gz (29.9 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━        24.8/29.9 MB 165.9 kB/s eta 0:00:31
ERROR: Exception:
Traceback (most recent call last):
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/urllib3/response.py", line 438, in _error_catcher
    yield
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/urllib3/response.py", line 519, in read
    data = self._fp.read(amt) if not fp_closed else b""
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/cachecontrol/filewrapper.py", line 90, in read
    data = self.__fp.read(amt)
  File "/usr/local/lib/python3.7/http/client.py", line 461, in read
    n = self.readinto(b)
  File "/usr/local/lib/python3.7/http/client.py", line 505, in readinto
    n = self.fp.readinto(b)
  File "/usr/local/lib/python3.7/socket.py", line 589, in readinto
    return self._sock.recv_into(b)
  File "/usr/local/lib/python3.7/ssl.py", line 1071, in recv_into
    return self.read(nbytes, buffer)
  File "/usr/local/lib/python3.7/ssl.py", line 929, in read
    return self._sslobj.read(len, buffer)
socket.timeout: The read operation timed out

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/cli/base_command.py", line 167, in exc_logging_wrapper
    status = run_func(*args)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/cli/req_command.py", line 205, in wrapper
    return func(self, options, args)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/commands/install.py", line 340, in run
    reqs, check_supported_wheels=not options.target_dir
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/resolver.py", line 95, in resolve
    collected.requirements, max_rounds=try_to_avoid_resolution_too_deep
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/resolvelib/resolvers.py", line 481, in resolve
    state = resolution.resolve(requirements, max_rounds=max_rounds)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/resolvelib/resolvers.py", line 348, in resolve
    self._add_to_criteria(self.state.criteria, r, parent=None)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/resolvelib/resolvers.py", line 172, in _add_to_criteria
    if not criterion.candidates:
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/resolvelib/structs.py", line 151, in __bool__
    return bool(self._sequence)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 155, in __bool__
    return any(self)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 143, in <genexpr>
    return (c for c in iterator if id(c) not in self._incompatible_ids)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 47, in _iter_built
    candidate = func()
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/factory.py", line 220, in _make_candidate_from_link
    version=version,
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 294, in __init__
    version=version,
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 158, in __init__
    self.dist = self._prepare()
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 227, in _prepare
    dist = self._prepare_distribution()
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 299, in _prepare_distribution
    return preparer.prepare_linked_requirement(self._ireq, parallel_builds=True)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/operations/prepare.py", line 487, in prepare_linked_requirement
    return self._prepare_linked_requirement(req, parallel_builds)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/operations/prepare.py", line 538, in _prepare_linked_requirement
    hashes,
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/operations/prepare.py", line 218, in unpack_url
    hashes=hashes,
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/operations/prepare.py", line 94, in get_http_url    from_path, content_type = download(link, temp_dir.path)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/network/download.py", line 146, in __call__
    for chunk in chunks:
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/cli/progress_bars.py", line 304, in _rich_progress_bar
    for chunk in iterable:
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_internal/network/utils.py", line 87, in response_chunks
    decode_content=False,
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/urllib3/response.py", line 576, in stream
    data = self.read(amt=amt, decode_content=decode_content)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/urllib3/response.py", line 541, in read
    raise IncompleteRead(self._fp_bytes_read, self.length_remaining)
  File "/usr/local/lib/python3.7/contextlib.py", line 130, in __exit__
    self.gen.throw(type, value, traceback)
  File "/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/pip/_vendor/urllib3/response.py", line 443, in _error_catcher
    raise ReadTimeoutError(self._pool, None, "Read timed out.")
pip._vendor.urllib3.exceptions.ReadTimeoutError: HTTPSConnectionPool(host='files.pythonhosted.org', port=443): Read timed out.
WARNING: You are using pip version 22.0.4; however, version 23.2.1 is available.
You should consider upgrading via the '/opt/vector-role/.tox/py37-ansible210/bin/python -m pip install --upgrade pip' command.

=============================================================== log end ================================================================ERROR: could not install deps [-rtox-requirements.txt, ansible<3.0]; v = InvocationError("/opt/vector-role/.tox/py37-ansible210/bin/python -m pip install -rtox-requirements.txt 'ansible<3.0'", 2)
py37-ansible30 create: /opt/vector-role/.tox/py37-ansible30
py37-ansible30 installdeps: -rtox-requirements.txt, ansible<3.1

py37-ansible30 installed: ansible==3.0.0,ansible-base==2.10.17,ansible-compat==1.0.0,ansible-lint==5.1.3,arrow==1.2.3,bcrypt==4.0.1,binaryornot==0.4.4,bracex==2.3.post1,cached-property==1.5.2,Cerberus==1.3.5,certifi==2023.7.22,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.2.0,click==8.1.7,click-help-colors==0.9.2,cookiecutter==2.3.1,cryptography==41.0.4,distro==1.8.0,enrich==1.2.7,idna==3.4,importlib-metadata==6.7.0,Jinja2==3.1.2,jmespath==1.0.1,lxml==4.9.3,markdown-it-py==2.2.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==1.1.0,packaging==23.1,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.2.0,pycparser==2.21,Pygments==2.16.1,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,requests==2.31.0,rich==13.5.3,ruamel.yaml==0.17.32,ruamel.yaml.clib==0.2.7,selinux==0.2.1,six==1.16.0,subprocess-tee==0.3.5,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.7.1,urllib3==2.0.5,wcmatch==8.4.1,yamllint==1.26.3,zipp==3.15.0
py37-ansible30 run-test-pre: PYTHONHASHSEED='1609218219'
py37-ansible30 run-test: commands[0] | molecule test -s compatibility --destroy always
CRITICAL 'molecule/compatibility/molecule.yml' glob failed.  Exiting.
ERROR: InvocationError for command /opt/vector-role/.tox/py37-ansible30/bin/molecule test -s compatibility --destroy always (exited with code 1)
py39-ansible210 create: /opt/vector-role/.tox/py39-ansible210
py39-ansible210 installdeps: -rtox-requirements.txt, ansible<3.0
ERROR: invocation failed (exit code 2), logfile: /opt/vector-role/.tox/py39-ansible210/log/py39-ansible210-1.log
============================================================================== log start ===============================================================================
Collecting ansible<3.0
  Downloading ansible-2.10.7.tar.gz (29.9 MB)
     ╸                                        0.5/29.9 MB 13.8 kB/s eta 0:35:23
ERROR: Exception:
Traceback (most recent call last):
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 438, in _error_catcher
    yield
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 519, in read
    data = self._fp.read(amt) if not fp_closed else b""
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/cachecontrol/filewrapper.py", line 90, in read
    data = self.__fp.read(amt)
  File "/usr/local/lib/python3.9/http/client.py", line 458, in read
    n = self.readinto(b)
  File "/usr/local/lib/python3.9/http/client.py", line 502, in readinto
    n = self.fp.readinto(b)
  File "/usr/local/lib/python3.9/socket.py", line 704, in readinto
    return self._sock.recv_into(b)
  File "/usr/local/lib/python3.9/ssl.py", line 1241, in recv_into
    return self.read(nbytes, buffer)
  File "/usr/local/lib/python3.9/ssl.py", line 1099, in read
    return self._sslobj.read(len, buffer)
ConnectionResetError: [Errno 104] Connection reset by peer

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/cli/base_command.py", line 167, in exc_logging_wrapper
    status = run_func(*args)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/cli/req_command.py", line 205, in wrapper
    return func(self, options, args)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/commands/install.py", line 339, in run
    requirement_set = resolver.resolve(
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/resolver.py", line 94, in resolve
    result = self._result = resolver.resolve(
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/resolvelib/resolvers.py", line 481, in resolve
    state = resolution.resolve(requirements, max_rounds=max_rounds)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/resolvelib/resolvers.py", line 348, in resolve
    self._add_to_criteria(self.state.criteria, r, parent=None)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/resolvelib/resolvers.py", line 172, in _add_to_criteria
    if not criterion.candidates:
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/resolvelib/structs.py", line 151, in __bool__
    return bool(self._sequence)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 155, in __bool__
    return any(self)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 143, in <genexpr>
    return (c for c in iterator if id(c) not in self._incompatible_ids)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 47, in _iter_built
    candidate = func()
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/factory.py", line 215, in _make_candidate_from_link
    self._link_candidate_cache[link] = LinkCandidate(
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 288, in __init__
    super().__init__(
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 158, in __init__
    self.dist = self._prepare()
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 227, in _prepare
    dist = self._prepare_distribution()
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 299, in _prepare_distribution
    return preparer.prepare_linked_requirement(self._ireq, parallel_builds=True)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 487, in prepare_linked_requirement
    return self._prepare_linked_requirement(req, parallel_builds)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 532, in _prepare_linked_requirement
    local_file = unpack_url(
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 214, in unpack_url
    file = get_http_url(
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 94, in get_http_url
    from_path, content_type = download(link, temp_dir.path)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/network/download.py", line 146, in __call__
    for chunk in chunks:
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/cli/progress_bars.py", line 304, in _rich_progress_bar
    for chunk in iterable:
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_internal/network/utils.py", line 63, in response_chunks
    for chunk in response.raw.stream(
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 576, in stream
    data = self.read(amt=amt, decode_content=decode_content)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 541, in read
    raise IncompleteRead(self._fp_bytes_read, self.length_remaining)
  File "/usr/local/lib/python3.9/contextlib.py", line 135, in __exit__
    self.gen.throw(type, value, traceback)
  File "/opt/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 455, in _error_catcher
    raise ProtocolError("Connection broken: %r" % e, e)
pip._vendor.urllib3.exceptions.ProtocolError: ("Connection broken: ConnectionResetError(104, 'Connection reset by peer')", ConnectionResetError(104, 'Connection reset by peer'))
WARNING: You are using pip version 22.0.4; however, version 23.2.1 is available.
You should consider upgrading via the '/opt/vector-role/.tox/py39-ansible210/bin/python -m pip install --upgrade pip' command.

=============================================================================== log end ================================================================================
ERROR: could not install deps [-rtox-requirements.txt, ansible<3.0]; v = InvocationError("/opt/vector-role/.tox/py39-ansible210/bin/python -m pip install -rtox-requirements.txt 'ansible<3.0'", 2)
py39-ansible30 create: /opt/vector-role/.tox/py39-ansible30
py39-ansible30 installdeps: -rtox-requirements.txt, ansible<3.1
ERROR: invocation failed (exit code 2), logfile: /opt/vector-role/.tox/py39-ansible30/log/py39-ansible30-1.log
============================================================================== log start ===============================================================================
Collecting ansible<3.1
  Using cached ansible-3.0.0.tar.gz (30.8 MB)
  Preparing metadata (setup.py): started
  Preparing metadata (setup.py): finished with status 'done'
Collecting selinux
  Downloading selinux-0.3.0-py2.py3-none-any.whl (4.2 kB)
Collecting ansible-lint==5.1.3
  Using cached ansible_lint-5.1.3-py3-none-any.whl (113 kB)
Collecting yamllint==1.26.3
  Using cached yamllint-1.26.3.tar.gz (126 kB)
  Preparing metadata (setup.py): started
  Preparing metadata (setup.py): finished with status 'done'
Collecting lxml
  Downloading lxml-4.9.3-cp39-cp39-manylinux_2_28_x86_64.whl (8.0 MB)
     ━━━━━                                    1.0/8.0 MB 25.5 kB/s eta 0:04:35
ERROR: Exception:
Traceback (most recent call last):
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 438, in _error_catcher
    yield
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 519, in read
    data = self._fp.read(amt) if not fp_closed else b""
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/cachecontrol/filewrapper.py", line 90, in read
    data = self.__fp.read(amt)
  File "/usr/local/lib/python3.9/http/client.py", line 458, in read
    n = self.readinto(b)
  File "/usr/local/lib/python3.9/http/client.py", line 502, in readinto
    n = self.fp.readinto(b)
  File "/usr/local/lib/python3.9/socket.py", line 704, in readinto
    return self._sock.recv_into(b)
  File "/usr/local/lib/python3.9/ssl.py", line 1241, in recv_into
    return self.read(nbytes, buffer)
  File "/usr/local/lib/python3.9/ssl.py", line 1099, in read
    return self._sslobj.read(len, buffer)
ConnectionResetError: [Errno 104] Connection reset by peer

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/cli/base_command.py", line 167, in exc_logging_wrapper
    status = run_func(*args)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/cli/req_command.py", line 205, in wrapper
    return func(self, options, args)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/commands/install.py", line 339, in run
    requirement_set = resolver.resolve(
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/resolver.py", line 94, in resolve
    result = self._result = resolver.resolve(
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/resolvelib/resolvers.py", line 481, in resolve
    state = resolution.resolve(requirements, max_rounds=max_rounds)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/resolvelib/resolvers.py", line 348, in resolve
    self._add_to_criteria(self.state.criteria, r, parent=None)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/resolvelib/resolvers.py", line 172, in _add_to_criteria
    if not criterion.candidates:
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/resolvelib/structs.py", line 151, in __bool__
    return bool(self._sequence)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 155, in __bool__
    return any(self)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 143, in <genexpr>
    return (c for c in iterator if id(c) not in self._incompatible_ids)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/found_candidates.py", line 47, in _iter_built
    candidate = func()
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/factory.py", line 215, in _make_candidate_from_link
    self._link_candidate_cache[link] = LinkCandidate(
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 288, in __init__
    super().__init__(
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 158, in __init__
    self.dist = self._prepare()
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 227, in _prepare
    dist = self._prepare_distribution()
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/resolution/resolvelib/candidates.py", line 299, in _prepare_distribution
    return preparer.prepare_linked_requirement(self._ireq, parallel_builds=True)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 487, in prepare_linked_requirement
    return self._prepare_linked_requirement(req, parallel_builds)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 532, in _prepare_linked_requirement
    local_file = unpack_url(
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 214, in unpack_url
    file = get_http_url(
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/operations/prepare.py", line 94, in get_http_url
    from_path, content_type = download(link, temp_dir.path)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/network/download.py", line 146, in __call__
    for chunk in chunks:
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/cli/progress_bars.py", line 304, in _rich_progress_bar
    for chunk in iterable:
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_internal/network/utils.py", line 63, in response_chunks
    for chunk in response.raw.stream(
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 576, in stream
    data = self.read(amt=amt, decode_content=decode_content)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 541, in read
    raise IncompleteRead(self._fp_bytes_read, self.length_remaining)
  File "/usr/local/lib/python3.9/contextlib.py", line 135, in __exit__
    self.gen.throw(type, value, traceback)
  File "/opt/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/pip/_vendor/urllib3/response.py", line 455, in _error_catcher
    raise ProtocolError("Connection broken: %r" % e, e)
pip._vendor.urllib3.exceptions.ProtocolError: ("Connection broken: ConnectionResetError(104, 'Connection reset by peer')", ConnectionResetError(104, 'Connection reset by peer'))
WARNING: You are using pip version 22.0.4; however, version 23.2.1 is available.
You should consider upgrading via the '/opt/vector-role/.tox/py39-ansible30/bin/python -m pip install --upgrade pip' command.

=============================================================================== log end ================================================================================
ERROR: could not install deps [-rtox-requirements.txt, ansible<3.1]; v = InvocationError("/opt/vector-role/.tox/py39-ansible30/bin/python -m pip install -rtox-requirements.txt 'ansible<3.1'", 2)
_______________________________________________________________________________ summary ________________________________________________________________________________
ERROR:   py37-ansible210: could not install deps [-rtox-requirements.txt, ansible<3.0]; v = InvocationError("/opt/vector-role/.tox/py37-ansible210/bin/python -m pip install -rtox-requirements.txt 'ansible<3.0'", 2)
ERROR:   py37-ansible30: commands failed
ERROR:   py39-ansible210: could not install deps [-rtox-requirements.txt, ansible<3.0]; v = InvocationError("/opt/vector-role/.tox/py39-ansible210/bin/python -m pip install -rtox-requirements.txt 'ansible<3.0'", 2)
ERROR:   py39-ansible30: could not install deps [-rtox-requirements.txt, ansible<3.1]; v = InvocationError("/opt/vector-role/.tox/py39-ansible30/bin/python -m pip install -rtox-requirements.txt 'ansible<3.1'", 2) 
4 Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость
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
5. sudo docker run --privileged=True -v  ~/mnt-homeworks/08-ansible-05-testing/myrole/vector:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
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



