---
title: Azure-beli automata felügyelet virtuális gépeken
description: Ismerje meg a virtuális gépek Azure-beli autofelügyeletét.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 7772d57937393da1c48fa2658818d8a1a2b28a1f
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550784"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure-beli automata felügyelet virtuális gépeken

Ez a cikk a virtuális gépek Azure-beli autofelügyeletével kapcsolatos információkat ismerteti, amelyek a következő előnyöket nyújtják:

- Intelligens üzembe helyezés a virtuális gépeken az ajánlott eljárások Azure-szolgáltatások kiválasztásához
- Az Azure ajánlott eljárásaiban automatikusan konfigurálja az egyes szolgáltatásokat
- Figyelők az észleléshez és a kijavítására
- Egyszerű élményt nyújt (pont, kattintás, beállítás, elfelejti)


## <a name="overview"></a>Áttekintés

Az Azure-beli automatikus felügyelet virtuális gépeken olyan szolgáltatás, amely kiküszöböli a bevezetést, a bevezetést és az Azure-ban olyan szolgáltatások konfigurálását, amelyek hasznosak a virtuális gép számára. Ezek a szolgáltatások segítenek a virtuális gépek megbízhatóságának, biztonságának és kezelésének javításában, és az Azure ajánlott eljárásokat szolgáló szolgáltatásai, például az [azure Update Management](../automation/update-management/overview.md) és az [Azure Backup](../backup/backup-overview.md) – csak hogy csak néhányat említsünk.

Miután bevezette a virtuális gépeket az Azure automatikus felügyelet szolgáltatásba, automatikusan konfigurálja az egyes ajánlott eljárásokat a javasolt beállításokhoz. Az ajánlott eljárások az egyes szolgáltatások esetében eltérőek. Lehetséges például, hogy Azure Backup, ahol az ajánlott eljárás a virtuális gép biztonsági mentése naponta egyszer, a megőrzési időtartam pedig hat hónap.

Az Azure automatikus felügyelet emellett automatikusan figyeli a sodródás és a kijavítani kívánt adatok észlelését. Ez azt jelenti, hogy ha a virtuális gépet az Azure automanage szolgáltatásba integrálták, nem csak az Azure-ban ajánlott eljárásokat fogjuk konfigurálni, de figyelni fogjuk a gépet annak biztosítására, hogy továbbra is megfeleljenek az ajánlott eljárásoknak a teljes életciklusa során. Ha a virtuális gép elsodródik, vagy eltérnek ezektől a gyakorlattól, akkor kijavítani fogjuk, és vissza kell kérni a gépet a kívánt állapotba.

Végül az élmény hihetetlenül egyszerű.


## <a name="prerequisites"></a>Előfeltételek

Több előfeltételt kell figyelembe vennie, mielőtt az Azure-beli automanage-t megpróbálja engedélyezni a virtuális gépeken.

- Csak Windows Server-alapú virtuális gépek
- A virtuális gépeknek támogatott régión belül kell lenniük (lásd az alábbi bekezdést)
- A felhasználónak megfelelő engedélyekkel kell rendelkeznie (lásd az alábbi bekezdést)
- Az automanage nem támogatja a homokozó-előfizetéseket.

Azt is fontos megjegyezni, hogy az automatikus felügyelet csak a következő régiókban található Windows rendszerű virtuális gépeket támogatja: Nyugat-Európa, USA keleti régiója, USA 2. nyugati régiója, Közép-Kanada, USA nyugati középső régiója, Kelet-Japán.

A virtuális gépeket tartalmazó erőforráscsoporthoz **közreműködő** szerepkörrel kell rendelkeznie, amely lehetővé teszi, hogy a virtuális gépeken egy meglévő automanage fiók használatával engedélyezze az automanage szolgáltatást. Ha új automanage-fiókkal engedélyezi az autofelügyeletet, az előfizetéséhez a következő engedélyek szükségesek: **tulajdonosi** szerepkör vagy **közreműködő** a **felhasználói hozzáférés rendszergazdai** szerepköreivel együtt.

> [!NOTE]
> Ha egy másik előfizetésben lévő munkaterülethez csatlakozó virtuális gépen szeretné használni az automanage-t, az egyes előfizetésekben a fent ismertetett engedélyekkel kell rendelkeznie.

## <a name="participating-services"></a>Résztvevő szolgáltatások

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Intelligensen integrált szolgáltatások.":::

Az [Azure automatikus kezelésével Virtual Machines ajánlott eljárásokat](virtual-machines-best-practices.md) a résztvevő Azure-szolgáltatások teljes listájához, valamint a támogatott konfigurációs profilokhoz.

 A rendszer automatikusan bevezeti Önt ezekhez a résztvevő szolgáltatásokhoz. Alapvető fontosságúak az ajánlott eljárásokról szóló tanulmány, amelyet a [felhőalapú bevezetési keretrendszerben](/azure/cloud-adoption-framework/manage/azure-server-management)talál.

