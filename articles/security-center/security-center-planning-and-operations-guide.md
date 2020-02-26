---
title: Útmutató a Security Center tervezéséhez és működtetéséhez | Microsoft Docs
description: Ez a dokumentum segít az Azure Security Center bevezetése előtti tervezésben, valamint megismerteti a napi műveletekhez tartozó szempontokkal.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 039a30d23c45471d88132f544c11df813fb4b8e6
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603734"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Útmutató az Azure Security Center tervezéséhez és működtetéséhez
Ez az útmutató olyan informatikai (IT) szakemberek, informatikai építészek, Információbiztonsági elemzők és felhőalapú rendszergazdák számára készült, akik a Azure Security Center használatát tervezik.


## <a name="planning-guide"></a>Tervezési útmutató
Ez az útmutató azokat a feladatokat ismerteti, amelyeket követve optimalizálhatja Security Center használatát a szervezet biztonsági követelményei és a felhőalapú felügyeleti modell alapján. Ahhoz, hogy a lehető legnagyobb mértékben kihasználhassa a Security Center által nyújtott előnyöket, fontos tisztában lennie azzal, hogy a vállalat különböző osztályai és dolgozói hogyan használják a szolgáltatást, mivel így könnyebben teljesítheti a biztonságos fejlesztésre, működésre, ellenőrzésre, irányításra és incidensmegoldásra vonatkozó követelményeket. A Security Center használatának tervezésekor fordítson különös figyelmet a következő területekre:

* Biztonsági szerepkörök és hozzáférés-vezérlés
* Biztonsági szabályzatok és javaslatok
* Adatgyűjtés és -tárolás
* Folyamatos nem Azure-erőforrások
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

A Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti. A Security Center megnyitásakor a felhasználók csak azon erőforrások információit látják, amelyekhez hozzáféréssel rendelkeznek. Ez azt jelenti, hogy a felhasználó az előfizetés vagy az erőforrást tartalmazó erőforráscsoport tulajdonosi, közreműködői vagy olvasói szerepköréhez van hozzárendelve. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- **Biztonsági olvasó**: az ehhez a szerepkörhöz tartozó felhasználó csak a Security Center-konfigurációkat tekintheti meg, amelyekbe beletartoznak a javaslatok, riasztások, szabályzatok és az állapot, de módosításokat nem hajthat végre.
- **Biztonsági rendszergazda**: ugyanazokkal a jogosultságokkal rendelkezik mint a biztonsági olvasó, de frissítheti a biztonsági szabályzatot, valamint javaslatokat és riasztásokat utasíthat el.

A fent bemutatott Security Center-szerepkörök nem rendelkeznek hozzáféréssel az Azure egyéb szolgáltatási területeihez, például a Storage-hez, a Web & Mobile-hoz vagy az Eszközök internetes hálózatához.

Az előző ábrán felsorolt személyek esetében a következő szerepköralapú hozzáférés-vezérlés szükséges:

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

A Security Centerre vonatkozó szerepköralapú hozzáférés-vezérlés kialakítása során fontos, hogy tisztában legyen azzal, hogy a vállalatnál ki fogja használni a Security Centert. Ezenkívül tudnia kell azt, hogy ezek a személyek milyen típusú feladatokat fognak végrehajtani, és ennek megfelelően kell beállítania a szerepköralapú hozzáférés-vezérlést.

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Érdemes például az Olvasó szerepkört rendelni azokhoz a felhasználókhoz, akik csupán megtekintik az erőforrások biztonsági állapotát, de nem tesznek lépéseket azzal kapcsolatban (tehát nem alkalmazhatnak javaslatokat, és nem módosíthatják a szabályzatokat).
>
>

## <a name="security-policies-and-recommendations"></a>Biztonsági szabályzatok és javaslatok
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. A Security Centerben a számítási feladatok típusának vagy az adatok bizalmasságának megfelelően határozhatja meg az Azure-előfizetésekre vonatkozó szabályzatokat.

