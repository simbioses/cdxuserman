---
title: "Clinic Registration"
date: 2019-06-14T11:37:29-07:00
weight: 5
---

Login into the OBIB's VM via SSH:

```
$ vagrant ssh
```

Go to the Vagrant's shared folder **/vagrant**:

```
$ cd /vagrant/
```

Execute the register script with the required option:

```
$ ./register.sh
Usage: ./register.sh OPTION [VALUE]
Options:
    -r | --register   : register a new clinic
    -u | --unregister : unregister an existent clinic
    -c | --check      : check if a clinic is registered.
                        requires the clinic id as [VALUE]
    -h | --help       : this help information
```

The register option will create a keystore **obibcerts.pfx** along with other files for the registered clinic (**&lt;clinic_username&gt;.crt**, **&lt;clinic_username&gt;.csr** and **&lt;clinic_username&gt;.key**). Those files must be stored in a safe place, and the keystore copied to the classpath of the *OBIB Connector*.

```
$ ./register.sh -r
Please, enter the clinic information.
 notes:
  - fields marked with (CDX) are information provided or generated within CDX.
  - fields marked with (OBIB) are new information required to generate the OBIB Connector certificate.
Clinic ID (CDX): test_clinic_id
Clinic Name (CDX): Test Clinic Name
Clinic Username (CDX): clinic_username
Clinic Password (CDX): clinicPass
CDX KeyStore Path (CDX): TestClinicKeystore.pfx
CDX Keystore Password (CDX): keystorePass
New Password for Clinic Certificate (OBIB): obibKeystorePass
Please, confirm the clinic information. "c" = confirm, "r" = redo, "q" = quit: c

Saving clinic information...

Verifying clinic registration...
Clinic registered.
Certificate file found.

Generating clinic cetificate for OBIB connector...
Generating a 2048 bit RSA private key
.................+++
................+++
writing new private key to './clinic_username.key'
-----
Signature ok
subject=C = CA, O = OSP, OU = OBIB, CN = Test Clinic Name
Getting CA Private Key
Certificate was added to keystore

The clinic certificate was created and imported into OBIB keystore.
Please, move the generated files (./clinic_username.key, ./clinic_username.csr, ./clinic_username.crt and ./obibcerts.pfx) to a secure place.

$ ls
certs                deploy.sh          README.md
clinic_username.crt  gen_obib_certs.sh  register.sh
clinic_username.csr  install.sh         TestClinicKeystore.pfx
clinic_username.key  mirth_connect.sh   Vagrantfile
configs              obibcerts.pfx
```
