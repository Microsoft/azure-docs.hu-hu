---
title: Security Center tervezési és üzemeltetési útmutató
description: Ez a dokumentum segít az Azure Security Center bevezetése előtti tervezésben, valamint megismerteti a napi műveletekhez tartozó szempontokkal.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: a8b285ca0b8d0dae88bfe8dd19ac7e1bc8372e32
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099199"
---
# <a name="planning-and-operations-guide"></a>Tervezési és működtetési útmutató
Ez az útmutató olyan informatikai (IT) szakemberek, informatikai építészek, Információbiztonsági elemzők és felhőalapú rendszergazdák számára készült, akik a Azure Security Center használatát tervezik.


## <a name="planning-guide"></a>Tervezési útmutató
Ez az útmutató azokat a feladatokat ismerteti, amelyeket követve optimalizálhatja Security Center használatát a szervezet biztonsági követelményei és a felhőalapú felügyeleti modell alapján. Ahhoz, hogy a lehető legnagyobb mértékben kihasználhassa a Security Center által nyújtott előnyöket, fontos tisztában lennie azzal, hogy a vállalat különböző osztályai és dolgozói hogyan használják a szolgáltatást, mivel így könnyebben teljesítheti a biztonságos fejlesztésre, működésre, ellenőrzésre, irányításra és incidensmegoldásra vonatkozó követelményeket. A Security Center használatának tervezésekor fordítson különös figyelmet a következő területekre:

* Biztonsági szerepkörök és hozzáférés-vezérlés
* Biztonsági szabályzatok és javaslatok
* Adatgyűjtés és -tárolás
* Nem Azure-erőforrások előkészítése
* A biztonság folyamatos ellenőrzése
* Incidensmegoldás

A következő részekben bemutatjuk, hogyan alkothatja meg a fenti területekre vonatkozó tervet, és hogyan alkalmazhatja a javasolt lépéseket saját környezetének megfelelően.


> [!NOTE]
> Az [Azure Security Center frequently asked questions (FAQ)](faq-general.md) (Az Azure Security Centerhez kapcsolódó gyakori kérdések) témakörben számos általánosan előforduló kérdést és választ talál, amelyek szintén hasznosak lehetnek a tervezési fázisban.

## <a name="security-roles-and-access-controls"></a>Biztonsági szerepkörök és hozzáférés-vezérlés
A vállalat méretétől és felépítésétől függően előfordulhat, hogy sok ember és csoport is használni fogja a Security Centert a különböző biztonsági feladatok elvégzésére. A következő ábrán néhány képzeletbeli személyt mutatunk be, és ismertetjük a szerepköreiket és biztonsági feladataikat:

![Szerepkörök](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Ezek a személyek a Security Center segítségével teljesítik feladataikat. Például:

**Bálint (számítási feladatok felelőse)**

* A felhő munkaterhelését és kapcsolódó erőforrásait kezeli.
* Feladata a védelmi megoldások megvalósítása és kezelése a vállalat biztonsági szabályzatának megfelelően.

**Eszter (adatvédelmi felelős/számítástechnikai felelős)**

* A vállalat általános biztonságáért felelős, ennek szempontjaival foglalkozik.
* Ismernie kell a vállalat biztonsági irányelveit a felhőbeli munkaterhelésekben.
* Tájékoztatást kell kapnia a komolyabb támadásokról és kockázatokról.

**András (számítástechnikai biztonsági felelős)**

* Kidolgozza a vállalat biztonsági szabályzatát a megfelelő védelmi megoldások alkalmazása érdekében.
* Ellenőrzi a szabályzatok betartását.
* Jelentéseket készít a vezetőség és az auditorok számára.

**Judit (biztonsági műveletek felelőse)**

* Figyeli a biztonsági riasztásokat a hét 7 napján, 24 órában, és megfelelően reagál azokra.
* Továbbítja a problémákat a felhőbeli munkaterhelés tulajdonosának vagy a számítástechnikai biztonsági elemzőnek.

**Sándor (biztonsági elemző)**

* Kivizsgálja a támadásokat.
* Együttműködés a felhőbeli munkaterhelés tulajdonosával a problémák megoldásához

Security Center az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)használja, amely [beépített szerepköröket](../role-based-access-control/built-in-roles.md) biztosít a felhasználókhoz, csoportokhoz és szolgáltatásokhoz az Azure-ban. A Security Center megnyitásakor a felhasználók csak azon erőforrások információit látják, amelyekhez hozzáféréssel rendelkeznek. Ez azt jelenti, hogy a felhasználó az előfizetés vagy az erőforrást tartalmazó erőforráscsoport tulajdonosi, közreműködői vagy olvasói szerepköréhez van hozzárendelve. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- **Biztonsági olvasó**: az ehhez a szerepkörhöz tartozó felhasználó csak a Security Center-konfigurációkat tekintheti meg, amelyekbe beletartoznak a javaslatok, riasztások, szabályzatok és az állapot, de módosításokat nem hajthat végre.
- **Biztonsági rendszergazda**: ugyanazokkal a jogosultságokkal rendelkezik mint a biztonsági olvasó, de frissítheti a biztonsági szabályzatot, valamint javaslatokat és riasztásokat utasíthat el.

