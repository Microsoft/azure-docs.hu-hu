---
title: DoD Impact Level 5 tervezet minta
description: Üzembe helyezheti a DoD Impact Level 5 tervezetének lépéseit, beleértve a Blueprint-összetevők paraméterének részleteit.
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: deb6ffa0f886cc6a4a9bea10a38d7cd82e7df8f1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978227"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>A DoD Impact Level 5 Blueprint minta üzembe helyezése

Az Azure-tervezetek a Defense Impact Level 5 (DoD IL5) tervezet mintájának üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **tervrajzokat**.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg a **DoD Impact Level 5** tervezet mintát _más minták_ alatt, és válassza a **minta használata**lehetőséget.

1. Adja meg a tervezet mintájának _alapjait_ :

   - **Terv neve**: adjon meg egy nevet a DoD Impact Level 5 Blueprint minta példányának.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza ki az oldal tetején található _összetevők fület_ , vagy a **következőt:** összetevők az oldal alján.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a DoD Impact Level 5 vezérlőn kívülről is áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint például a DoD IL5 Blueprint minta első verziója. Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a terv **kiosztása** elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paramétereinek

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták **díjmentesek**. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, akiknek szerepelniük kell a Windows VM-rendszergazdák csoportban|A rendszergazdák helyi csoportba foglalandó felhasználók pontosvesszővel tagolt listája; Pl.: rendszergazda; myUser1; myUser2|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A Windows VM-Rendszergazdák csoportból kizárt felhasználók listája|A rendszergazdák helyi csoportba kizárandó felhasználók pontosvesszővel tagolt listája; Pl.: rendszergazda; myUser1; myUser2|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak||
|DoD Impact Level 5|Szabályzat-hozzárendelés|Log Analytics munkaterület-azonosító a virtuálisgép-ügynök jelentéskészítéséhez|Az Log Analytics munkaterület azonosítója (GUID), amelyben a virtuális gépeket tartalmazó ügynököknek jelenteniük kell|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Azon régiók listája, amelyeken engedélyezni kell a Network Watcher|A régiók teljes listájának megtekintéséhez használja a Get-AzLocation,|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A Windows-webkiszolgálók minimális TLS-verziója|A TLS protokoll minimális verziója, amelyet engedélyezni kell a Windows-webkiszolgálókon|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A PHP legújabb verziója|A App Services legújabb támogatott PHP-verziója|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Legújabb Java-verzió|A legújabb támogatott Java-verzió a App Services|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Legújabb Windows Python-verzió|A App Services legújabb támogatott Python-verziója|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Legújabb Linux Python-verzió|A App Services legújabb támogatott Python-verziója|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Nem kötelező: a Windows rendszerű virtuálisgép-rendszerképek listája, amelyek támogatják Log Analytics ügynököt a naplózási hatókörhöz való hozzáadáshoz|A képek pontosvesszővel tagolt listája; Pl.:/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Nem kötelező: azon linuxos virtuálisgép-rendszerképek listája, amelyek támogatják Log Analytics ügynököt a naplózási hatókörhöz való hozzáadáshoz|A képek pontosvesszővel tagolt listája; Pl.:/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az előfizetéshez egynél több tulajdonos rendelhető hozzá|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a lemezes titkosítást a virtuális gépeken kell alkalmazni|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: engedélyezni kell a nagy súlyosságú riasztások előfizetési tulajdonosának szóló e-mailes értesítést.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a távoli hibakeresést ki kell kapcsolni a Function apps esetében|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a függvényalkalmazás részeként használja|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az SQL-adatbázisokon engedélyezni kell a transzparens adattitkosítás|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az Endpoint Protection-megoldást telepíteni kell a virtuálisgép-méretezési csoportokra|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a felügyelt SQL-példány speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a virtuálisgép-méretezési csoportok biztonsági beállításaiban található biztonsági réseket szervizelni kell|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: engedélyezze a biztonságos átvitelt a Storage-fiókoknak.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a Geo-redundáns biztonsági mentést engedélyezni kell Azure Database for PostgreSQL|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az előfizetéshez meg kell adni egy biztonsági kapcsolattartási e-mail címet|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: a CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazásokhoz|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az elavult fiókokat el kell távolítani az előfizetésből|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Function alkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a biztonsági réseket a sebezhetőség-felmérési megoldásnak kell kijavítania|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a Geo-redundáns biztonsági mentést engedélyezni kell Azure Database for MySQL|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a webalkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a rendszerfrissítéseket telepíteni kell a gépekre.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a Java-verzió a legújabb, ha az API-alkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatására használatos|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni az API-alkalmazásban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az MFA-nak engedélyezve kell lennie az előfizetéséhez írási engedéllyel rendelkező fiókoknak|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az SQL Server speciális adatbiztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használatos.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a Microsoft IaaSAntimalware bővítményt Windows-kiszolgálókra kell telepíteni|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a Java-verzió a legújabb, ha a Function alkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: az internet felé irányuló végponton keresztüli hozzáférés korlátozása korlátozott|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: Security Center Standard díjszabási szintet kell kiválasztani|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az egyéni RBAC-szabályok használatának naplózása|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a webalkalmazásnak csak HTTPS-kapcsolaton keresztül kell elérhetőnek lennie|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az SQL Server naplózását engedélyezni kell|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a Log Analytics ügynöknek telepítve kell lennie a virtuális gépeken|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: DDoS Protection a standard beállítást engedélyezni kell|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a Function alkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításaiban.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: hiányzó Endpoint Protection figyelése Azure Security Center|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: az igény szerinti hálózati hozzáférés-vezérlést a virtuális gépekre kell alkalmazni|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az előfizetéshez meg kell adni egy biztonsági kapcsolatfelvételi telefonszámot|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: Service Fabric a fürtök csak Azure Active Directory használatát használják az ügyfél-hitelesítéshez|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az API-alkalmazás csak HTTPS protokollon keresztül érhető el|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: a komplex veszélyforrások elleni védelem típusainak "all" értékűnek kell lenniük az SQL felügyelt példány speciális adatbiztonsági beállításainál|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a Geo-redundáns tárterületet engedélyezni kell a Storage-fiókok esetében.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha az API-alkalmazás részeként van használatban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL Server speciális biztonsági beállításaiban.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a távoli hibakeresést ki kell kapcsolni a webalkalmazások esetében|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: a hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a számítógépeken a biztonsági konfiguráció biztonsági beállításainak javítását javítani kell|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a Function app futtatásához használatos.|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: a komplex veszélyforrások elleni védelem típusainak az SQL Server speciális biztonsági beállításaiban az "all" értékre kell állítani|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a távoli hibakeresést ki kell kapcsolni API Apps|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: az elavult, tulajdonosi engedélyekkel rendelkező fiókokat el kell távolítani az előfizetésből|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a Log Analytics ügynöknek telepítve kell lennie a Virtual Machine Scale Sets|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni a webalkalmazásban|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni a függvényalkalmazás|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A következő házirend hatása: [előzetes verzió]: a Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Házirend hatása: az SQL-adatbázisok biztonsági réseit szervizelni kell|A szabályzat Azure Policy hatása; a hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: https://aka.ms/policyeffects|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a DoD Impact Level 5 tervrajzi minta üzembe helyezésének lépéseit, tekintse meg a következő cikkeket a terv és a vezérlési leképezés megismeréséhez:

> [!div class="nextstepaction"]
> [DoD Impact Level 5 tervezet – áttekintés](./index.md) 
>  [DoD Impact Level 5 tervezet – vezérlési leképezés](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.