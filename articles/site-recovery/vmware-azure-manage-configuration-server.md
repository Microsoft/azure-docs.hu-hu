---
title: A konfigurációs kiszolgáló kezelése a vész-helyreállításhoz Azure Site Recovery
description: Ismerje meg a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő helyreállításához szükséges általános feladatokat a helyszíni konfigurációs kiszolgáló és a Azure Site Recovery használatával.
author: Rajeswari-Mamilla
ms.author: ramamill
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 67f25c97e0b4433ed09cb8c6337afe671424ba0b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048544"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>VMware virtuális gép/fizikai kiszolgáló vészhelyreállítási konfigurációs kiszolgálójának kezelése

Ha [Azure site Recoveryt](site-recovery-overview.md) használ a VMWare virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállításához, a helyszíni konfigurációs kiszolgálót kell beállítania. A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. Ez a cikk összefoglalja a konfigurációs kiszolgáló üzembe helyezése után történő felügyeletének gyakori feladatait.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Windows-licenc frissítése

A OVF sablonnal megadott licenc 180 napig érvényes próbaverziós licenc. A folyamatos használat érdekében aktiválnia kell a Windowst egy beszerzett licenccel. A licencek frissítése történhet önálló kulcs vagy KMS standard kulcs használatával. Az útmutató a [DISM Windows-parancssorban érhető el az operációs rendszer futtatásához](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). A kulcsok beszerzéséhez tekintse meg a [KMS-ügyfél beállítása](/windows-server/get-started/kmsclientkeys)című témakört.

## <a name="access-configuration-server"></a>Hozzáférési konfigurációs kiszolgáló

A konfigurációs kiszolgálót a következőképpen érheti el:

* Jelentkezzen be a virtuális gépre, amelyen telepítve van, és indítsa el **Azure Site Recovery Configuration Managert** az asztali parancsikonról.
* Másik lehetőségként a konfigurációs kiszolgálót távolról is elérheti a https://*ConfigurationServerName*/: 44315/. Jelentkezzen be rendszergazdai hitelesítő adatokkal.

## <a name="modify-vmware-server-settings"></a>VMware-kiszolgáló beállításainak módosítása