A fent bemutatott Security Center-szerepkörök nem rendelkeznek hozzáféréssel az Azure egyéb szolgáltatási területeihez, például a Storage-hez, a Web & Mobile-hoz vagy az Eszközök internetes hálózatához.

Az előző ábrán leírt personák használatával a következő Azure-RBAC lenne szükséges:

**Bálint (számítási feladatok felelőse)**

* Erőforráscsoport tulajdonosa/közreműködői

**Eszter (adatvédelmi felelős/számítástechnikai felelős)**

* Előfizetés-tulajdonos/közreműködő vagy biztonsági rendszergazda

**András (számítástechnikai biztonsági felelős)**

* Előfizetés-tulajdonos/közreműködő vagy biztonsági rendszergazda

**Judit (biztonsági műveletek felelőse)**

* Előfizetés: olvasó vagy biztonsági rendszergazda a riasztások megtekintéséhez
* A riasztások elvetéséhez szükséges előfizetés-tulajdonos/közreműködő vagy biztonsági rendszergazda

**Sándor (biztonsági elemző)**

* Előfizetés: olvasó a riasztások megtekintéséhez
* A riasztások elvetéséhez szükséges előfizetés-tulajdonos/közreműködő
* Előfordulhat, hogy hozzáférés szükséges a munkaterülethez

Egyéb megfontolandó szempontok:

* A biztonsági szabályzatot kizárólag olyanok módosíthatják, akik az előfizetésnél Tulajdonos/Közreműködő vagy Biztonsági rendszergazda szerepkörrel rendelkeznek.
* Kizárólag az előfizetésnél és az erőforráscsoportnál Tulajdonos és Közreműködő szerepkörrel rendelkezők alkalmazhatják a biztonsági javaslatokat az erőforrásra.

A hozzáférés-vezérlés Security Center Azure RBAC való megtervezése során érdemes megismerni, hogy a szervezeten belül ki fogja használni a Security Center. Azt is megteheti, hogy milyen típusú feladatokat kell végrehajtania, majd ennek megfelelően konfigurálja az Azure RBAC-t.

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Érdemes például az Olvasó szerepkört rendelni azokhoz a felhasználókhoz, akik csupán megtekintik az erőforrások biztonsági állapotát, de nem tesznek lépéseket azzal kapcsolatban (tehát nem alkalmazhatnak javaslatokat, és nem módosíthatják a szabályzatokat).
>
>

## <a name="security-policies-and-recommendations"></a>Biztonsági szabályzatok és javaslatok
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. A Security Centerben a számítási feladatok típusának vagy az adatok bizalmasságának megfelelően határozhatja meg az Azure-előfizetésekre vonatkozó szabályzatokat.

A Security Center-szabályzatok a következő összetevőkből állnak:
- [Adatgyűjtés](security-center-enable-data-collection.md): ügynökkiépítési és adatgyűjtési beállítások.
- [Biztonsági házirend](tutorial-security-policy.md): egy [Azure Policy](../governance/policy/overview.md) , amely meghatározza, hogy mely vezérlőket figyeli és javasolja a Security Center, vagy a Azure Policy használatával új definíciókat hozhat létre, további házirendeket határozhat meg, és szabályzatokat rendelhet hozzá a felügyeleti csoportokhoz.
- [E-mail-értesítések](security-center-provide-security-contact-details.md): biztonsági felelősök kapcsolati adatai és értesítési beállításai.
- [Díjszabási](security-center-pricing.md)csomag: az Azure Defender szolgáltatással vagy anélkül meghatározható, hogy mely Security Center szolgáltatások érhetők el a hatókörben lévő erőforrásokhoz (előfizetésekhez és munkaterületekhez, illetve az API-t használó erőforráscsoportokhoz is megadható).

