---
title: Fő VHD-rendszerkép előkészítése és testreszabása – Azure
description: Windows rendszerű virtuális asztali rendszerképek előkészítése, testreszabása és feltöltése az Azure-ba.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 654b5b293397af9737813d759a7c784d90629942
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445805"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Fő VHD-rendszerkép létrehozása és testreszabása

Ebből a cikkből megtudhatja, hogyan készítse elő a fő virtuális merevlemez (VHD) lemezképét az Azure-ba való feltöltéshez, beleértve a virtuális gépek (VM-EK) létrehozását és a szoftverek telepítését. Ezek az utasítások egy olyan Windows rendszerű virtuális asztali konfigurációra vonatkoznak, amelyet a szervezet meglévő folyamataihoz használhat.

>[!IMPORTANT]
>Javasoljuk, hogy az Azure rendszerkép-katalógusában található rendszerképet használja. Ha azonban egyéni rendszerképet kell használnia, győződjön meg arról, hogy még nincs telepítve a virtuális GÉPEN a Windows rendszerű virtuális asztali ügynök. A Windows rendszerű virtuális asztali ügynökkel testreszabott rendszerkép használatával problémákat okozhat a rendszerképben, például blokkolhatja a regisztrációt, és megakadályozhatja a felhasználói munkamenetek kapcsolatait.  

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A Windows 10 Enterprise multi-session szolgáltatás elérhető az Azure rendszerkép-katalógusában. A rendszerkép testreszabására két lehetőség áll rendelkezésre.

