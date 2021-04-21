---
title: Kibocsátási megjegyzések a Azure Security Center
description: A description of what's new and changed in Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c47543fa1401a8da533a9af3ebe4554e405622d3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835531"
---
# <a name="whats-new-in-azure-security-center"></a>A Azure Security Center újdonsága

Security Center fejlesztés alatt áll, és folyamatosan kap fejlesztéseket. Hogy naprakész maradjon a legújabb fejlesztésekkel kapcsolatban, ez az oldal az új funkciókkal, hibajavításokkal és elavult funkciókkal kapcsolatos információkat tartalmaz.

Ez az oldal gyakran frissül, ezért gyakran újra meg kell jelenni. 

A hamarosan *várható* tervezett változásokról további Security Center [fontos](upcoming-changes.md)közelgő változásokat a Azure Security Center. 

> [!TIP]
> Ha hat hónapnál régebbi elemeket keres, azokat az újdonságok archívumában találja [Azure Security Center.](release-notes-archive.md)

## <a name="april-2021"></a>2021. április

Áprilisi frissítések:
- [A közelmúltban lekért tároló-beállításjegyzék-rendszerképek mostantól hetente újra vannak szkenzve (általánosan elérhető)](#recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability)
- [A Azure Defender for Kubernetes és a többfelhős Kubernetes-környezetek védelme (előzetes verzió)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [Javaslatok a dns Azure Defender- és Resource Manager engedélyezéséhez (előzetes verzió)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview)
- [Három szabályozási megfelelőségi szabványt adott hozzá: Azure CIS 1.3.0, CMMC Level 3 és Új-Zéland ISM Restricted](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Négy új javaslat a vendégkonfigurációval kapcsolatban (előzetes verzió)](#four-new-recommendations-related-to-guest-configuration-preview)
- [A CMK-javaslatok átkerültek az ajánlott eljárások biztonsági szabályozására](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender riasztások elavultak](#11-azure-defender-alerts-deprecated)
- [A "Rendszerfrissítések alkalmazása" biztonsági vezérlő két ajánlása elavult](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Azure Defender sql on machine csempe eltávolítása az Azure Defender irányítópultról](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)

### <a name="recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability"></a>A közelmúltban lekért tároló-beállításjegyzék-rendszerképek mostantól hetente újra vannak szkenzve (általánosan elérhető)

Azure Defender tárolóregisztrálókhoz beépített biztonsági réseket ellenőrző ellenőrzőeszközt is tartalmaz. Ez a szkenner azonnal átvizsgálja a regisztrációs adatbázisba leküldött összes képet és az elmúlt 30 napban lekért rendszerképeket.

Naponta új biztonsági réseket fedeznek fel. Ezzel a frissítéssel a beállításregisztrálókból az elmúlt 30 napban lekért tároló-rendszerképeket minden héten újra **el** fogjuk helyezni. Ez biztosítja, hogy az újonnan felfedezett biztonsági rések azonosíthatók a képeken.

A vizsgálat díja rendszerképenként történik, így ezekért az újravizsgálatokért nem számítunk fel további díjat.

További információ erről a [scannerről: Use Azure Defender for container registries to scan your images for](defender-for-container-registries-usage.md)vulnerabilities (Tárolóregisztrálók használata a tárolóregisztrálókhoz biztonsági rések kereséséhez a képeken).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>A Azure Defender for Kubernetes és a többfelhős Kubernetes-környezetek védelme (előzetes verzió)

Azure Defender for Kubernetes bővíti a fenyegetésvédelmi képességeit, hogy megvédhesse a fürtöt, bárhol is legyen az üzembe helyezésük. Ezt az engedélyezett [Kubernetes-Azure Arc és](../azure-arc/kubernetes/overview.md) új bővítményképességei [integrálásával engedélyezték.](../azure-arc/kubernetes/extensions.md) 

Ha engedélyezte az Azure Arc-t a nem Azure Kubernetes-fürtökön, az Azure Security Center új ajánlása szerint mindössze néhány kattintással üzembe helyezheti bennük az Azure Defender-bővítményt.

Használja a javaslatot (Azure Arc engedélyezett **Kubernetes-fürtökön** telepítve kell lennie a Azure Defender-bővítménynek) és a bővítménynek a más felhőszolgáltatóknál üzembe helyezett Kubernetes-fürtök védelmére, bár a felügyelt Kubernetes-szolgáltatásokon nem.

A kubernetes-Azure Security Center, Azure Defender és Azure Arc integráció a következőt hozza:

- A Azure Defender bővítmény egyszerű kiépítése Azure Arc kubernetes-fürtökhöz (manuálisan és nagy léptékben)
- Az Azure Defender bővítmény és kiépítési állapotának monitorozása a Azure Arc Portálról
- A biztonsági javaslatok Security Center a portál új Biztonsági oldalán Azure Arc meg
- A biztonsági fenyegetések Azure Defender a portál új Biztonság lapján Azure Arc nak.
- Azure Arc engedélyezett Kubernetes-fürtök integrálva vannak a Azure Security Center platformba és felhasználói élménybe

További információ: Azure Defender for Kubernetes helyszíni és többfelhős [Kubernetes-fürtök használata.](defender-for-kubernetes-azure-arc.md)

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center az engedélyezett Kubernetes-fürtökhöz Azure Defender üzembe Azure Arc bővítmény üzembe helyezését." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview"></a>Javaslatok a dns Azure Defender- és Resource Manager engedélyezéséhez (előzetes verzió)

Két új javaslat lett hozzáadva a [](defender-for-resource-manager-introduction.md) DNS-hez való Azure Defender és Resource Manager Azure Defender [egyszerűsítése érdekében:](defender-for-dns-introduction.md)

- **Azure Defender az Resource Manager** számára – a Defender for Resource Manager automatikusan figyeli a szervezetben az erőforrás-kezelési műveleteket. Azure Defender észleli a fenyegetéseket, és riasztást küld a gyanús tevékenységekről.
- **Azure Defender** DNS-t engedélyezni kell – A Defender for DNS egy további védelmi réteget biztosít a felhőbeli erőforrások számára az Azure-erőforrásokból származó összes DNS-lekérdezés folyamatos monitorozása által. Azure Defender riasztást küld a DNS-réteg gyanús tevékenységével kapcsolatban.

A Azure Defender engedélyezése díjakat fog fizetni. A régiónkénti díjszabásról a Security Center díjszabási oldalán olvashat: https://aka.ms/pricing-security-center .

> [!TIP]
> Az előzetes verzióra vonatkozó javaslatok nem renderelik az erőforrásokat nem megfelelőnek, és nem szerepelnek a biztonsági pontszám kiszámításakor. Ahol csak lehetséges, szervizeljük őket, hogy az előzetes verzió időszakának végén hozzájáruljanak a pontszámhoz. További információ a javaslatokra való válaszadásról: Javaslatok szervize [a Azure Security Center.](security-center-remediate-recommendations.md)


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Három szabályozási megfelelőségi szabványt adott hozzá: Azure CIS 1.3.0, CMMC Level 3 és Új-Zéland ISM Restricted

Három szabványt adtunk hozzá a Azure Security Center. A jogszabályi megfelelési irányítópulton a következővel követheti nyomon a megfelelőségét:

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC 3. szint](../governance/policy/samples/cmmc-l3.md)
- [Új-Zélandi ISM korlátozott](../governance/policy/samples/new-zealand-ism.md)

Ezeket hozzárendelheti az előfizetéseihez a Szabványkészlet testreszabása a jogszabályi megfelelési [irányítópulton leírtak szerint.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Három új szabvány a Azure Security Center megfelelőségi irányítópultjához." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

További információ:
- [A szabványok testreszabása a jogszabályi megfelelési irányítópulton](update-regulatory-compliance-packages.md)
- [Oktatóanyag: Jogszabályi megfelelés javítása](security-center-compliance-dashboard.md)
- [GYIK – Jogszabályi megfelelési irányítópult](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Négy új javaslat a vendégkonfigurációval kapcsolatban (előzetes verzió)

Az Azure [vendégkonfigurációs bővítményjelentései](../governance/policy/concepts/guest-configuration.md) Security Center, hogy a virtuális gépek vendégen lévő beállításai megsokosodnak. A bővítmény nem szükséges az Arc-kompatibilis kiszolgálókhoz, mert szerepel az Arc connected Machine-ügynökben. A bővítményhez rendszer által felügyelt identitás szükséges a gépen.

Négy új javaslatot adtunk hozzá, hogy Security Center a bővítményt a legtöbbet hozva ki.

- Két javaslat arra kéri, hogy telepítse a bővítményt és annak szükséges, rendszer által felügyelt identitását:
    - **A vendégkonfigurációs bővítményt telepíteni kell a gépekre**
    - **A virtuális gépek vendégkonfigurációs bővítményét rendszer által hozzárendelt felügyelt identitással kell üzembe helyezni**

- A bővítmény telepítésekor és futtatásakor megkezdi a gépek naplózását, és a rendszer felszólítja a beállítások, például az operációs rendszer és a környezeti beállítások konfigurálásának megerősítésére. Ez a két javaslat felszólítja a Windows és Linux rendszerű gépek megerősítésre a leírt módon:
    - **Windows Defender Exploit Guardnak engedélyezve kell lennie a gépeken**
    - **A Linux rendszerű gépek hitelesítéséhez SSH-kulcsokra van szükség**

További [információ: A Azure Policy vendégkonfigurációjának megismerve.](../governance/policy/concepts/guest-configuration.md)

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>A CMK-javaslatok átkerültek az ajánlott eljárások biztonsági szabályozására

Minden szervezet biztonsági programja tartalmaz adattitkosítási követelményeket. Alapértelmezés szerint az Azure-ügyfelek adatai szolgáltatás által kezelt kulcsokkal vannak titkosítva. Az ügyfél által kezelt kulcsokra (CMK) azonban általában szükség van a jogszabályi megfelelőségi szabványoknak való megfeleléshez. A CMK-k az Ön által létrehozott Azure Key Vault [kulcsokkal](../key-vault/general/overview.md) titkosítják az adatokat. Ez teljes körű vezérlést és felelősséget biztosít a kulcs életciklusával, beleértve a rotációt és a felügyeletet is.

Azure Security Center biztonsági vezérlői a kapcsolódó biztonsági javaslatok logikai csoportjai, és tükrözik a sebezhető támadási felületeket. Minden vezérlő a biztonsági pontszámhoz hozzáadható pontok maximális számával rendelkezik, ha a vezérlőben felsorolt összes javaslatot helyreigazító javaslatokat tesz az összes erőforráshoz. Az **Ajánlott biztonsági eljárások megvalósítása** biztonsági vezérlő nulla pontot ér. Így a vezérlő javaslatai nincsenek hatással a biztonsági pontszámra.

Az alább felsorolt javaslatok átkerülnek az Ajánlott biztonsági eljárások megvalósítása biztonsági vezérlőbe, hogy jobban tükrözzék a választható jellegüket.  Ez az áthelyezés biztosítja, hogy ezek a javaslatok a legmegfelelőbben szabályozhatóak a célkitűzésük elérése érdekében.

- Azure Cosmos DB fiókoknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához
- Azure Machine Learning munkaterületeket ügyfél által felügyelt kulccsal (CMK) kell titkosítani
- Cognitive Services fióknak engedélyeznie kell az adattitkosítást egy ügyfél által felügyelt kulccsal (CMK)
- A tárolóregisztrálókat ügyfél által felügyelt kulccsal (CMK) kell titkosítani
- A felügyelt SQL-példányok az ügyfelek által felügyelt kulcsokat használják az adatok titkosításához az
- Az SQL-kiszolgálóknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához
- A tárfiókok a felhasználó által felügyelt kulcsot (CMK) használják a titkosításhoz

Ismerje meg, hogy mely javaslatok vannak az egyes biztonsági vezérlőkben a [Biztonsági vezérlők és az ő javaslataik között.](secure-score-security-controls.md#security-controls-and-their-recommendations)


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender riasztás elavult

Az Azure Defender alább felsorolt riasztások elavultak.

- Az új riasztások felváltják ezt a két riasztást, és jobb lefedettséget biztosítanak:

    | AlertType (Riasztástípus)                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | ELŐZETES VERZIÓ – A rendszer a "Get-AzureDomainInfo" függvény futtatását észlelte a MicroBurst eszközkészletben |
    | ARM_MicroBurstRunbook    | ELŐZETES VERZIÓ – A rendszer a "Get-AzurePasswords" függvény futtatását észlelte a MicroBurst eszközkészletben  |
    |                          |                                                                          |

- Ez a kilenc riasztás egy már Azure Active Directory Identity Protection összekötőhöz (IPC) kapcsolódik:

    | AlertType (Riasztástípus)           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation (Ismeretlen hely)  | Szokatlan bejelentkezési tulajdonságok |
    | AnonymousLogin      | Névtelen IP-cím          |
    | InfectedDeviceLogin (InfectedDeviceLogin) | kártevő szoftverrel összekapcsolt IP-cím     |
    | ImpossibleTravel    | Szokatlan utazás               |
    | MaliciousIP         | rosszindulatú IP-cím          |
    | LeakedCredentials (Kiszivárgott adattitkok)   | Kiszivárgott hitelesítő adatok            |
    | PasswordSpray       | Szórásos jelszójelszó                |
    | LeakedCredentials (Kiszivárgott adattitkok)   | Azure AD Intelligens veszélyforrás-felderítés  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > Ez a kilenc IPC-riasztás soha nem Security Center riasztásokat. Ezek a Azure Active Directory (AAD) Identity Protection-összekötő (IPC) részei, amely a Security Center. Az elmúlt két évben csak azok az ügyfelek kapták meg ezeket a riasztásokat, amelyek 2019-ben vagy korábban konfigurálták az exportálást (az összekötőből az ASC-be). Az AAD IPC továbbra is a saját riasztási rendszerében mutatja ezeket, és továbbra is elérhetők a Azure Sentinel. Az egyetlen változás az, hogy már nem jelennek meg a Security Center.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>A "Rendszerfrissítések alkalmazása" biztonsági vezérlő két ajánlása elavult 

Az alábbi két javaslat elavult, és a módosítások a biztonsági pontszámra gyakorolt csekély hatást eredményezhetnek:

- **A rendszerfrissítések alkalmazásához a gépeket újra kell indítani**
- **A monitorozási ügynököt telepíteni kell a gépekre.** Ez a javaslat csak a helyszíni gépekre vonatkozik, és a logikájának egy része átkerül egy másik javaslatra, a Log Analytics-ügynök állapotával kapcsolatos problémákat meg kell oldani a **gépeken**

Javasoljuk, hogy ellenőrizze a folyamatos exportálási és munkafolyamat-automatizálási konfigurációkat annak ellenőrzésére, hogy ezek a javaslatok szerepelnek-e bennük. Emellett az ezeket használó irányítópultokat vagy más monitorozási eszközöket ennek megfelelően kell frissíteni.

Ezekről a javaslatokról a biztonsági javaslatok [referenciaoldalán olvashat bővebben.](recommendations-reference.md)

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Azure Defender sql on machine csempe eltávolítása az Azure Defender irányítópultról

Az Azure Defender irányítópult lefedettségi területe a környezetéhez kapcsolódó Azure Defender csempéket tartalmaz. A védett és nem védett erőforrások számáról szóló jelentéskészítési probléma miatt úgy döntöttünk, hogy a probléma megoldásáig ideiglenesen eltávolítjuk az **Azure Defender SQL** erőforrás-lefedettségi állapotát a gépeken.


## <a name="march-2021"></a>2021. március

A márciusi frissítések a következők:

- [Azure Firewall beépített Security Center](#azure-firewall-management-integrated-into-security-center)
- [Az SQL sebezhetőségi felmérése mostantól tartalmazza a "Szabály letiltása" (előzetes verzió) élményt](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor beépített Security Center és három megadott sablon](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [A jogszabályi megfelelés irányítópultja mostantól tartalmazza az Azure-naplózási jelentéseket (előzetes verzió)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [A javaslatok adatai megtekinthetők a Azure Resource Graph "Explore in ARG" (ARG-sel való felfedezés)](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [A munkafolyamat-automatizálás üzembe helyezésére vonatkozó szabályzatok frissítései](#updates-to-the-policies-for-deploying-workflow-automation)
- [Két örökölt javaslat már nem ír adatokat közvetlenül az Azure-tevékenységnaplóba](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Javaslatok oldal fejlesztései](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall beépített Security Center

Amikor megnyitja Azure Security Center lapot, az első megjelenő oldal az áttekintési oldal. 

Ez az interaktív irányítópult egységes áttekintést nyújt a hibrid felhőbeli számítási feladatok biztonsági útjára. Emellett biztonsági riasztásokat, lefedettségi adatokat és egyéb információkat is tartalmaz.

A biztonsági állapot központi felhasználói felületről való megtekintésének részeként integráltuk a Azure Firewall Manager irányítópultba. Mostantól az összes hálózat tűzfal-lefedettségi állapotát ellenőrizheti, és központilag felügyelheti Azure Firewall szabályzatokat a Security Center.

Erről az irányítópultról további Azure Security Center [áttekintési oldalán talál.](overview-page.md)

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center irányítópultja egy csempével a Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Az SQL sebezhetőségi felmérése mostantól tartalmazza a "Szabály letiltása" (előzetes verzió) élményt

Security Center beépített biztonsági rések vizsgálatával felderítheti, nyomon követheti és orvosolhatja az adatbázisok lehetséges biztonsági réseit. Az értékelési vizsgálatok eredményei áttekintést nyújtanak az SQL-gépek biztonsági állapotáról és az esetleges biztonsági eredményekről.

Ha a cégnek nem kell figyelembe vennie a kereséseket, és nem kell orvosolni őket, letilthatja azt. A letiltott eredmények nem befolyásolják a biztonsági pontszámot vagy nem kívánt zajt generálnak.

További információ: [Meghatározott eredmények letiltása.](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor beépített Security Center és három megadott sablon

Az Ignite 2021. tavaszi részeként bejelentettük a Azure Monitor-munkafüzetek integrált Security Center.

Az új integráció segítségével használatba is használhatja a használatra Security Center sablonokat. Munkafüzetsablonok használatával dinamikus és vizuális jelentéseket is elérhető és készíthet a szervezet biztonsági ure-ának nyomon követéséhez. Emellett új munkafüzeteket hozhat létre az Security Center-adatok vagy bármely más támogatott adattípus alapján, és gyorsan üzembe helyezhet közösségi munkafüzeteket Security Center GitHub-közösségből.

Három sablonjelentés áll rendelkezésre:

- **Secure Score Over Time** (Biztonsági pontszám idővel) – Nyomon követheti az előfizetések pontszámait és az erőforrásokra vonatkozó javaslatok módosításait
- **Rendszerfrissítések** – A hiányzó rendszerfrissítések megtekintése erőforrások, operációs rendszer, súlyosság és egyéb adatok alapján
- **Sebezhetőségi felmérés eredményei** – Az Azure-erőforrások sebezhetőségi vizsgálatának eredményei

Ismerje meg ezeknek a jelentéseknek a használatával vagy saját, gazdag, interaktív jelentések készítése az Security Center [létrehozásához.](custom-dashboards-azure-workbooks.md)

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Biztonsági pontszám az idő alapján jelentés":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>A jogszabályi megfelelés irányítópultja mostantól tartalmazza az Azure auditjelentéseit (előzetes verzió)

A jogszabályi megfelelési irányítópult eszköztárán mostantól letöltheti az Azure- és Dynamics-minősítési jelentéseket. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="A jogszabályi megfelelés irányítópultjának eszköztára":::

Kiválaszthatja a megfelelő jelentéstípusok (PCI, SOC, ISO stb.) lapját, és szűrőkkel megkeresheti a kívánt jelentéseket.

További információ a [szabványok kezeléséről a jogszabályi megfelelési irányítópulton.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Az elérhető Azure-auditjelentések listájának szűrése":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>A javaslatok adatai megtekinthetők a Azure Resource Graph "Explore in ARG" (ARG-sel való felfedezés)

A javaslat részleteit tartalmazó oldalak mostantól tartalmazzák a "Explore in ARG" (Felfedezés az ARG-ban) eszköztárgombot. Ezzel a gombbal megnyithatja a Azure Resource Graph lekérdezést, és feltárhatja, exportálhatja és megoszthatja a javaslat adatait.

Azure Resource Graph (ARG) azonnali hozzáférést biztosít az erőforrás-információkhoz a felhőalapú környezetekben robusztus szűrési, csoportosítási és rendezési képességekkel. Ez egy gyors és hatékony módja az információk lekérdezésének programozott módon vagy az Azure-előfizetésen belülről Azure Portal.

További információ a [Azure Resource Graph (ARG) ről.](../governance/resource-graph/index.yml)

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="A javaslatok adatainak feltárása a Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>A munkafolyamat-automatizálás üzembe helyezésére vonatkozó szabályzatok frissítései

A szervezet figyelési és incidensválasz-folyamatainak automatizálása jelentősen megnövelheti a biztonsági incidensek kivizsgálásának és enyhítésének idejét.

Három olyan Azure Policy "DeployIfNotExist" szabályzatot biztosítunk, amelyek munkafolyamat-automatizálási eljárásokat hoznak létre és konfigurálnak, hogy az automatizálásokat a szervezeten belül üzembe tudja helyezni:

|Cél  |Szabályzat  |Szabályzatazonosító  |
|---------|---------|---------|
|Munkafolyamat-automatizálás biztonsági riasztásokhoz|[Azure Security Center-riasztásokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Munkafolyamat-automatizálás biztonsági javaslatokhoz|[Azure Security Center-javaslatokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Munkafolyamat-automatizálás jogszabályi megfelelési változásokhoz|[Munkafolyamat-automatizálás üzembe helyezése Azure Security Center megfelelőség érdekében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

A szabályzatok funkcióinak két frissítése van:

- Hozzárendeléskor a kényszerítési funkció továbbra is engedélyezett marad.
- Most már testre szabhatja ezeket a szabályzatokat, és frissítheti bármelyik paramétert, még akkor is, ha már telepítve vannak. Ha például egy felhasználó egy másik értékelési kulcsot szeretne hozzáadni, vagy szerkeszteni szeretne egy meglévő értékelési kulcsot, azt is meg tudja tenni.

A [munkafolyamat-automatizálási sablonok – első lépések.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

További információ az [eseményindítókra adott válaszok Security Center automatizálásával kapcsolatban.](workflow-automation.md)


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Két örökölt javaslat már nem ír adatokat közvetlenül az Azure-tevékenységnaplóba 

Security Center szinte minden biztonsági javaslat adatait továbbítja a Azure Advisor, amely pedig az [Azure-tevékenységnaplóba írja az adatokat.](../azure-monitor/essentials/activity-log.md)

Két javaslathoz a rendszer egyidejűleg közvetlenül az Azure-tevékenységnaplóba írja az adatokat. Ezzel a módosítással a Security Center leállítja az adatok írását az örökölt biztonsági javaslatokhoz közvetlenül a tevékenységnaplóba. Ehelyett az adatokat a Azure Advisor exportáljuk, mint a többi javaslat esetében.

A két régi javaslat a következő:
- A végpontvédelmi állapottal kapcsolatos problémákat meg kell oldani a gépeken
- A gépek biztonsági konfigurációjának biztonsági réseit helyre kell ásni

Ha már hozzáfért ehhez a két javaslathoz a tevékenységnapló "TaskDiscovery típusú javaslat" kategóriájában, akkor ez a továbbiakban nem érhető el.


### <a name="recommendations-page-enhancements"></a>A Javaslatok lap fejlesztései 

Kiadták a javaslatok listájának továbbfejlesztett verzióját, hogy egy pillantással több információt nyújtsunk.

A lapon a következőt fogja látni:

1. Az egyes biztonsági vezérlők maximális és aktuális pontszáma.
1. A címkéket lecserélő ikonok, például **a Gyorsjavítás és** az **Előzetes verzió.**
1. Új oszlop, amely az egyes javaslatokhoz kapcsolódó [Policy kezdeményezést](security-policy-concept.md) mutatja – akkor látható, ha a "Csoportosítás vezérlők szerint" beállítás le van tiltva.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="A Azure Security Center oldalának fejlesztései – 2021. március" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="A Azure Security Center &quot;lapos&quot; listájának fejlesztései – 2021. március" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

További információ: [Biztonsági javaslatok a Azure Security Center.](security-center-recommendations.md)


## <a name="february-2021"></a>2021. február

A februári frissítések a következők:

- [Új biztonsági riasztások lap a Azure Portal általánosan elérhető (GA) számára](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [A Kubernetes számítási feladatok védelmére vonatkozó javaslatok általánosan elérhetőek (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [A Microsoft Defender az Azure Defender támogatja a Windows Server 2019-et és Windows 10 Virtual Desktopot (WVD) (előzetes verzió)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Szabályzatra mutató közvetlen hivatkozás a javaslat részleteit tartalmazó lapon](#direct-link-to-policy-from-recommendation-details-page)
- [Az SQL-adatbesorolási javaslat már nincs hatással a biztonsági pontszámra](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [A munkafolyamat-automatizálást a jogszabályi megfelelési értékelések változásai aktiválják (előzetes verzióban)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Az eszközleltár oldalának fejlesztései](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Új biztonsági riasztások lap a Azure Portal általánosan elérhető (GA) számára

Azure Security Center biztonsági riasztások lapját a következő tervezéssel lett áttervezve:

- **Továbbfejlesztett osztályozási** élmény a riasztások számára – segít csökkenteni a riasztások elszennyedtségét és a legfontosabb fenyegetésekre való összpontosítást. A lista testre szabható szűrőket és csoportosítási lehetőségeket tartalmaz.
- **További információ a riasztások listájában** – például MITRE ATT&ACK-taktikák.
- **A mintariasztásokat** létrehozására Azure Defender és a riasztások tesztelésére. konfiguráció (SIEM-integrációhoz, e-mail-értesítésekhez és munkafolyamat-automatizáláshoz), mintariasztásokat hozhat létre az összes Azure Defender tervből.
- **Igazodás Azure Sentinel** az incidensek élményével – a mindkét terméket használatba vevő ügyfelek számára a váltás már egyszerűbb folyamat, és könnyű egymástól tanulni.
- **Nagyobb méretű riasztáslisták** jobb teljesítménye.
- **Navigálás billentyűzettel** a riasztások listájában.
- **Riasztások a Azure Resource Graph** – az összes erőforráshoz Azure Resource Graph kusto-hez hasonló API-ban is lekérdezhet riasztásokat. Ez akkor is hasznos, ha saját riasztási irányítópultokat épít fel. [További információ a Azure Resource Graph.](../governance/resource-graph/index.yml)
- **Mintariasztás létrehozása funkció** – Minta riasztások az új riasztások felhasználói élményből való létrehozásához lásd: Mintariasztás [Azure Defender létrehozása.](security-center-alert-validation.md#generate-sample-azure-defender-alerts)

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center biztonsági riasztások listája":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>A Kubernetes számítási feladatok védelmére vonatkozó javaslatok általánosan elérhető (GA)

Örömmel jelentjük be a Kubernetes számítási feladatok védelmére vonatkozó javaslatok általános rendelkezésre állását (GA).

Annak érdekében, hogy a Kubernetes számítási feladatai alapértelmezés szerint biztonságosak, a Security Center Kubernetes szintű ellenőrző javaslatokat adott hozzá, beleértve a Kubernetes belépésvezérlésének kényszerítési lehetőségeit is.

Ha telepítve van a Kuberneteshez használható Azure Policy-bővítmény az Azure Kubernetes Service- (AKS-) fürtön, a rendszer a Kubernetes API-kiszolgálóra vonatkozó összes kérést a fürtön való megőrzés előtt a 13 biztonsági javaslatként megjelenített, előre meghatározott ajánlott eljárások szerint figyeli. Ezután konfigurálhatja a-t, hogy kikényszeríteni tudja az ajánlott eljárásokat, és a jövőbeli számítási feladatokhoz is be legyen állítva.

Megadhatja például, hogy a rendszer-jogosultságú tárolók ne legyen létrehozva, és a további erre vonatkozó kérések le lesznek tiltva.

További információ: A Számítási feladatok védelme – ajánlott [eljárások a Kubernetes belépésvezérlésének használatával.](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

> [!NOTE]
> Amíg a javaslatok előzetes verzióban voltak, nem rendereltek egy AKS-fürterőforrást nem megfelelőnek, és nem szerepeltek a biztonsági pontszám számításaiban. ezzel az GA-bejelentéssel ezek szerepelnek a pontszámszámításban. Ha még nem orvosolta őket, az némi hatással lehet a biztonsági pontszámra. Ha lehetséges, szervizek a Javaslatok szervize a következőben leírtak [szerint: Azure Security Center.](security-center-remediate-recommendations.md)


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>A Microsoft Defender az Azure Defender támogatja a Windows Server 2019-et és Windows 10 Virtual Desktopot (WVD) (előzetes verzió)

A Microsoft Defender for Endpoint egy holisztikus, felhőalapú végpontbiztonsági megoldás. Kockázatalapú biztonsági rések kezelését és értékelését, valamint végpontészlelést és -választ (EDR) biztosít. A Defender for Endpoint és az Azure Security Center együttes használatának előnyeinek teljes listájáért lásd: A végpontok védelme az Security Center integrált [EDR-megoldásával: Microsoft Defender végponthoz.](security-center-wdatp.md)

Ha engedélyezi a Azure Defender windowsos kiszolgálókon, a csomag tartalmazza a Defender for Endpoint licencét. Ha már engedélyezte a Azure Defender számára a kiszolgálókhoz, és Windows 2019-kiszolgálókkal rendelkezik az előfizetésében, a rendszer ezzel a frissítéssel automatikusan megkapja a Defendert a végponthoz. Nincs szükség manuális műveletre. 

A támogatás mostantól a Windows Server 2019-re és a Windows Virtual Desktop [(WVD)](../virtual-desktop/overview.md)is kiterjed.

> [!NOTE]
> Ha Windows Server 2019 rendszerű gépen engedélyezi a Defender for Endpoint használatát, győződjön meg arról, hogy az megfelel a Microsoft Defender végpontintegrációhoz való engedélyezésével kapcsolatos cikk [előfeltételeinek.](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Szabályzatra mutató közvetlen hivatkozás a javaslat részleteit tartalmazó lapon

Egy javaslat részleteinek áttekintésekor gyakran hasznos lehet a mögöttes szabályzat megtekintése. Minden szabályzat által támogatott javaslathoz új hivatkozás található a javaslat részleteit tartalmazó oldalon:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Hivatkozás Azure Policy javaslatot támogató adott szabályzat lapjára":::

Ezen a hivatkozáson megtekintheti a szabályzatdefiníciót, és áttekinti a kiértékelési logikát. 

Ha áttekinti a biztonsági javaslatok referencia-útmutatójában található javaslatok [listáját,](recommendations-reference.md)a szabályzatdefiníciós oldalakra mutató hivatkozásokat is látni fog:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Egy adott szabályzat Azure Policy oldalának elérése közvetlenül a Azure Security Center javaslatok referenciaoldalán" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Az SQL-adatbesorolási javaslat már nincs hatással a biztonsági pontszámra
Az **SQL-adatbázisok bizalmas adatait** a továbbiakban nem szabad besorolni, ez már nem befolyásolja a biztonsági pontszámot. Ez az egyetlen javaslat az Adatbesorolás biztonsági vezérlő **alkalmazása** részen, így a vezérlő biztonsági pontszáma mostantól 0.

A biztonsági vezérlők teljes listáját a Security Center pontszámokkal és az egyes javaslatok listájával együtt lásd: Biztonsági vezérlők és [javaslatok.](secure-score-security-controls.md#security-controls-and-their-recommendations)

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>A munkafolyamat-automatizálást a jogszabályi megfelelőségi értékelések változásai aktiválják (előzetes verzióban)
Hozzáadtunk egy harmadik adattípust a munkafolyamat-automatizálás eseményindítóihoz: a jogszabályi megfelelőségi értékelések változásait.

Ismerje meg, hogyan használhatja a munkafolyamat-automatizálási eszközöket a Válaszok automatizálása Security Center [eseményindítókhoz.](workflow-automation.md)

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="A jogszabályi megfelelési értékelések változásainak használata munkafolyamat-automatizálás aktiválásához" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Az eszközleltár oldalának fejlesztései
Security Center eszközleltár oldalán a következő módokon továbbfejlesztettük:

- Az oldal tetején található összegzések most már tartalmazzák a Nem regisztrált előfizetéseket, amelyek az engedélyezett előfizetések Security Center mutatják.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="A nem regisztrált előfizetések száma az eszközleltár oldal tetején található összegzések között":::

- A szűrők ki vannak bontva és kibővültek a következőkkel:
    - **Darabszámok** – Minden szűrő az egyes kategóriák feltételeinek megfelelő erőforrások számát mutatja

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="A szűrők száma az eszközleltár oldalán a Azure Security Center":::

    - **Kivételszűrőt tartalmaz** (nem kötelező) – leszűkíti az eredményeket a kivételekkel nem rendelkezik vagy nem kapott erőforrásokra. Ez a szűrő alapértelmezés szerint nem jelenik meg, de elérhető a **Szűrő hozzáadása gombról.**

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="A &quot;kivételt tartalmaz&quot; szűrő hozzáadása Azure Security Center eszközleltár oldalán":::

További információ az erőforrások az eszközleltárból való felfedezéséről [és kezelésről.](asset-inventory.md)

## <a name="january-2021"></a>2021. január

A januári frissítések a következők:

- [Az Azure biztonsági teljesítményteszt mostantól az alapértelmezett szabályzat kezdeményezése a Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Megjelent a biztonságirés-felmérés a virtuális gépeken és a többfelhős gépeken az általánosan elérhető (GA) szolgáltatáshoz](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [A felügyeleti csoportok biztonsági pontszáma előzetes verzióban érhető el](#secure-score-for-management-groups-is-now-available-in-preview)
- [Megjelent a biztonsági pontszám API az általánosan elérhető (GA) verzióhoz](#secure-score-api-is-released-for-general-availability-ga)
- [Az Azure Defender dns-védelem App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [A többfelhős összekötők általánosan elérhetőek (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Teljes javaslatok kivétele az előfizetések és felügyeleti csoportok biztonsági pontszáma alól](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [A felhasználók mostantól a globális rendszergazdájuktól kérhetik a bérlői szintű láthatóságot](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 előzetes verzióra vonatkozó javaslat hozzáadva az Azure biztonsági teljesítményteszt lefedettségének növeléséhez](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [A javaslatok szűrt listájának CSV-exportálása](#csv-export-of-filtered-list-of-recommendations)
- [A "Nem alkalmazható" erőforrások mostantól megfelelőként vannak jelentve a Azure Policy értékelésekben](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Biztonsági pontszámok és jogszabályi megfelelési adatok heti pillanatképének exportálása folyamatos exportálással (előzetes verzió)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Mostantól az Azure biztonsági teljesítményteszt az alapértelmezett szabályzati kezdeményezés a Azure Security Center

Az Azure biztonsági teljesítménytesztje a Microsoft által írt, Azure-specifikus irányelvek készlete a közös megfelelőségi keretrendszeren alapuló biztonsági és megfelelőségi ajánlott eljárásokhoz. Ez a széles körben elterjedt teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) vezérlőire épül, és a felhőközpontú biztonságra összpontosít.

Az elmúlt Security Center a beépített biztonsági javaslatok listája jelentősen megnőtt, hogy bővítse a teljesítményteszt lefedettségét.

Ebben a kiadásban a teljesítményteszt a Security Center javaslatainak alapja, és teljes mértékben integrálva van alapértelmezett szabályzati kezdeményezésként. 

Minden Azure-szolgáltatásnak van egy biztonsági alapkonfiguráció oldala a dokumentációjában. Ez például [a Security Center alapkonfigurációja.](security-baseline.md) Ezek az alapkonfigurációk az Azure biztonsági teljesítménytesztjére épülnek.

Ha az Security Center megfelelőségi irányítópultját használja, a teljesítményteszt két példányát fogja látni az átmeneti időszakban:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center Azure biztonsági teljesítménytesztet megjelenítő, jogszabályi megfelelést bemutató irányítópultja":::

A meglévő javaslatokat ez nem érinti, és a teljesítményteszt növekedésével a változások automatikusan megjelennek a Security Center. 

További tudnivalókért tekintse meg a következő oldalakat:

- [További információ az Azure biztonsági teljesítménytesztjről](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [A szabványok halmazának testreszabása a jogszabályi megfelelési irányítópulton](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>A biztonságirés-felmérést a rendszer általánosan elérhetőként (GA) ad ki a többfelhős és a felhőalapú számítógépeken.

Októberben bejelentettük a Azure Arc kiszolgálók és Azure Defender [](defender-for-servers-introduction.md)biztonsági rések integrált ellenőrzőeszközének (Qualys által működtetett) vizsgálatának előzetes kiadását.

Mostantól általánosan elérhető.

Ha engedélyezte a Azure Arc a nem Azure-beli gépeken, a Security Center lehetővé fogja tenni az integrált biztonsági rések ellenőrzőeszközének manuális és nagy léptékű üzembe helyezését.

Ezzel a frissítéssel felszabadíthatja  a kiszolgálók Azure Defender áramot, hogy összevonja a sebezhetőségkezelési programot az összes Azure-beli és nem Azure-beli eszközre.

Fő képességek:

- A biztonsági rések felmérése (VA) olvasó kiépítési állapotának monitorozása Azure Arc gépeken
- Az integrált VA-ügynök üzembe Azure Arc Windows és Linux rendszerű virtuális gépeken (manuálisan és nagy léptékben)
- Az üzembe helyezett ügynökök által észlelt biztonsági rések fogadása és elemzése (manuálisan és nagy léptékben)
- Egységes felhasználói élmény Azure-beli virtuális gépekhez és Azure Arc gépekhez

További információ az integrált sebezhetőségi ellenőrzőeszköz [hibrid gépeken való üzembe helyezéséről.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[További információ az engedélyezett Azure Arc kiszolgálókról.](../azure-arc/servers/index.yml)


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>A felügyeleti csoportok biztonsági pontszáma előzetes verzióban érhető el

A biztonsági pontszám oldal mostantól az előfizetési szint mellett a felügyeleti csoportok összesített biztonsági pontszámait is megjeleníti. Így most már láthatja a szervezetben található felügyeleti csoportok listáját és az egyes felügyeleti csoportok pontszámát.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A felügyeleti csoportok biztonsági pontszámainak megtekintése.":::

További információ a biztonsági pontszámról és [a biztonsági vezérlőkről a Azure Security Center.](secure-score-security-controls.md)

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Megjelent a biztonsági pontszám API az általánosan elérhető (GA) verzióhoz

Most már elérheti a pontszámát a [biztonsági pontszám API-n keresztül.](/rest/api/securitycenter/securescores/) Az API-metódusok rugalmasan lekérdezik az adatokat, és saját jelentéskészítési mechanizmust hoznak létre a biztonsági pontszámok alapján. Például:

- a **Secure Scores** API-val lekért pontszám egy adott előfizetéshez
- A Secure **Score Controls** API használatával listába foglalhatja az előfizetések biztonsági vezérlőit és aktuális pontszámát

Ismerje meg a biztonsági pontszám API-val elérhető külső eszközöket a [GitHub-közösség](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)biztonságos pontszám területén.

További információ a biztonsági pontszámról és [a biztonsági vezérlőkről a Azure Security Center.](secure-score-security-controls.md)


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Az Azure Defender dns-védelem App Service

Az altartományok feladatátvétele gyakori, súlyos fenyegetést jelent a szervezetek számára. Az altartományok feladatátvétele akkor fordulhat elő, ha olyan DNS-rekordja van, amely egy megszüntetett webhelyre mutat. Az ilyen DNS-rekordokat "felcsatolásos DNS-bejegyzéseknek" is nevezik. A CNAME-rekordok különösen ki vannak téve ennek a fenyegetésnek. 

Az altartományok átvétele lehetővé teszi, hogy a fenyegetéstékony felhasználók rosszindulatú tevékenységet végző webhelyre irányítsák át a szervezet tartományának szánt forgalmat.

Azure Defender a App Service észleli a felcsatolt DNS-bejegyzéseket, amikor egy App Service webhelyet leszerelnek. Ez az a pillanat, amikor a DNS-bejegyzés egy nem létező erőforrásra mutat, és a webhely sebezhető az altartomány-feladatátvételsel. Ezek a védelmi lehetőségek akkor is elérhetők, ha a tartományokat Azure DNS vagy egy külső tartományregisztráló kezeli, és windowsos és App Service-alapú App Service on Linux.

További információ:

- [App Service referenciatáblája](alerts-reference.md#alerts-azureappserv) – Két új riasztást Azure Defender, amelyek akkor aktiválódnak, ha a rendszer összecsomó DNS-bejegyzést észlel
- [A dangolásos DNS-bejegyzések](../security/fundamentals/subdomain-takeover.md) megakadályozása és az altartományok feladatátvételének elkerülése – További információ az altartományok feladatátvételének fenyegetésről és az összeomó DNS-aspektusról
- [Bevezetés a Azure Defender for App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>A többfelhős összekötők általánosan elérhetőek (GA)

A gyakran több felhőplatformra kiterjedő felhőalapú számítási feladatok esetében a felhőbiztonsági szolgáltatásoknak ugyanezt kell megtenniük.

Azure Security Center az Azure, a Amazon Web Services (AWS) és a Google Cloud Platform (GCP) számítási feladatait.

Az AWS- vagy GCP-fiókok csatlakoztatása integrálja natív biztonsági eszközeiket, például az AWS Security Hubot és a GCP Security Command Centert a Azure Security Center.

Ez a képesség azt jelenti, Security Center az összes nagyobb felhőkörnyezet számára biztosítja a láthatóságot és a védelmet. Az integráció néhány előnye:

- Automatikus ügynök-kiépítés – Security Center a Azure Arc a Log Analytics-ügynök AWS-példányon való üzembe helyezéséhez
- Szabályzatkezelés
- Biztonságirés-kezelés
- Beágyazott végpontészlelés és -válasz (EDR)
- A biztonsági konfigurációk hibás észlelése
- Egyetlen nézet, amely az összes felhőszolgáltató biztonsági javaslatait mutatja
- Az összes erőforrást bele kell Security Center biztonsági pontszámok számításaiba
- Az AWS- és GCP-erőforrások jogszabályi megfelelőségi értékelései

A Security Center menüjében válassza a **Többfelhős** összekötők lehetőséget, és megjelenik az új összekötők létrehozásának lehetőségei:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="AWS-fiók hozzáadása gomb Security Center többfelhős összekötők oldalán":::

További információ:
- [AWS-fiókok csatlakoztatása Azure Security Center](quickstart-onboard-aws.md)
- [A GCP-fiókok csatlakoztatása a Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Teljes javaslatok kivétele az előfizetések és felügyeleti csoportok biztonsági pontszáma alól

Bővítjük a kivételi képességet, hogy teljes javaslatokat tartalmazzanak. További lehetőségek biztosítása az előfizetések, felügyeleti csoportok vagy erőforrások Security Center által nyújtott biztonsági javaslatok finomhangolásához.

Előfordulhat, hogy egy erőforrás nem megfelelőként szerepel a listán, ha tudja, hogy a problémát egy külső eszköz oldotta meg, Security Center nem észlelte. Vagy egy javaslat olyan hatókörben fog jelenni, amelyhez úgy érzi, hogy nem tartozik. Előfordulhat, hogy a javaslat nem megfelelő egy adott előfizetéshez. Vagy lehet, hogy a szervezete úgy döntött, hogy elfogadja az adott erőforrással vagy javaslattal kapcsolatos kockázatokat.

Ezzel az előzetes verziójú funkcióval kivételt hozhat létre egy javaslathoz a következőre:

- **Mentesítsen egy** erőforrást, hogy az ne legyen felsorolva a nem megfelelő erőforrásokkal a listában a jövőben, és nincs hatással a biztonsági pontszámra. Az erőforrás nem alkalmazhatóként jelenik meg, és az ok "kivételként" jelenik meg a kiválasztott indoklással együtt.

- **Mentesítsen egy előfizetést** vagy felügyeleti csoportot, hogy a javaslat ne befolyásolja a biztonsági pontszámot, és a jövőben ne legyen látható az előfizetés vagy a felügyeleti csoport számára. Ez a meglévő erőforrásokra és a jövőben létrehozott erőforrásokra vonatkozik. A javaslat a kiválasztott hatókörnek megfelelő indoklással lesz megjelölve.

További információ: Erőforrások és javaslatok [kivétele a biztonsági pontszám alól.](exempt-resource.md)



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>A felhasználók mostantól a globális rendszergazdájuktól kérhetik a bérlői szintű láthatóságot

Ha a felhasználó nem rendelkezik engedéllyel a Security Center adatainak Security Center, egy hivatkozást fog látni, amely engedélyeket kér a szervezet globális rendszergazdájától. A kérelem tartalmazza a kért szerepkört és annak indoklását.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Szalagcím, amely tájékoztatja a felhasználót, hogy bérlői szintű engedélyeket kérhet.":::

További [információ: Bérlői szintű engedélyek kérése, ha a sajátja nem elegendő.](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 előzetes verzióra vonatkozó javaslat hozzáadva az Azure biztonsági teljesítményteszt lefedettségének növeléséhez

[Az Azure biztonsági teljesítményteszt](https://docs.microsoft.com/security/benchmark/azure/introduction) a szabályzatok alapértelmezett kezdeményezése a Azure Security Center. 

A teljesítményteszt lefedettségének növelése érdekében az alábbi 35 előzetes verzióra vonatkozó javaslat lett hozzáadva a Security Center.

> [!TIP]
> Az előzetes verzióra vonatkozó javaslatok nem renderelik az erőforrásokat nem megfelelőnek, és nem szerepelnek a biztonsági pontszám kiszámításakor. Ahol csak lehetséges, szervizeljük őket, hogy az előzetes verzió időszakának végén hozzájáruljanak a pontszámhoz. További információ a javaslatokra való válaszadásról: Javaslatok szervize [a Azure Security Center.](security-center-remediate-recommendations.md)

| Biztonsági vezérlés                     | Új javaslatok                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Titkosítás engedélyezése az adattitkosításhoz            | – Azure Cosmos DB fiókoknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához<br>– Azure Machine Learning munkaterületeket ügyfél által felügyelt kulccsal (CMK) kell titkosítani<br>– A Saját kulcs használata adatvédelmet engedélyezni kell a MySQL-kiszolgálókon<br>– A Saját kulcs használata adatvédelmet engedélyezni kell a PostgreSQL-kiszolgálókhoz<br>– Cognitive Services fióknak engedélyeznie kell az adattitkosítást egy ügyfél által felügyelt kulccsal (CMK)<br>– A tárolóregisztrálókat ügyfél által felügyelt kulccsal (CMK) kell titkosítani<br>– A felügyelt SQL-példányok az ügyfelek által felügyelt kulcsokat használják az adatok titkosításához az<br>– Az SQL-kiszolgálóknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához<br>– A tárfiókok az ügyfél által felügyelt kulcsot (CMK) használják a titkosításhoz                                                                                                                                                              |
| Ajánlott biztonsági eljárások megvalósítása    | – Az előfizetések biztonsági problémák esetén egy kapcsolattartási e-mail-címmel kell, hogy rendelkezik<br> – Engedélyezni kell a Log Analytics-ügynök automatikus kiépítését az előfizetésben<br> – Engedélyezni kell a nagy súlyosságú riasztások e-mailes értesítését<br> – Engedélyezni kell a nagy súlyosságú riasztások e-mailes értesítését az előfizetés tulajdonosának<br> – A kulcstartókban engedélyezni kell a véglegesen kiürítés elleni védelmet<br> – A kulcstartókban engedélyezni kell a soft delete parancsot |
| Hozzáférés és engedélyek kezelése        | – A függvényalkalmazásokban engedélyezni kell az "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítást |
| Alkalmazások védelme DDoS-támadásokkal szemben | – Web Application Firewall (WAF) számára engedélyezni kell a Application Gateway<br> – Web Application Firewall (WAF) számára engedélyezni kell az Azure Front Door Service szolgáltatást |
| Jogosulatlan hálózati hozzáférés korlátozása | – A tűzfalat engedélyezni kell a Key Vault<br> – A privát végpontot konfigurálni kell a Key Vault<br> – App Configuration használjon privát hivatkozást<br> – Azure Cache for Redis virtuális hálózaton belül kell lennie<br> – Azure Event Grid tartománynak privát kapcsolatot kell használnia<br> – Azure Event Grid témaköröknek privát hivatkozást kell használniuk<br> – Azure Machine Learning munkaterületnek privát hivatkozást kell használnia<br> – Azure SignalR Service kapcsolat használata<br> – Azure Spring Cloud használjon hálózati injektálást<br> – A tárolóregisztrálók nem engedélyezhetik a korlátlan hálózati hozzáférést<br> – A tárolóregisztrálóknak privát kapcsolatot kell használniuk<br> – Le kell tiltani a nyilvános hálózati hozzáférést a MariaDB-kiszolgálókhoz<br> – A nyilvános hálózati hozzáférést le kell tiltani a MySQL-kiszolgálókon<br> – A nyilvános hálózati hozzáférést le kell tiltani a PostgreSQL-kiszolgálókon<br> – A tárfióknak privát kapcsolati kapcsolatot kell használnia<br> – A tárfiókok virtuális hálózati szabályokkal korlátozzák a hálózati hozzáférést<br> – A virtuálisgép Image Builder sablonnak privát hivatkozást kell használnia|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Kapcsolódó hivatkozások:

- [További információ az Azure biztonsági teljesítménytesztjről](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [További információ a Azure Database for MariaDB](../mariadb/overview.md)
- [További információ a Azure Database for MySQL](../mysql/overview.md)
- [További információ a Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>A javaslatok szűrt listájának CSV-exportálása 

2020 novemberében szűrőket adtunk hozzá a javaslatok oldalához ( A Javaslatok[lista már szűrőket is tartalmaz).](#recommendations-list-now-includes-filters) Decemberben bővítettük ezeket a szűrőket ( A Javaslatok oldalon új szűrők érhetők el a[környezethez,](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)a súlyossághoz és az elérhető válaszokhoz ). 

Ezzel a bejelentéssel módosítjuk a Letöltés CSV-fájlba gomb viselkedését, hogy a **CSV-exportálás** csak a szűrt listában aktuálisan megjelenő javaslatokat tartalmazza. 

Az alábbi képen például látható, hogy a lista két javaslatra van szűrve. A létrehozott CSV-fájl tartalmazza a két javaslat által érintett összes erőforrás állapotadatokat.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Szűrt javaslatok exportálása CSV-fájlba":::

További információ: [Biztonsági javaslatok a Azure Security Center.](security-center-recommendations.md)


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>A "Nem alkalmazható" erőforrások mostantól megfelelőként vannak jelentve a Azure Policy értékelésekben

Korábban a javaslatra kiértékelt és nem  alkalmazhatónak talált erőforrások "Nem megfelelő" Azure Policy jelentek meg. Egyetlen felhasználói művelet sem módosíthatja az állapotát "Megfelelő" állapotra. Ezzel a módosítással a rendszer "Megfelelőként" jelenti őket a jobb átláthatóság érdekében.

Az egyetlen hatás azon a Azure Policy, ahol a megfelelő erőforrások száma növekedni fog. A biztonsági pontszám nem lesz hatással a Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Biztonsági pontszámok és jogszabályi megfelelési adatok heti pillanatképének exportálása folyamatos exportálással (előzetes verzió)

A biztonsági pontszámról és a [](continuous-export.md) jogszabályi megfelelési adatokról készült heti pillanatképek exportálására használható folyamatos exportálási eszközökhöz egy új előzetes verziójú funkció érhető el.

Folyamatos exportálás definiáláskor állítsa be az exportálás gyakoriságát:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="A folyamatos exportálás gyakoriságának kiválasztása":::

- **Streamelés** – az értékelések valós időben lesznek elküldve az erőforrás állapotának frissítésekor (ha nem történik frissítés, a rendszer nem küld adatokat).
- **Pillanatképek** – Minden héten pillanatképet küldjük el az összes szabályozási megfelelőségi értékelés aktuális állapotáról (ez egy előzetes verziójú funkció a biztonsági pontszámok és a jogszabályi megfelelési adatok heti pillanatképei számára).

További információ a funkció teljes képességeiről: Folyamatos exportálás Security Center [adatokból.](continuous-export.md)

## <a name="december-2020"></a>2020. december

A decemberi frissítések a következők:

- [Azure Defender sql-kiszolgálók általánosan elérhetőek a gépeken](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender sql-támogatás Azure Synapse Analytics sql-készlet általánosan elérhető](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [A globális rendszergazdák mostantól bérlőszintű engedélyeket adhatnak maguknak](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Két új Azure Defender terv: Azure Defender DNS-hez és Azure Defender a Resource Manager (előzetes verzió)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Új biztonsági riasztások lap a Azure Portal (előzetes verzió)](#new-security-alerts-page-in-the-azure-portal-preview)
- [A Security Center élmény Azure SQL Database & SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Az eszközleltár eszközei és szűrői frissítve](#asset-inventory-tools-and-filters-updated)
- [Javaslat az SSL-tanúsítványokat kérő webalkalmazásokra, amelyek már nem részei a biztonsági pontszámnak](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [A Javaslatok lap új szűrőket tartalmaz a környezethez, a súlyossághoz és az elérhető válaszokhoz](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [A folyamatos exportálás új adattípusokat és továbbfejlesztett üzembe helyezésimegjegyzési szabályzatokat kap](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender sql-kiszolgálók általánosan elérhetőek a gépeken

Azure Security Center SQL-kiszolgálókhoz Azure Defender két csomag áll a kínálatban:

- **Azure Defender adatbázis-Azure SQL –** megvédi az Azure-natív SQL-kiszolgálókat 
- **Azure Defender gépeken található** SQL-kiszolgálókhoz – ugyanezeket a védelmet kiterjeszti az SQL-kiszolgálókra hibrid, többfelhős és helyszíni környezetekben is

Ennek a bejelentésnek a **Azure Defender az SQL-hez** mostantól védelmet nyújt az adatbázisai és adataik számára, bárhol is vannak.

Azure Defender SQL-hez való használata biztonsági rések felmérésére is képes. A sebezhetőségi felmérési eszköz a következő speciális funkciókat tartalmazza:

- **Alapkonfiguráció** (Új!) a sebezhetőségi vizsgálatok eredményeinek intelligens finomításához a valódi biztonsági problémákat képviselőkre. Miután létrehozta az alapkonfiguráció biztonsági állapotát, a sebezhetőségi felmérési eszköz csak az alapállapottól való eltéréseket jelenti. Az alapkonfigurációnak megfelelő eredmények későbbi vizsgálatoknak minősülnek. Így Ön és az elemzői oda összpontosítanak, ahol fontos.
- **Részletes teljesítményteszt-információk,** *amelyek* segítenek megérteni a felfedezett eredményeket, és hogy miért kapcsolódnak az erőforrásaihoz.
- **Szervizelési szkriptek** az azonosított kockázatok mérséklése érdekében.

További információ az [SQL-Azure Defender való működésről.](defender-for-sql-introduction.md)


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender dedikált SQL-Azure Synapse Analytics SQL-készlet SQL-támogatása általánosan elérhető

Azure Synapse Analytics (korábban SQL DW) egy elemzési szolgáltatás, amely egyesíti a vállalati adatraktározást és big data elemzéseket. A dedikált SQL-készletek a szolgáltatások vállalati adatraktározási Azure Synapse. További [információ: Mi az Azure Synapse Analytics (korábban SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)című cikk.

Azure Defender SQL-hez való használata a következőkkel védi a dedikált SQL-készleteket:

- **Komplex veszélyforrások elleni védelem** a fenyegetések és a támadások észlelésére 
- **Biztonsági rések felmérési képességei** a hibás biztonsági konfigurációk azonosításához és orvoslásához

Azure Defender SQL sql-támogatásához a Azure Synapse Analytics SQL-készletek automatikusan hozzá vannak adva a Azure SQL adatbáziscsomaghoz a Azure Security Center. A Synapse-munkaterület oldalán új "SQL-Azure Defender" lapot fog találni a Azure Portal.

További információ az [SQL Azure Defender való használatával kapcsolatban.](defender-for-sql-introduction.md)


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>A globális rendszergazdák mostantól bérlőszintű engedélyeket adhatnak maguknak

A globális rendszergazdai Azure Active Directory szerepkörű felhasználók bérlői szintű felelősségekkel is rendelkeznek, de nem rendelkeznek azure-engedélyekkel ahhoz, hogy a teljes szervezetre kiterjedő információkat megtekintsen Azure Security Center.  

Ha bérlőszintű engedélyeket szeretne hozzárendelni saját maga számára, kövesse a Bérlői szintű engedélyek megadása saját magának [útmutatását.](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Két új Azure Defender terv: Azure Defender DNS-hez és Azure Defender a Resource Manager (előzetes verzió)

Két új natív felhőbeli fenyegetésvédelmi képességet adtunk hozzá az Azure-környezethez.

Ezek az új védelmi funkciók jelentősen növelik a fenyegetést védő aknák támadásokkal szembeni rugalmasságát, és jelentősen növelik a fenyegetések által védett Azure-erőforrások Azure Defender.

- **Azure Defender a Resource Manager** – automatikusan figyeli a szervezetben végrehajtott összes erőforrás-kezelési műveletet. További információkért lásd:
    - [Bevezetés a Azure Defender-Resource Manager](defender-for-resource-manager-introduction.md)
    - [Válaszadás a Resource Managerhez készült Azure Defender-riasztásokra](defender-for-resource-manager-usage.md)
    - [Az Azure Defender által Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender DNS használata** – folyamatosan figyeli az Azure-erőforrások összes DNS-lekérdezését. További információkért lásd:
    - [A DNS-Azure Defender bemutatása](defender-for-dns-introduction.md)
    - [Válaszadás a DNS-hez készült Azure Defender-riasztásokra](defender-for-dns-usage.md)
    - [A DNS-hez Azure Defender riasztások listája](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Új biztonsági riasztások lap a Azure Portal (előzetes verzió)

Azure Security Center biztonsági riasztások lapja a következő szolgáltatásokhoz lett újratervezve:

- **Továbbfejlesztett osztályozási** élmény a riasztások számára – segít csökkenteni a riasztások eltagadását és a legfontosabb fenyegetésekre való összpontosítást. A lista testre szabható szűrőket és csoportosítási lehetőségeket tartalmaz
- **További információ a riasztások listájában** – például MITRE ATT&ACK-taktikák
- **Mintariasztásokat** tartalmazó gomb – Azure Defender képességek kiértékeléséhez és Azure Defender riasztáskonfiguráció tesztelésére (SIEM-integrációhoz, e-mail-értesítésekhez és munkafolyamat-automatizáláshoz) mintariasztásokat hozhat létre az összes Azure Defender tervből
- **Igazodás Azure Sentinel** incidensek felhasználói élményével – a mindkét terméket használatba vevő ügyfelek számára a váltás mostantól egyszerűbb, és könnyen elsajátítható egymástól
- **Nagyobb méretű riasztások** listái jobb teljesítménye
- **Navigálás billentyűzettel** a riasztások listájában
- **Riasztások a Azure Resource Graph** – az összes erőforráshoz Azure Resource Graph kusto-hez hasonló API-ban is lekérdezhet riasztásokat. Ez akkor is hasznos, ha saját riasztási irányítópultokat épít fel. [További információ a Azure Resource Graph.](../governance/resource-graph/index.yml)

Az új felhasználói élmény eléréséhez használja a biztonsági riasztások oldalának tetején található szalagcím "Kipróbálom most" hivatkozását.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Szalagcím az új előzetes verziójú riasztási felhasználói élményre mutató hivatkozással":::

Mintariasztásokat az új riasztások felhasználói élményében a Mintariasztás létrehozása Azure Defender [alapján.](security-center-alert-validation.md#generate-sample-azure-defender-alerts)


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>A Security Center felhasználói élmény Azure SQL Database & SQL Managed Instance 

Az Security Center sql-hez való hozzáférés a következő szolgáltatásokhoz biztosít hozzáférést Security Center és Azure Defender SQL-funkciókhoz:

- **Biztonsági javaslatok** – Security Center rendszeresen elemzi az összes csatlakoztatott Azure-erőforrás biztonsági állapotát a lehetséges biztonsági konfigurációk azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések orvoslásával és a szervezetek biztonsági helyzetének javításával kapcsolatban.
- **Biztonsági riasztások** – Egy észlelési szolgáltatás, amely folyamatosan figyeli a Azure SQL, például az SQL-injektálást, a találgatásos támadásokat és a jogosultságokkal való visszaélést. Ez a szolgáltatás részletes és műveletorientált biztonsági riasztásokat aktivál a Security Center-ban, és lehetőségeket biztosít a Azure Sentinel, a Microsoft azure-natív SIEM-megoldásával végzett vizsgálatok folytatására.
- **Eredmények –** Egy biztonsági rések felmérésére alkalmas szolgáltatás, amely folyamatosan figyeli Azure SQL konfigurációit, és segít a biztonsági rések megoldásában. Az értékelési vizsgálatok áttekintést nyújtanak Azure SQL biztonsági Azure SQL részletes biztonsági eredményekkel együtt.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure Security Center SQL biztonsági funkciói a szolgáltatáson belül Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Az eszközleltár eszközei és szűrői frissültek

A Azure Security Center a következő módosításokkal frissült:

- **Útmutatók és visszajelzések** az eszköztárhoz adva. Ez megnyit egy panelt, amely a kapcsolódó információkra és eszközökre mutató hivatkozásokat tartalmaz. 
- **Az előfizetések szűrője** hozzá van adva az erőforrásokhoz elérhető alapértelmezett szűrőkhöz.
- **Nyisson meg** egy lekérdezési hivatkozást, amely megnyitja az aktuális szűrési beállításokat Azure Resource Graph lekérdezésként (korábbi nevén "Megtekintés az erőforrás-gráfkezelőben").
- **Az egyes szűrők** operátori beállításai. Mostantól a "=" helyett több logikai operátor közül is választhat. Előfordulhat például, hogy meg szeretné találni az összes olyan aktív javaslatokkal rendelkezik erőforrást, amelynek a címe tartalmazza az "encrypt" sztringet. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Az operátori beállítás vezérlői az eszközleltár szűrőiben":::

További információ a leltárról: Erőforrások feltárása és [kezelése az eszközleltárával.](asset-inventory.md)


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Az SSL-tanúsítványokat kérő webalkalmazásokra vonatkozó javaslat már nem része a biztonsági pontszámnak

A "Web apps should request an SSL certificate for all incoming requests" (A webalkalmazások SSL-tanúsítványt  kell kérniük az összes bejövő kérelemhez) biztonsági vezérlő a Hozzáférés és engedélyek kezelése **(legfeljebb** 4 pts értékű) biztonsági vezérlőből átkerült az Ajánlott biztonsági eljárások implementálja (ami nem ér pontot). 

Annak biztosítása, hogy egy webalkalmazás tanúsítványt kérjen, minden bizonnyal biztonságossá teszi azt. A nyilvánosan elérhető webalkalmazások esetében azonban ez irreleváns. Ha a webhelyét HTTP-protokollon keresztül, és nem HTTPS-protokollon keresztül érik el, nem fog ügyfél-tanúsítványt kapni. Ha tehát az alkalmazás ügyféltanúsítványokat igényel, ne engedélyezze a kéréseket HTTP-protokollon keresztül az alkalmazásnak. További információ: [TLS kölcsönös hitelesítés konfigurálása a Azure App Service.](../app-service/app-service-web-configure-tls-mutual-auth.md)

Ezzel a módosítással a javaslat mostantól ajánlott eljárás, amely nem befolyásolja a pontszámot. 

Ismerje meg, hogy mely javaslatok vannak az egyes biztonsági vezérlőkben a [Biztonsági vezérlők és az ő javaslataik között.](secure-score-security-controls.md#security-controls-and-their-recommendations)


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>A Javaslatok lap új szűrőket tartalmaz a környezethez, a súlyossághoz és az elérhető válaszokhoz

Azure Security Center az összes csatlakoztatott erőforrást, és biztonsági javaslatokat hoz létre. Ezekkel a javaslatokkal megerősítheti a hibrid felhő helyzetét, és nyomon követheti a vállalatra, az iparágra és az országra vonatkozó szabályzatoknak és szabványoknak való megfelelőséget.

A Security Center és funkcióinak bővítése során a biztonsági javaslatok listája havonta növekszik. Lásd például [a 29 előzetes verzióra vonatkozó javaslatot](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)az Azure-biztonsági teljesítményteszt lefedettségének növeléséhez.

Az egyre bővülő listában szűrni kell a javaslatokat, hogy megtalálja a legnagyobb érdeklődést. Novemberben szűrőket adtunk hozzá a javaslatok oldalához (lásd: [A Javaslatok lista már szűrőket tartalmaz).](#recommendations-list-now-includes-filters)

Az ebben a hónapban hozzáadott szűrők a következő lehetőségeket kínálják a javaslatok listájának finomítására:

- **Környezet** – Az AWS-, GCP- vagy Azure-erőforrásokra (vagy bármilyen kombinációra) vonatkozó javaslatok megtekintése
- **Súlyosság** – A javaslatok megtekintése a súlyossági besorolás alapján Security Center
- **Válaszműveletek** – A gyorsjavítások, a megtagadás és a kényszerítés Security Center alapján megtekintheti a javaslatokat a válaszbeállítások rendelkezésre állása alapján.

    > [!TIP]
    > A válaszműveletek szűrője lecseréli az Elérhető **gyorsjavítás (Igen/Nem) szűrőt.** 
    > 
    > További információ az egyes válaszbeállításokról:
    > - [Gyorsjavítás szervizelése](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Helytelen konfigurációk megelőzése Kényszerítés/Megtagadás javaslatokkal](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Javaslatok biztonsági vezérlők szerint csoportosítva" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>A folyamatos exportálás új adattípusokat és továbbfejlesztett üzembe helyezésimegjegyzési szabályzatokat kap

Azure Security Center folyamatos exportálási eszközeivel exportálhatja a Security Center más monitorozási eszközökkel való használatra vonatkozó javaslatokat és riasztásokat a környezetben.

A folyamatos exportálással teljes mértékben testre szabhatja, hogy mit és hová fog exportálni. További részletekért lásd: [Folyamatos exportálási Security Center adatok.](continuous-export.md)

Ezeket az eszközöket a következő módokon bővítettük és bővítettük:

- **A folyamatos exportálás deployifnotexist szabályzata továbbfejlesztett**. A szabályzatok most:

    - **Ellenőrizze, hogy a konfiguráció engedélyezve van-e.** Ha nem, a szabályzat nem megfelelőként fog mutatni, és létrehoz egy megfelelő erőforrást. További információ a Azure Policy sablonjairól: "Üzembe helyezés nagy méretben Azure Policy lapon" A folyamatos [exportálás beállítása.](continuous-export.md#set-up-a-continuous-export)

    - **Biztonsági eredmények exportálásának támogatása.** A sablonsablonok Azure Policy a folyamatos exportálást úgy konfigurálhatja, hogy tartalmazza az eredményeket. Ez akkor releváns, ha olyan javaslatokat exportál, amelyek "sub" javaslatokkal is szolgálnak, például a biztonsági rések felmérésére vonatkozó ellenőrzők eredményei vagy a "Rendszerfrissítések telepítve kell a gépekre" "szülő" javaslat adott rendszerfrissítései.
    
    - **A biztonsági pontszámadatok exportálásának támogatása.**

- **Előírásoknak való megfelelés felmérésére vonatkozó adatok hozzáadva (előzetes verzióban).** Mostantól folyamatosan exportálhatja a jogszabályi megfelelőségi értékelések frissítéseit, beleértve az egyéni kezdeményezéseket is, egy Log Analytics-munkaterületre vagy eseményközpontba. Ez a funkció országos/szuverén felhőkben nem érhető el.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="A szabályozásnak megfelelő értékelési információk folyamatos exportálási adatokkal való megszétlése.":::


## <a name="november-2020"></a>2020. november

Novemberi frissítések többek között a következők:

- [29 előzetes verzióra vonatkozó javaslat az Azure-biztonsági teljesítményteszt lefedettségének növeléséhez](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 hozzáadva Security Center megfelelőségi irányítópultjához](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [A javaslatok listája már szűrőket is tartalmaz](#recommendations-list-now-includes-filters)
- [Továbbfejlesztett és bővített automatikus kiépítési élmény](#auto-provisioning-experience-improved-and-expanded)
- [A biztonsági pontszám mostantól elérhető a folyamatos exportálásban (előzetes verzió)](#secure-score-is-now-available-in-continuous-export-preview)
- ["A rendszerfrissítéseket telepíteni kell a gépeken" javaslat mostantól tartalmazza az alparancsokat](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [A szabályzatkezelési oldal Azure Portal megjeleníti az alapértelmezett szabályzat-hozzárendelések állapotát](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 előzetes verzióra vonatkozó javaslat az Azure-biztonsági teljesítményteszt lefedettségének növelése érdekében

Az Azure biztonsági teljesítménytesztje a Microsoft által írt, Azure-specifikus irányelvek készlete a közös megfelelőségi keretrendszeren alapuló biztonsági és megfelelőségi ajánlott eljárásokhoz. [További tudnivalók az Azure-biztonsági teljesítménytesztről](https://docs.microsoft.com/security/benchmark/azure/introduction).

Az alábbi 29 előzetes javaslat érhető el a Security Center a teljesítményteszt lefedettségének növelése érdekében.

Az előzetes verzióra vonatkozó javaslatok nem renderelik az erőforrásokat nem megfelelőnek, és nem szerepelnek a biztonsági pontszám kiszámításakor. Ahol csak lehetséges, szervizeljük őket, hogy az előzetes verzió időszakának végén hozzájáruljanak a pontszámhoz. További információ a javaslatokra való válaszadásról: Javaslatok szervize [a Azure Security Center.](security-center-remediate-recommendations.md)

| Biztonsági vezérlés                     | Új javaslatok                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Átvitel közbeni adatok titkosítása              | – Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz<br>– Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz<br>– A TLS-t frissíteni kell az API-alkalmazás legújabb verziójára<br>– A TLS-t frissíteni kell a függvényalkalmazás legújabb verziójára<br>– A TLS-t frissíteni kell a webalkalmazás legújabb verziójára<br>– AZ FTPS-nek kötelezőnek kell lennie az API-alkalmazásban<br>– FtpS-re van szükség a függvényalkalmazásban<br>– A webalkalmazásban FTPS-re van szükség                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Hozzáférés és engedélyek kezelése        | – A webalkalmazásnak SSL-tanúsítványt kell igényelni minden bejövő kéréshez<br>– A felügyelt identitást az API-alkalmazásban kell használni<br>– A felügyelt identitást a függvényalkalmazásban kell használni<br>– A felügyelt identitást a webalkalmazásban kell használni                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Jogosulatlan hálózati hozzáférés korlátozása | – A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz<br>– A privát végpontot engedélyezni kell a MariaDB-kiszolgálókhoz<br>– Engedélyezni kell a privát végpontot a MySQL-kiszolgálókhoz                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Naplózás és naplózás engedélyezése          | – A diagnosztikai naplókat App Services engedélyezni kell                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Ajánlott biztonsági eljárások megvalósítása    | – Azure Backup virtuális gépeken engedélyezni kell a virtuális gépeket<br>– A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB<br>– A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL<br>– A georedundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL<br>– A PHP-t frissíteni kell az API-alkalmazás legújabb verziójára<br>– A PHP-t frissíteni kell a webalkalmazás legújabb verziójára<br>– A Java-t frissíteni kell az API-alkalmazás legújabb verziójára<br>– A Java-t frissíteni kell a függvényalkalmazás legújabb verziójára<br>– A Java-t frissíteni kell a webalkalmazás legújabb verziójára<br>– A Pythont frissíteni kell az API-alkalmazás legújabb verziójára<br>– A Pythont frissíteni kell a függvényalkalmazás legújabb verziójára<br>– A Pythont frissíteni kell a webalkalmazás legújabb verziójára<br>– Az SQL-kiszolgálók naplózási megőrzését legalább 90 napra kell állítani |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Kapcsolódó hivatkozások:

- [További információ az Azure biztonsági teljesítménytesztjről](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [További információ az Azure API-alkalmazásokról](../app-service/app-service-web-tutorial-rest-api.md)
- [További információ az Azure-függvényalkalmazásokkal kapcsolatban](../azure-functions/functions-overview.md)
- [További információ az Azure-webalkalmazásokkal kapcsolatban](../app-service/overview.md)
- [További információ a Azure Database for MariaDB](../mariadb/overview.md)
- [További információ a Azure Database for MySQL](../mysql/overview.md)
- [További információ a Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 hozzáadva Security Center megfelelőségi irányítópultjához

Az NIST SP 800-171 R2 szabvány mostantól beépített kezdeményezésként érhető el a Azure Security Center megfelelőségi irányítópultjához. A vezérlők leképezését a [Details of the NIST SP 800-171 R2 Regulatory Compliance (NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md)szabályozási megfelelőség) beépített kezdeményezés részleteiben ismertetjük. 

A szabványok előfizetésre való alkalmazásához és a megfelelőségi állapot folyamatos figyeléséhez kövesse a Következő útmutatót: Szabványkészlet testreszabása a jogszabályi megfelelési [irányítópulton.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Az NIST SP 800 171 R2 szabvány a Security Center megfelelőségi irányítópultján":::

További információ erről a megfelelőségi szabványról: [NIST SP 800-171 R2.](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)


### <a name="recommendations-list-now-includes-filters"></a>A javaslatok listája már szűrőket is tartalmaz

Most már számos feltételnek megfelelően szűrheti a biztonsági javaslatok listáját. A következő példában a javaslatok listája úgy van szűrve, hogy a következő javaslatokat mutassa:

- általánosan **elérhetők** (ez nem előzetes verzió)
- **tárfiókok esetén**
- gyorsjavítás **támogatása** a szervizeléshez

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="A javaslatok listájának szűrői":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Továbbfejlesztett és bővített automatikus kiépítési élmény

Az automatikus kiépítési funkció segít csökkenteni a felügyeleti terhelést azáltal, hogy a szükséges bővítményeket új – és meglévő – Azure-beli virtuális gépekre telepíti, hogy kihasználják a Security Center által védelmet. 

A Azure Security Center növekedésével egyre több bővítményt fejlesztettek ki, és Security Center az erőforrástípusok nagyobb listáját figyelheti. Az automatikus kiépítési eszközök mostantól más bővítmények és erőforrástípusok támogatására is kibővültek a szolgáltatás Azure Policy.

Most már konfigurálhatja a következő automatikus kiépítést:

- Log Analytics-ügynök
- (Új) Azure Policy Bővítmény a Kuberneteshez
- (Új) Microsoft Függőségi ügynök

További [információ: Ügynökök és](security-center-enable-data-collection.md)bővítmények automatikus kiépítése a Azure Security Center.


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>A biztonsági pontszám mostantól elérhető a folyamatos exportálásban (előzetes verzió)

A biztonsági pontszám folyamatos exportálásával valós időben streamelheti a pontszám módosításait egy Azure Event Hubs Log Analytics-munkaterületre. Ezzel a képességgel a következő lehetőségeket használhatja:

- biztonsági pontszám nyomon követése dinamikus jelentésekkel
- biztonsági pontszám adatainak exportálása Azure Sentinel (vagy bármely más SIEM-be)
- ezeket az adatokat integrálhatja azokkal a folyamatokkal, amelyek esetleg már a szervezetben a biztonsági pontszám monitorozására vannak használva

További információ az adatok [folyamatos exportálásának Security Center.](continuous-export.md)


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"A rendszerfrissítéseket telepíteni kell a gépeken" javaslat mostantól tartalmazza az alparancsokat

A **rendszerfrissítéseket telepíteni kell a gépekre** javaslat továbbfejlesztve. Az új verzió minden hiányzó frissítéshez tartalmaz alparancsokat, és a következő fejlesztéseket tartalmazza:

- Újratervezett élmény a Azure Security Center oldalának Azure Portal. A rendszerfrissítések  javaslati részleteit tartalmazó oldalt az alább látható módon kell telepíteni a gépekre. Egyetlen keresés kiválasztásakor megnyílik a Részletek panel, amely a szervizelési információkra és az érintett erőforrások listájára mutató hivatkozást tartalmaz.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="A frissített javaslathoz a portál egyik alparancsának megnyitása a portálon":::

- A javaslat bővített adatai a Azure Resource Graph (ARG) alapján. Az ARG egy olyan Azure-szolgáltatás, amely hatékony erőforrás-feltárást biztosít. Az ARG használatával nagy méretekben is lekérdezhet egy adott előfizetés-készletet, így hatékonyan irányíthatja a környezetet. 

    A Azure Security Center az ARG és a [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/) használatával számos biztonsági adatokat lekérdezhet.

    Korábban, ha lekérdezte ezt a javaslatot az ARG-ben, az egyetlen elérhető információ az volt, hogy a javaslatot egy gépen kell szervizni. A bővített verzió alábbi lekérdezése gép szerint csoportosított, hiányzó rendszerfrissítéseket ad vissza.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>A szabályzatkezelési oldal Azure Portal alapértelmezett szabályzat-hozzárendelések állapotát jeleníti meg

Most már láthatja, hogy az előfizetéséhez hozzá van-e rendelve az  alapértelmezett Security Center szabályzat a Security Center szabályzat lapján a Azure Portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Az alapértelmezett szabályzat-hozzárendeléseket Azure Security Center szabályzatkezelési oldal":::