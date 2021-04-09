---
title: Azure-beli automata felügyelet virtuális gépeken
description: Ismerje meg a virtuális gépek Azure-beli autofelügyeletét.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 1d3b2174df5dd83852ce120ec6693ae187a3e795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643540"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure-beli automata felügyelet virtuális gépeken

Ez a cikk a virtuális gépek Azure-beli autofelügyeletével kapcsolatos információkat ismerteti, amelyek a következő előnyöket nyújtják:

- Intelligens üzembe helyezés a virtuális gépeken az ajánlott eljárások Azure-szolgáltatások kiválasztásához
- Az Azure ajánlott eljárásaiban automatikusan konfigurálja az egyes szolgáltatásokat
- Figyelők az észleléshez és a kijavítására
- Egyszerű élményt nyújt (pont, kattintás, beállítás, elfelejti)


## <a name="overview"></a>Áttekintés

Az Azure-beli automatikus felügyelet virtuális gépeken olyan szolgáltatás, amely kiküszöböli a bevezetést, a bevezetést és az Azure-ban olyan szolgáltatások konfigurálását, amelyek hasznosak a virtuális gép számára. Ezek a szolgáltatások az Azure ajánlott eljárásokat szolgáló szolgáltatásai, és segítenek a virtuális gépek megbízhatóságának, biztonságának és kezelésének javításában. A szolgáltatások közé tartoznak például az [Azure Update Management](../automation/update-management/overview.md) és az [Azure Backup](../backup/backup-overview.md).

Miután bevezette a virtuális gépeket az Azure automanage-ba, minden ajánlott eljárás szolgáltatás a javasolt beállításokra van konfigurálva. Az ajánlott eljárások az egyes szolgáltatások esetében eltérőek. Lehetséges például, hogy Azure Backup, ahol az ajánlott eljárás a virtuális gép biztonsági mentése naponta egyszer, a megőrzési időtartam pedig hat hónap.

Az Azure automatikus felügyelet emellett automatikusan figyeli a sodródás és a kijavítani kívánt adatok észlelését. Ez azt jelenti, hogy ha a virtuális gépet az Azure automanage szolgáltatásba integrálták, nem csak az Azure-ban ajánlott eljárásokat fogjuk konfigurálni, de figyelni fogjuk a gépet annak biztosítására, hogy továbbra is megfeleljenek az ajánlott eljárásoknak a teljes életciklusa során. Ha a virtuális gép elsodródik vagy eltérnek ezektől a gyakorlattól (például ha egy szolgáltatás offboarded), a rendszer kijavította, majd visszaküldi a gépet a kívánt állapotba.

## <a name="prerequisites"></a>Előfeltételek

Több előfeltételt kell figyelembe vennie, mielőtt az Azure-beli automanage-t megpróbálja engedélyezni a virtuális gépeken.

