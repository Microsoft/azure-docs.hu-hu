---
title: Az Azure Sentinel-entitások típusai – dokumentáció | Microsoft Docs
description: Ez a cikk az Azure Sentinel-entitások típusait és a szükséges azonosítókat jeleníti meg.
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456309"
---
# <a name="azure-sentinel-entity-types-reference"></a>Azure Sentinel-entitások típusai – dokumentáció

## <a name="entity-types-and-identifiers"></a>Entitások típusai és azonosítói

A következő táblázat az Azure Sentinelben jelenleg elérhető **entitások típusait** , valamint az egyes entitások típusának **azonosítóként** elérhető **attribútumait** mutatja be, amelyek az [elemzési szabály varázsló](tutorial-detect-threats-custom.md) [entitás-hozzárendelés](map-data-fields-to-entities.md) szakaszának **azonosítók** legördülő listájában jelennek meg.

A **szükséges azonosítók** oszlopban szereplő azonosítók mindegyike minimálisan szükséges az entitás azonosításához. Előfordulhat azonban, hogy egy szükséges azonosító önmagában nem elegendő az *egyedi* azonosításhoz. Minél több azonosító van használatban, annál nagyobb az egyedi azonosítás valószínűsége. Egyetlen entitás hozzárendeléséhez legfeljebb három azonosítót használhat.

A legjobb eredmény érdekében – a garantált egyedi azonosításhoz a **legerősebb azonosítók** oszlop azonosítóit kell használnia, amikor csak lehetséges. Több erős azonosító használata lehetővé teszi a különböző adatforrásokból és sémáokból származó erős azonosítók közötti korrelációt. Ez lehetővé teszi, hogy az Azure Sentinel átfogóbb elemzéseket nyújtson egy adott entitáshoz.

