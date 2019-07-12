---
title: Előkészítése és a egy fő VHD-rendszerképet – Azure testreszabása
description: Hogyan előkészítése, testre szabhatja, és a egy virtuális asztali Windows-előnézeti kép feltöltése az Azure-bA.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 2413a380adf32755452482d2b68d2055f7db666d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620441"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Fő VHD-rendszerkép létrehozása és testreszabása

Ez a cikk bemutatja, hogyan készülhet fel a fő virtuális merevlemez (VHD) Rendszerkép feltöltése az Azure-ba, beleértve az virtuális gépek (VM) létrehozása és telepítése rajtuk. Ezeket az utasításokat, amelyek a szervezet meglévő folyamatok használható Windows virtuális asztal előzetes jellemző konfiguráció esetén is.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Az Azure lemezkép-katalógusában a Windows 10 Enterprise több munkamenet érhető el. Ez a rendszerkép testreszabásához két lehetőség van.

Az első lehetőség az, hogy a virtuális gép (VM) üzembe helyezése az Azure-ban utasításait követve [virtuális gép létrehozása felügyelt rendszerképből](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed), majd lépjen tovább, és [szoftver előkészítési és -telepítési](set-up-customize-master-image.md#software-preparation-and-installation).

A második lehetőség, hogy a lemezkép létrehozásának helyileg letölteni a képet, a Hyper-V virtuális gépek üzembe helyezésével és szabja testre az igényeinek, amely a következő szakasz ismerteti.

### <a name="local-image-creation"></a>Helyi kép létrehozása

A kép letöltése egy helyi helyre, után nyissa meg a **Hyper-V Manager** a VHD-t másolta a virtuális gép létrehozásához. Az alábbi utasításokat követve egy egyszerű verziója, de a részletes utasításokat talál [hozzon létre egy virtuális gépet a Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Virtuális gép létrehozása a másolt VHD-vel:

1. Nyissa meg a **új virtuális gép varázsló**.

2. A generáció megadása oldalon válassza ki a **1. generációs**.

    ![Egy lapjának képernyőképe, a generációt. Az 1. generációs"beállítás van kiválasztva.](media/a41174fd41302a181e46385e1e701975.png)

3. Az ellenőrzőpont-típusa tiltsa le az ellenőrzőpontok a jelölőnégyzet jelölésének törlése.

    ![Az ellenőrző pont típusa a szakasz az ellenőrzőpontok oldal képernyőképe.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

A következő parancsmagot a PowerShellben az ellenőrzőpontok letiltása is futtathatja.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Rögzített méretű lemezek

Ha egy virtuális Gépet hoz létre meglévő VHD-vel, alapértelmezés szerint létrehoz egy dinamikus lemezre. Ez módosítható rögzített kiválasztásával **lemez szerkesztése...**  az alábbi képen látható módon. További részletes utasítások: [készítse elő a Windows VHD vagy VHDX, az Azure-bA feltöltendő](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Képernyőkép a lemez szerkesztése lehetőséget.](media/35772414b5a0f81f06f54065561d1414.png)

A következő PowerShell-parancsmag módosítása a lemezt rögzített is futtathatja.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Szoftver-előkészítési és -telepítés

Ez a szakasz bemutatja, hogyan készítheti elő és FSLogix, a Windows Defender és egyéb gyakori alkalmazások telepítése. 

Ha a virtuális Gépen telepít az Office 365 ProPlus és a onedrive vállalati verzió [telepítése Office egy fő VHD-rendszerképet a](install-office-on-wvd-master-image.md). A hivatkozásra kattintva térjen vissza a cikkhez, és a fő virtuális merevlemez folyamat befejezéséhez a cikk a következő lépésben.

Ha a felhasználók bizonyos LOB-alkalmazások eléréséhez szükséges, javasoljuk ebben a szakaszban utasítások végrehajtása után telepítse őket.

### <a name="disable-automatic-updates"></a>Az automatikus frissítések letiltásához

Helyi csoportházirend segítségével az automatikus frissítések letiltásához:

1. Nyissa meg **Helyicsoportházirend-szerkesztő\\felügyeleti sablonok\\Windows-összetevők\\Windows Update**.
2. Kattintson a jobb gombbal **automatikus frissítés konfigurálása** , beállíthatja azt a **letiltott**.

A következő parancsot egy parancssorban az automatikus frissítések letiltásához is futtathatja.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>A Windows 10 rendszerű számítógépen (nem kötelező) adja meg a kezdő elrendezés

Adjon meg egy kezdő elrendezés Windows 10 rendszerű számítógépen a következő parancs futtatásával.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-user-profile-container-fslogix"></a>Állítsa be a felhasználói profil tároló (FSLogix)

A lemezkép részeként adja meg a FSLogix tároló, kövesse a [állítsa be a felhasználói állomás készlet megosztásának](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Tesztelheti a FSLogix tároló működésének [ebben a rövid útmutatóban](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>A Windows Defender konfigurálása

Ha a Windows Defender van konfigurálva a virtuális gépen, győződjön meg arról, azt úgy konfigurálva, hogy nem vizsgálat VHD és vhdx formátumú teljes tartalmát a melléklet során.

Ez a konfiguráció csak eltávolítja a VHD és VHDX-fájlok vizsgálata során a mellékletet, de nincsenek hatással a valós idejű vizsgálatát.

Részletes utasítások a Windows Defender a Windows Server konfigurálása, lásd: [a Windows Server konfigurálása a Windows Defender víruskereső kizárások](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

A Windows Defender vizsgálata az egyes fájlok kizárása konfigurálásával kapcsolatos további tudnivalókért lásd: [konfigurálása, és ellenőrizze a fájl kiterjesztése és a mappa helye alapján kizárások](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Munkamenet időkorlátja szabályzatok konfigurálása

Távoli munkamenet-szabályzatai csoportházirend szinten kényszeríthető, mert a gazdagép-készletben lévő összes virtuális gép ugyanarra a biztonsági csoportra részét képezik.

Távoli munkamenet-szabályzatok konfigurálása:

1. Navigáljon a **felügyeleti sablonok** > **Windows-összetevők** > **távoli asztali szolgáltatások**  >  **Távoli asztali munkamenetgazda** > **időkorlátokat**.
2. A jobb oldali panelen válassza ki a **aktív, de inaktív távoli asztali szolgáltatások munkamenet során beállított időkorlát** házirend.
3. Miután a modális ablak jelenik meg, módosítsa a házirend-beállításként az **nincs konfigurálva** való **engedélyezve** aktiválása a szabályzat.
4. A házirend-beállításként alatt a legördülő menüben, és az időtartamnak a beállítása **4 óra**.

Távoli munkamenet-szabályzatai manuálisan is konfigurálja a következő parancsok futtatásával:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Időzóna átirányításának beállítása

Időzóna-átirányítást csoportházirend szinten kényszeríthető, mert a gazdagép-készletben lévő összes virtuális gép ugyanarra a biztonsági csoportra részét képezik.

Az átirányítási idő zónák:

1. Az Active Directory-kiszolgálón nyissa meg a **Csoportházirend kezelése konzol**.
2. Bontsa ki a tartomány és a csoportházirend-objektumok.
3. Kattintson a jobb gombbal a **csoportházirend-objektum** a csoportházirend-beállítások, és válassza ki a létrehozott **szerkesztése**.
4. Az a **Csoportházirendkezelés-szerkesztő**, navigáljon a **számítógép konfigurációja** > **házirendek** > **felügyeleti Sablonok** > **Windows-összetevők** > **távoli asztali szolgáltatások** > **távoli asztali munkamenetgazda**   >  **Eszközök és erőforrások átirányítását**.
5. Engedélyezze a **időzóna átirányításához** beállítás.

Ez a parancs a fő rendszerkép időzónák átirányítani a is futtathatja:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Disable Storage Sense

Windows virtuális asztali munkamenetének gazdagépét, amelyek használják a Windows 10 Enterprise vagy Windows 10 Enterprise több munkamenet javasolt Tárterületsegéd letiltása. Tárterületsegéd letilthatja a beállítások menü alatt **tárolási**, ahogy az alábbi képernyőfelvételen látható:

![A Storage menü beállításaiban képernyőképe. A "Tárterületsegéd" lehetőség ki van kapcsolva.](media/storagesense.png)

Emellett módosíthatja a beállítást a beállításjegyzék a következő parancs futtatásával:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>További nyelvek támogatását

Ez a cikk nem tárgyalja a nyelvi és területi támogatási konfigurálása. További információkért tekintse át a következő cikkeket:

- [Nyelv hozzáadása Windows-rendszerképek](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Az igény szerinti szolgáltatások](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Az igény szerinti (FTM) nyelvéhez és régiójához funkciók](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Más alkalmazások és a beállításjegyzék konfigurációja

Ez a szakasz ismerteti az alkalmazás és az operációs rendszer konfigurálása. Ebben a szakaszban az összes konfiguráció a beállításjegyzék bejegyzéseit, amelyek parancssori által futtatható keresztül történik, és regedit eszközök.

>[!NOTE]
>Ajánlott eljárások a csoportházirend-objektumok (GPO) vagy a beállításjegyzék-import-konfiguráció valósítható meg. A rendszergazdák kiválaszthatják, vagy a szervezeti követelmények alapján lehetőséget.

Visszajelzési központ gyűjteménye, telemetriai adatokat a Windows 10 Enterprise több munkamenet a következő parancs futtatásával:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Futtassa a Watson szoftverleállások elhárításához a következő parancsot:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

A következő parancsokat az 5 k felbontás támogatása hárítsa el a beállításjegyzék-szerkesztővel. A párhuzamos verem engedélyezése előtt futtatnia kell a parancsokat.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>A Rendszerkép feltöltése az Azure előkészítése

Miután befejeződött a konfigurációs és telepített összes alkalmazást, kövesse a [készítse elő a Windows VHD vagy VHDX, az Azure-bA feltöltendő](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) készítenie a rendszerképet.

Előkészítését követően a lemezképet a feltöltéshez, ellenőrizze, hogy a virtuális gép kikapcsolt vagy felszabadított állapotban marad.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Fő rendszerkép feltöltése az Azure storage-fiókba

Ez a szakasz csak akkor vonatkozik, a fő rendszerkép létrehozásakor helyileg.

Az alábbi utasításokat követve megtudhatja, hogyan lehet a fő rendszerkép feltöltése az Azure storage-fiókba. Ha még nem rendelkezik Azure storage-fiók, kövesse az utasításokat a [Ez a cikk](https://code.visualstudio.com/tutorials/static-website/create-storage) hozhat létre egyet.

1. Ha még nem rendelkezik rögzített méretű átalakítása a Virtuálisgép-lemezkép (VHD). Ha a kép nem átalakítása rögzített méretű, nem sikerült létrehozni a lemezképet.

2. Töltse fel a VHD-t a tárfiókban található blob-tárolóba. A gyors feltöltheti a [Storage Explorer eszköz](https://azure.microsoft.com/features/storage-explorer/). A Storage Explorer eszközzel kapcsolatos további tudnivalókért lásd: [Ez a cikk](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![A Microsoft Azure Storage Explorer eszközével keresési ablak képernyőképe. A ".vhd- vagy vhdx-fájlok feltöltése lapblobként (ajánlott)" jelölőnégyzet be van jelölve.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ezután nyissa meg az Azure Portalon, a böngészőben, és keressen a "Képek." A keresés kell vezethet, hogy a **kép létrehozása** lapon, az alábbi képernyőképen látható módon:

    ![A létrehozása lemezkép lap az Azure Portal képernyőképe kitöltött példaértékeket a lemezképhez.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Miután létrehozta a rendszerképet, egy, az alábbi képernyőképhez hasonló értesítést kell megjelennie:

    ![A "sikeresen létrehozott lemezképpel" értesítés képernyőképe.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>További lépések

Most, hogy van egy rendszerképe, hozzon létre, vagy frissítse a host készletek. Hogyan hozhat létre, és frissítse a host készletek kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Gazdagép-készlet létrehozása egy Azure Resource Manager-sablonnal](create-host-pools-arm-template.md)
- [Oktatóanyag: Gazdagép-készlet létrehozása az Azure Marketplace-en](create-host-pools-azure-marketplace.md)
- [Gazdagép-készlet létrehozása a PowerShell-lel](create-host-pools-powershell.md)
- [A felhasználói profil megosztás gazdagép készlet beállítása](create-host-pools-user-profile.md)
- [Windows virtuális asztal terheléselosztási módszer konfigurálása](configure-host-pool-load-balancing.md)