> [!NOTE]
> Biztonsági kapcsolattartó kinevezésével biztosítható, hogy az Azure a biztonsági incidensek esetén elérje a megfelelő illetékes személyt. Olvassa el a [Provide security contact details in Azure Security Center](security-center-provide-security-contact-details.md) (A biztonsági kapcsolattartó adatainak megadása az Azure Security Centerben) részt, amelyből megtudhatja, hogyan engedélyezheti ezt a javaslatot.

### <a name="security-policies-definitions-and-recommendations"></a>Biztonsági szabályzatok definíciói és javaslatok
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A szabályzatot a Security Centerben szerkesztheti, vagy az Azure Policy használatával létrehozhat új meghatározásokat, meghatározhat további szabályzatokat, valamint hozzárendelhet szabályzatokat a Felügyeleti csoportokhoz (ezek jelenthetik a teljes céget vagy szervezetet, egy üzleti egységet stb.), és monitorozhatja az adott szabályzatokra vonatkozó megfelelőséget ezeken a hatókörökön belül.

A biztonsági szabályzatok konfigurálása előtt mindig olvassa el a [biztonsági javaslatokat](security-center-recommendations.md), és döntse el, hogy ezek a szabályzatok megfelelőek-e az Ön által használt előfizetésekhez és erőforráscsoportokhoz. Ezenkívül fontos, hogy tisztában legyen azzal, milyen lépéseket kell tennie a biztonsági javaslatok kezelése érdekében, és a szervezeténél ki az új javaslatok monitorozásának és a szükséges lépések megtételének a felelőse.

## <a name="data-collection-and-storage"></a>Adatgyűjtés és -tárolás
A Azure Security Center a Log Analytics ügynököt használja – ez ugyanaz az ügynök, amelyet a Azure Monitor szolgáltatás használ – a biztonsági adatok gyűjtésére a virtuális gépekről. Az ebből az ügynökből [gyűjtött adatokat](security-center-enable-data-collection.md) a rendszer a Log Analytics-munkaterület(ek)en tárolja.

### <a name="agent"></a>Ügynök

Ha a biztonsági házirendben engedélyezve van az automatikus kiépítés, a Log Analytics ügynök ( [Windows](../azure-monitor/agents/agent-windows.md) vagy [Linux](../azure-monitor/vm/quick-collect-linux-computer.md)rendszerhez) az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra van telepítve. Ha a virtuális gépen vagy számítógépen már telepítve van a Log Analytics ügynök, Azure Security Center a jelenleg telepített ügynököt fogja használni. Az ügynök folyamata nem invazív, és nagyon csekély hatással van a virtuális gépek teljesítményére.

A Windows Log Analytics ügynökének a 443-es TCP-portot kell használnia. További részleteket talál a [Hibaelhárításról szóló cikkben](security-center-troubleshooting-guide.md).

Ha bármikor ki szeretné kapcsolni az adatgyűjtést, ezt a biztonsági szabályzatban teheti meg. Mivel azonban a Log Analytics-ügynököt más Azure felügyeleti és figyelési szolgáltatások is használhatják, az ügynök nem lesz automatikusan eltávolítva, amikor kikapcsolja az adatgyűjtés Security Center. Ha szükséges, manuálisan távolíthatja el az ügynököt.

> [!NOTE]
> A támogatott virtuális gépek listáját az [Az Azure Security Centerhez kapcsolódó gyakori kérdések (GYIK)](faq-vms.md) című témakörben találja meg.

### <a name="workspace"></a>Munkaterület

A munkaterület egy adattárolóként szolgáló Azure-erőforrás. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

A Log Analytics ügynöktől (Azure Security Center) gyűjtött adatokat az Azure-előfizetéshez társított meglévő Log Analytics munkaterület vagy egy új munkaterület (ok) tárolja, a virtuális gép földrajzi helyének figyelembevételével.

Az Azure Portalon megkeresheti a Log Analytics munkaterületeinek listáját, beleértve azokat is, amelyeket az Azure Security Center hozott létre. Egy kapcsolódó erőforráscsoport jön létre az új munkaterületek számára. Mindkettő ezt az elnevezési konvenciót követi:

* Munkaterület: *DefaultWorkspace-[subscription-ID]-[geo]*
* Erőforráscsoport: *DefaultResourceGroup-[geo]*

Az Azure Security Center által létrehozott munkaterületek adatait 30 napig őrzi meg a rendszer. A meglévő munkaterületeknél a megőrzési idő a munkaterület tarifacsomagjától függ. Ha szeretné, használhat egy létező munkaterületet is.

> [!NOTE]
> A Microsoft erős kötelezettségvállalásokat tesz az adatok védelmének és biztonságának védelme érdekében. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig. Az adatkezeléssel és adatbiztonsággal kapcsolatos további információkért olvassa el az [Azure Security Center adatbiztonság](security-center-data-security.md) című cikket.
>

## <a name="onboard-non-azure-resources"></a>Nem Azure-erőforrások előkészítése

A Security Center a nem Azure-számítógépek biztonsági állapotát is képes monitorozni, azonban ehhez elő kell készítenie ezeket az erőforrásokat. A nem Azure-beli erőforrások bevezetésével kapcsolatos további információkért olvassa el a [nem Azure-beli számítógépek](quickstart-onboard-machines.md) bevezetését ismertető témakört.

## <a name="ongoing-security-monitoring"></a>A biztonság folyamatos ellenőrzése
A kezdeti konfigurációt, illetve a Security Center javaslatainak alkalmazását követően a következő lépés a Security Center működési folyamatainak megtervezése.

A Security Center Áttekintés felülete egységesített képet nyújt Azure-erőforrásai és csatlakoztatott nem Azure-erőforrásai biztonsági állapotáról. Az alábbi példa egy számos elhárítandó problémával rendelkező környezetet ábrázol:

