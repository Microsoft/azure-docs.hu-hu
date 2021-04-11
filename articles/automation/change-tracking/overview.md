---
title: A Azure Automation Change Tracking és a leltár áttekintése
description: Ez a cikk ismerteti a Change Tracking és a leltár szolgáltatást, amely segít azonosítani a szoftvereket és a Microsoft szolgáltatásbeli módosításokat a környezetben.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: ed29def305bfa33a0a947a331775de89275e5f7f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220866"
---
# <a name="change-tracking-and-inventory-overview"></a>A Change Tracking és a leltár áttekintése

Ez a cikk a Azure Automation Change Tracking és leltározását ismerteti. Ez a szolgáltatás nyomon követi az Azure-ban, a helyszínen és más felhőalapú környezetekben üzemeltetett virtuális gépek változásait, így könnyebben azonosíthatja az üzemeltetési és környezeti problémákat a Distribution Package Manager által kezelt szoftverekkel. A Change Tracking és a leltár által követett elemek a következők:

- Windows-szoftver
- Linuxos szoftverek (csomagok)
- Windows-és Linux-fájlok
- Windows-beállításkulcsok
- Microsoft-szolgáltatások
- Linux-démonok

> [!NOTE]
> A Azure Resource Manager tulajdonságok változásainak nyomon követéséhez tekintse meg az Azure Resource Graph [változási előzményeit](../../governance/resource-graph/how-to/get-resource-changes.md).

A Change Tracking és a leltár a [Azure Security Center file Integrity monitoring (FIM)](../../security-center/security-center-file-integrity-monitoring.md) használatával vizsgálja meg az operációs rendszer és az alkalmazások fájljait, valamint a Windows beállításjegyzékét. Míg a FIM figyeli ezeket az entitásokat, Change Tracking és leltárt natívan nyomon követi:

- A szoftver módosításai
- Microsoft-szolgáltatások
- Linux-démonok

