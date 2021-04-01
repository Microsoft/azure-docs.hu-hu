---
title: Hibakeresés – Azure webalkalmazási tűzfal
description: Ez a cikk az Azure-hoz készült webalkalmazási tűzfal (WAF) hibaelhárítási információit ismerteti Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92131836"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Az Azure Application Gateway webalkalmazási tűzfalának (WAF) hibáinak megoldása

Néhány dolog elvégezhető, ha a webalkalmazási tűzfalon (WAF) továbbított kérések le vannak tiltva.

Először is győződjön meg róla, hogy elolvasta a [WAF áttekintését](ag-overview.md) és a [WAF konfigurációs](application-gateway-waf-configuration.md) dokumentumait. Győződjön meg arról is, hogy engedélyezte a [WAF-figyelést](../../application-gateway/application-gateway-diagnostics.md) . Ezek a cikkek ismertetik a WAF működését, a WAF-szabály működését, valamint a WAF-naplók elérését.

A OWASP-szabályrendszerek úgy vannak kialakítva, hogy nagyon szigorúak legyenek a dobozból, és az alkalmazás vagy a szervezet konkrét igényeinek megfelelően hangolják magukat a WAF használatával. Ez teljesen normális, és valójában sok esetben várható, a kizárások, az egyéni szabályok létrehozása és a hibák vagy a téves pozitívan okozó szabályok letiltása is. A hely és az URI-alapú házirendek lehetővé teszik, hogy ezek a módosítások csak bizonyos helyeket vagy URI-ket érintsenek, ezért a módosításoknak nem kell más olyan helyeket érinteniük, amelyek esetleg nem futnak az adott problémákkal. 

## <a name="understanding-waf-logs"></a>A WAF-naplók ismertetése

A WAF-naplók célja, hogy minden, a WAF által egyeztetett vagy letiltott kérelmet megjelenítse. Ez az összes kiértékelt kérelem főkönyve, amely megfelel vagy le van tiltva. Ha azt tapasztalja, hogy a WAF blokkol egy olyan kérést, amely nem (hamis pozitív), elvégezhet néhány dolgot. Először, Szűkítse le, és keresse meg az adott kérést. Tekintse át a naplókat, hogy megkeresse a kérelem egyedi URI-JÁT, időbélyegét vagy tranzakciós AZONOSÍTÓját. A társított naplóbejegyzések megkeresése után megkezdheti a hamis pozitív műveletek elvégzését.

Tegyük fel például, hogy rendelkezik egy olyan legitim forgalommal, amely az *1 = 1* karakterláncot tartalmazza, amelyet át szeretne adni a WAF. Ha kipróbálja a kérelmet, a WAF letiltja az *1 = 1* karakterláncot tartalmazó forgalmat bármely paraméterben vagy mezőben. Ez az SQL-injektálási támadáshoz gyakran társított karakterlánc. Megtekintheti a naplókat, és megtekintheti a kérés időbélyegét és a blokkolt/egyeztetett szabályokat.

