---
title: Webalkalmazási tűzfal (WAF) finomhangolása az Azure bejárati ajtóhoz
description: Ebből a cikkből megtudhatja, hogyan hangolhatja be a WAF a bejárati ajtóhoz.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: b2f551257fb6869d5dec47014be3a8522b61b9fa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506633"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Webalkalmazási tűzfal (WAF) finomhangolása az Azure bejárati ajtóhoz
 
Az Azure által felügyelt alapértelmezett szabálykészlet a [OWASP alapszintű szabálykészlet (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) alapján történik, és úgy van kialakítva, hogy az ne legyen a dobozban. Gyakran várható, hogy a WAF-szabályokat úgy kell hangolni, hogy megfeleljenek az alkalmazás vagy a szervezet konkrét igényeinek a WAF használatával. Ezt általában a szabályok kizárásának definiálásával, egyéni szabályok létrehozásával, valamint olyan szabályok letiltásával lehet megvalósítani, amelyek problémákat okozhatnak vagy téves pozitívak lehetnek. Néhány dolog elvégezhető, ha a webalkalmazási tűzfalon (WAF) továbbított kérések le vannak tiltva.

Először is győződjön meg róla, hogy elolvasta a [bejárati ajtó WAF áttekintését](afds-overview.md) és a WAF-szabályzatot az [előtérben](waf-front-door-create-portal.md) lévő dokumentumokhoz. Győződjön meg arról is, hogy engedélyezte a [WAF-figyelést és a naplózást](waf-front-door-monitor.md). Ezek a cikkek ismertetik a WAF működését, a WAF-szabály működését, valamint a WAF-naplók elérését.
 
## <a name="understanding-waf-logs"></a>A WAF-naplók ismertetése
 
A WAF-naplók célja, hogy minden, a WAF által egyeztetett vagy letiltott kérelmet megjelenítse. Az összes kiértékelt kérelem gyűjteménye, amely megfelel vagy le van tiltva. Ha azt tapasztalja, hogy a WAF blokkol egy olyan kérést, amely nem (hamis pozitív), elvégezhet néhány dolgot. Először, Szűkítse le, és keresse meg az adott kérést. Ha kívánja, [konfigurálhat egy egyéni válaszüzenetet](./waf-front-door-configure-custom-response-code.md) , amely tartalmazza a `trackingReference` mezőt az esemény egyszerű azonosítására és a napló lekérdezésének végrehajtására az adott értéken. Tekintse át a naplókat, hogy megkeresse a kérelem egyedi URI-JÁT, időbélyegét vagy ügyfél-IP-címét. Ha megtalálta a kapcsolódó naplóbejegyzéseket, megkezdheti a hamis pozitív műveletek elvégzését. 
 
Tegyük fel például, hogy rendelkezik egy olyan legitim forgalommal, `1=1` amely a WAF átadni kívánt karakterláncot tartalmazza. A kérelem a következőképpen néz ki:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Ha kipróbálja a kérelmet, a WAF letiltja az *1 = 1* karakterláncot tartalmazó forgalmat bármely paraméterben vagy mezőben. Ez az SQL-injektálási támadáshoz gyakran társított karakterlánc. Megtekintheti a naplókat, és megtekintheti a kérés időbélyegét és a blokkolt/egyeztetett szabályokat.
 
A következő példában egy `FrontdoorWebApplicationFirewallLog` szabály egyezése miatt létrehozott naplót vizsgálunk. Az alábbi Log Analytics lekérdezéssel megkeresheti az elmúlt 24 órában blokkolt kérelmeket:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
A `requestUri` mezőben megtekintheti a kérést, hogy `/api/Feedbacks/` konkrétan megtörténjen. További információért keresse meg a szabály AZONOSÍTÓját `942110` a `ruleName` mezőben. A szabály AZONOSÍTÓjának ismerete után megtekintheti a [OWASP ModSecurity alapszintű szabálykészlet hivatalos tárházát](https://github.com/coreruleset/coreruleset) , és megkeresheti az adott [szabály azonosítóját](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) , és megtudhatja, hogy pontosan milyen a szabály egyezése. 
 
A mező ellenőrzésével `action` azt láthatjuk, hogy ez a szabály úgy van beállítva, hogy letiltsa a kérelmeket, és megerősítjük, hogy a kérést a WAF valóban letiltotta, mert a értéke a következő: `policyMode` `prevention` . 
 
Most nézzük meg az adatokat a `details` mezőben. Itt láthatja a `matchVariableName` és az `matchVariableValue` információkat. Megtudhatjuk, hogy ez a szabály ki lett indítva, mert valaki a webalkalmazás mezőjében *1 = 1* adatot adott meg `comment` .
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
A hozzáférési naplók ellenőrzése is megtörténik, hogy bővítse az adott WAF-eseményre vonatkozó ismereteit. Az alábbiakban áttekintjük a `FrontdoorAccessLog` fenti eseményre adott válaszként generált naplót.
 
Ezek a kapcsolódó naplók is láthatók az `trackingReference` azonos érték alapján. Az általános betekintést nyújtó különböző mezők (például a `userAgent` és a `clientIP` ) között a és a mezőket is Figyeljük `httpStatusCode` `httpStatusDetails` . Itt ellenőrizheti, hogy az ügyfél HTTP 403 választ kapott-e, amely teljesen megerősíti, hogy a kérést megtagadták és letiltották. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Hamis pozitív problémák megoldása
 
Ahhoz, hogy tájékozott döntést hozzon a hamis pozitív megoldásokról, fontos, hogy megismerkedjen az alkalmazás által használt technológiákkal. Tegyük fel például, hogy a technológiai veremben nincs SQL Server-kiszolgáló, és a szabályokkal kapcsolatos téves pozitív eredményt kap. A szabályok letiltása nem feltétlenül gyengíti a biztonságot. 

Ezekkel az információkkal és az 942110-es szabály megismerésével, amely megfelel a `1=1` példában szereplő sztringnek, elvégezhető néhány dolog, amivel leállíthatja ezt a legitim kérést:
 
* Kizárási listák használata
  * A kizárási listával kapcsolatos további információkért tekintse meg a [webalkalmazási tűzfal (WAF) és a bejárati ajtó szolgáltatás kizárási listáját](waf-front-door-exclusion.md) . 
* WAF műveletek módosítása
  * További információt a [WAF-műveletek](afds-overview.md#waf-actions) című témakörben talál arról, hogy milyen műveleteket lehet elvégezni, ha egy kérelem megfelel a szabály feltételeinek.
* Egyéni szabályok használata
  * Az egyéni szabályokkal kapcsolatos további információkért tekintse [meg a webalkalmazási tűzfal egyéni szabályai az Azure bejárati ajtóval](waf-front-door-custom-rules.md) című témakört.
* Szabályok letiltása 

> [!TIP]
> Ha kijelöl egy olyan módszert, amely engedélyezi a legitim kérelmeket a WAF keresztül, a lehető legszűkebb értékre teheti ezt. Például érdemes lehet kizárási listát használni, mint a szabályok teljes letiltása.

### <a name="using-exclusion-lists"></a>Kizárási listák használata

A kizárási lista használatának egyik előnye, hogy csak a kizárni kívánt Match változót fogja megvizsgálni az adott kérelem esetében. Ez azt is megteheti, hogy kiválaszthatja az egyes kérések fejléceit, megkérheti a cookie-kat, a lekérdezési karakterlánc argumentumait vagy a kérelem törzsét, ha egy bizonyos feltétel teljesül, a teljes kérelemnek a vizsgálat alól való kizárásával szemben. A kérelem többi nem megadott változóját a rendszer továbbra is szabályosan ellenőrzi.
 
Fontos figyelembe venni, hogy a kizárások globális beállítások. Ez azt jelenti, hogy a konfigurált kizárás a WAF áthaladó összes forgalomra érvényes lesz, nem csak egy adott webalkalmazásra vagy URI-ra. Ez lehet például egy olyan probléma, ha *1 = 1* egy adott webalkalmazás törzsében érvényes kérelem, de nem az azonos WAF-szabályzatban szereplő mások számára. Ha érdemes különböző kizárási listát használni a különböző alkalmazásokhoz, érdemes lehet különböző WAF-házirendeket alkalmazni az egyes alkalmazásokhoz, és azokat alkalmazni az egyes alkalmazások előtérben.
 
A felügyelt szabályok kizárási listájának konfigurálásakor dönthet úgy, hogy kizárja a szabálykészlet összes szabályát, a szabálykészlet összes szabályát vagy egy adott szabályt. A kizárási lista a [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), az [Azure CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), a [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)vagy a Azure Portal használatával konfigurálható.

* Kizárások egy szabály szintjén
  * A kizárások szabály szintjén történő alkalmazása azt jelenti, hogy a megadott kizárások csak az adott szabály alapján lesznek elemezve, míg a szabálykészlet minden más szabálya továbbra is elemzésre kerül. Ez a legrészletesebb kizárási szint, amellyel a felügyelt szabálykészlet a WAF-naplókban található információk alapján finomítható az események hibaelhárításakor.
* Kizárások a szabály csoport szintjén
  * Ha kizárja a kizárásokat egy szabálytípus szintjén, azt jelenti, hogy a megadott kizárások nem lesznek elemezve az adott szabálytípus-csoporttal. Ha például a *SQLI* kihagyása kizárt szabálykészlet, akkor a SQLI-specifikus szabályok nem ellenőrzik a definiált kérelmek kizárását, de más csoportokban, például a *php*-ben, az *rfiben* vagy az *XSS*-ben is megvizsgáljuk azokat. Ez a típusú kizárás akkor lehet hasznos, ha biztosak vagyunk abban, hogy az alkalmazás nem alkalmas bizonyos típusú támadásokra. Előfordulhat például, hogy egy olyan alkalmazás, amely nem rendelkezik SQL-adatbázissal, kizárhatja az összes *SQLI* -szabályt, anélkül, hogy ez hátrányos lenne a biztonsági szintjére.
* Kizárások a szabály beállított szintjén 
  * Ha a kizárásokat egy szabálykészlet szintjén alkalmazza, azt jelenti, hogy a megadott kizárások nem lesznek elemezve az adott szabálykészlet által elérhető biztonsági szabályokkal. Ez egy átfogó kizárás, ezért körültekintően kell használni.

Ebben a példában egy kizárást végzünk a legrészletesebbebb szinten (egyetlen szabály kizárásával), és a rendszer kizárja az egyeztetési változót tartalmazó **kérelem törzsét** , amely tartalmazza az argumentumot `comment` . Ez nyilvánvaló, mert az egyeztetési változó részleteit a tűzfal naplójában tekintheti meg: `"matchVariableName": "PostParamValue:comment"` . Az attribútum a `comment` . Ezt az attribútumot néhány más módon is megkeresheti, lásd: a [kérelmek attribútumainak neve](#finding-request-attribute-names).

![Kizárási szabályok](../media/waf-front-door-tuning/exclusion-rules.png)

![Szabály kizárása adott szabályhoz](../media/waf-front-door-tuning/exclusion-rule.png)

Időnként előfordulhat, hogy bizonyos paramétereket nem lehet intuitív módon átadni a WAF. Például van egy token, amely a Azure Active Directory használatával történő hitelesítéskor lesz átadva. Ez a token `__RequestVerificationToken` általában kérelem cookie-ként lesz átadva. Bizonyos esetekben azonban, amikor a cookie-k le vannak tiltva, ez a token kérelem utáni argumentumként is át lesz adva. Emiatt az Azure AD-jogkivonat hamis pozitív megoldásához gondoskodnia kell arról, hogy az a `__RequestVerificationToken` és a kizárási listájához legyen hozzáadva `RequestCookieNames` `RequestBodyPostArgsNames` .

A mezőnév (választó) kizárása azt jelenti, hogy az értéket a WAF már nem értékeli *ki*. A mező neve azonban továbbra is ki lesz értékelve, és ritka esetekben előfordulhat, hogy egy WAF-szabálynak megfelelő, és műveletet indít el.

![Szabály kizárása a szabálykészlet számára](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF műveletek módosítása

A WAF-szabályok viselkedésének egy másik módja az, ha a kérés megfelel a szabály feltételeinek. Az elérhető műveletek a következők: [Engedélyezés, letiltás, napló és átirányítás](afds-overview.md#waf-actions).

Ebben a példában az alapértelmezett műveleti *blokkot* módosítottuk a *log* műveletre a 942110-es szabályon. Ez azt eredményezi, hogy a WAF naplózza a kérést, és továbbra is kiértékeli ugyanezt a kérést a fennmaradó alacsonyabb prioritási szabályokkal.

![WAF műveletek](../media/waf-front-door-tuning/actions.png)

Ha ugyanezt a kérést elvégezte, hivatkozhatunk a naplókra, és látni fogjuk, hogy ez a kérelem a 942110-as AZONOSÍTÓJÚ szabálynak felelt meg, és hogy a `action_s` mező most már a *blokkolás* helyett a *naplót* jelzi. Ezután kibővítettük a napló lekérdezését, hogy belefoglaljuk az `trackingReference_s` adatokat, és meglássuk, mi történt ezzel a kéréssel.

![Több szabály egyezését bemutató napló](../media/waf-front-door-tuning/actions-log.png)

Érdekes, hogy a 942110-es szabály feldolgozása után egy másik SQLI-szabály egyeztetése ezredmásodpercben történik. Ugyanez a kérelem megfelel a 942310-as szabálynak, és ezúttal az alapértelmezett műveleti *blokk* lett aktiválva.

A WAF finomhangolása vagy hibaelhárítása során a *log* művelet egy másik előnye, hogy azonosítani tudja, hogy egy adott szabálykészlet több szabálya felel-e meg egy adott kérésnek. Ezután létrehozhatja a kizárásokat a megfelelő szinten, azaz a szabály vagy a szabály csoport szintjén. 

### <a name="using-custom-rules"></a>Egyéni szabályok használata

Miután azonosította, hogy mi okozza a WAF-szabálynak megfelelőt, egyéni szabályok segítségével módosíthatja, hogy a WAF hogyan válaszoljon az eseményre. Az egyéni szabályok a felügyelt szabályok előtt lesznek feldolgozva, több feltételt is tartalmazhatnak, és a műveleteik [engedélyezheti, megtagadhatják, naplózzák vagy átirányíthatók](afds-overview.md#waf-actions). Ha egy szabály egyezik, a WAF motor leállítja a feldolgozást. Ez azt jelenti, hogy az alacsonyabb prioritású és felügyelt szabályokkal rendelkező egyéb egyéni szabályok már nem lesznek végrehajtva.

Az alábbi példában két feltételt tartalmazó egyéni szabályt hoztunk létre. Az első feltétel a `comment` kérelem törzsében található értéket keresi. A második feltétel a `/api/Feedbacks/` kérelem URI azonosítójában található értéket keresi.

Az egyéni szabályok lehetővé teszik a legrészletesebben a WAF-szabályok finomhangolását, valamint a hamis pozitív problémák kezelését. Ebben az esetben nem csak a `comment` kérelem törzse alapján történik művelet, amely több webhelyre vagy alkalmazásra is létezhet ugyanazon WAF-házirendben. Azzal, hogy egy másik feltételnek is egyeznie kell egy adott kérés URI azonosítóval `/api/Feedbacks/` , gondoskodunk róla, hogy ez az egyéni szabály valóban érvényes legyen erre a kifejezett használati esetre, amelyet bevettünk. Ez biztosítja, hogy ugyanazt a támadást, ha a különböző feltételekkel hajtják végre, továbbra is meg kell vizsgálni és meg kell akadályozni a WAF motor.

![Napló](../media/waf-front-door-tuning/custom-rule.png)

A napló feltárásakor láthatja, hogy a `ruleName_s` mező tartalmazza a létrehozott egyéni szabály nevét: `redirectcomment` . A `action_s` mezőben láthatja, hogy az adott esemény *átirányítási* művelete megtörtént. A `details_matches_s` mezőben láthatjuk, hogy mindkét feltétel részletei megegyeznek.

### <a name="disabling-rules"></a>Szabályok letiltása

Egy másik módszer a hamis pozitív megoldás megszerzésére, hogy letiltsa a bemenetnek megfelelő szabályt, amely a WAF szerint rosszindulatú volt. Mivel elemezte a WAF-naplókat, és letiltotta a szabályt a 942110-ra, letilthatja a Azure Portal. Lásd: [webalkalmazási tűzfalszabályok testreszabása a Azure Portal használatával](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Ha biztos abban, hogy az adott feltételnek megfelelő kérések letiltanak egy adott szabályt, vagy ha biztos abban, hogy a szabály egyszerűen nem vonatkozik a környezetre (például az SQL-injektálási szabály letiltására, mert nem SQL-háttérrel rendelkezik). 
 
A szabályok letiltása azonban globális beállítás, amely a WAF házirendhez társított összes előtér-gazdagépre vonatkozik. Ha egy szabály letiltását választja, akkor előfordulhat, hogy a WAF-házirendhez társított egyéb előtér-gazdagépek védelme vagy észlelése nélkül kilép a biztonsági rések ellen.
 
Ha a felügyelt szabály letiltásához Azure PowerShellt szeretne használni, tekintse meg az [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) objektum dokumentációját. Ha az Azure CLI-t szeretné használni, tekintse meg a [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override) dokumentációt.

![WAF-szabályok](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> Érdemes dokumentálni a WAF-szabályzatban végrehajtott módosításokat. Példákat tartalmaz a hamis pozitív észlelés szemléltetésére, és egyértelműen megmagyarázza, miért adtak hozzá egyéni szabályt, letiltottak egy szabályt vagy szabályrendszert, vagy hozzáadtak egy kivételt. Ez a dokumentáció akkor lehet hasznos, ha a jövőben újratervezi az alkalmazást, és ellenőriznie kell, hogy a módosítások még érvényesek-e. A szolgáltatás segítséget nyújt abban az esetben is, ha bármikor naplózva van, vagy meg kell indokolnia, hogy miért konfigurálta újra a WAF házirendet az alapértelmezett beállítások alapján.

## <a name="finding-request-fields"></a>Kérelem mezőinek keresése

A [Hegedűs](https://www.telerik.com/fiddler)használatával megvizsgálhatja az egyes kéréseket, és meghatározhatja a weblapok adott mezőit. Ez akkor hasznos, ha ki kell zárni bizonyos mezőket a WAF-ben található kizárási listával.

### <a name="finding-request-attribute-names"></a>Kérelem-attribútumok nevének megállapítása
 
Ebben a példában megtekintheti azt a mezőt, ahol a `1=1` karakterláncot megadta `comment` . Ezeket az adatkéréseket a POST kérelem törzsében adták át.

![Hegedűs-kérelem törzsének megjelenítése](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Ezt a mezőt kizárhatja. További információ a kizárási listáról: [webalkalmazási tűzfal kizárási listája](./waf-front-door-exclusion.md). Ebben az esetben kizárhatja a kiértékelést a következő kizárás konfigurálásával:

![Kizárási szabály](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Azt is megvizsgálhatja, hogy a tűzfal naplófájljaiban megtekintheti, hogy mire van szükség a kizárási listához való hozzáadásához. A naplózás engedélyezéséhez lásd: [mérőszámok és naplók figyelése az Azure-beli bejárati ajtón](./waf-front-door-monitor.md).

Vizsgálja meg a tűzfal naplófájljában azt az `PT1H.json` órát, ameddig a vizsgálat megtörtént. `PT1H.json` a fájlok a Storage-fiók tárolókban érhetők el `FrontDoorWebApplicationFirewallLog` , ahol a és a `FrontDoorAccessLog` diagnosztikai naplók tárolódnak.

Ebben a példában megtekintheti a kérést letiltó szabályt (ugyanazzal a tranzakciós hivatkozással), és a pontos időpontot is megtörtént:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Ismerje meg, hogy az Azure által felügyelt szabályok hogyan működnek (lásd: [webalkalmazási tűzfal az Azure-beli bejárati ajtón](afds-overview.md)) arról, hogy a *művelet: Block* tulajdonság blokkolása a kérelem törzsében megegyező adatmennyiség alapján történik. Megtekintheti a mintázatnak megfelelő részleteket ( `1=1` ), és a mező neve `comment` . Az előző lépések végrehajtásával zárja ki a kérelem törzsét a tartalmazni kívánt argumentumok után `comment` .

### <a name="finding-request-header-names"></a>Kérelmek fejlécének neveinek keresése

A Hegedűs egy hasznos eszköz a kérelmek fejlécének neveinek megkereséséhez. A következő képernyőképen megtekintheti a GET kérelem fejléceit, amelyek közé tartozik a Content-Type, a User-Agent stb. A kérések fejléceit is használhatja kizárások és egyéni szabályok létrehozására a WAF-ben.

![A "Hegedűs"-kérelem fejléce](../media/waf-front-door-tuning/fiddler-request-header-name.png)

A kérések és válaszok fejlécek megtekintésének egy másik módja, ha a böngésző fejlesztői eszközein (például a Edge vagy a Chrome) belül keres. Nyomja le az F12 billentyűt, vagy kattintson a jobb gombbal > **vizsgálja** meg  ->  a **fejlesztői eszközök**, majd válassza a **hálózat** fület. töltsön be egy weblapot, és kattintson a vizsgálni kívánt kérelemre.

![Hálózati felügyelői kérelem](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Kérelmek cookie-neveinek keresése

Ha a kérelem cookie-kat tartalmaz, a cookie-k lapon lehet megtekinteni őket a Hegedűsben. A cookie-adatok használatával kizárásokat vagy egyéni szabályokat hozhat létre a WAF-ben.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az [Azure webalkalmazási tűzfalat](../overview.md).
- Útmutató a [Front Door létrehozásához](../../frontdoor/quickstart-create-front-door.md).
