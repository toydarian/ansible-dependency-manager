The Simple Solution
===================

This solution relies purely on ansible and doesn't require any additional modules. All you need is to specifiy your roles and include the tasks from `/simple/tasks.yml`.

Configuration
-------------

As this uses the [git-module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/git_module.html), most of the configuration here is diretly used on this module.

Here is an example-configuration:
```yaml
---
role_deps:
  accept_hostkey: yes
  roles_dir: "roles/"
  fail_when_changed: false
  roles:
    - name: postgres
      repo: "https://github.com/geerlingguy/ansible-role-postgresql.git"
      version: "3.2.0"
      force: yes
```

Explanation:

 - `accept_hostkeys`: skips the hostkey-check if set to `yes` (default: `no`)
 - `roles_dir`: where to store the roles (default: `roles/`)  
   If you change that, you need to make sure that the directory is in the search-path yourself!
 - `fail_when_changed`: Fail after pulling all roles if at least one of them changed. See [Limitations](#limitations) for more information. (default: `false`)
 - `roles`: a list of roles to manage (required)
 - `roles[*].name`: the name of the directory in `roles` where the role is stored (required)
 - `roles[*].repo`: the repository where the role is managed (required)
 - `roles[*].version`: version (tag/branch/hash) to check out (default: `master`)
 - `roles[*].force`: overwrite locally changed files in the role (default: `no`)

What Happens
------------

The role will ensure the `roles_dir` directory exists and then ensure the specified versions of all specified roles are in there. It will clone missing roles and check out the specified versions if necessary.

Limitations
-----------

- Nested dependencies (e.g. such specified in a role in `meta/main.yml`) are not taken into account. If those exist, you need to list them in `role_deps.roles`.
- No validation of roles. You can specify any repository. If it doesn't contain a valid role, this will not fail.
- Only works with `include_role`, not with `import_role` if it is in the same playbook. This is due to `import_role` processing the role before the version is updated. So if you use `import_role`, your role will be updated but the old version of the role will run.
- Doesn't work with the `roles`-directive of plays, as the role needs to be present when the playbook starts running.

To work around the last two items, you can set `role_deps.fail_when_changed` to `true` to make sure you use the lates version with `import_role` even if it changed. The role still needs to be present before running, though.
