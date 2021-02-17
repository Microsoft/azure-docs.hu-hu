---
title: Jelentések tervezése & figyelés üzembe helyezése – Azure AD
description: Útmutatás a jelentéskészítés és a figyelés megvalósításának tervezéséhez és végrehajtásához.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 683367251c220abe36660d61463bce9e5a0c52f9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577766"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Azure Active Directory jelentéskészítés és figyelés üzembe helyezésének megtervezése

A Azure Active Directory (Azure AD) jelentéskészítési és figyelési megoldása a jogi, biztonsági és üzemeltetési követelményektől, valamint a meglévő környezettől és folyamattól függ. Ez a cikk bemutatja a különböző kialakítási lehetőségeket, és végigvezeti Önt a megfelelő üzembe helyezési stratégiában.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Az Azure AD jelentéskészítés és monitorozás előnyei

Az Azure AD jelentéskészítés átfogó áttekintést nyújt az Azure AD-tevékenységekről a környezetében, beleértve a bejelentkezési eseményeket, a naplózási eseményeket és a címtárban történt módosításokat.

A megadott adatokkal a következőket teheti:

* határozza meg az alkalmazások és szolgáltatások használatát.

* a környezet állapotát befolyásoló lehetséges kockázatok észlelése.

* hibák elhárítása a felhasználók munkavégzésének megakadályozása érdekében.

* betekintést nyerhet az Azure AD-címtár változásainak naplózási eseményeinek megtekintésére.

> [!IMPORTANT]
> Az Azure AD-figyelés lehetővé teszi, hogy az Azure AD-jelentéskészítés által létrehozott naplókat a különböző célcsoportokra irányítsa. Ezután megőrizheti őket hosszú távú használatra, vagy külső biztonságiinformáció- és eseménykezelési (SIEM-) eszközökkel integrálva elemezheti a környezetet.

Az Azure AD-figyelés segítségével a naplók a következőre irányíthatók:

* Azure Storage-fiók archiválási célokra.
* Azure Monitor naplókat, korábbi nevén Azure Log Analytics munkaterületet, ahol elemezheti az adatelemzést, irányítópultokat hozhat létre, és riasztásokat adhat meg bizonyos eseményekről.
* egy Azure Event hub, amely integrálható a meglévő SIEM-eszközökkel, például a splunk, a Sumologic vagy a QRadar.

> [!NOTE]
A közelmúltban kezdtük el használni a Azure Monitor naplók kifejezést Log Analytics helyett. A naplózási adatokat a rendszer továbbra is egy Log Analytics munkaterületen tárolja, és ugyanazokat a Log Analytics szolgáltatást gyűjti és elemzi. Frissíti a terminológiát, hogy jobban tükrözze a naplók szerepét a [Azure monitorban](../../azure-monitor/data-platform.md). A részletekért tekintse meg [Azure monitor terminológiai módosításokat](../../azure-monitor/terminology.md) .

[További információ a jelentések adatmegőrzési házirendjeiről](./reference-reports-data-retention.md).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Az Azure AD jelentéskészítési és figyelési licencelése és előfeltételei

Az Azure AD bejelentkezési naplóihoz való hozzáféréshez szüksége lesz egy prémium szintű Azure AD-licencre.

