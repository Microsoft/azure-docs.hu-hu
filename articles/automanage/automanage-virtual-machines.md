---
title: Azure Automanage virtuális gépekhez
description: További információ Azure Automanage virtuális gépekhez való Azure Automanage kapcsolatban.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 514f1af2a1b120254840986fc5ceb803dfc24345
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363376"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage virtuális gépekhez

Ez a cikk a virtuális gépek Azure Automanage kapcsolatos információkat tartalmaz, amelyek a következő előnyökkel járnak:

- Virtuális gépek intelligens létrehozása az ajánlott eljárások kiválasztásához – Azure-szolgáltatások
- Automatikusan konfigurálja az egyes szolgáltatásokat az Azure ajánlott eljárásai szerint
- A sodródás figyelői és az észleléskor megfelelők
- Egyszerű felhasználói élményt biztosít (pont, kattintás, beállítás, elfelejtés)


## <a name="overview"></a>Áttekintés

Azure Automanage a virtuális gépekhez való használatra vonatkozó szolgáltatás szükségtelenné válik a virtuális gép számára előnyös egyes szolgáltatások felderítése, a használatuk megismerése és konfigurálása az Azure-ban. Ezeket a szolgáltatásokat az Azure ajánlott eljárásokat kezelő szolgáltatásainak tekintjük, és segítenek növelni a virtuális gépek megbízhatóságát, biztonságát és felügyeletét. Ilyen szolgáltatás például az [Azure Update Management](../automation/update-management/overview.md) és [a Azure Backup.](../backup/backup-overview.md)

A virtuális gépek Azure Automanage után minden ajánlott eljárás szerint konfigurálva vannak az ajánlott beállítások. Az ajánlott eljárások az egyes szolgáltatásokhoz eltérőek. Erre jó példa lehet Azure Backup, amikor az ajánlott eljárás a virtuális gép biztonsági létrehozása naponta egyszer, és hat hónapos megőrzési időtartam.

Azure Automanage automatikusan figyeli a sodródásokat, és észleli azok megfelelőségét. Ez azt jelenti, hogy ha a virtuális gépe Azure Automanage-hoz van beállítva, akkor nem csak az Azure ajánlott eljárásai szerint konfiguráljuk, hanem figyeljük a gépet is, hogy az folyamatosan megfeleljen ezeknek az ajánlott eljárásoknak a teljes életciklus során. Ha a virtuális gép nem tér el ezektől az eljárásoktól (például ha egy szolgáltatás ki van kapcsolva), kijavítjuk azt, és visszaveszi a gépet a kívánt állapotba.

## <a name="prerequisites"></a>Előfeltételek

A virtuális gépeken a virtuális gépeken való Azure Automanage előtt több előfeltételt is figyelembe kell venni.

