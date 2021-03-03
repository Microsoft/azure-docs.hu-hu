---
title: A Azure Security Center kibocsátási megjegyzései
description: A Azure Security Center újdonságait és változásait ismertető Leírás
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: memildin
ms.openlocfilehash: e34d5520e13d45d15079a5f11775d2ef930fc62a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727090"
---
# <a name="whats-new-in-azure-security-center"></a>A Azure Security Center újdonságai

Security Center aktívan működik, és folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja az oldalt, amely az új funkciókkal, hibajavításokkal és elavult funkciókkal kapcsolatos információkat tartalmaz.

Ez az oldal gyakran frissül, ezért gyakran érdemes újra felkeresni. 

Ha szeretne többet megtudni a Security Center hamarosan elérhető *tervezett* változásokról, tekintse meg [a Azure Security Center fontos jövőbeli változásait](upcoming-changes.md). 

> [!TIP]
> Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Security Center újdonságait](release-notes-archive.md).


## <a name="february-2021"></a>2021. február

A februári frissítések a következők:

- [Új biztonsági riasztások oldal a Azure Portal általánosan elérhetővé vált (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Az általánosan elérhető Kubernetes munkaterhelés-védelmi javaslatok (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [A Microsoft Defender for Endpoint Integration with Azure Defender mostantól támogatja a Windows Server 2019 és a Windows 10 Virtual Desktop (WVD) használatát (előzetes verzió)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Közvetlen hivatkozás a szabályzatra a javaslat részletei oldalon](#direct-link-to-policy-from-recommendation-details-page)
- [Az SQL-adatbesorolásra vonatkozó javaslat már nem érinti a biztonságos pontszámot](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [A munkafolyamatok automatizálása a szabályozások megfelelőségi felmérésének változásai alapján indítható el (előzetes verzió)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [A tárgyieszköz-leltár oldal továbbfejlesztései](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Új biztonsági riasztások oldal a Azure Portal általánosan elérhetővé vált (GA)

A Azure Security Center biztonsági riasztások lapja az alábbiak biztosítására lett újratervezve:

- **Jobb osztályozási élmény a riasztásokhoz** – segít csökkenteni a riasztások fáradtságát, és könnyebben koncentrálni a legfontosabb fenyegetésekre, a lista tartalmaz testreszabható szűrőket és csoportosítási lehetőségeket.
- **További információk a riasztások listájában** – például a MITRE ATT&ACK taktikák.
- **Riasztás létrehozása** – az Azure Defender képességeinek kiértékeléséhez és a riasztások teszteléséhez gomb. konfiguráció (SIEM-integrációhoz, e-mail-értesítések és munkafolyamat-automatizálások esetén) az összes Azure Defender-csomagból létrehozhatók minta-riasztások.
- Az **Azure Sentinel incidensekkel való összehangolása** – a két terméket használó ügyfelek esetében a váltás mostantól egyszerű, és könnyen megtanulható a többi közül.
- **Nagyobb teljesítmény** a nagyméretű riasztások listájához.
- A riasztások listáján **navigáljon a billentyűzeten** .
- **Riasztások az Azure Resource Graph-ból** – a riasztásokat lekérdezheti az Azure Resource Graph-ban, a Kusto API-t az összes erőforráshoz. Ez akkor is hasznos, ha saját riasztási irányítópultokat épít ki. [További információ az Azure Resource Graph-ról](../governance/resource-graph/index.yml).
- **Példa a riasztások szolgáltatás létrehozására** – az új riasztások tapasztalataiból származó minta riasztások létrehozásával kapcsolatban lásd: [minta Azure Defender-riasztások](security-center-alert-validation.md#generate-sample-azure-defender-alerts)létrehozása.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center biztonsági riasztások listája":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Az általánosan elérhető Kubernetes munkaterhelés-védelmi javaslatok (GA)

Örömmel jelentjük be, hogy a Kubernetes munkaterhelés-védelemre vonatkozó javaslatok általános elérhetősége (GA).

Annak biztosítása érdekében, hogy a Kubernetes-munkaterhelések alapértelmezés szerint biztonságosak legyenek, Security Center Kubernetes-szinten megerősítő javaslatokat adott hozzá, beleértve a Kubernetes-belépésvezérlés használatával történő kényszerítési lehetőségeket.

Ha a Kubernetes Azure Policy-bővítménye telepítve van az Azure Kubernetes Service-(ak-) fürtön, a Kubernetes API-kiszolgálónak küldött összes kérést az előre meghatározott ajánlott eljárások határozzák meg. Ezután konfigurálhatja az ajánlott eljárások betartatását és a jövőbeli munkaterhelések megadását.

Megadhatja például, hogy az emelt szintű tárolók ne legyenek létrehozva, és minden jövőbeli kérelem le lesz tiltva.

További információ a [munkaterhelések elleni védelemben – ajánlott eljárások a Kubernetes belépésvezérlés használatával](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Habár a javaslatok előzetes verzióban voltak, nem tudták nem megfelelő állapotba helyezni az AK-beli fürterőforrás-erőforrásokat, és nem szerepeltek a biztonságos pontszám számításában. Ebben a GA-közleményben ezek a pontszám kiszámításakor szerepelnek. Ha már nem szervizelte őket, ez a biztonsági pontszám enyhe kihatását okozhatja. Ha lehetséges, javítsa azokat a [Azure Security Centerban található javaslatok szervizelése](security-center-remediate-recommendations.md)című témakörben leírtak szerint.


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>A Microsoft Defender for Endpoint Integration with Azure Defender mostantól támogatja a Windows Server 2019 és a Windows 10 Virtual Desktop (WVD) használatát (előzetes verzió)

A Microsoft Defender for Endpoint egy holisztikus, felhőalapú végponti biztonsági megoldás. Kockázatalapú sebezhetőségi kezelést és értékelést, valamint végpontok észlelését és reagálását (EDR) biztosít. A Defender és a Azure Security Center együttes használatának előnyeiről a következő témakörben talál további információt [: a végpontok Security Center integrált EDR megoldással](security-center-wdatp.md)való ellátása, a Microsoft Defender for Endpoint.

Ha engedélyezi az Azure Defender for Servers szolgáltatást a Windows Serveren, a rendszer a csomaghoz tartozó Defender-licencet is tartalmazza. Ha már engedélyezte az Azure Defender for Servers szolgáltatást, és rendelkezik az előfizetésében található Windows 2019-kiszolgálókkal, a rendszer automatikusan megkapja a Defendert a jelen frissítéssel rendelkező végpontnak. Nincs szükség manuális beavatkozásra. 

A támogatás mostantól kibővült a Windows Server 2019 és a [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)szolgáltatással.

> [!NOTE]
> Ha Windows Server 2019 rendszerű gépen engedélyezi a Defender számára a végpontot, győződjön meg arról, hogy megfelel a [Microsoft Defender for Endpoint Integration engedélyezése](security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration)című témakörben ismertetett előfeltételeknek.

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Közvetlen hivatkozás a szabályzatra a javaslat részletei oldalon

A javaslatok részleteinek áttekintéséhez gyakran hasznos, ha látni szeretné a mögöttes szabályzatot. A szabályzat által támogatott minden javaslathoz van egy új hivatkozás a javaslat részletei lapról:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Hivatkozás Azure Policy lapra a javaslatot támogató konkrét szabályzathoz":::

Ezzel a hivatkozással megtekintheti a szabályzat definícióját, és áttekintheti az értékelési logikát. 

Ha áttekinti a javaslatok listáját a [biztonsági javaslatok hivatkozási útmutatójában](recommendations-reference.md), akkor a szabályzat-definíciós lapokra mutató hivatkozásokat is láthatja:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Egy adott szabályzat Azure Policy oldalának elérése közvetlenül a Azure Security Center javaslatok hivatkozási oldaláról" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Az SQL-adatbesorolásra vonatkozó javaslat már nem érinti a biztonságos pontszámot
Az **SQL-adatbázisokban lévő bizalmas adatokra vonatkozó ajánlás besorolása** nem befolyásolja a biztonságos pontszámot. Ez az egyetlen javaslat az **adatbesorolás** biztonságának szabályozására, hogy a vezérlő most a 0 értékkel rendelkező biztonságos pontszám legyen.

A Security Center összes biztonsági vezérlőjének teljes listáját, valamint azok pontszámait és a javaslatok listáját lásd: [biztonsági vezérlők és javaslataik](secure-score-security-controls.md#security-controls-and-their-recommendations).

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>A munkafolyamatok automatizálása a szabályozások megfelelőségi felmérésének változásai alapján indítható el (előzetes verzió)
Egy harmadik adattípust adtunk hozzá a munkafolyamat-automatizálások trigger-beállításaihoz: a szabályozási megfelelőségi felmérések módosításai.

Megtudhatja, hogyan használhatja a munkafolyamat-automatizálási eszközöket a [Security Center triggerekre adott válaszok automatizálására](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="A szabályozások megfelelőségi vizsgálatának módosításai a Munkafolyamat-automatizálás elindításához" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>A tárgyieszköz-leltár oldal továbbfejlesztései
A Security Center eszköz leltározási lapja a következő módokon fejlődött:

- A lap tetején található összefoglalók mostantól tartalmazzák a nem **regisztrált előfizetéseket**, amelyek az Security Center engedélyezése nélküli előfizetések számát mutatják.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="A nem regisztrált előfizetések száma az Asset Inventory oldal tetején található összegzésekben":::

- A szűrők kibontása és továbbfejlesztése a következőkre terjed ki:
    - **Counts** – az egyes szűrők az egyes kategóriák feltételeit teljesítő erőforrások számát jelenítik meg

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Megszámolja a szűrőket a Azure Security Center tárgyieszköz-leltár lapján.":::

    - **Kivételeket tartalmazó szűrőt tartalmaz** (nem kötelező) – Szűkítse az eredményeket olyan erőforrásokra, amelyeknek nincsenek kivételei. Ez a szűrő alapértelmezés szerint nem jelenik meg, de a **szűrő hozzáadása** gombbal érhető el.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="A (z) &quot;kivételt tartalmazó&quot; szűrő hozzáadása Azure Security Center eszköz leltározási lapján":::

További információ az [erőforrások az eszközök leltározásával való megismeréséről és kezeléséről](asset-inventory.md).

## <a name="january-2021"></a>2021. január

A januári frissítések a következők:

- [Az Azure biztonsági teljesítményteszt mostantól az alapértelmezett házirend-kezdeményezés a Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [A helyszíni és a többfelhős gépek sebezhetőségi felmérése általánosan elérhető (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [A felügyeleti csoportok biztonságos pontszáma mostantól előzetes verzióban érhető el](#secure-score-for-management-groups-is-now-available-in-preview)
- [A Secure score API általánosan elérhető (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Az Azure Defender számára a App Servicehoz hozzáadott DNS-védelem](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [A többfelhős összekötők elérhetők az általánosan elérhető verzióban (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Az előfizetések és a felügyeleti csoportok biztonságos pontszámának teljes körű javaslatainak kizárása](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [A felhasználók mostantól a globális rendszergazdától igényelhetik a bérlői szintű láthatóságot](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 az Azure biztonsági teljesítményteszt lefedettségének növeléséhez hozzáadott előzetes javaslatok](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Az ajánlatok szűrt listájának CSV-exportálása](#csv-export-of-filtered-list-of-recommendations)
- [A "nem alkalmazható" erőforrások mostantól "megfelelőként" jelennek meg Azure Policy értékelésekben](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [A biztonságos pontszámra és a megfelelőségi előírások folyamatos exportálásával kapcsolatos heti Pillanatképek exportálása (előzetes verzió)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Az Azure biztonsági teljesítményteszt mostantól az alapértelmezett házirend-kezdeményezés a Azure Security Center

Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, Azure-specifikus irányelvek a biztonsági és megfelelőségi szabályzatok közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. Ez a széles körben tiszteletben lévő teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) által vezérelt vezérlőkre épül, és a felhő-központú biztonságra összpontosít.

Az elmúlt hónapokban Security Center a beépített biztonsági javaslatok listája jelentősen bővült a teljesítményteszt lefedettségének kibővítéséhez.

Ebből a kiadásból a benchmark a Security Center javaslatainak alapja, és az alapértelmezett házirend-kezdeményezésnek megfelelően teljes mértékben integrálva van. 

Az összes Azure-szolgáltatáshoz tartozik egy biztonsági alapkonfiguráció lap a dokumentációjában. [Ez például Security Center](security-baseline.md)alapkonfigurációja. Ezek az alapkonfigurációk az Azure biztonsági Teljesítménytesztre épülnek.

Ha Security Center szabályozási megfelelőségi irányítópultját használja, a teljesítményteszt két példányát fogja látni egy átmeneti időszak alatt:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center megfelelőségi irányítópultja, amely az Azure biztonsági Teljesítménytesztét mutatja be":::

A meglévő javaslatok nem érintettek, és a teljesítményteszt növekedésével a módosítások automatikusan megjelennek Security Centeron belül. 

További információt a következő lapokon talál:

- [További információ az Azure biztonsági teljesítménytesztről](../security/benchmarks/introduction.md)
- [A szabályzatok megfelelőségi irányítópultján lévő szabványok testreszabása](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>A helyszíni és a többfelhős gépek sebezhetőségi felmérése általánosan elérhető (GA)

Októberben bejelentettük az Azure arc-kompatibilis kiszolgálók vizsgálatának előzetes verzióját az [Azure Defender for Servers](defender-for-servers-introduction.md)integrált sebezhetőség-felmérési ellenőrzőeszköz (Qualys) használatával.

Most már megjelent az általános elérhetősége (GA).

Ha engedélyezte az Azure arc használatát a nem Azure-beli gépeken, Security Center az integrált biztonsági rések képolvasó üzembe helyezését teszi lehetővé manuálisan és méretezéssel.

Ezzel a frissítéssel kihasználhatja az **Azure Defender-kiszolgálók** erejét, hogy megszilárdítsa a sebezhetőségi kezelési programot az összes Azure-beli és nem Azure-beli eszközön.

Főbb képességek:

- A VA (sebezhetőségi felmérés) képolvasó üzembe helyezési állapotának figyelése az Azure arc-gépeken
- Az integrált VA-ügynök kiépítés a nem védett Windows-és Linux Azure-alapú arc-gépekre (manuálisan és nagy méretekben)
- Észlelt biztonsági rések fogadása és elemzése az üzembe helyezett ügynököktől (manuálisan és nagy méretben)
- Az Azure-beli virtuális gépek és az Azure arc-számítógépek egységes felhasználói felülete

[További információ az integrált sebezhetőségi képolvasó hibrid gépekre való üzembe helyezéséről](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[További információ az Azure arc használatára képes kiszolgálókról](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>A felügyeleti csoportok biztonságos pontszáma mostantól előzetes verzióban érhető el

A biztonságos pontszám lapon az előfizetés szintjén kívül a felügyeleti csoportok összesített biztonsági pontszáma is látható. Így most már megtekintheti a szervezet felügyeleti csoportjainak listáját, valamint az egyes felügyeleti csoportok pontszámát.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A felügyeleti csoportok biztonságos pontszámának megtekintése.":::

További információ a [Azure Security Center biztonságos pontszámáról és biztonsági vezérlőinek](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>A Secure score API általánosan elérhető (GA)

Most már elérheti a pontszámát a [Secure score API](/rest/api/securitycenter/securescores/)-n keresztül. Az API-módszerek lehetővé teszik az adatlekérdezés rugalmasságát és a biztonságos pontszámok saját jelentési mechanizmusának elkészítését az idő múlásával. Például:

- egy adott előfizetés pontszámának beszerzéséhez használja a **Secure scores** API-t
- a **Secure score Controls** API használatával listázhatja az előfizetések biztonsági vezérlőit és aktuális pontszámát

Ismerje meg a [GitHub-Közösség biztonságos pontszám területén](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)elérhető, a biztonságos pontszám API-val lehetséges külső eszközöket.

További információ a [Azure Security Center biztonságos pontszámáról és biztonsági vezérlőinek](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Az Azure Defender számára a App Servicehoz hozzáadott DNS-védelem

Az altartományok beszerzése gyakori, nagy súlyosságú fenyegetést jelent a szervezetek számára. Az altartományok átvétele akkor fordulhat elő, ha olyan DNS-rekorddal rendelkezik, amely egy kiépített webhelyre mutat. Az ilyen DNS-rekordokat "lelógó DNS"-bejegyzéseknek is nevezzük. A CNAME rekordok különösen sebezhetők a fenyegetéssel szemben. 

Az altartományok átvétele lehetővé teszi a veszélyforrások számára, hogy átirányítsák a szervezet tartománya számára a kártékony tevékenységeket végző helyekre irányuló forgalmat.

Az Azure Defender for App Service mostantól észleli a DNS-bejegyzéseket a App Service webhely leszerelése után. Ez az a pillanat, amikor a DNS-bejegyzés egy nem létező erőforrásra mutat, és a webhely ki van téve egy altartomány átvételének. Ezek a védelem elérhetőek, függetlenül attól, hogy a tartományokat Azure DNS vagy külső tartományregisztráló felügyeli-e, és hogy a Windows App Service és a Linux rendszeren is App Service-e.

További információ:

- [App Service riasztási hivatkozási tábla](alerts-reference.md#alerts-azureappserv) – két új Azure Defender-riasztást tartalmaz, amelyek akkor aktiválódnak, ha a rendszer LELÓGÓ DNS-bejegyzést észlel
- [DNS-bejegyzések letiltásának és a tartományon belüli átvétel elkerülésének megakadályozása](../security/fundamentals/subdomain-takeover.md) – Ismerje meg a altartományok átvételének fenyegetését és a lelógó DNS-aspektust
- [A App Service Azure Defender bemutatása](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>A többfelhős összekötők elérhetők az általánosan elérhető verzióban (GA)

A Felhőbeli számítási feladatok gyakran több felhőalapú platformra is kiterjednek, a Cloud Security servicesnek ugyanezt kell tennie.

Azure Security Center védi a munkaterheléseket az Azure-ban, a Amazon Web Services (AWS) és a Google Cloud Platformban (GCP).

Az AWS-vagy GCP-fiókok csatlakoztatásával a natív biztonsági eszközöket, például az AWS Security hub-t és a GCP Security Command centert integrálhatja Azure Security Centerba.

Ez a funkció azt jelenti, hogy a Security Center az összes jelentős felhőalapú környezet láthatóságát és védelmét biztosítja. Az integráció néhány előnye:

- Az ügynök automatikus kiépítése – Security Center az Azure arc használatával helyezi üzembe a Log Analytics-ügynököt az AWS-példányokon
- Szabályzatkezelés
- Biztonságirés-kezelés
- Beágyazott végpontok észlelése és válasza (EDR)
- Biztonsági konfigurációs beállítások észlelése
- Egyetlen nézet, amely az összes felhőalapú szolgáltató biztonsági javaslatait tartalmazza
- Az összes erőforrás beépítése Security Center biztonságos pontszámának számításaiba
- Az AWS-és GCP-erőforrások szabályozási megfelelőségi értékelése

A Security Center menüjében válassza a **többfelhős összekötők** lehetőséget, és megtekintheti az új összekötők létrehozásának lehetőségeit:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="AWS-fiók hozzáadása gomb a Security Center multi Cloud Connectors oldalán":::

További információ:
- [AWS-fiókok összekötése Azure Security Center](quickstart-onboard-aws.md)
- [A GCP-fiókok összekapcsolásának Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Az előfizetések és a felügyeleti csoportok biztonságos pontszámának teljes körű javaslatainak kizárása

Kiterjesztjük a mentesítési képességet a teljes javaslatok befoglalására. További lehetőségeket biztosít az előfizetések, a felügyeleti csoportok és az erőforrások Security Center által nyújtott biztonsági javaslatok finomhangolásához.

Időnként előfordulhat, hogy az erőforrás nem kifogástalan állapotú, ha ismeri a problémát egy olyan külső eszköz, amelyet Security Center még nem észlelt. Vagy egy javaslat olyan hatókörben fog megjelenni, amelyben úgy érzi, hogy nem tartozik hozzá. Előfordulhat, hogy a javaslat nem megfelelő egy adott előfizetéshez. Vagy lehet, hogy a szervezete úgy döntött, hogy elfogadja az adott erőforrással vagy javaslattal kapcsolatos kockázatokat.

Ezzel az előzetes verziójú szolgáltatással mostantól kivételt hozhat létre a következőhöz:

- **Kivételt** képeznek az erőforrások, hogy a jövőben ne jelenjen meg a nem megfelelő állapotú erőforrások, és nem befolyásolja a biztonságos pontszámot. Az erőforrás nem alkalmazhatóként jelenik meg, és az OK "kivételként" jelenik meg a kiválasztott indoklással.

- **Kivételt képez az előfizetés vagy a felügyeleti csoport alól** , hogy a javaslat ne befolyásolja a biztonságos pontszámot, és a jövőben ne jelenjen meg az előfizetés vagy a felügyeleti csoport számára. Ez a meglévő erőforrásokhoz és a jövőben létrehozott mindenhez kapcsolódik. A javaslat a kiválasztott hatókörhöz megadott indoklással lesz megjelölve.

További információk: az [erőforrások és a javaslatok védelme a biztonságos pontszám alól](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>A felhasználók mostantól a globális rendszergazdától igényelhetik a bérlői szintű láthatóságot

Ha a felhasználó nem rendelkezik jogosultsággal Security Center adatok megjelenítéséhez, a rendszer most egy hivatkozást fog látni, amely a szervezet globális rendszergazdájától kér engedélyeket. A kérelem tartalmazza a kívánt szerepkört, valamint annak indoklását, hogy miért szükséges.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="A felhasználó bérlői szintű engedélyeket kérhet a felhasználónak, hogy tájékoztassa a felhasználót.":::

További információ a [bérlői szintű engedélyek kérése, ha a tiéd nem elegendő](security-center-management-groups.md#request-tenant-wide-permissions-when-yours-are-insufficient) .


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 az Azure biztonsági teljesítményteszt lefedettségének növeléséhez hozzáadott előzetes javaslatok

Az Azure Security benchmark a Azure Security Center alapértelmezett házirend-kezdeményezése. 

A teljesítményteszt lefedettségének növeléséhez a következő 35 előzetes javaslatok lettek hozzáadva a Security Centerhoz.

> [!TIP]
> Az előzetes verzióra vonatkozó javaslatok nem jelenítik meg az erőforrás állapotát, és nem tartoznak bele a biztonságos pontszám számításaiba. Ha lehetséges, javítsa őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul. További információ a javaslatokról a [Azure Security Centerban található javaslatok szervizelése](security-center-remediate-recommendations.md)című témakörben található.

| Biztonsági ellenőrzés                     | Új javaslatok                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Titkosítás engedélyezése nyugalmi állapotban            | – Azure Cosmos DB fiókoknak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához<br>-Azure Machine Learning munkaterületeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)<br>-A saját kulcsú adatvédelem engedélyezése a MySQL-kiszolgálókon<br>-A saját kulcsú adatvédelem engedélyezése a PostgreSQL-kiszolgálókon<br>-Cognitive Services fiókoknak engedélyeznie kell az adattitkosítást az ügyfél által felügyelt kulccsal (CMK)<br>– A tároló-beállításjegyzékek titkosítása ügyfél által felügyelt kulccsal (CMK) történik<br>– Az SQL felügyelt példányainak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához<br>– Az SQL-kiszolgálóknak az ügyfél által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához<br>– A Storage-fiókoknak ügyfél által felügyelt kulcsot (CMK) kell használniuk a titkosításhoz                                                                                                                                                              |
| Ajánlott biztonsági eljárások megvalósítása    | – A biztonsági problémákhoz az előfizetésekhez kapcsolattartó e-mail-címmel kell rendelkeznie<br> – Az Log Analytics ügynök automatikus kiépítés engedélyezése az előfizetésen<br> – Engedélyezni kell a nagy súlyosságú riasztások e-mailes értesítését<br> – Engedélyezni kell a nagy súlyosságú riasztások előfizetésének tulajdonosának szóló e-mailes értesítést.<br> – A Key vaultoknak engedélyezve kell lennie a kiürítési védelemmel<br> – A Key vaultok esetében engedélyezve van a helyreállítható törlés |
| Hozzáférés és engedélyek kezelése        | – A Function alkalmazásoknak engedélyezve kell lennie az "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" |
| Alkalmazások elleni védelem a DDoS-támadások ellen | – A webalkalmazási tűzfalat (WAF) engedélyezni kell Application Gateway<br> – Engedélyezni kell a webalkalmazási tűzfalat (WAF) az Azure bejárati szolgáltatásának szolgáltatásához |
| Jogosulatlan hálózati hozzáférés korlátozása | – Engedélyezni kell a tűzfalat Key Vault<br> -A magánhálózati végpontot be kell állítani Key Vault<br> – Az alkalmazás konfigurációjának privát hivatkozást kell használnia<br> – Az Azure cache for Redis-nek egy virtuális hálózaton belül kell lennie<br> – Azure Event Grid tartományoknak privát hivatkozást kell használniuk<br> – Azure Event Grid témaköröknek privát hivatkozást kell használniuk<br> – Azure Machine Learning munkaterületeknek privát hivatkozást kell használniuk<br> – Az Azure Signaler szolgáltatásnak privát hivatkozást kell használnia<br> – Az Azure Spring Cloud-nak hálózati befecskendezést kell használnia<br> – A tároló-beállításjegyzékek nem engedélyezhetik a nem korlátozott hálózati hozzáférést<br> – A tároló-beállításjegyzékeknek privát hivatkozást kell használniuk<br> – A MariaDB-kiszolgálók esetében le kell tiltani a nyilvános hálózati hozzáférést<br> – A nyilvános hálózati hozzáférést le kell tiltani a MySQL-kiszolgálók esetében<br> – A nyilvános hálózati hozzáférést le kell tiltani a PostgreSQL-kiszolgálókon<br> – A Storage-fióknak magánhálózati kapcsolatot kell használnia<br> – A Storage-fiókoknak a virtuális hálózati szabályok használatával kell korlátoznia a hálózati hozzáférést<br> – A virtuálisgép-rendszerkép-készítő sablonoknak privát hivatkozást kell használniuk|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Kapcsolódó hivatkozások:

- [További információ az Azure biztonsági teljesítménytesztről](../security/benchmarks/introduction.md)
- [További információ a Azure Database for MariaDB](../mariadb/overview.md)
- [További információ a Azure Database for MySQL](../mysql/overview.md)
- [További információ a Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Az ajánlatok szűrt listájának CSV-exportálása 

November 2020-én szűrők lettek hozzáadva a javaslatok oldalhoz (a[javaslatok listája mostantól tartalmazza a szűrőket is](#recommendations-list-now-includes-filters)). Decemberben kibővítettük ezeket a szűrőket (a[javaslatok oldalon új szűrők találhatók a környezethez, a súlyossághoz és a rendelkezésre álló válaszokhoz](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Ebben a közleményben a **Letöltés CSV-** re gombra változik, így a CSV-Exportálás csak a szűrt listában jelenleg megjelenő javaslatokat tartalmazza. 

Az alábbi képen például láthatja, hogy a lista két javaslatra van szűrve. A létrehozott CSV-fájl tartalmazza a két javaslat által érintett összes erőforrás állapotának részleteit.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Szűrt javaslatok exportálása CSV-fájlba":::

További tudnivalókat a [Azure Security Center biztonsági javaslataiban](security-center-recommendations.md)olvashat.


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>A "nem alkalmazható" erőforrások mostantól "megfelelőként" jelennek meg Azure Policy értékelésekben

Korábban a javaslathoz kiértékelt és a **nem** megfelelőnek talált erőforrások Azure Policy "nem megfelelő" néven jelennek meg. Egyetlen felhasználói művelet sem változtathatja meg a "megfelelő" állapotot. Ennek a változásnak a jelentése "megfelelőként" jelent meg a jobb érthetőség érdekében.

Az egyetlen hatása a Azure Policy, ahol a megfelelő erőforrások száma növekedni fog. Azure Security Center a biztonságos pontszáma nem lesz hatással.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>A biztonságos pontszámra és a megfelelőségi előírások folyamatos exportálásával kapcsolatos heti Pillanatképek exportálása (előzetes verzió)

Új előzetes verziójú funkciót adtunk hozzá a [folyamatos exportálási](continuous-export.md) eszközökhöz a biztonságos pontszámok és a szabályozási megfelelőségi adatmennyiségek heti pillanatképének exportálásához.

Ha folyamatos exportálást határoz meg, állítsa be az Exportálás gyakoriságát:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="A folyamatos exportálás gyakoriságának kiválasztása":::

- **Folyamatos átvitel** – az értékelések valós időben lesznek elküldve, amikor egy erőforrás állapota frissül (ha nem történik frissítés, a rendszer nem küldi el az adatküldést).
- **Pillanatképek** – az összes megfelelőségi értékelés aktuális állapotának pillanatképét hetente küldi el a rendszer (ez egy előzetes verziójú funkció a biztonságos pontszámok és a szabályozási megfelelőségi adatok heti pillanatképének elkészítéséhez).

További információ a funkció teljes képességeiről a Security Center- [adat folyamatos exportálásával](continuous-export.md)

## <a name="december-2020"></a>2020. december

A decemberi frissítések a következők:

- [Általánosan elérhető az Azure Defender az SQL-kiszolgálókhoz a gépeken](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Általánosan elérhető az Azure Defender for SQL-támogatás az Azure szinapszis Analytics dedikált SQL-készletéhez](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [A globális rendszergazdák mostantól bérlői szintű engedélyeket is biztosíthatnak](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Két új Azure Defender-csomag: az Azure Defender for DNS és az Azure Defender for Resource Manager (előzetes verzió)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Új biztonsági riasztások oldal a Azure Portal (előzetes verzió)](#new-security-alerts-page-in-the-azure-portal-preview)
- [A Azure SQL Database & SQL felügyelt példányának revitalizáló Security Center felülete](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Tárgyieszköz-leltári eszközök és szűrők frissítve](#asset-inventory-tools-and-filters-updated)
- [Javaslatok az SSL-tanúsítványokat kérő webalkalmazásokról, amelyek már nem részei a biztonságos pontszámnak](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [A javaslatok oldalon új szűrők találhatók a környezethez, a súlyossághoz és a rendelkezésre álló válaszokhoz](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [A folyamatos exportálás új adattípusokat és továbbfejlesztett deployifnotexist szabályzatokat kap](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Általánosan elérhető az Azure Defender az SQL-kiszolgálókhoz a gépeken

Azure Security Center két Azure Defender-csomagot kínál az SQL Serverhez:

- Azure **Defender Azure SQL Database-kiszolgálókhoz** – védi az Azure-beli natív SQL-kiszolgálókat 
- **Azure Defender a gépeken futó SQL Server-kiszolgálókon** – a hibrid, többfelhős és helyszíni környezetekben az SQL-kiszolgálókon ugyanazok a védelem kiterjeszthető.

Ebben a közleményben az **Azure Defender for SQL** mostantól védi az adatbázisait és az adataikat, bárhol is legyenek.

Az SQL-hez készült Azure Defender biztonsági rések felmérésére szolgáló képességeket tartalmaz. A sebezhetőség-felmérési eszköz a következő speciális funkciókat tartalmazza:

- **Alapkonfiguráció** (új!) – a biztonsági rések vizsgálatának eredményét intelligensen finomíthatja azokon, amelyek valós biztonsági problémákat jelenthetnek. Miután létrehozta az alapkonfiguráció biztonsági állapotát, a sebezhetőség-felmérési eszköz csak az eredeti állapottól származó eltéréseket jelenti. Az alapkonfigurációnak megfelelő eredmények a következő vizsgálatoknak minősülnek. Ez lehetővé teszi, hogy Ön és az elemzők a fontos dolgokra összpontosítsanak.
- **Részletes teljesítményteszt-információk** , amelyek segítenek *megérteni* a felderített megállapításokat, és hogy miért kapcsolódnak az erőforrásokhoz.
- **Szervizelési parancsfájlok** , amelyek segítenek az azonosított kockázatok enyhítésében.

További információ [Az Azure Defender for SQL szolgáltatásról](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Általánosan elérhető az Azure Defender for SQL-támogatás az Azure szinapszis Analytics dedikált SQL-készletéhez

Az Azure szinapszis Analytics (korábbi nevén SQL DW) egy olyan elemzési szolgáltatás, amely egyesíti a vállalati adattárházat és a big data elemzéseket. A dedikált SQL-készletek az Azure szinapszis vállalati adattárház-szolgáltatásai. További információ: [Mi az az Azure szinapszis Analytics (korábban SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Az SQL-hez készült Azure Defender az alábbiakkal védi a dedikált SQL-készleteket:

- Komplex **veszélyforrások elleni védelem** a fenyegetések és a támadások észlelése érdekében 
- **Sebezhetőségi felmérési képességek** a biztonsági konfigurációs beállítások azonosításához és szervizeléséhez

Az Azure Defender for SQL az Azure szinapszis Analytics SQL-készletekhez való támogatását automatikusan hozzáadja az Azure SQL Databases csomaghoz Azure Security Center. A Azure Portal a szinapszis munkaterület lapján egy új "Azure Defender for SQL" lapot talál.

További információ [Az Azure Defender for SQL szolgáltatásról](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>A globális rendszergazdák mostantól bérlői szintű engedélyeket is biztosíthatnak

A **globális rendszergazda** Azure Active Directory szerepkörrel rendelkező felhasználó bérlői szintű felelősséggel rendelkezhet, de nem rendelkezik az Azure-engedélyekkel a szervezetre vonatkozó információk megtekintésére a Azure Security Centerban. 

A bérlői szintű engedélyek kiosztásához kövesse a [bérlői szintű engedélyek megadása saját magának](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself)című témakör utasításait.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Két új Azure Defender-csomag: az Azure Defender for DNS és az Azure Defender for Resource Manager (előzetes verzió)

Az Azure-környezethez két új, felhőben natív, széles körű veszélyforrások elleni védelmi képességgel bővült.

Ezek az új védelem nagy mértékben fokozza rugalmasságát a veszélyforrások elleni támadásokkal szemben, és jelentősen növeli az Azure Defender által védett Azure-erőforrások számát.

- **Azure Defender for Resource Manager** – a szervezeten belül végrehajtott összes erőforrás-kezelési művelet automatikus figyelése. További információkért lásd:
    - [A Resource Managerhez készült Azure Defender bemutatása](defender-for-resource-manager-introduction.md)
    - [Válaszadás a Resource Managerhez készült Azure Defender-riasztásokra](defender-for-resource-manager-usage.md)
    - [Az Azure Defender for Resource Manager által biztosított riasztások listája](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender DNS-ben** – folyamatosan figyeli az Azure-erőforrások összes DNS-lekérdezését. További információkért lásd:
    - [A DNS-hez készült Azure Defender bemutatása](defender-for-dns-introduction.md)
    - [Válaszadás a DNS-hez készült Azure Defender-riasztásokra](defender-for-dns-usage.md)
    - [Az Azure Defender által a DNS-hez biztosított riasztások listája](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Új biztonsági riasztások oldal a Azure Portal (előzetes verzió)

A Azure Security Center biztonsági riasztások lapja az alábbiak biztosítására lett újratervezve:

- **Jobb osztályozási élmény a riasztásokhoz** – segít csökkenteni a riasztások fáradtságát, és könnyebben koncentrálni a legfontosabb fenyegetésekre, a lista tartalmaz testreszabható szűrőket és csoportosítási lehetőségeket.
- **További információk a riasztások listájában** – például a MITRE ATT&ACK-taktika
- A riasztások **létrehozásának gombja** – az Azure Defender képességeinek kiértékeléséhez és a riasztások konfigurációjának teszteléséhez (Siem-integráció, e-mail-értesítések és munkafolyamat-automatizálások esetén) létrehozhat minta-riasztásokat az összes Azure Defender-csomagból.
- Az **Azure Sentinel incidensekkel való összehangolása** – a két terméket használó ügyfelek esetében a váltás mostantól egyszerűbb, és könnyen megtanulható egy másik
- **Nagyobb teljesítmény** a nagyméretű riasztások listája esetén
- A riasztások listáján a **billentyűzetes Navigálás**
- **Riasztások az Azure Resource Graph-ból** – a riasztásokat lekérdezheti az Azure Resource Graph-ban, a Kusto API-t az összes erőforráshoz. Ez akkor is hasznos, ha saját riasztási irányítópultokat épít ki. [További információ az Azure Resource Graph-ról](../governance/resource-graph/index.yml).

Az új felület eléréséhez használja a "kipróbálás most" hivatkozást a biztonsági riasztások oldal tetején található szalagcímből.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Az új előzetes riasztási élményre mutató hivatkozást tartalmazó szalagcím":::

A riasztások új felhasználói élményből való létrehozásával kapcsolatban lásd: [minta Azure Defender-riasztások](security-center-alert-validation.md#generate-sample-azure-defender-alerts)létrehozása.


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>A Azure SQL Database & SQL felügyelt példányának revitalizáló Security Center felülete 

Az SQL-en belüli Security Center élmény hozzáférést biztosít a következő Security Center és az Azure Defender for SQL-funkciókhoz:

- **Biztonsági javaslatok** – a Security Center rendszeresen elemzi az összes csatlakoztatott Azure-erőforrás biztonsági állapotát, hogy azonosítsa a lehetséges biztonsági hibás konfigurációkat. Ezután javaslatokat tesz a biztonsági rések orvoslására és a szervezetek biztonsági helyzetének javítására.
- **Biztonsági riasztások** – olyan észlelési szolgáltatás, amely folyamatosan figyeli az Azure SQL-tevékenységeket olyan fenyegetésekkel szemben, mint például az SQL-injektálás, a találgatásos támadás és a jogosultságok elleni visszaélés. Ez a szolgáltatás részletes és működés közbeni biztonsági riasztásokat indít a Security Centerban, és lehetőségeket biztosít az Azure Sentinel, a Microsoft Azure-natív SIEM megoldásának folytatására.
- **Megállapítások** – sebezhetőségi felmérési szolgáltatás, amely folyamatosan figyeli az Azure SQL-konfigurációkat, és segít elhárítani a biztonsági réseket. Az értékelési vizsgálatok áttekintést nyújtanak az Azure SQL biztonsági állapotáról, valamint részletes biztonsági eredményeket tartalmaznak.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="A Azure Security Center SQL-alapú biztonsági funkciói elérhetők az Azure SQL-en belül":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Tárgyieszköz-leltári eszközök és szűrők frissítve

A Azure Security Center leltár lapja a következő változásokkal frissült:

- Az eszköztárhoz hozzáadott **útmutatók és visszajelzések** . Ekkor megnyílik egy ablaktábla, amely a kapcsolódó információkra és eszközökre mutató hivatkozásokat tartalmaz. 
- Az **előfizetések szűrő** az erőforrások számára elérhető alapértelmezett szűrőkhöz lett hozzáadva.
- A **lekérdezési hivatkozás megnyitása** az aktuális szűrési beállítások Azure Resource Graph-lekérdezésként való megnyitásához (korábbi nevén "View in Resource Graph Explorer").
- Az egyes szűrők **operátori beállításai** . Most több logikai operátor közül választhat, amelyek nem a "=". Előfordulhat például, hogy az összes olyan erőforrást meg kívánja találni, amelynek a címei tartalmazzák a "titkosítás" karakterláncot. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Az Asset Inventory szűrők operátori beállításának vezérlői":::

További információ a leltárban az [erőforrások az eszközök leltározásával való megismeréséhez és kezeléséhez](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Javaslatok az SSL-tanúsítványokat kérő webalkalmazásokról, amelyek már nem részei a biztonságos pontszámnak

A "Web Apps szolgáltatásnak az összes bejövő kérelemhez SSL-tanúsítványt kell igényelnie" a biztonsági vezérlő a **hozzáférés és az engedélyek kezelése** (maximum 4 pont) értékre való áthelyezése után a biztonsági **bevált eljárások megvalósítására** (ami nem áll fenn pontok). 

Annak biztosítása, hogy egy webalkalmazás egy tanúsítványt kér, biztosan biztonságosabbá válik. A nyilvános webes alkalmazások esetében azonban lényegtelen. Ha HTTP-n keresztül fér hozzá a webhelyhez, és nem HTTPS-kapcsolaton keresztül, akkor nem fog ügyféltanúsítványt kapni. Tehát ha az alkalmazáshoz Ügyféltanúsítványok szükségesek, akkor a HTTP-n keresztül nem engedélyezheti a kérelmeket az alkalmazásnak. További információ: a [TLS kölcsönös hitelesítésének konfigurálása Azure app Servicehoz](../app-service/app-service-web-configure-tls-mutual-auth.md).

Ezzel a módosítással a javaslat mostantól javasolt ajánlott eljárás, amely nem befolyásolja a pontszámát. 

Ismerje meg, hogy mely javaslatok szerepelnek a [biztonsági ellenőrzésekben és javaslataikban](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>A javaslatok oldalon új szűrők találhatók a környezethez, a súlyossághoz és a rendelkezésre álló válaszokhoz

Azure Security Center figyeli az összes kapcsolódó erőforrást, és biztonsági javaslatokat hoz létre. Ezekkel az ajánlásokkal megerősítheti a hibrid Felhőbeli testtartást, és nyomon követheti a szervezet, az iparág és az ország szempontjából releváns szabályzatokat és szabványokat.

Ahogy Security Center továbbra is bővíti a lefedettségét és funkcióit, a biztonsági javaslatok listája havonta növekszik. Az [Azure biztonsági teljesítményteszt lefedettségének növeléséhez](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)például tekintse meg az előzetes verzióhoz hozzáadott 29. javaslatokat.

A növekvő listával meg kell szűrnie az ajánlásokat, hogy megtalálják a legfontosabb érdekeket. Novemberben hozzáadunk szűrőket a javaslatok oldalhoz (lásd a [javaslatok listája mostantól tartalmazza a szűrőket](#recommendations-list-now-includes-filters)).

Az ebben a hónapban hozzáadott szűrők lehetővé teszik a javaslatok listájának pontosítását az alábbiak szerint:

- **Környezet** – az AWS-, GCP-vagy Azure-erőforrásokra vonatkozó ajánlások megtekintése (vagy bármely kombináció)
- **Súlyosság** – a Security Center által meghatározott súlyossági besorolásnak megfelelő ajánlások megtekintése
- **Response Actions** – a javaslatok megtekintése a Security Center válaszának rendelkezésre állása alapján: gyors javítás, megtagadás és betartatás

    > [!TIP]
    > A Response Actions szűrő lecseréli az **elérhető gyors javítás (igen/nem)** szűrőt. 
    > 
    > További információ az egyes válaszok lehetőségeiről:
    > - [Gyors javítás szervizelése](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Helytelen konfigurációk megelőzése Kényszerítés/Megtagadás javaslatokkal](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Biztonsági ellenőrzés szerint csoportosított javaslatok" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>A folyamatos exportálás új adattípusokat és továbbfejlesztett deployifnotexist szabályzatokat kap

Azure Security Center folyamatos exportálási eszközei lehetővé teszik Security Center javaslatainak és riasztásoknak az exportálását a környezetében lévő más figyelési eszközökkel való használatra.

A folyamatos exportálással teljes mértékben testreszabhatja, hogy mi történjen az exportálásban, és hová fog menni. A részletekért lásd:  [Security Center adatok folyamatos exportálása](continuous-export.md).

Ezek az eszközök a következő módokon lettek kibővítve és kiterjesztve:

- **A folyamatos exportálás deployifnotexist-házirendjeinek továbbfejlesztése**. A szabályzatok most:

    - **Győződjön meg arról, hogy a konfiguráció engedélyezve van-e.** Ha nem, a szabályzat nem megfelelőként jelenik meg, és megfelelő erőforrást hoz létre. Azure Policy a [folyamatos exportálás beállításával](continuous-export.md#set-up-a-continuous-export)kapcsolatos további információkért tekintse meg a következő témakört: a "központi telepítés méretezése a Azure Policy lapon" című szakasz.

    - **A biztonsági eredmények exportálásának támogatása.** A Azure Policy-sablonok használatakor beállíthatja a folyamatos exportálást, hogy tartalmazza a megállapításokat. Ez akkor fontos, ha olyan javaslatok exportálására van szükség, amelyek "alárendelt" javaslatokkal rendelkeznek, például a sebezhetőségi felmérési képolvasók vagy a "szülő" javaslat rendszerfrissítéseinek adott rendszerfrissítései a számítógépekre telepíthetők.
    
    - **A biztonságos pontszámok adatexportálásának támogatása.**

- **A szabályzatok megfelelőségi felmérésének adatszolgáltatása (előzetes verzió).** Mostantól folyamatosan exportálhatja a frissítéseket a szabályozási megfelelőségi vizsgálatokra, beleértve az egyéni kezdeményezéseket Log Analytics munkaterületre vagy az Event hub-ra is. Ez a funkció nem érhető el a nemzeti/szuverén felhőkben.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="A szabályozásnak megfelelő értékelési információkra vonatkozó beállítások a folyamatos exportálási adatokkal.":::


## <a name="november-2020"></a>2020. november

A novemberi frissítések a következők:

- [29 előzetes javaslat hozzáadva az Azure biztonsági teljesítményteszt lefedettségének növeléséhez](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 hozzáadva a Security Center szabályozási megfelelőségi irányítópulthoz](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [A javaslatok listája mostantól szűrőket is tartalmaz](#recommendations-list-now-includes-filters)
- [Továbbfejlesztett és bővített automatikus üzembe helyezési élmény](#auto-provisioning-experience-improved-and-expanded)
- [A biztonságos pontszám már elérhető a folyamatos exportálásban (előzetes verzió)](#secure-score-is-now-available-in-continuous-export-preview)
- ["A rendszerfrissítéseket telepíteni kell a gépekre" javaslat mostantól magában foglalja az ajánlásokat](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [A Azure Portal házirend-kezelés lapja megjeleníti az alapértelmezett házirend-hozzárendelések állapotát.](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 előzetes javaslat hozzáadva az Azure biztonsági teljesítményteszt lefedettségének növeléséhez

Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, Azure-specifikus, a biztonsági és megfelelőségi irányelvek a közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. [További tudnivalók az Azure-biztonsági teljesítménytesztről](../security/benchmarks/introduction.md).

A jelen teljesítményteszt lefedettségének növeléséhez a következő 29 előzetes javaslat lett hozzáadva a Security Centerhoz.

Az előzetes verzióra vonatkozó javaslatok nem jelenítik meg az erőforrás állapotát, és nem tartoznak bele a biztonságos pontszám számításaiba. Ha lehetséges, javítsa őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul. További információ a javaslatokról a [Azure Security Centerban található javaslatok szervizelése](security-center-remediate-recommendations.md)című témakörben található.

| Biztonsági ellenőrzés                     | Új javaslatok                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adatforgalom titkosítása              | -Az SSL-kapcsolat érvényesítése engedélyezve kell legyen a PostgreSQL-adatbázis-kiszolgálókhoz<br>– Engedélyezni kell az SSL-kapcsolat betartatását a MySQL adatbázis-kiszolgálókon<br>-A TLS-t frissíteni kell az API-alkalmazás legújabb verziójára<br>-A TLS-t frissíteni kell a Function alkalmazás legújabb verziójára<br>– A TLS-t a webalkalmazás legújabb verziójára kell frissíteni<br>-FTPS szükséges az API-alkalmazásban<br>– A FTPS kötelező megadni a Function alkalmazásban<br>– A FTPS kötelező megadni a webalkalmazásban                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Hozzáférés és engedélyek kezelése        | – A webalkalmazásoknak SSL-tanúsítványt kell igényelnie minden bejövő kérelemhez<br>-Felügyelt identitást kell használni az API-alkalmazásban<br>-Felügyelt identitást kell használni a Function alkalmazásban<br>-Felügyelt identitást kell használni a webalkalmazásban                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Jogosulatlan hálózati hozzáférés korlátozása | -A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz<br>-A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon<br>-A privát végpontot engedélyezni kell a MySQL-kiszolgálókon                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Naplózás és naplózás engedélyezése          | – A App Services diagnosztikai naplóit engedélyezni kell                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Ajánlott biztonsági eljárások megvalósítása    | -Azure Backup engedélyezni kell a virtuális gépeket<br>-A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB<br>-A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL<br>-A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL<br>-A PHP-t frissíteni kell az API-alkalmazás legújabb verziójára<br>– A PHP-t a webalkalmazás legújabb verziójára kell frissíteni<br>– A Java-t az API-alkalmazás legújabb verziójára kell frissíteni<br>– A Java-t a Function app legújabb verziójára kell frissíteni<br>– A Java-t a webalkalmazás legújabb verziójára kell frissíteni<br>-A Pythont az API-alkalmazás legújabb verziójára kell frissíteni<br>-A Pythont a Function alkalmazás legújabb verziójára kell frissíteni<br>-A Pythont a webalkalmazás legújabb verziójára kell frissíteni<br>– Az SQL-kiszolgálók naplózási megőrzését legalább 90 napra kell beállítani |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Kapcsolódó hivatkozások:

- [További információ az Azure biztonsági teljesítménytesztről](../security/benchmarks/introduction.md)
- [További információ az Azure API apps szolgáltatásról](../app-service/app-service-web-tutorial-rest-api.md)
- [További információ az Azure Function apps szolgáltatásról](../azure-functions/functions-overview.md)
- [További információ az Azure Web Apps szolgáltatásról](../app-service/overview.md)
- [További információ a Azure Database for MariaDB](../mariadb/overview.md)
- [További információ a Azure Database for MySQL](../mysql/overview.md)
- [További információ a Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 hozzáadva a Security Center szabályozási megfelelőségi irányítópulthoz

A NIST SP 800-171 R2 standard mostantól beépített kezdeményezésként használható Azure Security Center szabályozási megfelelőségi irányítópultján való használatra. A vezérlők leképezéseit a [NIST SP 800-171 R2 szabályozási megfelelőség beépített kezdeményezésének részletes ismertetése](../governance/policy/samples/nist-sp-800-171-r2.md)ismerteti. 

A standard előfizetésekre való alkalmazásához és a megfelelőségi állapot folyamatos figyeléséhez használja a [szabványoknak a szabályozási megfelelőségi irányítópulton való testreszabására](update-regulatory-compliance-packages.md)vonatkozó utasításokat.

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="A NIST SP 800 171 R2 Standard Security Center szabályozási megfelelőségi irányítópultján":::

További információ erről a megfelelőségi szabványról: [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>A javaslatok listája mostantól szűrőket is tartalmaz

Mostantól szűrheti a biztonsági javaslatok listáját a feltételek körének megfelelően. A következő példában a javaslatok listáját a rendszer szűrte a következő javaslatok megjelenítéséhez:

- **általánosan elérhetők** (azaz nem előzetes verzió)
- a **Storage-fiókok** esetében
- **gyors javítási** szervizelés támogatása

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="A javaslatok listához tartozó szűrők":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Továbbfejlesztett és bővített automatikus üzembe helyezési élmény

Az automatikus kiépítési funkció segít csökkenteni a felügyeleti terhelést azáltal, hogy telepíti a szükséges bővítményeket az új és a meglévő Azure-beli virtuális gépekre, így azok a Security Center védelmére is kihasználhatják őket. 

Ahogy Azure Security Center nő, több bővítmény lett kifejlesztve, és Security Center képes figyelni az erőforrástípusok nagyobb listáját. Az automatikus kiépítési eszközök mostantól a Azure Policy képességeinek kihasználásával kibővültek más bővítmények és erőforrástípusok támogatásához.

Mostantól konfigurálhatja az automatikus kiépítés lehetőségeit:

- Log Analytics-ügynök
- Új A Kubernetes Azure Policy bővítménye
- Új Microsoft függőségi ügynök

További információ: [Azure Security Centerból származó automatikus kiépítési ügynökök és bővítmények](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>A biztonságos pontszám már elérhető a folyamatos exportálásban (előzetes verzió)

A biztonságos pontszám folyamatos exportálásával valós időben továbbíthatja a pontszám változásait az Azure Event Hubs vagy egy Log Analytics-munkaterületre. Ezt a képességet a következő célra használhatja:

- a biztonságos pontszám időbeli követése dinamikus jelentésekkel
- biztonságos pontszám-adatértékek exportálása az Azure Sentinelbe (vagy bármely más SIEM)
- integrálja ezeket az összes olyan folyamattal, amelyet esetleg már használ a biztonságos pontszám figyeléséhez a szervezetében

További információ a [Security Center adatainak folyamatos exportálásáról](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"A rendszerfrissítéseket telepíteni kell a gépekre" javaslat mostantól magában foglalja az ajánlásokat

A **rendszerfrissítéseket telepíteni kell a Machines** javaslatra. Az új verzió minden hiányzó frissítéshez tartalmaz aljavaslatokat, és a következő újításokat tartalmazza:

- Egy újratervezett élmény a Azure Portal Azure Security Center lapjain. A **rendszerfrissítésekre** vonatkozó ajánlás részletei lapon telepíteni kell a számítógépeken az alább látható megállapításokat tartalmazó listát. Egyetlen keresés kiválasztásakor megnyílik a részletek ablaktábla a Szervizelési információkra mutató hivatkozással és az érintett erőforrások listájával.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="A frissített javaslathoz tartozó, a portálon észlelt javaslatok egyikének megnyitása":::

- Dúsított adatok az Azure Resource Graph (ARG) javaslatához. Az ARG egy olyan Azure-szolgáltatás, amely hatékony erőforrás-feltárást tesz lehetővé. Az ARG használatával nagy léptékű lekérdezéseket végezhet az adott előfizetések között, így hatékonyan szabályozhatja a környezetét. 

    Azure Security Center esetében az ARG és a [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/) használatával kérdezheti le a biztonsági testhelyzetek széles körét.

    Korábban, ha a jelen ajánlást az ARG-ben kérdezte le, az egyetlen elérhető információ az volt, hogy a javaslatot szervizelni kell egy gépen. A továbbfejlesztett verzió következő lekérdezése visszaküldi a hiányzó rendszerfrissítéseket a számítógép szerint csoportosítva.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>A Azure Portal házirend-kezelés lapja megjeleníti az alapértelmezett házirend-hozzárendelések állapotát.

Most már megtekintheti, hogy az előfizetések rendelkeznek-e az alapértelmezett Security Center házirend hozzárendelésével a Azure Portal Security Center **biztonsági házirend** lapján.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Az alapértelmezett szabályzat-hozzárendeléseket megjelenítő Azure Security Center házirend-kezelési lapja":::

## <a name="october-2020"></a>2020. október

Az októberi frissítések a következők:
- [Biztonsági rések felmérése helyszíni és többfelhős gépekhez (előzetes verzió)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall javaslat hozzáadva (előzetes verzió)](#azure-firewall-recommendation-added-preview)
- [Az illetékes IP-tartományokat a gyors javítással frissített Kubernetes Services-javaslatban kell meghatározni.](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [A szabályozási megfelelőségi irányítópult mostantól tartalmazza a szabványok eltávolításának lehetőségét](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft. Security/securityStatuses tábla eltávolítva az Azure Resource Graphből (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Biztonsági rések felmérése helyszíni és többfelhős gépekhez (előzetes verzió)

[Az Azure Defender for Servers](defender-for-servers-introduction.md)beépített sebezhetőség-felmérési ellenőrzőeszköze (Qualys-alapú) mostantól megvizsgálja az Azure arc-kompatibilis kiszolgálókat.

Ha engedélyezte az Azure arc használatát a nem Azure-beli gépeken, Security Center az integrált biztonsági rések képolvasó üzembe helyezését teszi lehetővé manuálisan és méretezéssel.

Ezzel a frissítéssel kihasználhatja az **Azure Defender-kiszolgálók** erejét, hogy megszilárdítsa a sebezhetőségi kezelési programot az összes Azure-beli és nem Azure-beli eszközön.

Főbb képességek:

- A VA (sebezhetőségi felmérés) képolvasó üzembe helyezési állapotának figyelése az Azure arc-gépeken
- Az integrált VA-ügynök kiépítés a nem védett Windows-és Linux Azure-alapú arc-gépekre (manuálisan és nagy méretekben)
- Észlelt biztonsági rések fogadása és elemzése az üzembe helyezett ügynököktől (manuálisan és nagy méretben)
- Az Azure-beli virtuális gépek és az Azure arc-számítógépek egységes felhasználói felülete

[További információ az integrált sebezhetőségi képolvasó hibrid gépekre való üzembe helyezéséről](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[További információ az Azure arc használatára képes kiszolgálókról](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall javaslat hozzáadva (előzetes verzió)

Új javaslat lett hozzáadva az összes virtuális hálózat Azure Firewall-mel való ellátásához.

Javasoljuk, hogy a **virtuális hálózatokat a Azure Firewall védelemmel** lássa el, hogy korlátozza a hozzáférést a virtuális hálózatokhoz, és megakadályozza a potenciális fenyegetéseket Azure Firewall használatával.

További információ a [Azure Firewallról](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Az illetékes IP-tartományokat a gyors javítással frissített Kubernetes Services-javaslatban kell meghatározni.

Az ajánlott **IP-címtartományok meghatározása a Kubernetes Services szolgáltatásban** most már rendelkezik egy gyors javítási lehetőséggel.

További információ erről a javaslatról és az összes többi Security Center javaslatról: [biztonsági javaslatok – útmutató](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="A hitelesítő IP-tartományokat a Kubernetes Services javaslatban kell meghatározni a gyors javítás lehetőséggel":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>A szabályozási megfelelőségi irányítópult mostantól tartalmazza a szabványok eltávolításának lehetőségét

A Security Center szabályozási megfelelőségi irányítópultja betekintést nyújt a megfelelőségi állapotba, és megfelel a megfelelőségi ellenőrzéseknek és követelményeknek.

Az irányítópult a szabályozási szabványok alapértelmezett készletét tartalmazza. Ha a megadott szabványok bármelyike nem felel meg a szervezete számára, mostantól egy egyszerű folyamat távolítja el őket a felhasználói felületről egy előfizetéshez. A szabványokat csak az *előfizetés* szintjén lehet eltávolítani; a felügyeleti csoport hatóköre nem.

További információ: [standard eltávolítása az irányítópultról](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft. Security/securityStatuses tábla eltávolítva az Azure Resource Graphből (ARG)

Az Azure Resource Graph egy Azure-szolgáltatás, amelynek célja, hogy hatékony erőforrás-feltárást biztosítson, amely lehetővé teszi az adott előfizetések skálájának lekérdezését, így hatékonyan szabályozhatja a környezetét. 

Azure Security Center esetében az ARG és a [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/) használatával kérdezheti le a biztonsági testhelyzetek széles körét. Például:

- Eszközök leltározása (ARG)
- Dokumentáltak egy példa ARG-lekérdezést a [fiókok azonosításához a többtényezős hitelesítés (MFA) engedélyezése nélkül](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Az ARG-n belül vannak olyan adattáblák, amelyek a lekérdezésekben használhatók.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Az Azure Resource Graph Explorer és a rendelkezésre álló táblák":::

> [!TIP]
> Az ARG dokumentációja felsorolja az összes elérhető táblázatot az [Azure Resource Graph-táblában és az erőforrástípus-referenciában](../governance/resource-graph/reference/supported-tables-resources.md).

Ebből a frissítésből a **Microsoft. Security/securityStatuses** tábla el lett távolítva. A securityStatuses API továbbra is elérhető.

Az adatcsere a Microsoft. Security/Assessments tábla használatával végezhető el.

A Microsoft. Security/securityStatuses és a Microsoft. Security/Assessments között a legfontosabb különbség az, hogy míg az első az értékelések összesítését mutatja, a másodpercek mindegyike egyetlen rekorddal rendelkezik.

Például a Microsoft. Security/securityStatuses egy olyan eredményt ad vissza, amely két policyAssessments tömbjét eredményezi:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Míg a Microsoft. Security/Assessments egy rekordot tart fenn az egyes házirend-értékelésekhez a következőképpen:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Példa egy meglévő ARG-lekérdezés securityStatuses használatával történő átalakítására az értékelés táblázatának használatával:**

SecurityStatuses hivatkozó lekérdezés:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Az értékelések táblázatának helyettesítő lekérdezése:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

További információt a következő hivatkozásokon talál:
- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)
- [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020. szeptember

A szeptemberben elérhető frissítések a következők:
- [Security Center új arculatot kap!](#security-center-gets-a-new-look)
- [Az Azure Defender megjelent](#azure-defender-released)
- [Általánosan elérhető az Azure Defender for Key Vault](#azure-defender-for-key-vault-is-generally-available)
- [Általánosan elérhető az Azure Defender a fájlok és ADLS Gen2 tárolásának védelméhez](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Az Asset Inventory Tools mostantól általánosan elérhető](#asset-inventory-tools-are-now-generally-available)
- [A tároló-beállításjegyzék és a virtuális gépek vizsgálatára szolgáló adott sebezhetőség letiltása](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Erőforrás kivonása egy javaslatból](#exempt-a-resource-from-a-recommendation)
- [A Security Center AWS-és GCP-összekötők több felhős élményt nyújtanak](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes munkaterhelés-védelmi javaslat csomag](#kubernetes-workload-protection-recommendation-bundle)
- [A sebezhetőségi felmérés eredményei mostantól folyamatos exportálással érhetők el](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Biztonsági konfigurációs problémák megakadályozása az új erőforrások létrehozásakor feltett javaslatok végrehajtásakor](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [A hálózati biztonsági csoport javaslatai javultak](#network-security-group-recommendations-improved)
- [Elavult előnézeti AK-javaslat: "Pod biztonsági szabályzatokat kell meghatározni a Kubernetes-szolgáltatásokban"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Azure Security Center továbbfejlesztett e-mail-értesítések](#email-notifications-from-azure-security-center-improved)
- [A biztonságos pontszám nem tartalmazza az előzetes verzióra vonatkozó javaslatokat](#secure-score-doesnt-include-preview-recommendations)
- [A javaslatok mostantól a súlyossági mutatót és a frissességi időközt is tartalmazzák](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center új arculatot kap!

Megjelent egy frissített felhasználói felület a Security Center portál oldalain. Az új lapok közé tartozik egy új áttekintő oldal és irányítópult a biztonságos pontszám, az eszközök leltározása és az Azure Defender számára.

Az átalakított áttekintő oldal mostantól tartalmaz egy csempét a biztonságos pontszám, a tárgyieszköz-leltár és az Azure Defender-irányítópultok eléréséhez. Emellett egy csempe is kapcsolódik a szabályozási megfelelőségi irányítópulthoz.

További információ az [áttekintő oldalról](overview-page.md).


### <a name="azure-defender-released"></a>Az Azure Defender megjelent

Az **Azure Defender** a felhőalapú munkaterhelés-védelmi platform (CWPP), amely a fejlett, intelligens, Azure-és hibrid számítási feladatok védelméhez Security Centeron belül integrált. Lecseréli Security Center Standard díjszabási csomagját. 

Amikor engedélyezi az Azure Defender számára a Azure Security Center **díjszabási és beállítási** területét, a következő Defender-csomagok egyidejűleg engedélyezve vannak, és átfogó védelmet biztosítanak a környezet számítási, adatés szolgáltatási rétegei számára:

- [Azure Defender kiszolgálókhoz](defender-for-servers-introduction.md)
- [Azure Defender App Service-hez](defender-for-app-service-introduction.md)
- [Azure Defender tároláshoz](defender-for-storage-introduction.md)
- [Azure Defender SQL-hez](defender-for-sql-introduction.md)
- [Azure Defender Key Vaulthoz](defender-for-key-vault-introduction.md)
- [Azure Defender Kuberneteshez](defender-for-kubernetes-introduction.md)
- [Azure Defender tárolóregisztrációs adatbázisokhoz](defender-for-container-registries-introduction.md)

Ezeket a csomagokat a Security Center dokumentációjában külön ismertetjük.

A dedikált irányítópulttal az Azure Defender biztonsági riasztásokat és komplex veszélyforrások elleni védelmet biztosít a Virtual Machines, az SQL Database, a containers, a web Applications, a Network és más rendszerekhez.

[További információ az Azure Defenderről](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Általánosan elérhető az Azure Defender for Key Vault

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. 

Az **Azure Defender for Key Vault** biztosítja az Azure-natív, komplex veszélyforrások elleni védelmet a Azure Key Vault számára, amely további biztonsági intelligenciát biztosít. A bővítmények által Key Vault Azure Defender a Key Vault-fiókoktól függő számos erőforrás védelmét is védi.

A választható csomag már a GA. Ez a szolgáltatás előzetes verzióként érhető el, mivel "komplex veszélyforrások elleni védelem Azure Key Vault".

Emellett a Azure Portal Key Vault lapjain szerepel egy dedikált **biztonsági** oldal is **Security Center** javaslatok és riasztások számára.

További információ: [Key Vault Azure Defender](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Általánosan elérhető az Azure Defender a fájlok és ADLS Gen2 tárolásának védelméhez 

Az **Azure Defender for Storage** észleli az Azure Storage-fiókokban potenciálisan ártalmas tevékenységeket. Az Ön adatai védetté tehetik, hogy blob-tárolóként, fájlmegosztásként vagy adattavakként vannak tárolva.

A [Azure Files](../storage/files/storage-files-introduction.md) és [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) támogatása már általánosan elérhető.

2020. október 1-től kezdődően megkezdjük a szolgáltatások erőforrásainak védelmét.

További információ az [Azure Defender for Storage szolgáltatásban](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Az Asset Inventory Tools mostantól általánosan elérhető

Azure Security Center eszközök leltározási lapja egyetlen oldalt biztosít a Security Centerhoz csatlakoztatott erőforrások biztonsági állapotának megtekintéséhez.

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldására.

Ha bármilyen erőforráshoz függőben lévő javaslatok vannak, akkor azok megjelennek a leltárban.

További információ: [erőforrások felderítése és kezelése az eszközök leltározásával](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>A tároló-beállításjegyzék és a virtuális gépek vizsgálatára szolgáló adott sebezhetőség letiltása

Az Azure Defender biztonsági réseket tartalmazó képolvasók a Azure Container Registry és a virtuális gépek rendszerképeinek vizsgálatára is lehetőséget biztosítanak.

Ha a szervezetnek figyelmen kívül kell hagynia egy megállapítást, és nem javítja azt, akkor letilthatja. A letiltott eredmények nem befolyásolják a biztonságos pontszámot, vagy nem eredményeznek nemkívánatos zajt.

Ha a keresés megfelel a letiltási szabályokban definiált feltételeknek, az nem jelenik meg az eredmények listájában.

Ez a lehetőség a javaslatok részleteinek oldalain érhető el:

- **A Azure Container Registry lemezképek biztonsági réseit szervizelni kell**
- **A virtuális gépek biztonsági réseit szervizelni kell**

További információ a [tároló-lemezképek konkrét megállapításainak letiltásával](defender-for-container-registries-usage.md#disable-specific-findings-preview) és [a virtuális gépek konkrét megállapításainak letiltásával](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)kapcsolatban.


### <a name="exempt-a-resource-from-a-recommendation"></a>Erőforrás kivonása egy javaslatból

Időnként előfordulhat, hogy egy erőforrás nem megfelelőként jelenik meg egy konkrét javaslatra vonatkozóan (és így csökkenti a biztonságos pontszámát), még akkor is, ha úgy érzi, hogy nem lenne. Lehetséges, hogy a Security Center által nem követett folyamat szervizelte. Vagy lehet, hogy a szervezete úgy döntött, hogy elfogadja az adott erőforrás kockázatát. 

Ilyen esetekben létrehozhat egy kivételi szabályt, és gondoskodhat arról, hogy az erőforrás ne szerepeljen a nem megfelelő állapotú erőforrások között a jövőben. Ezek a szabályok az alább leírtak szerint dokumentált indoklásokat is tartalmazhatnak.

További információ: az erőforrások kizárása a [javaslatokból és a biztonságos pontszámból](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>A Security Center AWS-és GCP-összekötők több felhős élményt nyújtanak

A Felhőbeli számítási feladatok gyakran több felhőalapú platformra is kiterjednek, a Cloud Security servicesnek ugyanezt kell tennie.

A Azure Security Center mostantól védi a munkaterheléseket az Azure-ban, az Amazon Web Services (AWS) és a Google Cloud Platformban (GCP).

Az AWS-és GCP-fiókok Security Centerba való bevezetésével integrálható az AWS biztonsági központ, a GCP biztonsági parancs és a Azure Security Center. 

További információt az [AWS-fiókok Összekapcsolásával Azure Security Center](quickstart-onboard-aws.md) és [a GCP-fiókok Azure Security Centerhoz való összekapcsolásával](quickstart-onboard-gcp.md)kapcsolatban itt olvashat.


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes munkaterhelés-védelmi javaslat csomag

Annak biztosítása érdekében, hogy a Kubernetes-munkaterhelések alapértelmezés szerint biztonságosak legyenek, Security Center Kubernetes-szint megerősítő javaslatokat ad hozzá, beleértve a Kubernetes-belépésvezérlés használatával történő kényszerítési lehetőségeket is.

Ha telepítette a Kubernetes Azure Policy bővítményét az AK-fürtön, a Kubernetes API-kiszolgálónak küldött összes kérést a rendszer a fürtön megőrzött ajánlott eljárások alapján figyeli. Ezután konfigurálhatja az ajánlott eljárások betartatását és a jövőbeli munkaterhelések megadását.

Megadhatja például, hogy az emelt szintű tárolók ne legyenek létrehozva, és minden jövőbeli kérelem le lesz tiltva.

További információ a [munkaterhelések elleni védelemben – ajánlott eljárások a Kubernetes belépésvezérlés használatával](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>A sebezhetőségi felmérés eredményei mostantól folyamatos exportálással érhetők el

A folyamatos exportálással valós időben továbbíthatja a riasztásokat és a javaslatokat az Azure Event Hubsba, Log Analytics munkaterületekre vagy Azure Monitorokra. Innen az adatok integrálása SIEM-ekkel (például Azure Sentinel, Power BI, Azure Adatkezelő stb.) végezhető el.

Security Center integrált sebezhetőségi felmérési eszközei a "szülő" javaslaton belül, például "a virtuális gépek biztonsági rései" javításával kapcsolatos ajánlott ajánlásokat adnak vissza az erőforrásokra vonatkozóan. 

A biztonsági megállapítások mostantól a folyamatos exportálással is elérhetők, amikor kiválasztja az ajánlások lehetőséget, és engedélyezi a **biztonsági megállapítások belefoglalása** lehetőséget.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Biztonsági megállapítások bekapcsolása a folyamatos exportálási konfigurációban" :::

Kapcsolódó lapok:

- [Security Center az Azure Virtual Machines szolgáltatáshoz készült integrált sebezhetőségi felmérési megoldás](deploy-vulnerability-assessment-vm.md)
- [Security Center integrált sebezhetőségi felmérési megoldás Azure Container Registry rendszerképekhez](defender-for-container-registries-usage.md)
- [Folyamatos exportálás](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Biztonsági konfigurációs problémák megakadályozása az új erőforrások létrehozásakor feltett javaslatok végrehajtásakor

A biztonsági incidensek jelentős oka a biztonság. Security Center mostantól lehetővé teszi az új erőforrások helytelen konfigurációjának *megelőzését* az adott javaslatok tekintetében. 

Ez a funkció segít megőrizni a számítási feladatokat, és stabilizálni tudja a biztonságos pontszámot.

A biztonságos konfiguráció egy adott javaslat alapján történő érvényesítése két üzemmódban érhető el:

- A Azure Policy **megtagadásának** hatására leállíthatja a nem megfelelő állapotú erőforrásokat

- A **kikényszerítés** lehetőség használatával kihasználhatja az Azure-szabályzat **DeployIfNotExist** hatását, és a létrehozáskor automatikusan elháríthatja a nem megfelelő erőforrásokat.
 
Ez a kiválasztott biztonsági javaslatok esetében elérhető, és az erőforrás részletei lap tetején található.

További információ a [helytelen konfigurációkkal kapcsolatos kényszerítő/megtagadási javaslatok megelőzéséről](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>A hálózati biztonsági csoport javaslatai javultak

A hálózati biztonsági csoportokkal kapcsolatos következő biztonsági javaslatok javultak a téves pozitív események egyes példányainak csökkentése érdekében.

- Az összes hálózati portot korlátozni kell a virtuális géphez társított NSG
- A felügyeleti portokat be kell zárni a virtuális gépeken
- Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- Az alhálózatokat hálózati biztonsági csoporttal kell társítani


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Elavult előnézeti AK-javaslat: "Pod biztonsági szabályzatokat kell meghatározni a Kubernetes-szolgáltatásokban"

A "Pod biztonsági házirendek meghatározása a Kubernetes-szolgáltatásokban" előzetes javaslat az [Azure Kubernetes szolgáltatás](../aks/use-pod-security-policies.md) dokumentációjában leírtaknak megfelelően elavult.

A pod biztonsági házirend (előzetes verzió) szolgáltatás elavultnak van beállítva, és a továbbiakban nem lesz elérhető 2020. október 15-én, Azure Policy az AK-ban.

Miután a pod biztonsági házirend (előzetes verzió) elavult, le kell tiltania a szolgáltatást minden meglévő fürtön az elavult funkcióval a későbbi fürtök frissítéséhez és az Azure-támogatáson belüli tartózkodáshoz.


### <a name="email-notifications-from-azure-security-center-improved"></a>Azure Security Center továbbfejlesztett e-mail-értesítések

A biztonsági riasztásokkal kapcsolatos e-mailek következő területei javultak: 

- Lehetőség van a riasztásokról értesítő e-mailek küldésére az összes súlyossági szinthez.
- Lehetőség van arra, hogy az előfizetésen keresztül különböző Azure-szerepkörökkel tájékoztassa a felhasználókat
- Alapértelmezés szerint az előfizetések tulajdonosait proaktívan értesítjük a nagy súlyosságú riasztásokról (amelyek nagy valószínűséggel valódi behatolást okoznak)
- Eltávolította a telefonszám mezőt az e-mail értesítések konfigurációs oldaláról.

További információt a [biztonsági riasztások e-mail értesítéseinek beállítása](security-center-provide-security-contact-details.md)című témakörben olvashat.


### <a name="secure-score-doesnt-include-preview-recommendations"></a>A biztonságos pontszám nem tartalmazza az előzetes verzióra vonatkozó javaslatokat 

Security Center folyamatosan felméri az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémákra. Ezután összesíti az összes megállapítást egyetlen pontszámba, így eldöntheti, hogy az aktuális biztonsági helyzet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint.

Mivel új fenyegetések észlelhetők, új biztonsági tanácsokat Security Center új javaslatok keretében elérhetővé tesznek. Ha el szeretné kerülni, hogy a meglepetések ne legyenek a biztonságos pontszámban, és adjon meg egy türelmi időszakot, amelyben az új ajánlásokat még a pontszámok befolyásolása előtt megtekintheti, az **előzetes** verzióként megjelölt javaslatok már nem szerepelnek a biztonságos pontszám kiszámítása során. Ha lehetséges, még mindig szervizelni kell őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul.

Emellett az **előzetes** verzióra vonatkozó javaslatok nem teszik lehetővé a "nem megfelelő" erőforrás megjelenítését.

Példa az előzetes verziójú javaslatra:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Javaslat az előnézet jelölővel":::

[További információ a biztonságos pontszámról](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>A javaslatok mostantól a súlyossági mutatót és a frissességi időközt is tartalmazzák

A javaslatok részleteit tartalmazó oldal mostantól tartalmazza a frissességi intervallum jelzőjét (ha van ilyen), valamint a javaslat súlyosságának egyértelmű megjelenítését.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="A frissességet és súlyosságot mutató javaslat oldal":::