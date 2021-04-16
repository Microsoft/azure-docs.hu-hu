---
title: DoD Impact Level 4 tervminta
description: A DoD Impact Level 4 tervminta üzembe helyezési lépései, beleértve a terv összetevő-paraméterének részleteit.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: ce0d2d162bf77c147e0e4bc26e68964cd50f4372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378448"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>A DoD Impact Level 4 tervminta üzembe helyezése

Az Azure Blueprints Department of Defense Impact Level 4 (DoD IL4) tervmintáját a következő lépésekben kell üzembe helyezni:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha még nincs előfizetése Azure Government igényelj [próba-előfizetést](https://azure.microsoft.com/global-infrastructure/government/request/) a kezdés előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **DoD Impact Level 4** tervmintát az Egyéb minták _alatt,_ és válassza **a Minta használata lehetőséget.**

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve:** Adja meg a DoD Impact Level 4 tervminta másolatának nevét.
   - **Definíció helye:** A három ponttal válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A tervminta másolata testre szabható a környezet és az igények szerint, de ez a módosítás távolodhat a DoD Impact Level 4 vezérlőivel való igazítástól.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon **meg módosítási megjegyzéseket,** például: "A DoD IL4 tervmintából közzétett első verzió.". Ezután válassza a lap alján található **Közzététel** lehetőséget.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

A tervminta sikeres közzététele után hozzárendelhető egy előfizetéshez a felügyeleti csoporton belül, amelybe mentve lett. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések:** Válasszon ki egy vagy több olyan előfizetést, amely abban a felügyeleti csoportban van, amelybe a tervminta másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve:** A rendszer előre kitölti a nevet a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely:** Válassza ki azt a régiót, ahol a felügyelt identitás létre lesz hozva. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [Azure-erőforrások felügyelt identitása.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Tervdefiníció verziója:** Válassza **ki** a tervminta másolatának Közzétett verzióját.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszer által hozzárendelt_ felügyelt identitás beállítást.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek,](../../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak meghatározva. A teljes lista- vagy összetevőparamétereket és azok leírását az [Artifact parameters table (Összetevő-paraméterek táblázata) tartalmazza.](#artifact-parameters-table)

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Engedélyezett helyek|Szabályzat-hozzárendelés|Engedélyezett helyek|Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a szervezet megadhat az erőforrások üzembe helyezésekor. A földrajzi megfelelőségi követelmények betartására szolgál.|
|Erőforráscsoportok engedélyezett helyei|Szabályzat-hozzárendelés |Engedélyezett helyek|Ezzel a szabályzattal korlátozhatja, hogy a szervezet mely helyeken hozhat létre erőforráscsoportokat. A földrajzi megfelelőségi követelmények betartására szolgál.|
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|A megőrzési időtartam napokban megadott értéke (a 0 korlátlan adatmegőrzést jelez)|Megőrzési napok (nem kötelező, 180 nap, ha nincs megadva)|
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|Az SQL Server naplózási szolgáltatásának tárfiókjának erőforráscsoport-neve|A naplózás az adatbázis-eseményeket az Azure Storage-fiók naplóiba írja (minden régióban létrejön egy tárfiók, ahol létrejön egy SQL Server, amelyet az adott régióban található összes kiszolgáló megoszt). Fontos – a naplózás megfelelő működéséhez ne törölje vagy nevezze át az erőforráscsoportot vagy a tárfiókokat.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|Tárfiók-előtag a hálózati biztonsági csoportok diagnosztikához|Ez az előtag a hálózati biztonsági csoport helyével kombinálva lesz létrehozva a tárfiók neveként.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|A hálózati biztonsági csoportok diagnosztikához használt tárfiók erőforráscsoport-neve (léteznie kell)|Az erőforráscsoport, amelybe a tárfiók létre lesz hozva. Ennek az erőforráscsoportnak már léteznie kell.|
|Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuálisgép-méretezési készletekhez|Szabályzat-hozzárendelés|Log Analytics-munkaterület Linux rendszerű virtuálisgép-méretezési készletekhez|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, manuálisan kell megadnia a "Log Analytics-közreműködő" engedélyt (vagy hasonlót) a szabályzat-hozzárendelés főazonosítójának.|
|Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuálisgép-méretezési készletekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-rendszerképek listája, amelyek támogatják a Linux operációs rendszer hatókörbe való felvételét|Egy üres tömb használatával jelezhető, hogy nincsenek választható paraméterek: \[\]|
|Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépekhez|Szabályzat-hozzárendelés|Log Analytics-munkaterület Linux rendszerű virtuális gépekhez|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, manuálisan kell megadnia a "Log Analytics-közreműködő" engedélyt (vagy hasonlót) a szabályzat-hozzárendelés főazonosítójának.|
|Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-rendszerképek listája, amelyek támogatják a Linux operációs rendszer hatókörbe való felvételét|Üres tömbök használatával jelezhető, hogy nincsenek választható paraméterek: \[\]|
|Log Analytics-ügynök üzembe helyezése Windows rendszerű virtuálisgép-méretezési készletekhez|Szabályzat-hozzárendelés|Log Analytics-munkaterület Windows rendszerű virtuálisgép-méretezési készletekhez|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, manuálisan kell megadnia a "Log Analytics-közreműködő" engedélyt (vagy hasonlót) a szabályzat-hozzárendelés fő azonosítójának.|
|Log Analytics-ügynök üzembe helyezése Windows rendszerű virtuálisgép-méretezési készletekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-rendszerképek listája, amelyek a hatókörbe felvenni támogatott Windows operációs rendszereket támogatnak|Egy üres tömb használatával jelezhető, hogy nincsenek választható paraméterek: \[\]|
|Log Analytics-ügynök üzembe helyezése Windows rendszerű virtuális gépekhez|Szabályzat-hozzárendelés|Log Analytics-munkaterület Windows rendszerű virtuális gépekhez|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, manuálisan kell megadnia a "Log Analytics-közreműködő" engedélyt (vagy hasonlót) a szabályzat-hozzárendelés fő azonosítójának.|
|Log Analytics-ügynök üzembe helyezése Windows rendszerű virtuális gépekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-rendszerképek listája, amelyeken támogatott a Windows operációs rendszer hozzáadása a hatókörbe|Egy üres tömb használatával jelezhető, hogy nincsenek választható paraméterek: \[\]|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A Rendszergazdák helyi csoport tagjai|Azon tagok pontosvesszővel tagolt listája, amelyek a Rendszergazdák helyi csoportból kizárhatók. Pl. Rendszergazda; myUser1; myUser2|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A Rendszergazdák helyi csoportból kizárható tagok|Azon tagok pontosvesszővel tagolt listája, amelyeknek szerepelnie kell a Rendszergazdák helyi csoportban. Pl. Rendszergazda; myUser1; myUser2|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyek esetében engedélyezni kell a diagnosztikai naplókat|Azon erőforrástípusok listája, amelyek naplózva vannak, ha a diagnosztikai naplók beállítása nincs engedélyezve. Az elfogadható értékeket a diagnosztikai [naplók Azure Monitor talál.](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Log Analytics-munkaterület azonosítója, amelyhez a virtuális gépeket konfigurálni kell|Ez annak a Log Analytics-munkaterületnek az azonosítója (GUID), amelyhez a virtuális gépeket konfigurálni kell.|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A hosszú távú georedundáns biztonsági mentést engedélyezni kell a Azure SQL adatbázisokhoz|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A sebezhetőségi felmérést engedélyezni kell a felügyelt SQL-példányon|A szabályzat hatásaival kapcsolatos információk a [További Azure Policy találhatók.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon|A szabályzat hatásaival kapcsolatos információkat a További Azure Policy [oldalon talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Engedélyezni kell a georedundáns tárolást a tárfiókok számára|A szabályzat hatásaival kapcsolatos információk a [További Azure Policy találhatók.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL|A szabályzat hatásaival kapcsolatos információk a [További Azure Policy találhatók.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A georedundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL|A szabályzat hatásaival kapcsolatos információk a [További Azure Policy találhatók.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie|A szabályzat hatásaival kapcsolatos információkat a További Azure Policy [oldalon talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Az írási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Az elavult fiókokat el kell távolítani az előfizetésből|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A CORS-nak nem szabad minden erőforrás számára engedélyeznie a webalkalmazáshoz való hozzáférést|A szabályzat hatásaival kapcsolatos információkat a [További Azure Policy talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|A virtuálisgép-méretezési készletek rendszerfrissítéseit telepíteni kell|A szabályzat hatásaival kapcsolatos információk a [További Azure Policy találhatók.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon|A szabályzat hatásaival kapcsolatos információkat a További Azure Policy [oldalon talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban|A szabályzat hatásaival kapcsolatos információkat a További Azure Policy [oldalon talál.](../../../policy/concepts/effects.md)|
|\[Előzetes \] verzió: DoD Impact Level 4|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetés írási engedélyekkel rendelkező fiókjaiban|A szabályzat hatásaival kapcsolatos információkat a További Azure Policy [oldalon talál.](../../../policy/concepts/effects.md)|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintte a DoD Impact Level 4 tervminta üzembe helyezésének lépéseit, tekintse meg a következő cikkeket a terv és a vezérlőelem-leképezés megismerése érdekében:

> [!div class="nextstepaction"]
> [DoD Impact Level 4 terv – Áttekintés](./index.md) 
>  [DoD Impact Level 4 terv – Vezérlőelem-leképezés](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
