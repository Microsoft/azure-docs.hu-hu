---
title: DoD Impact Level 4 terv minta-vezérlők
description: A DoD Impact Level 4 terv minta hozzárendelésének vezérlése. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 5e022f4cea10be628d7d58c85ac10eb960d8ffa0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386975"
---
# <a name="control-mapping-of-the-dod-impact-level-4-blueprint-sample"></a>A DoD Impact Level 4 tervezetének vezérlése

A következő cikk részletesen ismerteti, hogy az Azure-tervezetek védelmi Minisztériuma 4. szintű (DoD IL4) tervezet-minta térképek a DoD Impact 4. szintű vezérlőknek. További információ a vezérlőkről: a [DoD Cloud Computing biztonsági követelményeinek útmutatója (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf). A védelmi információs rendszerek Ügynöksége (DISA) az Egyesült Államok védelmi Minisztériuma (DoD) ügynöksége, amely a DoD Cloud Computing biztonsági követelményeinek (SRG) fejlesztésére és karbantartására felelős. A SRG határozza meg a DoD-információkat,-rendszereket és-alkalmazásokat üzemeltető felhőalapú szolgáltatók (CSP-k) alapszintű biztonsági követelményeit, valamint a DoD Cloud Services általi használatát.  

A következő leképezések a **DoD Impact 4** -es vezérlőre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki az **\[ előzetes verziót \] : DoD Impact Level 4** beépített házirend kezdeményezés.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-4/control-mapping.md).

## <a name="ac-2-account-management"></a>AC-2 fiókok kezelése

Ez a terv segít áttekinteni azokat a fiókokat, amelyek esetleg nem felelnek meg a szervezete fiókjának felügyeleti követelményeinek. Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek a külső fiókokat az előfizetések és az elavult fiókok olvasási, írási és tulajdonosi engedélyeivel naplózzák. A szabályzatok által auditált fiókok áttekintésével megteheti a megfelelő lépéseket a fiókok kezelésével kapcsolatos követelmények teljesítése érdekében.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Fiókkezelés | Role-Based sémák

Az Azure [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../../../role-based-access-control/overview.md) valósít meg, amellyel kezelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat is HOZZÁRENDEL az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését. A terv emellett egy Azure Policy definíciót rendel hozzá az egyéni Azure RBAC-szabályok használatának naplózásához. Az egyéni Azure RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni Azure RBAC-szabályok hibásak.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Egyéni RBAC-szabályok használatának naplózása
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) fiókok kezelése | Fiókok figyelése/atipikus használat

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A virtuális gépek eléréséhez szükséges JIT-kérelmeket a rendszer naplózza a tevékenység naplójában, amely lehetővé teszi az atipikus használat figyelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="ac-4-information-flow-enforcement"></a>AC-4 információáramlás kényszerítése

A több eredetű erőforrás-megosztás (CORS) lehetővé teszi App Services-erőforrások kérését egy külső tartományból. A Microsoft azt javasolja, hogy csak a szükséges tartományokat engedélyezze az API-val, a funkcióval és a webalkalmazásokkal való interakcióhoz. Ez a terv egy [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amely segít a CORS-erőforrások hozzáférési korlátozásának figyelésében Azure Security Centerban. A CORS-implementációk ismertetése segít ellenőrizni, hogy az információáramlási vezérlők implementálva vannak-e.

- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazáshoz

## <a name="ac-5-separation-of-duties"></a>A feladatok AC-5 elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a tervezet segítséget nyújt a megfelelő számú Azure-előfizetési tulajdonos fenntartásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. A terv emellett Azure Policy definíciókat is hozzárendel, amelyek segítenek a rendszergazdák csoport tagságának vezérlésében a Windows rendszerű virtuális gépeken. Az előfizetés tulajdonosa és a virtuális gép rendszergazdai engedélyeinek kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) legalacsonyabb jogosultság | Felhasználói jogosultságok áttekintése

Az Azure [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../../../role-based-access-control/overview.md) valósít meg, amellyel kezelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a naplózási fiókokhoz, amelyeket érdemes áttekinteni. A fiókokra vonatkozó mutatók áttekintésével meggyőződhet arról, hogy a minimális jogosultsági szintű vezérlők implementálva vannak.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) távoli hozzáférés | Automatikus figyelés/vezérlés

