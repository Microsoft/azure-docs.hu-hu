---
title: Az Azure Sentinel-adatnormalizálás sémájának referenciája | Microsoft Docs
description: Ez a cikk az Azure Sentinel adatnormalizálás sémáját mutatja be.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806935"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Az Azure Sentinel-adatnormalizálás sémájának referenciája

## <a name="terminology"></a>Terminológia

Az Azure Sentinel-sémák a következő terminológiát használják:

| Időszak | Definíció |
| ---- | ---------- |
| Jelentéskészítő eszköz | A rekordokat az Azure Sentinel szolgáltatásba küldi. Előfordulhat, hogy nem a rekord tulajdonosi rendszere. |
| Rekord | A jelentési eszközről elküldett adatok egysége. Ezt gyakran nevezik "naplónak", "eseménynek" vagy "riasztásnak", de nem feltétlenül kell ezek egyike. |
|

## <a name="data-types-and-formats"></a>Adattípusok és-formátumok

Az értékeket az alábbi irányelvek alapján kell normalizálni. Ez a normalizált mezők esetében kötelező, és más mezők esetében ajánlott. 

| Adattípus | Fizikai típus | Formátum és érték |
| --------- | ------------- | ---------------- |
| **Dátum és idő** | A betöltési módszer képességtől függően csökkenő prioritású:<ul><li>Log Analytics beépített datetime típusú</li><li>Egész szám típusú mező, amely Log Analytics datetime numerikus ábrázolást használ</li><li>Log Analytics datetime numerikus ábrázolást használó karakterlánc-mező</li></ul> | Log Analytics datetime-ábrázolás. <br></br>Log Analytics dátum & az időábrázolás hasonló a természetben, de eltér a UNIX-időbeli ábrázolástól. Tekintse át ezeket a konverziós irányelveket. <br></br>A dátum & az időzónát módosítani kell. |
| **MAC-címe** | Sztring | Colon-Hexadecimal jelölés |
| **IP-cím** | IP-cím | A sémának nincs külön IPv4-és IPv6-címe. Bármely IP-cím mező tartalmazhat IPv4-címet vagy IPv6-címet:<ul><li>IPv4 pont-decimális jelöléssel</li><li>Az IPv6 8 hextets jelöléssel rendelkezik, amely lehetővé teszi az itt ismertetett rövid formák használatát.</li></ul> |
| **Felhasználó** | Sztring | A következő 3 felhasználói mező érhető el:<ul><li>Felhasználónév</li><li>Felhasználói UPN</li><li>Felhasználói tartomány</li></ul> |
| **Felhasználói azonosító** | Sztring | A következő két felhasználói azonosító jelenleg támogatott:<ul><li>Felhasználói SID</li><li>Azure Active Directory-azonosító</li></ul> |
| **Eszköz** | Sztring | A következő 3 eszköz/állomás oszlop támogatott:<ul><li>ID (Azonosító)</li><li>Name</li><li>Teljes tartománynév (FQDN)</li></ul> |
| **Ország** | Sztring | ISO 3166-1-et használó karakterlánc a következő prioritás szerint:<ul><li>Alpha-2 kódok (például a Egyesült Államok)</li><li>Alpha-3 kódok (azaz az Egyesült Államok a Egyesült Államok)</li><li>Rövid név</li></ul> |
| **Régió** | Sztring | Az ország alosztásának neve az ISO 3166-2 használatával |
| **City** | Sztring | |
| **Hosszúság** | Dupla | ISO 6709 koordináta-ábrázolás (aláírt decimális) |
| **Szélesség** | Dupla | ISO 6709 koordináta-ábrázolás (aláírt decimális) |
| **Kivonatoló algoritmus** | Sztring | A következő 4 kivonatoló oszlop támogatott:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Fájl típusa** | Sztring | A fájltípus típusa:<ul><li>Mellék</li><li>Osztály</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Hálózati munkamenetek tábla sémája

Alább látható a hálózati munkamenetek táblázatának sémája, a verzióval ellátott 1.0.0

