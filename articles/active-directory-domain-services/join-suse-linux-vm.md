---
title: SLE-es virtuális gép csatlakoztatása Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és csatlakozhat egy SUSE Linux Enterprise rendszerű virtuális géphez egy Azure AD Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619606"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>SUSE Linux Enterprise rendszerű virtuális gép csatlakoztatása Azure Active Directory Domain Services felügyelt tartományhoz

Annak érdekében, hogy a felhasználók egyetlen hitelesítő adat használatával jelentkezzenek be a virtuális gépekre az Azure-ban, csatlakoztathatja a virtuális gépeket egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományhoz. Amikor egy virtuális gépet csatlakoztat egy Azure AD DS felügyelt tartományhoz, a tartomány felhasználói fiókjai és hitelesítő adatai használhatók a kiszolgálók bejelentkezéséhez és kezeléséhez. A felügyelt tartományból származó csoporttagságok a virtuális gépen található fájlokhoz vagy szolgáltatásokhoz való hozzáférés szabályozására is vonatkoznak.

Ez a cikk bemutatja, hogyan csatlakozhat egy SUSE Linux Enterprise (SLE) virtuális géphez egy felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services felügyelt tartományt hoz létre és konfigurál][create-azure-ad-ds-instance].
* A felügyelt tartomány részét képező felhasználói fiók.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>SLE Linux rendszerű virtuális gép létrehozása és kapcsolódás