Ez a terv segít a távelérés figyelésében és vezérlésében azáltal, hogy [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével figyeli, hogy a Azure app Service alkalmazás távoli hibakeresése ki van-e kapcsolva, valamint olyan házirend-definíciókat, amelyek olyan Linux virtuális gépeket naplóznak, amelyek lehetővé teszik a távoli kapcsolódást Ez a terv egy Azure Policy-definíciót is hozzárendel, amely segít megfigyelni a nem korlátozott hozzáférést a Storage-fiókokhoz. Ezen mutatók monitorozásával biztosítható, hogy a távelérési módszerek megfeleljenek a biztonsági szabályzatnak.

- \[Előzetes verzió \] : jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózása
- \[Előzetes verzió \] : követelmények központi telepítése a jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózásához
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni függvényalkalmazás
- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz

## <a name="ac-23-data-mining"></a>AC-23 adatbányászat

Ez a terv olyan szabályzat-definíciókat tartalmaz, amelyek segítségével biztosítható, hogy az adatbiztonsági értesítések megfelelően engedélyezve legyenek. A terv emellett biztosítja, hogy a naplózás és a speciális adatok biztonsága az SQL-kiszolgálókon legyen konfigurálva.

- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- A speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál
- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server
- A rendszergazdák és az előfizetések tulajdonosainak szóló e-mail-értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban
- A rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításaiban.
- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- Az SQL által felügyelt példány speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Az AU-3 (2) naplózási rekordok tartalma | Tervezett auditálási rekordok központosított kezelése

A Azure Monitor által gyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi konfigurációt és a felügyeletet. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- \[Előzetes verzió \] : log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- A Linux rendszerű virtuálisgép-méretezési csoportokra vonatkozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- A Windows rendszerű virtuálisgép-méretezési csoportokhoz tartozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Válasz a feldolgozási hibák naplózására

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek figyelik a naplózási és eseménynaplózási konfigurációkat. Ezeknek a konfigurációknak a figyelése a rendszerhiba vagy a helytelen konfiguráció jelzését, valamint a javítási műveletek elvégzését is lehetővé teszi.

- Diagnosztikai beállítás naplózása
- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) audit felülvizsgálat, elemzés és jelentéskészítés | Központi áttekintés és elemzés

A Azure Monitor által összegyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi jelentéskészítést és elemzést. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- \[Előzetes verzió \] : log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- A Linux rendszerű virtuálisgép-méretezési csoportokra vonatkozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- A Windows rendszerű virtuálisgép-méretezési csoportokhoz tartozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) audit felülvizsgálat, elemzés és jelentéskészítés | Integrációs/ellenőrzési és figyelési képességek

Ez a terv olyan szabályzat-definíciókat biztosít, amelyek a virtuális gépeken, a virtuálisgép-méretezési csoportokon, a SQL Database-kiszolgálókon és az SQL felügyelt példány-kiszolgálókon végzett sebezhetőségi felmérés elemzésével Ezek a szabályzat-definíciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekre. Ezek az információk valós idejű információkat biztosítanak az üzembe helyezett erőforrások biztonsági állapotáról, és segíthetnek a Szervizelési műveletek rangsorolásában. A biztonsági rések vizsgálatának és figyelésének részletes kihasználása érdekében javasoljuk az Azure Sentinel és a Azure Security Center használatát is.

- \[Előzetes verzió \] : a sebezhetőségi felmérést engedélyezni kell Virtual machines
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- Diagnosztikai beállítás naplózása
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)

## <a name="au-12-audit-generation"></a>AU-12 naplózási generáció

