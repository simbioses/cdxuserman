# Oscar Bc Interoperability Broker (OBIB) Deployment Instructions

## Introduction

The server side of OBIB is implemented as a group of Mirth Connect channels that transforms messages received from the OBIB Connector into CDA messages and sends those messages to CDX through the Web Service.

OBIB server depends on a serie of softwares, namely Mirth Connect, Java, MySQL/MariaDB, OpenSSL and NGINX. Thus, to simplify the OBIB installation, a production-ready Vagrant VM was created along with administrative scripts to manage the clinics within OBIB.

This document shows how to install and update the Vagrant VM and how to register a new clinic in OBIB. In addition it is also shown how to build an OBIB deployment file, and how to manually deploy OBIB within Mirth Connect. These lasts tasks are usefull for OBIB developers.

## Installing a Vagrant VM for OBIB

The following steps are going to create a Vagrant VM with OBIB ready to production.

1. Uncompress the **obib-&lt;version-number&gt;.zip** file and enter in the extracted folder **obib-&lt;version-number&gt;/**.

    For development environment it is recommended to start the Vagrant VM from the source folder, e.g., **&lt;dev-workspace&gt;/OBIB/mirthchannels/OBIB_vm**. Thus, any change to the scripts will be immediately available for the VM as well.

2. Change the configurations in **Vagrantfile** and **mirth_connect.sh** as needed. Most settings are within mirth_connect.sh only, except for the VM IP address which is located in both files.

    VM IP address in Vagrantfile:

    ```
    config.vm.network "private_network", ip: "192.168.100.101"
    ```

    Mirth Connect (VM) IP address in mirth_connect.sh:

    ```
    SERVER_IP='192.168.100.101'
    ```

3. To create a new Vagrant VM with the environment installed and configured execute the following command:

    ```
    $ vagrant up
    ```

4. Then, restart the VM to ensure all services are starting at boot time:

    ```
    $ vagrant reload
    ```

## Deploying OBIB

To deploy a new OBIB or redeploy an update of OBIB, execute the deploy provision from the folder where Vagrant VM is installed in, using the following command:

```
$ vagrant provision --provision-with deploy
```

*All operations should display **Response code: 2XX** in the console*. In case of error, all deployment operation returns are stored in the folder /home/vagrant/output in the VM.*

If the installations is successful, Mirth Connect and OBIB is available from the same IP configured in the Step 2. For example, OBIB Connector addres will be available at https://192.168.100.101 and Mirht Connector Adminitration at https://192.168.100.101:8443 if the VM IP address is 192.168.100.101.

## Registering a new Clinic

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

The register option will create a keystore **obibcerts.pfx** along with other files for the registered clinic (**&lt;clinic_username&gt;.crt**, **&lt;clinic_username&gt;.csr** and **&lt;clinic_username&gt;.key**). Those files must be stored in a safe place, and the keystore copied for the classpath of *OBIB Connector*.

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

## Generating the OBIB deployment file

To create an ZIP file for deployment, execute the maven deploy command from the IDE or from the OBIB source folder. E.g.:
    
```
$ mvn deploy
```

This command is going to generate an **obib-&lt;version-number&gt;.zip** file inside the folder *target/* inside the OBIB source directory.

## Manual Deployment

This deployment option is executed using the *Mirth Connect Administrator*, available at https://www.nextgen.com/products-and-services/NextGen-Connect-Integration-Engine-Downloads.

Login into the Mirth Connect Administrator

[//]: # "TODO add image of Laucher?"

The address and credentials to access Mirth Connect using the Administrator are defined in the mirth_connect.sh file, used during the Vagrant VM installation.

[//]: # "TODO add image of Laucher Login?"

The two necessary files to deploy OBIB are: the **OBIB_global_scripts.xml**, which contains the Vagrant Global Scripts and **OBIB_channel_group.xml**, which contains the OBIB Channels and Template Codes.

[//]: # "TODO add image of Mirth Main screen?"

To import the Vagrant Global Scripts do:

1. Click on **Channels** in Mirth Connect menus.
3. Click on **Edit Global Scripts** in Channel Tasks menus.
4. Click on **Import Scripts** in Script Tasks menus.
5. Select the **OBIB_global_scripts.xml** file from */mirthchannels/OBIB_vm/configs/obib/* folder.
6. Click on **Save Scripts** in Script Tasks menus.

To import the OBIB Channes and Template Codes do:

1. Click on **Channels** in Mirth Connect menus.
2. Click on **Import Group** in Group Tasks menu.
3. Select the **OBIB_channel_group.xml** file from */mirthchannels/OBIB_vm/configs/obib/* folder.
4. Answer **Yes** to import the Code Template Libraries at same time.

After the previous steps, OBIB is ready to deploy within Mirht Connect. This is done by selecting the **OBIB** group on main screen and clickin on **Redeploy all** in the Channel Tasks menus.