A Security Center-szabályzatok a következő összetevőkből állnak:
- [Adatgyűjtés](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): ügynökkiépítési és adatgyűjtési beállítások.
- [Biztonsági házirend](https://docs.microsoft.com/azure/security-center/security-center-policies): egy [Azure Policy](../governance/policy/overview.md) , amely meghatározza, hogy mely vezérlőket figyeli és javasolja a Security Center, vagy a Azure Policy használatával új definíciókat hozhat létre, további házirendeket határozhat meg, és szabályzatokat rendelhet hozzá a felügyeleti csoportokhoz.
- [E-mail-értesítések](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): biztonsági felelősök kapcsolati adatai és értesítési beállításai.
- [Tarifacsomag](https://docs.microsoft.com/azure/security-center/security-center-pricing): ingyenes vagy standard díjszabás kiválasztása, amely meghatározza, hogy a Security Center mely szolgáltatásai érhetők el a hatókörbe eső erőforrásokhoz (beállítható előfizetésekhez, erőforráscsoportokhoz és munkaterületekhez).

> [!NOTE]
> Biztonsági kapcsolattartó kinevezésével biztosítható, hogy az Azure a biztonsági incidensek esetén elérje a megfelelő illetékes személyt. Olvassa el a [Provide security contact details in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) (A biztonsági kapcsolattartó adatainak megadása az Azure Security Centerben) részt, amelyből megtudhatja, hogyan engedélyezheti ezt a javaslatot.

### <a name="security-policies-definitions-and-recommendations"></a>Biztonsági szabályzatok definíciói és javaslatok
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A szabályzatot a Security Centerben szerkesztheti, vagy az Azure Policy használatával létrehozhat új meghatározásokat, meghatározhat további szabályzatokat, valamint hozzárendelhet szabályzatokat a Felügyeleti csoportokhoz (ezek jelenthetik a teljes céget vagy szervezetet, egy üzleti egységet stb.), és monitorozhatja az adott szabályzatokra vonatkozó megfelelőséget ezeken a hatókörökön belül.

A biztonsági szabályzatok konfigurálása előtt mindig olvassa el a [biztonsági javaslatokat](https://docs.microsoft.com/azure/security-center/security-center-recommendations), és döntse el, hogy ezek a szabályzatok megfelelőek-e az Ön által használt előfizetésekhez és erőforráscsoportokhoz. Ezenkívül fontos, hogy tisztában legyen azzal, milyen lépéseket kell tennie a biztonsági javaslatok kezelése érdekében, és a szervezeténél ki az új javaslatok monitorozásának és a szükséges lépések megtételének a felelőse.

## <a name="data-collection-and-storage"></a>Adatgyűjtés és -tárolás
Azure Security Center a Microsoft monitoring agentet használja – ez ugyanaz az ügynök, amelyet a Azure Monitor szolgáltatás használ – a virtuális gépekről származó biztonsági adatok gyűjtésére. Az ebből az ügynökből [gyűjtött adatokat](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) a rendszer a Log Analytics-munkaterület(ek)en tárolja.

### <a name="agent"></a>Ügynök

Miután a biztonsági szabályzatban engedélyezte az automatikus kiépítést, a rendszer telepíti a Microsoft Monitoring Agentet ([Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) vagy [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) rendszerhez) az összes támogatott Azure-beli és újonnan létrehozott virtuális gépen. Ha a virtuális gépre vagy a számítógépre már telepítve van a Microsoft Monitoring Agent, az Azure Security Center az aktuális telepített ügynököt használja. Az ügynök folyamata nem invazív és rendkívül csekély hatással van a virtuális gép teljesítményére.

A Windows rendszerhez készült Microsoft Monitoring Agenthez a 443-as TCP-port használata szükséges. További részleteket talál a [Hibaelhárításról szóló cikkben](security-center-troubleshooting-guide.md).

Ha bármikor ki szeretné kapcsolni az adatgyűjtést, ezt a biztonsági szabályzatban teheti meg. Mivel azonban a Microsoft monitoring agentet más Azure felügyeleti és figyelési szolgáltatások is használhatják, az ügynök nem lesz automatikusan eltávolítva, amikor kikapcsolja az adatgyűjtés Security Center. Ha szükséges, manuálisan távolíthatja el az ügynököt.

> [!NOTE]
> A támogatott virtuális gépek listáját az [Az Azure Security Centerhez kapcsolódó gyakori kérdések (GYIK)](faq-vms.md) című témakörben találja meg.

### <a name="workspace"></a>Munkaterület

A munkaterület egy adattárolóként szolgáló Azure-erőforrás. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

A Microsoft Monitoring Agentből (az Azure Security Center nevében) gyűjtött adatok az Azure-előfizetésével társított meglévő Log Analytics-munkaterületen tárolódnak, vagy egy új munkaterületen, a virtuális gép földrajzi helyét is figyelembe véve.

Az Azure Portalon megkeresheti a Log Analytics munkaterületeinek listáját, beleértve azokat is, amelyeket az Azure Security Center hozott létre. Az új munkaterületekhez kapcsolódó erőforráscsoport fog létrejönni. Mindkettő ezt az elnevezési konvenciót követi:

* Munkaterület: *AlapértelmezettMunkaterület-[előfizetés-azonosító]-[geo]*
* Erőforráscsoport: *DefaultResourceGroup-[geo]*

Az Azure Security Center által létrehozott munkaterületek adatait 30 napig őrzi meg a rendszer. A meglévő munkaterületeknél a megőrzési idő a munkaterület tarifacsomagjától függ. Ha szeretné, használhat egy létező munkaterületet is.

> [!NOTE]
> A Microsoft fontos kötelességének tekinti ezeknek az adatoknak a védelmét és biztonságát. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig. Az adatkezeléssel és adatbiztonsággal kapcsolatos további információkért olvassa el az [Azure Security Center adatbiztonság](security-center-data-security.md) című cikket.
>

## <a name="onboarding-non-azure-resources"></a>Nem Azure-erőforrások előkészítése

A Security Center a nem Azure-számítógépek biztonsági állapotát is képes monitorozni, azonban ehhez elő kell készítenie ezeket az erőforrásokat. A nem Azure-erőforrások előkészítésével kapcsolatos további információkért olvassa el a [fokozott biztonságot nyújtó Azure Security Center Standard előfizetésre történő előkészítéssel](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) foglalkozó témakört.

## <a name="ongoing-security-monitoring"></a>A biztonság folyamatos ellenőrzése
A kezdeti konfigurációt, illetve a Security Center javaslatainak alkalmazását követően a következő lépés a Security Center működési folyamatainak megtervezése.

A Security Center Áttekintés felülete egységesített képet nyújt Azure-erőforrásai és csatlakoztatott nem Azure-erőforrásai biztonsági állapotáról. Az alábbi példa egy számos elhárítandó problémával rendelkező környezetet ábrázol:

![irányítópult](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> A Security Center nem akadályozza Önt a megszokott eljárások elvégzésében, csupán passzívan ellenőrzi az üzemelő példányokat, és javaslatokat biztosít a beállított biztonsági szabályzatok alapján.

Amikor első alkalommal engedélyezi az aktuálisan használt Azure-környezethez a Security Center használatát, ellenőrizze, hogy elolvasta-e az összes javaslatot. Ezt a **Javaslatok** csempén, illetve erőforrásonként (**Számítás**, **Hálózat**, **Tárolás és adatok** és **Alkalmazás**) teheti meg.

Ha már az összes javaslattal foglalkozott, az összes érintett erőforráshoz tartozó **Megelőzés** szakasznak zölden kell megjelennie. Innentől kezdve egyszerűbbé válik a folyamatos ellenőrzés, hiszen kizárólag akkor kell lépéseket tennie, ha az erőforrások biztonsági állapotában változás következik be, vagy javaslat jelenik meg a megfelelő csempén.

Az **Észlelés** szakasz gyakrabban változik. Itt azok az aktuálisan zajló, illetve a múltban lezajlott és a Security Center és a külső fejlesztőktől származó rendszerek vezérlőelemei által észlelt problémákkal kapcsolatos riasztások láthatók. A Biztonsági riasztások csempén az egyes napokon jelzett fenyegetésészlelési figyelmeztetések számát, illetve ezek kategóriák (alacsony, közepes, magas) szerinti eloszlását mutató oszlopdiagramok láthatók. A biztonsági riasztásokkal kapcsolatos további információkért lásd: [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben).

Építse be a [fenyegetésfelderítési](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) szolgáltatás használatát napi biztonsági rutinjába. Itt azonosíthatja a környezetre leselkedő biztonsági fenyegetéseket, például megállapíthatja, ha egy adott számítógép egy botnet része.

### <a name="monitoring-for-new-or-changed-resources"></a>Új vagy módosult erőforrások keresése
A legtöbb Azure-környezet dinamikus, és az erőforrások létrehozása, megpördült vagy leskálázása, újrakonfigurálása és módosítása történik. A Security Center segítségével könnyen átláthatja az új erőforrások biztonsági állapotát.

A Security Center automatikusan felfedezi az Azure-környezethez adott új erőforrásokat (virtuális gépeket, SQL-adatbázisokat stb.), és elkezdi a biztonsági állapotuk figyelését. Ide tartoznak a PaaS webes és feldolgozói szerepkörei is. Ha a [Biztonsági szabályzat](tutorial-security-policy.md) beállításainál bekapcsolja az adatgyűjtést, a rendszer automatikusan további figyelési funkciókat kapcsol be a virtuális gépeken.

![Fontos területek](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Virtuális gépek esetén kattintson a **számítási & alkalmazások**elemre az **erőforrás-biztonsági higiénia** szakaszban. Az adatokhoz vagy az azzal kapcsolatos javaslatokhoz tartozó problémák az **Áttekintés** lapon a **Figyelési javaslatok** részben jelennek meg.
2. A **Javaslatok** áttekintésével tudhatja meg, hogy a rendszer azonosított-e biztonsági kockázatokat az új erőforrással kapcsolatban, és ha igen, miket.
3. Gyakran előfordul, hogy a környezethez adott új virtuális gépekre csak az operációs rendszer van telepítve. Az erőforrás tulajdonosának több időre lehet szüksége a virtuális gépeken használt további alkalmazások telepítéséhez.  Az az ideális, ha tisztában van a számítási feladat végső céljával. Alkalmazáskiszolgálóként fogja használni? Az új számítási feladat típusától függően beállíthatja a megfelelő **biztonsági szabályzatot**, amely a jelen munkafolyamat harmadik lépése.
4. Az Azure-környezetbe új erőforrások hozzáadásakor új riasztások jelenhetnek meg a **biztonsági riasztások** csempén. Keresse meg az új riasztásokat ebben a csempében, és kövesse az ajánlásokat.

Emellett rendszeresen figyelnie kell a meglévő erőforrásokat azokra a konfigurációs változásokra vonatkozóan, amelyek biztonsági kockázatokat hoztak létre, az ajánlott alaptervek és a biztonsági riasztások miatt. Kezdje a Security Center irányítópultjánál. Innentől kezdve három fő területet kell megvizsgálnia, amelyek következetesen áttekinthetők.

![Műveletek](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. A **Megelőzés** szakasz paneljén gyorsan elérheti a legfontosabb erőforrásokat. Ezzel a funkcióval monitorozhatja a Számítást, a Hálózatot, a Tárolást és adatokat, illetve az Alkalmazásokat.
2. A **Javaslatok** panelen megtekintheti a Security Center javaslatait. A folyamatos monitorozás során előfordulhat, hogy nem rendelkezik napi javaslatokkal, ami normális, mivel a kezdeti Security Center-telepítésre vonatkozó összes javaslatot feloldotta. Ezért fordulhat elő, hogy ebben a szakaszban nem jelennek meg minden nap új információk, így csak olyankor kell megnyitnia, ha szükséges.
3. Az **Észlelés** szakasz vagy rendkívül gyakran, vagy rendkívül ritkán jelez változásokat. Mindig tekintse meg a biztonsági riasztásokat, és tegye meg a Security Center javaslatai szerinti lépéseket.

### <a name="hardening-access-and-applications"></a>A hozzáférés megnehezítése és az alkalmazások védelmének megerősítése

A biztonsági folyamatai részeként érdemes bevezetnie megelőző intézkedéseket a virtuális gépek hozzáférésének megakadályozása és a rajtuk futó alkalmazások szabályozása érdekében. Az Azure-beli virtuális gépek bemenő forgalmának zárolásával kevésbé fogják veszélyeztetni a támadások, ugyanakkor könnyű hozzáférést biztosít arra az esetre, amikor csatlakozni kell a virtuális gépekhez. Az [igény szerinti virtuálisgép-hozzáférési](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) szolgáltatással megerősítheti a virtuális gépekhez való hozzáférést.

Az [adaptív alkalmazások vezérlői](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) segítségével korlátozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Más előnyök mellett ez segít megerősíteni a virtuális gépeket a kártevők ellen. A gépi tanulás használatával a Security Center elemzi a virtuális gépen futó folyamatokat, hogy segítsen az engedélyezési szabályok létrehozásában.


## <a name="incident-response"></a>Incidensmegoldás
A Security Center észleli az előforduló fenyegetéseket, és riasztást küld róluk. Javasoljuk, hogy mindig kövesse figyelemmel az új biztonsági riasztásokat, és tegye meg a szükséges lépéseket a támadás alaposabb kivizsgálása vagy következményeinek elhárítása érdekében. A Security Center fenyegetések észlelésének működésével kapcsolatos további információkért olvassa el, [Hogyan észleli és reagáljon a Azure Security Center a fenyegetésekre](security-center-alerts-overview.md#detect-threats).

Bár ez a cikk nem nyújt segítséget a saját incidensmegoldási tervének kidolgozásához, a Microsoft Azure Security Response szolgáltatást fogjuk használni a felhő életciklusában az incidensmegoldási szakaszok alapjaként. Ezek a szakaszok a következő ábrán láthatók:

![Gyanús tevékenység](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

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

Ezen a lapon a támadás idejére, a forrás eszköznevére és a megcélzott virtuális gépre vonatkozó adatokat, illetve a javasolt következő lépésre vonatkozó információkat talál. Bizonyos esetekben előfordulhat, hogy a támadás forrására vonatkozó információ üres. Ezzel kapcsolatban további információkat talál a [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Hiányzó forrásadatok az Azure Security Center riasztásaiban) című cikkben.

Erről a lapról is indítható [vizsgálat](https://docs.microsoft.com/azure/security-center/security-center-investigation) a támadás idővonalának, a támadás lefolyásának, a veszélyeztetett rendszereknek és a használt hitelesítő adatoknak a behatóbb ismerete és a teljes támadásfolyam grafikus megjelenítése érdekében.

A feltört rendszer azonosítása után futtathat egy korábban létrehozott munkafolyamat- [automatizálást](workflow-automation.md) . Ezek olyan eljárások gyűjteményei, amelyeket Security Center lehet végrehajtani a riasztások indítása után.

Az [incidensekre adott válaszokkal kapcsolatos videók Azure Security Center & Microsoft Operations Management Suite kihasználása](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) érdekében olyan bemutatókat láthat, amelyek segítségével megismerheti, hogy a Security Center hogyan használhatók az egyes fázisokban.

> [!NOTE]
> Tekintse át a [biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md) című témakört, amelyből megtudhatja, hogyan használhatja a Security Center képességeket az incidensek megválaszolásának megkönnyítése érdekében.
>
>

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megismerkedhetett a Security Center bevezetésével. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](faq-general.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
