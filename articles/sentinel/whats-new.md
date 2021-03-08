---
title: Az Azure Sentinel újdonságai
description: Ez a cikk az Azure Sentinel új funkcióit ismerteti az elmúlt néhány hónapban.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: c24512ad38f9397c9a40a85489c45fcb0de906b3
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453801"
---
# <a name="whats-new-in-azure-sentinel"></a>Az Azure Sentinel újdonságai

Ez a cikk az Azure Sentinel legújabb funkcióit, valamint a kapcsolódó szolgáltatások új funkcióit sorolja fel, amelyek az Azure Sentinel fejlett felhasználói élményt biztosítanak.

A korábbi funkciókkal kapcsolatos információkért tekintse meg a [Tech Community blogs](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)című témakört.

A feljegyzett funkciók jelenleg előzetes verzióban érhetők el. Az [Azure előzetes verziójának kiegészítő feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak az olyan Azure-funkciókra vonatkozó további jogi feltételek, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelennek meg az általánosan elérhetővé tételben.


> [!TIP]
> A Microsoft fenyegetésekkel foglalkozó csapatai az [Azure Sentinel közösséghez](https://github.com/Azure/Azure-Sentinel)járulnak hozzá a lekérdezésekhez, a forgatókönyvekhez, a munkafüzetekhez és a jegyzetfüzetekhez, beleértve a csapat által alkalmazkodni és használni kívánt konkrét [vadászati lekérdezéseket](https://github.com/Azure/Azure-Sentinel) is. 
>
> Ön is hozzájárulhat! Csatlakozzon hozzánk az [Azure Sentinel Threat Hunters GitHub-közösségében](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="february-2021"></a>2021. február

- [Kiberbiztonsági érettségi modell minősítési (CMMC) munkafüzet](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Harmadik féltől származó adatösszekötők](#third-party-data-connectors)
- [UEBA az entitások oldalán](#ueba-insights-in-the-entity-page)
- [Javított incidensek keresése](#improved-incident-search)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Kiberbiztonsági érettségi modell minősítési (CMMC) munkafüzet

Az Azure Sentinel CMMC munkafüzete lehetővé teszi a CMMC-vezérlőkhöz igazított naplók megtekintését a Microsoft portfóliójában, beleértve a Microsoft biztonsági ajánlatait, az Office 365, a Teams, az Intune, a Windows Virtual Desktop és sok más funkciót.

A CMMC-munkafüzet lehetővé teszi a biztonsági építészek, mérnökök, biztonsági műveleti elemzők, vezetők és informatikai szakemberek számára, hogy a Felhőbeli számítási feladatok biztonsági helyzetének megfelelő helyzetet kapjanak. A Microsoft ajánlatok kiválasztására, tervezésére, üzembe helyezésére és konfigurálására vonatkozó javaslatok a megfelelő CMMC követelményekkel és eljárásokkal való összehangolásra is vonatkoznak.

Még ha nem is kell megfelelnie a CMMC, a CMMC-munkafüzet hasznos lehet a biztonsági üzemeltetési központok kiépítésében, a riasztások fejlesztésében, a fenyegetések megjelenítésében és a számítási feladatok helyzetének megismerésében.

Nyissa meg a CMMC-munkafüzetet az Azure Sentinel- **munkafüzetek** területén. Válassza a **sablon** lehetőséget, majd keresse meg a **CMMC**.

További információkért lásd:

- [Azure Sentinel kiberbiztonsági érettségi modell minősítési (CMMC) munkafüzet](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Oktatóanyag: Az adatok megjelenítése és figyelése](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Harmadik féltől származó adatösszekötők

A harmadik féltől származó integrációs gyűjtemények folyamatosan növekednek, és az elmúlt két hónapban harminc összekötőt adnak hozzá. Itt található egy lista:

- [Agari adathalászat elleni védelem és márka védelme](connect-agari-phishing-defense.md)
- [Akamai biztonsági események](connect-akamai-security-events.md)
- [Active Directory Alsid](connect-alsid-active-directory.md)
- [Apache HTTP-kiszolgáló](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [BlackBerry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco tűzerő eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (FKR)](connect-cisco-ucs.md)
- [ESET nagyvállalati ellenőr](connect-data-sources.md)
- [ESET biztonsági felügyeleti központ](connect-data-sources.md)
- [Google Workspace (korábban G csomag)](connect-google-workspace.md)
- [Inperverz WAF-átjáró](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS-naplók](connect-nxlog-dns.md)
- [NXLog Linux audit](connect-nxlog-linuxaudit.md)
- [Onapsis platform](connect-data-sources.md)
- [Proofpoint on demand – E-mail biztonság (POD)](connect-proofpoint-pod.md)
- [Qualys biztonsági rések kezelése – Tudásbázis](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall tűzfal](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMWare ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page"></a>UEBA az entitások oldalán

Az Azure Sentinel-entitások részletes oldalain [betekintési panel](identify-threats-with-entity-behavior-analytics.md#entity-insights)jelenik meg, amely az entitás viselkedési információit jeleníti meg, és segít a rendellenességek és a biztonsági fenyegetések gyors azonosításában.

Ha engedélyezve van a [UEBA](ueba-enrichments.md), és legalább négy napos időkeretet választott ki, akkor ez a betekintő ablaktábla a következő új szakaszt is tartalmazza a UEBA-eredményekhez:

|Section  |Leírás  |
|---------|---------|
|**UEBA-ismeretek**     | A rendellenes felhasználói tevékenységek összegzése: <br>-Földrajzi helyeken, eszközökön és környezeteken keresztül<br>– A felhasználók saját előzményeihez képest időbeli és gyakorisági horizonton keresztül <br>– A társak viselkedéséhez képest <br>-A szervezet viselkedéséhez képest     |
|**Felhasználói partnerek biztonsági csoporttagság alapján**     |   Felsorolja az Azure AD biztonsági csoportok tagságán alapuló felhasználói partnereket, így biztosítva a biztonsági műveleti csapatok számára a hasonló engedélyekkel rendelkező más felhasználók listáját.  |
|**Felhasználói hozzáférési engedélyek az Azure-előfizetéshez**     |     Megjeleníti a felhasználó hozzáférési engedélyeit az Azure-előfizetésekhez közvetlenül elérhető, vagy Azure AD-csoportokon/egyszerű szolgáltatásokon keresztül.   |
|**A felhasználóval kapcsolatos veszélyforrás-mutatók**     |  A felhasználó tevékenységeiben szereplő IP-címekkel kapcsolatos ismert fenyegetések gyűjteményét listázza. A fenyegetéseket a fenyegetés típusa és családja sorolja fel, és a Microsoft fenyegetési intelligencia szolgáltatása gazdagítja őket.       |
|     |         |

### <a name="improved-incident-search"></a>Javított incidensek keresése

Javítottuk az Azure Sentinel incidensek keresésének élményét, ami lehetővé teszi, hogy gyorsabban navigáljon az incidenseken keresztül, amikor egy adott fenyegetést vizsgál meg.

Ha az Azure Sentinelben incidenseket keres, mostantól a következő incidensek közül választhat:

- ID (Azonosító)
- Cím
- Termék
- Tulajdonos
- Címke

## <a name="january-2021"></a>2021. január

- [Elemzési szabály varázsló: továbbfejlesztett lekérdezés-szerkesztési élmény (nyilvános előzetes verzió)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az az. SecurityInsights PowerShell-modul (nyilvános előzetes verzió)](#azsecurityinsights-powershell-module-public-preview)
- [SQL Database-összekötő](#sql-database-connector)
- [Dynamics 365-összekötő](#dynamics-365-connector)
- [Javított incidensek megjegyzései](#improved-incident-comments)
- [Dedikált Log Analytics-fürtök](#dedicated-log-analytics-clusters)
- [Logic apps – felügyelt identitások](#logic-apps-managed-identities)
- [Továbbfejlesztett szabályok finomhangolása az analitikai szabályok előzetes verziójának diagramjaival](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Elemzési szabály varázsló: továbbfejlesztett lekérdezés-szerkesztési élmény (nyilvános előzetes verzió)

Az Azure Sentinel ütemezett elemzési szabály varázsló most a következő fejlesztéseket biztosítja a lekérdezések írásához és szerkesztéséhez:

-   Egy bővíthető szerkesztési ablak, amely több képernyős területet biztosít a lekérdezés megtekintéséhez.
-   A lekérdezés kódjában szereplő legfontosabb szavak kiemelése.
-   Bővített automatikus kiegészítés támogatása.
-   Valós idejű lekérdezések érvényessége. A lekérdezésben szereplő hibák mostantól piros blokkként jelennek meg a görgetősávon, és a **szabály logikája** lap neve piros pont. Emellett a hibákkal rendelkező lekérdezések nem menthetők.

További információt az [oktatóanyag: egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](tutorial-detect-threats-custom.md)című témakörben talál.
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az az. SecurityInsights PowerShell-modul (nyilvános előzetes verzió)

Az Azure Sentinel mostantól támogatja az új az [. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell-modult.

Az az **. SecurityInsights** modul támogatja a közös Azure Sentinel-használati eseteket, például az incidensekkel való interakciót a szobrok, a súlyosság, a tulajdonos és így tovább, a megjegyzések és címkék hozzáadásával és könyvjelzők létrehozásával.

Habár javasoljuk, hogy a CI/CD-folyamathoz [Azure Resource Manager-(ARM-)](../azure-resource-manager/templates/index.yml) sablonokat használjon, az az **. SecurityInsights** modul az üzembe helyezés utáni feladatok esetében hasznos, és a SoC automationt célozza meg.  Például a SOC Automation tartalmazhatja az adatösszekötők konfigurálásának lépéseit, az elemzési szabályok létrehozását, illetve az Automation-műveletek az elemzési szabályokhoz való hozzáadását.

További információkért, beleértve az elérhető parancsmagok teljes listáját és leírását, a paraméterek leírását és a példákat lásd az az [. SecurityInsights PowerShell-dokumentációban](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>SQL Database-összekötő

Az Azure Sentinel mostantól egy Azure SQL Database-összekötőt biztosít, amellyel az adatbázisok naplózási és diagnosztikai naplóit továbbíthatja az Azure Sentinelbe, és folyamatosan figyelheti a tevékenységeket az összes példányban.

Az Azure SQL egy teljes körűen felügyelt, szolgáltatásként szolgáló platform, amely az adatbázis-kezelési funkciókat, például a verziófrissítést, a javítást, a biztonsági mentést és a figyelést kezeli, felhasználói beavatkozás nélkül.

További információkért lásd: az [Azure SQL Database diagnosztika és a naplózási naplók összekötése](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector"></a>Dynamics 365-összekötő

Az Azure Sentinel mostantól egy összekötőt biztosít a Microsoft Dynamics 365-hez, amely lehetővé teszi a Dynamics 365-alkalmazások felhasználói, felügyeleti és támogatási tevékenységének naplóinak begyűjtését az Azure Sentinelbe. Ezekkel az értékekkel a lehetséges biztonsági rések teljes adatfeldolgozási műveleteinek naplózását és elemzését végezheti el.

További információ: [a Dynamics 365-tevékenység naplófájljainak összekapcsolása az Azure Sentinel](connect-dynamics-365.md)szolgáltatással.

### <a name="improved-incident-comments"></a>Javított incidensek megjegyzései

Az elemzők incidensekkel kapcsolatos megjegyzéseket alkalmaznak az incidensekkel való együttműködésre, a folyamatok és a lépések manuális vagy egy forgatókönyv részeként történő dokumentálására. 

A továbbfejlesztett incidensek kommentálása lehetővé teszi a megjegyzések formázását, valamint a meglévő megjegyzések szerkesztését és törlését.

További információ: [incidensek automatikus létrehozása a Microsoft biztonsági értesítéseiből](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Dedikált Log Analytics-fürtök

Az Azure Sentinel mostantól támogatja a dedikált Log Analytics-fürtöket központi telepítési lehetőségként. Javasoljuk, hogy a dedikált fürtöt a következőket érdemes figyelembe venni:

- **Naponta több mint 1 TB** -nyi betöltés az Azure Sentinel-munkaterületre
- **Több Azure Sentinel-munkaterülettel rendelkezik** az Azure-regisztrációban

A dedikált fürtök lehetővé teszik olyan funkciók használatát, mint az ügyfél által felügyelt kulcsok, a kulcstároló, a kettős titkosítás és a több munkaterületre kiterjedő lekérdezések, ha ugyanazon a fürtön több munkaterülettel rendelkezik.

További információ: [Azure monitor naplók dedikált fürtök](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Logic apps – felügyelt identitások

Az Azure Sentinel mostantól támogatja a felügyelt identitások használatát az Azure Sentinel Logic Apps-összekötőhöz, amely lehetővé teszi, hogy a további identitások létrehozása helyett közvetlenül egy adott forgatókönyvhöz engedélyezze az engedélyeket az Azure Sentinelben való működéshez.

- **Felügyelt identitás nélkül** az Logic apps-összekötő külön identitást igényel egy Azure Sentinel RBAC-szerepkörrel az Azure Sentinel futtatásához. A különálló identitás lehet egy Azure AD-felhasználó vagy egy egyszerű szolgáltatásnév, például egy Azure AD-beli regisztrált alkalmazás.

- A **felügyelt identitás támogatásának bekapcsolása a logikai alkalmazásban** regisztrálja a logikai alkalmazást az Azure ad-ben, és megadja az objektum azonosítóját. Az Azure Sentinel objektum-azonosítójával társítsa a logikai alkalmazást egy Azure RBAC-szerepkörrel az Azure Sentinel-munkaterületen. 

További információkért lásd:

- [Hitelesítés felügyelt identitással Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Az Azure Sentinel Logic Apps-összekötő dokumentációja](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Továbbfejlesztett szabályok finomhangolása az analitikai szabályok előzetes verziójának diagramjaival (nyilvános előzetes verzió)

Az Azure Sentinel mostantól segít jobban meghangolni az elemzési szabályokat, így növelheti a pontosságot és csökkentheti a zajt.

Miután szerkeszt egy elemzési szabályt a **szabály logikájának beállítása** lapon, keresse meg a jobb oldalon található **eredmények szimulálása** lehetőséget. 

Válassza a **tesztelés az aktuális adattal** lehetőséget, hogy az Azure Sentinel szimulálja az elemzési szabály utolsó 50 futtatását. A rendszer létrehoz egy gráfot, amely megjeleníti a szabály által generált riasztások átlagos számát a kiértékelt nyers esemény adatai alapján. 

További információ: [a szabály lekérdezési logikájának megadása és a beállítások konfigurálása](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>2020. december

- [80 új beépített vadászati lekérdezések](#80-new-built-in-hunting-queries)
- [Log Analytics ügynök fejlesztése](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 új beépített vadászati lekérdezések
 
Az Azure Sentinel beépített vadászati lekérdezései lehetővé teszik a SOC-elemzők számára a jelenlegi észlelési lefedettség hiányának csökkentését és az új vadászati érdeklődők meggyújtását.

Ez az Azure Sentinel-frissítés olyan új vadászati lekérdezéseket tartalmaz, amelyek lefedettséget biztosítanak a MITRE ATT&a CK Framework Matrixban:

- **Gyűjtemény**
- **Parancs és vezérlés**
- **Hitelesítőadat-hozzáférés**
- **Felderítés**
- **Végrehajtási**
- **Kiszűrése**
- **Hatás**
- **Kezdeti hozzáférés**
- **Kitartás**
- **Jogosultságok eszkalációja**

A hozzáadott vadászati lekérdezések célja, hogy segítsen megtalálni a gyanús tevékenységeket a környezetben. Habár a jogszerű tevékenységet és a potenciálisan rosszindulatú tevékenységeket is visszaadhatják, hasznosak lehetnek a vadászatra. 

Ha a lekérdezések futtatása után biztos abban, hogy az eredményeket megbízik, érdemes lehet konvertálni az elemzési szabályokba, vagy a vadászat eredményeit meglévő vagy új incidensekhez hozzáadni.

Az összes hozzáadott lekérdezés az Azure Sentinel Hunting oldalon érhető el. További információ: [a fenyegetések vadászata az Azure Sentinel](hunting.md)szolgáltatással.

### <a name="log-analytics-agent-improvements"></a>Log Analytics ügynök fejlesztése

Az Azure Sentinel felhasználói a következő Log Analytics-ügynökkel kapcsolatos előnyöket élvezhetik:

- **Több operációs rendszer támogatása**, például a CentOS 8, a RedHat 8 és a SUSE Linux 15.
- A Python **3 támogatása** a Python 2 mellett

Az Azure Sentinel a Log Analytics ügynök használatával eljuttatja az eseményeket a munkaterületre, beleértve a Windows biztonsági eseményeit, a syslog-eseményeket, a CEF-naplókat és egyebeket.

> [!NOTE]
> A Log Analytics ügynök más néven OMS-ügynök vagy a Microsoft monitoring Agent (MMA). 
> 

További információkért tekintse meg a [log Analytics dokumentációját](../azure-monitor/agents/log-analytics-agent.md) és a [log Analytics ügynök kibocsátási megjegyzéseit](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>2020. november

- [A forgatókönyvek állapotának monitorozása az Azure Sentinelben](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender-összekötő (nyilvános előzetes verzió)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>A forgatókönyvek állapotának monitorozása az Azure Sentinelben

Az Azure Sentinel-forgatókönyvek olyan [Azure log-alkalmazásokban](../logic-apps/index.yml)létrehozott munkafolyamatokon alapulnak, amelyek segítségével a feladatok, az üzleti folyamatok és a munkafolyamatok ütemezhetők, automatizálható és hangolható. A forgatókönyvek automatikusan meghívhatók az incidensek létrehozásakor, illetve a osztályozásakor és az incidensek használatakor is. 

A forgatókönyvek állapotának, teljesítményének és használatának megtalálása érdekében egy forgatókönyvek **állapotának monitorozása** nevű [munkafüzetet](../azure-monitor/visualize/workbooks-overview.md) adtunk hozzá. 

A forgatókönyvek **állapotának figyelése** munkafüzettel figyelheti a forgatókönyvek állapotát, vagy a sikeres vagy sikertelen futtatások mennyiségében megkeresheti a rendellenességeket. 

A forgatókönyvek **állapotának figyelésére szolgáló** munkafüzet mostantól elérhető az Azure Sentinel templates galériájában:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Példa a forgatókönyvek állapotának figyelésére szolgáló munkafüzetre":::

További információkért lásd:

- [Logic Apps dokumentáció](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Az Azure Monitor dokumentációja](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-összekötő (nyilvános előzetes verzió)
 
Az Azure Sentinel Microsoft 365 Defender-összekötője lehetővé teszi a speciális vadászati naplók (a nyers események adattípusának) továbbítását Microsoft 365 Defenderből az Azure Sentinelbe. 

A [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) a [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) biztonsági esernyőbe való integrálásával mostantól összegyűjtheti a Microsoft Defender a végpontok speciális vadászati eseményeit az Microsoft 365 Defender-összekötő használatával, és közvetlenül az Azure Sentinel munkaterületen található új, célra épülő táblákba továbbíthatja őket. 

Az Azure Sentinel-táblázatok a Microsoft 365 Defender-portálon használt sémára épülnek, és teljes hozzáférést biztosítanak a speciális vadászati naplók teljes készletéhez. 

További információ: [adatok Összekötése Microsoft 365 defenderből az Azure sentinelbe](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender korábbi nevén Microsoft Threat Protection vagy MTP. A Microsoft Defender for Endpoint korábban a Microsoft Defender komplex veszélyforrások elleni védelmi vagy MDATP néven ismert.
> 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[A fedélzeti Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Áttekinthető riasztások](quickstart-get-visibility.md)