- Támogatott [Windows Server-verziók](automanage-windows-server.md#supported-windows-server-versions) és [Linux-disztribúciók](automanage-linux.md#supported-linux-distributions-and-versions)
- A virtuális gépeknek támogatott régióban kell lenniük (lásd alább)
- A felhasználónak megfelelő engedélyekkel kell rendelkeznie (lásd alább)
- Az automanage nem támogatja a homokozó-előfizetéseket.

### <a name="supported-regions"></a>Támogatott régiók
Az automanage csak a következő régiókban lévő virtuális gépeket támogatja:
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

### <a name="required-rbac-permissions"></a>Szükséges RBAC engedélyek
A fiókja kis mértékben eltérő RBAC-szerepköröket igényel attól függően, hogy engedélyezi-e az automanage szolgáltatást egy új, automatikusan felügyelt fiókkal.

Ha egy új automanage-fiókkal engedélyezi az autofelügyeletet:
* A virtuális gépeket tartalmazó előfizetés (ek) **tulajdonosi** szerepköre, _**vagy**_
* **Közreműködő** és **felhasználói hozzáférés rendszergazdai** szerepkörök a virtuális gépeket tartalmazó előfizetés (ek) hez

Ha egy meglévő automanage-fiókkal engedélyezi az autofelügyeletet:
* **Közreműködő** szerepkör a virtuális gépeket tartalmazó erőforráscsoporthoz

> [!NOTE]
> Ha egy másik előfizetésben lévő munkaterülethez csatlakozó virtuális gépen szeretné használni az automanage-t, az egyes előfizetésekben a fent ismertetett engedélyekkel kell rendelkeznie.

## <a name="participating-services"></a>Résztvevő szolgáltatások

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Intelligensen integrált szolgáltatások.":::

A résztvevő Azure-szolgáltatások teljes listáját, valamint azok támogatott környezetét a következő témakörben tekintheti meg:
- [A Linux-alapú autokezelés](automanage-linux.md)
- [A Windows Server felügyeletének autokezelése](automanage-windows-server.md)

 A rendszer automatikusan bevezeti Önt ezekhez a résztvevő szolgáltatásokhoz. Alapvető fontosságúak az ajánlott eljárásokról szóló tanulmány, amelyet a [felhőalapú bevezetési keretrendszerben](/azure/cloud-adoption-framework/manage/azure-server-management)talál.

Az összes szolgáltatás esetében automatikus bevezetést, automatikus konfigurálást, a drift figyelését, valamint a drift észlelését követően közvetítjük.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure Portal-beli virtuális gépek autokezelésének engedélyezése

A Azure Portalban engedélyezheti a meglévő virtuális gépeken a felügyeletet, vagy új virtuális gép létrehozásakor. A folyamat tömör lépéseihez tekintse meg a [virtuális gépekkel kapcsolatos](quick-create-virtual-machines-portal.md)gyors útmutató című témakört.

Ha első alkalommal engedélyezi a virtuális gép autofelügyeletét, a Azure Portal kereshet az **automanage – Azure virtuális gépek ajánlott eljárásaiban**. Kattintson a **meglévő virtuális gép engedélyezése** lehetőségre, válassza ki a bevezetni kívánt virtuális gépeket, kattintson a **kiválasztás**, majd az **Engedélyezés** elemre, és készen áll.

Előfordulhat, hogy a virtuális géppel való kommunikációra csak akkor van szükség, ha a virtuális gépet szervizelni próbáltuk, de ezt a szolgáltatást nem sikerült kijavítani. Ha sikeresen szervizeljük a virtuális gépet, akkor még a riasztás nélkül visszatesszük a megfelelőséget. További részletekért lásd: [virtuális gépek állapota](#status-of-vms).


## <a name="environment-configuration"></a>Környezet konfigurálása

Ha engedélyezi a virtuális gép autofelügyeletét, akkor szükség van egy környezetre. A környezetek a szolgáltatás alapja. Ők határozzák meg, hogy mely szolgáltatások legyenek bevezetve a gépekre, és hogy milyen mértékben működnek ezek a szolgáltatások.

### <a name="default-environments"></a>Alapértelmezett környezetek

Jelenleg két környezet érhető el.

- A **fejlesztési és tesztelési** környezet fejlesztési és tesztelési célú gépekhez készült.
- **Éles környezet az** éles környezetben.

A differenciálás oka az, hogy bizonyos szolgáltatások használata a futó munkaterhelés alapján ajánlott. Egy üzemi gépen például automatikusan bekerül a Azure Backupba. A fejlesztési és tesztelési célú gépek esetében azonban a biztonsági mentési szolgáltatás szükségtelennek számít, mivel a fejlesztési/tesztelési gépek jellemzően alacsonyabb üzleti hatással bírnak.

### <a name="customizing-an-environment-using-preferences"></a>Környezet testreszabása beállítások használatával

A szolgáltatásban elérhető standard szolgáltatások mellett lehetővé tesszük a beállítások bizonyos részhalmazának konfigurálását. Ezek a beállítások a különböző konfigurációs beállításokon belül engedélyezettek. Azure Backup esetében például megadhatja a biztonsági mentés gyakoriságát, valamint a hét azon napját, amely a héten bekövetkezik.

> [!NOTE]
> A fejlesztési és tesztelési környezetben a virtuális gépet egyáltalán nem fogjuk biztonsági másolatot készíteni.

Az alapértelmezett környezet beállításait a beállítások között módosíthatja. Itt megtudhatja, hogyan hozhat létre előnyt [itt](virtual-machines-custom-preferences.md).

> [!NOTE]
> A enivonrment-konfiguráció nem módosítható a virtuális gépen, miközben az automatikus felügyelet engedélyezve van. Le kell tiltania az adott virtuális gép autofelügyeletét, majd újra engedélyeznie kell az automatikusan felügyelt környezetet a kívánt környezettel és beállításokkal.

A résztvevő Azure-szolgáltatások teljes listájáért, és ha támogatják a beállításokat, tekintse meg a következőt:
- [A Linux-alapú autokezelés](automanage-windows-server.md)
- [A Windows Server felügyeletének autokezelése](automanage-windows-server.md)


## <a name="automanage-account"></a>Fiók autokezelése

Az automatikus felügyelet fiók a biztonsági környezet vagy az az identitás, amely alatt az automatizált műveletek történnek. Általában az automatikus felügyelet fiók lehetőség nem szükséges a kiválasztásához, de ha olyan delegálási forgatókönyv volt, amelyben az erőforrások automatizált felügyeletét szeretné osztani (például két rendszergazda között), ez a beállítás lehetővé teszi az Azure-identitás meghatározását az egyes rendszergazdák számára.

Ha a virtuális gépeken engedélyezi az automanage funkciót, az Azure Portal-élményben az Azure-beli **virtuális gépre vonatkozó ajánlott eljárások** panel speciális legördülő menüje lehetővé teszi az automanage-fiók hozzárendelését vagy manuális létrehozását.

Az automatikusan felügyelt fiók a **közreműködő** és az erőforrás- **házirend közreműködői** szerepkört is megkapja azon előfizetések (ek) számára, amelyek az automatikusan felügyelni kívánt gépet (ka) t tartalmazzák. Ugyanazokat az automatikusan felügyelt fiókokat használhatja a gépeken több előfizetésen keresztül is, ami lehetővé teszi, hogy a fiók **közreműködői** és az **erőforrás-házirend közreműködői** engedélyeit az összes előfizetésre vonatkozóan megadja.

Ha a virtuális gép egy másik előfizetéshez tartozó Log Analytics munkaterülethez csatlakozik, az automatikusan felügyelt fiók a **közreműködő** és az **erőforrás-házirend közreműködőjét** is megadja a másik előfizetésben.

Ha új automanage-fiókkal engedélyezi az autofelügyeletet, az előfizetéséhez a következő engedélyek szükségesek: **tulajdonosi** szerepkör vagy **közreműködő** a **felhasználói hozzáférés rendszergazdai** szerepköreivel együtt.

Ha egy meglévő automanage-fiókkal engedélyezi az autofelügyeletet, rendelkeznie kell a **közreműködő** szerepkörrel a virtuális gépeket tartalmazó erőforráscsoporthoz.

> [!NOTE]
> Ha letiltja az ajánlott eljárások autofelügyeletét, az automatikusan kezeli a fiók engedélyeit a társított előfizetésekben. Manuálisan távolítsa el az engedélyeket az előfizetés IAM oldalára kattintva, vagy törölje az automanage-fiókot. Az automanage fiók nem törölhető, ha továbbra is kezeli a gépeket.


## <a name="status-of-vms"></a>Virtuális gépek állapota

A Azure Portal nyissa meg az automatikus **kezelés – Azure virtuális gépek ajánlott eljárásai** lapot, amely felsorolja az összes automatikusan felügyelt virtuális gépet. Itt láthatja az egyes virtuális gépek általános állapotát.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="A konfigurált virtuális gépek listája.":::

Az egyes felsorolt virtuális gépeknél a következő adatok jelennek meg: név, környezet, konfigurációs beállítások, állapot, operációs rendszer, fiók, előfizetés és erőforráscsoport.

Az **állapot** oszlopban a következő állapotok jelenhetnek meg:
- *Folyamatban* – a virtuális gép csak engedélyezve van, és konfigurálva van
- *Konfigurálva* – a virtuális gép konfigurálva van, és nem észlelhető eltolódás
- *Sikertelen* – a virtuális gép átsodródott, ezért nem sikerült elhárítani
- *Függőben* – a virtuális gép jelenleg nem fut, és az automanage megkísérli a virtuális gép bevezetését vagy szervizelését, amikor a következő fut

Ha az **állapotot** *nem sikerült* megtekinteni, a virtuális gép által használt erőforráscsoport használatával elháríthatja a telepítést. Lépjen az **erőforráscsoportok** elemre, válassza ki az erőforráscsoportot, kattintson a **központi telepítések** elemre, és tekintse meg a *hibás* állapotot a hiba részleteivel együtt.


## <a name="disabling-automanage-for-vms"></a>Virtuális gépek autokezelésének letiltása

Dönthet úgy is, hogy egy nap elteltével letiltja az automanage szolgáltatást bizonyos virtuális gépeken. Előfordulhat például, hogy a gép valamilyen rendkívül érzékeny biztonságos munkaterhelést futtat, és az Azure-nál még nagyobb mértékben le kell zárnia azt, ezért az Azure ajánlott eljárásain kívül kell konfigurálnia a gépet.

Ehhez a Azure Portal nyissa meg az automatikus **kezelés – Azure virtuális gépek ajánlott eljárásai** lapot, amely felsorolja az összes automatikusan felügyelt virtuális gépet. Jelölje be az **automanagement** szolgáltatásból letiltani kívánt virtuális gép melletti jelölőnégyzetet, majd kattintson az önfelügyelet letiltása gombra.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="A virtuális gépeken az automanage szolgáltatás letiltása.":::

A **Letiltás** előtt olvassa el figyelmesen az üzenetküldést az eredményül kapott előugró ablakban.

> [!NOTE]
> A virtuális gépeken az automanagement letiltása a következő viselkedést eredményezi:
>
> - A virtuális gép és a hozzájuk tartozó szolgáltatások konfigurációja nem változik.
> - A szolgáltatásokban felmerülő költségek számlázandóek maradnak, és továbbra is felmerülnek.
> - Az automanage viselkedés azonnal leáll.


Az első és legfontosabb, hogy a virtuális gépet minden olyan szolgáltatásból kiállítjuk, amelyet a be-és kiállítottunk. Így a szolgáltatásokban felmerülő költségek továbbra is számlázva maradnak. Szükség esetén ki kell jelentkeznie a táblából. Az automanage viselkedés azonnal leáll. Például a rendszer nem figyeli a virtuális gépet a drifthez.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogy a Virtual Machines szolgáltatás automatikusan felügyeli az Azure-szolgáltatások ajánlott eljárásainak ismeretét, bevezetését és konfigurálásának szükségességét. Emellett, ha a virtuális gépek automatikus felügyeletére szolgáló gép a környezet beállításával sodródik, a rendszer automatikusan visszaküldi a megfelelőséget.

Próbálja meg engedélyezni a Azure Portalban található virtuális gépek autofelügyeletét.

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)