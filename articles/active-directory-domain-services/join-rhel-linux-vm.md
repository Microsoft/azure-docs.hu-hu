---
title: RHEL virtuális gép csatlakoztatása Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és csatlakoztathat egy Red Hat Enterprise Linux virtuális gépet egy Azure AD Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 285a972936bfdf4b173e2a20223143883cd8b7d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619555"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Red Hat Enterprise Linux virtuális gép csatlakoztatása Azure Active Directory Domain Services felügyelt tartományhoz

Annak érdekében, hogy a felhasználók egyetlen hitelesítő adat használatával jelentkezzenek be a virtuális gépekre az Azure-ban, csatlakoztathatja a virtuális gépeket egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományhoz. Amikor egy virtuális gépet csatlakoztat egy Azure AD DS felügyelt tartományhoz, a tartomány felhasználói fiókjai és hitelesítő adatai használhatók a kiszolgálók bejelentkezéséhez és kezeléséhez. A felügyelt tartományból származó csoporttagságok a virtuális gépen található fájlokhoz vagy szolgáltatásokhoz való hozzáférés szabályozására is vonatkoznak.

Ez a cikk bemutatja, hogyan csatlakozhat egy Red Hat Enterprise Linux (RHEL) virtuális géphez egy felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services felügyelt tartományt hoz létre és konfigurál][create-azure-ad-ds-instance].
* A felügyelt tartomány részét képező felhasználói fiók.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>RHEL Linux rendszerű virtuális gép létrehozása és kapcsolódás