Ez a terv olyan szabályzat-definíciókat biztosít, amelyek a Log Analytics ügynök üzembe helyezését az Azure Virtual Machines szolgáltatásban, valamint az egyéb Azure-erőforrástípusok naplózási beállításainak konfigurálását is lehetővé teszi.
Ezek a szabályzat-definíciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekre. Emellett a naplózás és a speciális adatbiztonság is konfigurálva van az SQL-kiszolgálókon.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- \[Előzetes verzió \] : log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- A Linux rendszerű virtuálisgép-méretezési csoportokra vonatkozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- A Windows rendszerű virtuálisgép-méretezési csoportokhoz tartozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken
- Diagnosztikai beállítás naplózása
- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) naplózási generáció | System-Wide/Time-Correlated naplózási nyomvonal

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével.
Ehhez a beépített szabályzathoz az erőforrástípusok tömbjét kell megadnia annak vizsgálatához, hogy engedélyezve vannak-e a diagnosztikai beállítások.

- Diagnosztikai beállítás naplózása

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) a legkevésbé szükséges funkciók | Program végrehajtásának megakadályozása

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési lista megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem kényszerítési módban futtatható, amely tiltja a nem jóváhagyott alkalmazások futtatását. Ez a tervrajz olyan Azure Policy-definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, ahol az alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva.

- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) a legkevésbé szükséges funkciók | Engedélyezett szoftver/engedélyezési lista

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési lista megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem lehetővé teszi a jóváhagyott alkalmazások listáját a virtuális gépekhez. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, ahol az alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva.

- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken

## <a name="cm-11-user-installed-software"></a>CM – 11 User-Installed szoftver

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési lista megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem segítségével érvényesítheti és figyelheti a szoftverkorlátozó házirendek megfelelőségét. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, ahol az alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva.

- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatív feldolgozási hely

Azure Site Recovery replikálja a virtuális gépeken futó munkaterheléseket egy elsődleges helyről a másodlagos helyre. Ha áramkimaradás fordul elő az elsődleges helyen, a munkaterhelés feladatátvétele a másodlagos helyen történik. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely a virtuális gépeket a vész-helyreállítási beállítások nélkül naplózza. A kijelző figyelése segít biztosítani a szükséges készenléti ellenőrzéseket.

- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) információs rendszerek biztonsági mentése | Átvitel másik tárolóhelyre

Ez a terv olyan Azure Policy-definíciókat rendel hozzá, amelyek a szervezet rendszerbiztonsági mentési információit elektronikus úton naplózzák a másodlagos tárolóhelyre. A tárolási metaadatok fizikai szállítása esetén érdemes Azure Data Box használni.

- A Geo-redundáns tárterületet engedélyezni kell a Storage-fiókokhoz
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB
- A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés a Kiemelt jogosultságú fiókokhoz

Ez a terv segít a privilegizált hozzáférés korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével olyan fiókokat naplózni, amelyek tulajdonosi és/vagy írási engedélyekkel nem rendelkeznek a többtényezős hitelesítés engedélyezésével. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedélyekkel rendelkező fiókjaiban

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) azonosítás és hitelesítés (szervezeti felhasználók) | Nem Kiemelt fiókok hálózati hozzáférése

Ez a terv segítséget nyújt a hozzáférés korlátozásához és vezérléséhez, ha egy [Azure Policy](../../../policy/overview.md) definíció hozzárendelésével naplózza a fiókokat olyan olvasási engedélyekkel, amelyeken nincs engedélyezve a többtényezős hitelesítés. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

## <a name="ia-5-authenticator-management"></a>IA-5 hitelesítő kezelése

