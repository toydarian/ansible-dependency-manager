Why I started this project
==========================

Imagine you and your team of three people are managing several environments where each of them consists of a number of components.  
You have an inventory for each environment and a role for each component. Then you have one or more playbooks to setup / update environments that include those roles.

Each of the roles has its own repository and the playbooks and inventories are in another "head"-repository:

```
head-repo
|--+ roles
|  |-- role-1
|  |-- role-2
|  +-- role-3
+--+ inventory
|  |-- env-1.yml
|  +-- env-2.yml
+--- playbook.yml
```

You could, for example, have the roles as git-sub-modules or, you just have them cloned there. In any case, you rely on your team to do a `git pull` on each role before running the playbook. For three roles, that might be doable, but if you have some more, this gets tedious.

Now assume, you changed `role-1`, because the component that it installs needs an additional configuration option. The file is managed using a template, so you add the new option to the template, push the changes and run it on all environments. Your colleague changes `role-2`, as a package that is installed in this role needs an important security-update. He forgets to pull `role-1` but runs the playbook, as well. That leads to your changes of the config-files being overwritten. What follows is a debugging session, you wondering why your changes are suddenly gone on the environments, finally the realization that somebody did not pull the role and then some annoyed/passive-aggressive mails with a reminder to not forget pulling.

So what do you do to avoid this kind of situation? You automate the process of pulling the correct version of each role before running the playbook. And this is exactly what this project tires to achieve.
