---
title: CRS-szabályok csoportjai és szabályai
titleSuffix: Azure Web Application Firewall
description: Ez az oldal információt nyújt a webalkalmazási tűzfal CRS-szabályainak csoportjairól és szabályairól.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e2c88091072921f1ca674868e401c34d354418de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746509"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Webalkalmazási tűzfal CRS-szabályok csoportjai és szabályai

Application Gateway webalkalmazási tűzfal (WAF) megvédi a webalkalmazásokat a gyakori biztonsági rések és kiaknázások ellen. Ez az 3,1, 3,0 vagy 2.2.9 OWASP alapszabály-készletek alapján meghatározott szabályokon keresztül történik. Ezek a szabályok szabály-szabály alapján letilthatók. Ez a cikk a jelenleg elérhető szabályokat és szabálykészlet-készleteket tartalmazza.

## <a name="core-rule-sets"></a>Alapvető szabálykészletek

A Application Gateway WAF alapértelmezés szerint előre konfigurálva van a CRS 3,0-ben. Ehelyett azonban a CRS 3,1 vagy a CRS 2.2.9 használatát is választhatja. A CRS 3,1 új szabályokat biztosít a Java-fertőzések elleni védekezéshez, a fájlfeltöltés-ellenőrzések kezdeti készletéhez, a rögzített hamis pozitív állapotokhoz és egyebekhez. A CRS 3,0 a CRS-2.2.9 képest csökkentett téves riasztásokat kínál. [Az igényeinek megfelelően testre szabhatja a szabályokat](application-gateway-customize-waf-rules-portal.md)is.