Ez a terv azokat a [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek a Linux rendszerű virtuális gépeket a jelszavak nélküli fiókok távoli kapcsolatainak engedélyezésére és/vagy helytelen engedélyekkel rendelkeznek a passwd fájlban. Ez a terv a Windows rendszerű virtuális gépekhez tartozó jelszó-titkosítási típus konfigurációját naplózó szabályzat-definíciókat is hozzárendeli. Ezen mutatók monitorozásával biztosíthatja, hogy a rendszerhitelesítő megfeleljenek a szervezete azonosítási és hitelesítési házirendjének.

- \[Előzetes verzió \] : olyan linuxos virtuális gépek naplózása, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- \[Előzetes verzió \] : jelszavak nélküli fiókkal rendelkező linuxos virtuális gépek naplózása
- \[Előzetes \] verzió: a jelszavakat nem tároló Windows rendszerű virtuális gépek naplózása visszafejthető titkosítással
- \[Előzetes verzió \] : követelmények telepítése olyan linuxos virtuális gépek naplózására, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- \[Előzetes verzió \] : követelmények telepítése a jelszavak nélküli fiókkal rendelkező linuxos virtuális gépek naplózására
- \[Előzetes \] verzió: követelmények telepítése a jelszavakat nem tároló Windows-alapú virtuális gépek naplózásához visszafejthető titkosítással

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) hitelesítő kezelése | Password-Based hitelesítés

Ez a terv segítséget nyújt az erős jelszavak betartatásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem igénylő Windows rendszerű virtuális gépeket naplózzák. A virtuális gépeknek a jelszó erőssége megsértésével kapcsolatos ismerete segít megtenni a szükséges javítási műveleteket, hogy az összes virtuálisgép-felhasználói fiók jelszava megfeleljen a szervezete jelszavas házirendjének.

- \[Előzetes \] verzió: az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózása
- \[Előzetes \] verzió: a maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózása 70 nap
- \[Előzetes \] verzió: olyan Windows rendszerű virtuális gépek naplózása, amelyek nem rendelkeznek minimális jelszóval (1 nap)
- \[Előzetes verzió \] : engedélyezve van a jelszó-bonyolultsági beállítással nem rendelkező Windows rendszerű virtuális gépek naplózása
- \[Előzetes verzió \] : a jelszó minimális hosszát 14 karakternél nem korlátozó Windows-alapú virtuális gépek naplózása
- \[Előzetes \] verzió: a jelszavakat nem tároló Windows rendszerű virtuális gépek naplózása visszafejthető titkosítással
- \[Előzetes \] verzió: követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát
- \[Előzetes \] verzió: követelmények központi telepítése Windows rendszerű virtuális gépek naplózásához, amelyek nem rendelkeznek maximális jelszóval (70 nap)
- \[Előzetes \] verzió: követelmények telepítése a Windows rendszerű virtuális gépek naplózásához, amelyek nem rendelkeznek legalább 1 napos jelszóval
- \[Előzetes verzió \] : követelmények központi telepítése a jelszó-bonyolultsági beállítással nem rendelkező Windows rendszerű virtuális gépek naplózásához
- \[Előzetes verzió \] : követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes \] verzió: követelmények telepítése a jelszavakat nem tároló Windows-alapú virtuális gépek naplózásához visszafejthető titkosítással

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) incidens jelentése | Incidensekkel kapcsolatos sebezhetőségek

Ez a terv olyan szabályzat-definíciókat tartalmaz, amelyek a virtuális gépek, a virtuálisgép-méretezési csoportok és az SQL-kiszolgálók sebezhetőségi felmérésének elemzésével naplózzák a rekordokat. Ezek az információk valós idejű információkat biztosítanak az üzembe helyezett erőforrások biztonsági állapotáról, és segíthetnek a Szervizelési műveletek rangsorolásában.

- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell

## <a name="ra-5-vulnerability-scanning"></a>RA-5 sebezhetőségi vizsgálat

Ez a terv segítséget nyújt az információs rendszer biztonsági réseinak kezeléséhez olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek figyelik az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek Azure Security Center- A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. A terv emellett olyan szabályzat-definíciókat is hozzárendel, amelyek az SQL-kiszolgálókon a speciális adatbiztonságot auditálják és érvényesítik. A speciális adatbiztonság biztonsági rések felmérése és a komplex veszélyforrások elleni védelem lehetővé teszi, hogy jobban megértse a telepített erőforrások sebezhetőségeit.

- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A virtuális gépek biztonsági konfigurációjában lévő biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="sc-5-denial-of-service-protection"></a>SC-5 szolgáltatásmegtagadás-védelem

Az Azure Distributed szolgáltatásmegtagadás (DDoS) standard csomagja további funkciókat és kockázatcsökkentő képességeket biztosít az alapszintű szolgáltatási szinten. Ezek a további funkciók Azure Monitor integrációt és a támadás utáni kockázatcsökkentő jelentések áttekintését is lehetővé teszi. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely azt naplózza, hogy a DDoS standard szint engedélyezve van-e. A szolgáltatási rétegek közötti képességbeli különbség megismerése segíthet kiválasztani a legjobb megoldást az Azure-környezethez tartozó szolgáltatások megtagadásának kezeléséhez.

- DDoS Protection a standardot engedélyezni kell

## <a name="sc-7-boundary-protection"></a>SC-7 határ védelme

Ez a terv segít a Rendszerhatár kezelésében és szabályozásában azáltal, hogy hozzárendel egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a hálózati biztonsági csoportra vonatkozó ajánlásokat a Azure Security Centerban. Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a potenciális támadási felület csökkentése érdekében.
Emellett a terv a nem védett végpontokat, alkalmazásokat és tárolási fiókokat figyelő szabályzat-definíciókat is hozzárendeli. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- A IaaS lévő webalkalmazások NSG-szabályait meg kell erősíteni
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) határ védelme | Hozzáférési pontok

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gép hozzáférése segít korlátozni az Azure-ban lévő erőforrások külső kapcsolatainak számát. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) határ védelme | Külső távközlési szolgáltatások

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gépekhez való hozzáférés a hozzáférési kérelmek és a jóváhagyási folyamatok megkönnyítésével segíti a forgalomra vonatkozó kivételek kezelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) a továbbítás titkossága és integritása | Titkosítási vagy alternatív fizikai védelem

Ez a terv segít a továbbított adatok bizalmas és integritásának védelmében [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, amelyek segítségével figyelheti a kommunikációs protokollok által megvalósított titkosítási mechanizmusokat. A kommunikáció megfelelő titkosítása segíthet a szervezet követelményeinek teljesítésében, illetve az információk jogosulatlan közzétételtől és módosítástól való védelmében.

- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- A biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózása
- Követelmények telepítése a biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózásához
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A webalkalmazás csak HTTPS protokollon keresztül érhető el

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) a REST-adatok védelme | Titkosítási védelem

Ebből a tervből megtudhatja, hogyan kényszerítheti ki a szabályzatot a titkosítási vezérlők használatára, hogy olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendeljen hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzat-definíciók titkosítást igényelnek a Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; és naplózza a hiányzó titkosítást az SQL-adatbázisokon, a virtuális gépek lemezein és az Automation-fiók változóinak.

- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Az SQL DB transzparens adattitkosításának üzembe helyezése
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Titkosítás megkövetelése Data Lake Store fiókokon
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="si-2-flaw-remediation"></a>SI-2 hibák szervizelése

Ebből a tervből megtudhatja, hogyan kezelheti a rendszerhibákat a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL-biztonsági réseket és a virtuális gépek Azure Security Center-beli biztonsági réseit figyelő [Azure Policy](../../../policy/overview.md) definíciók A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. Ez a terv egy szabályzat-definíciót is hozzárendel, amely biztosítja a virtuálisgép-méretezési csoportok operációs rendszerének javítását.

- Az operációsrendszer-rendszerkép automatikus javításának megkövetelése Virtual Machine Scale Sets
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell
- A rendszerfrissítéseket telepíteni kell a virtuális gépekre
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A virtuális gépek biztonsági konfigurációjában lévő biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>SI-02 (06) hibák elhárítása | A szoftver/belső vezérlőprogram korábbi verzióinak eltávolítása