| Entitástípus | Azonosítók | Szükséges azonosítók | Legerősebb azonosítók |
| - | - | - | - |
| [**Felhasználói fiók**](#user-account)<br>*Fiók* | Name<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>SID<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>SID<br>Name<br>AadUserId<br>PUID<br>ObjectGuid | Név + NTDomain<br>Név + UPNSuffix<br>AADUserId<br>SID |
| [**Gazdagép**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | Állomásnév + NTDomain<br>Állomásnév + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP-cím**](#ip-address)<br>*IP* | Cím | Cím | |
| [**Kártevő**](#malware) | Name<br>Kategória | Name | |
| [**Fájl**](#file) | Címtár<br>Név | Név | |
| [**Folyamat**](#process) | Folyamatazonosító<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>Folyamatazonosító | |
| [**Felhőbeli alkalmazás**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**Tartománynév**](#domain-name)<br>*DNS* | DomainName | DomainName | |
| [**Azure-erőforrás**](#azure-resource) | ResourceId | ResourceId | |
| [**Fájlkivonat**](#file-hash)<br>*(FileHash)* | Algoritmus<br>Érték | Algoritmus + érték | |
| [**Beállításkulcs**](#registry-key) | Hive<br>Kulcs | Hive<br>Kulcs | Struktúra + kulcs |
| [**Beállításazonosító**](#registry-value) | Name<br>Érték<br>ÉrtékTípusa | Name | |
| [**Biztonsági csoport**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL-cím**](#url) | URL-cím | URL-cím | |
| [**IoT-eszköz**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Forrás<br>SourceRef<br>Gyártó<br>Modellezés<br>OperatingSystem<br>IP-cím<br>MacAddress<br>Protokollok<br>Sorozatszám | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**Postaláda**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>UPN<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**Levelezési fürt**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Fenyegetések<br>Lekérdezés<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>Forrás<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>Clustergroup felsorolásban találhatók | Lekérdezés<br>Forrás | Lekérdezés és forrás |
| [**Levélüzenet**](#mail-message) | Címzett<br>URLs<br>Fenyegetések<br>Küldő<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>Tárgy<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Nyelv<br>ThreatDetectionMethods | NetworkMessageId<br>Címzett | NetworkMessageId + címzett |
| [**E-mailek küldése**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Beküldő<br>NetworkMessageId<br>Időbélyeg<br>Címzett<br>Küldő<br>SenderIp<br>Tárgy<br>ReportType | SubmissionId<br>NetworkMessageId<br>Címzett<br>Beküldő |  |
|

## <a name="entity-type-schemas"></a>Entitás típusú sémák

Az alábbiakban részletesen áttekintheti az egyes entitások típusának teljes sémáit. Láthatja, hogy számos séma tartalmaz hivatkozásokat más típusú entitásokra, például a felhasználói fiók sémája tartalmaz egy hivatkozást a gazda entitás típusára, mivel a felhasználói fiók egyetlen attribútuma az a gazdagép, amelyet az adott meg. Ezek a külsőleg csatolt entitások nem használhatók az entitások leképezésének azonosítói, de nagyon hasznosak abban, hogy teljes képet adjanak az entitások és a vizsgálati gráf entitásokról.

> [!NOTE]
> A **Type (típus** ) oszlopban szereplő érték után megjelenő kérdőjel azt jelzi, hogy a mező üres.

## <a name="user-account"></a>Felhasználói fiók

*Entitás neve: fiók*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | fiók |
| Name | Sztring | A fiók neve. Ebben a mezőben csak a név adható meg, anélkül, hogy a tartomány hozzá lett adva. |
| *FullName* | *N/A* | *Nem része a sémának, az entitások leképezésének régi verziójával való visszamenőleges kompatibilitás érdekében.*
| NTDomain | Sztring | A NETBIOS-tartománynév, ahogy az a riasztás formátumában jelenik meg – váltózó Példák: pénzügy, NT AUTHORITY |
| DnsDomain | Sztring | A tartomány teljes DNS-neve. Példák: finance.contoso.com |
| UPNSuffix | Sztring | A fiókhoz tartozó egyszerű felhasználónév utótagja. Bizonyos esetekben ez a tartománynév is. Példák: contoso.com |
| Gazdagép | Entitás | A fiókot tartalmazó gazdagép, ha az helyi fiók. |
| SID | Sztring | A fiók biztonsági azonosítója, például S-1-5-18. |
| AadTenantId | GUID? | Az Azure AD-bérlő azonosítója, ha ismert. |
| AadUserId | GUID? | Az Azure AD-fiók objektumának azonosítója, ha ismert. |
| PUID | GUID? | Az Azure AD Passport felhasználói azonosítója, ha ismert. |
| IsDomainJoined | Bool? | Meghatározza, hogy ez egy tartományi fiók-e. |
| DisplayName | Sztring | A fiók megjelenített neve. |
| ObjectGuid | GUID? | A objectGUID attribútum egy egyértékű attribútum, amely a Active Directory által hozzárendelt objektum egyedi azonosítója. |
|

Fiók entitásának erős azonosítója:

- Név + UPNSuffix
- AadUserId
- SID + gazdagép (a beépített fiókok SID-azonosítójához szükséges)
- SID (a beépített fiókok biztonsági azonosítóinak kivételével)
- Név + NTDomain (kivéve, ha a NTDomain egy beépített tartomány, például "munkacsoport")
- Név + gazdagép (ha a NTDomain egy beépített tartomány, például "munkacsoport")
- Név + DnsDomain
- PUID
- ObjectGuid

Egy fiók entitásának gyenge azonosítója:

- Name

## <a name="host"></a>Gazdagép

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | gazdagép |
| DnsDomain | Sztring | Az a DNS-tartomány, amelyhez a gazdagép tartozik. Ha ismert, a tartomány teljes DNS-utótagját kell tartalmaznia. |
| NTDomain | Sztring | Az a NT-tartomány, amelyhez a gazdagép tartozik. |
| HostName | Sztring | Az állomásnév a tartomány utótagja nélkül. |
| *FullName* | *N/A* | *Nem része a sémának, az entitások leképezésének régi verziójával való visszamenőleges kompatibilitás érdekében.*
| NetBiosName | Sztring | Az állomásnév (Windows előtti 2000). |
| IoTDevice | Entitás | A IoT-eszköz entitása (ha a gazdagép IoT-eszközt jelöl). |
| AzureID | Sztring | A virtuális gép Azure-erőforrás-azonosítója, ha ismert. |
| OMSAgentID | Sztring | A OMS-ügynök azonosítója, ha a gazdagépen telepítve van a OMS-ügynök. |
| OSFamily | Enum? | A következő értékek egyike: <li>Linux<li>Windows<li>Android<li>iOS |
| OSVersion | Sztring | Az operációs rendszer ingyenes szöveges ábrázolása.<br>Ennek a mezőnek a célja, hogy az adott verziókat a OSFamily, a OSFamily enumerálása által nem támogatott jövőbeli értékeket is megtartsa. |
| IsDomainJoined | Logikai | Meghatározza, hogy a gazdagép tartományhoz tartozik-e. |
|

Gazda entitás erős azonosítója:
- Állomásnév + NTDomain
- Állomásnév + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (entitás-hozzárendelés esetén nem támogatott)

Egy gazda entitás gyenge azonosítói:
- HostName
- NetBiosName

## <a name="ip-address"></a>IP-cím

*Entitás neve: IP*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | IP |
| Cím | Sztring | Az IP-cím karakterláncként, például 127.0.0.1 (IPv4-ben vagy IPv6-ban). |
| Hely | Térinformatikai | Az IP-entitáshoz csatolt földrajzi hely környezete. |
|

Egy IP-entitás erős azonosítói:
- Cím

## <a name="malware"></a>Kártevő szoftver

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | kártevő |
| Name | Sztring | A gyártó által megadott kártevő-név, például: `Win32/Toga!rfn` . |
| Kategória | Sztring | A gyártó által megadott kártevő-kategória, például trójai. |
| Fájlok | Lista\<Entity> | Azon csatolt fájl-entitások listája, amelyeken a kártevő található. A fájl entitásokat beágyazottként vagy hivatkozásként is tartalmazhatja.<br>A struktúra további részleteiért tekintse meg a fájl entitást. |
| Folyamatok | Lista\<Entity> | Azon összekapcsolt folyamat entitások listája, amelyeken a kártevő található. Ez általában akkor használható, ha a riasztás nem a fájlokkal kapcsolatos tevékenységnél aktiválódik.<br>A struktúra további részleteiért tekintse meg a [folyamat](#process) entitást. |
|

Kártevő-entitás erős azonosítója:

- Név és Kategória

## <a name="file"></a>Fájl

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | fájl |
| Címtár | Sztring | A fájl teljes elérési útja. |
| Name | Sztring | Az elérési út nélküli fájlnév (Előfordulhat, hogy egyes riasztások nem tartalmazzák az elérési utat). |
| Gazdagép | Entitás | Az a gazdagép, amelyen a fájlt tárolták. |
| FileHashes | Entitás listázása &lt;&gt; | A fájlhoz társított fájlkivonat. |
|

Egy fájl entitásának erős azonosítója:
- Név + könyvtár
- Név + FileHash
- Név + könyvtár + FileHash

## <a name="process"></a>Folyamat

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | folyamat |
| Folyamatazonosító | Sztring | A folyamat azonosítója. |
| CommandLine | Sztring | A folyamat létrehozásához használt parancssor. |
| ElevationToken | Enum? | A folyamathoz társított jogosultságszint-emelési jogkivonat.<br>Lehetséges értékek:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | A folyamat futásának időpontja. |
| Image | Entitás (fájl) | A a fájl entitást inline vagy hivatkozásként is tartalmazhatja.<br>A struktúra további részleteiért tekintse meg a fájl entitást. |
| Fiók | Entitás | A folyamatokat futtató fiók.<br>A [fiók](#user-account) entitása beágyazott vagy hivatkozásként is szerepelhet.<br>A struktúra további részleteiért tekintse meg a [fiók](#user-account) entitást. |
| ParentProcess | Entitás (folyamat) | A fölérendelt folyamat entitása. <br>Tartalmazhat részleges adatértéket, azaz csak a PID-t. |
| Gazdagép | Entitás | Az a gazdagép, amelyen a folyamat futott. |
| LogonSession | Entitás (HostLogonSession) | Az a munkamenet, amelyben a folyamat futott. |
|

Egy folyamat entitásának erős azonosítója:

- Gazdagép + folyamatazonosító + CreationTimeUtc
- Gazdagép + ParentProcessId + CreationTimeUtc + parancssori
- Gazdagép + folyamatazonosító + CreationTimeUtc + Image
- Gazdagép + folyamatazonosító + CreationTimeUtc + image. FileHash

Egy folyamat entitásának gyenge azonosítója:

- Folyamatazonosító + CreationTimeUtc + CommandLine (és nincs gazdagép)
- Folyamatazonosító + CreationTimeUtc + Image (és nincs gazdagép)

## <a name="cloud-application"></a>Felhőbeli alkalmazás

*Entitás neve: CloudApplication*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | "Cloud-Application" |
| AppId | Int | Az alkalmazás technikai azonosítója. Ennek a [Felhőbeli alkalmazás-azonosítók](#cloud-application-identifiers)listájában megadott értékek egyikének kell lennie. A AppId mező értéke nem kötelező. |
| Name | Sztring | A kapcsolódó Felhőbeli alkalmazás neve. Az alkalmazásnév értéke nem kötelező. |
| InstanceName | Sztring | A felhőalapú alkalmazás felhasználó által definiált példányának neve. Gyakran használják az ügyfél által birtokolt különböző típusú alkalmazások megkülönböztetésére. |
|

Felhőalapú alkalmazás entitásának erős azonosítója:
 - AppId (példánynév nélkül)
 - Név (példánynév nélkül)
 - AppId + példánynév
 - Név + példánynév

## <a name="domain-name"></a>Tartománynév

*Entitás neve: DNS*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | DNS |
| DomainName | Sztring | A riasztáshoz tartozó DNS-rekord neve. |
| IP-cím | &lt;Entitás (IP) listázása&gt; | A megoldott IP-címekhez tartozó entitások. |
| DnsServerIp | Entitás (IP) | A kérés feloldására szolgáló DNS-kiszolgálót jelölő entitás. |
| HostIpAddress | Entitás (IP) | A DNS-kérési ügyfelet képviselő entitás. |
|

DNS-entitás erős azonosítói:
- Tartománynév + DnsServerIp + HostIpAddress

A DNS-entitások gyenge azonosítói:
- Tartománynév + HostIpAddress

## <a name="azure-resource"></a>Azure-erőforrás

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | "Azure-Resource" |
| ResourceId | Sztring | Az erőforrás Azure-erőforrás-azonosítója. |
| SubscriptionId | Sztring | Az erőforrás előfizetés-azonosítója. |
| TryGetResourceGroup | Logikai | Az erőforráscsoport értéke, ha létezik. |
| TryGetProvider | Logikai | A szolgáltató értéke, ha létezik. |
| TryGetName | Logikai | A név értéke, ha létezik. |
|

Azure-erőforrás entitások erős azonosítói:
- ResourceId

## <a name="file-hash"></a>Fájlkivonat

*Entitás neve: FileHash*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | 'filehash' |
| Algoritmus | Enumeráció | A kivonatoló algoritmus típusa. Lehetséges értékek:<li>Ismeretlen<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Érték | Sztring | A kivonat értéke. |
|

Fájlkivonat-entitás erős azonosítója:
- Algoritmus + érték

## <a name="registry-key"></a>Beállításkulcs

*Entitás neve: RegistryKey*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | "beállításjegyzék-kulcs" |
| Hive | Enum? | A következő értékek egyike:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Kulcs | Sztring | A beállításkulcs elérési útja. |
|

Beállításkulcs-entitás erős azonosítói:
- Struktúra + kulcs

## <a name="registry-value"></a>Beállításazonosító

*Entitás neve: RegistryValue*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | "Registry-Value" |
| Kulcs | Entitás (RegistryKey) | A beállításkulcs entitása. |
| Name | Sztring | A beállításazonosító neve. |
| Érték | Sztring | Az érték karakterlánc formátumú ábrázolása. |
| ÉrtékTípusa | Enum? | A következő értékek egyike:<li>Sztring<li>Bináris<li>DWord<li>Qword<li>Többkarakterláncos<li>ExpandString<li>Nincsenek<li>Ismeretlen<br>Az értékeknek meg kell felelniük a Microsoft. Win32. RegistryValueKind enumerálásnak. |
|

Egy beállításazonosító típusú entitás erős azonosítója:
- Kulcs + név

Egy beállításazonosító típusú entitás gyenge azonosítói:
- Név (kulcs nélkül)

## <a name="security-group"></a>Biztonsági csoport

*Entitás neve: SecurityGroup*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | "biztonsági csoport" |
| DistinguishedName | Sztring | A csoport megkülönböztető neve. |
| SID | Sztring | A SID attribútum egy egyértékű attribútum, amely meghatározza a csoport biztonsági azonosítóját (SID). |
| ObjectGuid | GUID? | A objectGUID attribútum egy egyértékű attribútum, amely a Active Directory által hozzárendelt objektum egyedi azonosítója. |
|

Egy biztonsági csoport entitásának erős azonosítója:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL-cím

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | URL |
| URL-cím | URI | Teljes URL-cím, amelyre az entitás mutat. |
|

URL-entitás erős azonosítói:
- URL-cím (abszolút URL-cím esetén)

URL-entitás gyenge azonosítói:
- URL-cím (relatív URL-cím esetén)

## <a name="iot-device"></a>IoT-eszköz

*Entitás neve: IoTDevice*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | 'iotdevice' |
| IoTHub | Entitás (AzureResource) | Az eszközhöz tartozó IoT Hub jelképező AzureResource entitás. |
| DeviceId | Sztring | Az eszköz azonosítója a IoT Hub környezetében. |
| DeviceName | Sztring | Az eszköz rövid neve. |
| IoTSecurityAgentId | GUID? | Az eszközön futó *IoT* -ügynökhöz tartozó Defender azonosítója. |
| DeviceType | Sztring | Az eszköz típusa ("hőmérséklet-érzékelő", "fagyasztó", "szélturbina" stb.). |
| Forrás | Sztring | Az eszköz entitásának forrása (Microsoft/szállító). |
| SourceRef | Entitás (URL) | Egy URL-hivatkozás arra a forrásoldali tételre, ahol az eszköz felügyelve van. |
| Gyártó | Sztring | Az eszköz gyártója. |
| Modellezés | Sztring | Az eszköz típusa. |
| OperatingSystem | Sztring | Az az operációs rendszer, amelyen az eszköz fut. |
| IP-cím | Entitás (IP) | Az eszköz aktuális IP-címe. |
| MacAddress | Sztring | Az eszköz MAC-címe. |
| Protokollok | Lista &lt; karakterlánca&gt; | Az eszköz által támogatott protokollok listája. |
| Sorozatszám | Sztring | Az eszköz sorozatszáma. |
|

Egy IoT-eszköz entitásának erős azonosítója:
- IoTHub + DeviceId

Egy IoT-eszköz entitásának gyenge azonosítói:
- DeviceId (IoTHub nélkül)

## <a name="mailbox"></a>Mailbox

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | postaláda |
| MailboxPrimaryAddress | Sztring | A postaláda elsődleges címe. |
| DisplayName | Sztring | A postaláda megjelenítendő neve. |
| UPN | Sztring | A postaláda UPN-je. |
| RiskLevel | Enum? | A postaláda kockázati szintje. Lehetséges értékek:<li>Nincsenek<li>Alacsony<li>Közepes<li>Magas |
| ExternalDirectoryObjectId | GUID? | A postaláda AzureAD azonosítója. Hasonló a AadUserId, de ez a tulajdonság az Office-oldalon lévő postaláda-objektumra vonatkozik. |
|

Postaláda-entitás erős azonosítói:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>Levelezési fürt

*Entitás neve: MailCluster*

> [!NOTE]
> **Mdo**  =  **Microsoft Defender for Office 365**, korábbi nevén Office 365 Advanced Threat Protection (O365 ATP).

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | "mail-cluster" |
| NetworkMessageIds | IList &lt; karakterlánc&gt; | A levelezési fürt részét képező levelezési üzenetek azonosítói. |
| CountByDeliveryStatus | IDictionary &lt; karakterlánc, int&gt; | A DeliveryStatus által küldött üzenetek száma. |
| CountByThreatType | IDictionary &lt; karakterlánc, int&gt; | A ThreatType által küldött üzenetek száma. |
| CountByProtectionStatus | IDictionary &lt; karakterlánc, hosszú&gt; | A veszélyforrások elleni védelem állapota által küldött üzenetek száma. |
| Fenyegetések | IList &lt; karakterlánc&gt; | A levelezési fürt részét képező e-mail-üzenetek fenyegetései. |
| Lekérdezés | Sztring | A levelezési fürt üzeneteinek azonosítására szolgáló lekérdezés. |
| QueryTime | DateTime? | A lekérdezés ideje. |
| MailCount | Int? | A levelezési fürt részét képező e-mail-üzenetek száma. |
| IsVolumeAnomaly | Bool? | Meghatározza, hogy ez egy kötet rendellenességének levelezési fürtje-e. |
| Forrás | Sztring | A levelezési fürt forrása (az alapértelmezett érték a "O365 ATP"). |
| ClusterSourceIdentifier | Sztring | A levelezési fürt forrását képező e-mail hálózati üzenet azonosítója. |
| ClusterSourceType | Sztring | A levelezési fürt forrásának típusa. Ez leképezi a MailClusterSourceType beállítást a MDO (lásd a fenti megjegyzést). |
| ClusterQueryStartTime | DateTime? | A fürt megszámlálási lekérdezésének kezdési ideje. Általában a Befejezés időpontjának dátuma mínusz a DaysToLookBack beállítása a MDO (lásd a fenti megjegyzést). |
| ClusterQueryEndTime | DateTime? | Fürtözött befejezési idő – a fürt számlálási lekérdezésének befejezési időpontjaként használatos. Általában a küldemények fogadásának ideje. |
| Clustergroup felsorolásban találhatók | Sztring | Megfelel a MDO használt Kusto-lekérdezési kulcsnak (lásd a fenti megjegyzést). |
|

Egy levelezési fürt entitásának erős azonosítója:
- Lekérdezés és forrás

## <a name="mail-message"></a>Levélüzenet

*Entitás neve: MailMessage*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | "levelezési üzenet" |
| Fájlok | IList- &lt; fájl&gt; | Az e-mail-üzenet mellékleteinek fájl entitásai. |
| Címzett | Sztring | A levelezési üzenet címzettje. Több címzett esetén a rendszer átmásolja az e-mail-üzenetet, és mindegyik másolat egy címzettet tartalmaz. |
| URLs | IList &lt; karakterlánc&gt; | Az e-mail-üzenetben található URL-címek. |
| Fenyegetések | IList &lt; karakterlánc&gt; | Az e-mail-üzenetben szereplő fenyegetések. |
| Küldő | Sztring | A feladó e-mail-címe. |
| P1Sender | Sztring | A (delegált) felhasználó e-mail-címe, aki a "P2 (elsődleges)" felhasználó nevében elküldte ezt a levelet. Ha a delegált nem küldi el az e-mailt, ez az érték egyenlő a P2Sender. |
| P1SenderDisplayName | Sztring | Annak a (delegált) felhasználónak a megjelenítendő neve, aki ezt a levelet elküldte "a P2 (elsődleges) felhasználó nevében". Az e-mail-fejlécben a "OnbehalfofSenderDisplayName" tulajdonság alapján képviselteti magát. |
| P1SenderDomain | Sztring | Annak a (delegált) felhasználónak az e-mail tartománya, aki ezt az e-mailt elküldte "a P2 (elsődleges) felhasználó nevében". Ha a delegált nem küldi el az e-mailt, ez az érték egyenlő a P2SenderDomain. |
| P2Sender | Sztring | Azon (elsődleges) felhasználó e-mail-címe, akinek az e-mail-címét elküldték. |
| P2SenderDisplayName | Sztring | Azon (elsődleges) felhasználó megjelenítendő neve, aki nevében az e-mail el lett küldve. Ha a delegált nem küldi el az e-mailt, ez a feladó megjelenítendő nevét jelöli. |
| P2SenderDomain | Sztring | Azon (elsődleges) felhasználó e-mail-tartománya, akinek az e-mail-címét elküldték. Ha a delegált nem küld e-mailt, ez a feladó tartományát jelöli. |
| SenderIP | Sztring | A küldő IP-címe. |
| ReceivedDate | DateTime | Az üzenet kézhezvételének dátuma. |
| NetworkMessageId | GUID? | A levelezési üzenet hálózati üzenetének azonosítója. |
| InternetMessageId | Sztring | Az e-mail-üzenet azonosítója. |
| Tárgy | Sztring | A levelezési üzenet tárgya. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | A Microsoft Defender for Office 365 használja az egyező vagy hasonló e-mail-üzenetek kereséséhez. |
| AntispamDirection | Enum? | Az e-mail-üzenet írásirányát. Lehetséges értékek:<li>Ismeretlen<li>Bejövő<li>Kimenő<li>Intraorg (belső) |
| DeliveryAction | Enum? | A levelezési üzenet kézbesítési művelete. Lehetséges értékek:<li>Ismeretlen<li>DeliveredAsSpam<li>Kézbesítve<li>Blokkolva<li>Helyébe |
| DeliveryLocation | Enum? | Az e-mail-üzenet kézbesítési helye. Lehetséges értékek:<li>Ismeretlen<li>Beérkezett üzenetek<li>JunkFolder<li>DeletedFolder<li>Karantén<li>Külső<li>Sikertelen<li>Csökkent<li>Továbbított |
| Nyelv | Sztring | Az a nyelv, amelyben a levél tartalma megírásra kerül. |
| ThreatDetectionMethods | IList &lt; karakterlánc&gt; | Az e-mailen alkalmazott veszélyforrások észlelési módszereinek listája. |
|

A levél üzenet entitásának erős azonosítója:
- NetworkMessageId + címzett

## <a name="submission-mail"></a>E-mailek küldése

*Entitás neve: SubmissionMail*

| Mező | Típus | Leírás |
| ----- | ---- | ----------- |
| Típus | Sztring | 'SubmissionMail' |
| SubmissionId | GUID? | A beküldési azonosító. |
| SubmissionDate | DateTime? | A beküldéshez tartozó jelentett dátum és idő. |
| Beküldő | Sztring | Az elküldő e-mail-címe. |
| NetworkMessageId | GUID? | Azon e-mail hálózati üzenet azonosítója, amelybe a Küldés tartozik. |
| Időbélyeg | DateTime? | Az üzenet fogadásakor megjelenő időbélyegző (E-mail). |
| Címzett | Sztring | A levél címzettje. |
| Küldő | Sztring | A levél küldője. |
| SenderIp | Sztring | A küldő IP-címe. |
| Tárgy | Sztring | A beküldési e-mail tárgya. |
| ReportType | Sztring | Az adott példány beküldési típusa. Ez leképezi a levélszemét, az adathalászok, a kártevők vagy a NotJunk. |
|

Egy SubmissionMail entitás erős azonosítói:
- SubmissionId, küldő, NetworkMessageId, címzett

## <a name="cloud-application-identifiers"></a>Felhőbeli alkalmazás-azonosítók

Az alábbi lista az ismert felhőalapú alkalmazások azonosítóit határozza meg. Az alkalmazás-azonosító érték a [Cloud Application](#cloud-application) Entity azonosítójának használata.

|Alkalmazásazonosító|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive szoftver|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon webszolgáltatások|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive Vállalati verzió|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Az Autodesk Fusion életciklusa|
|23043|Slack|
|23233|Online Microsoft Office|
|25275|Microsoft Skype Vállalati verzió|
|25988|Google docs|
|26055|Microsoft Office 365 felügyeleti központ|
|26060|OPSWAT-fogaskerekek|
|26061|Microsoft Word online|
|26062|Microsoft PowerPoint online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft ás|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS proxy emulátor|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan alakította ki az Entity, az azonosítókat és a sémát az Azure Sentinelben.

További információ az [entitásokról](entities-in-azure-sentinel.md) és az [entitások hozzárendeléséről](map-data-fields-to-entities.md). 
