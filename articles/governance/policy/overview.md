---
title: Az Azure szabályzatának áttekintése
description: Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2dd31ab29479fade21d27b8e2c23952f905f530a
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979160"
---
# <a name="overview-of-the-azure-policy-service"></a>Az Azure Policy szolgáltatás áttekintése

Cégirányítási ellenőrzi, hogy a szervezet a célok leghatékonyabb használatán keresztül érhető el, informatikai. Üzleti céljait és informatikai projektek között egyértelműség létrehozásával megfelel ennek az igénynek.

A cégben jelentős mennyiségű, láthatóan megoldhatatlan informatikai probléma jelentkezik?
A hatékony informatikai szabályozás stratégiai szinten kezeli a kezdeményezések tervezését és a prioritások meghatározását a problémák kezelése és megelőzése érdekében. A stratégiai szükség, ahol az Azure Policy pedig kifejezetten.

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. Az Azure Policy megfelel ennek az igénynek az erőforrások megfelel-e a hozzárendelt szabályzatok kiértékelik. Például rendelkezhet egy olyan szabályzattal, amely csak bizonyos méretű virtuálisgép-termékváltozatot engedélyez a környezetben. Ez a házirend megvalósítása, miután új és meglévő erőforrások megfelelőségi értékeli ki. A megfelelő szabályzat típusát, a meglévő erőforrások megfelelőségének tehető meg. Később a jelen dokumentációban azt átjövünk további részleteket a létrehozása és az Azure Policyvel házirendek megvalósítása.

> [!IMPORTANT]
> Az Azure Policy megfelelőségértékelése mostantól tarifacsomagtól függetlenül minden hozzárendeléshez elérhető. Ha a hozzárendeléseknél nem láthatók a megfelelőségi adatok, győződjön meg róla, hogy az előfizetés regisztrálva van a Microsoft.PolicyInsights erőforrás-szolgáltatón.

## <a name="how-is-it-different-from-rbac"></a>Mennyiben különbözik ez az RBAC-től?

Az Azure Policy és a szerepköralapú hozzáférés-vezérlés (RBAC) közötti néhány fontos különbség van. Az RBAC a különféle hatókörű felhasználói műveletekre összpontosít. Előfordulhat, hogy lehet hozzáadni egy erőforráscsoport, a közreműködő szerepkört lehetővé teszi, hogy hajtsa végre a módosításokat az adott erőforráscsoporton. A meglévő erőforrások erőforrás-tulajdonságok üzembe helyezés során, és már az Azure házirend összpontosít. Az Azure Policy tulajdonságok, mint például a fájltípusok vagy az erőforrások szabályozza. RBAC, eltérően az Azure Policy alapértelmezés szerint, és explicit tiltási rendszer.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-engedélyek az Azure Policyban

Az Azure Policy több engedéllyel (más néven művelettel) rendelkezik két erőforrás-szolgáltatóban:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Számos beépített szerepkör oszt ki engedélyeket Azure Policy-erőforrásoknak. A **erőforrás-szabályzati közreműködő (előzetes verzió)** szerepkör tartalmazza a legtöbb Azure szabályzat-műveletek. **Tulajdonos** teljes körű jogosultságokkal rendelkezik. Mindkét **közreműködői** és **olvasó** minden olvasási műveletek az Azure Policy, használhatja, de **közreműködői** is el lehet indítani a szervizelés.

Ha egyik beépített szerepkör sem tartalmazza a szükséges engedélyeket, hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Szabályzatdefiníció

Az Azure Policyban a szabályzatok létrehozásának és bevezetésének folyamata egy szabályzatdefiníció létrehozásával kezdődik. Mindegyik szabályzatdefiníció feltételekkel, kényszerítve van rendelkezik. És a egy meghatározott hatása, ha a feltételek teljesülnek.

Az Azure Policy biztosítunk számos beépített szabályzatok alapértelmezés szerint elérhető. Példa:

- **SQL Server 12.0-s verziójának megkövetelése**: Ellenőrzi, hogy minden SQL-kiszolgáló 12.0-s verziót használja-e. Hatás az, hogy ezek a feltételek nem felelnek meg az összes kiszolgáló megtagadja.
- **Engedélyezett termékváltozatok a tárolási fiók**: Meghatározza, hogy parancsfájlműveletekkel tárfiók a Termékváltozat-méretek belül. Annak hatását, hogy megtagadja az összes storage-fiókok, amelyek nem a Termékváltozat-méretek meghatározott készletét.
- **Engedélyezett erőforrástípus**: Határozza meg az üzembe helyezhető erőforrástípusok. Hatás az összes olyan erőforrást, amely nem része a definiált listában elutasítása.
- **Engedélyezett helyek**: Korlátozza az elérhető helyek új erőforrások. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Engedélyezett virtuálisgép-termékváltozatok**: Virtuális gépek azon Termékváltozatai, amely központilag telepíthető egy halmazát határozza meg.
- **Címke és címke alapértelmezett értékének alkalmazása**: Érvényes egy címke és az alapértelmezett értékére, ha a telepítés kérelemben nincs megadva.
- **Címke és címke értékének kényszerítése**: Kényszerít egy címkét és a hozzá tartozó érték megadását egy erőforráshoz.
- **Nem engedélyezett erőforrástípusok**: Megakadályozza, hogy erőforrástípusok listája üzembe helyezve.

Megvalósítani a ezeket a szabályzatdefiníciók (mind a beépített és egyéni-definíciók), kell rendelni őket. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

Számos különböző műveletek, például a szabályzat-hozzárendelést vagy a házirend-frissítési szabályzat-kiértékelés történik. Teljes listáját lásd: [házirend kiértékelése eseményindítók](./how-to/get-compliance-data.md#evaluation-triggers).

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](./concepts/definition-structure.md) foglalkozó témakört.

## <a name="policy-assignment"></a>Szabályzat-hozzárendelés

A szabályzat-hozzárendelés egy olyan szabályzatdefiníció, amely egy adott hatókörön belül érvényes. Ez a hatókör bármi lehet egy [felügyeleti csoporttól](../management-groups/overview.md) egy erőforráscsoportig. A *hatókör* kifejezés az összes olyan erőforráscsoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyekhez a szabályzatdefiníció hozzá lett rendelve. A szabályzat-hozzárendeléseket az összes alárendelt erőforrás örökli. Ez a kialakítás azt jelenti, hogy egy erőforráscsoport alkalmazott házirend is alkalmazza a rendszer az adott erőforráscsoportba tartozó erőforrások. Azt is meg lehet azonban adni, hogy a szabályzat-hozzárendelés egy adott alhatókörre ne vonatkozzon.

Egy előfizetésre vonatkozóan megadhat például egy olyan szabályzatot, amely megakadályozza a hálózati erőforrások létrehozását, Egy erőforráscsoport, az adott előfizetésben hálózati infrastruktúra szánt sikerült kizárni. Ezután a hálózati erőforrások létrehozásával megbízott felhasználóknak hálózati erőforráscsoporthoz hozzáférést adhat.

Egy másik példa lehet hozzárendelni kívánt erőforrás típusát engedélyezése esetén a lista szabályzatok a felügyeleti csoport szintjén. Ezután hozzárendelne egy megengedőbb (több erőforrástípust engedélyező) szabályzatot egy alárendelt felügyeleti csoporthoz vagy akár közvetlenül az előfizetésekhez. Ez azonban nem működne, mivel a szabályzat egy explicit tiltási rendszer. A megoldás az, hogy kizárja az alárendelt felügyeleti csoportot vagy az előfizetést a felügyeleti csoport szintjén hozzárendelt szabályzatból, majd hozzárendeli a megengedőbb szabályzatot az alárendelt felügyeleti csoportok vagy az előfizetések szintjén. Ha egy szabályzat eredménye egy erőforrás, majd az erőforrás engedélyezésének egyetlen módja, hogy a tiltó szabályzat módosítása.

További, a szabályzatdefiníciók és -hozzárendelések a portálon keresztül történő beállítására vonatkozó tudnivalókért lásd a [szabályzat-hozzárendelés nem megfelelő erőforrások azonosításának céljából, az Azure környezetben történő létrehozásával](assign-policy-portal.md) foglalkozó cikket. A [PowerShellhez](assign-policy-powershell.md) és az [Azure CLI-hez](assign-policy-azurecli.md) is elérhetők lépések.

## <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

Paraméterek vannak meghatározva, a szabályzat-definíció létrehozása során. Ha egy paraméter meg van határozva, nevet és opcionálisan adott érték. Például megadhat egy paramétert egy *hely* nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például *EastUS* vagy *WestUS*.