Ez a terv olyan szabályzat-definíciókat rendel hozzá, amelyek segítségével biztosítható, hogy az alkalmazások a HTTP, a Java, a PHP, a Python és a TLS legújabb verzióját használják. Ez a terv egy olyan szabályzat-definíciót is hozzárendel, amely biztosítja, hogy a Kubernetes-szolgáltatások a nem sebezhető verzióra legyen frissítve.

- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használatos
- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a Function app futtatásához használatos
- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatására használatos
- Győződjön meg arról, hogy a Java-verzió a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a Function alkalmazás részeként van használatban
- Győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás részeként van használatban
- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Function alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként van használatban
- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS-verziót kell használni a függvényalkalmazás
- A legújabb TLS-verziót kell használni a webalkalmazásban
- A Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni

## <a name="si-3-malicious-code-protection"></a>SI-3 kártékony kódok védelme

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek figyelik a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását, és kikényszerítik a Microsoft antimalware megoldást a Windows rendszerű virtuális gépeken.

- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) kártékony kódok védelme | Központi felügyelet

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását figyelik. A Azure Security Center központosított felügyeleti és jelentéskészítési képességeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést kapjon az üzembe helyezett Azure-erőforrások biztonsági állapotával.

- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 információs rendszer figyelése

Ez a terv a naplózás és az adatbiztonság Azure-erőforrásokon keresztüli naplózásával és érvényesítésével segíti a rendszerek figyelését. A szabályzatok a Log Analytics ügynök üzembe helyezését, valamint az SQL-adatbázisok, a Storage-fiókok és a hálózati erőforrások fokozott biztonsági beállításait naplózzák és érvényesítik. Ezek a képességek segítenek a rendellenes viselkedés és a támadási mutatók észlelésében, így elvégezheti a megfelelő lépéseket.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- \[Előzetes verzió \] : log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- A Linux rendszerű virtuálisgép-méretezési csoportokra vonatkozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- A Windows rendszerű virtuálisgép-méretezési csoportokhoz tartozó Log Analytics-ügynök üzembe helyezése
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon
- Engedélyezett helyek
- Erőforráscsoportok engedélyezett helyei

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) információs rendszer figyelése | Automatizált riasztások

Ez a terv olyan szabályzat-definíciókat tartalmaz, amelyek segítségével biztosítható, hogy az adatbiztonsági értesítések megfelelően engedélyezve legyenek. A terv emellett biztosítja, hogy a standard díjszabási szint engedélyezve legyen Azure Security Center számára. Vegye figyelembe, hogy a standard szintű díjszabás lehetővé teszi a fenyegetések észlelését a hálózatokon és a virtuális gépeken, a fenyegetések felderítését, a rendellenességek észlelését és a viselkedés-elemzést Azure Security Centerban.

- Engedélyezni kell az e-mailes értesítést a nagy súlyosságú riasztások előfizetésének tulajdonosának
- Az előfizetéshez meg kell adni egy biztonsági kapcsolattartási e-mail címet 
- A rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításaiban. 
- A rendszergazdák és az előfizetések tulajdonosainak szóló e-mail-értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban 
- Az előfizetéshez meg kell adni egy biztonsági kapcsolatfelvételi telefonszámot
- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- Security Center Standard díjszabási szintet kell kiválasztani

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) információs rendszer figyelése | Traffic/Covert kiszűrése elemzése

Az Azure Storage komplex veszélyforrások elleni védelme szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A védelmi riasztások közé tartoznak a rendellenes hozzáférési minták, a rendellenes kibontások/feltöltések és a gyanús tárolási tevékenységek. Ezek a mutatók segítséget nyújtanak a további kiszűrése észlelésében.

- Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat. 

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a DoD Impact Level 4 tervezetének vezérlési leképezését, a következő cikkekben megismerheti a terv ismertetését és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [DoD Impact Level 4 tervezet – áttekintés](./index.md) 
>  [DoD Impact Level 4 tervezet – a lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.