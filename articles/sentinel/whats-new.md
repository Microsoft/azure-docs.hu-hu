---
title: A Azure Sentinel
description: Ez a cikk az elmúlt Azure Sentinel új funkcióit ismerteti.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7f9a8cb54458999d8f20a258bc36241dfdbd0de8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376035"
---
# <a name="whats-new-in-azure-sentinel"></a>A Azure Sentinel

Ez a cikk a szolgáltatáshoz hozzáadott Azure Sentinel és a kapcsolódó szolgáltatások új funkcióit sorolja fel, amelyek továbbfejlesztett felhasználói élményt biztosítanak a Azure Sentinel.

A korábbi funkciókról a Tech Community blogja [nyújt tájékoztatást.](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)

A feljegyzett funkciók jelenleg előzetes verzióban állnak rendelkezésre. Az [Azure előzetes verzió kiegészítő feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) további jogi feltételeket tartalmaznak, amelyek a bétaverzióban, előzetes verzióban vagy más, általánosan elérhető Azure-funkciókra vonatkoznak.


> [!TIP]
> A Microsoft veszélyforrás-keresési csapatai lekérdezésekkel, forgatókönyvekkel, munkafüzetekkel és jegyzetfüzetekkel járulnak hozzá a [Azure Sentinel közösségének,](https://github.com/Azure/Azure-Sentinel)beleértve a csapatai által adaptálható és használható speciális veszélyforrás-keresési lekérdezéseket is. [](https://github.com/Azure/Azure-Sentinel)
>
> Ön is közreműködhet! Csatlakozzon hozzánk a [Azure Sentinel ThreatHub-közösségében.](https://github.com/Azure/Azure-Sentinel/wiki)
>

## <a name="april-2021"></a>2021. április

- [Incidens idővonala (nyilvános előzetes verzió)](#incident-timeline-public-preview)

### <a name="incident-timeline-public-preview"></a>Incidens idővonala (nyilvános előzetes verzió)

Az incidens részleteit tartalmazó oldal első lapja most az Idővonal, amely az incidensben lévő riasztások és könyvjelzők idővonalát jeleníti meg. Az incidens idővonala segíthet jobban megérteni az incidenst, és rekonstruálni a támadói tevékenység idővonalát a kapcsolódó riasztások és könyvjelzők között.

- Jelöljön ki egy elemet az idősoron, hogy az incidens környezetének elhagyása nélkül lássa annak részleteit
- Szűrje az idővonal tartalmát, hogy csak riasztásokat vagy könyvjelzőket, illetve egy adott súlyosságú vagy MITRE-taktikával megjelölt elemeket mutasson.
- A Rendszerriasztás **azonosítója** hivatkozásra kattintva megtekintheti a teljes rekordot, vagy az **Események** hivatkozást a kapcsolódó események megtekintéséhez a **Naplók területen.**

Például:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Incidens idővonal lapja":::

További információ: [Oktatóanyag: Incidensek kivizsgálása a Azure Sentinel.](tutorial-investigate-cases.md)

## <a name="march-2021"></a>2021. március

- [Munkafüzetek automatikus frissítésének beállítása megtekintési módban](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Új észlelések a Azure Firewall](#new-detections-for-azure-firewall)
- [Automatizálási szabályok és incidens által](#automation-rules-and-incident-triggered-playbooks) aktivált forgatókönyvek (beleértve a teljesen új forgatókönyv dokumentációját)
- [Új riasztások bővülése: bővített entitásleképezés és egyéni részletek](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Kinyomtathatja a Azure Sentinel munkafüzeteket, vagy pdf-fájlként mentheti őket](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Az incidensszűrők és a rendezési beállítások mostantól mentve vannak a munkamenetben (nyilvános előzetes verzió)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender-incidensek integrációja (nyilvános előzetes verzió)](#microsoft-365-defender-incident-integration-public-preview)
- [Új Microsoft-szolgáltatás-összekötők Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Munkafüzetek automatikus frissítésének beállítása megtekintési módban

Azure Sentinel a felhasználók mostantól az új Azure Monitor [a](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) munkafüzet adatainak automatikus frissítésére egy megtekintési munkamenet során.

Az időközi beállítások megjelenítéséhez minden munkafüzetben vagy munkafüzetsablonban válassza az :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Automatikus** frissítés lehetőséget. Válassza ki az aktuális megtekintési munkamenethez használni kívánt lehetőséget, majd válassza az **Alkalmaz lehetőséget.**

- A támogatott frissítési időközök **5 perctől 1** **napig esnek.**
- Alapértelmezés szerint az automatikus frissítés ki van kapcsolva. A teljesítmény optimalizálása érdekében az automatikus frissítés is ki van kapcsolva minden alkalommal, amikor bezár egy munkafüzetet, és nem fut a háttérben. A munkafüzet következő megnyitásakor szükség szerint kapcsolja be újra az automatikus frissítést.
- Az automatikus frissítés szünetel a munkafüzet szerkesztése közben, és az automatikus frissítési időközök minden alkalommal újraindulnak, amikor visszakapcsol szerkesztési módból megtekintési módra.

    Az időközök akkor is újraindulnak, ha manuálisan frissíti a munkafüzetet a Frissítés :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **gombbal.**

További információkért [lásd: Oktatóanyag: Az](tutorial-monitor-your-data.md) adatok vizualizációja és figyelése, valamint [a Azure Monitor dokumentációja.](../azure-monitor/visualize/workbooks-overview.md)

### <a name="new-detections-for-azure-firewall"></a>Új észlelések a Azure Firewall

A rendszer számos, a szolgáltatáshoz Azure Firewall, az [Elemzés](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) területhez hozzáadott, Azure Sentinel. Ezekkel az új észlelésekkel a biztonsági csapatok riasztásokat kapnak, ha a belső hálózaton található gépek az észlelési szabály lekérdezésében meghatározottak szerint internetes tartományneveket vagy ismert I/O-khoz társított IP-címeket próbálnak lekérdezni vagy csatlakozni hozzájuk.

Az új észlelések a következők:

- [Fogarasi hálózati adatgyűjtő jelek](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Ismert GALLIUM-tartományok és -hashek](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Ismert IRIDIUM IP-cím](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Ismert tartomány-/IP-csoporttartományok a Különböző csoportokban](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [A DCU-lekért terület THALLIUM-tartománya](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Ismert, a MALDOC-maldochoz kapcsolódó kivonat](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Ismert STRONTIUM-csoporttartományok](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [ARRAIUM – Tartományi és IP-I/O-k – 2021. március](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Az Azure Firewall észlelései folyamatosan hozzáadva a beépített sablonkatatárhoz. Az adatforrások legújabb észlelésének Azure Firewall a **Szabálysablonok** alatt szűrje az adatforrásokat **a** **Azure Firewall:**

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Új észlelések az Analytics hatékonysági munkafüzetében":::

További információ: [Új észlelések](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958)a Azure Firewall a Azure Sentinel.

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Automatizálási szabályok és incidens által aktivált forgatókönyvek

Az automatizálási szabályok új fogalmat Azure Sentinel, amely lehetővé teszi az incidenskezelés automatizálásának központi kezelését. Amellett, hogy lehetővé teszi, hogy forgatókönyveket rendeljen incidenshez (nem csak a riasztások számára, mint korábban), az automatizálási szabályok lehetővé teszik több elemzési szabályra adott válaszok automatizálását egyszerre, az incidensek automatikus címkézését, hozzárendelését vagy lezárását anélkül, hogy forgatókönyveket kellene végrehajtani, és szabályozni tudja a végrehajtandó műveletek sorrendjét. Az automatizálási szabályok leegyszerűsítik az automatizálási Azure Sentinel, és lehetővé teszik az incidens-vezénylési folyamatok összetett munkafolyamatainak egyszerűsítését.

További információért olvassa el az [automatizálási szabályok teljes magyarázatát.](automate-incident-handling-with-automation-rules.md)

Ahogy korábban említettük, a forgatókönyveket a riasztási eseményindító mellett az incidens eseményindítóval is aktiválhatja. Az incidens eseményindítója nagyobb bemeneti adatokat biztosít a forgatókönyv számára (mivel az incidens tartalmazza az összes riasztási és entitásadatot is), így még nagyobb teljesítményt és rugalmasságot biztosít a válasz-munkafolyamatokban. Az incidens által aktivált forgatókönyveket automatizálási szabályokból hívjuk meg.

További információ a forgatókönyvekkel kapcsolatos továbbfejlesztett [](tutorial-respond-threats-playbook.md) [képességekről,](automate-responses-with-playbooks.md)valamint a forgatókönyvekkel és automatizálási szabályokkal való válasz-munkafolyamatok felhasználásával való meghozásról.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Új riasztások bővülése: bővített entitásleképezés és egyéni részletek

A riasztásokat két új módon bővíti, hogy használhatóbbak és informatívabbak.

Első lépésként az entitásleképezést a következő szintre kell felhozni. Most már szinte 20 entitást leképezhet felhasználókra, gazdagépekre és IP-címekre, fájlokra és folyamatokra, postaládákra, Azure-erőforrásokra és IoT-eszközökre. Minden entitáshoz több azonosítót is használhat az egyedi azonosítás megerősítéséhez. Ez sokkal gazdagabb adatkészletet biztosít az incidensek során, szélesebb korrelációt és hatékonyabb vizsgálatot biztosítva. [Ismerje meg a riasztások entitások leképezésének](map-data-fields-to-entities.md) új módját.

[További információ az entitásokról,](entities-in-azure-sentinel.md) valamint az elérhető entitások és azonosítóik [teljes listája.](entities-reference.md)

A riasztások testreszabásával még nagyobb hatékonyságot adhat a vizsgálati és reagálási képességeknek a nyers események részleteinek kijelzésére. Az eseménytartalmak láthatóságának biztosítása az incidensekbe, így nagyobb hatékonyságot és rugalmasságot biztosít a biztonsági fenyegetésekre való reagálás és a fenyegetések kivizsgálása terén. [Megtudhatja, hogyan hozhat létre egyéni részleteket](surface-custom-details-in-alerts.md) a riasztások között.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Kinyomtathatja a Azure Sentinel munkafüzeteket, vagy pdf-fájlként mentheti őket

Mostantól kinyomtathat Azure Sentinel munkafüzeteket, így pdf-fájlba is exportálhatja őket, és helyileg vagy megosztással mentheti őket.

A munkafüzetben válassza a Tartalom nyomtatása > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **menüt.** Ezután jelölje ki a nyomtatót, vagy válassza a **Mentés PDF-fájlként lehetőséget.**

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Nyomtassa ki a munkafüzetet, vagy mentse PDF-fájlként.":::

További információ: [Oktatóanyag: Adatok vizualizációja és monitorezése.](tutorial-monitor-your-data.md)

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Incidensszűrők és rendezési beállítások mentése a munkamenetbe (nyilvános előzetes verzió)

Az incidensszűrők és -rendezés mostantól a teljes Azure Sentinel mentve lesz, még akkor is, ha a termék más területeire navigál.
Ha még mindig ugyanabban a munkamenetben van, az [](tutorial-investigate-cases.md) Incidensek területre visszatérve a Azure Sentinel ugyanúgy jeleníti meg a szűrőket és a rendezést, ahogy azt elhagyta.

> [!NOTE]
> A rendszer nem menti az incidensszűrőket és a rendezést, miután Azure Sentinel vagy frissítette a böngészőt.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender-incidensek integrációja (nyilvános előzetes verzió)

Azure Sentinel Microsoft 365 Defender [(M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) incidensintegrációja lehetővé teszi, hogy az összes M365D-incidenst streamelje az Azure Sentinel és szinkronizálja őket mindkét portál között. Az M365D (korábbi nevén Microsoft Veszélyforrások elleni védelem vagy MTP) incidensei tartalmazzák az összes kapcsolódó riasztást, entitást és releváns információt, így elegendő kontextust biztosítva az osztályozáshoz és az előzetes vizsgálathoz a Azure Sentinel. A Sentinelben az Incidensek kétirányú szinkronban maradnak az M365D-ben, így kihasználhatja mindkét portál előnyeit az incidens vizsgálata során.

Az Azure Sentinel és Microsoft 365 Defender együttes használata mindkét világ legjobbját biztosítja. A SIEM által a teljes szervezetben rendelkezésre álló információforrás-hatókört, valamint az XDR által az Microsoft 365-erőforrások védelme érdekében az Microsoft 365-erőforrások védelmére rendelkezésre álló testreszabott és testre szabott vizsgálati teljesítményt is biztosítja, amelyek a zökkenőmentes SOC-műveletek érdekében szinkronizálva és koordinálva is rendelkezésre áll.

További információ: [Microsoft 365 Defender integrációja a Azure Sentinel.](microsoft-365-defender-sentinel-integration.md)

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Új Microsoft-szolgáltatás-összekötők Azure Policy

[Azure Policy](../governance/policy/overview.md) egy Azure-szolgáltatás, amely lehetővé teszi, hogy szabályzatok használatával kényszerítsen és vezérelni egy erőforrás tulajdonságait. A szabályzatok használata biztosítja, hogy az erőforrások megfeleljenek az it-irányítási szabványoknak.

A szabályzatokkal vezérelhető erőforrások tulajdonságai között a diagnosztikai és auditnaplók létrehozása és kezelése áll. Azure Sentinel a Azure Policy segítségével a diagnosztikai naplók beállításainak közös készletét alkalmazhatja egy adott típusú (aktuális és jövőbeli) erőforrásra, amelynek a naplóit be szeretné Azure Sentinel. A Azure Policy erőforrásnak köszönhetően nem kell erőforrás szerint beállítania a diagnosztikai naplók beállításait.

Azure Policy-alapú összekötők már elérhetők a következő Azure-szolgáltatásokhoz:
- [Azure Key Vault](connect-azure-key-vault.md) (nyilvános előzetes verzió)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (nyilvános előzetes verzió)
- Azure SQL/kiszolgálók (GA)

Az ügyfelek továbbra is manuálisan küldhetik el a naplókat adott példányokhoz, és nem kell használni a szabályzatmotort.

## <a name="february-2021"></a>2021. február

- [Cybersecurity Maturity Model Certification (CMMC) munkafüzet](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Külső adat-összekötők](#third-party-data-connectors)
- [UEBA-elemzések az entitás oldalán (nyilvános előzetes verzió)](#ueba-insights-in-the-entity-page-public-preview)
- [Továbbfejlesztett incidenskeresés (nyilvános előzetes verzió)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Cybersecurity Maturity Model Certification (CMMC) munkafüzet

Az Azure Sentinel CMMC-munkafüzet olyan mechanizmust biztosít, amely lehetővé teszi a Microsoft-portfólió CMMC-vezérlőihez igazított naplólekérdezések megtekintését, beleértve többek között a Microsoft biztonsági ajánlatait, az Office 365-öt, a Teamset, az Intune-t és Windows Virtual Desktop.

A CMMC-munkafüzet lehetővé teszi a biztonsági tervezők, mérnökök, biztonsági üzemeltetési elemzők, vezetők és informatikai szakemberek számára, hogy helyzettudatosságot szerezzenek a felhőalapú számítási feladatok biztonsági helyzetével kapcsolatban. Emellett javaslatok is rendelkezésre állnak a Microsoft-ajánlatok kiválasztásához, tervezéséhez, üzembe helyezéséhez és konfigurálásához a CMMC követelményeinek és gyakorlatának megfelelően.

Még ha nem is kell megfelelnie a CMMC-nek, a CMMC-munkafüzet hasznos lehet a biztonsági műveleti központok kialakításában, a riasztások kialakításában, a fenyegetések megjelenítésében és a számítási feladatok helyzetre vonatkozó tájékoztatásában.

A CMMC-munkafüzet a Azure Sentinel **területen érhető** el. Válassza **a Sablon** lehetőséget, majd keressen rá a **CMMC kifejezésre.**

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="A CMMC-munkafüzet útmutatóját be- és kikapcsolni" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


További információkért lásd:

- [Azure Sentinel Cybersecurity Maturity Model Certification (CMMC) munkafüzet](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Oktatóanyag: Az adatok megjelenítése és figyelése](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Külső adat-összekötők

A harmadik féltől származó integrációk gyűjteménye folyamatosan bővül, és az elmúlt két hónapban 30 összekötőt adtak hozzá. Megjelenik egy lista:

- [Agari Adathalász védelem és márkavédelem](connect-agari-phishing-defense.md)
- [Akamai biztonsági események](connect-akamai-security-events.md)
- [Alsid a Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP-kiszolgáló](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google-munkaterület (korábban G Suite)](connect-google-workspace.md)
- [Imperva WAF-átjáró](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS-naplók](connect-nxlog-dns.md)
- [NXLog Linux-naplózás](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys biztonsági rések kezelésével kapcsolatos tudásbázis](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>UEBA-elemzések az entitás oldalán (nyilvános előzetes verzió)

A Azure Sentinel-adatok oldalai egy [](identify-threats-with-entity-behavior-analytics.md#entity-insights)Elemzés panelt biztosítanak, amely viselkedési elemzéseket jelenít meg az entitásról, és segít gyorsan azonosítani az anomáliákat és a biztonsági fenyegetéseket.

Ha az UEBA engedélyezve van, és legalább négy napos időkeretet választott ki, akkor ez az Elemzések panel az [UEBA-elemzések](ueba-enrichments.md)következő új szakaszait is tartalmazza:

|Section  |Leírás  |
|---------|---------|
|**UEBA-elemzések**     | Összegzi a rendellenes felhasználói tevékenységeket: <br>- Földrajzi helyek, eszközök és környezetek között<br>– Idő- és gyakorisági horizontok szerint a felhasználó saját előzményeihez képest <br>– A társviszonyok viselkedésével összehasonlítva <br>– A szervezet viselkedéséhez képest     |
|**Felhasználói társviszonyok biztonságicsoport-tagság alapján**     |   Felsorolja a felhasználó társviszonyait az Azure AD biztonsági csoportok tagsága alapján, és a biztonsági műveleti csapatok számára biztosítja a hasonló engedélyekkel rendelkező többi felhasználó listáját.  |
|**Felhasználói hozzáférési engedélyek az Azure-előfizetéshez**     |     Megjeleníti a felhasználó hozzáférési engedélyét a közvetlenül vagy Azure AD-csoportokon/szolgáltatásnévn keresztül elérhető Azure-előfizetések számára.   |
|**A felhasználóhoz kapcsolódó fenyegetésjelzők**     |  A felhasználó tevékenységeiben ábrázolt IP-címekre vonatkozó ismert fenyegetések gyűjteményét sorolja fel. A fenyegetések a fenyegetések típusa és a család szerint vannak felsorolva, és a Microsoft fenyegetésintelligencia-szolgáltatása bővíti őket.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Továbbfejlesztett incidenskeresés (nyilvános előzetes verzió)

Továbbfejlesztettük a Azure Sentinel keresési élményét, így gyorsabban navigálhat az incidensek között egy adott fenyegetés kivizsgálása során.

Amikor incidenseket keres a Azure Sentinel, a következő incidensadatok alapján kereshet:

- ID (Azonosító)
- Cím
- Termék
- Tulajdonos
- Címke

## <a name="january-2021"></a>2021. január

- [Elemzési szabály varázsló: Továbbfejlesztett lekérdezésszerkesztési élmény (nyilvános előzetes verzió)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az.SecurityInsights PowerShell-modul (nyilvános előzetes verzió)](#azsecurityinsights-powershell-module-public-preview)
- [SQL Database-összekötő](#sql-database-connector)
- [Dynamics 365-összekötő (nyilvános előzetes verzió)](#dynamics-365-connector-public-preview)
- [Továbbfejlesztett incidens-megjegyzések](#improved-incident-comments)
- [Dedikált Log Analytics-fürtök](#dedicated-log-analytics-clusters)
- [Logic Apps felügyelt identitások](#logic-apps-managed-identities)
- [Továbbfejlesztett szabály-finomhangolás az elemzési szabály előzetes verziójú grafikonjai segítségével](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Elemzési szabály varázsló: Továbbfejlesztett lekérdezésszerkesztési élmény (nyilvános előzetes verzió)

A Azure Sentinel ütemezett elemzési szabály varázslója mostantól a következő fejlesztéseket biztosítja a lekérdezések írása és szerkesztése során:

-   Egy kibontható szerkesztőablak, amely több képernyőterületet biztosít a lekérdezés megtekintéséhez.
-   Kulcsszavak kiemelése a lekérdezési kódban.
-   Bővített automatikus kiegészítés támogatása.
-   Valós idejű lekérdezés-ellenőrzések. A lekérdezés hibái mostantól piros blokkként mutatja a görgetősávot, és piros pontként a **Szabálylogika** beállítása lap neve alatt. Ezenkívül a hibákkal kapcsolatos lekérdezések nem menthetők.

További információ: [Oktatóanyag: Egyéni elemzési szabályok létrehozása a fenyegetések észlelésére.](tutorial-detect-threats-custom.md)
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell-modul (nyilvános előzetes verzió)

Azure Sentinel már támogatja az új [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell-modult.

Az **Az.SecurityInsights** modul támogatja a gyakori Azure Sentinel-használati eseteket, például az incidensekkel való interakciót a incidensek módosítása, súlyosság, tulajdonos stb., megjegyzések és címkék hozzáadása az incidensekhez, valamint könyvjelzők létrehozása.

Bár a [CI Azure Resource Manager](../azure-resource-manager/templates/index.yml) CD-folyamathoz ajánlott arm-sablonokat használni, az **Az.SecurityInsights** modul hasznos az üzembe helyezés utáni feladatokhoz, és soC-automatizálásra van kicélzva.  Az SOC-automatizálás tartalmazhat például adat-összekötők konfigurálásának lépéseit, elemzési szabályokat hozhat létre, vagy automatizálási műveleteket adhat hozzá az elemzési szabályokhoz.

További információkért, beleértve az elérhető parancsmagok teljes listáját és leírását, a paraméterleírásokat és példákat, tekintse meg az [Az.SecurityInsights PowerShell-dokumentációját.](/powershell/module/az.securityinsights/)

### <a name="sql-database-connector"></a>SQL Database-összekötő

Azure Sentinel egy Azure SQL-összekötőt biztosít, amellyel az adatbázisok naplózási és diagnosztikai naplóit streamelheti az Azure Sentinel-ba, és folyamatosan figyelheti az összes példány tevékenységét.

Azure SQL egy teljes körűen felügyelt, szolgáltatásként elérhető (PaaS) adatbázismotor, amely a legtöbb adatbázis-kezelési funkciót kezeli, például a frissítés, a javítás, a biztonsági mentések és a monitorozás felhasználói beavatkozás nélkül.

További információ: [Connect Azure SQL database diagnostics and auditing logs (Adatbázis-diagnosztikai és auditnaplók csatlakoztatása).](connect-azure-sql-logs.md)

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365-összekötő (nyilvános előzetes verzió)

Azure Sentinel már biztosít egy összekötőt a Microsoft Dynamics 365-höz, amellyel a Dynamics 365-alkalmazások felhasználói, rendszergazdai és támogatási tevékenységnaplóit gyűjtheti Azure Sentinel. Ezeket az adatokat felhasználhatja a teljes adatfeldolgozási művelet naplózására és az esetleges biztonsági incidensek elemzésére.

További információ: [Dynamics 365-tevékenységnaplók csatlakoztatása Azure Sentinel.](connect-dynamics-365.md)

### <a name="improved-incident-comments"></a>Továbbfejlesztett incidens-megjegyzések

Az elemzők az incidensek megjegyzései alapján dolgoznak együtt az incidensekkel, dokumentálják a folyamatokat és lépéseket manuálisan vagy egy forgatókönyv részeként. 

Továbbfejlesztett incidens-megjegyzések használata lehetővé teszi a megjegyzések formázását, valamint a meglévő megjegyzések szerkesztését vagy törlését.

További információ: [Incidensek automatikus létrehozása a Microsoft biztonsági riasztásaiból.](create-incidents-from-alerts.md)
### <a name="dedicated-log-analytics-clusters"></a>Dedikált Log Analytics-fürtök

Azure Sentinel már támogatja a dedikált Log Analytics-fürtök üzembe helyezési lehetőségként való beállítását. Javasoljuk, hogy a következőkor érdemes megfontolni egy dedikált fürt használatát:

- **Naponta több mint 1 TB** bemenő száma a Azure Sentinel munkaterületre
- **Több munkaterület Azure Sentinel az** Azure-regisztrációban

A dedikált fürtök lehetővé teszik olyan szolgáltatások használatát, mint az ügyfelek által kezelt kulcsok, a zárolási kulcs, a dupla titkosítás és a gyorsabb munkaterületközi lekérdezések, ha több munkaterület van ugyanazon a fürtön.

További információ: dedikált [Azure Monitor naplók létrehozása.](../azure-monitor/logs/logs-dedicated-clusters.md)

### <a name="logic-apps-managed-identities"></a>Logic Apps felügyelt identitások

Azure Sentinel már támogatja a Azure Sentinel Logic Apps-összekötő felügyelt identitását, így lehetővé teszi, hogy további identitások létrehozása helyett közvetlenül egy adott forgatókönyvnek adjon engedélyeket a Azure Sentinel való működéshez.

- **Felügyelt identitás nélkül a** Logic Apps-összekötőhöz külön identitásra van szükség egy Azure Sentinel RBAC-szerepkörhöz, hogy a Azure Sentinel. A különálló identitás lehet egy Azure AD-felhasználó vagy egy szolgáltatásnév, például egy Azure AD-ban regisztrált alkalmazás.

- **A felügyelt identitás támogatásának bekapcsolása** a logikai alkalmazásban regisztrálja a logikai alkalmazást az Azure AD-ban, és egy objektumazonosítót ad meg. A munkaterület objektumazonosítójával Azure Sentinel logikai alkalmazást egy Azure RBAC-szerepkörhöz rendelheti hozzá a Azure Sentinel munkaterületen. 

További információkért lásd:

- [Hitelesítés felügyelt identitással a Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Azure Sentinel Logic Apps összekötő dokumentációja](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Továbbfejlesztett szabályhangolás az elemzési szabály előzetes verziójú grafikonjai segítségével (nyilvános előzetes verzió)

Azure Sentinel segítségével jobban finomhangolhatja az elemzési szabályokat, így növelheti azok pontosságát és csökkentheti a zajt.

Miután szerkesztett egy elemzési szabályt a **Szabálylogika** beállítása lapon, keresse meg az Eredmények **szimulációja** területet a jobb oldalon. 

Válassza **a Tesztelés az aktuális adatokkal** lehetőséget, Azure Sentinel futtathatja az elemzési szabály legutóbbi 50 futtatásának szimulációját. A rendszer létrehoz egy grafikont, amely a szabály által generált riasztások átlagos számát mutatja a kiértékelt nyers eseményadatok alapján. 

További információ: [A szabálylekérdezés logikájának meghatározása és beállítások konfigurálása.](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)

## <a name="december-2020"></a>2020. december

- [80 új beépített keresési lekérdezés](#80-new-built-in-hunting-queries)
- [A Log Analytics-ügynök fejlesztései](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 új beépített keresési lekérdezés
 
Azure Sentinel SOC-elemzők a beépített keresési lekérdezésekkel csökkenthetik a jelenlegi észlelési lefedettség hiányosságait, és új keresési érdeklődőket küldhetnek.

Ez a frissítés Azure Sentinel új keresési lekérdezéseket tartalmaz, amelyek lefedettséget biztosítanak a MITRE ATT és&CK-keretrendszer mátrixában:

- **Gyűjtemény**
- **Parancs és vezérlés**
- **Hitelesítő adatok elérése**
- **Felderítés**
- **Futtatási**
- **Kiszivárgás**
- **Hatás**
- **Kezdeti hozzáférés**
- **Kitartás**
- **Jogosultság eszkalációja**

A hozzáadott keresési lekérdezések célja, hogy segítsenek a gyanús tevékenységek keresésében a környezetben. Bár szabályos és potenciálisan rosszindulatú tevékenységeket is visszaadhatnak, hasznosak lehetnek a veszélykeresésben. 

Ha a lekérdezések futtatása után biztos az eredményekben, akkor ezeket elemzési szabályokká konvertálhatja, vagy keresési eredményeket adhat hozzá a meglévő vagy új incidensekhez.

Az összes hozzáadott lekérdezés elérhető a Keresési Azure Sentinel oldalon. További információ: [Veszélyforrások elleni veszélyforrások elleni Azure Sentinel.](hunting.md)

### <a name="log-analytics-agent-improvements"></a>A Log Analytics-ügynök fejlesztései

Azure Sentinel Log Analytics-ügynök következő fejlesztései a felhasználók számára:

- **Több operációs rendszer támogatása,** köztük a CentOS 8, a RedHat 8 és a SUSE Linux 15.
- **A Python 3 támogatása** a Python 2 mellett

Azure Sentinel Log Analytics-ügynökkel küld eseményeket a munkaterületre, beleértve a Windows biztonság eseményeket, a Syslog-eseményeket, a CEF-naplókat stb.

> [!NOTE]
> A Log Analytics-ügynököt néha OMS-ügynöknek vagy Microsoft Monitoring Agentnek (MMA) is nevezik. 
> 

További információkért tekintse meg a [Log Analytics dokumentációját](../azure-monitor/agents/log-analytics-agent.md) és a [Log Analytics-ügynök kibocsátási megjegyzéseit.](https://github.com/microsoft/OMS-Agent-for-Linux/releases)
## <a name="november-2020"></a>2020. november

- [A forgatókönyv állapotának figyelése a Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender-összekötő (nyilvános előzetes verzió)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>A forgatókönyv állapotának figyelése a Azure Sentinel

Azure Sentinel-forgatókönyv az [Azure Log Appsben](../logic-apps/index.yml)készült munkafolyamatokra épül, amely egy felhőszolgáltatás, amely segít a feladatok, üzleti folyamatok és munkafolyamatok ütemezésében, automatizálásában és vezénylésében. A forgatókönyveket automatikusan meghívhatja incidens létrehozásakor, vagy az incidensek osztályozása és az azzal való munka során. 

A forgatókönyvei állapotának, teljesítményének és használatának elemzéséhez hozzáadtunk [](../azure-monitor/visualize/workbooks-overview.md) egy Forgatókönyv-állapotfigyelés **nevű munkafüzetet.** 

A **Forgatókönyv állapotmonitorozási** munkafüzet segítségével monitoroznia kell a forgatókönyvei állapotát, vagy rendellenességeket kell keresnie a sikeres vagy sikertelen futtatások mennyiségében. 

A **Forgatókönyv állapotának figyelése** munkafüzet mostantól elérhető a Azure Sentinel Sablonok gyűjteményben:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Minta forgatókönyv állapotfigyelési munkafüzete":::

További információkért lásd:

- [Logic Apps dokumentáció](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Az Azure Monitor dokumentációja](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-összekötő (nyilvános előzetes verzió)
 
A Microsoft 365 Defender-összekötő Azure Sentinel lehetővé teszi a speciális keresési naplók (a nyers eseményadatok típusa) streamelését a Microsoft 365 Defenderből a Azure Sentinel. 

A Microsoft [Defender for Endpoint (MDATP)](/windows/security/threat-protection/) az [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) biztonsági gyűjtésbe való integrálásával összegyűjtheti a Microsoft Defender végponti speciális keresési eseményekhez való használatát az Microsoft 365 Defender-összekötővel, és közvetlenül streamelheti azokat az Azure Sentinel-munkaterület új, célra szolgáló tábláiba. 

A Azure Sentinel táblák a Microsoft 365 Defender portálon használt sémára épülnek, és teljes hozzáférést biztosítanak a speciális keresési naplók teljes készletének. 

További információ: [Adatok csatlakoztatása a Microsoft 365 Defenderből a Azure Sentinel.](connect-microsoft-365-defender.md)

> [!NOTE]
> Microsoft 365 Defender korábbi nevén Microsoft Veszélyforrások elleni védelem MTP volt. A Microsoft Defender for Endpoint korábban Microsoft Defender Advanced Threat Protection vagy MDATP néven volt ismert.
> 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[On-board Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Áttekinthető riasztások](quickstart-get-visibility.md)
