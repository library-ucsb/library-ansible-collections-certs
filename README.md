# Ansible Collection - library.certs


Roles
===========

* [library.certs.sectigo](roles/sectigo)


Usage
===========


### Inclusion

To include the `library.certs` collection within your playbook, include the following within the `collections` section of your `requirements.txt` document

```
collections:
  - name: git@github.com:library-ucsb/library-ansible-collections-certs.git
```

Then update ansible-galaxy
```
$ ansible-galaxy -r requirements.txt
```

If you need to force update/re-install:
```
$ ansible-galaxy -r requirements.txt --force
```

If you want to force install **ONLY** this collection:
```
$ ansible-galaxy collection install -f git@github.com:library-ucsb/library-ansible-collections-certs.git
```

### Roles

With the collection now installed, you may use the roles that it provides.  Each role includes it's own parameters and usage instructions within it's root README.md.

