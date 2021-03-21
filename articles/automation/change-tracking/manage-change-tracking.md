---
title: A Change Tracking és a leltár kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan használható a Change Tracking és a leltár a szoftverek és a Microsoft-szolgáltatások változásainak nyomon követésére a környezetben.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: dff314f3c9fb72c565a7c2d522694d533c487895
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572647"
---
# <a name="manage-change-tracking-and-inventory"></a>A Change Tracking és az Inventory kezelése

Új fájl vagy beállításkulcs a nyomon követéséhez való hozzáadásakor Azure Automation engedélyezi a [change Tracking és a leltár](overview.md)számára. Ez a cikk ismerteti a nyomon követés konfigurálását, a nyomon követési eredmények áttekintését és a riasztások kezelését a változások észlelésekor.

A cikkben ismertetett eljárások használata előtt győződjön meg arról, hogy engedélyezte a Change Tracking és a leltárt a virtuális gépeken a következő módszerek egyikével:

* [A Change Tracking és az Inventory engedélyezése Automation-fiókból](enable-from-automation-account.md)
* [Change Tracking és leltár engedélyezése a Azure Portal tallózásával](enable-from-portal.md)
* [A Change Tracking és az Inventory engedélyezése runbookból](enable-from-runbook.md)
* [A Change Tracking és az Inventory engedélyezése Azure-beli virtuális gépről](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>A központi telepítés hatókörének korlátozása

A Change Tracking és a leltár a munkaterületen belüli hatókör-konfigurációval célozza meg a számítógépeket, hogy fogadják a módosításokat. További információ: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](manage-scope-configurations.md).

## <a name="track-files"></a>Fájlok nyomon követése

A fájlok és mappák/könyvtárak változásainak nyomon követéséhez használhatja a Change Tracking és a leltárt. Ez a szakasz azt ismerteti, hogyan konfigurálhatja a Windows és Linux rendszereken a fájlok nyomon követését.

### <a name="configure-file-tracking-on-windows"></a>A fájlok nyomon követésének konfigurálása Windows rendszeren

A következő lépésekkel konfigurálhatja a Windows rendszerű számítógépeken a fájlok nyomon követését:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a bemenet alapján szűri a javaslatokat. Válassza az **Automation-fiókok** elemet.

3. Az Automation-fiókok listájában válassza ki azt a fiókot, amelyet a Change Tracking és a leltár engedélyezésekor választott ki.

4. Az Automation-fiókban válassza a **change Tracking** elemet a **konfiguráció kezelése** területen.

5. Válassza a **beállítások szerkesztése** (a fogaskerék szimbóluma) lehetőséget.

6. A munkaterület konfigurációja lapon válassza a **Windows-fájlok** lehetőséget, majd kattintson a **+ Hozzáadás** gombra a nyomon követett új fájl hozzáadásához.

