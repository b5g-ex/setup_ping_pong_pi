### contents

- ansible.cfg is ansible's configuration file, which is INI file.
- inventory.yml is target machine settings.
- main.yml is recipes for target.

### install ansible

```sh
apt install ansible sshpass
```

### run ansible

```sh
ansible-playbook -i inventory.yml main.yml
```
