---
title: Ubuntu-alapú virtuális gép csatlakoztatása Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és csatlakoztathat egy Ubuntu Linux virtuális gépet egy Azure AD Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.custom: fasttrack-edit
ms.openlocfilehash: 8b6d022b9a1f3be70f69943a53754c9dd909ca99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619487"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ubuntu Linux virtuális gép csatlakoztatása Azure Active Directory Domain Services felügyelt tartományhoz

Annak érdekében, hogy a felhasználók egyetlen hitelesítő adat használatával jelentkezzenek be a virtuális gépekre az Azure-ban, csatlakoztathatja a virtuális gépeket egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományhoz. Amikor egy virtuális gépet csatlakoztat egy Azure AD DS felügyelt tartományhoz, a tartomány felhasználói fiókjai és hitelesítő adatai használhatók a kiszolgálók bejelentkezéséhez és kezeléséhez. A felügyelt tartományból származó csoporttagságok a virtuális gépen található fájlokhoz vagy szolgáltatásokhoz való hozzáférés szabályozására is vonatkoznak.

Ez a cikk bemutatja, hogyan csatlakozhat egy Ubuntu Linux virtuális géphez egy felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services felügyelt tartományt hoz létre és konfigurál][create-azure-ad-ds-instance].
* A felügyelt tartomány részét képező felhasználói fiók.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Ubuntu Linux virtuális gép létrehozása és kapcsolódás