A következő példában láthatja, hogy a rendszer négy szabályt indít el ugyanabban a kérésben (a tranzakcióazonosító mező használatával). Az első azt mondja, hogy megegyeznek, mert a felhasználó a kérelemhez numerikus/IP URL-címet használt, amely az anomália pontszámát háromra emeli, mivel ez egy figyelmeztetés. Az egyeztetett következő szabály a 942130, amely az, amit keres. A mezőben az *1 = 1* érték látható `details.data` . Ez tovább növeli az anomália pontszámát három újra, mivel ez egy figyelmeztetés is. Általánosságban elmondható, hogy minden olyan szabály, amely **megfelel** a műveletnek, növeli az anomália pontszámát, és ezen a ponton a anomália értéke hat lenne. További információ: [anomália pontozási mód](ag-overview.md#anomaly-scoring-mode).

A két utolsó naplóbejegyzés azt mutatja, hogy a kérelem le lett tiltva, mert a rendellenesség pontszáma elég magas volt. Ezek a bejegyzések más művelettel rendelkeznek, mint a másik kettő. Megmutatják, hogy ténylegesen *letiltották* a kérést. Ezek a szabályok kötelezőek, és nem tilthatók le. Nem gondolnak a szabályokra, hanem a belső WAF alapvető infrastruktúráját.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Hamis pozitív elemek javítása

Ezekkel az információkkal és a 942130-es szabálynak az *1 = 1* sztringnek megfelelő ismerettel a következő lépések elvégzésével állíthatja le a forgalmat:

- Kizárási lista használata

   A kizárási listával kapcsolatos további információkért tekintse meg a [WAF konfigurációját](application-gateway-waf-configuration.md#waf-exclusion-lists) .
- Tiltsa le a szabályt.

### <a name="using-an-exclusion-list"></a>Kizárási lista használata

Ahhoz, hogy tájékozott döntést hozzon a hamis pozitív megoldásokról, fontos, hogy megismerkedjen az alkalmazás által használt technológiákkal. Tegyük fel például, hogy a technológiai veremben nincs SQL Server-kiszolgáló, és a szabályokkal kapcsolatos téves pozitív eredményt kap. A szabályok letiltása nem feltétlenül gyengíti a biztonságot.

A kizárási lista használatának egyik előnye, hogy csak a kérés egy adott részét tiltja le a rendszer. Azonban ez azt jelenti, hogy egy adott kizárás a WAF áthaladó összes forgalomra érvényes, mivel ez egy globális beállítás. Ez például akkor okozhat problémát, ha *1 = 1* érvényes kérelem a törzsben egy adott alkalmazáshoz, de mások számára nem. Egy másik előny, hogy kiválaszthatja a törzs, a fejlécek és a cookie-k kizárását, ha egy bizonyos feltétel teljesül, a teljes kérelem kizárása helyett.

Időnként előfordulhat, hogy bizonyos paramétereket nem lehet intuitív módon átadni a WAF. Például van egy token, amely a Azure Active Directory használatával történő hitelesítéskor lesz átadva. Ez a token, *__RequestVerificationToken*, általában kérelem cookie-ként kerül beolvasásra. Bizonyos esetekben azonban, amikor a cookie-k le vannak tiltva, ez a token kérelem-attribútumként vagy "ARG"-ként is át lesz adva. Ha ez történik, gondoskodnia kell arról, hogy a *__RequestVerificationToken* a rendszer hozzáadja a kizárási listához a **kérelem-attribútum neveként** is.

![Kizárások](../media/web-application-firewall-troubleshoot/exclusion-list.png)

Ebben a példában szeretné kizárni a **kérelem attribútumának nevét** , amely egyenlő a *TEXT1*. Ennek az az oka, hogy az attribútum neve látható a tűzfal naplófájljaiban: **adatértékek: megfeleltetett adatértékek: 1 = 1 a következő argumentumok között: TEXT1:1 = 1**. Az attribútum a **TEXT1**. Ezt az attribútumot néhány más módon is megkeresheti, lásd: a [kérelmek attribútumainak neve](#finding-request-attribute-names).

![WAF kizárási listája](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Szabályok letiltása

Egy másik módszer a hamis pozitív megoldás megszerzésére, hogy letiltsa a bemenetnek megfelelő szabályt, amely a WAF szerint rosszindulatú volt. Mivel elemezte a WAF-naplókat, és letiltotta a szabályt a 942130-ra, letilthatja a Azure Portal. Lásd: [webalkalmazási tűzfalszabályok testreszabása a Azure Portalon keresztül](application-gateway-customize-waf-rules-portal.md).

Egy szabály letiltásának egyik előnye, hogy ha ismeri az összes olyan forgalmat, amely egy általában blokkolni kívánt állapotot tartalmaz, akkor letilthatja ezt a szabályt a teljes WAF. Ha azonban csak egy adott használati eset érvényes forgalma, akkor a biztonsági rést úgy nyithatja meg, hogy letiltja ezt a szabályt a teljes WAF, mivel ez globális beállítás.

Ha Azure PowerShell szeretne használni, tekintse meg a [webalkalmazási tűzfalszabályok testreszabása a PowerShell](application-gateway-customize-waf-rules-powershell.md)használatával című témakört. Ha az Azure CLI-t szeretné használni, tekintse meg [a webalkalmazási tűzfalszabályok testreszabása az Azure CLI](application-gateway-customize-waf-rules-cli.md)-n keresztül című témakört.

![WAF-szabályok](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Kérelem-attribútumok nevének megállapítása

A [Hegedűs](https://www.telerik.com/fiddler)segítségével megvizsgálhatja az egyes kéréseket, és meghatározhatja a weblapok adott mezőit. Ez segít kizárni bizonyos mezőket az ellenőrzésből a kizárási listák használatával.

Ebben a példában láthatja, hogy az *1 = 1* karakterláncot megadó mező neve **TEXT1**.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Képernyőkép a Telerik Hegedűs web Debugger folyamatáról. A RAW lapon az 1 = 1 érték látható a TEXT1 neve után." border="false":::

Ezt a mezőt kizárhatja. Ha többet szeretne megtudni a kizárási listáról, tekintse meg a [webalkalmazási tűzfal kérelmekre vonatkozó korlátozásait és a kizárási listát](application-gateway-waf-configuration.md#waf-exclusion-lists). Ebben az esetben kizárhatja a kiértékelést a következő kizárás konfigurálásával:

![WAF kizárása](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Azt is megvizsgálhatja, hogy a tűzfal naplófájljaiban megtekintheti, hogy mire van szükség a kizárási listához való hozzáadásához. A naplózás engedélyezéséhez tekintse [meg a háttér állapota, az erőforrás-naplók és a metrikák Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Vizsgálja meg a tűzfal naplóját, és tekintse meg azt a PT1H.jsa fájlban, ameddig a megvizsgálni kívánt kérelem órája megtörtént.

Ebben a példában láthatja, hogy négy olyan szabálya van, amely ugyanazzal a tranzakcióazonosító rendelkezik, és ezek mindegyike pontosan egy időben történt:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Ismerje meg, hogy a CRS-szabályok hogyan működnek, és hogy a CRS-ek 3,0-as verziójának működése anomália-pontozási rendszerrel működik (lásd: [webalkalmazási tűzfal az Azure](ag-overview.md)-ban Application Gateway) arról, hogy az alsó két szabály a következő **művelettel: a blokkolt** tulajdonság a teljes anomália pontszám alapján blokkolható. A összpontosítani kívánt szabályok az első kettő.

Az első bejegyzést a rendszer naplózza, mert a felhasználó egy numerikus IP-címet használt a Application Gatewayhoz való kapcsolódáshoz, amely ebben az esetben figyelmen kívül hagyható.

A második (942130-es szabály) az egyik érdekes. Megtekintheti a részleteket, amelyek megfelelnek egy mintának (1 = 1), és a mező neve **TEXT1**. Az előző lépések végrehajtásával zárja ki a **kérelem attribútumának nevét** , amely **1 = 1** értékkel **egyenlő** .

## <a name="finding-request-header-names"></a>Kérelmek fejlécének neveinek keresése

A Hegedűs egy hasznos eszköz a kérelmek fejlécének neveinek megkereséséhez. A következő képernyőképen megtekintheti a GET kérelem fejléceit, amelyek közé tartozik a *Content-Type*, a *User-Agent* stb.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Képernyőkép a Telerik Hegedűs web Debugger folyamatáról. A nyers lap felsorolja a kérelmek fejlécének részleteit, például a kapcsolat, a tartalom típusát és a felhasználói ügynököt." border="false":::

A kérések és válaszok fejlécek megtekintésének egy másik módja, ha a Chrome fejlesztői eszközein belül keres. Nyomja le az F12 billentyűt, vagy kattintson a jobb gombbal > **vizsgálja** meg  ->  a **fejlesztői eszközök**, majd válassza a **hálózat** fület. töltsön be egy weblapot, és kattintson a vizsgálni kívánt kérelemre.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Kérelmek cookie-neveinek keresése

Ha a kérelem cookie-kat tartalmaz, a **cookie-k** lapon lehet megtekinteni őket a hegedűsben.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>A globális paraméterek korlátozása a hamis pozitív adatok kiiktatására

- Kérelem törzsének ellenőrzésének letiltása

   A **kérés törzsének** kikapcsolására vonatkozó beállítással a WAF nem értékeli ki az összes forgalomra vonatkozó kérelmeket. Ez akkor lehet hasznos, ha tudja, hogy a kérések törzse nem rosszindulatú az alkalmazásában.

   Ha letiltja ezt a beállítást, a rendszer csak a kérés törzsét vizsgálja meg. A fejlécek és a cookie-k továbbra is megvizsgálva maradnak, kivéve, ha az egyeseket kizárják a kizárási lista funkcióival.

- Fájlméret korlátai

   A WAF fájlméretének korlátozásával korlátozható a webkiszolgálók támadásának lehetősége. A nagyméretű fájlok feltöltésének engedélyezésével megnő a háttérrendszer kockázata. A fájlok méretének a szokásos használati esetre való korlátozása az alkalmazáshoz csak egy másik módszer a támadások megelőzése érdekében.

   > [!NOTE]
   > Ha tudja, hogy az alkalmazásnak nincs szüksége a fájl feltöltésére egy adott méretnél magasabb szintre, korlátozhatja a korlátot.

## <a name="firewall-metrics-waf_v1-only"></a>Tűzfalak Metrikái (csak WAF_v1)

A v1-es webalkalmazási tűzfalak esetében a következő metrikák érhetők el a portálon: 

1. Webalkalmazási tűzfal letiltott kérelme a blokkolt kérelmek számának megszámlálása
2. Webalkalmazási tűzfal letiltott szabálya az összes olyan szabály számlálása, amely megfelelt **, és** a kérés blokkolva lett
3. Webalkalmazási tűzfal teljes szabályának elosztása az értékelés során egyeztetett szabályok szerint
     
A metrikák engedélyezéséhez válassza a **metrikák** fület a portálon, és válasszon egyet a három mérőszám közül.

## <a name="next-steps"></a>Következő lépések

Lásd: [webalkalmazási tűzfal konfigurálása Application Gatewayon](tutorial-restrict-web-traffic-powershell.md).