Az összes ilyen szolgáltatás esetében automatikusan bevezetést, automatikus konfigurálást, figyelést biztosítunk a drift esetében, és a rendszer a drift észlelésekor közvetíti a szolgáltatást.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure Portal-beli virtuális gépek autokezelésének engedélyezése

A Azure Portalban engedélyezheti a meglévő virtuális gépeken a felügyeletet, vagy új virtuális gép létrehozásakor. A folyamat tömör lépéseihez tekintse meg a [virtuális gépekkel kapcsolatos](quick-create-virtual-machines-portal.md)gyors útmutató című témakört.

Ha első alkalommal engedélyezi a virtuális gép autofelügyeletét, a Azure Portal kereshet az **automanage – Azure virtuális gépek ajánlott eljárásaiban**. Kattintson a **meglévő virtuális gép engedélyezése** lehetőségre, válassza ki a bevezetni kívánt virtuális gépeket, kattintson a **kiválasztás**, majd az **Engedélyezés** elemre, és készen áll.

Előfordulhat, hogy a virtuális géppel való kommunikációra csak akkor van szükség, ha a virtuális gépet szervizelni próbáltuk, de ezt a szolgáltatást nem sikerült kijavítani. Ha sikeresen szervizeljük a virtuális gépet, akkor még a riasztás nélkül visszatesszük a megfelelőséget.


## <a name="configuration-profiles"></a>Konfigurációs profilok

Ha engedélyezi a virtuális gép automatikus felügyeletét, szükség van egy konfigurációs profilra. A konfigurációs profilok a szolgáltatás alapja. Pontosan meghatározzák, hogy a gépek Milyen szolgáltatásokra vannak bevezetve, és hogy milyen mértékben működnek ezek a szolgáltatások.

### <a name="default-configuration-profiles"></a>Alapértelmezett konfigurációs profilok

Jelenleg két konfigurációs profil érhető el.

- **Azure-beli virtuális gépek – ajánlott eljárások – a dev/test** konfigurációs profil fejlesztési és tesztelési célú gépekhez készült.
- **Azure-beli virtuális gépek – ajánlott eljárások – üzemi** konfigurációs profil éles környezetben.

A differenciálás oka az, hogy bizonyos szolgáltatások használata a futó munkaterhelés alapján ajánlott. Egy üzemi gépen például automatikusan bekerül a Azure Backupba. A fejlesztési és tesztelési célú gépek esetében azonban a biztonsági mentési szolgáltatás szükségtelennek számít, mivel a fejlesztési/tesztelési gépek jellemzően alacsonyabb üzleti hatással bírnak.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Konfigurációs profil testreszabása beállítások használatával

A szolgáltatásban elérhető standard szolgáltatások mellett lehetővé tesszük a beállítások bizonyos részhalmazának konfigurálását. Ezek a beállítások számos olyan konfigurációs lehetőségen belül engedélyezettek, amelyek nem sértik az ajánlott eljárásokat. Azure Backup esetében például megadhatja a biztonsági mentés gyakoriságát, valamint a hét azon napját, amely a héten bekövetkezik. Azonban *nem* engedélyezzük a Azure Backup teljes kikapcsolását.

> [!NOTE]
> A fejlesztési/tesztelési konfigurációs profilban a virtuális gépet egyáltalán nem fogjuk biztonsági másolatot készíteni.

Az alapértelmezett konfigurációs profil beállításait a beállítások lehetőséggel módosíthatja. Itt megtudhatja, hogyan hozhat létre előnyt [itt](virtual-machines-custom-preferences.md).

> [!NOTE]
> A konfigurációs profil nem módosítható a virtuális gépen, amíg az automatikus felügyelet engedélyezve van. Le kell tiltania a virtuális gép automatikus felügyeletét, majd újra engedélyeznie kell az automatikus felügyeletet a kívánt konfigurációs profillal és beállításokkal.


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

Az egyes felsorolt virtuális gépeknél a következő részletek jelennek meg: név, konfigurációs profil, konfigurációs preferencia, állapot, fiók, előfizetés és erőforráscsoport.

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

Ebből a cikkből megtudhatta, hogy a Virtual Machines szolgáltatás automatikusan felügyeli az Azure-szolgáltatások ajánlott eljárásainak ismeretét, bevezetését és konfigurálásának szükségességét. Továbbá, ha a virtuális gépek automatikus felügyeletére szolgáló gép a beállított konfigurációs profilokkal sodródik, a rendszer automatikusan visszaállítja a megfelelőséget.

Próbálja meg engedélyezni a Azure Portalban található virtuális gépek autofelügyeletét.

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)