> [!div class="mx-imgBorder"]
> ![Szabályok kezelése](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

A WAF a következő webes biztonsági rések ellen véd:

- SQL-befecskendezéses támadások
- Helyek közötti parancsfájlok elleni támadások
- Más gyakori támadások, például a parancsok befecskendezése, a HTTP-kérelmek csempészete, a HTTP-válaszok felosztása és a távoli fájlok bevonása
- HTTP-protokollok megsértése
- HTTP-protokollok rendellenességei, például a hiányzó gazdagép felhasználói ügynök és a fejlécek elfogadása
- Robotprogramok, webbejárók és képolvasók
- Gyakori alkalmazások helytelen konfigurációja (például Apache és IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3,1

A CRS 3,1 13 szabályt tartalmaz, ahogy az alábbi táblázatban is látható. Minden csoport több szabályt tartalmaz, amelyek letilthatók.

> [!NOTE]
> A CRS 3,1 csak a WAF_v2 SKU-ban érhető el.

|Szabálycsoport|Leírás|
|---|---|
|**[Általános](#general-31)**|Általános csoport|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Zárolási módszerek (PUT, javítás)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Védelem a port-és környezeti képolvasók ellen|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Védelem a protokollok és a kódolási problémák ellen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Védelem a fejlécek injektálásával, a becsempészés kérelmezésével és a válaszok felosztásával szemben|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Védelem a fájl-és elérési utak elleni támadásokkal szemben|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Védelem a távoli fájlok bevonása (RFI) elleni támadásokkal szemben|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Távoli kódfuttatást okozó támadások elleni védelem|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Védelem a PHP-befecskendezés elleni támadásokkal szemben|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|A helyek közötti parancsfájlok elleni támadások elleni védelem|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|SQL-befecskendezéses támadások elleni védelem|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Védelem a munkamenet-rögzítési támadásokkal szemben|
|**[KÉRELEM-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|Védelem a JAVA-támadásokkal szemben|

### <a name="owasp-crs-30"></a>OWASP CRS 3,0

A CRS 3,0 12 szabályt tartalmaz, ahogy az alábbi táblázatban is látható. Minden csoport több szabályt tartalmaz, amelyek letilthatók.

|Szabálycsoport|Leírás|
|---|---|
|**[Általános](#general-30)**|Általános csoport|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Zárolási módszerek (PUT, javítás)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Védelem a port-és környezeti képolvasók ellen|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Védelem a protokollok és a kódolási problémák ellen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Védelem a fejlécek injektálásával, a becsempészés kérelmezésével és a válaszok felosztásával szemben|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Védelem a fájl-és elérési utak elleni támadásokkal szemben|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Védelem a távoli fájlok bevonása (RFI) elleni támadásokkal szemben|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Távoli kódfuttatást okozó támadások elleni védelem|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Védelem a PHP-befecskendezés elleni támadásokkal szemben|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|A helyek közötti parancsfájlok elleni támadások elleni védelem|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|SQL-befecskendezéses támadások elleni védelem|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Védelem a munkamenet-rögzítési támadásokkal szemben|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

A CRS 2.2.9 10 szabályt tartalmaz, az alábbi táblázatban látható módon. Minden csoport több szabályt tartalmaz, amelyek letilthatók.

|Szabálycsoport|Leírás|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Védelem a protokollok megsértése ellen (például érvénytelen karakterek vagy kérelem törzsének beolvasása)|
|**[crs_21_protocol_anomalies](#crs21)**|Helytelen fejléc-információk elleni védelem|
|**[crs_23_request_limits](#crs23)**|A korlátozásokat túllépő argumentumok vagy fájlok elleni védelem|
|**[crs_30_http_policy](#crs30)**|A korlátozott metódusok, fejlécek és fájltípusok elleni védelem|
|**[crs_35_bad_robots](#crs35)**|Védelem a webes bejárások és képolvasók ellen|
|**[crs_40_generic_attacks](#crs40)**|Általános támadásokkal szembeni védelem (például a munkamenet-rögzítés, a távoli fájlok bevonása és a PHP-injektálás)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|SQL-befecskendezéses támadások elleni védelem|
|**[crs_41_xss_attacks](#crs41xss)**|A helyek közötti parancsfájlok elleni támadások elleni védelem|
|**[crs_42_tight_security](#crs42)**|Védelem az elérésiút-átjárási támadásokkal szemben|
|**[crs_45_trojans](#crs45)**|Védelem a hátsóajtó Trójaiak ellen|

A következő szabálykészlet és szabályok érhetők el, ha Application Gateway webalkalmazási tűzfalat használ.

# <a name="owasp-31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Szabálykészlet

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Általános kérdések</p>

|RuleId|Leírás|
|---|---|
|200004|Lehetséges többrészes nem egyező határ.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Leírás|
|---|---|
|911100|A metódus nem engedélyezett a szabályzatban|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Leírás|
|---|---|
|913100|A biztonsági ellenőrzőeszközhöz tartozó User-Agent található|
|913101|A parancsfájl/általános HTTP-ügyfélhez társított User-Agent található|
|913102|A web lánctalpas/robothoz tartozó User-Agent található|
|913110|A biztonsági lapolvasóhoz tartozó kérelem fejléce található|
|913120|A biztonsági lapolvasóhoz tartozó kérelem fájlnév/argumentuma található.|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Leírás|
|---|---|
|920100|Érvénytelen HTTP-kérési sor|
|920120|Többrészes/űrlap – adatmegkerülési kísérlet|
|920121|Többrészes/űrlap – adatmegkerülési kísérlet|
|920130|Nem sikerült elemezni a kérelem törzsét.|
|920140|A többrészes kérelem törzse nem sikerült szigorú ellenőrzést végrehajtani|
|920160|A Content-Length HTTP-fejléc nem numerikus.|
|920170|GET vagy HEAD kérelem szövegtörzs tartalmával.|
|920171|GET vagy HEAD kérelem átvitel-kódolással.|
|920180|A POST kérelemből hiányzik a Content-Length fejléc.|
|920190|Range = érvénytelen az utolsó bájt értéke.|
|920200|Tartomány = túl sok mező (6 vagy több)|
|920201|Tartomány = túl sok mező a PDF-kérelemhez (35 vagy több)|
|920202|Tartomány = túl sok mező a PDF-kérelemhez (6 vagy több)|
|920210|Több/ütköző kapcsolatok fejléce nem található.|
|920220|URL-kódolással való visszaélés elleni támadási kísérlet|
|920230|Több URL-kódolás észlelhető|
|920240|URL-kódolással való visszaélés elleni támadási kísérlet|
|920250|UTF8-kódolást használó támadási kísérlet|
|920260|Unicode teljes vagy félszélességű támadási kísérlet|
|920270|Érvénytelen karakter a kérelemben (null karakter)|
|920271|Érvénytelen karakter a kérelemben (nem nyomtatható karakterek)|
|920272|Érvénytelen karakter a kérelemben (az ASCII 127 alatti nyomtatható karaktereken kívül)|
|920273|Érvénytelen karakter a kérelemben (a nagyon szigorú készleten kívül)|
|920274|Érvénytelen karakter a kérések fejlécében (a nagyon szigorú készleten kívül)|
|920280|A kérelemből hiányzik egy állomásfejléc|
|920290|Üres állomásfejléc|
|920300|A kérelemből hiányzik egy Accept fejléc|
|920310|A kérelemben üres az elfogadás fejléce|
|920311|A kérelemben üres az elfogadás fejléce|
|920320|Hiányzó felhasználói ügynök fejléce|
|920330|Üres felhasználói ügynök fejléce|
|920340|Tartalmat tartalmazó kérelem, de hiányzik a Content-Type fejléc|
|920341|A tartalmat tartalmazó kérelemnek Content-Type fejlécre van szüksége|
|920350|A állomásfejléc egy numerikus IP-cím|
|920360|Az argumentum neve túl hosszú|
|920370|Túl hosszú az argumentum értéke|
|920380|Túl sok argumentum van a kérelemben|
|920390|Az argumentumok teljes mérete túllépve|
|920400|Túl nagy a feltöltött fájl mérete|
|920410|A feltöltött fájlok teljes mérete túl nagy|
|920420|A kérelem tartalmának típusa nem engedélyezett a szabályzatban|
|920430|A házirend nem engedélyezi a HTTP protokoll verzióját|
|920440|Az URL-fájl kiterjesztését házirend korlátozza|
|920450|A HTTP-fejlécet a szabályzat korlátozza (% @ {MATCHED_VAR})|
|920460|Rendellenes Escape-karakterek|
|920470|Nem engedélyezett Content-Type fejléc|
|920480|A karakterkészlet paraméterének korlátozása a Content-Type fejlécen belül|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Leírás|
|---|---|
|921110|HTTP-kérelem csempészete elleni támadás|
|921120|HTTP-válasz felosztásának támadása|
|921130|HTTP-válasz felosztásának támadása|
|921140|HTTP-fejléc injekciós támadása fejléceken keresztül|
|921150|HTTP-fejléc injekciós támadása a hasznos adatokkal (CR/LF észlelve)|
|921151|HTTP-fejléc injekciós támadása a hasznos adatokkal (CR/LF észlelve)|
|921160|HTTP-fejléc injekciós támadás adattartalommal (CR/LF és header-Name)|
|921170|HTTP-paraméterek szennyezése|
|921180|HTTP-paraméterek szennyezése (% {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Leírás|
|---|---|
|930100|Elérési út átjárási támadása (/.. /)|
|930110|Elérési út átjárási támadása (/.. /)|
|930120|Operációs rendszer fájljának elérési kísérlete|
|930130|Korlátozott fájl-hozzáférési kísérlet|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Leírás|
|---|---|
|931100|Lehetséges távoli fájlok felvételének (RFI) támadása = URL-paraméter IP-cím használatával|
|931110|Lehetséges távoli fájlok bevonása (RFI) elleni támadás|
|931120|Lehetséges távoli fájlok felvételének (RFI) támadása = URL-adattartalom a megadott kérdőjel (?) karakternél|
|931130|Lehetséges távoli fájlok felvételének (RFI) támadása = Off-Domain hivatkozás/hivatkozás|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Leírás|
|---|---|
|932100|Távoli parancs végrehajtása: UNIX-parancs befecskendezése|
|932105|Távoli parancs végrehajtása: UNIX-parancs befecskendezése|
|932106|Távoli parancs végrehajtása: UNIX-parancs befecskendezése|
|932110|Távoli parancs végrehajtása: Windows-parancs befecskendezése|
|932115|Távoli parancs végrehajtása: Windows-parancs befecskendezése|
|932120|Távoli parancs végrehajtása = Windows PowerShell-parancs található|
|932130|Távoli parancs végrehajtása = UNIX rendszerhéj-kifejezés található|
|932140|Távoli parancs végrehajtása = Windows FOR/IF parancs található|
|932150|Távoli parancs végrehajtása: közvetlen UNIX-parancs végrehajtása|
|932160|Távoli parancs végrehajtása = UNIX rendszerhéj-kód található|
|932170|Távoli parancs végrehajtása = Shellshock (CVE-2014-6271)|
|932171|Távoli parancs végrehajtása = Shellshock (CVE-2014-6271)|
|932180|Korlátozott fájlfeltöltés-kísérlet|
|932190|Távoli parancs végrehajtása: helyettesítő karakter megkerülése módszer|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Leírás|
|---|---|
|933100|PHP-befecskendező támadás = megtalált címke megnyitása/bezárása|
|933110|PHP-befecskendezési támadás = PHP parancsfájl feltöltése megtalálva|
|933111|PHP-befecskendezési támadás: a PHP-parancsfájl feltöltése található|
|933120|PHP Injection Attack = konfigurációs irányelv található|
|933130|PHP-befecskendező támadás = változók találhatók|
|933131|PHP-befecskendezési támadás: talált változók|
|933140|PHP-befecskendezéses támadás: I/O-adatfolyam található|
|933150|PHP Injection támadás = High-Risk PHP-függvény neve megtalálható|
|933151|PHP-befecskendezési támadás: Medium-Risk PHP-függvény neve megtalálható|
|933160|PHP Injection támadás = High-Risk PHP-függvény hívása található|
|933161|PHP-befecskendezési támadás: Low-Value PHP-függvény hívása található|
|933170|PHP-befecskendezési támadás: Szerializált objektum injektálása|
|933180|PHP Injection támadás = változó függvény hívása található|
|933190|PHP-befecskendezéses támadás: a PHP záró címkéje található|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Leírás|
|---|---|
|941100|XSS-támadás észlelhető a libinjection-n keresztül|
|941101|XSS-támadás észlelhető a libinjection-n keresztül|
|941110|XSS-szűrő – 1. kategória = parancsfájl-címke vektora|
|941130|XSS-szűrő – 3. kategória = attribútum vektor|
|941140|XSS-szűrő – 4. kategória = JavaScript URI-vektor|
|941150|XSS-szűrő – 5. kategória = nem engedélyezett HTML-attribútumok|
|941160|NoScript XSS InjectionChecker: HTML-befecskendezés|
|941170|NoScript XSS-InjectionChecker: attribútum-injektálás|
|941180|Node-Validator Blocklist kulcsszavai|
|941190|XSS stíluslapok használatával|
|941200|XSS VML-keretek használatával|
|941210|XSS – eltorzított JavaScript használata|
|941220|XSS, felhasznált VB-szkript|
|941230|XSS a "beágyazás" címkével|
|941240|XSS az "import" vagy a "megvalósítás" attribútum használatával|
|941250|IE XSS-szűrők – észlelt támadás|
|941260|XSS a "meta" címkével|
|941270|XSS a "link" href használatával|
|941280|XSS a "Base" címkével|
|941290|XSS az "applet" címkével|
|941300|XSS az "Object" címkével|
|941310|US-ASCII hibás kódolású XSS-szűrő-támadás észlelhető.|
|941320|Lehetséges XSS-támadás észlelhető – HTML-címke kezelője|
|941330|IE XSS-szűrők – a rendszer támadást észlelt.|
|941340|IE XSS-szűrők – a rendszer támadást észlelt.|
|941350|UTF-7 kódolás, azaz XSS – a rendszer támadást észlelt.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Leírás|
|---|---|
|942100|SQL-injektálási támadás észlelhető a libinjection-n keresztül|
|942110|SQL-befecskendezési támadás: az általános befecskendezéses tesztelés észlelve|
|942120|SQL-befecskendezési támadás: az SQL-operátor észlelve|
|942130|SQL-befecskendezési támadás: az SQL-tautológia észlelve.|
|942140|SQL injection-támadás = közös adatbázis-nevek észlelhetők|
|942150|SQL-injektálási támadás|
|942160|Észleli a vak SQLi-teszteket az alvó () vagy a teljesítményteszt () használatával.|
|942170|Észleli az SQL-teljesítményteszteket és az alvó adatbefecskendezési kísérleteket, beleértve a feltételes|
|942180|Az alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 1/3|
|942190|Az MSSQL-kódok végrehajtásával és az információk gyűjtésével kapcsolatos kísérletek észlelése|
|942200|Észleli a MySQL-megjegyzéseket – a/Space-Obfuscated-befecskendezést és a kezdő-lezárást|
|942210|Észleli a láncolt SQL-injektálási kísérleteket 1/2|
|942220|Az egész számú túlcsordulás elleni támadásokat keresi a skipfish, kivéve a 3.0.00738585072|
|942230|Feltételes SQL-injektálási kísérletek észlelése|
|942240|Észleli a MySQL charset kapcsolót és az MSSQL DoS-kísérleteket|
|942250|Észleli az EGYEZTETÉSt, az EGYESÍTÉSt és az azonnali befecskendezések végrehajtását|
|942251|Észleli az injekciókat|
|942260|Az alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 2/3|
|942270|Alapszintű SQL-injektálást keres. Gyakori támadási karakterlánc a MySQL Oracle és mások számára|
|942280|Észleli a postgres pg_sleep injekciót, a waitfor késleltetési támadásokat és az adatbázis-leállítási kísérleteket|
|942290|Alapszintű MongoDB SQL-injektálási kísérletek keresése|
|942300|Észleli a MySQL-megjegyzéseket, a feltételeket és a ch (a) r-injektálásokat|
|942310|Észleli a láncolt SQL-injektálási kísérleteket 2/2|
|942320|A MySQL és a PostgreSQL tárolt eljárás/függvény befecskendezésének észlelése|
|942330|Észleli a klasszikus SQL injection probs 1/2|
|942340|Az alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 3/3|
|942350|Észleli a MySQL UDF-injektálást és más adat/struktúra manipulációs kísérleteket|
|942360|Az összefűzött alapszintű SQL-injektálási és SQLLFI-kísérletek észlelése|
|942361|A kulcsszó Alter vagy Union alapján észleli az alapszintű SQL-befecskendezést|
|942370|Észleli a klasszikus SQL injection probs 2/2|
|942380|SQL-injektálási támadás|
|942390|SQL-injektálási támadás|
|942400|SQL-injektálási támadás|
|942410|SQL-injektálási támadás|
|942420|Korlátozott SQL-karakteres rendellenességek észlelése (cookie-k): a speciális karakterek száma túllépve (8)|
|942421|Korlátozott SQL-karakteres rendellenességek észlelése (cookie-k): a speciális karakterek száma túllépve (3)|
|942430|Korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (12)|
|942431|Korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (6)|
|942432|Korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (2)|
|942440|A rendszer SQL-megjegyzési sorozatot észlelt.|
|942450|SQL hex-kódolás azonosítva|
|942460|Meta-Character rendellenesség észlelésének riasztása – ismétlődő nem Word karakterek|
|942470|SQL-injektálási támadás|
|942480|SQL-injektálási támadás|
|942490|Észleli a klasszikus SQL injection probs 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Leírás|
|---|---|
|943100|Lehetséges munkamenet-rögzítési támadás = cookie-értékek beállítása HTML-ben|
|943110|Lehetséges munkamenet-rögzítési támadás = munkamenet-azonosító paraméter neve Off-Domain Hivatkozóval|
|943120|Lehetséges munkamenet-rögzítési támadás = munkamenet-azonosító paraméter neve, hivatkozó nélkül|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">KÉRELEM-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|RuleId|Leírás|
|---|---|
|944120|Lehetséges adattartalom-végrehajtás és távoli parancs végrehajtása|
|944130|Gyanús Java-osztályok|
|944200|A Java deszerializálás Apache Commons kiaknázása|

# <a name="owasp-30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Szabálykészlet

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Általános kérdések</p>

|RuleId|Leírás|
|---|---|
|200004|Lehetséges többrészes nem egyező határ.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Leírás|
|---|---|
|911100|A metódus nem engedélyezett a szabályzatban|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Leírás|
|---|---|
|913100|A biztonsági ellenőrzőeszközhöz tartozó User-Agent található|
|913110|A biztonsági lapolvasóhoz tartozó kérelem fejléce található|
|913120|A biztonsági lapolvasóhoz tartozó kérelem fájlnév/argumentuma található.|
|913101|A parancsfájl/általános HTTP-ügyfélhez társított User-Agent található|
|913102|A web lánctalpas/robothoz tartozó User-Agent található|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Leírás|
|---|---|
|920100|Érvénytelen HTTP-kérési sor|
|920130|Nem sikerült elemezni a kérelem törzsét.|
|920140|A többrészes kérelem törzse nem sikerült szigorú ellenőrzést végrehajtani|
|920160|A Content-Length HTTP-fejléc nem numerikus.|
|920170|GET vagy HEAD kérelem szövegtörzs tartalmával.|
|920180|A POST kérelemből hiányzik a Content-Length fejléc.|
|920190|Range = érvénytelen az utolsó bájt értéke.|
|920210|Több/ütköző kapcsolatok fejléce nem található.|
|920220|URL-kódolással való visszaélés elleni támadási kísérlet|
|920240|URL-kódolással való visszaélés elleni támadási kísérlet|
|920250|UTF8-kódolást használó támadási kísérlet|
|920260|Unicode teljes vagy félszélességű támadási kísérlet|
|920270|Érvénytelen karakter a kérelemben (null karakter)|
|920280|A kérelemből hiányzik egy állomásfejléc|
|920290|Üres állomásfejléc|
|920310|A kérelemben üres az elfogadás fejléce|
|920311|A kérelemben üres az elfogadás fejléce|
|920330|Üres felhasználói ügynök fejléce|
|920340|Tartalmat tartalmazó kérelem, de hiányzik a Content-Type fejléc|
|920350|A állomásfejléc egy numerikus IP-cím|
|920380|Túl sok argumentum van a kérelemben|
|920360|Az argumentum neve túl hosszú|
|920370|Túl hosszú az argumentum értéke|
|920390|Az argumentumok teljes mérete túllépve|
|920400|Túl nagy a feltöltött fájl mérete|
|920410|A feltöltött fájlok teljes mérete túl nagy|
|920420|A kérelem tartalmának típusa nem engedélyezett a szabályzatban|
|920430|A házirend nem engedélyezi a HTTP protokoll verzióját|
|920440|Az URL-fájl kiterjesztését házirend korlátozza|
|920450|A HTTP-fejlécet a szabályzat korlátozza (% @ {MATCHED_VAR})|
|920200|Tartomány = túl sok mező (6 vagy több)|
|920201|Tartomány = túl sok mező a PDF-kérelemhez (35 vagy több)|
|920230|Több URL-kódolás észlelhető|
|920300|A kérelemből hiányzik egy Accept fejléc|
|920271|Érvénytelen karakter a kérelemben (nem nyomtatható karakterek)|
|920320|Hiányzó felhasználói ügynök fejléce|
|920272|Érvénytelen karakter a kérelemben (az ASCII 127 alatti nyomtatható karaktereken kívül)|
|920202|Tartomány = túl sok mező a PDF-kérelemhez (6 vagy több)|
|920273|Érvénytelen karakter a kérelemben (a nagyon szigorú készleten kívül)|
|920274|Érvénytelen karakter a kérések fejlécében (a nagyon szigorú készleten kívül)|
|920460|Rendellenes Escape-karakterek|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Leírás|
|---|---|
|921100|HTTP-kérelem csempészett támadása.|
|921110|HTTP-kérelem csempészete elleni támadás|
|921120|HTTP-válasz felosztásának támadása|
|921130|HTTP-válasz felosztásának támadása|
|921140|HTTP-fejléc injekciós támadása fejléceken keresztül|
|921150|HTTP-fejléc injekciós támadása a hasznos adatokkal (CR/LF észlelve)|
|921160|HTTP-fejléc injekciós támadás adattartalommal (CR/LF és header-Name)|
|921151|HTTP-fejléc injekciós támadása a hasznos adatokkal (CR/LF észlelve)|
|921170|HTTP-paraméterek szennyezése|
|921180|HTTP-paraméterek szennyezése (% @ {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Leírás|
|---|---|
|930100|Elérési út átjárási támadása (/.. /)|
|930110|Elérési út átjárási támadása (/.. /)|
|930120|Operációs rendszer fájljának elérési kísérlete|
|930130|Korlátozott fájl-hozzáférési kísérlet|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Leírás|
|---|---|
|931100|Lehetséges távoli fájlok felvételének (RFI) támadása = URL-paraméter IP-cím használatával|
|931110|Lehetséges távoli fájlok bevonása (RFI) elleni támadás|
|931120|Lehetséges távoli fájlok felvételének (RFI) támadása = URL-adattartalom a megadott kérdőjel (?) karakternél|
|931130|Lehetséges távoli fájlok felvételének (RFI) támadása = Off-Domain hivatkozás/hivatkozás|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Leírás|
|---|---|
|932120|Távoli parancs végrehajtása = Windows PowerShell-parancs található|
|932130|Távoli parancs végrehajtása = UNIX rendszerhéj-kifejezés található|
|932140|Távoli parancs végrehajtása = Windows FOR/IF parancs található|
|932160|Távoli parancs végrehajtása = UNIX rendszerhéj-kód található|
|932170|Távoli parancs végrehajtása = Shellshock (CVE-2014-6271)|
|932171|Távoli parancs végrehajtása = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Leírás|
|---|---|
|933100|PHP-befecskendező támadás = megtalált címke megnyitása/bezárása|
|933110|PHP-befecskendezési támadás = PHP parancsfájl feltöltése megtalálva|
|933120|PHP Injection Attack = konfigurációs irányelv található|
|933130|PHP-befecskendező támadás = változók találhatók|
|933150|PHP Injection támadás = High-Risk PHP-függvény neve megtalálható|
|933160|PHP Injection támadás = High-Risk PHP-függvény hívása található|
|933180|PHP Injection támadás = változó függvény hívása található|
|933151|PHP Injection támadás = Medium-Risk PHP-függvény neve megtalálható|
|933131|PHP-befecskendező támadás = változók találhatók|
|933161|PHP Injection támadás = Low-Value PHP-függvény hívása található|
|933111|PHP-befecskendezési támadás = PHP parancsfájl feltöltése megtalálva|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Leírás|
|---|---|
|941100|XSS-támadás észlelhető a libinjection-n keresztül|
|941110|XSS-szűrő – 1. kategória = parancsfájl-címke vektora|
|941130|XSS-szűrő – 3. kategória = attribútum vektor|
|941140|XSS-szűrő – 4. kategória = JavaScript URI-vektor|
|941150|XSS-szűrő – 5. kategória = nem engedélyezett HTML-attribútumok|
|941180|Node-Validator Blocklist kulcsszavai|
|941190|XSS stíluslapok használatával|
|941200|XSS VML-keretek használatával|
|941210|XSS – eltorzított JavaScript használata|
|941220|XSS, felhasznált VB-szkript|
|941230|XSS a "beágyazás" címkével|
|941240|XSS az "import" vagy a "megvalósítás" attribútum használatával|
|941260|XSS a "meta" címkével|
|941270|XSS a "link" href használatával|
|941280|XSS a "Base" címkével|
|941290|XSS az "applet" címkével|
|941300|XSS az "Object" címkével|
|941310|US-ASCII hibás kódolású XSS-szűrő-támadás észlelhető.|
|941330|IE XSS-szűrők – a rendszer támadást észlelt.|
|941340|IE XSS-szűrők – a rendszer támadást észlelt.|
|941350|UTF-7 kódolás, azaz XSS – a rendszer támadást észlelt.|
|941320|Lehetséges XSS-támadás észlelhető – HTML-címke kezelője|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Leírás|
|---|---|
|942100|SQL-injektálási támadás észlelhető a libinjection-n keresztül|
|942110|SQL-befecskendezési támadás: az általános befecskendezéses tesztelés észlelve|
|942130|SQL-befecskendezési támadás: az SQL-tautológia észlelve.|
|942140|SQL injection-támadás = közös adatbázis-nevek észlelhetők|
|942160|Észleli a vak SQLi-teszteket az alvó () vagy a teljesítményteszt () használatával.|
|942170|Észleli az SQL-teljesítményteszteket és az alvó adatbefecskendezési kísérleteket, beleértve a feltételes|
|942190|Az MSSQL-kódok végrehajtásával és az információk gyűjtésével kapcsolatos kísérletek észlelése|
|942200|Észleli a MySQL-megjegyzéseket – a/Space-Obfuscated-befecskendezést és a kezdő-lezárást|
|942230|Feltételes SQL-injektálási kísérletek észlelése|
|942260|Az alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 2/3|
|942270|Alapszintű SQL-injektálást keres. Gyakori támadási karakterlánc a MySQL Oracle és mások számára.|
|942290|Alapszintű MongoDB SQL-injektálási kísérletek keresése|
|942300|Észleli a MySQL-megjegyzéseket, a feltételeket és a ch (a) r-injektálásokat|
|942310|Észleli a láncolt SQL-injektálási kísérleteket 2/2|
|942320|A MySQL és a PostgreSQL tárolt eljárás/függvény befecskendezésének észlelése|
|942330|Észleli a klasszikus SQL injection probs 1/2|
|942340|Az alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 3/3|
|942350|Észleli a MySQL UDF-injektálást és más adat/struktúra manipulációs kísérleteket|
|942360|Az összefűzött alapszintű SQL-injektálási és SQLLFI-kísérletek észlelése|
|942370|Észleli a klasszikus SQL injection probs 2/2|
|942150|SQL-injektálási támadás|
|942410|SQL-injektálási támadás|
|942430|Korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (12)|
|942440|A rendszer SQL-megjegyzési sorozatot észlelt.|
|942450|SQL hex-kódolás azonosítva|
|942251|Észleli az injekciókat|
|942460|Meta-Character rendellenesség észlelésének riasztása – ismétlődő nem Word karakterek|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Leírás|
|---|---|
|943100|Lehetséges munkamenet-rögzítési támadás = cookie-értékek beállítása HTML-ben|
|943110|Lehetséges munkamenet-rögzítési támadás = munkamenet-azonosító paraméter neve Off-Domain Hivatkozóval|
|943120|Lehetséges munkamenet-rögzítési támadás = munkamenet-azonosító paraméter neve, hivatkozó nélkül|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Szabálykészlet

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Leírás|
|---|---|
|960911|Érvénytelen HTTP-kérési sor|
|981227|Apache Error = érvénytelen URI a kérelemben.|
|960912|Nem sikerült elemezni a kérelem törzsét.|
|960914|A többrészes kérelem törzse nem sikerült szigorú ellenőrzést végrehajtani|
|960915|A többrészes elemző egy lehetséges nem egyező határt észlelt.|
|960016|A Content-Length HTTP-fejléc nem numerikus.|
|960011|GET vagy HEAD kérelem szövegtörzs tartalmával.|
|960012|A POST kérelemből hiányzik a Content-Length fejléc.|
|960902|Az identitás kódolásának használata érvénytelen.|
|960022|A várakozási fejléc nem engedélyezett a HTTP 1,0 esetében.|
|960020|A sorpragmákat fejlécéhez Cache-Control fejléc szükséges a HTTP/1.1-kérelmekhez.|
|958291|A Range = mező létezik, és a 0 karakterrel kezdődik.|
|958230|Range = érvénytelen az utolsó bájt értéke.|
|958295|Több/ütköző kapcsolatok fejléce nem található.|
|950107|URL-kódolással való visszaélés elleni támadási kísérlet|
|950109|Több URL-kódolás észlelhető|
|950108|URL-kódolással való visszaélés elleni támadási kísérlet|
|950801|UTF8-kódolást használó támadási kísérlet|
|950116|Unicode teljes vagy félszélességű támadási kísérlet|
|960901|Érvénytelen karakter a kérelemben|
|960018|Érvénytelen karakter a kérelemben|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Leírás|
|---|---|
|960008|A kérelemből hiányzik egy állomásfejléc|
|960007|Üres állomásfejléc|
|960015|A kérelemből hiányzik egy Accept fejléc|
|960021|A kérelemben üres az elfogadás fejléce|
|960009|A kérelemből hiányzik egy felhasználói ügynök fejléce|
|960006|Üres felhasználói ügynök fejléce|
|960904|Tartalmat tartalmazó kérelem, de hiányzik a Content-Type fejléc|
|960017|A állomásfejléc egy numerikus IP-cím|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|Leírás|
|---|---|
|960209|Az argumentum neve túl hosszú|
|960208|Túl hosszú az argumentum értéke|
|960335|Túl sok argumentum van a kérelemben|
|960341|Az argumentumok teljes mérete túllépve|
|960342|Túl nagy a feltöltött fájl mérete|
|960343|A feltöltött fájlok teljes mérete túl nagy|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|Leírás|
|---|---|
|960032|A metódus nem engedélyezett a szabályzatban|
|960010|A kérelem tartalmának típusa nem engedélyezett a szabályzatban|
|960034|A házirend nem engedélyezi a HTTP protokoll verzióját|
|960035|Az URL-fájl kiterjesztését házirend korlátozza|
|960038|A HTTP-fejlécet házirend korlátozza|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|Leírás|
|---|---|
|990002|A kérelem azt jelzi, hogy a biztonsági ellenőrzőeszköz beolvasta a helyet|
|990901|A kérelem azt jelzi, hogy a biztonsági ellenőrzőeszköz beolvasta a helyet|
|990902|A kérelem azt jelzi, hogy a biztonsági ellenőrzőeszköz beolvasta a helyet|
|990012|Gazember webhely lánctalpas|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|Leírás|
|---|---|
|960024|Meta-Character rendellenesség észlelésének riasztása – ismétlődő nem Word karakterek|
|950008|Nem dokumentált ColdFusion-címkék injektálása|
|950010|LDAP-injektálási támadás|
|950011|SSI-befecskendező támadás|
|950018|A rendszer univerzális PDF XSS URL-címet észlelt.|
|950019|E-mail-befecskendezési támadás|
|950012|HTTP-kérelem csempészett támadása.|
|950910|HTTP-válasz felosztásának támadása|
|950911|HTTP-válasz felosztásának támadása|
|950117|Távoli fájl-integrációs támadás|
|950118|Távoli fájl-integrációs támadás|
|950119|Távoli fájl-integrációs támadás|
|950120|Lehetséges távoli fájlok felvételének (RFI) támadása = Off-Domain hivatkozás/hivatkozás|
|981133|981133. szabály|
|981134|981134. szabály|
|950009|Munkamenet-rögzítési támadás|
|950003|Munkamenet-rögzítés|
|950000|Munkamenet-rögzítés|
|950005|Távoli fájl-hozzáférési kísérlet|
|950002|Rendszerparancs-hozzáférés|
|950006|Rendszerparancs-befecskendezés|
|959151|PHP-befecskendezéses támadás|
|958976|PHP-befecskendezéses támadás|
|958977|PHP-befecskendezéses támadás|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Leírás|
|---|---|
|981231|A rendszer SQL-megjegyzési sorozatot észlelt.|
|981260|SQL hex-kódolás azonosítva|
|981320|SQL injection-támadás = közös adatbázis-nevek észlelhetők|
|981300|981300. szabály|
|981301|981301. szabály|
|981302|981302. szabály|
|981303|981303. szabály|
|981304|981304. szabály|
|981305|981305. szabály|
|981306|981306. szabály|
|981307|981307. szabály|
|981308|981308. szabály|
|981309|981309. szabály|
|981310|981310. szabály|
|981311|981311. szabály|
|981312|981312. szabály|
|981313|981313. szabály|
|981314|981314. szabály|
|981315|981315. szabály|
|981316|981316. szabály|
|981317|SQL SELECT utasítás anomália észlelési riasztás|
|950007|Vak SQL-injektálási támadás|
|950001|SQL-injektálási támadás|
|950908|SQL-injektálási támadás.|
|959073|SQL-injektálási támadás|
|981272|Észleli a vak SQLi-teszteket az alvó () vagy a teljesítményteszt () használatával.|
|981250|Észleli az SQL-teljesítményteszteket és az alvó adatbefecskendezési kísérleteket, beleértve a feltételes|
|981241|Feltételes SQL-injektálási kísérletek észlelése|
|981276|Alapszintű SQL-injektálást keres. Gyakori támadási karakterlánc a MySQL Oracle és mások számára.|
|981270|Alapszintű MongoDB SQL-injektálási kísérletek keresése|
|981253|A MySQL és a PostgreSQL tárolt eljárás/függvény befecskendezésének észlelése|
|981251|Észleli a MySQL UDF-injektálást és más adat/struktúra manipulációs kísérleteket|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Leírás|
|---|---|
|973336|XSS-szűrő – 1. kategória = parancsfájl-címke vektora|
|973338|XSS-szűrő – 3. kategória = JavaScript URI-vektor|
|981136|981136. szabály|
|981018|981018. szabály|
|958016|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958414|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958032|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958026|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958027|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958054|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958418|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958034|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958019|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958013|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958408|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958012|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958423|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958002|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958017|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958007|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958047|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958410|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958415|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958022|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958405|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958419|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958028|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958057|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958031|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958006|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958033|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958038|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958409|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958001|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958005|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958404|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958023|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958010|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958411|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958422|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958036|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958000|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958018|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958406|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958040|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958052|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958037|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958049|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958030|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958041|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958416|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958024|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958059|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958417|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958020|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958045|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958004|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958421|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958009|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958025|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958413|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958051|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958420|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958407|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958056|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958011|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958412|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958008|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958046|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958039|Helyek közötti parancsfájlok (XSS) elleni támadás|
|958003|Helyek közötti parancsfájlok (XSS) elleni támadás|
|973300|Lehetséges XSS-támadás észlelhető – HTML-címke kezelője|
|973301|XSS-támadás észlelhető|
|973302|XSS-támadás észlelhető|
|973303|XSS-támadás észlelhető|
|973304|XSS-támadás észlelhető|
|973305|XSS-támadás észlelhető|
|973306|XSS-támadás észlelhető|
|973307|XSS-támadás észlelhető|
|973308|XSS-támadás észlelhető|
|973309|XSS-támadás észlelhető|
|973311|XSS-támadás észlelhető|
|973313|XSS-támadás észlelhető|
|973314|XSS-támadás észlelhető|
|973331|IE XSS-szűrők – a rendszer támadást észlelt.|
|973315|IE XSS-szűrők – a rendszer támadást észlelt.|
|973330|IE XSS-szűrők – a rendszer támadást észlelt.|
|973327|IE XSS-szűrők – a rendszer támadást észlelt.|
|973326|IE XSS-szűrők – a rendszer támadást észlelt.|
|973346|IE XSS-szűrők – a rendszer támadást észlelt.|
|973345|IE XSS-szűrők – a rendszer támadást észlelt.|
|973324|IE XSS-szűrők – a rendszer támadást észlelt.|
|973323|IE XSS-szűrők – a rendszer támadást észlelt.|
|973348|IE XSS-szűrők – a rendszer támadást észlelt.|
|973321|IE XSS-szűrők – a rendszer támadást észlelt.|
|973320|IE XSS-szűrők – a rendszer támadást észlelt.|
|973318|IE XSS-szűrők – a rendszer támadást észlelt.|
|973317|IE XSS-szűrők – a rendszer támadást észlelt.|
|973329|IE XSS-szűrők – a rendszer támadást észlelt.|
|973328|IE XSS-szűrők – a rendszer támadást észlelt.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|Leírás|
|---|---|
|950103|Elérési út átjárási támadása|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Leírás|
|---|---|
|950110|Backdoor-hozzáférés|
|950921|Backdoor-hozzáférés|
|950922|Backdoor-hozzáférés|

---

## <a name="next-steps"></a>Következő lépések

- [Webalkalmazási tűzfalszabályok testreszabása a Azure Portal használatával](application-gateway-customize-waf-rules-portal.md)