![irányítópult](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> A Security Center nem akadályozza Önt a megszokott eljárások elvégzésében, csupán passzívan ellenőrzi az üzemelő példányokat, és javaslatokat biztosít a beállított biztonsági szabályzatok alapján.

Amikor először bekapcsolja Security Center használatát a jelenlegi Azure-környezethez, ellenőrizze, hogy áttekinti-e az összes javaslatot, amely a **javaslatok** oldalon végezhető el.

Építse be a fenyegetésfelderítési szolgáltatás használatát napi biztonsági rutinjába. Itt azonosíthatja a környezetre leselkedő biztonsági fenyegetéseket, például megállapíthatja, ha egy adott számítógép egy botnet része.

### <a name="monitoring-for-new-or-changed-resources"></a>Új vagy módosult erőforrások keresése

A legtöbb Azure-környezet dinamikus, és az erőforrások létrehozása, megpördült vagy leskálázása, újrakonfigurálása és módosítása történik. A Security Center segítségével könnyen átláthatja az új erőforrások biztonsági állapotát.

A Security Center automatikusan felfedezi az Azure-környezethez adott új erőforrásokat (virtuális gépeket, SQL-adatbázisokat stb.), és elkezdi a biztonsági állapotuk figyelését. Ide tartoznak a PaaS webes és feldolgozói szerepkörei is. Ha a [biztonsági házirendben](tutorial-security-policy.md)az adatgyűjtés engedélyezve van, a virtuális gépekre vonatkozó további figyelési funkciók automatikusan engedélyezve lesznek.

Emellett rendszeresen figyelnie kell a meglévő erőforrásokat azokra a konfigurációs változásokra vonatkozóan, amelyek biztonsági kockázatokat hoztak létre, az ajánlott alaptervek és a biztonsági riasztások miatt. 

### <a name="hardening-access-and-applications"></a>A hozzáférés megnehezítése és az alkalmazások védelmének megerősítése

A biztonsági folyamatai részeként érdemes bevezetnie megelőző intézkedéseket a virtuális gépek hozzáférésének megakadályozása és a rajtuk futó alkalmazások szabályozása érdekében. Az Azure-beli virtuális gépek bemenő forgalmának zárolásával kevésbé fogják veszélyeztetni a támadások, ugyanakkor könnyű hozzáférést biztosít arra az esetre, amikor csatlakozni kell a virtuális gépekhez. A [virtuális](security-center-just-in-time.md) gépek igény szerinti elérésének megkeményedéséhez használja az igény szerinti virtuálisgép-hozzáférési funkciót.

Az [adaptív alkalmazások vezérlői](security-center-adaptive-application.md) segítségével korlátozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Más előnyök mellett ez segít megerősíteni a virtuális gépeket a kártevők ellen. A gépi tanulás használatával a Security Center elemzi a virtuális gépen futó folyamatokat, hogy segítsen az engedélyezési listák létrehozásában.


## <a name="incident-response"></a>Incidensmegoldás
A Security Center észleli az előforduló fenyegetéseket, és riasztást küld róluk. Javasoljuk, hogy mindig kövesse figyelemmel az új biztonsági riasztásokat, és tegye meg a szükséges lépéseket a támadás alaposabb kivizsgálása vagy következményeinek elhárítása érdekében. A Security Center veszélyforrások elleni védelem működésével kapcsolatos további információkért olvassa el, [Hogyan észleli és reagáljon a Azure Security Center a fenyegetésekre](security-center-alerts-overview.md#detect-threats).

Habár ez a cikk nem nyújt segítséget a saját incidensekre vonatkozó válaszadási terv létrehozásához, Microsoft Azure biztonsági választ fogunk használni a felhő életciklusában az incidensek megválaszolásának alapjaként. Ezek a szakaszok a következő ábrán láthatók:

![Az incidens válaszának szakaszai a felhő életciklusában](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> A terv létrehozásában segítséget nyújthat az amerikai National Institute of Standards and Technology (Országos Szabványügyi és Technológiai Intézet, NIST) által kidolgozott [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Útmutató a számítógépes biztonsági incidensek kezeléséhez) című dokumentum.
>

A Security Center riasztásait a következő szakaszokban használhatja:

* **Észlelés**: a gyanús tevékenységek azonosítása egy vagy több erőforrásban.
* **Felmérés**: az első vizsgálat végrehajtása a gyanús tevékenységgel kapcsolatos további információk összegyűjtéséhez.
* **Diagnosztizálás**: a hibaelhárítási lépések végrehajtása a probléma megoldásához szükséges technikai eljárás lefolytatásához.

A biztonsági riasztásokban mindig talál olyan információkat, amelyek segítségével jobban megismerheti a támadás természetét. A riasztás ezenfelül lehetséges kockázatcsökkentési megoldásokat is tartalmaz. Egyes riasztásokban további információkra, vagy az Azure-ban található más forrásokra mutató hivatkozások is helyet kaptak. Az így kapott információk alapján további vizsgálatokat indíthat, és megkezdheti a kockázatcsökkentést, valamint a munkaterületen tárolt, a biztonsággal kapcsolatos adatokat is kereshet.

A következő példában gyanús RDP-tevékenységre figyelmeztető üzenetet láthat:

![Gyanús tevékenység](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Ezen a lapon a támadás idejére, a forrás eszköznevére és a megcélzott virtuális gépre vonatkozó adatokat, illetve a javasolt következő lépésre vonatkozó információkat talál. Bizonyos esetekben előfordulhat, hogy a támadás forrására vonatkozó információ üres. Ezzel kapcsolatban további információkat talál a [Missing Source Information in Azure Security Center Alerts](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts) (Hiányzó forrásadatok az Azure Security Center riasztásaiban) című cikkben.

A feltört rendszer azonosítása után futtathat egy korábban létrehozott munkafolyamat- [automatizálást](workflow-automation.md) . Ezek olyan eljárások gyűjteményei, amelyeket Security Center lehet végrehajtani a riasztások indítása után.

Az [incidensekre adott válaszokkal kapcsolatos videók Azure Security Center & Microsoft Operations Management Suite kihasználása](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) érdekében láthat néhány bemutatót, amely bemutatja, hogyan használhatók a Security Center az egyes fázisokban.

> [!NOTE]
> Tekintse át a [biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md) című témakört, amelyből megtudhatja, hogyan használhatja a Security Center képességeket az incidensek megválaszolásának megkönnyítése érdekében.
>
>

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megismerkedhetett a Security Center bevezetésével. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Partneri megoldások monitorozása Azure Security Centerokkal](./security-center-partner-integration.md) – megtudhatja, hogyan figyelheti a partneri megoldások állapotát.
* [Azure Security Center GYIK](faq-general.md) – gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security Blog](/archive/blogs/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.