Ha rendelkezik egy meglévő RHEL Linux virtuális géppel az Azure-ban, csatlakozzon az SSH-val, majd folytassa a következő lépéssel a [virtuális gép konfigurálásának megkezdéséhez](#configure-the-hosts-file).

Ha létre kell hoznia egy RHEL Linux rendszerű virtuális gépet, vagy létre szeretne hozni egy tesztelési virtuális gépet, amely a jelen cikkben használható, a következő módszerek egyikét használhatja:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

A virtuális gép létrehozásakor ügyeljen arra, hogy a virtuális hálózati beállításokkal ellenőrizze, hogy a virtuális gép tud-e kommunikálni a felügyelt tartománnyal:

* Telepítse a virtuális gépet ugyanabba vagy egy olyan virtuális hálózatba, amelyben engedélyezte a Azure AD Domain Services.
* Telepítse a virtuális gépet egy másik alhálózatra, mint a Azure AD Domain Services felügyelt tartománya.

A virtuális gép üzembe helyezését követően hajtsa végre a lépéseket, hogy SSH-kapcsolaton keresztül csatlakozzon a virtuális géphez.

## <a name="configure-the-hosts-file"></a>A Hosts fájl konfigurálása

Győződjön meg arról, hogy a virtuális gép állomásneve helyesen van konfigurálva a felügyelt tartományhoz, szerkessze a */etc/hosts* fájlt, és állítsa be a hostname:

```console
sudo vi /etc/hosts
```

A *gazdagépek* fájlban frissítse a *localhost* -címeket. A következő példában:

* a *aaddscontoso.com* a felügyelt tartomány DNS-tartományneve.
* a *RHEL* a felügyelt tartományhoz csatlakozó RHEL-alapú virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Ha elkészült, mentse és zárja be a *hosts* fájlt a `:wq` szerkesztő parancs használatával.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

A virtuális gépnek szüksége van néhány további csomagra a virtuális géphez a felügyelt tartományhoz való csatlakozáshoz. A csomagok telepítéséhez és konfigurálásához frissítse és telepítse a tartományhoz csatlakozó eszközöket a használatával `yum` . A 7. x és a RHEL 6. x RHEL között van néhány különbség, ezért a jelen cikk további részeiben használja a distro verziójának megfelelő parancsait.

**7. RHEL**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**6. RHEL**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz

Most, hogy a szükséges csomagok telepítve vannak a virtuális gépen, csatlakoztassa a virtuális gépet a felügyelt tartományhoz. Ismételje meg a megfelelő lépéseket a RHEL distro-verziójának használatához.

### <a name="rhel-7"></a>7. RHEL

1. A `realm discover` parancs használatával keresse meg a felügyelt tartományt. A következő példa felfedi a *AADDSCONTOSO.com* tartományát. Adja meg saját felügyelt tartománynevét az összes nagybetűvel:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Ha a `realm discover` parancs nem találja a felügyelt tartományt, tekintse át a következő hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja meg `ping aaddscontoso.com` megtekinteni, hogy a rendszer pozitív választ ad-e vissza.
    * Győződjön meg arról, hogy a virtuális gép telepítve van az azonos vagy egy olyan, a virtuális hálózatban, amelyben a felügyelt tartomány elérhető.
    * Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissítve lettek, hogy a felügyelt tartomány tartományvezérlőinek felé mutassanak.

1. Most inicializálja a Kerberost a `kinit` parancs használatával. A felügyelt tartomány részét képező felhasználót kell megadnia. Ha szükséges, [vegyen fel egy felhasználói fiókot egy csoportba az Azure ad-ben](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    A felügyelt tartománynevet is meg kell adni az összes nagybetűs mezőben. A következő példában a nevű fiók a `contosoadmin@aaddscontoso.com` Kerberos inicializálására szolgál. Adja meg a felügyelt tartomány részét képező saját felhasználói fiókot:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Végül csatlakoztassa a virtuális gépet a felügyelt tartományhoz a `realm join` parancs használatával. Ugyanazt a felhasználói fiókot használja, mint amely az előző parancsban megadott felügyelt tartomány része `kinit` , például `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

A virtuális gép a felügyelt tartományhoz való csatlakoztatása néhány percet vesz igénybe. A következő példa kimenete azt mutatja, hogy a virtuális gép sikeresen csatlakozott a felügyelt tartományhoz:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>6. RHEL

1. A `adcli info` parancs használatával keresse meg a felügyelt tartományt. A következő példa felfedi a *ADDDSCONTOSO.com* tartományát. Adja meg saját felügyelt tartománynevét az összes nagybetűvel:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Ha a `adcli info` parancs nem találja a felügyelt tartományt, tekintse át a következő hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja meg `ping aaddscontoso.com` megtekinteni, hogy a rendszer pozitív választ ad-e vissza.
    * Győződjön meg arról, hogy a virtuális gép telepítve van az azonos vagy egy olyan, a virtuális hálózatban, amelyben a felügyelt tartomány elérhető.
    * Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissítve lettek, hogy a felügyelt tartomány tartományvezérlőinek felé mutassanak.

1. Először is csatlakozzon a tartományhoz a `adcli join` paranccsal, ezzel a paranccsal a számítógép hitelesítésére is létrejön a keytab kiterjesztésű. Olyan felhasználói fiókot használjon, amely a felügyelt tartomány részét képezi.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Most konfigurálja a `/ect/krb5.conf` és hozza létre a `/etc/sssd/sssd.conf` fájlokat a `aaddscontoso.com` Active Directory tartomány használatához.
   Győződjön meg arról, hogy `AADDSCONTOSO.COM` a helyébe a saját tartománynév van lecserélve:

    Nyissa meg a `/ect/krb5.conf` fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Frissítse a `krb5.conf` fájlt, hogy az megfeleljen a következő mintának:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Hozza létre a `/etc/sssd/sssd.conf` fájlt:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Frissítse a `sssd.conf` fájlt, hogy az megfeleljen a következő mintának:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Győződjön meg arról, hogy `/etc/sssd/sssd.conf` az engedélyek 600, és a root felhasználó tulajdonában van:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. A használatával `authconfig` utasíthatja a virtuális gépet az ad Linux-integrációra:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. A sssd szolgáltatás elindítása és engedélyezése:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Ha a virtuális gép nem tudja sikeresen befejezni a tartományhoz való csatlakozás folyamatát, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat a 464-as TCP + UDP-porton a felügyelt tartományhoz tartozó virtuális hálózati alhálózatra.

Most ellenőrizze, hogy le tudja-e kérdezni a felhasználói AD-adatokat a `getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Jelszó-hitelesítés engedélyezése SSH-hoz

Alapértelmezés szerint a felhasználók csak az SSH nyilvános kulcs-alapú hitelesítés használatával jelentkezhetnek be egy virtuális gépre. A jelszó-alapú hitelesítés meghiúsul. Ha egy felügyelt tartományhoz csatlakoztatja a virtuális gépet, akkor ezeknek a tartományi fiókoknak jelszó alapú hitelesítést kell használniuk. Frissítse az SSH-konfigurációt a jelszó alapú hitelesítés engedélyezéséhez az alábbiak szerint.

1. Nyissa meg a *sshd_conf* fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. A *PasswordAuthentication* vonalának frissítése *Igen* értékre:

    ```console
    PasswordAuthentication yes
    ```

    Ha elkészült, mentse és zárja be a *sshd_conf* fájlt a `:wq` szerkesztő parancsával.

1. A módosítások alkalmazásához és a felhasználók jelszóval való beléptetéséhez indítsa újra az SSH-szolgáltatást a RHEL-disztribúció verziójához:

   **7. RHEL**

    ```console
    sudo systemctl restart sshd
    ```

   **6. RHEL**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "HRE DC Administrators" csoport sudo-jogosultságának megadása

Ahhoz, hogy a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai számára rendszergazdai jogosultságokat lehessen adni a RHEL virtuális gépen, hozzá kell adnia egy bejegyzést a */etc/sudoers*. A Hozzáadás után a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai használhatják a `sudo` parancsot a RHEL virtuális gépen.

1. Nyissa meg a *sudoers* fájlt a szerkesztéshez:

    ```console
    sudo visudo
    ```

1. Adja hozzá a következő bejegyzést a */etc/sudoers* fájl végéhez. A *HRE DC-rendszergazdák* csoport szóközt tartalmaz a névben, így a csoport neve tartalmazza a fordított perjel karaktert. Adja hozzá a saját tartománynevét, például *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Ha elkészült, mentse és zárja be a szerkesztőt a `:wq` szerkesztő parancsának használatával.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépre tartományi fiók használatával

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e a felügyelt tartományhoz, indítson el egy új SSH-kapcsolódást egy tartományi felhasználói fiók használatával. Győződjön meg arról, hogy a kezdőkönyvtár létrejött, és a rendszer a tartományból származó csoporttagság alkalmazását alkalmazza.

1. Hozzon létre egy új SSH-kapcsolatokat a konzolon. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik a `ssh -l` parancs használatával, például:, `contosoadmin@aaddscontoso.com` majd adja meg a virtuális gép (például *RHEL.aaddscontoso.com*) címeit. Ha a Azure Cloud Shell használja, a belső DNS-név helyett használja a virtuális gép nyilvános IP-címét.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Ha sikeresen csatlakozott a virtuális géphez, ellenőrizze, hogy helyesen lett-e inicializálva a kezdőkönyvtár:

    ```console
    pwd
    ```

    A */Home* könyvtárban kell lennie a saját címtárával, amely megfelel a felhasználói fióknak.

1. Most győződjön meg arról, hogy a csoporttagságok megfelelően vannak feloldva:

    ```console
    id
    ```

    A csoport tagságát a felügyelt tartományból kell látnia.

1. Ha a *HRE DC-rendszergazdák* csoport tagjaként jelentkezett be a virtuális gépre, ellenőrizze, hogy helyesen használja-e a következő `sudo` parancsot:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Következő lépések

Ha problémába ütközik a virtuális gép a felügyelt tartományhoz való csatlakoztatásával vagy egy tartományi fiókkal való bejelentkezéssel, tekintse meg a [tartományhoz való csatlakozással kapcsolatos problémák elhárítása](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
