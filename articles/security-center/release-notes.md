---
title: A Azure Security Center kibocsátási megjegyzései
description: A Azure Security Center újdonságait és változásait ismertető Leírás
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 81f741fd9b0e3d40eb0027a5cbe0ba4b7113bbea
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027618"
---
# <a name="whats-new-in-azure-security-center"></a>A Azure Security Center újdonságai

Security Center aktívan működik, és folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja az oldalt, amely az új funkciókkal, hibajavításokkal és elavult funkciókkal kapcsolatos információkat tartalmaz.

Ez az oldal gyakran frissül, ezért gyakran érdemes újra felkeresni. 

Ha szeretne többet megtudni a Security Center hamarosan elérhető *tervezett* változásokról, tekintse meg [a Azure Security Center fontos jövőbeli változásait](upcoming-changes.md). 

> [!TIP]
> Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Security Center újdonságait](release-notes-archive.md).

## <a name="april-2021"></a>Április 2021

Az áprilisi frissítések a következők:
- [Négy új, a vendég konfigurációjával kapcsolatos javaslat (előzetes verzió)](#four-new-recommendations-related-to-guest-configuration-preview)
- [A Kubernetes Azure Defender használata a hibrid és a többfelhős Kubernetes-üzembe helyezések (előzetes verzió) elleni védelemhez](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [11 Azure Defender-riasztás elavult](#11-azure-defender-alerts-deprecated)
- [A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzés két javaslata elavult](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Négy új, a vendég konfigurációjával kapcsolatos javaslat (előzetes verzió)

Az Azure [vendég-konfigurációs bővítménye](../governance/policy/concepts/guest-configuration.md) jelentést készít a Security Center, így biztosítva, hogy a virtuális gépek vendégen belüli beállításai megerősítve legyenek. Nincs szükség a bővítményre az ív-kompatibilis kiszolgálók esetében, mert az az ív csatlakoztatott számítógép ügynökének része. A kiterjesztéshez a számítógépen rendszer által felügyelt identitás szükséges.

Négy új javaslatot adtunk hozzá a bővítmények leghatékonyabban Security Centerához.

- Két javaslat arra kéri, hogy telepítse a bővítményt és a szükséges rendszer által felügyelt identitást:
    - **A vendég konfiguráció bővítményét telepíteni kell a gépekre**
    - **A Virtual Machines Guest Configuration bővítményt rendszerhez rendelt felügyelt identitással kell telepíteni**

- Ha a bővítmény telepítve van és fut, megkezdi a gépek naplózását, és a rendszer felszólítja, hogy megerősítse a beállításokat, például az operációs rendszer és a környezeti beállítások konfigurációját. Ez a két javaslat a Windows és a Linux rendszerű gépek megerősítését kéri az alábbiak szerint:
    - **A Windows Defender Exploit Guard-nek engedélyezve kell lennie a gépeken**
    - **A Linux rendszerű gépek hitelesítéséhez SSH-kulcsokra van szükség**

További információ a [Azure Policy vendég konfigurációjának megismeréséhez](../governance/policy/concepts/guest-configuration.md).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>A Kubernetes Azure Defender használata a hibrid és a többfelhős Kubernetes-üzembe helyezések (előzetes verzió) elleni védelemhez

A Kubernetes készült Azure Defender kibővíti a fenyegetések elleni védelmi képességeit, hogy bárhol is megvédjék a fürtöket. Ezt az [Azure arc-kompatibilis Kubernetes](../azure-arc/kubernetes/overview.md) és az új [bővítmények képességeinek](../azure-arc/kubernetes/extensions.md)integrálásával engedélyezték. 

Ha engedélyezte az Azure arc használatát a nem Azure-beli Kubernetes-fürtökön, akkor a Azure Security Center egy új javaslata arra, hogy csak néhány kattintással telepítse az Azure Defender-bővítményt rájuk.

Használja a javaslatot (az **Azure arc-kompatibilis Kubernetes-fürtökön telepítve kell lennie az Azure Defender bővítményének**) és a bővítménynek, hogy megvédje a más felhőalapú szolgáltatókon üzembe helyezett Kubernetes-fürtöket, de nem a felügyelt Kubernetes-szolgáltatásokban.

A Azure Security Center, az Azure Defender és az Azure arc-kompatibilis Kubernetes közötti integráció az alábbi előnyökkel jár:

- Az Azure Defender-bővítmény egyszerű üzembe helyezése az Azure arc-kompatibilis Kubernetes-fürtökön (manuálisan és nagy méretekben)
- Az Azure Defender bővítmény és az üzembe helyezési állapot monitorozása az Azure arc-portálról
- Az Security Center biztonsági javaslatai az Azure arc portál új biztonsági lapján jelennek meg
- Az Azure Defender által észlelt biztonsági fenyegetések az Azure arc portál új biztonsági lapján jelennek meg
- Az Azure arc-kompatibilis Kubernetes-fürtök integrálva vannak a Azure Security Center platformba és felhasználói élménybe

További információ: az [Azure Defender használata a helyszíni és a többfelhős Kubernetes-fürtökkel való Kubernetes](defender-for-kubernetes-azure-arc.md).

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center javaslata az Azure Defender-bővítmény Azure arc-kompatibilis Kubernetes-fürtökön való üzembe helyezéséhez." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender-riasztás elavult

Az alább felsorolt tizenegy Azure Defender-riasztás elavult.

- Az új riasztások felülírják ezt a két riasztást, és jobb lefedettséget biztosítanak:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | ELŐZETES verzió – a "Get-AzureDomainInfo" függvény futtatása |
    | ARM_MicroBurstRunbook    | ELŐZETES verzió – a "Get-AzurePasswords" függvény futtatása  |
    |                          |                                                                          |

- Ez a kilenc riasztás olyan Azure Active Directory Identity Protection-összekötőhöz (IPC) kapcsolódik, amely már elavult:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Szokatlan bejelentkezési tulajdonságok |
    | AnonymousLogin      | Névtelen IP-cím          |
    | InfectedDeviceLogin | Kártevők társított IP-címe     |
    | ImpossibleTravel    | Szokatlan utazás               |
    | MaliciousIP         | Kártékony IP-cím          |
    | LeakedCredentials   | Kiszivárgott hitelesítő adatok            |
    | PasswordSpray       | Jelszó spray                |
    | LeakedCredentials   | Azure AD-fenyegetések felderítése  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > Ezek a kilenc IPC-riasztás soha nem Security Center riasztásokat. A Azure Active Directory (HRE) Identity Protection-összekötő (IPC) részét képezik, amely Security Centernak küldi őket. Az elmúlt két évben csak azok az ügyfelek látják a riasztásokat, akik az exportálást (az összekötőtől az ASC-ig) konfigurálták a 2019-es vagy korábbi verziókban. A HRE IPC továbbra is saját riasztási rendszerekben jeleníti meg őket, és továbbra is elérhetőek voltak az Azure Sentinelben. Az egyetlen változás, hogy már nem jelennek meg Security Centerban.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzés két javaslata elavult 

A következő két javaslat elavult, és a módosítások némi hatással lehetnek a biztonságos pontszámra:

- **A számítógépeket újra kell indítani a rendszerfrissítések alkalmazásához**
- A **figyelési ügynököt telepíteni kell a gépekre**. Ez a javaslat csak a helyszíni gépekre vonatkozik, és a logikája egy másik javaslatba kerül át, **log Analytics az ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken**

Javasoljuk, hogy ellenőrizze a folyamatos exportálási és munkafolyamat-automatizálási konfigurációkat, és ellenőrizze, hogy ezek a javaslatok szerepelnek-e bennük. Emellett az azokat használó irányítópultokat vagy egyéb figyelési eszközöket ennek megfelelően kell frissíteni.

További információ ezekről a javaslatokról a [biztonsági javaslatok hivatkozását ismertető oldalon](recommendations-reference.md)található.


## <a name="march-2021"></a>Március 2021

A márciusi frissítések a következők:

- [Azure Firewall a Security Centerba integrált felügyelet](#azure-firewall-management-integrated-into-security-center)
- [Az SQL sebezhetőségi felmérés mostantól tartalmazza a "szabály letiltásának" élményét (előzetes verzió)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor Security Centerba integrált munkafüzetek és a megadott három sablon](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [A szabályozási megfelelőségi irányítópult mostantól tartalmazza az Azure audit-jelentéseket (előzetes verzió)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Az ajánlásokat az Azure Resource Graphban tekintheti meg az "Explore in ARG" használatával](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [A Munkafolyamat-automatizálás üzembe helyezésére vonatkozó szabályzatok frissítései](#updates-to-the-policies-for-deploying-workflow-automation)
- [Két örökölt javaslat már nem ír közvetlenül az Azure-tevékenység naplójába](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Javaslatok oldal továbbfejlesztései](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall a Security Centerba integrált felügyelet

A Azure Security Center megnyitásakor az Áttekintés oldalon megjelenik az első lap. 

Ez az interaktív irányítópult egységes nézetet biztosít a hibrid Felhőbeli számítási feladatok biztonsági állapotának kialakításához. Emellett a biztonsági riasztásokat, a lefedettségi adatokat és egyebeket is megjeleníti.

A biztonsági állapot központi felhasználói felületről való megtekintésének részeként a Azure Firewall Managert integráltuk ebbe az irányítópultba. Mostantól megtekintheti a tűzfal lefedettségi állapotát az összes hálózatban, és központilag kezelheti Azure Firewall házirendeket Security Centertól kezdve.

További információ az irányítópultról [Azure Security Center Áttekintés oldalán](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center áttekintő irányítópult a Azure Firewall csempével":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Az SQL sebezhetőségi felmérés mostantól tartalmazza a "szabály letiltásának" élményét (előzetes verzió)

A Security Center tartalmaz egy beépített sebezhetőségi képolvasót a potenciális adatbázis-sebezhetőségek felderítéséhez, nyomon követéséhez és szervizeléséhez. Az értékelés eredményei áttekintést nyújtanak az SQL-gépek biztonsági állapotáról, és ismertetik a biztonsági eredmények részleteit.

Ha a szervezetnek figyelmen kívül kell hagynia egy megállapítást, és nem javítja azt, akkor letilthatja. A letiltott eredmények nem befolyásolják a biztonságos pontszámot, vagy nem eredményeznek nemkívánatos zajt.

További információ az [egyes megállapítások letiltásával](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)kapcsolatban.



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor Security Centerba integrált munkafüzetek és a megadott három sablon

A Ignite Spring 2021-as részeként bejelentettük a Security Center integrált Azure Monitor munkafüzeteit.

Kihasználhatja az új integrációt, hogy megkezdje a beépített sablonok használatát Security Center galériájában. A munkafüzet-sablonok használatával dinamikus és vizuális jelentéseket hozhat létre a szervezet biztonsági helyzetének nyomon követéséhez. Emellett új munkafüzetek is létrehozhatók Security Center adatok vagy bármely más támogatott adattípus alapján, és gyorsan üzembe helyezhetők a közösségi munkafüzetek a Security Center GitHub-Közösségből.

Három sablon jelentés van megadva:

- **Biztonsági pontszám az idő múlásával** – nyomon követheti az előfizetések pontszámait és a javaslatok módosításait az erőforrásokra vonatkozóan
- **Rendszerfrissítések** – a hiányzó rendszerfrissítések megtekintése erőforrások, operációs rendszer, súlyosság és egyéb beállítások alapján
- **Sebezhetőségi felmérés eredményei** – megtekintheti az Azure-erőforrások sebezhetőségi vizsgálatának eredményeit

Ismerje meg, hogyan használhatja ezeket a jelentéseket, vagy hogyan [hozhat létre a saját, interaktív jelentések készítése Security Center adatairól](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Biztonságos pontszám időbeli jelentés":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>A szabályozási megfelelőségi irányítópult mostantól tartalmazza az Azure audit-jelentéseket (előzetes verzió)

A szabályozási megfelelőségi irányítópult eszköztárán mostantól letöltheti az Azure-és Dynamics-minősítési jelentéseket. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Szabályozási megfelelőségi irányítópult eszköztára":::

Kiválaszthatja a megfelelő jelentések típusát (PCI, SOC, ISO és egyebek), és szűrők használatával megkeresheti a szükséges konkrét jelentéseket.

További információ [a szabványok kezeléséről a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Az elérhető Azure-naplózási jelentések listájának szűrése":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Az ajánlásokat az Azure Resource Graphban tekintheti meg az "Explore in ARG" használatával

A javaslat részletei lapok mostantól tartalmazzák az "Explore az ARG-ben" eszköztárat. Ezzel a gombbal megnyithatja az Azure Resource Graph-lekérdezést, és megtekintheti, exportálhatja és megoszthatja az ajánlási adatforrásokat.

Az Azure Resource Graph (ARG) a Felhőbeli környezetekben az erőforrás-információkhoz való azonnali hozzáférést biztosít robusztus szűrési, csoportosítási és rendezési képességekkel. Az Azure-előfizetések programozott vagy a Azure Portalon keresztüli lekérdezésének gyors és hatékony módja.

További információ az [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)szolgáltatásról.

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Az ajánlott Azure Resource Graph-beli javaslatok megismerése.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>A Munkafolyamat-automatizálás üzembe helyezésére vonatkozó szabályzatok frissítései

A szervezet figyelési és incidens-reagálási folyamatainak automatizálása nagy mértékben növelheti a biztonsági incidensek kivizsgálásához és enyhítéséhez szükséges időt.

Három Azure Policy "DeployIfNotExist" szabályzatot biztosítunk, amelyek munkafolyamat-automatizálási eljárásokat hoznak létre és konfigurálnak, így a szervezeten belül üzembe helyezhetők az automatizálások:

|Cél  |Szabályzat  |Házirend-azonosító  |
|---------|---------|---------|
|Biztonsági riasztások munkafolyamat-automatizálása|[Azure Security Center-riasztásokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Munkafolyamat-automatizálás biztonsági javaslatokhoz|[Azure Security Center-javaslatokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Munkafolyamatok automatizálása szabályozási megfelelőségi változásokhoz|[Munkafolyamat-automatizálás üzembe helyezése Azure Security Center szabályozási megfelelőséghez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

A szabályzatok szolgáltatásainak két frissítése van:

- Ha hozzá van rendelve, a kényszerítéssel továbbra is engedélyezve lesznek.
- Mostantól testreszabhatja ezeket a házirendeket, és frissítheti a paramétereket még azután is, hogy azok már telepítve lettek. Ha például egy felhasználó hozzá szeretne adni egy másik értékelési kulcsot, vagy szerkeszteni kíván egy meglévő Assessment-kulcsot, azt megteheti.

Ismerkedés a [munkafolyamat-automatizálási sablonokkal](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

További információ a [Security Center eseményindítókkal kapcsolatos válaszok automatizálásáról](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Két örökölt javaslat már nem ír közvetlenül az Azure-tevékenység naplójába 

A Security Center szinte minden biztonsági javaslathoz átadja az adatot, hogy Azure Advisor, amely viszont azt írja az [Azure-tevékenység naplójába](../azure-monitor/essentials/activity-log.md).

Két javaslat esetében az adatgyűjtés egyidejűleg közvetlenül az Azure-tevékenység naplójában történik. Ezzel a módosítással a Security Center leállítja a korábbi biztonsági javaslatok adatírását közvetlenül a tevékenységi naplóba. Ehelyett exportáljuk az adatAzure Advisor, ahogy az összes többi javaslat esetében.

A két örökölt javaslat a következők:
- Az Endpoint Protection állapotával kapcsolatos problémákat fel kell oldani a gépeken
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell

Ha a következő két javaslathoz van hozzáférése a "TaskDiscovery típusú javaslat" kategóriában, akkor ez már nem érhető el.


### <a name="recommendations-page-enhancements"></a>Javaslatok oldal továbbfejlesztései 

Megjelent a javaslatok listájának továbbfejlesztett verziója, amely a további információkat mutatja be egy pillantással.

Most az oldalon láthatja:

1. Az egyes biztonsági vezérlők maximális pontszáma és aktuális pontszáma.
1. A címkéket helyettesítő ikonok, például a **gyors javítás** és az **előnézet**.
1. Egy új oszlop, amely az egyes javaslatokhoz kapcsolódó [házirend-kezdeményezést](security-policy-concept.md) jeleníti meg – akkor látható, ha a "Group By Controls" le van tiltva.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="A Azure Security Center ajánlásai oldalának továbbfejlesztése – március 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="A Azure Security Center &quot;Flat&quot; listára vonatkozó javaslatainak fejlesztései – március 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

További tudnivalókat a [Azure Security Center biztonsági javaslataiban](security-center-recommendations.md)olvashat.


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

További információ a [bérlői szintű engedélyek kérése, ha a tiéd nem elegendő](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


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

További információ a szolgáltatás teljes képességeiről [Security Center adatainak folyamatos exportálásával](continuous-export.md).

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
- **Azure Defender az SQL-kiszolgálókon a gépeken** – a hibrid, többfelhős és helyszíni környezetekben is kibővíti az SQL-kiszolgálókat.

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

A bérlői szintű engedélyek kiosztásához kövesse a [bérlői szintű engedélyek megadása saját magának](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)című témakör utasításait.


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