1. Ha másik VMware-kiszolgálót szeretne hozzárendelni a konfigurációs kiszolgálóhoz, a [Bejelentkezés](#access-configuration-server)után válassza a **Hozzáadás vCenter Server/vSphere ESXi-kiszolgáló** lehetőséget.
2. Adja meg a részleteket, majd kattintson **az OK gombra**.

## <a name="modify-credentials-for-automatic-discovery"></a>Hitelesítő adatok módosítása az automatikus felderítéshez

1. A VMware-alapú virtuális gépek automatikus felderítéséhez a VMware-kiszolgálóhoz való kapcsolódáshoz használt hitelesítő adatok frissítéséhez a [Bejelentkezés](#access-configuration-server)után válassza ki a fiókot, és kattintson a **Szerkesztés** gombra.
2. Adja meg az új hitelesítő adatokat, majd kattintson **az OK gombra**.

    ![VMware módosítása](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

A hitelesítő adatokat CSPSConfigtool.exe használatával is módosíthatja.

1. Jelentkezzen be a konfigurációs kiszolgálóra, és indítsa el CSPSConfigtool.exe
2. Válassza ki a módosítani kívánt fiókot, és kattintson a **Szerkesztés** gombra.
3. Adja meg a módosított hitelesítő adatokat, és kattintson **az OK** gombra.

## <a name="modify-credentials-for-mobility-service-installation"></a>A mobilitási szolgáltatás telepítésének hitelesítő adatainak módosítása

Módosítsa a mobilitási szolgáltatás automatikus telepítéséhez használt hitelesítő adatokat azokon a VMware virtuális gépeken, amelyeken engedélyezve van a replikáció.

1. [Bejelentkezés](#access-configuration-server)után válassza a **virtuális gép hitelesítő adatainak kezelése** lehetőséget.
2. Válassza ki a módosítani kívánt fiókot, és kattintson a **Szerkesztés** gombra.
3. Adja meg az új hitelesítő adatokat, majd kattintson **az OK gombra**.

    ![Mobilitási szolgáltatás hitelesítő adatainak módosítása](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

A hitelesítő adatokat CSPSConfigtool.exe használatával is módosíthatja.

1. Jelentkezzen be a konfigurációs kiszolgálóra, és indítsa el CSPSConfigtool.exe
2. Válassza ki a módosítani kívánt fiókot, és kattintson a **Szerkesztés** gombra.
3. Adja meg az új hitelesítő adatokat, és kattintson **az OK** gombra.

## <a name="add-credentials-for-mobility-service-installation"></a>Hitelesítő adatok hozzáadása a mobilitási szolgáltatás telepítéséhez

Ha kihagyta a hitelesítő adatok hozzáadását a konfigurációs kiszolgáló OVF üzembe helyezése során,

1. [Bejelentkezés](#access-configuration-server)után válassza a **virtuális gép hitelesítő adatainak kezelése** lehetőséget.
2. Kattintson a **virtuális gép hitelesítő adatainak hozzáadása** lehetőségre.
    ![Képernyőfelvétel: a virtuális gép hitelesítő adatainak hozzáadása panel a virtuális gép hitelesítő adatainak hozzáadása hivatkozással látható.](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Adja meg az új hitelesítő adatokat, és kattintson a **Hozzáadás** gombra.

CSPSConfigtool.exe használatával is hozzáadhat hitelesítő adatokat.

1. Jelentkezzen be a konfigurációs kiszolgálóra, és indítsa el CSPSConfigtool.exe
2. Kattintson a **Hozzáadás** gombra, adja meg az új hitelesítő adatokat, majd kattintson **az OK** gombra.

## <a name="modify-proxy-settings"></a>Proxybeállítások módosítása

Módosítsa a konfigurációs kiszolgáló számítógépe által az Azure-hoz való internet-hozzáféréshez használt proxybeállításokat. Ha a konfigurációs kiszolgáló gépen futó alapértelmezett Process Serveren kívül egy Process Server-géppel is rendelkezik, mindkét gépen módosítsa a beállításokat.

1. A konfigurációs kiszolgálóra való [Bejelentkezés](#access-configuration-server) után válassza a **kapcsolat kezelése** lehetőséget.
2. Frissítse a proxy értékeit. Ezután válassza a **Mentés** lehetőséget a beállítások frissítéséhez.

## <a name="add-a-network-adapter"></a>Hálózati adapter hozzáadása

A Open Virtualization Format (OVF) sablon egyetlen hálózati adapterrel telepíti a konfigurációs kiszolgáló virtuális gépet.

- [Hozzáadhat egy további adaptert a virtuális géphez](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), de hozzá kell adnia a konfigurációs kiszolgáló a tárolóban való regisztrálásához.
- Ha a konfigurációs kiszolgáló a tárolóban való regisztrálását követően szeretne hozzáadni egy adaptert, adja hozzá az adaptert a virtuális gép tulajdonságaihoz. Ezután [újra regisztrálnia](#reregister-a-configuration-server-in-the-same-vault) kell a kiszolgálót a tárolóban.

## <a name="how-to-renew-ssl-certificates"></a>SSL-tanúsítványok megújítása

A konfigurációs kiszolgáló beépített webkiszolgálóval rendelkezik, amely a mobilitási ügynökök tevékenységeit a védett gépeken, a beépített/kibővíthető folyamat-kiszolgálókon és a hozzájuk kapcsolódó fő célkiszolgálón dolgozza fel. A webkiszolgáló egy SSL-tanúsítványt használ az ügyfelek hitelesítéséhez. A tanúsítvány három év után lejár, és bármikor megújítható.

### <a name="check-expiry"></a>Érvényesség-ellenőrzési idő

A lejárati dátum a **konfigurációs kiszolgáló állapota** területen jelenik meg. A konfigurációs kiszolgálóknak a 2016. május előtt történő központi telepítéséhez a tanúsítvány lejárata egy évig volt beállítva. Ha van olyan tanúsítványa, amely lejár, a következők történnek:

- Ha a lejárati dátum két hónap vagy kevesebb, a szolgáltatás elkezdi az értesítések küldését a Portálon és e-mailben (ha előfizetett Site Recovery értesítésekre).
- Egy értesítési szalagcím jelenik meg a tár erőforrás lapján. További információért kattintson a szalagcímre.
- Ha a **Frissítés most** gomb jelenik meg, az azt jelzi, hogy a környezet egyes összetevői nem lettek frissítve a 9.4. xxxx. x vagy újabb verzióra. Frissítse az összetevőket a tanúsítvány megújítása előtt. Régebbi verziókban nem lehet megújítani.

### <a name="if-certificates-are-yet-to-expire"></a>Ha a tanúsítványok még nem járnak le

1. A megújításhoz nyissa meg **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálót** a tárolóban. Válassza ki a szükséges konfigurációs kiszolgálót.
2. Győződjön meg arról, hogy minden összetevő kibővíthető folyamat-kiszolgáló, fő célkiszolgáló és mobilitási ügynök az összes védett gépen a legújabb verziókban van, és csatlakoztatva van.
3. Most válassza a **tanúsítványok megújítása** lehetőséget.
4. Gondosan kövesse az oldalon megjelenő utasításokat, és kattintson az OK gombra a kiválasztott konfigurációs kiszolgálón lévő tanúsítványok megújításához és a hozzájuk társított összetevőkhöz.

### <a name="if-certificates-have-already-expired"></a>Ha a tanúsítványok már lejártak

1. A post lejárat után **a tanúsítványok nem megújíthatók Azure Portalról**. A továbblépés előtt gondoskodjon arról, hogy minden összetevő kibővítse a folyamat kiszolgálóit, a fő célkiszolgáló és a mobilitási ügynökök minden védett gépen a legújabb verziókat, és csatlakoztatva legyenek.
2. **Ezt az eljárást csak akkor hajtsa végre, ha a tanúsítványok már lejártak.** Jelentkezzen be a konfigurációs kiszolgálóra, navigáljon a C meghajtó > program adatai > Site Recovery > Home > svsystems > bin és a "RenewCerts" végrehajtó eszköz rendszergazdaként való végrehajtásához.
3. Megjelenik egy PowerShell-végrehajtási ablak, és elindítja a tanúsítványok megújítását. A művelet akár 15 percet is igénybe vehet. A megújítás befejezéséig ne zárjuk be az ablakot.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificates":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Konfigurációs kiszolgáló újraregisztrálása ugyanabban a tárolóban

Ha szükséges, a konfigurációs kiszolgáló újraregisztrálása ugyanabban a tárolóban végezhető el. Ha van egy további Process Server-gép, a konfigurációs kiszolgáló számítógépén futó alapértelmezett Process Server mellett, regisztrálja újra mindkét gépet.


1. A tárolóban nyissa meg a  >  **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálók** kezelése részt.
2. A **kiszolgálók** területen válassza a **regisztrációs kulcs letöltése** lehetőséget a tár hitelesítő adatainak fájljának letöltéséhez.
3. Jelentkezzen be a konfigurációs kiszolgáló számítógépére.
4. A **%ProgramData%\ASR\home\svsystems\bin** nyissa meg **cspsconfigtool.exe**.
5. A tároló **regisztrációja** lapon válassza a **Tallózás** lehetőséget, és keresse meg a letöltött tároló hitelesítő adatait tartalmazó fájlt.
6. Ha szükséges, adja meg a proxykiszolgáló részleteit. Válassza a **Regisztráció** lehetőséget.
7. Nyisson meg egy rendszergazdai PowerShell-parancssorablakot, és futtassa a következő parancsot:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >A legújabb tanúsítványok a konfigurációs kiszolgálóról a kibővíthető Process Serverre való **lekéréséhez** futtassa a következő parancsot: *" \<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> "--registermt parancsot*

8. Végül indítsa újra a obengine a következő parancs végrehajtásával.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Konfigurációs kiszolgáló regisztrálása egy másik tárba

> [!WARNING]
> A következő lépés leállítja a konfigurációs kiszolgálót az aktuális tárolóból, és a konfigurációs kiszolgáló alatt lévő összes védett virtuális gép replikálása leállt.

1. Jelentkezzen be a konfigurációs kiszolgálóra.
2. Nyisson meg egy rendszergazdai PowerShell-parancssorablakot, és futtassa a következő parancsot:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Indítsa el a konfigurációs kiszolgáló berendezésének böngésző-portálját az asztalán található parancsikon használatával.
4. Hajtsa végre az új konfigurációs kiszolgáló [regisztrálásához](vmware-azure-tutorial.md#register-the-configuration-server)hasonló regisztrációs lépéseket.

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló frissítése

A kumulatív frissítések futtatásával frissítheti a konfigurációs kiszolgálót. A frissítések akár N-4 verzióra is alkalmazhatók. Például:

- Ha 9,7, 9,8, 9,9 vagy 9,10-et futtat, közvetlenül is frissítheti a 9,11-es verzióra.
- Ha a 9,6-es vagy korábbi verzióját futtatja, és a 9,11-re szeretne frissíteni, először frissítenie kell az 9,7-es verzióra. 9,11 előtt.

A Azure Site Recovery Components támogatási nyilatkozatával kapcsolatos részletes útmutatást [itt](./service-updates-how-to.md#support-statement-for-azure-site-recovery)találja.
A kumulatív frissítésekre mutató hivatkozások a konfigurációs kiszolgáló összes verziójának frissítéséhez érhetők el [itt](./service-updates-how-to.md#links-to-currently-supported-update-rollups).

> [!IMPORTANT]
> Az Azure Site Recovery összes új, "N" verziójának kiadása után az "N-4" alá tartozó összes verzió támogatásnak minősül. Mindig ajánlott a legújabb verzióra frissíteni.</br>
> A Azure Site Recovery Components támogatási nyilatkozatával kapcsolatos részletes útmutatást [itt](./service-updates-how-to.md#support-statement-for-azure-site-recovery)találja.

A kiszolgálót a következőképpen frissítheti:

1. A tárolóban lépjen a   >  **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálók** kezelése elemre.
2. Ha van elérhető frissítés, megjelenik egy hivatkozás az **ügynök verziója** > oszlopban.
    ![Frissítés](./media/vmware-azure-manage-configuration-server/update2.png)
3. Töltse le a frissítési telepítő fájlt a konfigurációs kiszolgálóra.

    ![Képernyőfelvétel: a frissítési telepítőcsomag letöltéséhez kattintson ide.](./media/vmware-azure-manage-configuration-server/update1.png)

4. Kattintson ide duplán a telepítő futtatásához.
5. A telepítő észleli a gépen futó aktuális verziót. A frissítés elindításához kattintson az **Igen** gombra.
6. Ha a frissítés befejeződött, ellenőrizze a kiszolgáló konfigurációját.

    ![Képernyőkép, amely a befejezett kiszolgáló-ellenőrzési konfigurációt jeleníti meg.](./media/vmware-azure-manage-configuration-server/update3.png)

7. A telepítő bezárásához kattintson a **Befejezés** gombra.
8. A Site Recovery-összetevők további verziójának frissítéséhez tekintse meg a [frissítési útmutatót](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Konfigurációs kiszolgáló/folyamat kiszolgálójának frissítése a parancssorból

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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Konfigurációs kiszolgáló törlése vagy eltávolítása

1. [Tiltsa le](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) a konfigurációs kiszolgáló alatt lévő összes virtuális gép védelmét.
2. Szüntesse meg az összes replikációs házirend [hozzárendelését](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és [törlését](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a konfigurációs kiszolgálóról.
3. [Törölje](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) az összes olyan vCenter-kiszolgálót/vSphere-gazdagépet, amely a konfigurációs kiszolgálóhoz van társítva.
4. A tárolóban nyissa meg **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálókat**.
5. Válassza ki az eltávolítani kívánt konfigurációs kiszolgálót. Ezután a **részletek** lapon válassza a **Törlés** lehetőséget.

    ![Konfigurációs kiszolgáló törlése](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Törlés a PowerShell-lel

A konfigurációs kiszolgálót szükség esetén a PowerShell használatával is törölheti.

1. [Telepítse](/powershell/azure/install-Az-ps) a Azure PowerShell modult.
2. Jelentkezzen be az Azure-fiókjába a következő parancs használatával:

    `Connect-AzAccount`
3. Válassza ki a tároló előfizetését.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Állítsa be a tár környezetét.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. A konfigurációs kiszolgáló beolvasása.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Törölje a konfigurációs kiszolgálót.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> A konfigurációs kiszolgáló kényszerített törléséhez használhatja a Remove-AzSiteRecoveryFabric a **-Force** kapcsolót.

## <a name="generate-configuration-server-passphrase"></a>Konfigurációs kiszolgáló jelszavainak előállítása

1. Jelentkezzen be a konfigurációs kiszolgálóra, majd nyissa meg a parancssorablakot rendszergazdaként.
2. Ha a könyvtárat a Bin mappára szeretné módosítani, hajtsa végre a parancs **CD-%ProgramData%\ASR\home\svsystems\bin** .
3. A jelszó-fájl létrehozásához hajtsa végre a **genpassphrase.exe-v > MobSvc. hozzáférési** kódot.
4. A jelszó a **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase** címen található fájlban lesz tárolva.

## <a name="refresh-configuration-server"></a>Konfigurációs kiszolgáló frissítése

1. A Azure Portal navigáljon a **Recovery Services Vault**  >    >  **site Recovery infrastruktúra** kezelése  >  **a VMware & fizikai gépek**  >  **konfigurációs kiszolgálóihoz**
2. Kattintson a frissíteni kívánt konfigurációs kiszolgálóra.
3. A kiválasztott konfigurációs kiszolgáló részleteit tartalmazó panelen kattintson a **további**  >  **frissítési kiszolgáló** lehetőségre.
4. A feladat előrehaladásának figyelése **Recovery Services** tároló  >  **figyelése**  >  **site Recovery feladatok** területen.

## <a name="failback-requirements"></a>Feladat-visszavételre vonatkozó követelmények

Az ismételt védelem és a feladat-visszavétel során a helyszíni konfigurációs kiszolgálónak futnia kell, és csatlakoztatott állapotban kell lennie. A sikeres feladat-visszavétel érdekében a visszaadott virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában.

Győződjön meg arról, hogy a konfigurációs kiszolgáló rendszeres ütemezett biztonsági mentést készít. Ha katasztrófa következik be, és a konfigurációs kiszolgáló elvész, először vissza kell állítania a konfigurációs kiszolgálót egy biztonsági másolatból, és biztosítania kell, hogy a visszaállított konfigurációs kiszolgáló ugyanazzal az IP-címmel legyen ellátva, amellyel a tárolóban regisztrálva volt. A feladat-visszavétel nem fog működni, ha a visszaállított konfigurációs kiszolgálóhoz másik IP-cím van használatban.

## <a name="next-steps"></a>Következő lépések

Tekintse át az oktatóanyagokat a [VMWare virtuális gépek](vmware-azure-tutorial.md) Azure-ba történő helyreállításának beállításához.
