---
title: Az Azure szabályzatának áttekintése
description: Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben.
ms.date: 01/14/2021
ms.topic: overview
ms.openlocfilehash: df430586af2e701ec2881f6ea760095fd2ca79d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220728"
---
# <a name="what-is-azure-policy"></a>Mi az Azure Policy?

Az Azure Policy segít a szervezeti szabványok betartatásában és a megfelelőségi követelmények nagy léptékű kiértékelésében. A megfelelőségi irányítópulton egy összesített nézetet biztosít a környezet általános állapotának kiértékeléséhez, amely lehetővé teszi, hogy lebontsa az erőforrás-alapú, a házirendek részletességét. Emellett a meglévő erőforrások tömeges szervizelésével és az új erőforrások automatikus szervizelésével is lehetővé teszi az erőforrások megfelelőségét.

A Azure Policy gyakori használati esetei közé tartozik az erőforrás-konzisztencia megvalósítása, a szabályozások megfelelősége, a biztonság, a költséghatékonyság és a felügyelet. Ezeknek a gyakori használati eseteknek a szabályzat-definíciói már elérhetők az Azure-környezetben olyan beépített modulként, amely segítséget nyújt az első lépésekhez.

Minden Azure Policy adatok és objektumok inaktív állapotban vannak titkosítva. További információ: Azure-beli [adattitkosítás](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Áttekintés

Azure Policy kiértékeli az erőforrásokat az Azure-ban az erőforrások tulajdonságainak az üzleti szabályokhoz való összehasonlításával. A [JSON formátumban](./concepts/definition-structure.md)leírt üzleti szabályok [szabályzat-definíciók](#policy-definition). A felügyelet egyszerűsítése érdekében több üzleti szabály is csoportosítható egy [házirend-kezdeményezés](#initiative-definition) (más néven _policySet_) formájában. Az üzleti szabályok létrejötte után a házirend-definíció vagy kezdeményezés az Azure által támogatott összes erőforrás-hatókörhöz [hozzá van rendelve](#assignments) , például [felügyeleti csoportok](../management-groups/overview.md), előfizetések, [erőforráscsoportok](../../azure-resource-manager/management/overview.md#resource-groups)vagy egyedi erőforrások. A hozzárendelés az adott hozzárendelés [Resource Manager-hatókörében](../../azure-resource-manager/management/overview.md#understand-scope) lévő összes erőforrásra vonatkozik. Ha szükséges, az alhatókörök kizárható. További információ: [scope in Azure Policy](./concepts/scope.md).

A Azure Policy [JSON-formátumban formázza](./concepts/definition-structure.md) a kiértékelés által használt logikát annak megállapításához, hogy az erőforrás megfelel-e vagy sem. A definíciók közé tartoznak a metaadatok és a házirend-szabály. A definiált szabály a függvények, paraméterek, logikai operátorok, feltételek és tulajdonság- [aliasok](./concepts/definition-structure.md#aliases) használatával pontosan megfelel a kívánt forgatókönyvnek. A házirend-szabály meghatározza, hogy mely erőforrások legyenek kiértékelve a hozzárendelés hatókörében.

### <a name="understand-evaluation-outcomes"></a>A kiértékelés eredményeinek megismerése

Az erőforrásokat az erőforrás-életciklus, a szabályzat-hozzárendelés életciklusa és a rendszeres folyamatos megfelelőség kiértékelése során meghatározott időpontokban értékeli ki. Az erőforrások kiértékelését kiváltó idők vagy események a következők:

- Egy erőforrás létrehozása, frissítése vagy törlése házirend-hozzárendeléssel rendelkező hatókörben történik.
- Egy házirend vagy kezdeményezés újonnan van hozzárendelve egy hatókörhöz.
- Egy hatókörhöz már hozzárendelt házirend vagy kezdeményezés frissül.
- A standard megfelelőség kiértékelési ciklusa során, amely 24 óránként egyszer történik.

A szabályzatok kiértékelésének idejével és módjával kapcsolatos részletes információk: [kiértékelési eseményindítók](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>A kiértékelésre adott válasz szabályozása

A nem megfelelő erőforrások kezelésére vonatkozó üzleti szabályok széles körben változnak a szervezetek között. Példák arra, hogy a szervezet hogyan szeretné a platformot egy nem megfelelő erőforrásra válaszolni:

- Az erőforrás módosításának megtagadása
- Az erőforrás módosításának naplózása
- Az erőforrás módosítása a módosítás előtt
- Az erőforrás módosítása a módosítás után
- Kapcsolódó megfelelő erőforrások üzembe helyezése

Azure Policy az egyes üzleti válaszokat a [hatások](./concepts/effects.md)alkalmazásával lehetségesvé teszi. A hatások a [házirend-definíció](./concepts/definition-structure.md) **szabályzat-szabály** részében vannak beállítva.

### <a name="remediate-non-compliant-resources"></a>Nem megfelelő erőforrások szervizelése

Habár ezek a hatások elsősorban az erőforrás létrehozásakor vagy frissítésekor érintik az erőforrást, Azure Policy a meglévő nem megfelelő erőforrások kezelését is támogatja anélkül, hogy módosítani kellene az adott erőforrást. A meglévő erőforrások megfelelővé tételével kapcsolatos további információkért lásd: [szervizelését-erőforrások](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Videó – áttekintés

Az Azure Policy alábbi áttekintése a 2018-as buildből származik. A diaképek és videók letöltéséhez látogasson el [Az Azure-környezetre a Channel 9 Azure Policyon keresztül](https://channel9.msdn.com/events/Build/2018/THR2030) .

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Első lépések

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy és az Azure RBAC

A Azure Policy és az Azure szerepköralapú hozzáférés-vezérlése (Azure RBAC) között néhány lényeges különbség van. Azure Policy kiértékeli az állapotot a Resource Managerben és egyes erőforrás-szolgáltatók tulajdonságaiban megjelenített erőforrások tulajdonságainak vizsgálatával. Azure Policy nem korlátozza a műveleteket (más néven _műveletek_). Azure Policy gondoskodik arról, hogy az erőforrás-állapot megfeleljen az üzleti szabályoknak, anélkül, hogy ki kellene használnia a változást, vagy hogy ki jogosult a módosításra.

Az Azure RBAC a felhasználói [műveletek](../../role-based-access-control/resource-provider-operations.md) különböző hatókörökön történő felügyeletére koncentrál. Ha egy művelet vezérlésére van szükség, akkor az Azure RBAC a megfelelő eszköz a használathoz. Még akkor is, ha az adott személy nem megfelelő erőforrást hoz létre, Azure Policy továbbra is blokkolja a létrehozás vagy a frissítés műveleteit.

Az Azure RBAC és Azure Policy kombinációja teljes körű felügyeletet biztosít az Azure-ban.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Azure RBAC engedélyek a Azure Policyban

Az Azure Policy több engedéllyel (más néven művelettel) rendelkezik két erőforrás-szolgáltatóban:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft. PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Számos beépített szerepkör oszt ki engedélyeket Azure Policy-erőforrásoknak. Az **erőforrás-házirend közreműködői** szerepkör a legtöbb Azure Policy műveletet tartalmazza. A **tulajdonos** teljes körű jogosultságokkal rendelkezik. Mind a **közreműködő** , mind az **olvasó** hozzáfér az összes _olvasási_ Azure Policy művelethez. A **közreműködő** erőforrás-szervizelést indíthat, de nem _hozhat létre_ definíciókat vagy hozzárendeléseket. A **felhasználói hozzáférés rendszergazdájának** meg kell adnia a felügyelt identitást a **deployIfNotExists** , vagy **módosítania** kell a hozzárendelésekhez szükséges engedélyeket.

Ha egyik beépített szerepkör sem tartalmazza a szükséges engedélyeket, hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> A **deployIfNotExists** felügyelt identitásának vagy a házirend-hozzárendelés **módosításának** elegendő engedélyre van szüksége a célzott erőforrások létrehozásához vagy frissítéséhez. További információ: szabályzat- [definíciók konfigurálása szervizeléshez](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Azure Policy által érintett erőforrások

Azure Policy kiértékeli az összes Azure-erőforrást az előfizetések szintjén vagy azok alatt, beleértve az ív használatára képes erőforrásokat is. Bizonyos erőforrás-szolgáltatók, például a [vendég konfigurációja](./concepts/guest-configuration.md), az [Azure Kubernetes Service](../../aks/intro-kubernetes.md)és a [Azure Key Vault](../../key-vault/general/overview.md)esetében mélyebb integráció áll rendelkezésre a beállítások és objektumok kezeléséhez. További információ: [erőforrás-szolgáltatói módok](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

Íme néhány mutató, és a következő tippeket kell szem előtt tartani:

- Ha a házirend-definíció hatásait szeretné követni a környezetben lévő erőforrásokon, a megtagadási hatás helyett egy naplózási effektust kell kezdenie. Ha már van olyan parancsfájl, amely az alkalmazások automatikus méretezésére van beállítva, akkor a Megtagadás hatásának beállítása hátráltathatja az ilyen automatizálási feladatokat.

- Definíciók és hozzárendelések létrehozásakor vegye fontolóra a szervezeti hierarchiákat. Javasoljuk, hogy a definíciókat olyan magasabb szinteken hozza létre, mint a felügyeleti csoport vagy az előfizetés szintje. Ezután hozza létre a hozzárendelést a következő gyermek szinten. Ha létrehoz egy definíciót egy felügyeleti csoportban, a hozzárendelés az adott felügyeleti csoportban lévő előfizetésre vagy erőforráscsoporthoz is felhasználható.

- Javasoljuk, hogy egyetlen házirend-definícióhoz is hozzon létre és rendeljen hozzá kezdeményezési definíciókat.
  Például megadhatja a szabályzat-definíció _policyDefA_ , és létrehozhatja azt a Initiative definition _initiativeDefC_ alatt. Ha később új szabályzat-definíciót hoz létre a _definícióéhoz_ -hoz a _policyDefA_ hasonló célokkal, akkor hozzáadhatja azt a _initiativeDefC_ alatt, és nyomon követheti azokat.

- A kezdeményezési hozzárendelés létrehozása után a kezdeményezéshez hozzáadott szabályzat-definíciók is részévé válnak a kezdeményezés hozzárendeléseinek.

- A kezdeményezési hozzárendelés kiértékelése után a rendszer a kezdeményezésen belüli összes szabályzatot is kiértékeli.
  Ha egy házirendet külön kell kiértékelnie, akkor jobb, ha nem szeretné belefoglalni egy kezdeményezésbe.

## <a name="azure-policy-objects"></a>Objektumok Azure Policy

### <a name="policy-definition"></a>Szabályzatdefiníció

Az Azure Policyban a szabályzatok létrehozásának és bevezetésének folyamata egy szabályzatdefiníció létrehozásával kezdődik. Mindegyik szabályzatdefiníció feltételekkel rendelkezik, amelyek teljesülése esetén életbe lép. Továbbá egy meghatározott hatással van, amely a feltételek teljesülése esetén zajlik.

Azure Policy számos beépített szabályzatot kínálunk, amelyek alapértelmezés szerint elérhetők. Például:

- **Engedélyezett Storage-fiók SKU** -jának (megtagadás): meghatározza, hogy az üzembe helyezett Storage-fiók SKU-méreteken belül van-e. Ennek hatására az összes olyan Storage-fiók megtagadható, amely nem felel meg a definiált SKU-méretek készletének.
- **Engedélyezett erőforrástípus** (megtagadás): meghatározza az üzembe helyezhető erőforrástípusok típusát. Ennek hatására az összes olyan erőforrást meg kell tagadni, amely nem része ennek a definiált listának.
- **Engedélyezett helyszínek** (megtagadás): korlátozza az új erőforrásokhoz elérhető helyekre vonatkozó korlátozásokat. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Engedélyezett virtuálisgép-SKU** -EK (megtagadás): az üzembe helyezhető virtuálisgép-SKU-készletet határozza meg.
- **Címke hozzáadása az erőforrásokhoz** (módosítás): a kötelező címkét és az alapértelmezett értéket alkalmazza, ha azt nem az üzembe helyezési kérelem adja meg.
- **Nem engedélyezett erőforrástípusok** (megtagadás): megakadályozza, hogy a rendszer ne telepítse az erőforrástípusok listáját.

A szabályzat-definíciók (beépített és egyéni definíciók) megvalósításához hozzá kell rendelnie őket. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

A szabályzat kiértékelése számos különböző művelettel, például házirend-hozzárendeléssel vagy házirend-frissítésekkel történik. A teljes listát lásd: [szabályzat-kiértékelési eseményindítók](./how-to/get-compliance-data.md#evaluation-triggers).

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](./concepts/definition-structure.md) foglalkozó témakört.

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

A paraméterek meghatározása házirend-definíció létrehozásakor történik. A paraméterek a definiálásukkor kapnak egy nevet és esetleg egy értéket is. Például megadhat egy paramétert egy _hely_ nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például _EastUS_ vagy _WestUS_.

A házirend-paraméterekkel kapcsolatos további információkért lásd: [definíciós struktúra – paraméterek](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

> [!NOTE]
> Az SDK, például az Azure CLI és a Azure PowerShell, a **PolicySet** nevű tulajdonságok és paraméterek használatával hivatkozhat a kezdeményezésekre.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

- **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
- Az **operációs rendszer biztonsági réseinak figyelése Security Centerban** – olyan kiszolgálók figyelésére, amelyek nem elégítik ki a beállított alapkonfigurációt.
- **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. A kezdeményezési paraméterek a kezdeményezésen belüli szabályzat-definíciók által használt paraméterek.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) az **A szabályzat** és a **B szabályzat** szabályzatdefiníció tartozik, amelyek mindketten különböző típusú paramétert várnak:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | array  |Ez a paraméter sztringek listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |sztring |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa sztringként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

- A kezdeményezésen belüli szabályzatdefiníciók paramétereinek használata. Ebben az esetben az _allowedLocations_ és az _allowedSingleLocation_ lesz a **C kezdeményezés** kezdeményezési paramétere.
- Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. Ebben a példában megadhat egy listát a **policya** paraméterhez – **allowedLocations** és **b szabályzat** paraméter – **allowedSingleLocation**. Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
- Adjon meg egy listát mindazon lehetséges _értékekről_, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

Ha egy kezdeményezési definícióban értéket hoz létre, nem adhat meg más értéket a kezdeményezési hozzárendelés során, mert nem része a listának.

Ha többet szeretne megtudni a kezdeményezési definíciók struktúrájáról, tekintse át a [kezdeményezési definíció szerkezetét](./concepts/initiative-definition-structure.md).

### <a name="assignments"></a>Hozzárendelések

A hozzárendelés olyan házirend-definíció vagy kezdeményezés, amely egy adott hatókörön belülre van rendelve. Ez a hatókör egy [felügyeleti csoportból](../management-groups/overview.md) egy adott erőforrásra terjedhet. A _hatókör_ kifejezés az összes olyan erőforrásra, erőforrás-csoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyhez a definíció hozzá van rendelve. A hozzárendeléseket az összes alárendelt erőforrás örökli. Ez a kialakítás azt jelenti, hogy az erőforráscsoporthoz alkalmazott definíció az adott erőforráscsoport erőforrásaira is vonatkozik. Kihagyhat azonban egy alhatókört a hozzárendelésből.

Az előfizetés hatókörében például hozzárendelhet egy olyan definíciót, amely megakadályozza a hálózati erőforrások létrehozását. Kizárhatja az adott előfizetéshez tartozó erőforráscsoportot, amely a hálózati infrastruktúrához készült. Ezután hozzáférést biztosíthat ehhez a hálózati erőforrás-csoporthoz azokhoz a felhasználókhoz, akikkel megbízik a hálózati erőforrások létrehozásában.

Egy másik példában előfordulhat, hogy a felügyeleti csoport szintjén hozzá kell rendelnie egy erőforrástípus engedélyezési lista definícióját. Ezután rendeljen hozzá egy engedékenyebb szabályzatot (több erőforrástípus engedélyezése) alárendelt felügyeleti csoportban, vagy akár közvetlenül előfizetésekben is. Ez a példa azonban nem fog működni, mert Azure Policy egy explicit megtagadási rendszer. Ehelyett ki kell zárnia a alárendelt felügyeleti csoportot vagy előfizetést a felügyeleti csoport szintű hozzárendelésből. Ezután rendelje hozzá a további engedékenység definícióját a alárendelt felügyeleti csoport vagy az előfizetés szintjén. Ha bármely hozzárendelés egy erőforrás megtagadását eredményezi, akkor az erőforrás csak úgy engedélyezhető, hogy módosítsa a megtagadási hozzárendelést.

A hozzárendelések portálon keresztüli beállításával kapcsolatos további információkért lásd: [szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához az Azure-környezetben](./assign-policy-portal.md). A [PowerShellhez](./assign-policy-powershell.md) és az [Azure CLI-hez](./assign-policy-azurecli.md) is elérhetők lépések. A hozzárendelési struktúrával kapcsolatos információkért lásd: [hozzárendelések szerkezete](./concepts/assignment-structure.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Azure Policy objektumok maximális száma

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb fogalmat, folytatásként a következő témaköröket javasoljuk:

- [Tekintse át a szabályzat-definíció szerkezetét](./concepts/definition-structure.md).
- [Rendeljen hozzá egy szabályzat-definíciót a portál használatával](./assign-policy-portal.md).
