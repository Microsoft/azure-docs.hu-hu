---
title: Azure-fájlmegosztás használata Windowson | Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure-fájlmegosztást a Windows és a Windows Server használatával. Azure-fájlmegosztás használata SMB 3,0-mel a helyszíni vagy Azure-beli virtuális gépeken futó Windows-példányokon.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626894"
---
# <a name="use-an-azure-file-share-with-windows"></a>Azure-fájlmegosztás használata Windowson
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások zökkenőmentesen használhatóak Windowson és Windows Serveren. Ebben a cikkben az Azure-fájlmegosztások Windowson és Windows Serveren való használatának szempontjairól olvashat.

Ha egy Azure-fájlmegosztást az üzemeltető Azure-régión kívül kíván alkalmazni, például a helyszínen vagy más Azure-régióban, az operációs rendszernek támogatnia kell az SMB 3.0-s verziót. 

Azure-fájlmegosztásokat az Azure-beli virtuális gépeken vagy helyszínen futó Windows-telepítéseken használhat. A következő táblázatban látható, hogy melyik operációsrendszer-verzió melyik környezetekben támogatja a fájlmegosztások elérését:

| Windows-verzió        | SMB-verzió | Azure-beli virtuális gépeken csatlakoztatható | Helyszíni üzembe helyezhető |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Igen | Yes |
| Windows 10<sup>1</sup> | SMB 3.0 | Igen | Yes |
| Windows Server féléves csatorna<sup>2</sup> | SMB 3.0 | Igen | Yes |
| Windows Server 2016 | SMB 3.0 | Igen | Yes |
| Windows 8.1 | SMB 3.0 | Igen | Yes |
| Windows Server 2012 R2 | SMB 3.0 | Igen | Yes |
| Windows Server 2012 | SMB 3.0 | Igen | Yes |
| Windows 7<sup>3</sup> | SMB 2.1 | Igen | Nem |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Igen | Nem |

<sup>1</sup> Windows 10, 1507, 1607, 1803, 1809, 1903, 1909 és 2004.  
<sup>2</sup> Windows Server, 1809, 1903, 1909, 2004.  
<sup>3</sup> A Microsoft a Windows 7 és a Windows Server 2008 R2 rendszerhez készült rendszeres támogatás befejeződött. A biztonsági frissítések további támogatását csak a [bővített biztonsági frissítés (EUME) programon](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)keresztül lehet megvásárolni. Erősen ajánlott áttelepíteni ezeket az operációs rendszereket.

> [!Note]  
> Javasoljuk, hogy mindig a Windows-verziójához legutóbb kiadott frissítést használja.

## <a name="prerequisites"></a>Előfeltételek 

Győződjön meg arról, hogy a 445-ös port nyitva van: Az SMB protokollhoz szükséges, hogy a 445-ös TCP port nyitva legyen; a csatlakozás nem sikerül, ha a 445-ös port blokkolva van. Megtekintheti, hogy a tűzfal blokkolja-e a 445-es portot a `Test-NetConnection` parancsmaggal. Ha többet szeretne megtudni a letiltott 445-portok használatáról, tekintse meg a Windows hibaelhárítási útmutatójának [1. oka: a 445-es port letiltott](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) szakaszát.

## <a name="using-an-azure-file-share-with-windows"></a>Az Azure-fájlmegosztások használata Windowson
Az Azure-fájlmegosztások Windowson való használatához csatlakoztatnia kell azokat, azaz hozzájuk kell rendelnie egy meghajtó betűjelét vagy egy csatlakoztatási pont elérési útját, vagy pedig az [UNC-útvonalukon](/windows/win32/fileio/naming-a-file) keresztül érheti el azokat. 

Ez a cikk a Storage-fiók kulcsát használja a fájlmegosztás eléréséhez. A Storage-fiók kulcsa a Storage-fiókhoz tartozó rendszergazdai kulcs, beleértve a megosztott fájlmegosztás összes fájljának és mappájának rendszergazdai engedélyeit, valamint a Storage-fiókban található összes fájlmegosztást és egyéb tárolási erőforrást (Blobok, várólisták, táblák stb.). Ha ez nem elegendő a munkaterheléshez, [Azure file Sync](storage-sync-files-planning.md) lehet használni, vagy az [SMB protokollon alapuló identitás-alapú hitelesítést](storage-files-active-directory-overview.md)is használhat.

Az SMB-fájlmegosztást váró üzletági (LOB) alkalmazások Azure-ba való áthelyezése esetén gyakori megoldás az Azure-fájlmegosztások használata a dedikált Windows-fájlkiszolgálók Azure-beli virtuális gépeken történő futtatása helyett. Az üzletági alkalmazások egy Azure-fájlmegosztás használatára való sikeres migrálása érdekében fontos figyelembe venni, hogy számos üzletági alkalmazás, egy korlátozott rendszerengedélyekkel rendelkező dedikált szolgáltatásfiók környezetében fut a virtuális gép rendszergazdai fiókja helyett. Ezért győződjön meg róla, hogy az Azure-fájlmegosztáshoz szükséges hitelesítő adatokat a szolgáltatásfiók helyett a rendszergazdai fiókon keresztül csatlakoztatja/menti.