Ha rendelkezik egy meglévő Ubuntu Linux virtuális géppel az Azure-ban, csatlakozzon az SSH-val, majd folytassa a következő lépéssel a [virtuális gép konfigurálásának megkezdéséhez](#configure-the-hosts-file).

Ha létre kell hoznia egy Ubuntu Linux virtuális gépet, vagy létre szeretne hozni egy tesztelési virtuális gépet, amely a jelen cikkben használható, a következő módszerek egyikét használhatja:

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
* az *Ubuntu* a felügyelt tartományhoz csatlakozó Ubuntu-alapú virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Ha elkészült, mentse és zárja be a *hosts* fájlt a `:wq` szerkesztő parancs használatával.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

A virtuális gépnek szüksége van néhány további csomagra a virtuális géphez a felügyelt tartományhoz való csatlakozáshoz. A csomagok telepítéséhez és konfigurálásához frissítse és telepítse a tartományhoz csatlakozó eszközöket a következő használatával: `apt-get`

A Kerberos telepítésekor a *krb5* csomag minden nagybetűvel kéri a tartománynevet. Ha például a felügyelt tartomány neve *aaddscontoso.com*, adja meg a *AADDSCONTOSO.com* tartományt. A telepítés a `[realm]` és a `[domain_realm]` szakaszt a */etc/krb5.conf állományt* konfigurációs fájlba írja. Győződjön meg arról, hogy a tartomány minden nagybetűvel rendelkezik:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>A Network Time Protocol (NTP) konfigurálása

Ahhoz, hogy a tartományi kommunikáció megfelelően működjön, az Ubuntu-alapú virtuális gép dátumának és időpontjának szinkronizálnia kell a felügyelt tartománnyal. Adja hozzá a felügyelt tartomány NTP-állomásnevét a */etc/ntp.conf* -fájlhoz.

1. Nyissa meg az *NTP. conf* fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. Az *NTP. conf* fájlban hozzon létre egy sort a felügyelt tartomány DNS-nevének hozzáadásához. A következő példában egy *aaddscontoso.com* bejegyzést adnak hozzá. Saját DNS-név használata:

    ```console
    server aaddscontoso.com
    ```

    Ha elkészült, mentse és zárja be az *NTP. conf* fájlt a `:wq` szerkesztő parancsának használatával.

1. A következő lépések szükségesek annak biztosításához, hogy a virtuális gép szinkronizálva legyen a felügyelt tartománnyal:

    * Az NTP-kiszolgáló leállítása
    * A dátum és idő frissítése a felügyelt tartományból
    * Az NTP szolgáltatás elindítása

    Futtassa a következő parancsokat a lépések végrehajtásához. Használja a saját DNS-nevét a `ntpdate` paranccsal:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz

Most, hogy a szükséges csomagok telepítve vannak a virtuális gépen, és az NTP konfigurálva van, csatlakoztassa a virtuális gépet a felügyelt tartományhoz.

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
    kinit -V contosoadmin@AADDSCONTOSO.COM
    ```

1. Végül csatlakoztassa a virtuális gépet a felügyelt tartományhoz a `realm join` parancs használatával. Ugyanazt a felhasználói fiókot használja, mint amely az előző parancsban megadott felügyelt tartomány része `kinit` , például `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

A virtuális gép a felügyelt tartományhoz való csatlakoztatása néhány percet vesz igénybe. A következő példa kimenete azt mutatja, hogy a virtuális gép sikeresen csatlakozott a felügyelt tartományhoz:

```output
Successfully enrolled machine in realm
```

Ha a virtuális gép nem tudja sikeresen befejezni a tartományhoz való csatlakozás folyamatát, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat a 464-as TCP + UDP-porton a felügyelt tartományhoz tartozó virtuális hálózati alhálózatra.

Ha a hiba *meghatározatlan GSS sikertelen volt.  Előfordulhat, hogy a másodlagos kód további információkat tartalmaz (a kiszolgáló nem található a Kerberos-adatbázisban)*, nyissa meg a */etc/krb5.conf állományt* fájlt, és adja hozzá a következő kódot a `[libdefaults]` szakaszhoz, és próbálkozzon újra:

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>A SSSD konfigurációjának frissítése

Az előző lépésben telepített csomagok egyike a System Security Services Daemon (SSSD) volt. Amikor egy felhasználó tartományi hitelesítő adatokkal próbál bejelentkezni egy virtuális gépre, a SSSD továbbítja a kérést egy hitelesítési szolgáltatónak. Ebben az esetben a SSSD az Azure AD DS használatával hitelesíti a kérést.

1. Nyissa meg a *sssd. conf* fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Tegye megjegyzésbe az *use_fully_qualified_names* sorát a következők szerint:

    ```console
    # use_fully_qualified_names = True
    ```

    Ha elkészült, mentse és zárja be a *sssd. conf* fájlt a `:wq` szerkesztő parancsának használatával.

1. A módosítás alkalmazásához indítsa újra a SSSD szolgáltatást:

    ```console
    sudo systemctl restart sssd
    ```

## <a name="configure-user-account-and-group-settings"></a>A felhasználói fiók és a csoport beállításainak konfigurálása

Ha a virtuális gép a felügyelt tartományhoz van csatlakoztatva, és a hitelesítésre van konfigurálva, néhány felhasználói konfigurációs lehetőség is befejeződik. A konfigurációs változások közé tartozik a jelszó-alapú hitelesítés engedélyezése, valamint a helyi virtuális gépen lévő otthoni könyvtárak automatikus létrehozása, amikor a tartományi felhasználók először jelentkeznek be.

### <a name="allow-password-authentication-for-ssh"></a>Jelszó-hitelesítés engedélyezése SSH-hoz

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

1. A módosítások alkalmazásához és a felhasználók jelszóval való bejelentkezéséhez indítsa újra az SSH-szolgáltatást:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Az automatikus kezdőkönyvtár létrehozásának konfigurálása

Ha engedélyezni szeretné a kezdőkönyvtár automatikus létrehozását, amikor a felhasználó először jelentkezik be, hajtsa végre a következő lépéseket:

1. Nyissa meg a */etc/pam.d/Common-Session* fájlt egy szerkesztőben:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Adja hozzá a következő sort a fájlhoz a sor alatt `session optional pam_sss.so` :

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Ha elkészült, mentse és zárja be a *Common-Session* fájlt a `:wq` szerkesztő parancs használatával.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "HRE DC Administrators" csoport sudo-jogosultságának megadása

Ha a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai számára rendszergazdai jogosultságokat kíván adni az Ubuntu virtuális gépen, vegyen fel egy bejegyzést a */etc/sudoers*. A hozzáadást követően a *HRE DC-rendszergazdák* csoport tagjai az `sudo` Ubuntu virtuális gépen található parancsot használhatják.

1. Nyissa meg a *sudoers* fájlt a szerkesztéshez:

    ```console
    sudo visudo
    ```

1. Adja hozzá a következő bejegyzést a */etc/sudoers* fájl végéhez:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Ha elkészült, mentse és zárja be a szerkesztőt a `Ctrl-X` parancs használatával.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépre tartományi fiók használatával

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e a felügyelt tartományhoz, indítson el egy új SSH-kapcsolódást egy tartományi felhasználói fiók használatával. Győződjön meg arról, hogy a kezdőkönyvtár létrejött, és a rendszer a tartományból származó csoporttagság alkalmazását alkalmazza.

1. Hozzon létre egy új SSH-kapcsolatokat a konzolon. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik a `ssh -l` parancs használatával, például:, `contosoadmin@aaddscontoso.com` majd adja meg a virtuális gép (például *Ubuntu.aaddscontoso.com*) címeit. Ha a Azure Cloud Shell használja, a belső DNS-név helyett használja a virtuális gép nyilvános IP-címét.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>Következő lépések

Ha problémába ütközik a virtuális gép a felügyelt tartományhoz való csatlakoztatásával vagy egy tartományi fiókkal való bejelentkezéssel, tekintse meg a [tartományhoz való csatlakozással kapcsolatos problémák elhárítása](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
