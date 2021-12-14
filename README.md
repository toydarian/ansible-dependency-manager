Ansible Dependency Manager
==========================

The tools collected in this repository all try to solve the same issue:  
When multiple people are working on the same playbooks and roles, somebody will have an outdated version of some role when running a playbook that includes this role which will break something.  
If you are unsure if it will solve your problem, have a look at ["Why I started this project"](docs/why.md).

The Simple Solution
-------------------

This is the most simple solution which will work just fine if you have a bunch of roles in git-repositories that you want to have on the correct versions for everybody.  
It will not cosider any dependencies that are specified in the roles, you need to specify all roles including transitive dependencies. (E.g. if you need a role called `role-a`, but that has a dependency on `role-b`, you need to specify `role-b` explicitely)

You need to have a list of dependencies in `role_deps.roles`, where each item at least has a field `name` and `repo`. Then you can include [simple/tasks.yml](simple/tasks.yml) to run on a local connection and the specified version (default is `master`) of all roles will be checked out to `roles/`.  
To try it, create a file called `test.yml` in this directory with the following content.
```yaml
- name: ensure correct versions of required roles are installed
  connection: local
  hosts: 127.0.0.1
  vars:
    role_deps:
      roles:
        - name: postgres
          repo: "https://github.com/geerlingguy/ansible-role-postgresql.git"
          version: "3.2.0"
  tasks:
    - name: update roles
      include_tasks: 'simple/tasks.yml'
```
Then run `ansible-playbook test.yml`. When it finished, you should find a directory called `roles/` that contains the specified repository in a directory called `postgres`.  
For documentation on all options, check [docs/simple.md](docs/simple.md)