- Támogatott [Windows Server-verziók és](automanage-windows-server.md#supported-windows-server-versions) [Linux-disztribúciók](automanage-linux.md#supported-linux-distributions-and-versions)
- A virtuális gépeknek támogatott régióban kell lennie (lásd alább)
- A felhasználónak megfelelő engedélyekkel kell rendelkeznie (lásd alább)
- Az automatikus automatizálás jelenleg nem támogatja a sandbox-előfizetéseket

### <a name="supported-regions"></a>Támogatott régiók
Az automatikus automatizálás csak a következő régiókban található virtuális gépeket támogatja:
* Nyugat-Európa
* Észak-Európa
* USA középső régiója
* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati régiója
* USA 2. nyugati régiója
* Közép-Kanada
* USA nyugati középső régiója
* USA déli középső régiója
* Kelet-Japán
* Az Egyesült Királyság déli régiója
* Ausztrália keleti régiója
* Délkelet-Ausztrália
* Délkelet-Ázsia

### <a name="required-rbac-permissions"></a>Szükséges RBAC-engedélyek
A fiókhoz némileg eltérő RBAC-szerepkörökre lesz szükség attól függően, hogy az automatikus kezelést egy új automatikus fiókkal engedélyezi-e.

Ha új Automanage-fiókkal engedélyezi az automatikus et:
* **Tulajdonosi** szerepkör a virtuális gépeket tartalmazó előfizetés(ek)hez, _**vagy**_
* **Közreműködői** **és felhasználói hozzáférés-rendszergazdai** szerepkörök a virtuális gépeket tartalmazó előfizetés(ek)hez

Ha egy meglévő Automanage-fiókkal engedélyezi az automatikusmanage-t:
* **Közreműködői** szerepkör a virtuális gépeket tartalmazó erőforráscsoporton

Az automatikus kezelőfiók közreműködői  és erőforrás-házirend-közreműködői jogosultságot kap az automatikusan kezelett gépeken való műveletek végrehajtásához. 

> [!NOTE]
> Ha egy másik előfizetésben lévő munkaterülethez csatlakoztatott virtuális gépen szeretné használni az Automatikusmanage-t, akkor minden előfizetéshez rendelkeznie kell a fent ismertetett engedélyekkel.

## <a name="participating-services"></a>Résztvevő szolgáltatások

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="A szolgáltatások intelligens módon való táblába való berekesze.":::

A részt vevő Azure-szolgáltatások teljes listájáért, valamint a támogatott környezetükért tekintse meg a következőket:
- [Automatikus automatizálás Linuxon](automanage-linux.md)
- [Automatikus automatizálás a Windows Serverhez](automanage-windows-server.md)

 Ezeket a szolgáltatásokat automatikusan fel fogjuk venni. Ezek elengedhetetlenek az ajánlott eljárásokhoz, amelyet a következő dokumentumban [talál: felhőadaptálási keretrendszer.](/azure/cloud-adoption-framework/manage/azure-server-management)

Ezen szolgáltatások mindegyikében automatikusan bevetjük, automatikusan konfiguráljuk, figyeljük a sodródásokat, és közvetítjük a sodródás észlelése esetén.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>A virtuális gépek automatikus Azure Portal

A Azure Portal engedélyezheti az automatikus automatizációt egy meglévő virtuális gépen, vagy új virtuális gép létrehozásakor. A folyamat rövid lépéseiért tekintse meg a virtuális gépek automatikus feldolgozásának rövid [útmutatóját.](quick-create-virtual-machines-portal.md)

Ha most először engedélyezi az automatikus szolgáltatásokat a virtuális géphez, a virtuális gép automatikus Azure Portal – Azure-beli virtuális gépekre vonatkozó ajánlott eljárások között **kereshet.** Kattintson **az Engedélyezés meglévő** virtuális gépen lehetőségre, válassza ki a virtuális gépeket, amelyekre fel szeretneni, kattintson a Kijelölés elemre, kattintson az **Engedélyezés** elemre, és kész. 

Előfordulhat, hogy csak akkor kell kapcsolatba lépnie ezzel a virtuális géptel ezeknek a szolgáltatásoknak a kezeléséhez, ha megpróbáljuk szervizelni a virtuális gépet, de ez nem sikerült. Ha sikeresen szervizelést adtunk a virtuális gépnek, a hiba a riasztás nélkül is megfelelő lesz. További részletekért lásd: [Virtuális gépek állapota.](#status-of-vms)

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>A virtuális gépek automatikus manage-ének engedélyezése a Azure Policy
A virtuális gépek automatikus szolgáltatását nagy léptékben is engedélyezheti a beépített Azure Policy. A szabályzat DeployIfNotExists hatást gyakorol, ami azt jelenti, hogy a szabályzat hatókörében található összes jogosult virtuális gép automatikusan elő lesz automatizálva a virtuális gépek automatikus alkalmazásával kapcsolatos ajánlott eljárásokhoz.

A szabályzatra mutató közvetlen hivatkozás itt [található.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)

### <a name="how-to-apply-the-policy"></a>A szabályzat alkalmazása
1. A **szabályzatdefiníció** megtekintésekor kattintson a Hozzárendelés gombra
1. Válassza ki azt a hatókört, amelyre alkalmazni szeretné a szabályzatot (lehet felügyeleti csoport, előfizetés vagy erőforráscsoport)
1. A **Paraméterek alatt** adja meg az automatikus felügyelet fiókjának, konfigurációs profiljának és hatásának paramétereit (a hatás általában DeployIfNotExists lesz)
    1. Ha nem fiókja van automatikus automatizálva, létre kell hoznia [egyet.](./automanage-account.md)
1. A **Szervizelés alatt** jelölje be a "Kattintson egy szervizelési feladatra" jelölőnégyzetet. Ezzel elvégzi az automatikus végrehajtásához szükséges be- és leállítást.
1. Kattintson **az Áttekintés + létrehozás elemre,** és ellenőrizze, hogy minden beállítás megfelelő-e.
1. Kattintson a **Létrehozás** lehetőségre.

## <a name="environment-configuration"></a>Környezet konfigurálása

Ha engedélyezi az Automatikus automatizálást a virtuális gépen, szükség van egy környezetre. A szolgáltatás alapja a környezetek. Ezek határozzák meg, hogy mely szolgáltatásokhoz adhatja meg a gépeket, és bizonyos mértékig a szolgáltatások konfigurációját is.

### <a name="default-environments"></a>Alapértelmezett környezetek

Jelenleg két környezet érhető el.

- **A Dev/Test-környezet** dev/test gépekhez lett kialakítva.
- **Az** éles környezet éles környezetben való.

Ennek a különbségnek az az oka, hogy bizonyos szolgáltatásokat a futó számítási feladat alapján ajánlunk. Az éles gépeken például automatikusan felveszünk egy Azure Backup. Fejlesztési/tesztelési gépeken azonban szükségtelen lenne a biztonsági mentési szolgáltatás, mivel a Dev/Test gépek általában kisebb hatással vannak az üzletre.

### <a name="customizing-an-environment-using-preferences"></a>Környezet testreszabása beállítások használatával

A standard szolgáltatások mellett a beállítások egy bizonyos részkészletének konfigurálható. Ezek a beállítások számos konfigurációs beállításon belül engedélyezettek. Például az Azure Backup megadhatja a biztonsági mentés gyakoriságát és azt, hogy a hét melyik napján történik.

> [!NOTE]
> A Dev/Test-környezetben a virtuális gépről egyáltalán nem lesz biztonságimentés.

Az alapértelmezett környezet beállításait a beállítások segítségével módosíthatja. Itt megtudhatja, hogyan hozhat létre [előnyben részesítő beállításokat.](virtual-machines-custom-preferences.md)

> [!NOTE]
> Az automatikus kezelés engedélyezése közben a virtuális gép enivonrment konfigurációja nem változtatható meg. Le kell tiltania a virtuális gép automatikus szolgáltatását, majd újra engedélyeznie kell az automatikus et a kívánt környezetben és beállításokban.

A részt vevő Azure-szolgáltatások teljes listáját és ha támogatják a beállításokat, tekintse meg a következőt:
- [Automatikus automatizálás Linux rendszeren](automanage-windows-server.md)
- [Automatikus automatizálás a Windows Serveren](automanage-windows-server.md)


## <a name="automanage-account"></a>Fiók automatikus létrehozása

Az automatikus felügyelet fiókja az a biztonsági környezet vagy identitás, amelyben az automatikus műveletek le vannak végezve. A fiók automatikus kezelése lehetőség kiválasztása általában nem szükséges, de ha olyan delegálásról van szó, amelyben el szeretné osztani az erőforrások automatikus felügyeletét (például két rendszergazda között), az engedélyezés folyamatában az Automatikus fiókkezelés lehetőséggel azure-identitást határozhat meg az egyes rendszergazdák számára.

Ha többet szeretne megtudni az automatikus fiókról és a fiók létrehozásáról, olvassa el a fiók [automatikusmanitálása dokumentumot.](./automanage-account.md)

## <a name="status-of-vms"></a>A virtuális gépek állapota

A Azure Portal az **Automanage – Azure virtual machine best practices (Az** Azure-beli virtuális gépek automatikus kezelése – Azure-beli virtuális gépek ajánlott eljárásai) lapra, amely felsorolja az összes automatikusan felügyelt virtuális gépet. Itt láthatja az egyes virtuális gépek általános állapotát.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="A konfigurált virtuális gépek listája.":::

Minden felsorolt virtuális géphez a következő adatok jelennek meg: Név, Környezet, Konfigurációs beállítások, Állapot, Operációs rendszer, Fiók, Előfizetés és Erőforráscsoport.

Az **Állapot oszlopban** a következő állapotok jelennek meg:
- *Folyamatban –* a virtuális gép csak most lett engedélyezve, és konfigurálva van
- *Konfigurálva –* a virtuális gép konfigurálva van, és nem észlelhető eltérés
- *Sikertelen* – a virtuális gép sodródott, és nem sikerült orvosolni
- *Függőben* – a virtuális gép jelenleg nem fut, és az Automanage megpróbálja a következő futtatáskor megkísérelni a virtuális gép beállítását vagy szervizelét

Ha a Sikertelen **állapotot** *látja,* az üzembe helyezés hibaelhárítását az erőforráscsoporton keresztül háríthatja el, amely a virtuális géphez tartozik. Az **Erőforráscsoportok menüben** válassza ki  az erőforráscsoportot, kattintson az Üzemelő példányok elemre, és itt tekintse meg a Sikertelen állapotot a hiba részleteivel együtt. 


## <a name="disabling-automanage-for-vms"></a>Virtuális gépek automatikus automatizálásának letiltása

Dönthet úgy, hogy egy napon letiltja az automatikus automatizálódást bizonyos virtuális gépeken. A gépe például rendkívül bizalmas, biztonságos számítási feladatot futtat, és az Azure-ban természetes módon végzettnél is tovább kell zárolnia, ezért a gépet az Azure ajánlott eljárásaitól különböző módon kell konfigurálnia.

Ezt az automatikus Azure Portal automatikus kezelése –  Azure-beli virtuális gépekre vonatkozó ajánlott eljárások oldalon tudja megtenni, amely felsorolja az összes automatikusan felügyelt virtuális gépet. Jelölje be a jelölőnégyzetet a virtuális gép mellett, amelyről le szeretné tiltani az Automatikusmanage-et, majd kattintson az **Automatikusmanagálás letiltása gombra.**

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Az automatikus automatizáció letiltása egy virtuális gépen.":::

A Disable (Letiltás) engedélyezése előtt olvassa el figyelmesen az üzenetkezelést az eredményül kapott **előugró ablakban.**

> [!NOTE]
> Ha letiltja az automatikus adatokat egy virtuális gépen, az a következő viselkedést követi:
>
> - A virtuális gép konfigurációja és az azt bevetett szolgáltatások nem változnak.
> - A szolgáltatásokért fizetendő díjak továbbra is számlázhatók, és továbbra is felmerülnek.
> - A sodródás automatikus felügyelete azonnal leáll.


Mindenekelőtt nem fogjuk kiveszni a virtuális gépet az összes olyan szolgáltatásból, amelybe a virtuális gépet beállította és beállította. Így az ezen szolgáltatásokért fizetendő díjak továbbra is számlázhatók maradnak. Szükség esetén ki kellni a táblából. Az automanage viselkedés azonnal leáll. A továbbiakban például nem figyeljük a virtuális gép eltérését.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogy az Automanage for virtual machines olyan eszközt kínál, amellyel nem kell az Azure-szolgáltatásokat ismernünk, bevetnünk és konfigurálnunk. Emellett ha egy gép, amelynél az Automanage for Virtual Machines szolgáltatáshoz van előállítva, és nem felel meg a környezet beállításának, a rendszer automatikusan visszaveszi a megfelelőséget.

Próbálja meg engedélyezni a virtuális gépek automatikus Azure Portal.

> [!div class="nextstepaction"]
> [A virtuális gépek automatikus automatizálásának engedélyezése a Azure Portal](quick-create-virtual-machines-portal.md)