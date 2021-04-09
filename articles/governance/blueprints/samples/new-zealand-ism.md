---
title: Új-zélandi ISM korlátozott terv minta
description: Az új-zélandi ISM korlátozott terv mintájának áttekintése. Ennek a tervmintának a segítségével az ügyfelek adott vezérlőket mérhetnek fel.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803930"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Új-zélandi ISM korlátozott terv minta

Az új-zélandi ISM korlátozott terv mintája az irányítási őr-síneket olyan [Azure Policy](../../policy/overview.md) segítségével biztosítja, amelyek segítenek az egyes [új-zélandi adatok biztonsági manuális](https://www.nzism.gcsb.govt.nz/) ellenőrzésének értékelésében. Ez a terv segít az ügyfeleknek a szabályzatok alapkészletének üzembe helyezésében bármely olyan Azure-beli üzembe helyezett architektúrán, amely az új-zélandi ISM korlátozás alá eső vezérlőket implementálja

## <a name="control-mapping"></a>Vezérlőelem-leképezés

Az [Azure Policy-vezérlési leképezés](../../policy/samples/new-zealand-ism.md) részletesen ismerteti a tervben szereplő szabályzat-definíciókat, valamint azt, hogy a szabályzat-definíciók hogyan képezik le az új-zélandi információs biztonsági kézikönyvben található **vezérlőket** Architektúrához való hozzárendeléskor a rendszer kiértékeli az erőforrásokat a hozzárendelt szabályzat-definíciókkal való meg nem felelés esetén Azure Policy. További információ: [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Üzembe helyezés

A következő lépéseket kell végrehajtani az Azure-tervezetek üzembe helyezéséhez: új-zélandi ISM – korlátozott terv – minta

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

### <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg az **új-zélandi ISM korlátozott** tervezet mintát _más minták_ alatt, és válassza **a minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet az új-zélandi ISM korlátozott tervezet mintájának másolatához.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a terv minta részét képező összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

### <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás elmozdulhat az új-zélandi ISM korlátozott vezérlőkkel való összehangolással.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió, amely megjelent az új-zélandi ISM korlátozott tervből". Ezután válassza a lap alján található **Közzététel** lehetőséget.

### <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

### <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, akiknek szerepelniük kell a Windows VM-rendszergazdák csoportban|A rendszergazdák helyi csoportba foglalandó felhasználók pontosvesszővel tagolt listája; Pl.: rendszergazda; myUser1; myUser2|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, akiket ki kell zárni a Windows VM-Rendszergazdák csoportból|A rendszergazdák helyi csoportba kizárandó felhasználók pontosvesszővel tagolt listája; Pl.: rendszergazda; myUser1; myUser2|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, amelyeket a Windows VM-rendszergazdák csoportnak csak tartalmaznia kell|A rendszergazdák helyi csoportjának várt tagjainak pontosvesszővel tagolt listája; Pl.: rendszergazda; myUser1; myUser2|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Log Analytics munkaterület-azonosító a virtuálisgép-ügynök jelentéskészítéséhez|Az Log Analytics munkaterület azonosítója (GUID), amelyben a virtuális gépeket tartalmazó ügynököknek jelenteniük kell|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a webalkalmazási tűzfalat (WAF) engedélyezni kell az Azure bejárati ajtó szolgáltatásához|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az SQL Server sebezhetőségi felmérési beállításaiban szerepelnie kell egy e-mail-címnek a vizsgálati jelentések fogadásához.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: az adaptív hálózat megerősítő javaslatainak alkalmazása szükséges az internetre irányuló virtuális gépeken|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az előfizetéshez egynél több tulajdonos rendelhető hozzá|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a lemezes titkosítást a virtuális gépeken kell alkalmazni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a távoli hibakeresést ki kell kapcsolni a Function apps esetében|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a webalkalmazási tűzfal (WAF) a megadott módot használja Application Gateway|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A WAF mód követelménye Application Gateway|A megelőzési vagy észlelési módot engedélyezni kell a Application Gateway szolgáltatásban|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az SQL-adatbázisokon engedélyezni kell a transzparens adattitkosítás|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a sebezhetőségi felmérést engedélyezni kell a felügyelt SQL-példányon.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Nem kötelező: azon egyéni virtuálisgép-rendszerképek listája, amelyeken támogatott a Windows operációs rendszer, amely a katalógusban található rendszerképekhez való hozzáadást támogatja a Szabályzathoz: telepítés – a függőségi ügynök konfigurálása a Windows rendszerű virtuális gépeken való engedélyezéshez|A vendég konfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a Redis csak az Azure cache-hez való biztonságos kapcsolatok engedélyezése engedélyezett|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az Endpoint Protection-megoldást telepíteni kell a virtuálisgép-méretezési csoportokra|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: a Windows rendszerű gépek naplózása a rendszergazdák csoport tagjai közül hiányzik|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Nem kötelező: azon egyéni virtuálisgép-rendszerképek listája, amelyeken támogatott a Windows operációs rendszer, amely a katalógusban található rendszerképekhez a (z) [előzetes verzió] házirendhez hozzáadva, Log Analytics ügynöknek engedélyezve kell lennie a felsorolt virtuálisgép-lemezképekhez.|A vendég konfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Nem kötelező: azon egyéni virtuálisgép-rendszerképek listája, amelyek támogatottak a Linux operációs rendszernek a katalógusban található rendszerképekhez való hozzáadásához a szabályzat: [előzetes verzió]: Log Analytics ügynöknek engedélyezve kell lennie a felsorolt virtuálisgép-rendszerképeknél.|A vendég konfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a Storage-fiókoknak korlátoznia kell a hálózati hozzáférést|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Nem kötelező: azon egyéni virtuálisgép-rendszerképek listája, amelyeken támogatott a Windows operációs rendszer, amely a katalógusban található rendszerképekhez való hozzáadást támogatja a Szabályzathoz: telepítés – a függőségi ügynök konfigurálása a Windows virtuálisgép-méretezési csoportokban való engedélyezéshez.|A vendég konfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a virtuálisgép-méretezési csoportok biztonsági beállításaiban található biztonsági réseket szervizelni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: a rendszergazdák csoportba tartozó további fiókkal rendelkező Windows-gépek naplózása|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: engedélyezze a biztonságos átvitelt a Storage-fiókoknak.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: a webalkalmazási tűzfal (WAF) az Azure bejárati szolgáltatásának megadott módját használja|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A WAF mód követelménye az Azure bejárati ajtó szolgáltatásához|A megelőzési vagy észlelési módot engedélyezni kell az Azure bejárati ajtó szolgáltatásban|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: a biztonságos alkalmazások meghatározásához engedélyezni kell az adaptív alkalmazás-vezérlőket a gépeken|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A következő házirend hatása: [előzetes verzió]: a Storage-fiók nyilvános hozzáférését nem szabad engedélyezni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: A sebezhetőségi felmérési megoldást engedélyezni kell a virtuális gépeken|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a webalkalmazási tűzfalat (WAF) engedélyezni kell Application Gateway|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: a CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazásokhoz|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: a nem biztonságos kommunikációs protokollt használó Windows-webkiszolgálók naplózása|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A Windows-webkiszolgálók minimális TLS-verziója|Az alacsonyabb TLS-verziókkal rendelkező Windows-webkiszolgálókat a rendszer nem megfelelőként fogja értékelni|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Nem kötelező: azon egyéni virtuálisgép-rendszerképek listája, amelyek támogatottak a Linux operációs rendszernek a katalógusban található rendszerképekhez való hozzáadásához Log Analytics.|A vendég konfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Nem kötelező: azon egyéni virtuálisgép-rendszerképek listája, amelyek támogatják a Windows operációs rendszernek a katalógusban található rendszerképekhez való hozzáadásához szükséges hatókört: Log Analytics ügynöknek engedélyezve kell lennie a virtuálisgép-méretezési csoportokban a felsorolt virtuálisgép-lemezképekhez.|A vendég konfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: a rendszergazdák csoportba tartozó, a megadott tagokkal rendelkező Windows-gépek naplózása|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az elavult fiókokat el kell távolítani az előfizetésből|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: az Azure-előfizetéseknek log-profillal kell rendelkezniük a műveletnapló számára|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak||
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a rendszerfrissítéseket telepíteni kell a gépekre.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni az API-alkalmazásban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az MFA-nak engedélyezve kell lennie az előfizetéséhez írási engedéllyel rendelkező fiókoknak|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a Microsoft IaaSAntimalware bővítményt Windows-kiszolgálókra kell telepíteni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a webalkalmazásnak csak HTTPS-kapcsolaton keresztül kell elérhetőnek lennie|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: Azure DDoS Protection a standard beállítást engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: hiányzó Endpoint Protection figyelése Azure Security Center|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: a tevékenység naplóját legalább egy évig meg kell őrizni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: a virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: Service Fabric a fürtök csak Azure Active Directory használatát használják az ügyfél-hitelesítéshez|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az API-alkalmazás csak HTTPS protokollon keresztül érhető el|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: a Windows Defender Exploit Guard-et nem engedélyező Windows-gépek naplózása|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: a Windows Defender Exploit Guard-t nem engedélyező Windows-gépek naplózása|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Megfelelőségi állapot jelentés olyan Windows rendszerű gépekről, amelyeken a Windows Defender Exploit Guard nem érhető el|A Windows Defender Exploit Guard csak a Windows 10/Windows Server 1709-es frissítéssel kezdődően érhető el. Ha ezt az értéket "nem megfelelő" értékre állítja, akkor a régebbi verziókkal rendelkező gépeket jeleníti meg, amelyeken a Windows Defender Exploit Guard nem érhető el (például Windows Server 2012 R2) nem megfelelőként. Ha ezt az értéket "megfelelő" értékre állítja, a megfelelőként jeleníti meg ezeket a gépeket.|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a távoli hibakeresést ki kell kapcsolni a webalkalmazások esetében|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a számítógépeken a biztonsági konfiguráció biztonsági beállításainak javítását javítani kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a távoli hibakeresést ki kell kapcsolni API Apps|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: a jelszavak nélküli fiókok távoli kapcsolatait engedélyező linuxos gépek naplózása|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: olyan linuxos gépek naplózása, amelyek lehetővé teszik a távoli kapcsolatokat jelszavak nélküli fiókokból|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: az elavult, tulajdonosi engedélyekkel rendelkező fiókokat el kell távolítani az előfizetésből|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni a webalkalmazásban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a Windows rendszerű gépeknek meg kell felelniük a "biztonsági beállítások – fiókok házirendjei" követelményeinek.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A Windows rendszerű virtuális gépek helyi fiókjaihoz tartozó korábbi jelszavak érvényesítése|Korlátozza a jelszó-újrafelhasználást – a jelszó megismétlése előtt hányszor kell létrehozni új jelszót egy felhasználói fiókhoz.|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: a Windows rendszerű számítógépeknek meg kell felelniük a "biztonsági beállítások – fiók házirendek" követelményeinek|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A Windows rendszerű virtuális gépek helyi fiókjainak maximális jelszava|Meghatározza, hogy legfeljebb hány nap telhet el a felhasználói fiók jelszavának módosítása előtt; az érték formátuma két egész szám, vesszővel elválasztva, egy befogadó tartomány megadása|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A jelszó minimális kora a Windows rendszerű virtuális gépek helyi fiókjainál|Azt határozza meg, hogy hány nap elteltével kell eltelnie a felhasználói fiók jelszavának megváltoztatásához.|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A jelszó minimális hossza a Windows rendszerű virtuális gépek helyi fiókjainál|A felhasználói fiók jelszava által tartalmazott karakterek minimális számát határozza meg|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A jelszónak meg kell felelnie a Windowsos virtuális gépek helyi fiókjainak összetettségi követelményei|Meghatározza, hogy a felhasználói fiók jelszavának bonyolultnak kell lennie; Ha szükséges, egy összetett jelszó nem tartalmazhatja a felhasználó fiókjának nevét vagy teljes nevét; legalább 6 karakter hosszúnak kell lennie; nagybetűket, kisbetűket, számokat és nem alfabetikus karaktereket tartalmazó kombinációt tartalmaz|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A házirend hatása: olyan linuxos gépek naplózása, amelyekhez jelszó nélküli fiók tartozik|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Az arc-kompatibilis kiszolgálók belefoglalása a szabályzat kiértékelése során: jelszó nélküli fiókkal rendelkező linuxos gépek naplózása|Az "igaz" kiválasztásával elfogadja, hogy az ív-kapcsolattal rendelkező számítógépeken havonta kell fizetnie|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni a függvényalkalmazás|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|A következő házirend hatása: [előzetes verzió]: az összes internetes forgalmat az üzembe helyezett Azure Firewall keresztül kell átirányítani|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Új-zélandi ISM-korlátozás|Szabályzat-hozzárendelés|Házirend hatása: az SQL-adatbázisok biztonsági réseit szervizelni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Következő lépések

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.