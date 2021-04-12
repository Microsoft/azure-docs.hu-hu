---
title: A FedRAMP High Blueprint minta üzembe helyezése
description: A FedRAMP magas tervrajzi mintájának üzembe helyezése, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 0ba321bb0048499e12eec55fbc55d67b0a1289f9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386702"
---
# <a name="deploy-the-fedramp-high-blueprint-sample"></a>A FedRAMP High Blueprint minta üzembe helyezése

Az Azure-tervezetek nagy FedRAMP való üzembe helyezéséhez a következő lépéseket kell végrehajtani:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **FedRAMP High** Blueprint mintát _más minták_ alatt, és válassza a **minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet a FedRAMP High Blueprint minta példányának.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a FedRAMP magas vezérlőkkel való összehangolását is áthelyezheti.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió, amely a FedRAMP High Blueprint mintából lett közzétéve." Ezután válassza a lap alján található **Közzététel** lehetőséget.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Log Analytics munkaterület-azonosító, amelyhez a virtuális gépeket konfigurálni kell|Ez a Log Analytics munkaterület azonosítója (GUID), amelyhez a virtuális gépeket konfigurálni kell.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak|A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Elfogadható értékek találhatók [Azure monitor diagnosztikai naplók sémái](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)között.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A Windows rendszerű virtuális gépek rendszergazdái csoportból kizárandó felhasználók listája|A rendszergazdák helyi csoportba kizárandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A Windows rendszerű virtuális gépek rendszergazdái csoportjának részét képező felhasználók listája|A rendszergazdák helyi csoportba foglalandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux-VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Log Analytics a Linux VM Scale Sets (VMSS) munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux-VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|A Linux rendszerű virtuális gépek Log Analytics munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[Előzetes verzió \] : log Analytics ügynök központi telepítése Windows VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows VM Scale Setshoz (VMSS)|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics ügynök központi telepítése Windows VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows rendszerű virtuális gépekhez|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon|Szabályzat-hozzárendelés|Hatás|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|A megőrzési időtartam napokban megadott értéke (a 0 korlátlan megőrzést jelez)|Megőrzési napok (nem kötelező, 180 nap, ha nincs megadva)|
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|Az SQL Server naplózásához használt Storage-fiók erőforráscsoport-neve|A naplózás az adatbázis-eseményeket egy naplóba írja az Azure Storage-fiókban (a Storage-fiók minden régióban létrejön, ahol létrejön egy SQL Server, amelyet az adott régióban lévő összes kiszolgáló megoszt majd). Fontos – a naplózás megfelelő működéséhez ne törölje vagy nevezze át az erőforráscsoportot vagy a Storage-fiókokat.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|A hálózati biztonsági csoport diagnosztika Storage-fiókjának előtagja|Ezt az előtagot a hálózati biztonsági csoport helyével együtt kell összekapcsolni a létrehozott Storage-fiók nevének létrehozásához.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|A hálózati biztonsági csoport diagnosztikát szolgáló Storage-fiók erőforráscsoport-neve (léteznie kell)|Az az erőforráscsoport, amelyben a Storage-fiók létre lesz hozva. Ez az erőforráscsoport már léteznie kell.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Erőforrások és erőforráscsoportok engedélyezett helyei|Az erőforrások üzembe helyezése során a szervezet által megadható Azure-beli helyszínek listája. Ezt a megadott értéket az "engedélyezett helyszínek" házirend is használja a házirend kezdeményezésen belül.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A sebezhetőségi felmérést engedélyezni kell Virtual Machines|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A Geo-redundáns tárterületet engedélyezni kell a Storage-fiókokhoz|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A webalkalmazás csak HTTPS protokollon keresztül érhető el|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Az elavult fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazás elérését|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetés írási engedélyekkel rendelkező fiókjaiban|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[Előzetes verzió \] : a FedRAMP magas szintű vezérlése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához|Szabályzat-hozzárendelés|A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|


## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a FedRAMP High Blueprint-minta üzembe helyezésének lépéseit, tekintse meg a következő cikkeket a terv és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [FedRAMP nagy terv – áttekintés](./index.md) 
>  [FedRAMP nagy terv – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