Részletes funkció-és licencelési információk a [Azure Active Directory díjszabási útmutatójában](https://azure.microsoft.com/pricing/details/active-directory/).

Az Azure AD-figyelés és-jelentéskészítés üzembe helyezéséhez szüksége lesz egy olyan felhasználóra, aki az Azure AD-bérlő globális rendszergazdai vagy biztonsági rendszergazdája.

A naplózási adatai végső céljától függően a következők egyikére lesz szüksége:

* Egy Azure Storage-fiók, amelyen ListKeys jogosultsággal rendelkezik. Azt javasoljuk, hogy általános tárfiókot használjon, ne Blob Storage-fiókot. A tárolás díjszabásával kapcsolatban lásd az [Azure Storage-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Egy Azure Event Hubs névtér, amely integrálható a harmadik féltől származó SIEM-megoldásokkal.

* Egy Azure Log Analytics-munkaterület, amely naplókat küld Azure Monitor naplókba.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Azure jelentéskészítési és figyelési üzembe helyezési projekt megtervezése

Ebben a projektben megadhatja azokat a célközönségeket, amelyek felhasználják és figyelik a jelentéseket, és meghatározzák az Azure AD-figyelési architektúrát.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak elérhetővé. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md). Gondoskodjon arról is, hogy a projektben szereplő érintett szerepkörök jól megértettek legyenek az érintett felek és a projekt bemeneti és elszámoltathatóság dokumentálása révén.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kommunikálhat a felhasználókkal, hogy a felhasználói élmény hogyan módosuljon, mikor módosul, és hogyan szerezhet támogatást, ha problémákat tapasztal.

### <a name="document-your-current-infrastructure-and-policies"></a>Az aktuális infrastruktúra és szabályzatok dokumentálása

A jelenlegi infrastruktúrája és szabályzata a jelentéskészítési és figyelési tervet fogja irányítani. Győződjön meg arról, hogy ismeri

* Az Ön által használt SIEM-eszközök.

* Azure-infrastruktúrája, beleértve a meglévő Storage-fiókokat és a használt figyelést.

* A naplókhoz tartozó szervezeti adatmegőrzési szabályzatok, beleértve a vonatkozó megfelelőségi keretrendszereket is. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Az Azure AD jelentéskészítési és figyelési üzembe helyezésének megtervezése

A jelentéskészítés és a monitorozás az üzleti igények kielégítésére szolgál, betekintést nyerhet a használati mintákba, és növelheti a szervezet biztonsági helyzetét.

### <a name="business-use-cases"></a>Üzleti használati esetek

* Az üzleti igények kielégítéséhez szükséges megoldás
* Örülök, hogy meg kell felelnie az üzleti igényeknek
* Nem alkalmazható

|Terület |Description |
|-|-|
|Megőrzés| **30 napnál hosszabb napló megőrzése**. Jogi vagy üzleti követelmények miatt a naplókat és az Azure AD bejelentkezési naplóit 30 napnál hosszabb ideig kell tárolni. |
|Elemzés| **A naplóknak kereshetőnek kell lenniük**. A tárolt naplóknak analitikai eszközökkel kell kereshetőnek lennie. |
| Operational Insights| **Különböző csapatok bepillantást nyerhet**. Ahhoz, hogy a különböző felhasználók hozzáférhessenek az operatív elemzésekhez, például az alkalmazások használatáról, a bejelentkezési hibákról, az önkiszolgáló használatról, a trendekről stb. |
| Biztonsági ismeretek| **Különböző csapatok bepillantást nyerhet**. Ahhoz, hogy a különböző felhasználók hozzáférhessenek az operatív elemzésekhez, például az alkalmazások használatáról, a bejelentkezési hibákról, az önkiszolgáló használatról, a trendekről stb. |
| Integráció SIEM-rendszerekben      | **Siem-integráció**. Az Azure AD bejelentkezési naplók és naplók meglévő SIEM-rendszerbe való integrálásának és továbbításának szükségessége. |

### <a name="choose-a-monitoring-solution-architecture"></a>Válasszon ki egy figyelési megoldás architektúráját

Az Azure AD-figyelés segítségével átirányíthatja az Azure AD-tevékenységek naplóit egy olyan rendszerre, amely a legjobban megfelel az Ön üzleti igényeinek. Megtarthatja őket a hosszú távú jelentéskészítés és elemzés érdekében, hogy betekintést nyerjen a környezetbe, és integrálja az SIEM-eszközökkel.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Döntési folyamatábra![A következő szakaszokban ismertetett ábrán látható kép](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Naplófájlok archiválása egy Storage-fiókban

Egy Azure Storage-fiókba irányítja át a naplókat, így az [adatmegőrzési szabályzatokban](./reference-reports-data-retention.md)megadott alapértelmezett megőrzési időtartamnál hosszabb ideig tarthat. Akkor használja ezt a módszert, ha archiválni szeretné a naplókat, de nem kell azokat egy SIEM-rendszerrel integrálni, és nem kell folyamatos lekérdezéseket és elemzéseket végeznie. Továbbra is igény szerinti kereséseket végezhet.

További információ [az adatok a tárfiókokba való irányításáról](./quickstart-azure-monitor-route-logs-to-storage-account.md).

#### <a name="send-logs-to-azure-monitor-logs"></a>Naplók elküldése Azure Monitor naplókba

[Azure monitor naplók](../../azure-monitor/logs/log-query-overview.md) különböző forrásokból származó figyelési adatok összesítését végzik. Egy lekérdezési nyelvet és elemzési motort is biztosít, amely betekintést nyújt az alkalmazások működésére és az erőforrások használatára. Az Azure AD-tevékenység naplófájljainak Azure Monitor naplókba küldésével gyorsan lekérheti, figyelheti és riasztást gyűjthet az összegyűjtött adatokról. Akkor használja ezt a módszert, ha nem rendelkezik olyan meglévő SIEM-megoldással, amelyet közvetlenül szeretne elküldeni, de lekérdezéseket és elemzéseket szeretne küldeni. Miután az adatai beAzure Monitor naplókba, elküldheti azt az Event hub-ba, és onnan egy SIEM-be, ha szeretné.

Megtudhatja, hogyan [küldhet adatküldést Azure monitor naplókba](./howto-integrate-activity-logs-with-log-analytics.md).

Az Azure AD-tevékenységek naplóihoz készült előre összeállított nézeteket is telepítheti a bejelentkezést és a naplózási eseményeket érintő gyakori forgatókönyvek figyeléséhez.

Ismerje meg, hogyan [telepítheti és használhatja a log Analytics-nézeteket az Azure ad-tevékenységek naplóihoz](./howto-install-use-log-analytics-views.md).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Stream-naplók az Azure Event hub-ba

Az Azure Event hub-ba történő útválasztási naplók lehetővé teszik a harmadik féltől származó SIEM-eszközökkel való integrációt. Ez az integráció lehetővé teszi, hogy az Azure AD-tevékenységek naplójának adatait kombinálja a SIEM által kezelt egyéb adatszolgáltatásokkal, így a környezet mélyebb betekintést biztosít. 

További információ [a naplók eseményközpontokba való streameléséről](./tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Az Azure AD jelentéskészítési és figyelési műveleteinek és biztonságának megtervezése

Az érintetteknek el kell érniük az Azure AD-naplókat az operatív információk beszerzéséhez. Valószínű, hogy a felhasználók a biztonsági csapat tagjai, a belső vagy külső könyvvizsgálók, valamint az identitás-és hozzáférés-kezelési műveletek csapatával rendelkeznek.

Az Azure AD-szerepkörök lehetővé teszik, hogy a szerepköre alapján delegálja az Azure AD-jelentések konfigurálásának és megtekintésének lehetőségét. Állapítsa meg, hogy a szervezeten belül kinek van szüksége az Azure AD-jelentések olvasásához és a számukra megfelelő szerephez. 

Az Azure AD-jelentések a következő szerepkörökből olvashatók be:

* Globális rendszergazda

* Biztonsági rendszergazda

* Biztonsági olvasó

* Jelentésolvasó

További információ az [Azure ad rendszergazdai szerepköreiről](../roles/permissions-reference.md).

*Mindig alkalmazza a legalacsonyabb jogosultsági szintű koncepciót, hogy csökkentse a fiókok biztonságának kockázatát*. A szervezet további biztonságossá tételéhez vegye fontolóra [Privileged Identity Management](../privileged-identity-management/pim-configure.md) megvalósítását.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD-jelentéskészítés és-figyelés üzembe helyezése

A fenti tervezési útmutató alapján a korábban létrehozott döntéseknek megfelelően ez a szakasz végigvezeti Önt a különböző telepítési lehetőségek dokumentációjában.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD-naplók felhasználása és archiválása

[Tevékenységjelentések keresése az Azure Portalon](./howto-find-activity-reports.md)

[Azure AD-naplók archiválása Azure Storage-fiókba](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>A monitorozás és az elemzés megvalósítása

[Naplók elküldése a Azure Monitorba](./howto-integrate-activity-logs-with-log-analytics.md)

[A log Analytics-nézetek telepítése és használata Azure Active Directory](./howto-install-use-log-analytics-views.md)

[Azure AD-beli tevékenység-naplók elemzése Azure Monitor naplókkal](./howto-analyze-activity-logs-log-analytics.md)

* [A naplózási naplók sémájának értelmezése Azure Monitor](./reference-azure-monitor-audit-log-schema.md)

* [A bejelentkezési naplók sémájának értelmezése Azure Monitor](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Azure AD-naplók továbbítása Azure Event hub-ba](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Azure AD-naplók integrálása a Splunkkal az Azure Monitor használatával](./howto-integrate-activity-logs-with-splunk.md)

* [Azure AD-naplók integrálása a SumoLogickal az Azure Monitor használatával](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>Következő lépések

[Privileged Identity Management](../privileged-identity-management/pim-configure.md) megvalósításának megfontolása 

Vegye fontolóra az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) megvalósítását