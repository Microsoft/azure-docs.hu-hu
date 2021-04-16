---
title: DoD Impact Level 5 tervminta
description: A DoD Impact Level 5 tervminta üzembe helyezési lépései, beleértve a terv összetevő-paraméterének részleteit.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: b978b9fb30732c13785a6a425e5195daf67bae0f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377403"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>A DoD Impact Level 5 tervminta üzembe helyezése

Az Azure Blueprints Department of Defense Impact Level 5 (DoD IL5) tervmintáját a következő lépésekben kell üzembe helyezni:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha még nincs előfizetése Azure Government igényelj [próba-előfizetést](https://azure.microsoft.com/global-infrastructure/government/request/) a kezdés előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **DoD Impact Level 5** tervmintát az Egyéb minták _alatt,_ és válassza **a Minta használata lehetőséget.**

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve:** Adja meg a DoD Impact Level 5 tervminta másolatának nevét.
   - **Definíció helye:** A három ponttal válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A tervminta másolata testre szabható a környezet és az igények szerint, de ez a módosítás távolodhat a DoD Impact Level 5 vezérlőinek megfelelőtől.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon **meg módosítási megjegyzéseket,** például: "A DoD IL5 tervmintából közzétett első verzió.". Ezután válassza a lap alján található **Közzététel** lehetőséget.

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

     Hagyja meg az _alapértelmezett, rendszer által hozzárendelt_ felügyelt identitás beállítást.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek,](../../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak meghatározva. A teljes lista- vagy összetevőparamétereket és azok leírását az Artifact parameters table (Összetevőparaméterek [táblázata) tartalmazza.](#artifact-parameters-table)

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Azon felhasználók listája, akik szerepelniük kell a Windows rendszerű virtuális gépek rendszergazdái csoportjában|Azon felhasználók pontosvesszővel elválasztott listája, akiknek szerepelnie kell a Rendszergazdák helyi csoportban; Pl: Rendszergazda; myUser1; myUser2|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A Windows rendszerű virtuális gépek rendszergazdái csoportból kizárt felhasználók listája|Azon felhasználók pontosvesszővel elválasztott listája, akik ki vannak zárva a Rendszergazdák helyi csoportból; Pl: Rendszergazda; myUser1; myUser2|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeken engedélyezve kell lennie a diagnosztikai naplóknak||
|DoD Impact Level 5|Szabályzat-hozzárendelés|Log Analytics-munkaterület azonosítója virtuálisgép-ügynök jelentéséhez|Annak a Log Analytics-munkaterületnek az azonosítója (GUID), ahol a virtuális gépek ügynökének jelentést kell készítése|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Azon régiók listája, ahol Network Watcher engedélyezni kell|A régiók teljes listájának megjelenik a Get-AzLocation,|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Minimális TLS-verzió Windows-webkiszolgálókhoz|A windowsos webkiszolgálókon engedélyezni szükséges minimális TLS protokollverzió|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A PHP legújabb verziója|A legújabb támogatott PHP-verzió a App Services|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Legújabb Java-verzió|A legújabb támogatott Java-verzió a App Services|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A Windows Python legújabb verziója|A legújabb támogatott Python-verzió a App Services|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A Linux Python legújabb verziója|Legújabb támogatott Python-verzió App Services|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Nem kötelező: Azon Windows rendszerű virtuálisgép-rendszerképek listája, amelyek támogatják a Log Analytics-ügynököt a naplózási hatókörhöz való hozzáadáshoz|A képek pontosvesszővel elválasztott listája; Például: /subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Nem kötelező: Azon Linux rendszerű virtuálisgép-rendszerképek listája, amelyek támogatják a Log Analytics-ügynököt a naplózási hatókörhöz való hozzáadáshoz|A képek pontosvesszővel elválasztott listája; Például: /subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A házirend hatása: Lemeztitkosítást kell alkalmazni a virtuális gépeken|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Engedélyezni kell az e-mailes értesítést az előfizetés tulajdonosának a nagy súlyosságú riasztások esetén|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: A függvényalkalmazások esetében a távoli hibakeresést ki kell kapcsolva|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy .NET-keretrendszer verzió a legújabb, ha a függvényalkalmazás részeként használják|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: transzparens adattitkosítás SQL-adatbázisokra vonatkozó adatokat engedélyezni kell|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A sebezhetőségi felmérést engedélyezni kell a felügyelt SQL-példányon|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként használják|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Egy Azure Active Directory kell kiépítve az SQL-kiszolgálókhoz|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Csak a Redis Cache kapcsolatokat kell engedélyezni|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A végpontvédelmi megoldást telepíteni kell a virtuálisgép-méretezési készletekre|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Tárfiókok korlátlan hálózati hozzáférésének naplózása|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A felügyelt SQL-példány speciális adatbiztonsági beállításainak tartalmaznia kell egy e-mail-címet a biztonsági riasztások fogadására|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A virtuálisgép-méretezési készletek biztonsági beállításainak biztonsági réseit helyre kelligazító|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Engedélyezni kell a biztonságos átvitelt a tárfiókok között|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A georedundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a webalkalmazás részeként használják|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az előfizetéshez meg kell adni egy biztonsági kapcsolattartó e-mail-címét|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A CORS nem engedélyezheti minden erőforrás számára a webalkalmazások hozzáférését|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az írási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Az elavult fiókokat el kell távolítani az előfizetésből|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A függvényalkalmazás csak HTTPS-protokollon keresztül érhető el|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként használja|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a függvényalkalmazás részeként használja|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a WEBalkalmazás részeként használja|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként használják|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A biztonsági réseket egy sebezhetőségi felmérési megoldásnak kell orvosolni|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a webalkalmazás részeként használják|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A rendszerfrissítéseket telepíteni kell a gépekre|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha az API-alkalmazás részeként használják|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatásához használja|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az API-alkalmazásban a legújabb TLS-verziót kell használni|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az MFA-t engedélyezni kell az előfizetés írási engedélyekkel rendelkező fiókjainál|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az SQL Server speciális adatbiztonsági beállításainak tartalmaznia kell egy e-mail-címet a biztonsági riasztások fogadására|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használja|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A Microsoft IaaSAntimalware bővítményt Windows-kiszolgálókra kell telepíteni|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a függvényalkalmazás részeként használja|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az internetre néző végponton keresztüli hozzáférést korlátozni kell|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Security Center standard tarifacsomag legyen kiválasztva|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Egyéni RBAC-szabályok használatának naplózása|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A webalkalmazás csak HTTPS-protokollon keresztül érhető el|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Engedélyezni kell az SQL Serveren végzett naplózást|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A Log Analytics-ügynököt telepíteni kell a virtuális gépekre|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: DDoS Protection Standard beállításnak engedélyezve kell lennie|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a függvényalkalmazás részeként használják|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Engedélyezni kell a speciális adatbiztonságot a felügyelt SQL-példányon|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A rendszergazdáknak és előfizetések tulajdonosainak küldött e-mailes értesítéseket engedélyezni kell a felügyelt SQL-példány speciális adatbiztonsági beállításaiban|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Hiányzó Endpoint Protection figyelése a Azure Security Center|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A virtuális gépeken szükség szerint kell szabályozni a hálózathoz való hozzáférést|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az előfizetéshez meg kell adni egy biztonsági kapcsolattartó telefonszámát|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Service Fabric fürtök csak ügyfél Azure Active Directory hitelesítéshez használjanak|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az API-alkalmazás csak HTTPS-protokollon keresztül érhető el|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Az Advanced Threat Protection-típusokat "Mind" beállításra kell állítani a felügyelt SQL-példányban Advanced Data Security beállításokban|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A tárfiókok esetében engedélyezni kell a georedundáns tárolást|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy .NET-keretrendszer legújabb verziójú, ha az API-alkalmazás részeként használják|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A virtuálisgép-méretezési készletek rendszerfrissítéseit telepíteni kell|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A rendszergazdáknak és előfizetés-tulajdonosoknak küldött e-mailes értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: A webalkalmazások esetében a távoli hibakeresést ki kell kapcsolva|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A hosszú távú georedundáns biztonsági mentést engedélyezni kell a Azure SQL adatbázisokhoz|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A gépek biztonsági konfigurációjában a biztonsági réseket helyre kell ásni|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Győződjön meg arról, hogy a HTTP-verzió a legújabb, ha a függvényalkalmazás futtatásához használja|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Az MFA-t engedélyezni kell az előfizetés olvasási engedélyekkel rendelkező fiókjaiban|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: Az Advanced Threat Protection-típusokat "Mind" beállításra kell állítani az SQL Server Advanced Data Security beállításaiban|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A tárolóbiztonsági konfigurációk biztonsági réseit helyre kell helyezni|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: A távoli hibakeresést ki kell kapcsolva a API Apps|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A Log Analytics-ügynököt telepíteni kell a Virtual Machine Scale Sets|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A webalkalmazásban a legújabb TLS-verziót kell használni|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|Szabályzat hatása: A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: A függvényalkalmazásban a legújabb TLS-verziót kell használni|Azure Policy a szabályzatra vonatkozó hatás; A hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: [Előzetes verzió]: A Kubernetes Servicest frissíteni kell egy nem sebezhető Kubernetes-verzióra|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|
|DoD Impact Level 5|Szabályzat-hozzárendelés|A szabályzat hatása: Az SQL-adatbázisok biztonsági réseit meg kell orvosolni|Azure Policy a szabályzat hatása; a hatásokkal kapcsolatos további információkért látogasson el ide: https://aka.ms/policyeffects|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintotta a DoD Impact Level 5 tervminta üzembe helyezésének lépéseit, a következő cikkekben megismerheti a terv- és vezérlőelem-leképezést:

> [!div class="nextstepaction"]
> [DoD Impact Level 5 terv – Áttekintés](./index.md) 
>  [DoD Impact Level 5 terv – Vezérlőelem-leképezés](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.