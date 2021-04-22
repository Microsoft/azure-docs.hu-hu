---
title: Hangolási Web Application Firewall (WAF) a Azure Front Door
description: Ebből a cikkből megtudhatja, hogyan finomhangolhatja a WAF-Front Door.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: c0879edc0e3fbd6cf6bcadc26dd862f95ecf4fd4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872352"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Hangolási Web Application Firewall (WAF) a Azure Front Door
 
Az Azure által felügyelt alapértelmezett szabálykészlet az [OWASP Alapvető szabálykészleten (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) alapul, és szigorú használatra készült. Gyakran előfordul, hogy a WAF-szabályokat a WAF-et használó alkalmazás vagy szervezet igényeinek megfelelően kell hangolni. Ez általában szabálykizárások definiálásával, egyéni szabályok létrehozásával, vagy akár olyan szabályok letiltásával érhető el, amelyek problémákat vagy téves riasztásokat okozhatnak. Van néhány dolog, amit akkor lehet megtenni, ha a waf Web Application Firewall áthaladó kérések le vannak tiltva.

Először is olvassa el a [waf Front Door WAF](afds-overview.md) áttekintését és a [waf-szabályzatot a Front Door](waf-front-door-create-portal.md) dokumentumokhoz. Győződjön meg arról is, hogy engedélyezte a [WAF monitorozását és naplózását.](waf-front-door-monitor.md) Ezek a cikkek ismertetik a WAF működését, a WAF-szabálykészletek működését és a WAF-naplókhoz való hozzáférést.
 
## <a name="understanding-waf-logs"></a>A WAF-naplók ismertetése
 
A WAF-naplók célja a WAF által egyező vagy blokkolt összes kérés megjelenítése. Ez az összes megfelelt vagy blokkolt kiértékelt kérés gyűjteménye. Ha azt veszi észre, hogy a WAF blokkolja azt a kérést, amit nem kellene (téves riasztás), néhány dolgot is meg tud tenni. Először szűkítse le, és keresse meg az adott kérést. Ha szeretné, konfigurálhat [egy](./waf-front-door-configure-custom-response-code.md) egyéni válaszüzenetet úgy, hogy az tartalmazza a mezőt az esemény egyszerű azonosítása és az adott értékre vonatkozó `trackingReference` naplólekérdezés végrehajtása érdekében. A naplókban keresse meg a kérelem adott URI-ját, időbélyegét vagy ügyfél IP-címét. Ha megtalálja a kapcsolódó naplóbejegyzéseket, elkezdheti a téves riasztásokat. 
 
Tegyük fel például, hogy egy megbízható forgalom tartalmazza a WAF-kapcsolaton áthaladni kívánt `1=1` sztringet. A kérés így néz ki:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Ha kipróbálja a kérést, a WAF letiltja az *1=1* sztringet tartalmazó forgalmat bármely paraméterben vagy mezőben. Ez egy gyakran SQL-injektálásos támadással társított sztring. Tekintse át a naplókat, és tekintse meg a kérés időbélyegét, valamint a blokkolt/egyező szabályokat.
 
A következő példában egy szabály egyezése miatt `FrontdoorWebApplicationFirewallLog` létrehozott naplót vizsgálunk meg. A következő Log Analytics-lekérdezéssel megkeresheti az elmúlt 24 órában blokkolt kéréseket:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
A `requestUri` mezőben láthatja, hogy a kérés konkrétan a következőhöz `/api/Feedbacks/` lett megkért. Tovább ásva megkeresjük a szabályazonosítót `942110` a `ruleName` mezőben. A szabályazonosító ismerete után az [OWASP ModSecurity Core Rule Set Official](https://github.com/coreruleset/coreruleset) Adattárban kereshet a szabályazonosító alapján, hogy áttekintse a kódját, és pontosan tudja, hogy ez a szabály pontosan mire illeszkedik. [](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) 
 
Ezután a mező ellenőrzését követően azt látjuk, hogy ez a szabály úgy van beállítva, hogy blokkolja a kéréseket egyezéskor, és megerősítjük, hogy a WAF valójában blokkolta a kérést, mert a beállítása `action` `policyMode` `prevention` . 
 
Most ellenőrizzük a mezőben található `details` információkat. Itt láthatja a és az `matchVariableName` `matchVariableValue` információt. Tudjuk, hogy ez a szabály azért aktiválódott, mert valaki *1=1-et* ad meg `comment` a webalkalmazás mezőjében.
 
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
 
A hozzáférési naplók ellenőrzésével egy adott WAF-eseményre vonatkozó tudását is bővítheti. Az alábbiakban áttekintjük a fenti eseményre adott válaszként `FrontdoorAccessLog` létrehozott naplót.
 
Láthatja, hogy ezek a kapcsolódó naplók az értékük `trackingReference` alapján azonosak. A és a mezők között, amelyek általános elemzéseket biztosítanak, mint például a és a, felhívjuk a figyelmet a és a `userAgent` `clientIP` `httpStatusCode` `httpStatusDetails` mezőre. Itt ellenőrizhetjük, hogy az ügyfél kapott-e HTTP 403-as választ, amely teljes mértékben megerősíti, hogy a kérést a rendszer megtagadta és blokkolta. 
 
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

## <a name="resolving-false-positives"></a>Téves riasztások feloldása
 
Ahhoz, hogy tájékozott döntést hozhassanak a téves riasztások kezelésével kapcsolatban, fontos, hogy megismerkedjen az alkalmazás által használt technológiákkal. Tegyük fel például, hogy a technológiai veremnek nincs SQL-kiszolgálója, és téves riasztásokat kap a szabályokkal kapcsolatban. Ezeknek a szabályoknak a letiltása nem feltétlenül jelenti a biztonságot. 

Ezen információk és a 942110-es szabálynak a példánkban található sztringnek megfelelő tudás birtokában néhány dolgot tehetünk a szabályos kérés `1=1` letiltásának érdekében:
 
* Kizárási listák használata
  * A Web Application Firewall listákkal kapcsolatos további információkért tekintse Web Application Firewall (WAF) a Front Door [szolgáltatáskizárási](waf-front-door-exclusion.md) listákkal. 
* WAF-műveletek módosítása
  * A [WAF Actions (WAF-műveletek)](afds-overview.md#waf-actions) oldalon további információt kaphat arról, hogy milyen műveleteket lehet tenni, ha egy kérés megfelel egy szabály feltételeinek.
* Egyéni szabályok használata
  * Az [egyéni szabályokkal](waf-front-door-custom-rules.md) Web Application Firewall Azure Front Door egyéni szabályokról további információt az Egyéni szabályok a szabályzatokkal kapcsolatban.
* Szabályok letiltása 

> [!TIP]
> Ha olyan módszert választ, amely engedélyezi a megbízható kéréseket a WAF-hoz, próbálja meg a lehető legszűkebbre szűkíteni a kéréseket. Érdemesebb például kizárási listát használni, mint teljesen letiltani egy szabályt.

### <a name="using-exclusion-lists"></a>Kizárási listák használata

A kizárási lista használatának egyik előnye, hogy csak a kizárni kívánt egyezésű változót vizsgálja a rendszer a továbbiakban az adott kérésre. Ez azt jelenti, hogy választhat adott kérésfejlécek, cookie-k kérése, lekérdezési sztring argumentumai vagy a kérelem törzsének argumentumai közül, amelyek kizárhatók egy adott feltétel teljesüléséből, nem pedig kizárhatja a teljes kérést a vizsgálatból. A kérés többi nem megadott változója továbbra is a szokásos módon lesz megvizsgálva.
 
Fontos figyelembe venni, hogy a kizárások globális beállítások. Ez azt jelenti, hogy a konfigurált kizárás a WAF-ön áthaladó összes forgalomra vonatkozik, nem csak egy adott webalkalmazásra vagy URI-ra. Ez például aggodalomra ad okot, ha *az 1=1* érvényes kérés a törzsben egy adott webalkalmazásra, de más, ugyanazon WAF-szabályzat alá nem tartozik. Ha különböző kizárási listákat érdemes használni a különböző alkalmazásokhoz, fontolja meg különböző WAF-szabályzatok használatát az egyes alkalmazásokhoz, és alkalmazza őket az egyes alkalmazások előtere esetében.
 
A felügyelt szabályok kizárási listáinak konfigurálásakor kizárhatja a szabálykészleten belüli összes szabályt, a szabálycsoporton belüli összes szabályt vagy egy adott szabályt. Kizárási lista konfigurálható a [PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject) [az Azure CLI,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add)a [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)vagy a Azure Portal.

* Kizárások szabályszinten
  * A kizárások szabályszinten való alkalmazása azt jelenti, hogy a megadott kizárásokat a rendszer nem csak az adott szabály alapján elemzi, a szabálykészlet összes többi szabálya viszont továbbra is elemzi őket. Ez a kizárások legszűkebb szintje, és a segítségével finomhangolhatja a felügyelt szabálykészletet a WAF-naplókban található információk alapján az események hibaelhárítása során.
* Kizárások szabálycsoportszinten
  * A kizárások szabálycsoport szintjén való alkalmazása azt jelenti, hogy a megadott kizárások nem lesznek elemezve az adott szabálytípusokon. Ha például az *SQLI-t* kiválasztja kizárt szabálycsoportként, az azt jelzi, hogy a meghatározott kérelemkivételeket egyetlen SQLI-specifikus szabály sem vizsgálná meg, de más csoportok( *például PHP,* *RFI* vagy *XSS)* szabályai továbbra is vizsgálnák. Ez a kizárási típus akkor lehet hasznos, ha biztos lehet abban, hogy az alkalmazás nem ki van téve bizonyos típusú támadásoknak. Egy OLYAN alkalmazás például, amely nem tartalmazza az SQL-adatbázisokat, kizárhatja az *összes SQLI-szabályt* anélkül, hogy az hátrányosan befolyásolhatja annak biztonsági szintjét.
* Kizárások a szabálykészlet szintjén 
  * A kizárások szabálykészlet-szinten való alkalmazása azt jelenti, hogy a rendszer nem elemzi a megadott kizárásokat a szabálykészletben elérhető biztonsági szabályok alapján. Ez egy átfogó kizárás, ezért körültekintően kell használni.

Ebben a példában kizárást hajtunk végre a leg granuláris szinten (egyetlen szabályra alkalmazva a kizárást), és ki szeretnénk zárni a Request **body post args name** változót, amely tartalmazza a következőt: `comment` . Ez azért nyilvánvaló, mert a tűzfalnaplóban láthatja az egyező változó részleteit: `"matchVariableName": "PostParamValue:comment"` . Az attribútum `comment` a következő: . Ezt az attribútumnevet más módokon is megkeresheti: [Kérelemattribútum-nevek keresése.](#finding-request-attribute-names)

![Kizárási szabályok](../media/waf-front-door-tuning/exclusion-rules.png)

![Szabálykizárás adott szabályhoz](../media/waf-front-door-tuning/exclusion-rule.png)

Bizonyos esetekben előfordulhat, hogy bizonyos paramétereket nem intuitív módon ad át a WAF-nak. Van például egy jogkivonat, amely akkor lesz átkulálva, amikor az Azure Active Directory. Ezt a `__RequestVerificationToken` jogkivonatot () általában kérési cookie-ként ad át a rendszer. Bizonyos esetekben azonban, ahol a cookie-k le vannak tiltva, ez a jogkivonat kérés utáni argumentumként is át lesz stb. Ezért az Azure AD-jogkivonatok téves pozitív eredményének kezelése érdekében meg kell győződni arról, hogy a és a kizárási listájához is hozzá van `__RequestVerificationToken` `RequestCookieNames` `RequestBodyPostArgsNames` adva.

A mezőnév kizárásai (*választó*) azt jelenti, hogy a WAF már nem értékeli ki az értéket. Maga a mezőnév azonban továbbra is ki lesz értékelve, és ritka esetekben megfelelhet egy WAF-szabálynak, és elindíthat egy műveletet.

![Szabálykészlet szabálykizárása](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF-műveletek módosítása

A WAF-szabályok viselkedésének másik módja az, ha kiválasztja azt a műveletet, amelyet akkor fog elhozni, ha egy kérés megfelel egy szabály feltételeinek. A következő műveletek érhetők el: [Allow (Engedélyezése, blokkolása, naplózása) és Redirect (Átirányítás).](afds-overview.md#waf-actions)

Ebben a példában a  Block (Blokkolás) alapértelmezett műveletet a 942110-es szabály Napló műveletét módosította.  Ennek hatására a WAF naplózhatja a kérést, és folytathatja ugyanazon kérés kiértékelését a fennmaradó alacsonyabb prioritású szabályokkal szemben.

![WAF-műveletek](../media/waf-front-door-tuning/actions.png)

Ugyanezen kérés végrehajtása után vissza lehet hivatkozni a naplókra, és látni fogjuk, hogy ez a kérés megfelelt a 942110-es szabályazonosítónak, és a mező most már a Log (Napló) adatokat jelzi a Block (Blokkolás) `action_s` helyett.   Ezután kibontottunk egy naplólekérdezés, hogy tartalmazza az információkat, és lássuk, mi `trackingReference_s` történt még ezzel a kéréssel.

![Több szabály egyezését bemutató napló](../media/waf-front-door-tuning/actions-log.png)

Érdekes módon azt látjuk, hogy a 942110-es szabályazonosító feldolgozása után egy másik SQLI-szabály egyezése ezredmásodpercben történik. Ugyanez a kérés megfelelt a 942310-es szabályazonosítónak, és ezúttal az alapértelmezett *Block* művelet aktiválódott.

A WAF  finomhangolása vagy hibaelhárítása során a Napló művelet használatának egy másik előnye, hogy megállapíthatja, hogy egy adott szabálycsoporton belül több szabály megfelel-e egy adott kérésnek, és blokkolja-e azokat. Ezután a kizárásokat a megfelelő szinten, például a szabály vagy a szabálycsoport szintjén hozhatja létre. 

### <a name="using-custom-rules"></a>Egyéni szabályok használata

Miután azonosította, mi okozza a WAF-szabály egyezését, egyéni szabályokkal módosíthatja, hogyan reagáljon a WAF az eseményre. Az egyéni szabályok a felügyelt szabályok előtt lesznek feldolgozva, több feltételt is tartalmazhatnak, és a műveletük lehet Allow [(Engedélyező) , Deny (Megtagadás), Log (Napló) vagy Redirect (Átirányítás).](afds-overview.md#waf-actions) Szabály egyezése után a WAF-motor leáll. Ez azt jelenti, hogy az alacsonyabb prioritású és felügyelt szabályokkal már nem lesz végrehajtva más egyéni szabályok.

Az alábbi példában létrehoztunk egy két feltételt is megszabadó egyéni szabályt. Az első feltétel a kérelem törzsében keresi az `comment` értéket. A második feltétel a kérelem `/api/Feedbacks/` URI-ját keresi.

Egyéni szabály használatával a legszemlesebb lehet a WAF-szabályok finomhangolása és a téves riasztások kezelése során. Ebben az esetben nem csak a kérelem törzsének értéke alapján fogunk műveletet eszközül, amely több helyen vagy alkalmazásban is létezhet ugyanazon `comment` WAF-szabályzatban. Azáltal, hogy egy másik feltételt is meg kell egyeznie egy adott kérelem URI-ján, biztosítjuk, hogy ez az egyéni szabály valóban érvényes legyen erre az explicit használatra, amelyet `/api/Feedbacks/` ellenőriztünk. Ez biztosítja, hogy a WAF-motor akkor is vizsgálja és megakadályozza ugyanazt a támadást, ha más körülmények között történt.

![Napló](../media/waf-front-door-tuning/custom-rule.png)

A napló feltárásakor láthatja, hogy a mező tartalmazza a létrehozott egyéni szabály `ruleName_s` nevét: `redirectcomment` . A `action_s` mezőben láthatja, hogy az *átirányítási* művelet történt ehhez az eseményhez. A `details_matches_s` mezőben látható, hogy mindkét feltétel adatai megegyeztek.

### <a name="disabling-rules"></a>Szabályok letiltása

Egy másik módszer a téves riasztások kiszűrére, ha letiltja a WAF által kártékonynak hitt bemenettel egyező szabályt. Mivel elemezte a WAF-naplókat, és leszűkítette a szabályt a 942110-esre, letilthatja a Azure Portal. Lásd: [Web Application Firewall szabályok testreszabása a Azure Portal.](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules)
 
A szabály letiltása akkor hasznos, ha biztos abban, hogy az adott feltételnek megfelelő összes kérelem valóban szabályos kérés, vagy ha biztos abban, hogy a szabály egyszerűen nem vonatkozik a környezetére (például letilt egy SQL-injektálásos szabályt, mert nem SQL-háttérkörnyezettel rendelkezik). 
 
A szabályok letiltása azonban globális beállítás, amely a WAF-szabályzathoz társított összes előtere-gazdagépre vonatkozik. Ha úgy dönt, hogy letilt egy szabályt, előfordulhat, hogy a WAF-szabályzathoz társított többi előtere gazdagép biztonsági réseit védelem vagy észlelés nélkül teszi elérhetővé.
 
Ha egy felügyelt szabály letiltásához Azure PowerShell szeretne használni, tekintse meg az objektum [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) dokumentációját. Ha az Azure CLI-t szeretné használni, tekintse meg a [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) dokumentációt.

![WAF-szabályok](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> A WAF-szabályzaton végrehajtott módosításokat javasolt dokumentálni. A téves pozitív észlelés szemléltetésére példakéréseket is tartalmazhat, és egyértelműen ismertetheti, hogy miért adott hozzá egyéni szabályt, letiltott egy szabályt vagy szabálykészletet, illetve miért adott hozzá kivételt. Ez a dokumentáció hasznos lehet, ha a jövőben újratervezte az alkalmazást, és ellenőriznie kell, hogy a módosítások érvényesek-e. Abban is segíthet, ha bármikor naplózva van, vagy meg kell indokolnia, hogy miért konfigurálta újra a WAF-szabályzatot az alapértelmezett beállításai alapján.

## <a name="finding-request-fields"></a>Kérelemmezők keresése

Egy böngészőproxy, például a [Fiddler](https://www.telerik.com/fiddler)használatával megvizsgálhatja az egyes kéréseket, és meghatározhatja, hogy egy weblap mely mezőit hívják meg. Ez akkor hasznos, ha bizonyos mezőket ki kell zárni a WAF kizárási listái használatával végzett vizsgálatból.

### <a name="finding-request-attribute-names"></a>Kérelemattribútumok nevének keresése
 
Ebben a példában a sztringet megadva a nevű mezőt `1=1` `comment` láthatja. Ezek az adatok egy POST-kérés törzsében lett átkért adatok.

![A Fiddler-kérelem törzse](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Ezt a mezőt kizárhatja. A kizárási listákkal kapcsolatos további információkért lásd: [Webalkalmazási tűzfalak kizárási listái.](./waf-front-door-exclusion.md) Ebben az esetben a kiértékelést a következő kizárás konfigurálásával zárhatja ki:

![Kizárási szabály](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

A tűzfalnaplók vizsgálatával le is kaphatja az információkat, hogy lássa, mit kell hozzáadnia a kizárási listához. A naplózás engedélyezéséhez lásd: Metrikák és naplók [monitorozása a Azure Front Door.](./waf-front-door-monitor.md)

Vizsgálja meg a fájlban található tűzfalnaplót abban az órában, amikor a vizsgálni `PT1H.json` kívánt kérés bekövetkezett. `PT1H.json` A fájlok a tárfiók tárolóiban érhetők el, ahol a és a `FrontDoorWebApplicationFirewallLog` `FrontDoorAccessLog` diagnosztikai naplók vannak tárolva.

Ebben a példában láthatja a szabályt, amely blokkolta a kérést (ugyanazokkal a tranzakcióhivatkozással), és pontosan ugyanakkor történt:

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

*Az* Azure által felügyelt szabálykészletek (lásd: [Web Application Firewall on Azure Front Door](afds-overview.md)) ismeretében tudja, hogy a következő művelettel: Block tulajdonság blokkolva van a kérelem törzsében egyező adatok alapján. A részletekben láthatja, hogy megfelelt egy mintának ( ), és a `1=1` mező neve `comment` . Kövesse az előző lépéseket a kérelmet tartalmazó post args név utáni `comment` kizárásához.

### <a name="finding-request-header-names"></a>Kérelemfejlécek nevének megkeresása

A Fiddler ismét hasznos eszköz a kérelemfejlécek nevének megkereséhez. Az alábbi képernyőképen láthatja a GET kérelem fejlécét, amely tartalmazza a Content-Type (Tartalomtípus) és a User-Agent (Felhasználói ügynök) stb. fejléceket. A WAF-ban a kérelemfejlécekkel kizárásokat és egyéni szabályokat is létrehozhat.

![Fejlécet megjelenítő Fiddler-kérelem](../media/waf-front-door-tuning/fiddler-request-header-name.png)

A kérés- és válaszfejlécek megtekintésének másik módja a böngésző fejlesztői eszközeinek, például az Edge vagy a Chrome megtekintése. Nyomja le az F12 billentyűt, vagy kattintson a jobb gombbal a -> **Inspect** Fejlesztői eszközök elemre, és válassza  ->  a **Hálózat** lapot. Weblap betöltése, majd kattintson a megvizsgálni kívánt kérésre.

![Hálózati vizsgáló kérése](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Kérési cookie-nevek keresése

Ha a kérés cookie-kat tartalmaz, a Cookie-k lapon megtekintheti őket a Fiddlerben. A cookie-adatok kizárások vagy egyéni szabályok létrehozására is használhatók a WAF-ban.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók az [Azure webalkalmazási tűzfalról.](../overview.md)
- Útmutató a [Front Door létrehozásához](../../frontdoor/quickstart-create-front-door.md).
