---
title: NFS-ügyfél konfigurálása a Azure NetApp Fileshoz | Microsoft Docs
description: Ismerteti, hogyan lehet konfigurálni az NFS-ügyfeleket a Azure NetApp Filessal való használatra.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: c1cdeaa41dda11f2ab520cf8d31ddb2116587082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409569"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>NFS-ügyfél konfigurálása az Azure NetApp Fileshoz

Az ebben a cikkben ismertetett NFS-ügyfél konfigurációja a [nfsv 4.1 Kerberos-titkosítás konfigurálásakor](configure-kerberos-encryption.md) vagy [egy kettős protokollú kötet létrehozásakor](create-volumes-dual-protocol.md)a telepítés része. A Linux-disztribúciók széles választéka használható a Azure NetApp Fileshoz. Ez a cikk két leggyakrabban használt környezet konfigurációit ismerteti: RHEL 8 és Ubuntu 18,04. 

A használt Linux-íztől függetlenül a következő konfigurációk szükségesek:
* Konfiguráljon egy NTP-ügyfelet, hogy elkerülje a problémák időbeli eldöntését.
* Konfigurálja a Linux-ügyfél DNS-bejegyzéseit névfeloldásra.  
    Ebben a konfigurációban szerepelnie kell az "A" (továbbítás) rekordnak és a PTR (fordított) rekordnak. 
* A tartományhoz való csatlakozáshoz hozzon létre egy számítógépfiókot a Linux-ügyfél számára a cél Active Directoryban (amely a tartományhoz való csatlakozás parancsban jön létre). 
    > [!NOTE] 
    > Az `$SERVICEACCOUNT` alábbi parancsokban használt változónak olyan felhasználói fióknak kell lennie, amely rendelkezik engedélyekkel vagy delegálással számítógépfiók létrehozásához a célként megadott szervezeti egységben.

## <a name="rhel-8-configuration"></a>RHEL 8 konfiguráció 

Ez a szakasz a NFSv 4.1 Kerberos-titkosításhoz és a kettős protokollhoz szükséges RHEL-konfigurációkat ismerteti.  

Az ebben a szakaszban szereplő példák a következő tartománynevet és IP-címet használják:  

* Tartománynév: `contoso.com`
* Magánhálózati IP-cím: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>RHEL 8 konfiguráció, ha NFSv 4.1 Kerberos-titkosítást használ

1. Konfigurálja `/etc/resolv.conf` a megfelelő DNS-kiszolgálóval.  

    Például:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Adja hozzá az NFS-ügyfél rekordját a DNS-kiszolgálón a DNS-címkeresés és a névkeresési zóna zónához.

3. A DNS ellenőrzéséhez használja az alábbi parancsokat az NFS-ügyfélről:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Csomagok telepítése:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Konfigurálja az NTP-ügyfelet.  

    A RHEL 8 alapértelmezés szerint a chrony használja. A konfigurációs irányelveket követve [ `Chrony` KONFIGURÁLJA az NTP-t a Suite használatával](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Csatlakozzon a Active Directory tartományhoz:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Például: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    Győződjön meg arról, hogy `default_realm` a a megadott tartományra van beállítva `/etc/krb5.conf` .  Ha nem, adja hozzá a `[libdefaults]` fájl szakaszához a következő példában látható módon:
    
    `default_realm = CONTOSO.COM`

7. Az összes NFS-szolgáltatás újraindítása:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Az újraindítás megakadályozza a hiba feltételét a `“mount.nfs: an incorrect mount option was specified”` Kerberos-csatlakoztatás során.

8. Futtassa a `kinit` parancsot a felhasználói fiókkal a jegyek beszerzéséhez: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Például:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>RHEL 8 konfiguráció, ha kettős protokollt használ

A következő lépések nem kötelezőek. A lépéseket csak akkor kell végrehajtania, ha felhasználói leképezést használ az NFS-ügyfélen: 

1. [Ha nfsv 4.1 Kerberos titkosítási szakaszt használ](#rhel8_nfsv41_kerberos) , hajtsa végre a RHEL 8 konfigurációban ismertetett lépéseket.   

2. Vegyen fel egy statikus DNS-rekordot a/etc/hosts-fájlba, hogy az AD-hez tartozó teljes tartománynevet (FQDN) használja a SSSD konfigurációs fájljában található IP-cím használata helyett:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. További szakasz hozzáadása a tartományokhoz az AD LDAP-kiszolgáló azonosítóinak feloldásához:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Győződjön meg arról `/etc/nsswitch.conf` , hogy rendelkezik a `sss` bejegyzéssel:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Indítsa újra a `sssd` szolgáltatást, és törölje a gyorsítótárat:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Ellenőrizze, hogy az ügyfél integrálva van-e az LDAP-kiszolgálóval:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu-konfiguráció   

Ez a szakasz a NFSv 4.1 Kerberos-titkosításhoz és a kettős protokollhoz szükséges Ubuntu-konfigurációkat ismerteti. 

Az ebben a szakaszban szereplő példák a következő tartománynevet és IP-címet használják:  

* Tartománynév: `contoso.com`
* Magánhálózati IP-cím: `10.6.1.4`

1. Konfigurálás `/etc/resolv.conf` a megfelelő DNS-kiszolgálóval:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Adja hozzá az NFS-ügyfél rekordját a DNS-kiszolgálón a DNS-címkeresés és a névkeresési zóna zónához.
 
    A DNS ellenőrzéséhez használja az alábbi parancsokat az NFS-ügyfélről:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Csomagok telepítése:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    Ha a rendszer kéri, adja `$DOMAIN.NAME` meg az alapértelmezett Kerberos-tartomány bemenetet (nagybetűvel, például `CONTOSO.COM` ).

4. A szolgáltatás újraindítása `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. Az Ubuntu 18,04 alapértelmezés szerint chrony használ. A konfigurációs irányelvek az [Ubuntu Bionic-ben: a Chrony használata az NTP konfigurálásához](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Csatlakozzon a Active Directory tartományhoz:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Például:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. `kinit`A felhasználóval elvégezheti a jegyek beszerzését: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Például:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Ubuntu-konfiguráció, ha kettős protokollt használ  

A következő lépések nem kötelezőek.  A lépéseket csak akkor kell végrehajtania, ha felhasználói leképezést szeretne használni az NFS-ügyfélen:  

1. Futtassa a következő parancsot a telepített csomagok frissítéséhez:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Az alábbi példa példákat használ. Ha a parancs bekéri a bevitelt, a környezet alapján kell megadnia a bemenetet. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Futtassa a következő parancsot a szolgáltatás újraindításához és engedélyezéséhez:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

Az alábbi példa az AD LDAP-kiszolgálót az Ubuntu LDAP-ügyfélről kérdezi le egy LDAP-felhasználó számára `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Következő lépések  

* [NFS-kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [Kettős protokollú kötet létrehozása a Azure NetApp Fileshoz](create-volumes-dual-protocol.md)

