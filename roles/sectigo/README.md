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

The name given to this certificate.  any deriviate certificate files will contain this name.  (default: acme-default.library.ucsb.edu)

#### `certs_cn` 

The common name.  typically the primary fqdn for the certiticate. (default: acme-default.library.ucsb.edu)


#### `certs_sans`

A  list of subject alternative names to include. (default: [] )  

#### `certs_acme_private_key`

This is the certificate bound to your ACME EAB account.  If this file does not exist, one will be generated and bound/registered to your ACME EAB account, using this filepath.

Upon first usage of an ACME EAB account, this file won't exist.  The play will then generate the 


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
