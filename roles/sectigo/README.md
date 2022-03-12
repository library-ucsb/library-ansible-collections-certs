library.certs.sectigo
=========

Manages the request and acquisition of a Sectigo SSL Signed Certificate via an ACME interface using EAB authentication.

Requirements
------------

- Delegated subdomain certificate management privileges established by UCSB SOC. [See Here](https://www.it.ucsb.edu/ssl-certificates/ssl-certificates-subdomain-delegation)
- Access to manage your domains via [Sectigo Certificate Manager Portal](https://www.cert-manager.com/customer/incommon).


Role Variables
--------------

All of the tunable variables can be found in `defaults/main.yml`.  The following are the key values that need adjustment, or at least further consideration.

#### `certs_default_email`

The email address registered with this certificate

#### `certs_name` 

The name given to this certificate.  any deriviate certificate files will contain this name.  

(default: acme-default.library.ucsb.edu)

#### `certs_cn` 

The common name.  typically the primary fqdn for the certiticate. 

(default: acme-default.library.ucsb.edu)


#### `certs_sans`

A  list of subject alternative names to include. 

( default: [] )  

#### `certs_acme_private_key`

This is the relative path for the certificate bound to your ACME EAB account.  If this file does not exist, one will be generated and bound/registered to your ACME EAB account, using this filepath.

**First Time with New ACME EAB Credentials**
1. This playbook is executed using new ACME EAB credentials.
2. The playbook will look for the file specified in `certs_acme_private_key`, see that it does not exist, and proceed to generate one.

**Following Runs Post ACME EAB Cert Creation and Registration with Sectigo**
1. The playbook is executed using existing ACME EAB credentials.
2. The playbook looks and finds the previously generated certificate file referenced in `certs_acme_private_key`, and proceed to use it in all subsequent certificate tasks in this playbook run.

**NOTE:** I haven't determined if one could just regenerate a certificate each run, discarding the previous registered certificate.  If so, do the previous certificates remain registered?  Does Sectigo evict the existing certificate?

#### `certs_acme_server_url`

The http endpoint used for ACME transactions.  When creating a Sectigo Domain ACME Account, there will be an endpoint url associated with it.  The two main endpoints used:

* https://acme.sectigo.com/v2/InCommonECCOV
* https://acme.sectigo.com/v2/InCommonRSAOV

#### `certs_acme_eab_key` and `certs_acme_eab_kid`

These attributes are the ACME EAB authentication values received when adding a new account to a Sectigo ACME endpoint under the section titled: **External Account Binding**.

* `certs_acme_eab_key`: the HMAC Key received from Sectigo
* `certs_acme_eab_kid`: the Key ID received from Sectigo

#### `certs_key_path`

This is the path, relative to the root of the playbook, where the generated certificate files will be placed.  Will be created if it does not exist.

#### `certs_keys_days`

The generated certificates lifespan.  Represented in number of days.

default: 90
maximum: 365 ( I think... )

#### `certs_key_passphrase` and `certs_key_cipher`

If you need to password protect the corresponding private key generated for the certificate, you establish that here.  **Both** values must be asserted, or **both** commented out.

`certs_key_passphrse`: passphrased assigned to the private key
`certs_key_cipher`: best to set this to `auto`

#### `certs_pkcs12_cert_passphrase`

If interested in the pkcs12 certificate that this playbook can generate, you will need to provide an "unlock" passphrase to assign.  Note that this is **not** the same as the private key passphrase, and from what I understand, is stored in a rather insecure manner.  It is necessary if you want a successfully generated pkcs12 cert, however.  If you don't care, just leave the default.


Dependencies
------------

None

Example Playbook
----------------

### Single Host Example Playbook
```
---
- name: single host certificate generation
  hosts: localhost
  become: no

  vars:
    certs_default_email: admin@domain.ucsb.edu
    certs_name: www.domain.ucsb.edu
    certs_sans:
      - site2.domain.ucsb.edu
      - site3.domain.ucsb.edu

    certs_acme_private_key: acme-rsa-private-key.pem
    certs_acme_server_url: https://acme.sectigo.com/v2/InCommonRSAOV

    certs_acme_eab_key: fa9dfhada8d8adfg8afd98gu98adfu8gsd9fg98dsfy8vsdf798sdn8dn8
    certs_acme_eab_kid: 5k620fg0590slk4k50g

    certs_key_path: certs/
    certs_key_days: 30

  roles:

    - name: library.certs.sectigo
      tags: [certs, ssl, sectigo]

```

To run
```
$ ansible-playbook --inventory=inventory.yml your_playbook_file.yml
```

If successful, you should have a series of certificates within the specfied `certs_key_path` directory.  These would be all the same files represented by the Sectigo/Incommon links delivered via email when requesting certs manually.

### Multiple Certs

`groups_vars/all/websites.yml`
```
list_of_domains:
  - name: www.domain.ucsb.edu
    ssl: true
    sans:
      - test-www.domain.ucsb.edu

  - name: mail.domain.ucsb.edu
    ssl: true
    sans: []

  - name: support.domain.ucsb.edu
    ssl: true
    sans: []
```

The following is a playbook I use to generate and deploy certificates out to one of our haproxy load balancers.

```
- name: create certificates for the defined sites
  hosts: localhost
  connection: local
  gather_facts: false

  vars:
    certs_default_email:    admin@domain.ucsb.edu
    certs_acme_private_key: acme-rsa-private-key.pem
    certs_acme_server_url:  https://acme.sectigo.com/v2/InCommonRSAOV
    certs_acme_eab_key:     fa9dfhada8d8adfg8afd98gu98adfu8gsd9fg98dsfy8vsdf798sdn8dn8
    certs_acme_eab_kid:     5k620fg0590slk4k50g
    certs_key_path:         certs/
    certs_key_days:         30


  tags: generate

  tasks:
    - name: generate site certificates
      include_role:
        name: library.certs.sectigo
      vars:
        certs_name:             "{{ item.name }}"
        certs_cn:               "{{ item.name }}"
        certs_sans:             "{{ item.sans }}"
        certs_acme_private_key: "../secrets/acme-private-key-ACTIVE.pem"
        certs_acme_eab_kid:     "{{ vault_certs_acme_rsa_eab_kid }}"
        certs_acme_eab_key:     "{{ vault_certs_acme_rsa_eab_key }}"
        certs_acme_server_url:  "{{ vault_certs_acme_rsa_server_url }}"
    
      loop: "{{ list_of_domains | selectattr('ssl') }}"


- name: deploy certificates
  hosts: ansible_haproxy_legacy
  gather_facts: true
  become: true

  tags: deploy

  tasks:

    - name: ensure '/etc/haproxy/certs' directory creation
      file:
        path: /etc/haproxy/certs
        owner: root
        group: root
        mode: '0750'
        state: directory

    - name: copy certs to haproxy hosts
      copy:
        src: "{{ certs_key_path }}/{{ item.name }}.haproxy.pem"
        dest: "/etc/haproxy/certs/"
        owner: haproxy
        group: haproxy
        mode: 0600
      loop: "{{ list_of_domains | selectattr('ssl') }}"
      notify:
        - restart haproxy

  handlers:

    - name: restart haproxy
      ansible.builtin.service:
       name: haproxy
       state: restarted   
```

To run:
```
$ ansible-playbook --inventory.yml name_of_your_playbook.yml
```


License
-------

BSD

Author Information
------------------
Josh is an ops person at the UCSB Library.