Az első lehetőség egy virtuális gép (VM) üzembe helyezése az Azure-ban a virtuális gépek [felügyelt rendszerképből való létrehozása](../virtual-machines/windows/create-vm-generalized-managed.md)című témakör útmutatásai alapján, majd a [szoftver előkészítése és telepítése](set-up-customize-master-image.md#software-preparation-and-installation)előtt ugorjon.

A második lehetőség a rendszerkép helyi létrehozása a rendszerkép letöltésével, egy Hyper-V virtuális gép üzembe helyezésével és az igényeinek megfelelő testreszabásával, amelyet a következő szakaszban talál.

### <a name="local-image-creation"></a>Helyi rendszerkép létrehozása

Miután letöltötte a rendszerképet egy helyi helyre, nyissa meg a **Hyper-V kezelőjét** , és hozzon létre egy virtuális gépet a másolt VHD-vel. Az alábbi utasítások egyszerűek, de részletesebb útmutatást talál a [virtuális gép Hyper-V-ben történő létrehozásához](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Virtuális gép létrehozása a másolt VHD-vel:

1. Nyissa meg az **új virtuális gép varázslót**.

2. A generáció megadása lapon válassza az **1. generáció** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a létrehozási lap megadásáról. Az "1. generáció" beállítás van kiválasztva.](media/a41174fd41302a181e46385e1e701975.png)

3. Az ellenőrzőpont típusa területen tiltsa le az ellenőrzőpontokat a jelölőnégyzet bejelölésének törlésével.

    > [!div class="mx-imgBorder"]
    > ![Az ellenőrzőpontok lap ellenőrzőpont-típus szakaszának képernyőképe.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Az ellenőrzőpontok letiltásához a PowerShellben a következő parancsmagot is futtathatja.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Rögzített lemez

Ha meglévő virtuális merevlemezről hoz létre virtuális gépet, alapértelmezés szerint dinamikus lemezt hoz létre. A rögzített lemezre a **lemez szerkesztése...** lehetőség kiválasztásával módosítható, ahogy az alábbi képen is látható. Részletesebb útmutatásért lásd: [Windows VHD vagy VHDX előkészítése az Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)-ba való feltöltéshez.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a lemez szerkesztése lehetőségről.](media/35772414b5a0f81f06f54065561d1414.png)

A következő PowerShell-parancsmag futtatásával is megváltoztathatja a lemezt egy rögzített lemezre.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Szoftverek előkészítése és telepítése

Ez a szakasz a FSLogix és a Windows Defender előkészítését és telepítését, valamint az alkalmazások és a rendszerkép beállításjegyzékének alapszintű konfigurációs beállításait ismerteti.

Ha nagyvállalati és OneDrive telepíti Microsoft 365 alkalmazásait a virtuális gépen, lépjen az [Office telepítése fő VHD-lemezképre](install-office-on-wvd-master-image.md) , és kövesse az itt található utasításokat az alkalmazások telepítéséhez. Ha elkészült, térjen vissza ehhez a cikkhez.

Ha a felhasználóknak hozzá kell férniük bizonyos LOB-alkalmazásokhoz, javasoljuk, hogy a szakasz utasításainak elvégzése után telepítse őket.

### <a name="set-up-user-profile-container-fslogix"></a>Felhasználói profil tárolójának (FSLogix) beállítása

Ha a FSLogix-tárolót a rendszerkép részeként szeretné felvenni, kövesse a következő témakör utasításait: [create a profile Container for an Host Pool with a file share (fájlmegosztás használatával](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)). [Ezzel](/fslogix/configure-cloud-cache-tutorial/)a rövid útmutatóval ellenőrizheti a FSLogix tároló funkcióit.

### <a name="configure-windows-defender"></a>A Windows Defender konfigurálása

Ha a Windows Defender konfigurálva van a virtuális gépen, győződjön meg arról, hogy az úgy van beállítva, hogy ne ellenőrizze a VHD-és VHDX-fájlok teljes tartalmát a mellékletben.

Ez a konfiguráció csak a VHD-és a VHDX-fájlok vizsgálatát távolítja el a mellékletben, de nem befolyásolja a valós idejű vizsgálatokat.

A Windows Defender Windows Serveren történő konfigurálásával kapcsolatos további információkért lásd: [Windows Defender víruskereső-kizárások konfigurálása Windows Serveren](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Ha többet szeretne megtudni arról, hogyan konfigurálhatja a Windows Defendert bizonyos fájlok vizsgálatból való kizárására, tekintse [meg a kizárások konfigurálása és ellenőrzése a fájlkiterjesztés és a mappa helye alapján](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)című témakört.

### <a name="disable-automatic-updates"></a>Automatikus frissítések letiltása

Az automatikus frissítések letiltása helyi Csoportházirend használatával:

1. Nyissa meg **Helyicsoportházirend-szerkesztő \\ Felügyeleti sablonok \\ Windows-összetevők \\ Windows Update**.
2. Kattintson a jobb gombbal az **automatikus frissítés konfigurálása** elemre, és állítsa **le a Letiltva** értékre.

A parancssorban a következő parancsot is futtathatja az automatikus frissítések letiltásához.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Windows 10 rendszerű számítógépek indítási elrendezésének megadása (nem kötelező)

Futtassa ezt a parancsot a Windows 10 rendszerű számítógépek indítási elrendezésének megadásához.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Időzóna-átirányítás beállítása

Az időzóna átirányítása Csoportházirend szinten kényszeríthető, mivel a gazdagépen lévő összes virtuális gép ugyanahhoz a biztonsági csoporthoz tartozik.

Időzónák átirányítása:

1. A Active Directory-kiszolgálón nyissa meg a **csoportházirend-kezelő konzol**.
2. Bontsa ki a tartományt és Csoportházirend objektumokat.
3. Kattintson a jobb gombbal a csoportházirend-beállításokhoz létrehozott **csoportházirend objektumra** , és válassza a **Szerkesztés** lehetőséget.
4. A **csoportházirend-felügyeleti szerkesztő** navigáljon a **számítógép-konfigurációs**  >  **házirendek**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztal munkamenet-gazdagép**  >  **eszköz és erőforrás-átirányítás** elemre.
5. Engedélyezze az **időzóna-átirányítás engedélyezése** beállítást.

Ezt a parancsot a fő lemezképen is futtathatja az időzónák átirányításához:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Tárolási értelem letiltása

A Windows 10 Enterprise vagy a Windows 10 Enterprise multi-session hostt használó Windowsos virtuális asztali munkamenetgazda esetében ajánlott letiltani a tárterületet. Az alábbi képernyőképen látható beállítások menüben letilthatja **a tárolási** érzékelést:

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a beállítások területen a tárolási menüről. A "tárolási értelem" beállítás ki van kapcsolva.](media/storagesense.png)

A beállítást a beállításjegyzékben a következő parancs futtatásával is módosíthatja:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>További nyelvi támogatás

Ez a cikk nem tárgyalja a nyelvi és a regionális támogatás konfigurálásának módját. További információért tekintse át a következő cikkeket:

- [Nyelvek hozzáadása Windows-lemezképekhez](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Igény szerinti szolgáltatások](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Igény szerinti nyelvi és területi funkciók (Franciaország)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Egyéb alkalmazások és beállításjegyzék-konfiguráció

Ez a szakasz az alkalmazások és az operációs rendszer konfigurálását ismerteti. Az ebben a szakaszban található összes konfiguráció a parancssori és a regedit-eszközök által végrehajtható beállításjegyzék-bejegyzéseken keresztül történik.

>[!NOTE]
>A Csoportházirend objektumok (GPO-k) vagy a beállításjegyzék-importálások konfigurálásához ajánlott eljárásokat alkalmazhat. A rendszergazda bármelyik lehetőséget kiválaszthatja a szervezet követelményei alapján.

A Windows 10 Enterprise multi-session telemetria-alapú adatvisszajelzési központ esetén futtassa ezt a parancsot:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Futtassa a következő parancsot a Watson-összeomlások kijavításához:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Adja meg a következő parancsokat a Rendszerleíróadatbázis-szerkesztőben az 5k-feloldás támogatásának javításához. A párhuzamos verem engedélyezése előtt futtatnia kell a parancsokat.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>A rendszerkép előkészítése az Azure-ba való feltöltésre

Miután befejezte a konfigurálást, és telepítette az összes alkalmazást, kövesse a [Windows VHD vagy VHDX előkészítése az Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) -ba való feltöltéséhez című témakör utasításait a rendszerkép előkészítéséhez.

A rendszerkép feltöltésre való előkészítése után győződjön meg arról, hogy a virtuális gép ki van kapcsolva vagy fel van foglalva.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Fő rendszerkép feltöltése egy Azure-beli Storage-fiókba

Ez a szakasz csak akkor érvényes, ha a fő lemezképet helyileg hozták létre.

Az alábbi útmutatást követve megtudhatja, hogyan töltheti fel a fő lemezképet egy Azure Storage-fiókba. Ha még nem rendelkezik Azure Storage-fiókkal, akkor a [cikk](/azure/developer/javascript/tutorial-vscode-static-website-node-03) utasításait követve hozzon létre egyet.

1. Ha még nem tette meg, alakítsa át a virtuális gép rendszerképét (VHD). Ha nem alakítja át a képet Rögzítettre, nem tudja sikeresen létrehozni a rendszerképet.

2. Töltse fel a VHD-t egy blob-tárolóba a Storage-fiókjában. Gyorsan feltöltheti az [Storage Explorer eszközzel](https://azure.microsoft.com/features/storage-explorer/). Ha többet szeretne megtudni a Storage Explorer eszközről, tekintse meg [ezt a cikket](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows).

    > [!div class="mx-imgBorder"]
    > ![A Microsoft Azure Storage Explorer eszköz keresési ablakának képernyőképe. A "feltöltés. vhd vagy vhdx fájlok blobként (ajánlott)" jelölőnégyzet be van jelölve.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ezután nyissa meg a böngészőben a Azure Portalt, és keressen rá a "képek" kifejezésre. A keresésnek a **rendszerkép létrehozása** lapra kell mutatnia, ahogy az alábbi képernyőképen is látható:

    > [!div class="mx-imgBorder"]
    > ![A Azure Portal rendszerkép létrehozása oldalának képernyőképe, amely a képen látható értékekkel lett kitöltve.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Miután létrehozta a rendszerképet, a következő képernyőképen láthatóhoz hasonló értesítést kell látnia:

    > [!div class="mx-imgBorder"]
    > ![A "rendszerkép létrehozása sikeres" értesítés képernyőképe.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy már rendelkezik rendszerképtel, létrehozhat vagy frissíthet gazdagép-készleteket. A gazdagép-készletek létrehozásával és frissítésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Oktatóanyag: állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell.md)
- [Profiltároló létrehozása gazdagépkészlet számára fájlmegosztás használatával](create-host-pools-user-profile.md)
- [A Windows Virtual Desktop terheléselosztási módjának beállítása](configure-host-pool-load-balancing.md)

Ha a VHD-rendszerkép előkészítése vagy testreszabása után csatlakozási probléma lépett fel, tekintse meg a Súgó [hibaelhárítási útmutatóját](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) .
