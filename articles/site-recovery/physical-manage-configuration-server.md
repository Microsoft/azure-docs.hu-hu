---
title: A Azure Site Recovery-beli fizikai kiszolgálók konfigurációs kiszolgálójának kezelése
description: Ez a cikk azt ismerteti, hogyan kezelhető a fizikai kiszolgáló vész-helyreállításának Azure Site Recovery konfigurációs kiszolgálója az Azure-ba.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017440"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>A fizikai kiszolgáló vész-helyreállítási konfigurációs kiszolgálójának kezelése

Ha a [Azure site Recovery](site-recovery-overview.md) szolgáltatást használja a fizikai kiszolgálók Azure-ba történő vész-helyreállításához, be kell állítania egy helyszíni konfigurációs kiszolgálót. A konfigurációs kiszolgáló koordinálja a helyszíni gépek és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. Ez a cikk összefoglalja a konfigurációs kiszolgáló üzembe helyezése után történő felügyeletének gyakori feladatait.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A táblázat összefoglalja a helyszíni konfigurációs kiszolgáló számítógépének üzembe helyezésének előfeltételeit.

| **Összetevő** | **Követelmény** |
| --- |---|
| Processzormagok| 8 |
| RAM | 16 GB|
| Lemezek száma | 3, beleértve az operációsrendszer-lemezt, a feldolgozási kiszolgáló gyorsítótárának lemezét és a feladat-visszavételi meghajtót |
| – Lemez szabad területe (Folyamatkiszolgálói gyorsítótár) | 600 GB
| Lemez szabad területe (adatmegőrzési lemez) | 600 GB|
| Operációs rendszer  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Operációs rendszer területi beállítása | Angol (US)|
| VMware vSphere PowerCLI verziója | Nem szükséges|
| Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
| Csoportházirendek| Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssorhoz való hozzáférés letiltása <br> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása <br> – A fájlmellékletek megbízhatósági logikája <br> – A parancsfájlok végrehajtásának bekapcsolása <br> [További információ](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | – Nincs előre meglévő alapértelmezett webhely <br> – [Névtelen hitelesítés](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) engedélyezése <br> – [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -beállítás engedélyezése  <br> – Nincs már meglévő webhely/alkalmazás a 443-es porton<br>|
| Hálózati adapter típusa | VMXNET3 (VMware virtuális gépként való üzembe helyezéskor) |
| IP-cím típusa | Statikus |
| Internet-hozzáférés | A kiszolgálónak hozzá kell férnie az alábbi URL-címekhez: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> -*. services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (a kibővíthető folyamat-kiszolgálók esetében nem szükséges) <br> - time.nist.gov <br> - time.windows.com |
| Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)|

## <a name="download-the-latest-installation-file"></a>A legújabb telepítési fájl letöltése

A konfigurációs kiszolgáló telepítési fájljának legújabb verziója a Site Recovery portálon érhető el. Emellett közvetlenül a [Microsoft letöltőközpontból](https://aka.ms/unifiedsetup)is letölthető.

1. Jelentkezzen be a Azure Portalba, és keresse meg a Recovery Services-tárolót.
2. Keresse meg **site Recovery infrastruktúra**  >  -**konfigurációs kiszolgálókat** (a VMware & fizikai gépeknél).
3. Kattintson a **+ kiszolgálók** gombra.
4. A **kiszolgáló hozzáadása** lapon kattintson a letöltés gombra a regisztrációs kulcs letöltéséhez. Erre a kulcsra szüksége lesz a konfigurációs kiszolgáló telepítése során a Azure Site Recovery szolgáltatásban való regisztráláshoz.
5. A konfigurációs kiszolgáló legújabb verziójának letöltéséhez kattintson a **Microsoft Azure site Recovery egyesített telepítő** hivatkozására.

   ![Letöltési oldal](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>A kiszolgáló telepítése és regisztrálása

1. Futtassa az egyesített telepítő fájlját.
2. A **Kezdés előtt** válassza **a konfigurációs kiszolgáló és a Process Server telepítése** lehetőséget.

    ![Előkészületek](./media/physical-manage-configuration-server/combined-wiz1.png)

3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.
4. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül. Győződjön meg arról, hogy engedélyezte a szükséges URL-címeket.

    - Ha a számítógépen aktuálisan beállított proxyval szeretne csatlakozni, válassza a **csatlakozás Azure site Recovery proxykiszolgálón keresztül** lehetőséget.
    - Ha azt szeretné, hogy a szolgáltató közvetlenül kapcsolódjon, válassza a **közvetlen csatlakozás Azure site Recovery proxykiszolgáló nélkül** lehetőséget.
    - Ha a meglévő proxy hitelesítést igényel, illetve ha egyéni proxyt szeretne használni a szolgáltatói kapcsolathoz, válassza a **Csatlakozás egyéni proxybeállítások** alapján lehetőséget, és adja meg a címeket, a portokat és a hitelesítő adatokat.
     ![Tűzfal](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Előfeltételek](./media/physical-manage-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ha Ön, a telepítő ellenőrzi, hogy a PowerCLI 6,0 telepítve van-e.
9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Telepítési hely](./media/physical-manage-configuration-server/combined-wiz8.png)
10. A **hálózat kiválasztása** területen válassza ki azt a hálózati adaptert, amelyet a beépített Process Server használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásoldali gépeken, majd válassza ki azt a hálózati adaptert, amelyet az Azure-hoz való kapcsolódáshoz használ a konfigurációs kiszolgáló. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. Ne használja a 443-es portot a replikációs forgalom küldésére és fogadására.

    ![Hálózat kiválasztása](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.


A regisztráció befejeződése után a kiszolgáló megjelenik a tároló **Beállítások**  >  **kiszolgálók** paneljén.


## <a name="install-from-the-command-line"></a>Telepítés parancssorból

Futtassa a telepítőfájlt a következőképpen:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Minta használata
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Paraméterek

|Paraméter neve| Típus | Leírás| Értékek|
|-|-|-|-|
| /ServerMode|Kötelező|Megadja, hogy a konfigurációs és folyamatkiszolgálót is, vagy csak a folyamatkiszolgálót kell-e telepíteni.|CS<br>PS|
|/InstallLocation|Kötelező|Az összetevők telepítési mappája| A számítógép bármely mappája|
|/MySQLCredsFilePath|Kötelező|A fájl elérési útja, amelyen a MySQL-kiszolgáló hitelesítő adatai tárolva vannak|A fájlnak az alább megadott formátumúnak kell lennie|
|/VaultCredsFilePath|Kötelező|A tároló hitelesítőadat-fájljának elérési útja|Érvényes fájlelérési út|
|/EnvType|Kötelező|A védelemmel ellátni kívánt környezet típusa |VMware<br>NonVMware|
|/PSIP|Kötelező|A replikációs adatátvitelhez használni kívánt hálózati adapter IP-címe| Bármilyen érvényes IP-cím|
|/CSIP|Kötelező|Annak a hálózati adapternek az IP-címe, amelyen a konfigurációs kiszolgáló figyel| Bármilyen érvényes IP-cím|
|/PassphraseFilePath|Kötelező|A jelszófájl teljes elérési útja|Érvényes fájlelérési út|
|/BypassProxy|Választható|Megadja, hogy a konfigurációs kiszolgáló proxy nélkül csatlakozik az Azure-hoz.|Az érték beszerzése innen: Venu|
|/ProxySettingsFilePath|Választható|Proxybeállítások (Az alapértelmezett proxyhoz hitelesítés vagy egyéni proxy szükséges).|A fájlnak az alább megadott formátumúnak kell lennie|
|DataTransferSecurePort|Választható|Az adatreplikációhoz használni kívánt PSIP-port száma| Érvényes portszám (az alapértelmezett érték 9433)|
|/SkipSpaceCheck|Választható|Gyorsítótárlemez terület-ellenőrzésének kihagyása| |
|/AcceptThirdpartyEULA|Kötelező|Ez a jelölő a külső féltől származó végfelhasználói licencszerződés elfogadását jelzi| |
|/ShowThirdpartyEULA|Választható|Külső felektől származó végfelhasználói licenszszerződés megjelenítése. Bemenetként való megadása esetén figyelmen kívül hagyja a többi paramétert.| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Fájl bemenetének létrehozása a MYSQLCredsFilePath

A MySQLCredsFilePath paraméter bemenetként veszi fel a fájlt. Hozza létre a fájlt a következő formátumban, és adja át bemeneti MySQLCredsFilePath paraméterként.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Fájl bemenetének létrehozása a ProxySettingsFilePath
A ProxySettingsFilePath paraméter bemenetként veszi fel a fájlt. Hozza létre a fájlt a következő formátumban, és adja át bemeneti ProxySettingsFilePath paraméterként.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Proxybeállítások módosítása

A konfigurációs kiszolgáló számítógép proxybeállításait a következőképpen módosíthatja:

1. Jelentkezzen be a konfigurációs kiszolgálóra.
2. Indítsa el a cspsconfigtool.exet az asztalon található parancsikon használatával.
3. Kattintson a tároló **regisztrációja** fülre.
4. Töltsön le egy új adattár-regisztrációs fájlt a portálról, és adja meg az eszközt bemenetként.

   ![regisztráció – konfigurációs kiszolgáló](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Adja meg az új proxy adatait, és kattintson a **regisztrálás** gombra.
6. Nyisson meg egy rendszergazdai PowerShell-parancssori ablakot.
7. Futtassa az alábbi parancsot:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Ha további, a konfigurációs kiszolgálóhoz csatlakoztatott folyamat-kiszolgálókkal rendelkezik, ki kell [javítania a proxybeállításokat a telepítésben lévő összes kibővíthető folyamat kiszolgálóján](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) .

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Konfigurációs kiszolgáló újraregisztrálása ugyanazzal a tárolóval
1. Jelentkezzen be a konfigurációs kiszolgálóra.
2. Indítsa el a cspsconfigtool.exet az asztalon található parancsikon használatával.
3. Kattintson a tároló **regisztrációja** fülre.
4. Töltsön le egy új regisztrációs fájlt a portálról, és adja meg az eszközt bemenetként.
      ![regisztráció – konfigurációs kiszolgáló](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Adja meg a proxykiszolgáló adatait, és kattintson a **regisztrálás** gombra.  
6. Nyisson meg egy rendszergazdai PowerShell-parancssori ablakot.
7. Futtassa a következő parancsot:

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Ha több Process Server-kiszolgálóval rendelkezik, akkor újra [regisztrálnia kell őket](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Konfigurációs kiszolgáló regisztrálása egy másik tárba

> [!WARNING]
> A következő lépés leállítja a konfigurációs kiszolgálót az aktuális tárolóból, és a konfigurációs kiszolgáló alatt lévő összes védett virtuális gép replikálása leállt.

1. Jelentkezzen be a konfigurációs kiszolgálóra
2. egy rendszergazdai parancssorból futtassa a következő parancsot:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Indítsa el a cspsconfigtool.exet az asztalon található parancsikon használatával.
4. Kattintson a tároló **regisztrációja** fülre.
5. Töltsön le egy új regisztrációs fájlt a portálról, és adja meg az eszközt bemenetként.
6. Adja meg a proxykiszolgáló adatait, és kattintson a **regisztrálás** gombra.  
7. Nyisson meg egy rendszergazdai PowerShell-parancssori ablakot.
8. Futtassa a következő parancsot:
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Konfigurációs kiszolgáló frissítése

A kumulatív frissítések futtatásával frissítheti a konfigurációs kiszolgálót. A frissítések akár N-4 verzióra is alkalmazhatók. Például:

- Ha 9,7, 9,8, 9,9 vagy 9,10 rendszert futtat, közvetlenül is frissíthet a 9,11-ra.
- Ha 9,6-es vagy korábbi verziót futtat, és a 9,11-re szeretne frissíteni, először frissítenie kell az 9,7-es verzióra. 9,11 előtt.

A kumulatív frissítésekre mutató hivatkozások a konfigurációs kiszolgáló összes verziójára való frissítéshez érhetők el a [wiki frissítései oldalon](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

A kiszolgálót a következőképpen frissítheti:

1. Töltse le a frissítési telepítő fájlt a konfigurációs kiszolgálóra.
2. Kattintson ide duplán a telepítő futtatásához.
3. A telepítő észleli a gépen futó aktuális verziót.
4. A megerősítéshez kattintson az **OK** gombra, majd futtassa a frissítést. 


## <a name="delete-or-unregister-a-configuration-server"></a>Konfigurációs kiszolgáló törlése vagy eltávolítása

> [!WARNING]
> A konfigurációs kiszolgáló leszerelésének megkezdése előtt győződjön meg a következőkről.
> 1. [Tiltsa le](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) az összes virtuális gép védelmét ezen a konfigurációs kiszolgálón.
> 2. Szüntesse meg az összes replikációs házirend [hozzárendelését](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és [törlését](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a konfigurációs kiszolgálóról.
> 3. [Törölje](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) az összes olyan vCenter-kiszolgálót/vSphere-gazdagépet, amely a konfigurációs kiszolgálóhoz van társítva.


### <a name="delete-the-configuration-server-from-azure-portal"></a>A konfigurációs kiszolgáló törlése Azure Portal
1. Azure Portal a tároló menüjében keresse meg **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálók** elemet.
2. Kattintson a leszerelni kívánt konfigurációs kiszolgálóra.
3. A konfigurációs kiszolgáló részletei lapon kattintson a **Törlés** gombra.
4. A kiszolgáló törlésének megerősítéséhez kattintson az **Igen** gombra.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>A konfigurációs kiszolgáló és a hozzá tartozó függőségek eltávolítása
> [!TIP]
>   Ha újra szeretné használni a konfigurációs kiszolgálót Azure Site Recovery újra, ugorjon a 4. lépésre.

1. Jelentkezzen be a konfigurációs kiszolgálóra rendszergazdaként.
2. Vezérlőpult megnyitása > program > programok eltávolítása
3. Távolítsa el a programokat a következő sorozatban:
   * Microsoft Azure Recovery Services ügynök
   * Microsoft Azure Site Recovery mobilitási szolgáltatás/fő célkiszolgáló
   * Microsoft Azure Site Recovery szolgáltató
   * Microsoft Azure Site Recovery konfigurációs kiszolgáló/folyamat kiszolgálója
   * Microsoft Azure Site Recovery konfigurációs kiszolgáló függőségei
   * MySQL-kiszolgáló 5,5
4. Futtassa a következő parancsot a és a rendszergazdai parancssorból.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Konfigurációs kiszolgáló (PowerShell) törlése vagy a regisztráció megszüntetése

1. [Telepítés](/powershell/azure/install-Az-ps) Azure PowerShell modul
2. Jelentkezzen be az Azure-fiókjába a parancs használatával
    
    `Connect-AzAccount`
3. Válassza ki azt az előfizetést, amelyben a tár megtalálható

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Most állítsa be a tár környezetét
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. A konfigurációs kiszolgáló kiválasztásának beolvasása

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. A konfigurációs kiszolgáló törlése

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> A Remove-AzSiteRecoveryFabric a **-Force** kapcsolóval kényszerítheti a konfigurációs kiszolgáló eltávolítását/törlését.

## <a name="renew-tlsssl-certificates"></a>TLS/SSL-tanúsítványok megújítása
A konfigurációs kiszolgáló beépített webkiszolgálóval rendelkezik, amely összehangolja a mobilitási szolgáltatás, a feldolgozó kiszolgálók és a hozzájuk kapcsolódó fő célkiszolgáló tevékenységeit. A webkiszolgáló TLS/SSL-tanúsítványt használ az ügyfelek hitelesítéséhez. A tanúsítvány három év után lejár, és bármikor megújítható.

### <a name="check-expiry"></a>Érvényesség-ellenőrzési idő

A konfigurációs kiszolgálóknak a 2016. május előtt történő központi telepítéséhez a tanúsítvány lejárata egy évig volt beállítva. Ha a tanúsítvány lejár, a következők történnek:

- Ha a lejárati dátum két hónap vagy kevesebb, a szolgáltatás elkezdi az értesítések küldését a Portálon és e-mailben (ha előfizetett Azure Site Recovery értesítésekre).
- Egy értesítési szalagcím jelenik meg a tár erőforrás lapján. További részletekért kattintson a szalagcímre.
- Ha a **Frissítés most** gomb jelenik meg, az azt jelzi, hogy a környezetben vannak olyan összetevők, amelyek nem lettek frissítve a 9.4. xxxx. x vagy újabb verzióra. Frissítse az összetevőket a tanúsítvány megújítása előtt. Régebbi verziókban nem lehet megújítani.

### <a name="renew-the-certificate"></a>A tanúsítvány megújítása

1. A tárolóban nyissa meg **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálót**, és kattintson a szükséges konfigurációs kiszolgálóra.
2. A lejárati dátum a **konfigurációs kiszolgáló állapota** területen jelenik meg
3. Kattintson a **tanúsítványok megújítása** elemre. 




## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Következő lépések

Tekintse át az oktatóanyagokat a [fizikai kiszolgálók](./physical-azure-disaster-recovery.md) Azure-ba való vész-helyreállításának beállításához.
