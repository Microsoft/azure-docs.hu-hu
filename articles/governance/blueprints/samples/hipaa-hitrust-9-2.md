---
title: HIPAA HITRUST 9,2 tervezet minta áttekintése
description: A HIPAA HITRUST 9,2 tervezet mintájának áttekintése. Ez a Blueprint-minta segít az ügyfeleknek az adott HIPAA HITRUST 9,2-vezérlők értékelésében.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: bd65b6113f291457096bacc02bdbcfd92d6e0f84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915544"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>HIPAA HITRUST 9,2 tervezet minta

A HIPAA HITRUST 9,2 tervezet mintája az irányítási őr-síneket olyan [Azure Policy](../../policy/overview.md) segítségével biztosítja, amelyek segítenek az egyes HIPAA HITRUST 9,2-vezérlők értékelésében. Ez a terv segít az ügyfeleknek az olyan Azure-beli üzembe helyezett architektúrák alapkészletének üzembe helyezésében, amelyeknek az HIPAA HITRUST 9,2 vezérlőket kell alkalmazniuk.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

Az [Azure Policy-vezérlési leképezés](../../policy/samples/hipaa-hitrust-9-2.md) részletesen ismerteti a tervben található szabályzat-definíciókat, valamint azt, hogy ezek a szabályzat-definíciók hogyan képezik le a **megfelelőségi tartományokat** **és a** HIPAA HITRUST 9,2. Architektúrához való hozzárendeléskor a rendszer kiértékeli az erőforrásokat a hozzárendelt szabályzat-definíciókkal való meg nem felelés esetén Azure Policy. További információ: [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Üzembe helyezés

Az Azure-tervezetek üzembe helyezéséhez a HITRUST 9,2 terv HIPAA a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

### <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **HIPAA HITRUST** tervrajz mintát _más minták_ alatt, és válassza **a minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet a HIPAA HITRUST 9,2 Blueprint minta példányának.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

### <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a HIPAA HITRUST 9,2-vezérlőkkel való összehangolásból is áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió a HIPAA HITRUST 9,2 Blueprint Sample-ből". Ezután válassza a lap alján található **Közzététel** lehetőséget.

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

|Összetevő neve |Paraméter neve |Leírás |
|---|---|---|
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést |A túlzottan megengedhető bejövő NSG-szabályok figyelésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Fiókok: Vendégfiók állapota |Megadja, hogy a helyi vendég fiók le van-e tiltva. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken |Az alkalmazás-engedélyezési lista figyelésének engedélyezése vagy letiltása Azure Security Center |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Az internethez vagy a Windows-tartományhoz való egyidejű kapcsolódás engedélyezése |Itt adhatja meg, hogy a számítógépek nem csatlakozhatnak-e egy tartományon alapuló hálózathoz és egy nem tartományon alapuló hálózathoz egy időben. A 0 érték lehetővé teszi az egyidejű kapcsolatok használatát, az 1 értéket pedig letiltja. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Az API-alkalmazás csak HTTPS v2-en keresztül érhető el |HTTPS használatának figyelésének engedélyezése vagy letiltása az API app v2-ben |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Alkalmazások nevei (támogatja a helyettesítő karaktereket) |A telepítendő alkalmazások neveinek pontosvesszővel tagolt listája. például: "Microsoft SQL Server 2014 (64 bites); Microsoft Visual Studio code "vagy" Microsoft SQL Server 2014 * "(a" Microsoft SQL Server 2014 "kezdetű alkalmazásokkal való egyezéshez) |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Folyamatmegszakítás naplózása |Megadja, hogy a rendszer a naplózási eseményeket hozza-e létre egy folyamat bezárásakor. A kritikus folyamatok figyeléséhez ajánlott. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz |A Storage-fiókhoz való hálózati hozzáférés figyelésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Naplózás: Rendszer azonnali leállítása, ha a biztonsági naplók nem írhatók |Naplózza, ha a rendszer leáll, ha nem sikerül naplózni a biztonsági eseményeket. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Tanúsítvány ujjlenyomatai megfelelnek |A megbízható főtanúsítvány-tárolóban (CERT: \ LocalMachine\Root) található tanúsítvány-ujjlenyomatai megfelelnek pontosvesszővel tagolt listája. például: THUMBPRINT1; THUMBPRINT2; THUMBPRINT3 |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell |Diagnosztikai naplók figyelésének engedélyezése vagy letiltása a Batch-fiókokban |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Az Event hub diagnosztikai naplóit engedélyezni kell |Diagnosztikai naplók figyelésének engedélyezése vagy letiltása az Event hub-fiókokban |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat. |Diagnosztikai naplók figyelésének engedélyezése vagy letiltása Azure Search szolgáltatásban |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A Virtual Machine Scale Sets lévő diagnosztikai naplókat engedélyezni kell |Diagnosztikai naplók figyelésének engedélyezése vagy letiltása Service Fabric |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A lemezes titkosítást a virtuális gépeken kell alkalmazni |A VM-lemezek titkosításának figyelése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Nem biztonságos vendég bejelentkezések engedélyezése |Megadja, hogy az SMB-ügyfél engedélyezze-e a nem biztonságos vendég bejelentkezését egy SMB-kiszolgálóra. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken |A hálózati időponthoz való hozzáférés figyelésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A felügyeleti portokat be kell zárni a virtuális gépeken |A nyílt felügyeleti portok figyelésének engedélyezése vagy letiltása Virtual Machines |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában |Az előfizetésben írási engedéllyel rendelkező fiókokhoz tartozó MFA figyelésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon |Engedélyezheti vagy letilthatja az MFA figyelését az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokhoz |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Hálózati hozzáférés: Távolról elérhető beállításjegyzéki elérési utak |Meghatározza, hogy a beállításjegyzék mely elérési útjai lesznek elérhetők a hálózaton keresztül, a beállításkulcs hozzáférés-vezérlési listájában (ACL) felsorolt felhasználóktól és csoporttól függetlenül `winreg` . |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Hálózati hozzáférés: távolról elérhető beállításjegyzékbeli elérési utak és alárendelt elérési utak |Meghatározza, hogy a beállításjegyzék mely elérési útjai és alútvonalai lesznek elérhetők a hálózaton keresztül, a beállításkulcs hozzáférés-vezérlési listájában (ACL) felsorolt felhasználóktól és csoporttól függetlenül `winreg` . |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Hálózati hozzáférés: Névtelenül elérhető megosztások |Meghatározza, hogy mely hálózati megosztások érhetők el a névtelen felhasználók számára. Ennek a házirend-beállításnak az alapértelmezett konfigurációja csekély hatással van, mivel az összes felhasználót hitelesíteni kell, mielőtt hozzá tudnak férni a kiszolgálón található megosztott erőforrásokhoz. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Helyreállítási konzol: a hajlékonylemez-másolás és az összes meghajtó és az összes mappa hozzáférésének engedélyezése |Megadja, hogy elérhető legyen-e a helyreállítási konzol SET parancsa, amely lehetővé teszi a helyreállítási konzol környezeti változóinak beállítását. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz |A távoli hibakeresés figyelésének engedélyezése vagy letiltása az API-alkalmazáshoz |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz |A webalkalmazáshoz tartozó távoli hibakeresés figyelésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A Batch-fiókok naplóihoz szükséges megőrzés (napokban) |A kötelező diagnosztikai naplók megőrzési ideje (nap) |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A Azure Search szolgáltatásban található naplók kötelező megőrzése (nap) |A kötelező diagnosztikai naplók megőrzési ideje (nap) |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A naplók kötelező megőrzése (nap) az Event hub-fiókokban |A kötelező diagnosztikai naplók megőrzési ideje (nap) |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A hálózati biztonsági csoportok diagnosztikai beállításainak üzembe helyezéséhez a Storage-fiók (léteznie kell) erőforráscsoport-neve |Az az erőforráscsoport, amelyben a Storage-fiók létre lesz hozva. Ez az erőforráscsoport már léteznie kell. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Role-Based-Access Control (RBAC) kell használni a Kubernetes-szolgáltatásokban |A Kubernetes szolgáltatások figyelésének engedélyezése vagy letiltása a RBAC engedélyezése nélkül |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A felügyelt SQL-példányok TDE-védőjét a saját kulcsával kell titkosítani |Engedélyezheti vagy letilthatja transzparens adattitkosítás (TDE) figyelését a saját kulcsának támogatásával. A TDE a saját kulcs támogatásával növelte az átláthatóságot és szabályozhatja a TDE-védőt, megnövelve a biztonságot egy HSM-alapú külső szolgáltatással, valamint a feladatok elkülönítését. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Az SQL Server TDE-védőt a saját kulccsal kell titkosítani |Engedélyezheti vagy letilthatja transzparens adattitkosítás (TDE) figyelését a saját kulcsának támogatásával. A TDE a saját kulcs támogatásával növelte az átláthatóságot és szabályozhatja a TDE-védőt, megnövelve a biztonságot egy HSM-alapú külső szolgáltatással, valamint a feladatok elkülönítését. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Storage-fiók előtagja regionális Storage-fiókhoz a hálózati biztonsági csoportok diagnosztikai beállításainak telepítéséhez |Ezt az előtagot a hálózati biztonsági csoport helyével együtt kell összekapcsolni a létrehozott Storage-fiók nevének létrehozásához. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell |A virtuálisgép-méretezési csoportok rendszerfrissítések jelentéskészítésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell |A virtuálisgép-méretezési csoportok rendszerfrissítések jelentéskészítésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Csoportos küldés névfeloldásának kikapcsolása |Megadja, hogy a LLMNR egy másodlagos névfeloldási protokoll, amely egyetlen alhálózat helyi alhálózati kapcsolatán keresztül továbbítja a csoportos küldést, engedélyezve van-e. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra |A klasszikus számítási virtuális gépek figyelésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell |Virtuálisgép-méretezési csoportok biztonsági rések figyelésének engedélyezése vagy letiltása |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni |Engedélyezheti vagy letilthatja a virtuális gépek sebezhetőségének észlelését sebezhetőségi felmérési megoldással |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain. |Olyan felügyelt SQL-példányok naplózása, amelyeknél engedélyezve van az ismétlődő sebezhetőségi felmérések ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (tartomány): helyi tűzfalszabályok alkalmazása |Megadja, hogy a helyi rendszergazdák létrehozhatnak-e helyi tűzfalszabályok, amelyek a tartományi profilhoz Csoportházirend által konfigurált tűzfalszabályok esetében érvényesek. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (tartomány): kimenő kapcsolatok viselkedése |Megadja a bejövő kapcsolatok viselkedését a kimenő tűzfalszabályok nem megfelelő tartományi profil esetében. A 0 érték alapértelmezett értéke a kapcsolatok engedélyezése, az 1 érték pedig a kapcsolatok blokkolását jelenti. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (tartomány): kimenő kapcsolatok viselkedése |Megadja a bejövő kapcsolatok viselkedését a kimenő tűzfalszabályok nem megfelelő tartományi profil esetében. A 0 érték alapértelmezett értéke a kapcsolatok engedélyezése, az 1 érték pedig a kapcsolatok blokkolását jelenti. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (tartomány): értesítések megjelenítése |Megadja, hogy a fokozott biztonságú Windows tűzfal megjelenjen-e az értesítések a felhasználónak, ha a rendszer letiltja a bejövő kapcsolatok fogadását a tartományi profilhoz. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (tartomány): profil beállításainak használata |Meghatározza, hogy a fokozott biztonságú Windows tűzfal a hálózati forgalom szűréséhez a tartományi profil beállításait használja-e. Ha a ki lehetőséget választja, a fokozott biztonságú Windows tűzfal nem fogja használni a profilhoz tartozó tűzfalszabályok vagy kapcsolatbiztonsági szabályok egyikét sem. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (privát): helyi kapcsolatbiztonsági szabályok alkalmazása |Meghatározza, hogy a helyi rendszergazdák létrehozhatnak-e olyan kapcsolatbiztonsági szabályokat, amelyek a magánhálózati profil Csoportházirend által konfigurált kapcsolatbiztonsági szabályokkal együtt alkalmazandók. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (privát): helyi tűzfalszabályok alkalmazása |Megadja, hogy a helyi rendszergazdák létrehozhatnak-e helyi tűzfalszabályok, amelyek a magánhálózati profilhoz Csoportházirend által konfigurált tűzfalszabályok esetében érvényesek. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (privát): a kimenő kapcsolatok működése |Meghatározza a magánhálózati profil kimenő kapcsolatainak viselkedését, amelyek nem felelnek meg a kimenő tűzfalszabály szabályainak. A 0 érték alapértelmezett értéke a kapcsolatok engedélyezése, az 1 érték pedig a kapcsolatok blokkolását jelenti. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (privát): értesítések megjelenítése |Megadja, hogy a fokozott biztonságú Windows tűzfal megjelenjen-e az értesítések a felhasználónak, ha egy program blokkolja a bejövő kapcsolatok fogadását a privát profilhoz. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (privát): profil beállításainak használata |Megadja, hogy a fokozott biztonságú Windows tűzfal használja-e a privát profil beállításait a hálózati forgalom szűréséhez. Ha a ki lehetőséget választja, a fokozott biztonságú Windows tűzfal nem fogja használni a profilhoz tartozó tűzfalszabályok vagy kapcsolatbiztonsági szabályok egyikét sem. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (nyilvános): helyi kapcsolatbiztonsági szabályok alkalmazása |Megadja, hogy a helyi rendszergazdák számára engedélyezett-e a nyilvános profilhoz Csoportházirend által konfigurált kapcsolatbiztonsági szabályokkal együtt alkalmazandó kapcsolatbiztonsági szabályok létrehozása. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (nyilvános): helyi tűzfalszabályok alkalmazása |Megadja, hogy a helyi rendszergazdák létrehozhatnak-e helyi tűzfalszabályok olyan szabályokat, amelyek a nyilvános profil Csoportházirend által konfigurált tűzfalszabályok esetében érvényesek. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (nyilvános): kimenő kapcsolatok viselkedése |Meghatározza a nyilvános profil kimenő kapcsolatainak viselkedését, amelyek nem felelnek meg a kimenő tűzfalszabály szabályainak. A 0 érték alapértelmezett értéke a kapcsolatok engedélyezése, az 1 érték pedig a kapcsolatok blokkolását jelenti. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (nyilvános): értesítések megjelenítése |Megadja, hogy a fokozott biztonságú Windows tűzfal megjelenjen-e az értesítések a felhasználónak, ha egy program blokkolja a bejövő kapcsolatok fogadását a nyilvános profilhoz. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal (nyilvános): profil beállításainak használata |Megadja, hogy a fokozott biztonságú Windows tűzfal a nyilvános profil beállításait használja-e a hálózati forgalom szűréséhez. Ha a ki lehetőséget választja, a fokozott biztonságú Windows tűzfal nem fogja használni a profilhoz tartozó tűzfalszabályok vagy kapcsolatbiztonsági szabályok egyikét sem. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal: tartomány: egyedi küldésű válasz engedélyezése |Megadja, hogy a fokozott biztonságú Windows tűzfal engedélyezi-e a helyi számítógép számára, hogy egyedi küldésű válaszokat kapjon a kimenő csoportos küldésű vagy szórásos üzenetekre; a tartományi profilhoz. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal: Private: egyedi küldésű válasz engedélyezése |Megadja, hogy a fokozott biztonságú Windows tűzfal engedélyezi-e a helyi számítógép számára, hogy egyedi küldésű válaszokat kapjon a kimenő csoportos küldésű vagy szórásos üzenetekre; a privát profilhoz. |
|HITRUST/HIPAA ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Windows tűzfal: nyilvános: egyedi küldésű válasz engedélyezése |Megadja, hogy a fokozott biztonságú Windows tűzfal engedélyezi-e a helyi számítógép számára, hogy egyedi küldésű válaszokat kapjon a kimenő csoportos küldésű vagy szórásos üzenetekre; a nyilvános profilhoz. |

## <a name="next-steps"></a>Következő lépések

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