7. A Windows-fájl hozzáadása Change Tracking ablaktáblán adja meg a nyomon követni kívánt fájl vagy mappa adatait, majd kattintson a **Save (Mentés**) gombra. A következő táblázat az információhoz használható tulajdonságokat határozza meg.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Igaz, ha a beállítás alkalmazva van, ellenkező esetben hamis.        |
    |Elem neve     | A nyomon követett fájl rövid neve.        |
    |Group     | Egy csoport neve a fájlok logikai csoportosításához.        |
    |Elérési út megadása     | A fájl keresésének elérési útja, például **c:\Temp \\ \* . txt**. Használhat környezeti változókat is, például: `%winDir%\System32\\\*.*` .       |
    |Elérési út típusa     | Az elérési út típusa A lehetséges értékek: fájl és mappa.        |    
    |Rekurzió     | True (igaz), ha a rendszer rekurziót használ a nyomon követett elem keresésekor, máskülönben hamis értéket ad.        |    
    |Fájl tartalmának feltöltése | True (igaz) – a fájl tartalmának feltöltése a nyomon követett változásokon, ellenkező esetben hamis.|

    Ha úgy tervezi, hogy a fájlok és mappák figyelését helyettesítő karakterekkel konfigurálja, vegye figyelembe a következőket:

    - Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
    - Helyettesítő karakterek csak az elérési út utolsó szegmensében használhatók, például *C:\folder\file* vagy */etc/*. conf *
    - Ha egy környezeti változó érvénytelen elérési utat tartalmaz, az érvényesítés sikeres lesz, de az elérési út sikertelen lesz a leltár futtatásakor.
    - Az elérési út beállításakor kerülje el az általános elérési utakat, például a *c:*. * * értéket, ami túl sok mappát fog áthaladni.

8. Győződjön meg arról, hogy a **fájl tartalmának feltöltéséhez** igaz értéket ad meg. Ezzel a beállítással engedélyezhető a fájl tartalmának követése a megadott elérési úthoz.

### <a name="configure-file-tracking-on-linux"></a>A fájlok nyomon követésének konfigurálása Linuxon

A következő lépésekkel konfigurálhatja a fájlok követését a Linux rendszerű számítógépeken:

1. Válassza a **beállítások szerkesztése** (a fogaskerék szimbóluma) lehetőséget.

2. A munkaterület konfigurációja lapon válassza a **Linux-fájlok**, majd a **+ Hozzáadás** lehetőséget a nyomon követett új fájl hozzáadásához.

3. A **change Tracking Linux-fájl hozzáadása** lapon adja meg a nyomon követni kívánt fájl vagy könyvtár adatait, majd kattintson a **Mentés** gombra. A következő táblázat az információhoz használható tulajdonságokat határozza meg.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Igaz, ha a beállítás alkalmazva van, ellenkező esetben hamis.        |
    |Elem neve     | A nyomon követett fájl rövid neve.        |
    |Group     | Egy csoport neve a fájlok logikai csoportosításához.        |
    |Elérési út megadása     | A fájl keresésének elérési útja, például **/etc/*. conf**.       |
    |Elérési út típusa     | Az elérési út típusa A lehetséges értékek a fájl és a könyvtár.        |
    |Rekurzió     | True (igaz), ha a rendszer rekurziót használ a nyomon követett elem keresésekor, máskülönben hamis értéket ad.        |
    |Sudo használata     | Igaz értéke a sudo használata az elem keresésekor, ellenkező esetben hamis.         |
    |Hivatkozások     | Ez a beállítás határozza meg, hogyan kezelje a szimbolikus hivatkozásokat a címtárak bejárásakor. Lehetséges értékek:<br> Mellőzés – figyelmen kívül hagyja a szimbolikus hivatkozásokat, és nem tartalmazza a hivatkozott fájlokat/címtárakat.<br>Követés – követi a szimbolikus hivatkozásokat a rekurzió során, és tartalmazza a hivatkozott fájlokat/címtárakat is.<br>Kezelés – követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom módosítását.<br>**Megjegyzés:** A kezelés lehetőség nem ajánlott, mert nem támogatja a fájl tartalmának lekérését.    |
    |Fájl tartalmának feltöltése | True (igaz) – a fájl tartalmának feltöltése a nyomon követett változásokon, ellenkező esetben hamis. |

4. Győződjön meg arról, hogy a **fájl tartalmának feltöltéséhez** **igaz** értéket ad meg. Ezzel a beállítással engedélyezhető a fájl tartalmának követése a megadott elérési úthoz.

   ![Linux-fájl hozzáadása](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Fájl tartalmának követése

A fájl tartalmának nyomon követése lehetővé teszi egy fájl tartalmának megtekintését a követett változások előtt és után. A szolgáltatás minden változás után menti a fájl tartalmát egy [Storage-fiókba](../../storage/common/storage-account-overview.md) . Íme néhány, a fájl tartalmának követésére szolgáló szabály:

* A fájl tartalmának tárolásához a Resource Manager-alapú üzemi modellt használó standard Storage-fiók szükséges.
* Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező kapcsolatokat elfogadnak. Ha úgy állította be a Storage-fiókot, hogy csak bizonyos forgalmat engedélyezzen, módosítania kell a konfigurációs szabályokat, hogy az Automation-fiókja csatlakozhasson hozzá. Lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md).
* Ne használja a prémium és a klasszikus telepítési modell Storage-fiókokat. Lásd: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-account-create.md).
* A Storage-fiókot csak egy Automation-fiókhoz lehet kapcsolni.
* Az Automation-fiókban engedélyezni kell a Change Tracking és a leltárt.

### <a name="enable-tracking-for-file-content-changes"></a>A fájl tartalmának változásai követésének engedélyezése

A következő lépésekkel engedélyezheti a fájlok tartalmának módosítását:

1. Válassza a **beállítások szerkesztése** (a fogaskerék szimbóluma) lehetőséget.

2. Válassza a **fájl tartalma** lehetőséget, majd válassza a **hivatkozás** lehetőséget. Ez a beállítás megnyitja a **tartalom helyének hozzáadása Change Tracking** oldalon.

   ![Tartalom helyének hozzáadása](./media/manage-change-tracking/enable.png)

3. Válassza ki a fájl tartalmának tárolásához használni kívánt előfizetést és Storage-fiókot.

5. Ha engedélyezni szeretné a fájl tartalmának nyomon követését az összes meglévő követett fájlnál, válassza **a be** lehetőséget a **fájl tartalmának feltöltéséhez az összes beállításnál**. Ezt a beállítást később is megváltoztathatja az egyes fájlok elérési útjára.

   ![Storage-fiók beállítása](./media/manage-change-tracking/storage-account.png)

6. A Change Tracking és a leltár megjeleníti a Storage-fiók és a közös hozzáférésű aláírás (SAS) URI-azonosítóját, amikor engedélyezi a fájl tartalmának módosítását. Az aláírások 365 nap után lejárnak, és újból létrehozhatja őket **az újbóli** létrehozás lehetőség kiválasztásával.

   ![Fiók kulcsainak listázása](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Követett fájl tartalmának megtekintése

Miután Change Tracking és a leltár észleli a nyomon követett fájl módosítását, megtekintheti a fájl tartalmát a változás részletei panelen.  

![Változások listázása](./media/manage-change-tracking/change-list.png)

1. Az Automation-fiók **change Tracking (változások nyomon követése** ) lapján válasszon ki egy fájlt a módosítások listájában, majd válassza a **fájl tartalmának módosításait** a fájl tartalmának megtekintéséhez. A változás részletei panelen az egyes tulajdonságokhoz tartozó Fájlinformációk előtt és után megjelenő szabvány látható.

   ![Változás részletei](./media/manage-change-tracking/change-details.png)

2. Egymás melletti nézetben tekinti meg a fájl tartalmát. A **beágyazott** nézetet kiválasztva megjelenítheti a módosításokat.

## <a name="track-registry-keys"></a>Beállításkulcsok nyomon követése

A következő lépésekkel konfigurálhatja a beállításjegyzék-kulcsok nyomon követését Windows rendszerű számítógépeken:

1. Az Automation-fiók **change Tracking (változások nyomon követése** ) lapján válassza a **beállítások szerkesztése** (a fogaskerék szimbóluma) lehetőséget.

2. A munkaterület konfigurációja lapon válassza a **Windows beállításjegyzék** lehetőséget.

3. Válassza a **+ Hozzáadás** lehetőséget a nyomon követett új beállításkulcs hozzáadásához.

4. A **change Tracking Windows beállításjegyzék hozzáadása** lapon adja meg a nyomon követett kulcs adatait, majd válassza a **Mentés** lehetőséget. A következő táblázat az információhoz használható tulajdonságokat határozza meg. A beállításjegyzék elérési útjának megadásakor a kulcsnak és nem értéknek kell lennie.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Értéke TRUE (igaz), ha egy beállítás alkalmazva van, ellenkező esetben hamis.        |
    |Elem neve     | A nyomon követni kívánt beállításkulcs rövid neve.        |
    |Group     | Csoport neve a beállításkulcsok logikai csoportosításához.        |
    |Windows-beállításkulcs   | Az elérési úttal rendelkező kulcsnév, például: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Naplók keresése a változási rekordokhoz

A változási rekordok esetében különböző kereséseket végezhet a Azure Monitor naplókon. Ha megnyitotta a Change Tracking (változások nyomon követése) lapot, kattintson **log Analytics** a naplók lap megnyitásához. A következő táblázat a változási rekordokra vonatkozó példákat tartalmaz.

|Lekérdezés  |Description  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Megjeleníti az automatikusra beállított és a leállítottként jelentett Microsoft-szolgáltatások legújabb leltározási rekordjait. Az eredmények a megadott számítógépnév és számítógép legutóbbi rekordjára korlátozódnak.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Megjeleníti a törölt szoftverek módosítási rekordjait.|

## <a name="next-steps"></a>Következő lépések

* A hatókör-konfigurációkkal kapcsolatos további információkért lásd: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](manage-scope-configurations.md).
* Ha Azure Monitor-naplókban tárolt naplókat kell keresnie, tekintse meg a Azure Monitor naplókban végzett [keresések](../../azure-monitor/logs/log-query-overview.md)című témakört.
* Ha a telepítésekkel fejeződött be, tekintse meg a [change Tracking és a leltár eltávolítása](remove-feature.md)című témakört.
* A virtuális gépek Change Tracking és leltárból való törléséhez lásd: [virtuális gépek eltávolítása Change Tracking és leltárból](remove-vms-from-change-tracking.md).
* A szolgáltatási hibák elhárításáról lásd: [change Tracking és leltározási problémák elhárítása](../troubleshoot/change-tracking.md).