### <a name="mount-the-azure-file-share"></a>Az Azure-fájlmegosztás csatlakoztatása

A Azure Portal egy olyan parancsfájlt biztosít, amellyel közvetlenül a gazdagéphez csatlakoztathatja a fájlmegosztást. Javasoljuk, hogy ezt a megadott parancsfájlt használja.

A szkript beszerzése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Navigáljon ahhoz a Storage-fiókhoz, amely a csatlakoztatni kívánt fájlmegosztást tartalmazza.
1. Válassza a **Fájlmegosztások** lehetőséget.
1. Válassza ki a csatlakoztatni kívánt fájlmegosztást.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="például":::

1. Válassza a **Kapcsolódás** lehetőséget.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Képernyőfelvétel a fájlmegosztás összekapcsolási ikonjáról.":::

1. Válassza ki a meghajtóbetűjelet, amelyhez a megosztást csatlakoztatni szeretné.
1. Másolja a megadott parancsfájlt.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Példa szövege":::

1. Illessze be a szkriptet arra a gazdagépre, amelyre a fájlmegosztást csatlakoztatni szeretné, majd futtassa.

Ezzel csatlakoztatta az Azure-fájlmegosztást.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Az Azure-fájlmegosztás csatlakoztatása a Fájlkezelővel
> [!Note]  
> Vegye figyelembe, hogy a következő példák a Windows 10-re vonatkoznak, és a régebbi kiadásokban eltérhetnek. 

1. Nyissa meg a Fájlkezelőt. Ezt a Start menüből vagy a Win+E billentyűkombináció lenyomásával teheti meg.