| Mező neve | Érték típusa | Példa | Leírás | Társított OSSEM-entitások |
|-|-|-|-|-|
| EventType | Sztring | Adatforgalom | Az összegyűjtött esemény típusa | Esemény |
| EventSubType | Sztring | Hitelesítés | A típus további leírása, ha alkalmazható | Esemény |
| EventCount | Egész szám  | 10 | Az összesített események száma, ha van ilyen. | Esemény |
| EventEndTime | Dátum/idő | Lásd az "adattípusok" kifejezést. | Az esemény befejezésének időpontja | Esemény |
| EventMessage | sztring |  hozzáférés megtagadva | Általános üzenet vagy leírás, amely a rekordban szerepel, vagy amelyet a rekordból generáltak | Esemény |
| DvcIpAddr | IP-cím |  23.21.23.34 | A rekordot létrehozó eszköz IP-címe | Eszköz<br>IP |
| DvcMacAddr | Sztring | 06:10:9F: EB: 8f: 14 | Azon jelentési eszköz hálózati adapterének MAC-címe, amelyről az eseményt elküldték. | Eszköz<br>Mac |
| DvcHostname | Eszköz neve (karakterlánc) | syslogserver1.contoso.com | Az üzenetet generáló eszköz neve. | Eszköz |
| EventProduct | Sztring | OfficeSharepoint | Az eseményt előállító termék. | Esemény |
| EventProductVersion | sztring | 9.0 |  Az eseményt előállító termék verziója. | Esemény |
| EventResourceId | Eszköz azonosítója (karakterlánc) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Az üzenetet létrehozó eszköz erőforrás-azonosítója. | Esemény |
| EventReportUrl | Sztring | https://192.168.1.1/repoerts/ae3-56.htm | A jelentéskészítő eszköz által létrehozott teljes jelentésre mutató hivatkozás | Esemény |
| EventVendor | Sztring | Microsoft | Az eseményt előállító termék gyártója. | Esemény |
| EventResult | Többértékű: sikeres, részleges, sikertelen, [üres] (karakterlánc) | Siker | A tevékenységhez jelentett eredmény. Üres érték, ha nem alkalmazható. | Esemény |
| EventResultDetails | Sztring | Helytelen jelszó | A EventResult által jelentett eredmény oka vagy részletei | Esemény |
| EventSchemaVersion | Valós | 0.1 | Azure Sentinel-séma verziója. Jelenleg 0,1. | Esemény |
| EventSeverity | Sztring | Alacsony | Ha a jelentett tevékenység biztonsági hatással van, a hatás súlyosságát jelzi. | Esemény |
| EventOriginalUid | Sztring | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | A jelentési eszköz rekordjának azonosítója. | Esemény |
| EventStartTime | Dátum/idő | Lásd az "adattípusok" kifejezést. | A megadott esemény időpontja | Esemény |
| TimeGenerated | Dátum/idő | Lásd az "adattípusok" kifejezést. | Az esemény időpontja, a jelentéskészítési forrás jelentése szerint. | Egyéni mező |
| EventTimeIngested | Dátum/idő | Lásd az "adattípusok" kifejezést. | Az esemény az Azure Sentinelbe való betöltésének időpontja. Hozzá lesz adva az Azure Sentinel. | Esemény |
| EventUid | GUID (karakterlánc) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Az Azure Sentinel által egy sor megjelölésére használt egyedi azonosító. | Esemény |
| NetworkApplicationProtocol | Sztring | HTTPS | A kapcsolat vagy a munkamenet által használt Application Layer protokoll. | Network (Hálózat) |
| DstBytes | int | 32455 | A célhelyről a kapcsolat vagy a munkamenet forrására eljuttatott bájtok száma. | Cél |
| SrcBytes | int | 46536 | A forrásról a kapcsolat vagy a munkamenet céljára eljuttatott bájtok száma. | Forrás |
| NetworkBytes | int | 78991 | A mindkét irányban elküldésre kerülő bájtok száma. Ha a BytesReceived és a BytesSent is létezik, akkor a BytesTotal-nek egyenlőnek kell lennie az összeggel. | Network (Hálózat) |
| NetworkDirection | Többszörös érték: bejövő, kimenő (karakterlánc) | Bejövő | A kapcsolat vagy a munkamenet iránya a szervezeten belül vagy kívül. | Network (Hálózat) |
| DstGeoCity | Sztring | Burlington | A cél IP-címhez társított város | Cél<br>Térség |
| DstGeoCountry | Ország (karakterlánc) | USA | A forrás IP-címhez társított ország | Cél<br>Térség |
| DstDvcHostname | Eszköz neve (karakterlánc) |  victim_pc | A céleszköz eszközének neve | Cél<br>Eszköz |
| DstDvcFqdn | Sztring | victim_pc. contoso. local | Annak a gazdagépnek a teljes tartományneve, amelyben a napló létrejött | Cél<br>Eszköz |
| DstDomainHostname | sztring | CONTOSO | A cél tartománya, a cél gazdagép tartománya (webhely, tartomány neve stb.), például DNS-keresésekhez vagy NS-keresésekhez | Cél |
| DstInterfaceName | sztring | Microsoft Hyper-V hálózati adapter | A cél eszköz által a kapcsolathoz vagy a munkamenethez használt hálózati adapter. | Cél |
| DstInterfaceGuid | sztring | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | A hitelesítési kérelemhez használt hálózati adapter GUID azonosítója  | Cél |
| DstIpAddr | IP-cím | 2001: DB8:: FF00:42:8329 | A kapcsolat vagy a munkamenet célhelyének IP-címe, amelyet a hálózati csomagban célként megadott IP-címnek nevezünk. | Cél<br>IP |
| DstDvcIpAddr | IP-cím | 75.22.12.2 | Egy olyan eszköz cél IP-címe, amely nem kapcsolódik közvetlenül a hálózati csomaghoz | Cél<br>Eszköz<br>IP
| DstGeoLatitude | Szélesség (Double) | 44,475833 | A cél IP-címhez társított földrajzi koordináta szélességét | Cél<br>Térség |
| DstMacAddr | Sztring | 06:10:9F: EB: 8f: 14 | Annak a hálózati adapternek a MAC-címe, amelyen a kapcsolat vagy a munkamenet meg lett szakítva, amelyet a hálózati csomagban lévő cél MAC-nek neveznek. | Cél<br>MAC |
| DstDvcMacAddr | Sztring | 06:10:9F: EB: 8f: 14 | Egy olyan eszköz cél MAC-címe, amely közvetlenül a hálózati csomaghoz van társítva. | Cél<br>Eszköz<br>MAC |
| DstDvcDomain | Sztring | CONTOSO | A célként megadott eszköz tartománya. | Cél<br>Eszköz |
| DstPortNumber | Egész szám | 443 | A cél IP-portja. | Cél<br>Port |
| DstGeoRegion | Régió (karakterlánc) | Vermont | A cél IP-címhez társított országon belüli régió | Cél<br>Térség |
| DstResourceId | Eszköz azonosítója (karakterlánc) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | A célként megadott eszköz erőforrás-azonosítója. | Cél |
| DstNatIpAddr | IP-cím | 2::1 | Ha egy közvetítő NAT-eszköz, például egy tűzfal, a NAT-eszköz által a forrással való kommunikációhoz használt IP-cím. | Cél NAT,<br>IP |
| DstNatPortNumber | int | 443 | Ha egy közvetítő NAT-eszköz, például egy tűzfal a jelentést, a NAT-eszköz által a forrással való kommunikációhoz használt port. | Cél NAT,<br>Port |
| DstUserSid | Felhasználói SID |  S-12-1445 | A munkamenet céljához társított identitás felhasználói azonosítója. Általában a kiszolgálók hitelesítéséhez használt identitás. Részletekért lásd az "adattípusok" című témakört. | Cél<br>User |
| DstUserAadId | Karakterlánc (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | A felhasználó Azure AD-fiók objektumának azonosítója a munkamenet céljának végén | Cél<br>User |
| DstUserName | Username (karakterlánc) | johnd | A munkamenet céljához társított identitás felhasználóneve.  | Cél<br>User |
| DstUserUpn | sztring | johnd@anon.com | A munkamenet céljához hozzárendelt identitás UPN-azonosítója. | Cél<br>User |
| DstUserDomain | sztring | MUNKACSOPORT | A fiók tartomány-vagy számítógépnév a munkamenet célhelyén | Cél<br>User |
| DstZone | Sztring | DMZ | A cél hálózati zónája, a jelentéskészítő eszköz által definiált módon. | Cél |
| DstGeoLongitude | Hosszúság (Double) | – 73,211944 | A cél IP-címhez társított földrajzi koordináta hosszúsága | Cél<br>Térség |
| DvcAction | Többszörös érték: Engedélyezés, megtagadás, drop (karakterlánc) | Engedélyezés | Ha egy közvetítő eszköz, például egy tűzfal a jelentést, az eszköz által végrehajtott művelet. | Eszköz |
| DvcInboundInterface | Sztring | ETH0 | Ha egy közvetítő eszköz, például egy tűzfal, az általa használt hálózati adaptert a forrásoldali eszközhöz való kapcsolódásra használja. | Eszköz |
| DvcOutboundInterface | Sztring  | Ethernet-adapter Ethernet 4 | Ha egy közvetítő eszköz, például egy tűzfal, az általa használt hálózati adaptert a célként megadott eszközhöz való kapcsolódásra használja. | Eszköz |
| NetworkDuration | Egész szám | 1500 | A hálózati munkamenet vagy kapcsolat befejezéséhez szükséges idő ezredmásodpercben | Network (Hálózat) |
| NetworkIcmpCode | Egész szám | 34 | ICMP-üzenet esetén az ICMP-üzenet típusa numerikus érték (RFC 2780 vagy RFC 4443). | Network (Hálózat) |
| NetworkIcmpType | Sztring | A cél nem érhető el | ICMP-üzenet esetén az ICMP-üzenet típusa szöveges ábrázolás (RFC 2780 vagy RFC 4443). | Network (Hálózat) |
| DstPackets | int  | 446 | A célhelyről a kapcsolat vagy a munkamenet forrására küldött csomagok száma. A csomagok jelentését a jelentéskészítő eszköz határozza meg. | Cél |
| SrcPackets | int  | 6478 | A forrásról a kapcsolat vagy a munkamenet célhelyére küldött csomagok száma. A csomagok jelentését a jelentéskészítő eszköz határozza meg. | Forrás |
| NetworkPackets | int  | 0 | A mindkét irányban küldött csomagok száma. Ha a PacketsReceived és a PacketsSent is létezik, akkor a BytesTotal-nek egyenlőnek kell lennie az összeggel. | Network (Hálózat) |
| HttpRequestTime | Egész szám | 700 | A kérésnek a kiszolgálóra való küldéséhez szükséges idő, ha van ilyen. | Http |
| HttpResponseTime | Egész szám | 800 | Az az időtartam, ameddig egy válasznak a kiszolgálón való fogadására volt szükség, ha van ilyen. | Http |
| NetworkRuleName | Sztring | AnyAnyDrop | Annak a szabálynak a neve vagy azonosítója, amely alapján a DeviceAction eldöntötték | Network (Hálózat) |
| NetworkRuleNumber | int |  23 | Egyeztetett szabály száma  | Network (Hálózat) |
| NetworkSessionId | sztring | 172_12_53_32_4322__123_64_207_1_80 | A jelentési eszköz által jelentett munkamenet-azonosító. Például a L7 a megadott alkalmazások munkamenet-azonosítóját a hitelesítés után | Network (Hálózat) |
| SrcGeoCity | Sztring | Burlington | A forrás IP-címhez társított város | Forrás<br>Térség |
| SrcGeoCountry | Ország (karakterlánc) | USA | A forrás IP-címhez társított ország | Forrás<br>Térség |
| SrcDvcHostname | Eszköz neve (karakterlánc) |  gazember | A forrás eszköz neve | Forrás<br>Eszköz |
| SrcDvcFqdn | sztring | Villain.malicious.com | Annak a gazdagépnek a teljes tartományneve, amelyben a napló létrejött | Forrás<br>Eszköz |
| SrcDvcDomain | sztring | EVILORG | Azon eszköz tartománya, amelyről a munkamenetet kezdeményezték | Forrás<br>Eszköz |
| SrcDvcOs | Sztring | iOS | A forrásoldali eszköz operációs rendszere | Forrás<br>Eszköz |
| SrcDvcModelName | Sztring | Samsung Galaxy – Megjegyzés | A forrásoldali eszköz modelljének neve | Forrás<br>Eszköz |
| SrcDvcModelNumber | Sztring | 10,0 | A forrásoldali eszköz modell-száma | Forrás<br>Eszköz |
| SrcDvcType | Sztring | Mobil | A forrásoldali eszköz típusa | Forrás<br> Eszköz |
| SrcIntefaceName | Sztring | eth01 | A forrásoldali eszköz által a kapcsolathoz vagy a munkamenethez használt hálózati adapter. | Forrás |
| SrcInterfaceGuid | Sztring | 46ad544b-eaf0-47ef-827c-266030f545a6 | A használt hálózati adapter GUID azonosítója | Forrás |
| SrcIpAddr | IP-cím | 77.138.103.108 | Az IP-cím, amelyből a kapcsolat vagy a munkamenet származik. | Forrás<br>IP |
| SrcDvcIpAddr | IP-cím | 77.138.103.108 | Egy olyan eszköz forrás IP-címe, amely közvetlenül nincs társítva a hálózati csomaghoz (szolgáltató által gyűjtött vagy explicit módon kiszámított). | Forrás<br>Eszköz<br>IP |
| SrcGeoLatitude | Szélesség (Double) | 44,475833 | A forrás IP-címhez társított földrajzi koordináta szélességét | Forrás<br>Térség |
| SrcGeoLongitude | Hosszúság (Double) | – 73,211944 | A forrás IP-címhez társított földrajzi koordináta hosszúsága | Forrás<br>Térség |
| SrcMacAddr | Sztring | 06:10:9F: EB: 8f: 14 | Annak a hálózati adapternek a MAC-címe, ahonnan a kapcsolati od-munkamenet származik. | Forrás<br>Mac |
| SrcDvcMacAddr | Sztring | 06:10:9F: EB: 8f: 14 | Egy olyan eszköz forrás MAC-címe, amely nem kapcsolódik közvetlenül a hálózati csomaghoz. | Forrás<br>Eszköz<br>Mac |
| SrcPortNumber | Egész szám | 2335 | Az az IP-port, amelyből a csatlakozás származik. Előfordulhat, hogy a munkamenet több kapcsolatot tartalmaz. | Forrás<br>Port |
| SrcGeoRegion | Régió (karakterlánc) | Vermont | A forrás IP-címhez társított országon belüli régió | Forrás<br>Térség |
| SrcResourceId | Sztring | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Az üzenetet létrehozó eszköz erőforrás-azonosítója. | Forrás |
| SrcNatIpAddr | IP-cím | 4.3.2.1 | Ha egy közvetítő NAT-eszköz, például egy tűzfal a jelentést, a NAT-eszköz által a célhoz való kommunikációhoz használt IP-cím. | Forrás NAT,<br>IP |
| SrcNatPortNumber | Egész szám | 345 | Ha egy közvetítő NAT-eszköz, például egy tűzfal a jelentést, a NAT-eszköz által a célhoz való kommunikációhoz használt port. | Forrás NAT,<br>Port |
| SrcUserSid | Felhasználói azonosító (karakterlánc) | S-15-1445 | A munkamenetek forrásához társított identitás felhasználói azonosítója. Általában az ügyfélen műveletet végző felhasználó. Részletekért lásd az "adattípusok" című témakört. | Forrás<br>User |
| SrcUserAadId | Karakterlánc (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | A felhasználó Azure AD-fiók objektumának azonosítója a munkamenet forrásának végén | Forrás<br>User |
| SrcUserName | Username (karakterlánc) | Bob | A munkamenetek forrásához társított identitás felhasználóneve. Általában az ügyfélen műveletet végző felhasználó. Részletekért lásd az "adattípusok" című témakört. | Forrás<br>User |
| SrcUserUpn | sztring | bob@alice.com | A munkamenetet kezdeményező fiók UPN-címe | Forrás<br>User |
| SrcUserDomain | sztring | ASZTALI | A munkamenetet kezdeményező fiók tartománya | Forrás<br>User |
| SrcZone | Sztring | Koppintson a  | A forrás hálózati zónája a jelentéskészítő eszköz által definiált módon. | Forrás |
| NetworkProtocol | Sztring | TCP | A kapcsolat vagy a munkamenet által használt IP-protokoll. Jellemzően TCP, UDP vagy ICMP | Network (Hálózat) |
| CloudAppName | Sztring | Facebook | Egy proxy által azonosított HTTP-alkalmazáshoz tartozó célalkalmazás neve. | Felhőbeli |
| CloudAppId | Sztring | 124 | Egy proxy által azonosított HTTP-alkalmazáshoz tartozó célalkalmazás azonosítója. Ez az érték általában a használt proxyra jellemző. | Felhőbeli |
| CloudAppOperation | Sztring | DeleteFile | Az a művelet, amelyet a felhasználó egy proxy által azonosított HTTP-alkalmazáshoz tartozó célalkalmazás kontextusában hajtott végre. Ez az érték általában a használt proxyra jellemző. | Felhőbeli |
| CloudAppRiskLevel | Sztring | 3 | Egy proxy által azonosított HTTP-alkalmazás kockázati szintje. Ez az érték általában a használt proxyra jellemző. | Felhőbeli |
| FileName | Sztring | ImNotMalicious.exe | A hálózati kapcsolatokon keresztül továbbított fájlnév, például FTP és HTTP, amely megadja a fájlnevek adatait. | Fájl |
| FilePath | Sztring | C:\Malicious\ImNotMalicious.exe | A fájl teljes elérési útja, beleértve a fájl nevét | Fájl |
| FileHashMd5 | Sztring | 51BC68715FC7C109DCEA406B42D9D78F | A hálózati kapcsolatokon keresztül továbbított fájl MD5-kivonatának értéke a protokollok esetében. | Fájl |
| FileHashSha1 | Sztring | 491AE3... C299821476F4 | A fájl SHA1 kivonatának értéke, amelyet a rendszer a protokollok hálózati kapcsolatain keresztül továbbított. | Fájl |
| FileHashSha256 | Sztring | 9B8F8EDB... C129976F03 | A SHA256 a protokollok hálózati kapcsolatain továbbított kivonat értéke. | Fájl |
| FileHashSha512 | Sztring | 5E127D... F69F73F01F361 | A SHA512 a protokollok hálózati kapcsolatain továbbított kivonat értéke. | Fájl |
| FileExtension |  Sztring | exe | A protokoll hálózati kapcsolatokon keresztül továbbított fájl típusa, például FTP és HTTP. | Fájl
| FileMimeType | Sztring | alkalmazás/msword | A hálózati kapcsolatokon keresztül továbbított fájl MIME-típusa (például FTP és HTTP) | Fájl |
| FileSize | Egész szám | 23500 | A protokollok hálózati kapcsolatokon keresztül továbbított fájljának mérete bájtban megadva. | Fájl |
| HttpVersion | Sztring | 2.0 | HTTP/HTTPS hálózati kapcsolatok HTTP-kérelmének verziója. | Http |
| HttpRequestMethod | Sztring | GET | HTTP/HTTPS hálózati munkamenetek HTTP-metódusa. | Http |
| HttpStatusCode | Sztring | 404 | HTTP/HTTPS hálózati munkamenetek HTTP-állapotkód. | Http |
| HttpContentType | Sztring | multipart/űrlap – adatértékek; határ = valami | A http-válasz tartalomtípusának fejléce HTTP/HTTPS hálózati munkamenetekhez. | Http |
| HttpReferrerOriginal | Sztring | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS hálózati munkamenetek HTTP-hivatkozó fejléce. | Http |
| HttpUserAgentOriginal | Sztring | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, például Gecko) Chrome/83.0.4103.97 Safari/537.36 | HTTP/HTTPS hálózati munkamenetek HTTP-felhasználói ügynökének fejléce. | Http |
| HttpRequestXff | Sztring | 120.12.41.1 | A http-X – továbbított – a HTTP/HTTPS hálózati munkamenetek fejléce. | Http |
| UrlCategory | Sztring | Keresőmotorok | Egy URL-cím definiált csoportosítása (vagy csak az URL-cím tartománya alapján), ami ahhoz kapcsolódik, hogy mi is a (például: felnőtt, hírek, reklám, parkolt tartományok stb.) | url |
| UrlOriginal | Sztring | https://contoso.com/fo/?k=v&q = u # f | HTTP/HTTPS hálózati munkamenetek HTTP-kérelmének URL-címe. | URL-cím |
| UrlHostname | Sztring | contoso.com | A HTTP-/HTTPS-hálózati munkamenetek HTTP-kérelem URL-címének tartománya. | URL-cím |
| ThreatCategory | Sztring | Trójai | Egy biztonsági rendszer által azonosított fenyegetés kategóriája, például egy IP-címek webes biztonsági átjárója, és ehhez a hálózati munkamenethez van társítva. | Fenyegetés |
| ThreatId | Sztring | TR. 124 | Egy biztonsági rendszer által azonosított fenyegetés azonosítója, például egy IP-címek webes biztonsági átjárója, és ehhez a hálózati munkamenethez van társítva. | Fenyegetés |
| ThreatName | Sztring | EICAR-teszt fájl | Az azonosított fenyegetés vagy kártevő neve | Fenyegetés |
| AdditionalFields | Dinamikus (JSON-táska) | {<br>Tulajdonság1: "val1",<br>Property2: "val2"<br>} | Ha a séma egyik oszlopa sem egyezik, további mezők is tárolhatók egy JSON-csomagban.<br>A lekérdezési idejű elemzéshez javasolt, hogy ne használja ezt a módszert, mert a JSON-ba csomagoló adatként a lekérdezés teljesítményének romlása csökken. Ehelyett ajánlott további oszlopokat előléptetni.<br>A jövőbeli betöltési idejű elemzési forgatókönyvek esetében a rendszer természetesen a JSON-táska oszlopba gyűjt további adatokat. | Egyéni mező |
| 