A Change Tracking és a leltárban foglalt összes szolgáltatás engedélyezése további díjakat eredményezhet. A továbblépés előtt tekintse át az [Automation díjszabását](https://azure.microsoft.com/pricing/details/automation/) és a [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/).

A Change Tracking és a leltár Azure Monitor naplókba továbbítja az adatokat, és az összegyűjtött adatokat egy Log Analytics munkaterületen tárolja. A fájlok integritásának figyelése (FIM) szolgáltatás csak akkor érhető el, ha engedélyezve van az **Azure Defender for Servers** . További információkért tekintse meg a Azure Security Center [díjszabását](../../security-center/security-center-pricing.md) . A FIM feltölti az adatait ugyanarra a Log Analytics munkaterületre, mint amelyet az adatok Change Tracking és leltárból való tárolásához hoztak létre. Javasoljuk, hogy figyelje a csatolt Log Analytics munkaterületet, hogy nyomon követhesse a pontos használatot. A Azure Monitor naplók adatfelhasználásának elemzésével kapcsolatos további információkért lásd: [a használat és a költséghatékonyság kezelése](../../azure-monitor/logs/manage-cost-storage.md).

A Log Analytics munkaterülethez csatlakozó gépek a [log Analytics ügynökkel](../../azure-monitor/agents/log-analytics-agent.md) gyűjtenek adatokat a telepített szoftverek, a Microsoft-szolgáltatások, a Windows-beállításjegyzék és-fájlok, valamint a figyelt kiszolgálókon futó Linux-démonok változásairól. Ha az adatokat elérhetővé teszi, az ügynök elküldi a feldolgozásra Azure Monitor naplókba. Azure Monitor naplók a fogadott adatokra vonatkozó logikát alkalmaznak, rögzítik, és elemzés céljából elérhetővé teszik.

> [!NOTE]
> A Change Tracking és a leltár használatához Log Analytics munkaterületet kell összekapcsolni az Automation-fiókkal. A támogatott régiók végleges listáját az [Azure Workspace-hozzárendelések](../how-to/region-mappings.md)című témakörben tekintheti meg. A régió-hozzárendelések nem befolyásolják a virtuális gépek az Automation-fióktól eltérő régióban való felügyeletének képességét.

Szolgáltatóként több ügyfél-bérlőt is felkészített az [Azure világítótoronyba](../../lighthouse/overview.md). Az Azure Lighthouse lehetővé teszi, hogy egyszerre több Azure Active Directory (Azure AD) bérlőre kiterjedő műveleteket hajtson végre, így a felügyeleti feladatok, például a Change Tracking és a leltár hatékonyabban használhatók a felelős bérlők számára. A Change Tracking és a leltár több előfizetésben lévő gépeket is kezelhet ugyanazon a bérlőn vagy az Azure-beli [delegált erőforrás-kezelést](../../lighthouse/concepts/azure-delegated-resource-management.md)használó bérlők között.

## <a name="current-limitations"></a>Aktuális korlátozások

A Change Tracking és a leltár nem támogatja, vagy a következő korlátozások vonatkoznak rá:

- Rekurzió a Windows beállításjegyzékének nyomon követéséhez
- Hálózati fájlrendszerek
- Különböző telepítési módszerek
- *a Windowson tárolt **. exe** fájlok
- A **Maximális fájlméret** oszlop és az értékek nem használhatók az aktuális implementációban.
- Ha egy 30 perces gyűjtési ciklusban több mint 2500 fájlt próbál összegyűjteni, a Change Tracking és a leltározás teljesítménye csökkenhet.
- Ha a hálózati forgalom magas, a rekordok módosítása akár hat órát is igénybe vehet.
- Ha egy számítógép vagy kiszolgáló leállítása közben módosítja a konfigurációt, akkor az előző konfigurációhoz tartozó módosításokat is közzéteheti.
- Gyorsjavítások frissítéseinek gyűjtése a Windows Server 2016 Core RS3-gépeken.
- Előfordulhat, hogy a Linux-démonok módosult állapotot mutatnak, de nem történt változás. Ez a probléma a `SvcRunLevels` Azure monitor [konfigurációváltozás](/azure/azure-monitor/reference/tables/configurationchange) táblázatában lévő adatírások miatt fordul elő.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Change Tracking és a leltár minden olyan operációs rendszeren támogatott, amely megfelel Log Analytics ügynök követelményeinek. A Log Analytics ügynök által jelenleg támogatott Windows és Linux operációsrendszer-verziók listáját a [támogatott operációs rendszerek](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) című részben tekintheti meg.

A TLS 1,2-hez szükséges ügyfél-követelmények megismeréséhez lásd: [tls 1,2 kényszerítés Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

### <a name="python-requirement"></a>Python-követelmény

A Change Tracking és a leltár csak a Python2 támogatja. Ha a gép olyan disztribúciót használ, amely alapértelmezés szerint nem tartalmazza a Python 2-et, akkor telepítenie kell azt. A következő minta parancsok a Python 2 különböző disztribúciókban való telepítését teszik ki.

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE `zypper install -y python2`

A python2 végrehajtható fájljának aliasnak kell lennie a *Pythonhoz*.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A Change Tracking és a leltárhoz szükséges portok, URL-címek és egyéb hálózatkezelési adatok részletes ismertetését [Azure Automation hálózati konfigurációban](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory) találja.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

A következő módokon engedélyezheti a Change Tracking és a leltárt:

- Egy vagy több Azure-beli és nem Azure-beli gép [automatizálási fiókjából](enable-from-automation-account.md) .

- Manuálisan nem Azure-beli gépek esetén, beleértve az [Azure arc-kompatibilis kiszolgálókon](../../azure-arc/servers/overview.md)regisztrált gépeket vagy kiszolgálókat. A hibrid gépek esetében javasoljuk, hogy Log Analytics a Windows-ügynököt az [Azure arc-kompatibilis kiszolgálókhoz](../../azure-arc/servers/overview.md)csatlakoztassa, majd a Azure Policy használatával rendelje hozzá a [log Analytics Agent üzembe helyezését a *Linux* vagy a *Windows* Azure arc Machines](../../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjéhez. Ha azt tervezi, hogy a gépeket Azure Monitor for VMs használatával is figyeli, használja az [Enable Azure monitor for VMS](../../governance/policy/samples/built-in-initiatives.md#monitoring) Initiative parancsot.

- Egyetlen Azure-beli virtuális géphez a Azure Portal [virtuális gép lapján](enable-from-vm.md) . Ez a forgatókönyv Linux és Windows rendszerű virtuális gépek esetében érhető el.

- [Több Azure](enable-from-portal.md) -beli virtuális gép esetén válassza ki őket a Azure Portal Virtual Machines lapján.

## <a name="tracking-file-changes"></a>A fájlok változásainak követése

A Windows és a Linux rendszerű fájlok változásainak nyomon követéséhez a Change Tracking és a leltár a fájlok MD5 kivonatait használja. A szolgáltatás a kivonatok használatával észleli, hogy történtek-e változások az utolsó leltár óta.

## <a name="tracking-file-content-changes"></a>A fájl tartalmának változásai nyomon követése

A Change Tracking és a leltár lehetővé teszi egy Windows-vagy Linux-fájl tartalmának megtekintését. Egy fájl minden módosítása esetén a Change Tracking és a leltár egy [Azure Storage-fiókban](../../storage/common/storage-account-create.md)tárolja a fájl tartalmát. Egy fájl nyomon követése után megtekintheti annak tartalmát a módosítás előtt vagy után. A fájl tartalma beágyazott vagy egymás mellett is megtekinthető.

![Egy fájl változásainak megtekintése](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Beállításkulcsok nyomon követése

A Change Tracking és a leltár lehetővé teszi a Windows-beállításkulcsok változásainak figyelését. A figyeléssel megadhatja azokat a bővíthetőségi pontokat, amelyekben a harmadik féltől származó kód és a kártevő is aktiválható. Az alábbi táblázat az előre konfigurált (de nem engedélyezett) beállításkulcsokat sorolja fel. A kulcsok nyomon követéséhez engedélyeznie kell mindegyiket.

> [!div class="mx-tdBreakAll"]
> |Beállításjegyzék kulcsa | Cél |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Az indításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | A leállításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Figyeli azokat a kulcsokat, amelyek betöltődik, mielőtt a felhasználó bejelentkezik a Windows-fiókba. A kulcs a 64 bites számítógépeken futó 32 bites alkalmazásokhoz használatos.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Az Alkalmazásbeállítások változásainak figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Azokat a helyi menüparancsokat figyeli, amelyek közvetlenül a Windows Intézőben csatlakoztathatók, és általában folyamatban van a **explorer.exe** használatával történő futtatás.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | A figyeli a közvetlenül a Windows Intézőben összekapcsoló Hook-kezelőket, és általában a **explorer.exe** használatával futtatja a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők az ikon átfedését kezelő regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők a 64 bites számítógépeken futó 32 bites alkalmazásokhoz tartozó ikon átfedési kezelői regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére és a Navigálás vezérlésére szolgál.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére, valamint a 64 bites számítógépeken futó 32 bites alkalmazások navigálásának vezérlésére használatos.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Figyelők az új Internet Explorer-bővítményekhez, például az egyéni eszközök menüihez és az egyéni eszköztárak gombjaihoz.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Az új Internet Explorer-bővítmények, például a 64 bites számítógépeken futó 32 bites alkalmazásokhoz készült egyéni eszköztárak és egyéni eszköztárgombok figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli. Hasonló a **system.ini** fájl [Drivers] szakaszához.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli a 64 bites számítógépeken futó 32 bites alkalmazásokhoz. Hasonló a **system.ini** fájl [Drivers] szakaszához.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Az ismert vagy leggyakrabban használt rendszerdll-fájlok listáját figyeli. A figyelés megakadályozza, hogy a felhasználók a rendszer DLL-fájljainak eldobásával kihasználják a gyenge alkalmazásspecifikus címtárbeli engedélyeket.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | A Windows rendszerhez készült interaktív bejelentkezési támogatási modellben figyeli azon csomagok listáját, amelyekről értesítéseket kaphat a **winlogon.exeról**.

## <a name="recursion-support"></a>Rekurzió támogatása

A Change Tracking és a leltár támogatja a rekurziót, ami lehetővé teszi helyettesítő karakterek megadását a címtárak közötti nyomkövetés egyszerűsítése érdekében. A rekurzió olyan környezeti változókat is biztosít, amelyek lehetővé teszik a fájlok különböző környezetekben történő nyomon követését több vagy dinamikus meghajtó nevével. A következő lista a rekurzió konfigurálásakor szükséges általános tudnivalókat tartalmazza:

- Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.

- A helyettesítő karaktereket csak a fájl elérési útjának utolsó szegmensében használhatja, például **c:\mappa \\ file** _ vagy _ */etc/*. conf * *.

- Ha egy környezeti változónak érvénytelen az elérési útja, az érvényesítés sikeres lesz, de az elérési út meghiúsul.

- Az elérési út beállításakor el kell kerülnie az általános elérési utak nevét, mivel az ilyen típusú beállítás túl sok mappa átjárását okozhatja.

## <a name="change-tracking-and-inventory-data-collection"></a>Change Tracking és leltározási adatgyűjtés

A következő táblázat a Change Tracking és a leltár által támogatott változások típusának adatgyűjtési gyakoriságát mutatja. Minden típus esetében az aktuális állapot adatpillanatképe legalább 24 óránként frissül.

| **Típus módosítása** | **Gyakoriság** |
| --- | --- |
| Windows beállításjegyzék | 50 perc |
| Windows-fájl | 30 perc |
| Linux-fájl | 15 perc |
| Microsoft-szolgáltatások | 10 másodperc és 30 perc között</br> Alapértelmezett: 30 perc |
| Linux-démonok | 5 perc |
| Windows-szoftver | 30 perc |
| Linuxos szoftverek | 5 perc |

A következő táblázat a nyomon követett elemek korlátozásait mutatja gépenként a Change Tracking és a leltár esetében.

| **Erőforrás** | **Korlátot** |
|---|---|---|
|Fájl|500|
|Regisztrációs adatbázis|250|
|Windows-szoftver (nem tartalmazza a gyorsjavításokat) |250|
|Linux-csomagok|1250|
|Szolgáltatások|250|
|Démonok|250|

A Change Tracking és a leltárt használó gépek átlagos Log Analytics adatfelhasználása körülbelül 40 MB/hó, a környezettől függően. A Log Analytics munkaterület használati és becsült költségek funkciójával megtekintheti a Change Tracking és a leltár által betöltött adatokat egy használati diagramon. Ezzel az adatnézettel kiértékelheti az adathasználatot, és meghatározhatja, hogyan befolyásolja a számláját. Lásd: [a használat és a becsült költségek megismerése](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Microsoft-szolgáltatásokra vonatkozó adatkezelés

A Microsoft-szolgáltatások alapértelmezett gyűjtési gyakorisága 30 perc. A gyakoriságot a **beállítások szerkesztése** elemre a **Microsoft-szolgáltatások** lapon található csúszka használatával konfigurálhatja.

![Microsoft-szolgáltatások csúszka](./media/overview/windowservices.png)

A teljesítmény optimalizálása érdekében a Log Analytics ügynök csak a változásokat követi nyomon. Ha a szolgáltatás visszatér az eredeti állapotába, a magas küszöbérték beállítása nem fog módosulni. Ha a gyakoriságot kisebb értékre állítja, az esetlegesen kihagyott módosításokat is elvégezheti.

> [!NOTE]
> Míg az ügynök 10 másodperces intervallumban nyomon követheti a változásokat, az adat továbbra is néhány percet vesz igénybe, hogy megjelenjen a Azure Portal. A portálon megjelenő időpontokban végrehajtott módosítások továbbra is nyomon követhetők és naplózhatók.

## <a name="support-for-alerts-on-configuration-state"></a>Riasztások támogatása a konfiguráció állapotában

Az Change Tracking és a leltár kulcsfontosságú funkciója, hogy a hibrid környezet konfigurációs állapotának változásairól riasztást küld. Számos hasznos művelet aktiválható a riasztásokra adott válaszként. Például az Azure functions, az Automation runbookok, a webhookok és a hasonló műveletek. A **c:\Windows\System32\drivers\etc\hosts** -fájl változásainak riasztása egy adott gépen a riasztások helyes alkalmazása Change Tracking és leltározási adatként. Számos további forgatókönyv is létezik a riasztásokhoz, beleértve a következő táblázatban definiált lekérdezési forgatókönyveket is.

|Lekérdezés  |Leírás  |
|---------|---------|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "files" és a FileSystemPath a "c: \\ Windows \\ System32 \\ Drivers \\ " kifejezést tartalmazza|A rendszerkritikus fájlok változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, hogy a FieldsChanged tartalmazza-e a "FileContentChecksum" és a FileSystemPath = = "c: \\ Windows system32 illesztőprogramok, illetve a \\ \\ \\ \\ gazdagépek"|Hasznos a legfontosabb konfigurációs fájlok módosításainak nyomon követéséhez.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "WindowsServices" és a SvcName tartalmazza a "W3SVC" és a SvcState = = "leállítva"|A rendszerkritikus szolgáltatások változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "DAEMONS" és a SvcName tartalmazza az "SSH" és a SvcState! = "Running"|A rendszerkritikus szolgáltatások változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "szoftver" és a ChangeCategory = = "hozzáadva"|Olyan környezetekhez hasznos, amelyeknek szükségük van a zárolt szoftveres konfigurációkra.|
|ConfigurationData <br>&#124;, ahol a SoftwareName tartalmazza a "figyelési ügynököt" és a CurrentVersion! = "8.0.11081.0"|Hasznos, ha azt látja, hogy mely gépeken van telepítve elavult vagy nem megfelelő szoftververzió. Ez a lekérdezés a legutóbbi jelentett konfigurációs állapotot jelenti, de nem jelenti a módosításokat.|
|Konfigurációváltozás <br>&#124;, ahol a RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ Software \\ Microsoft \\ Windows \\ CurrentVersion \\ QualityCompat"| Hasznos a kritikus vírusvédelmi kulcsok változásainak nyomon követéséhez.|
|Konfigurációváltozás <br>&#124;, ahol a RegistryKey a következőt tartalmazza: @ "HKEY_LOCAL_MACHINE \\ System \\ CurrentControlSet \\ Services \\ SharedAccess \\ Parameters \\ FirewallPolicy"| Hasznos a tűzfalbeállítások változásainak nyomon követéséhez.|

## <a name="next-steps"></a>Következő lépések

- Az Automation-fiókból való engedélyezéssel kapcsolatban lásd: [change Tracking és leltár engedélyezése Automation-fiókból](enable-from-automation-account.md).

- A Azure Portalból való engedélyezéshez tekintse meg [a Change Tracking és a leltár engedélyezése a Azure Portalből](enable-from-portal.md)című témakört.

- A runbook való engedélyezéssel kapcsolatban lásd: [change Tracking és leltár engedélyezése runbook](enable-from-runbook.md).

- Az Azure-beli virtuális gépekről a következő témakörben talál további információt: [change Tracking és leltár engedélyezése Azure](enable-from-vm.md)-beli virtuális gépről.