1. Az ablak bal oldalán navigáljon a **számítógéphez** . Ez módosítja a szalagon elérhető menüket. A Számítógép menüben válassza a **Hálózati meghajtó csatlakoztatása** elemet.
    
    ![A Hálózati meghajtó csatlakoztatása legördülő menü képernyőképe](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Válassza ki a meghajtóbetűjelet, és adja meg az UNC elérési utat, az UNC elérési út formátuma `\\<storageAccountName>.file.core.windows.net\<fileShareName>` . Példa: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![A Hálózati meghajtó csatlakoztatása párbeszédpanel képernyőképe](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Felhasználónévként használja a tárfiók `AZURE\` előtaggal kiegészített nevét, jelszóként pedig egy tárfiókkulcsot.
    
    ![A hálózati hitelesítő adatok párbeszédpanelének képernyőképe](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Használja az Azure-fájlmegosztást igény szerint.
    
    ![Az Azure-fájlmegosztás most már csatlakoztatva van](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Amikor készen áll az Azure-fájlmegosztás leválasztására, kattintson a jobb gombbal a megosztás bejegyzésére a Fájlkezelő **Hálózati helyek** területén, és válassza a **Leválasztás** parancsot.

### <a name="accessing-share-snapshots-from-windows"></a>Megosztási pillanatképek elérése a Windowsban
A valamely szkript vagy szolgáltatás, például az Azure Backup használatával manuálisan vagy automatikusan létrehozott megosztási pillanatképek segítségével megtekintheti a megosztások, a könyvtárak, illetve a fájlmegosztásokban vagy a Windows rendszeren lévő adott fájlok korábbi verzióit. A megosztási pillanatképeket [Azure PowerShell](storage-how-to-use-files-powershell.md), az [Azure CLI](storage-how-to-use-files-cli.md)vagy a [Azure Portal](storage-how-to-use-files-portal.md)használatával végezheti el.

#### <a name="list-previous-versions"></a>Előző verziók listázása
Tallózással keresse meg a visszaállítani kívánt elemet vagy szülőelemet. Duplán rákattintva lépjen a kívánt könyvtárra. Kattintson a jobb gombbal, majd válassza a menü **Tulajdonságok** elemét.

![Kijelölt könyvtár helyi menüje](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Válassza az **Előző verziók** lehetőséget a könyvtár megosztási pillanatképeinek listázásához. A lista betöltése a hálózat sebességétől és a könyvtárban lévő megosztási pillanatképek számától függően néhány másodpercet is igénybe vehet.

![Előző verziók lap](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

A **Megnyitás** elemre kattintva nyithatja meg az egyes pillanatképeket. 

![Megnyitott pillanatkép](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Visszaállítás korábbi verzióról
A **Visszaállítás** elemre kattintva visszaállíthatja a teljes könyvtár tartalmát az eredeti helyre a megosztási pillanatkép készítésének időpontjában érvényes állapotra.

 ![Visszaállítás gomb a figyelmeztető üzenetben](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>A Windows/Windows Server védelme
Az Azure-fájlmegosztás Windowson való csatlakoztatásához a 445-ös portnak elérhetőnek kell lennie. Számos szervezet blokkolja a 445-ös portot az SMB 1 eredendő biztonsági kockázatai miatt. Az SMB-1, más néven CIFS (Common Internet File System), egy korábbi fájlrendszerprotokoll, amelyet a Windows és a Windows Server tartalmaz. Az SMB-1 egy elavult, nem hatékony és legfőképpen nem biztonságos protokoll. A jó hír az, hogy az Azure Files nem támogatja az SMB 1-et, és a Windows és Windows Server összes támogatott verziója lehetővé teszi az SMB 1 eltávolítását vagy letiltását. Mindig [erősen ajánlott](https://aka.ms/stopusingsmb1) az SMB 1 ügyfél és kiszolgáló eltávolítása vagy letiltása a Windowsban az Azure-fájlmegosztások használata előtt.

A következő táblázat részletes leírást ad az SMB 1 állapotáról minden Windows-verzión:

| Windows-verzió                           | SMB 1 alapértelmezett állapota | Letiltási/eltávolítási módszer       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Disabled (Letiltva)             | Eltávolítás Windows-funkcióval |
| Windows Server, 1709+ verziók            | Disabled (Letiltva)             | Eltávolítás Windows-funkcióval |
| Windows 10, 1709+ verziók                | Disabled (Letiltva)             | Eltávolítás Windows-funkcióval |
| Windows Server 2016                       | Engedélyezve              | Eltávolítás Windows-funkcióval |
| Windows 10, 1507-es, 1607-es és 1703-as verziók | Engedélyezve              | Eltávolítás Windows-funkcióval |
| Windows Server 2012 R2                    | Engedélyezve              | Eltávolítás Windows-funkcióval | 
| Windows 8.1                               | Engedélyezve              | Eltávolítás Windows-funkcióval | 
| Windows Server 2012                       | Engedélyezve              | Letiltás a beállításjegyzékkel       | 
| Windows Server 2008 R2                    | Engedélyezve              | Letiltás a beállításjegyzékkel       |
| Windows 7                                 | Engedélyezve              | Letiltás a beállításjegyzékkel       | 

### <a name="auditing-smb-1-usage"></a>Az SMB 1 használatának naplózása
> A Windows Server 2019, a Windows Server féléves csatornára (1709 és 1803 verziók), a Windows Server 2016, a Windows 10 (Versions 1507, 1607, 1703, 1709 és 1803), a Windows Server 2012 R2 és a Windows 8,1 alkalmazásra vonatkozik

Mielőtt eltávolítja az SMB 1-et a környezetből, naplózhatja az SMB 1 használatát, hogy nyomon tudja követni, károsodik-e bármelyik ügyfél a változtatás miatt. Ha kérelem merül fel az SMB 1-gyel rendelkező SMB-megosztásokkal kapcsolatban, a rendszer egy naplózási eseményt rögzít az eseménynaplóba az `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` útvonalon. 

> [!Note]  
> A Windows Server 2012 R2-es és a Windows 8.1-es verzión való naplózási támogatás engedélyezéséhez telepítse legalább a [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720) frissítést.

A naplózás engedélyezéséhez hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Az SMB 1 eltávolítása a Windows Serverről
> A Windows Server 2019, a Windows Server féléves csatornára (1709 és 1803 verziók), a Windows Server 2016, a Windows Server 2012 R2 verzióra vonatkozik.

Az SMB 1 Windows Server-példányról történő eltávolításához hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Az eltávolítási folyamat befejezéséhez indítsa újra a kiszolgálót. 

> [!Note]  
> A Windows 10 és a Windows Server 1709-es verziótól kezdődően az SMB 1 alapértelmezés szerint nincs telepítve, és az SMB 1 ügyfél és az SMB 1 kiszolgáló különálló Windows-funkciókkal rendelkezik. Azt javasoljuk, hogy ne telepítse az SMB 1 kiszolgálót (`FS-SMB1-SERVER`) és az SMB 1 ügyfelet (`FS-SMB1-CLIENT`).

### <a name="removing-smb-1-from-windows-client"></a>Az SMB 1 eltávolítása a Windows-ügyfélről
> Érintett kiadások: Windows 10 (1507-es, 1607-es, 1703-as, 1709-es és 1803-as verziók) és Windows 8.1

Az SMB 1 Windows-ügyfélről történő eltávolításához hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Az eltávolítási folyamat befejezéséhez indítsa újra a számítógépet.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Az SMB 1 letiltása a Windows/Windows Server korábbi verzióin
> Érintett kiadások: Windows Server 2012, Windows Server 2008 R2 és Windows 7

Az SMB 1 nem távolítható el teljesen a Windows/Windows Server korábbi verzióin, a beállításjegyzék segítségével azonban letiltható. Az SMB-1 letiltásához hozzon létre egy új, `DWORD` típusú `SMB1` beállításkulcsot `0` értékkel a `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` útvonalon.

Ezt a következő PowerShell-parancsmaggal egyszerűen megteheti:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Miután létrehozta a beállításkulcsot, indítsa újra a kiszolgálót az SMB 1 letiltásához.

### <a name="smb-resources"></a>SMB-erőforrások
- [Az SMB 1 használatának leállítása](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 termék adategyeztető központ](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Az SMB 1 felfedezése a környezetben DSCEA használatával](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [Az SMB 1 letiltása csoportházirend használatával](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:
- [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
- [Gyakori kérdések](./storage-files-faq.md)
- [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)