Házirend-paraméterekkel kapcsolatos további információkért lásd: [Szabályzatdefiníciók struktúrája - paraméterek](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

- **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
- **A Security Center az operációs rendszer biztonsági réseinek figyelése** – a kiszolgálók monitorozásához, amelyek nem a konfigurált alapértékeknek.
- **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

## <a name="initiative-assignment"></a>Kezdeményezési hozzárendelés

A szabályzat-hozzárendelésekhez hasonlóan a kezdeményezési hozzárendelések olyan kezdeményezési definíciók, amelyek egy adott hatókörhöz vannak hozzárendelve. A kezdeményezési hozzárendeléseknek köszönhetően nem kell olyan sok kezdeményezési definíciót létrehozni az egyes hatókörökhöz. Ez a hatókör szintén bármi lehet egy felügyeleti csoporttól egy erőforráscsoportig.

Minden egyes kezdeményezés több hatókörhöz is hozzárendelhető. Egyaránt hozzárendelheti egy kezdeményezést **hozzárendelhető** és **b**.

## <a name="initiative-parameters"></a>Kezdeményezési paraméterek

A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. Kezdeményezési paraméterek a következők paramétereket a szabályzatdefiníciók a kezdeményezésen belül használja.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) az **A szabályzat** és a **B szabályzat** szabályzatdefiníció tartozik, amelyek mindketten különböző típusú paramétert várnak:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | tömb  |Ez a paraméter sztringek listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |sztring |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa sztringként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

- A kezdeményezés belüli szabályzatdefiníciók paramétereinek használata: Ebben a példában *allowedLocations* és *allowedSingleLocation* válnak a kezdeményezési paraméterek **kezdeményezés**.
- Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. Ebben az esetben megadhat egy helylistát az **A szabályzat paramétere – allowedLocations** és a **B szabályzat paramétere – allowedSingleLocation** számára. Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
- Adjon meg egy listát mindazon lehetséges *értékekről*, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

Amikor érték beállítások egy kezdeményezési definíciót hoz létre, Ön nem adhat egy másik értéket a kezdeményezés hozzárendelésekor, mert nem szerepel a listában.

## <a name="maximum-count-of-azure-policy-objects"></a>Az Azure a csoportházirend-objektumok maximális száma

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

Íme néhány mutatók és tippek figyelembe kell venni:

- Indítsa el egy naplózási kezdve a helyett, nyomon követni a szabályzatdefiníció hatását a környezet erőforrásaira egy megtagadási érvénybe. Ha vannak olyan parancsprogramjai már az automatikus méretezés, hogy az alkalmazások egy megtagadási effektus beállítása akadályozhatják ilyen automatizálási feladatok már érvényben.

- Fontolja meg a szervezeti hierarchia definíciókat és -hozzárendelések létrehozásakor. Javasolt létrehozni a definíciókat magasabb szintű, például a felügyeleti csoport vagy az előfizetés szintjén. Ezután hozzon létre a hozzárendelést a következő alárendelt szinten. Ha egy definíciót, egy felügyeleti csoportot hoz létre, a hozzárendelés leszűkítheti a egy előfizetést, vagy az erőforráscsoport, a felügyeleti csoporton belül.

- Javasoljuk, hogy hoz létre, és még egy szabályzat-definíció a kezdeményezési definíciók.
Például, hogy a szabályzat-definíció *policyDefA* , majd hozza létre a kezdeményezési definíciót *initiativeDefC*. Ha létrehoz egy másik szabályzatdefiníciót később *policyDefB* hasonló céljait *policyDefA*, is hozzáadhat a *initiativeDefC* és együtt nyomon követése.

- Miután létrehozott egy kezdeményezési hozzárendelést, az hozzáadódik a kezdeményezés szabályzatdefiníciók is a kezdeményezések hozzárendelések részévé válik.

- Kezdeményezési hozzárendelések abban az esetben, ha a kezdeményezésen belül minden szabályzat is értékeli ki. Egy házirend kiértékelése külön-külön kell, ha fontos foglalja bele-kezdeményezéshez.

## <a name="video-overview"></a>Áttekintő videó

Az Azure Policy alábbi áttekintése a 2018-as buildből származik. Diák vagy videó letöltése, látogasson el [szabályozása az Azure-környezetben keresztül az Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) Channel 9-on.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb fogalmat, folytatásként a következő témaköröket javasoljuk:

- [A portál használatával szabályzatdefiníció hozzárendelése](assign-policy-portal.md).
- [Az Azure CLI-vel szabályzatdefiníció hozzárendelése](assign-policy-azurecli.md).
- [PowerShell-lel szabályzatdefiníció hozzárendelése](assign-policy-powershell.md).
- Tekintse át a felügyeleti csoport van [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](..//management-groups/overview.md).
- Nézet [szabályozása az Azure-környezetben keresztül az Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) Channel 9-on.