Ha van egy meglévő SLE Linux rendszerű virtuális gép az Azure-ban, csatlakozzon hozzá az SSH-val, majd folytassa a következő lépéssel a [virtuális gép konfigurálásának megkezdéséhez](#configure-the-hosts-file).

Ha egy SLE Linux rendszerű virtuális gépet kell létrehoznia, vagy létre szeretne hozni egy tesztelési virtuális gépet a jelen cikk használatával, a következő módszerek egyikét használhatja:

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
* a *Linux-q2gr* a felügyelt tartományhoz csatlakozó SLE-es virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Ha elkészült, mentse és zárja be a *hosts* fájlt a `:wq` szerkesztő parancs használatával.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz a SSSD használatával

A felügyelt tartomány **SSSD** és a YaST *felhasználói bejelentkezés kezelési* moduljának használatával történő csatlakoztatásához hajtsa végre a következő lépéseket:

1. Telepítse a *felhasználói bejelentkezési felügyelet* YaST modulját:

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Nyissa meg a YaST.

1. Ha később szeretné használni a DNS automatikus észlelését, konfigurálja a felügyelt tartomány IP-címeit (a *Active Directory-kiszolgálót*) az ügyfél neveként.

    A YaST területen válassza a **System > hálózati beállítások** elemet.

1. Válassza ki a *hostname/DNS* fület, majd adja meg a felügyelt tartomány IP-címeit a (z) *1. kiszolgáló neve* mezőbe. Ezek az IP-címek a felügyelt tartomány Azure Portal *Tulajdonságok* ablakában jelennek meg, például a *10.0.2.4* és a *végpontjául szolgáló*.

    Adja hozzá saját felügyelt tartománya IP-címeit, majd kattintson **az OK gombra**.

1. A YaST főablakában válassza a *Network Services*  >  *felhasználói bejelentkezés kezelése* lehetőséget.

    A modul egy olyan áttekintéssel nyílik meg, amely a számítógép különböző hálózati tulajdonságait és a jelenleg használatban lévő hitelesítési módszert mutatja be, ahogyan az a következő képernyőképen látható:

    ![Példa a YaST felhasználói bejelentkezés kezelése ablakának képernyőképére](./media/join-suse-linux-vm/overview-window.png)

    A Szerkesztés megkezdéséhez válassza a **beállítások módosítása** lehetőséget.

Ha a virtuális gépet a felügyelt tartományhoz szeretné csatlakoztatni, hajtsa végre a következő lépéseket:

1. A párbeszédpanelen válassza a **tartomány hozzáadása** elemet.

1. Adja meg a helyes *tartománynevet*(például *aaddscontoso.com*), majd adja meg az identitási adatokhoz és a hitelesítéshez használandó szolgáltatásokat. Mindkettőhöz válassza a *Microsoft Active Directory* lehetőséget.

    Győződjön meg arról, hogy a *tartomány engedélyezése* lehetőség van kiválasztva.

1. Ha elkészült, kattintson **az OK gombra**.

1. Fogadja el az alapértelmezett beállításokat a következő párbeszédpanelen, majd kattintson az **OK gombra**.

1. A virtuális gép szükség szerint telepít további szoftvereket, majd ellenőrzi, hogy elérhető-e a felügyelt tartomány.

    Ha minden helyes, a következő példa párbeszédablak jelzi, hogy a virtuális gép felderítte a felügyelt tartományt, de *még nincs regisztrálva*.

    ![Példa a YaST Active Directory beléptetési ablakának képernyőképére](./media/join-suse-linux-vm/enroll-window.png)

1. A párbeszédpanelen a felügyelt tartomány részét képező felhasználó *felhasználónevét* és *jelszavát* kell megadnia. Ha szükséges, [vegyen fel egy felhasználói fiókot egy csoportba az Azure ad-ben](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Annak ellenőrzéséhez, hogy az aktuális tartomány engedélyezve van-e a Samba számára, aktiválja a *Samba-konfiguráció felülírása lehetőséget az ad-vel való együttműködéshez*.

1. A regisztráláshoz kattintson **az OK gombra**.

1. Megjelenik egy üzenet, amely megerősíti, hogy sikeresen bejelentkezett. A befejezéshez kattintson **az OK gombra**.

Miután a virtuális gépet regisztrálta a felügyelt tartományba, konfigurálja az ügyfelet a *tartomány felhasználói bejelentkezésének kezelése* paranccsal, ahogy az a következő példában látható:

![Példa a tartomány felhasználói bejelentkezési ablakának kezelése a YaST-ben – képernyőkép](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. A felügyelt tartomány által biztosított adatokat használó bejelentkezések engedélyezéséhez jelölje be a *tartomány felhasználói bejelentkezésének engedélyezése* jelölőnégyzetet.

1. Igény szerint a *tartomány adatforrásának engedélyezése* területen szükség szerint további adatforrásokat is megadhat a környezetéhez. Ezekkel a lehetőségekkel megadhatja, hogy mely felhasználók használhatják a **sudo** -t, vagy hogy mely hálózati meghajtók érhetők el.

1. Ha engedélyezni szeretné, hogy a felügyelt tartomány felhasználói rendelkezzenek a virtuális gépen lévő otthoni könyvtárakkal, jelölje be a *Kezdőlap könyvtárak létrehozása* jelölőnégyzetet.

1. Az oldalsó sávban válassza a **szolgáltatási beállítások > név kapcsoló**, majd a *kiterjesztett beállítások* elemet. Ebből az ablakból válassza a *fallback_homedir* vagy a *override_homedir* lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

1. A kezdőkönyvtár helyének értékét kell megadnia. Ha szeretné, hogy a kezdőkönyvtár */home/user_name*, használja a */Home/%u*. További információ a lehetséges változókról: sssd. conf Man Page ( `man 5 sssd.conf` ), szakasz *override_homedir*.

1. Válassza az **OK** lehetőséget.

1. A módosítások mentéséhez válassza az **OK** gombot. Ezután győződjön meg arról, hogy a megjelenített értékek helyesek. A párbeszédpanel elhagyásához válassza a **Mégse** lehetőséget.

1. Ha a SSSD és a winbind párhuzamosan kívánja futtatni (például a SSSD-on keresztüli csatlakozáshoz, majd egy Samba-fájlkiszolgáló futtatásához), akkor a rendszer az SMB. conf-ban a "Samba" *Kerberos-módszert* kell beállítania az " *keytab kiterjesztésű* " értékre. Az SSSD *ad_update_samba_machine_account_password* kapcsolót a SSSD. conf fájlban is *igaz* értékre kell állítani. Ezekkel a beállításokkal megakadályozható, hogy a rendszer keytab kiterjesztésű a szinkronizálást.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz a winbind használatával

A felügyelt tartomány a **winbind** és a YaST *Windows-tartomány tagsági* moduljának használatával való csatlakoztatásához hajtsa végre a következő lépéseket:

1. A YaST területen válassza a **Network Services > Windows-tartomány tagság** lehetőséget.

1. Adja meg a tartományhoz *vagy munkacsoporthoz* való csatlakozáshoz használandó tartományt a *Windows-tartomány tagsági* képernyőjén. Adja meg a felügyelt tartománynevet, például a *aaddscontoso.com* nevet.

    ![Példa a YaST Windows-tartomány tagsági ablakának képernyőképére](./media/join-suse-linux-vm/samba-client-window.png)

1. Az SMB-forrás Linux-hitelesítéshez való használatához használja az *SMB-információk használata Linux-hitelesítéshez* beállítást.

1. A virtuális gépen lévő felügyelt tartományi felhasználók számára a helyi kezdőkönyvtár automatikus létrehozásához a *bejelentkezéshez a kezdőkönyvtár létrehozása* lehetőséget kell bejelölni.

1. Ha a felügyelt tartomány átmenetileg nem érhető el, akkor ellenőrizze az *offline hitelesítés* beállítását, hogy a tartomány felhasználói bejelentkezzenek-e.

1. Ha módosítani szeretné a Samba-felhasználók és-csoportok UID-és GID-tartományait, válassza a *szakértői beállítások* lehetőséget.

1. Konfigurálja a hálózati idő protokoll (NTP) időszinkronizálását a felügyelt tartományhoz az *NTP-konfiguráció* kiválasztásával. Adja meg a felügyelt tartomány IP-címeit. Ezek az IP-címek a felügyelt tartomány Azure Portal *Tulajdonságok* ablakában jelennek meg, például a *10.0.2.4* és a *végpontjául szolgáló*.

1. Kattintson az **OK gombra** , és erősítse meg a tartományhoz való csatlakozást, ha a rendszer erre kéri.

1. Adja meg a rendszergazda jelszavát a felügyelt tartományban, és kattintson az **OK gombra**.

    ![Példa a hitelesítési párbeszédpanelre, amikor egy SLE virtuális gépet csatlakoztat a felügyelt tartományhoz](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

A felügyelt tartományhoz való csatlakozást követően bejelentkezhet a munkaállomásról az asztal vagy a konzol Display Manager használatával.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz a winbind használatával a YaST parancssori felületéről

A felügyelt tartomány csatlakoztatása a **winbind** és a *YaST parancssori felület* használatával:

* Csatlakozás a tartományhoz:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz a winbind használatával a terminálról

A felügyelt tartomány csatlakoztatása a **winbind** és a *`samba net` parancs* használatával:

1. A Kerberos-ügyfél és a Samba-winbind telepítése:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. A konfigurációs fájlok szerkesztése:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf állományt
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/Security/pam_winbind. conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Győződjön meg arról, hogy az Azure AD-ben és a Linux-ban lévő dátum és idő szinkronban van. Ezt úgy teheti meg, hogy hozzáadja az Azure AD-kiszolgálót az NTP szolgáltatáshoz:
   
   1. Adja hozzá a következő sort a/etc/ntp.conf:
     
      ```console
      server aaddscontoso.com
      ```

   1. Indítsa újra az NTP szolgáltatást:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Csatlakozás a tartományhoz:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. A winbind engedélyezése bejelentkezési forrásként a Linux rendszerű, csatlakoztatható hitelesítési modulokban (PAM):

   ```console
   pam-config --add --winbind
   ```

6. Engedélyezze a Kezdőlap könyvtárak automatikus létrehozását, hogy a felhasználók bejelentkezhetnek:

   ```console
   pam-config -a --mkhomedir
   ```

7. A winbind szolgáltatás elindítása és engedélyezése:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
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

1. A módosítások alkalmazásához és a felhasználók jelszóval való bejelentkezéséhez indítsa újra az SSH-szolgáltatást:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "HRE DC Administrators" csoport sudo-jogosultságának megadása

Ha a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai számára rendszergazdai jogosultságokat kíván adni az SLE virtuális gépen, adjon hozzá egy bejegyzést a */etc/sudoers*. A Hozzáadás után a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai HASZNÁLHATJÁK az `sudo` SLE virtuális gépen található parancsot.

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

1. Hozzon létre egy új SSH-kapcsolatokat a konzolon. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik a `ssh -l` parancs használatával, például:, `contosoadmin@aaddscontoso.com` majd adja meg a virtuális gép (például *Linux-q2gr.aaddscontoso.com*) címeit. Ha a Azure Cloud Shell használja, a belső DNS-név helyett használja a virtuális gép nyilvános IP-címét.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Ha sikeresen csatlakozott a virtuális géphez, ellenőrizze, hogy helyesen lett-e inicializálva a kezdőkönyvtár:

    ```console
    pwd
    ```

    A */Home* könyvtárban kell lennie a saját címtárával, amely megfelel a felhasználói fióknak.

3. Most győződjön meg arról, hogy a csoporttagságok megfelelően vannak feloldva:

    ```console
    id
    ```

    A csoport tagságát a felügyelt tartományból kell látnia.

4. Ha a *HRE DC-rendszergazdák* csoport tagjaként jelentkezett be a virtuális gépre, ellenőrizze, hogy helyesen használja-e a következő `sudo` parancsot:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Következő lépések

Ha problémába ütközik a virtuális gép a felügyelt tartományhoz való csatlakoztatásával vagy egy tartományi fiókkal való bejelentkezéssel, tekintse meg a [tartományhoz való csatlakozással kapcsolatos problémák elhárítása](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
