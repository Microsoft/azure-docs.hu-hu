---
title: Mi a Azure Web Application Firewall a Azure Application Gateway?
titleSuffix: Azure Web Application Firewall
description: Ez a cikk áttekintést nyújt a Web Application Firewall (WAF) Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 12/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: babd628280ebaee408d44dfacfaf6a5e14f57019
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481821"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Mi a Azure Web Application Firewall a Azure Application Gateway?

Azure Web Application Firewall (WAF) Azure Application Gateway védelmet nyújt a webalkalmazások számára a gyakori biztonsági rések ellen. A webalkalmazásokat egyre gyakrabban támadják rosszindulatú támadások, amelyek gyakran ismert biztonsági réseket kihasználnak. Az SQL-injektálás és a webhelyközi szkriptek használata az egyik leggyakoribb támadás.

A WAF Application Gateway alapszabálykészlet [(CRS)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3.1-es, 3.0-s vagy 2.2.9-es Open Web Application Security Project alapul. A WAF automatikusan frissül, és további konfiguráció nélkül nyújt védelmet az új biztonsági rések ellen. 

Az alább felsorolt ÖSSZES WAF-funkció egy WAF-szabályzaton belül található. Több szabályzatot is létrehozhat, és társíthatja őket egy Application Gateway-hez, az egyes figyelőkhöz vagy egy adott Application Gateway. Így szükség esetén külön szabályzatokat kaphat a Application Gateway mögötti összes helyhez. A WAF-szabályzatokkal kapcsolatos további információkért [lásd: WAF-szabályzat létrehozása.](create-waf-policy-ag.md)

![Application Gateway WAF-diagram](../media/ag-overview/waf1.png)

Application Gateway alkalmazásk kézbesítési vezérlőként (ADC) működik. A szolgáltatás Transport Layer Security (TLS), korábbi nevén SSL (SSL), leválasztás, cookie-alapú munkamenet-affinitás, ciklikus időkorrekciós terheléselosztás, tartalomalapú útválasztás, több webhely üzemeltethetősége és biztonsági fejlesztések.

Application Gateway biztonsági fejlesztések közé tartozik a TLS-szabályzatkezelés és a végpontok között támogatott TLS. Az alkalmazásbiztonságot tovább erősíti a WAF integrációja a Application Gateway. A kombináció megvédi a webalkalmazásokat a gyakori biztonsági rések ellen. Könnyen konfigurálható központi helyet biztosít a kezeléshez.

## <a name="benefits"></a>Előnyök

Ez a szakasz a WAF által a Application Gateway előnyeit ismerteti.

### <a name="protection"></a>Védelem

* A háttérkód módosítása nélkül védheti webalkalmazásait a webes biztonsági résekkel és támadásokkal szemben.

* Egyszerre több webalkalmazás védelme. A Application Gateway akár 40 webalkalmazási tűzfallal védett webhelyet is üzemeltethet.

* Egyéni WAF-szabályzatok létrehozása ugyanazon WAF mögötti különböző webhelyekhez 

* Webalkalmazások védelme rosszindulatú robotok ellen az IP-cím hírnevének szabálykészletével (előzetes verzió)

### <a name="monitoring"></a>Figyelés

* Valós idejű WAF-naplóval figyelheti a webalkalmazások elleni támadásokat. A napló integrálva van a [Azure Monitor](../../azure-monitor/overview.md) a WAF-riasztások nyomon követéséhez és a trendek egyszerű figyelése érdekében.

* A Application Gateway WAF integrálva van a Azure Security Center. Security Center az összes Azure-erőforrás biztonsági állapotának központi nézetét biztosítja.

### <a name="customization"></a>Testreszabás

* Testre szabhatja a WAF-szabályokat és -szabálycsoportokat az alkalmazás követelményeinek megfelelően, és kiszűrheti a téves riasztásokat.

* WAF-szabályzat társítása a WAF mögötti összes helyhez a helyspecifikus konfiguráció érdekében

* Egyéni szabályok létrehozása az alkalmazás igényeinek megfelelően

## <a name="features"></a>Funkciók

- SQL-injektálásos védelem.
- Helyközi parancsfájl-védelem.
- Védelem más gyakori webes támadásokkal szemben, mint például a parancsinjektálás, a HTTP-kérések széttagolása, a HTTP-válasz felosztása és a távoli fájlbe belefoglalás.
- HTTP-protokollsértések elleni védelem.
- HTTP-protokollanomáliák, például hiányzó gazdafelhasználói ügynök és elfogadó fejlécek elleni védelem.
- Webbejárások és képolvasók elleni védelem.
- Az alkalmazások (például az Apache és az IIS) gyakori hibás konfigurációinak észlelése.
- Konfigurálható kérelemméretkorlátok alsó és felső korlátokkal.
- A kizárási listákkal kihagyni bizonyos kérelemattribútumokat a WAF-értékelésekből. Gyakori példa a Active Directory vagy jelszómezőkhöz használt, beszúrt jogkivonatok használata.
- Hozzon létre egyéni szabályokat az alkalmazások igényeinek megfelelően.
- Földrajzi szűréssel engedélyezheti vagy letilthatja, hogy bizonyos országok/régiók hozzáférjenek az alkalmazásokhoz. (előzetes verzió)
- Az alkalmazások robotokkal való védelme a robot kockázatcsökkentési szabálykészletének alkalmazásával. (előzetes verzió)
- JSON és XML vizsgálata a kérés törzsében

## <a name="waf-policy-and-rules"></a>WAF-szabályzat és -szabályok

Ha engedélyezni szeretné a Web Application Firewall a Application Gateway, létre kell hoznia egy WAF-szabályzatot. Ebben a szabályzatban létezik az összes felügyelt szabály, egyéni szabály, kizárás és egyéb testreszabás, például a fájlfeltöltési korlát.

Konfigurálhat EGY WAF-szabályzatot, és a védelem érdekében társíthatja azt egy vagy több alkalmazásátjáróhoz. A WAF-szabályzatok kétféle biztonsági szabályból áll:

- Ön által létrehozott egyéni szabályok

- Felügyelt szabálykészletek, amelyek az Azure által felügyelt, előre konfigurált szabálykészletek gyűjteményei

Ha mindkettő jelen van, az egyéni szabályok feldolgozása a felügyelt szabálykészletek szabályainak feldolgozása előtt történik. A szabály egy egyeztetési feltételből, egy prioritásból és egy műveletből áll. A támogatott művelettípusok: ALLOW, BLOCK és LOG. Felügyelt és egyéni szabályok kombinálásával létrehozhat egy teljes mértékben testreszabott szabályzatot, amely megfelel az adott alkalmazásvédelmi követelményeknek.

A szabályzaton belüli szabályok feldolgozása prioritási sorrendben van megszabadva. A Prioritás egy egyedi egész szám, amely meghatározza a feldolgozni szükséges szabályok sorrendjét. A kisebb egész szám magasabb prioritást képvisel, és ezeket a szabályokat a rendszer a magasabb egész szám értékű szabályok előtt értékeli ki. Egy szabály egyeztetése után a rendszer a szabályban meghatározott megfelelő műveletet alkalmazza a kérésre. Az ilyen egyezések feldolgozása után az alacsonyabb prioritású szabályok feldolgozása nem tovább.

A vállalat által Application Gateway webalkalmazáshoz globális szinten, webhelyenként vagy URI-szinten társíthat WAF-szabályzatot.

### <a name="core-rule-sets"></a>Alapvető szabálykészletek

Application Gateway három szabálykészletet támogat: CRS 3.1, CRS 3.0 és CRS 2.2.9. Ezek a szabályok megvédik a webalkalmazásokat a kártékony tevékenységektől.

További információ: [Webalkalmazási tűzfal CRS-szabálycsoportja és szabályai.](application-gateway-crs-rulegroups-rules.md)

### <a name="custom-rules"></a>Egyéni szabályok

Application Gateway egyéni szabályokat is támogat. Egyéni szabályokkal saját szabályokat hozhat létre, amelyek kiértékelése a WAF-nak áteső összes kérésre történik. Ezek a szabályok magasabb prioritást élveznek, mint a felügyelt szabálykészletek többi szabálya. Ha egy feltételkészlet teljesül, a program engedélyezi vagy letiltja a műveletet. 

A geomatch operátor mostantól nyilvános előzetes verzióban is elérhető az egyéni szabályokhoz. További [információért tekintse meg a geomatch egyéni](custom-waf-rules-overview.md#geomatch-custom-rules-preview) szabályait.

> [!NOTE]
> Az egyéni szabályok geomatch operátora jelenleg nyilvános előzetes verzióban érhető el, és előzetes verziójú szolgáltatói szerződés keretében érhető el. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

További információ az egyéni szabályokról: Egyéni [szabályok a Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Robotcsökkentés (előzetes verzió)

A felügyelt robotvédelmi szabálykészlet engedélyezhető a WAF számára, hogy a felügyelt szabálykészlet mellett blokkolja vagy naplózza az ismert kártékony IP-címekről származó kéréseket. Az IP-címek forrása a Microsoft fenyegetésfelderítő hírcsatornája. A Microsoft veszélyforrás-felderítését az Intelligent Security Graph működteti, és azt több szolgáltatás, például az Azure Security Center is használja.

> [!NOTE]
> A robotvédelmi szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és előzetes verziójú szolgáltatói szerződést biztosít. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha a Bot Protection engedélyezve van, a rosszindulatú robot ügyfél-IP-nek megfelelő bejövő kérések a tűzfalnaplóban vannak naplózva. További információt alább olvashat. A WAF-naplókat tárfiókból, eseményközpontból vagy log analyticsből is elérheti. 

### <a name="waf-modes"></a>WAF-módok

A Application Gateway WAF a következő két módban való futtatásra konfigurálható:

* **Észlelési mód:** Figyeli és naplózza az összes fenyegetési riasztást. A naplózási diagnosztikát a Application Gateway szakaszban **kapcsolhatja** be. Arról is meg kell győződnie, hogy a WAF-napló ki van jelölve és be van kapcsolva. A webalkalmazási tűzfal nem blokkolja a bejövő kéréseket, ha észlelési módban működik.
* **Megelőzési mód:** Letiltja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és bezárja a kapcsolatot. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

> [!NOTE]
> Javasoljuk, hogy az újonnan telepített WAF-et rövid ideig észlelési módban futtassa éles környezetben. Ez lehetővé teszi a tűzfalnaplók [beszerzését](../../application-gateway/application-gateway-diagnostics.md#firewall-log) és a kivételek vagy egyéni szabályok frissítését a Megelőzés módba való váltás előtt. [](./custom-waf-rules-overview.md) Ez segíthet csökkenteni a váratlan blokkolt forgalom előfordulását.

### <a name="anomaly-scoring-mode"></a>Anomáliapontozási mód

Az OWASP két módban dönti el, hogy letiltja-e a forgalmat: hagyományos mód és anomáliapontozási mód.

Hagyományos módban a szabálynak megfelelő forgalmat a rendszer a többi szabály egyezéstől függetlenül számítja. Ez a mód könnyen érthető. Azonban az, hogy nincs információ arról, hogy hány szabály felel meg egy adott kérésnek, egy korlátozás. Ezért bevezettük az anomáliapontozási módot. Ez az OWASP 3 alapértelmezett beállítása. *x*.

Anomáliapontozási módban a szabálynak megfelelő forgalom nem lesz azonnal blokkolva, amikor a tűzfal Megelőzés módban van. A szabályok bizonyos súlyosságúak: *Kritikus,* *Hiba,* *Figyelmeztetés* vagy *Figyelmeztetés.* Ez a súlyosság hatással van a kérés numerikus értékére, amelyet anomáliapontszámnak nevezünk. Egy figyelmeztetési szabály *egyezése* például 3-lal járul hozzá a pontszámhoz. Egy *kritikus szabály* egyezése 5-öt is hozzájárul.

|Súlyosság  |Érték  |
|---------|---------|
|Kritikus     |5|
|Hiba        |4|
|Figyelmeztetés      |3|
|Észrevesz       |2|

A forgalom blokkolásához az anomáliapontszám küszöbértéke 5. Így egyetlen *Kritikus* szabály egyezése elegendő ahhoz, Application Gateway WAF még Megelőzés módban is blokkolni tudja a kéréseket. Egy *figyelmeztetési szabály* egyezése azonban csak 3-lal növeli az anomáliapontszámot, ami önmagában nem elegendő a forgalom blokkolásához.

> [!NOTE]
> A naplózott üzenet, ha egy WAF-szabály megfelel a forgalomnak, tartalmazza a "Letiltva" műveletértéket. A forgalom azonban csak az 5-ös vagy annál magasabb anomáliapontszám esetén van blokkolva. További információkért lásd a [Web Application Firewall (WAF) hibaelhárítását Azure Application Gateway.](web-application-firewall-troubleshoot.md#understanding-waf-logs) 

### <a name="waf-monitoring"></a>WAF-monitorozás

Fontos az alkalmazásátjáró állapotának folyamatos figyelése. A WAF és az alkalmazás által védett alkalmazások állapotának monitorozását támogatja az integráció a Azure Security Center, Azure Monitor és Azure Monitor naplókba.

![A WAF Application Gateway diagnosztika ábrája](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway naplók integrálva vannak a [Azure Monitor.](../../azure-monitor/overview.md) Ez lehetővé teszi a diagnosztikai információk, például a WAF-riasztások és -naplók nyomon követését. Ezt a képességet a  portálon, az erőforrás Application Gateway Diagnosztika lapján, vagy közvetlenül a portálon Azure Monitor. További információ a naplók engedélyezéséről: Application Gateway [diagnosztikák.](../../application-gateway/application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) segít a fenyegetések megelőzésében, észlelésében és a fenyegetésekre való válaszadásban. Nagyobb betekintést és irányítást biztosít az Azure-erőforrások biztonságába. Application Gateway [integrálva van a Security Center.](../../security-center/security-center-partner-integration.md#integrated-azure-security-solutions) Security Center átvizsgálja a környezetet a nem védett webalkalmazások észleléséhez. Javasoljuk, hogy Application Gateway WAF-et a sebezhető erőforrások védelme érdekében. A tűzfalakat közvetlenül a Security Center. Ezek a WAF-példányok integrálva vannak a Security Center. Riasztásokat és állapotinformációkat küldenek Security Center jelentéskészítéshez.

![Security Center áttekintő ablak](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel egy skálázható, natív felhőbeli, biztonsági információs eseménykezelési (SIEM) és biztonsági vezénylési automatizált válaszadási (SOAR) megoldás. Azure Sentinel intelligens biztonsági elemzéseket és fenyegetésészlelést biztosít a vállalaton belül, így egyetlen megoldást kínál a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív veszélyforrás-felderítésre és a fenyegetésekre való reagálásra.

A beépített Azure WAF tűzfalesemény-munkafüzet segítségével áttekintheti a WAF biztonsági eseményeit. Ez magában foglalja az eseményeket, az egyező és letiltott szabályokat, valamint minden mást, ami a tűzfalnaplókba kerül. A naplózásról alább olvashat bővebben. 


![Azure WAF-tűzfalesemények munkafüzete](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Azure Monitor-munkafüzet a WAF-hez

Ez a munkafüzet lehetővé teszi a biztonsággal kapcsolatos WAF-események egyéni vizualizációját számos szűrhető panelen. Minden WAF-típussal működik, beleértve a Application Gateway, Front Door és a CDN-t, és a WAF típusa vagy egy adott WAF-példány alapján szűrhető. Importálás ARM-sablonnal vagy katalógussablonnal. A munkafüzet üzembe helyezéséhez lásd: [WAF-munkafüzet.](https://aka.ms/AzWAFworkbook)

#### <a name="logging"></a>Naplózás

Application Gateway WAF részletes jelentéseket biztosít az észlelt fenyegetésekről. A naplózás integrálva van Azure Diagnostics naplókba. A riasztások .json formátumban vannak rögzítve. Ezek a naplók integrálhatók a [Azure Monitor naplókba.](../../azure-monitor/insights/azure-networking-analytics.md)

![Application Gateway diagnosztikai naplók ablakai](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway WAF – A termékváltozat díjszabása

A díjszabási modellek eltérőek az egyes WAF_v1 és WAF_v2 esetében. További információt [a Application Gateway oldalon](https://azure.microsoft.com/pricing/details/application-gateway/) olvashat. 

## <a name="whats-new"></a>Újdonságok

Az azure-Azure Web Application Firewall [újdonságokért lásd:](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall).

## <a name="next-steps"></a>Következő lépések

- További információ a [WAF által felügyelt szabályokról](application-gateway-crs-rulegroups-rules.md)
- További információ az egyéni [szabályokról](custom-waf-rules-overview.md)
- További információ [Web Application Firewall a Azure Front Door](../afds/afds-overview.md)
