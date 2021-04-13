---
title: Common Event Format (CEF) kulcs és CommonSecurityLog mező leképezése
description: Ez a cikk a CEF kulcsokat képezi le az Azure Sentinel CommonSecurityLog található megfelelő mezők neveire.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 1670d1bb291e30295018146f2a24c5282feac6e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311651"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF és CommonSecurityLog mező leképezése

Az alábbi táblázatok a Common Event Format (CEF) mezőneveket használják az Azure Sentinel CommonSecurityLog használt nevekre, és hasznosak lehetnek, ha egy CEF-adatforrással dolgozik az Azure Sentinelben.

További információ: [a külső megoldás összekötése a Common Event Format használatával](connect-common-event-format.md).

## <a name="a---c"></a>A – C

|CEF kulcs neve  |CommonSecurityLog mező neve  |Description  |
|---------|---------|---------|
| Act    |    <a name="deviceaction"></a> DeviceAction     |  Az eseményben említett művelet.       |
|   App  |    ApplicationProtocol     |  Az alkalmazásban használt protokoll, például HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAP és így tovább.   |
| CNT    |    EventCount     |  Az eseményhez tartozó szám, amely azt mutatja, hogy hányszor tartották meg ugyanazt az eseményt.       |
| | | |

## <a name="d"></a>T

|CEF kulcs neve  |CommonSecurityLog neve  |Description  |
|---------|---------|---------|
|Eszköz szállítója     |  DeviceVendor       | Karakterlánc, amely az eszköz termék-és verzió-definíciókkal együtt egyedileg azonosítja a küldő eszköz típusát.       |
|Eszköz terméke     |   DeviceProduct      |   Karakterlánc, amely az eszköz gyártójának és verziójának definícióit együtt egyedileg azonosítja a küldő eszköz típusát.        |
|Eszköz verziója     |   DeviceVersion      |      Karakterlánc, amely az eszköz termék-és szállító-definíciókkal együtt egyedileg azonosítja a küldő eszköz típusát.     |
| destinationDnsDomain    | DestinationDnsDomain        |   A teljes tartománynév (FQDN) DNS-része.      |
| destinationServiceName | DestinationServiceName | Az eseményt célzó szolgáltatás. Például: `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Meghatározza az esemény által hivatkozott lefordított célhelyet egy IP-hálózaton, IPv4-alapú IP-címként. |
| destinationTranslatedPort | DestinationTranslatedPort | Port, fordítás után, például egy tűzfal. <br>Érvényes portszámok: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | A megfigyelt kommunikáció irányával kapcsolatos bármilyen információ. Érvényes értékek: <br>- `0` = Bejövő <br>- `1` = Kimenő |
| deviceDnsDomain | DeviceDnsDomain | A teljes tartománynév (FQDN) DNS-tartomány része |
|DeviceEventClassID     |   DeviceEventClassID     |   Egy karakterlánc vagy egész szám, amely egyedi azonosítóként szolgál az esemény típusaként.      |
| deviceExternalID | DeviceExternalID | Az eseményt létrehozó eszközt egyedileg azonosító név. |
| deviceFacility | DeviceFacility | Az eseményt létrehozó létesítmény.|
| deviceInboundInterface | DeviceInboundInterface |Az a csatoló, amelyen a csomag vagy az eszköz bekerült.  |
| deviceNtDomain | DeviceNtDomain | Az eszközhöz tartozó Windows-tartomány |
| deviceOutboundInterface | DeviceOutboundInterface |Az a csatoló, amelyen a csomag vagy az adategység elhagyta az eszközt. |
| devicePayloadId |DevicePayloadId |Az eseményhez társított adattartalom egyedi azonosítója. |
| deviceProcessName | ProcessName | Az eseményhez társított folyamat neve. <br><br>A UNIX rendszerben például a syslog-bejegyzést generáló folyamat. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Meghatározza az IP-hálózatban az esemény által hivatkozott lefordított eszköz címét. <br><br>A formátum egy IPv4-címe. |
| dhost |DestinationHostName | Az a cél, amelyre az esemény hivatkozik egy IP-hálózatban.  <br>A formátumnak a cél csomóponthoz társított teljes tartománynévnek kell lennie, amikor egy csomópont elérhető. Például `host.domain.com` vagy `host`. |
| gal Baki Piroska | DestinationMacAddress | A cél MAC-címe (FQDN) |
| dntdom | DestinationNTDomain | A cél címe Windows-tartományneve.|
| dpid | DestinationProcessId |Az eseményhez társított cél folyamat azonosítója.|
| dpriv | DestinationUserPrivileges | Meghatározza a cél használatának jogosultságait. <br>Érvényes értékek: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | Az esemény rendeltetési folyamatának neve, például `telnetd` vagy `sshd.` |
| DPT | DestinationPort | Célport. <br>Érvényes értékek: `*0` - `65535` |
| cél | DestinationIP | A cél IpV4-cím, amelyet az esemény az IP-hálózatban hivatkozik. |
| dtz | DeviceTimeZon | Az eseményt létrehozó eszköz időzónája |
| DUID |DestinationUserId | Azonosítja a célként megadott felhasználót azonosító alapján. |
| duser | DestinationUserName |A cél felhasználó nevét azonosítja.|
| konkurens | DeviceAddress | Az eseményt létrehozó eszköz IPv4-címe. |
| dvchost | DeviceName | Az eszköz csomópontjához társított teljes tartománynév, ha van elérhető csomópont. Például `host.domain.com` vagy `host`.| 
| dvcmac | DeviceMacAddress | Az eseményt létrehozó eszköz MAC-címe. |
| dvcpid | Folyamatazonosító | Meghatározza az eseményt létrehozó eszközön lévő folyamat AZONOSÍTÓját. |

## <a name="e---i"></a>E-I

|CEF kulcs neve  |CommonSecurityLog neve  |Description  |
|---------|---------|---------|
|befejezés     |  EndTime       | Az az időpont, amikor az eseményhez kapcsolódó tevékenység befejeződött.        |
|externalId    |   ExternalID      | A kezdeményező eszköz által használt azonosító. Ezek az értékek jellemzően növelik az egyes eseményekhez társított értékeket.        |
|fileCreateTime     |  FileCreateTime      | A fájl létrehozásának ideje.        |
|fileHash     |   Fájlkivonat      |   Egy fájl kivonata.      |
|fileId     |   FileID      |Egy fájlhoz társított azonosító, például a inode.         |
| fileModificationTime | FileModificationTime |A fájl utolsó módosításának ideje. |
| filePath | FilePath | A fájl teljes elérési útja, beleértve a fájlnevet. Például: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` vagy `/usr/bin/zip` .|
| filePermission |FilePermission |A fájl engedélyei. |
| fileType | FileType | A fájl típusa, például a cső, a szoftvercsatorna stb.|
|fname | FileName| A fájl neve, az elérési út nélkül. |
| fsize | FileSize | A fájl mérete. |
|Gazdagép    |  Computer       | Gazdagép, a Syslogből        |
|be     |  ReceivedBytes      |Az átvitt bájtok száma.         |
| | | |

## <a name="m---p"></a>M-P

|CEF kulcs neve  |CommonSecurityLog neve  |Description  |
|---------|---------|---------|
|msg   |  Üzenet       | Egy üzenet, amely további részleteket biztosít az eseményről.        |
|Name     |  Tevékenység       |   Egy karakterlánc, amely az esemény ember által olvasható és értelmezhető leírását jelöli.     |
|oldFileCreateTime     |  OldFileCreateTime       | A régi fájl létrehozásának ideje.        |
|oldFileHash     |   OldFileHash      |   A régi fájl kivonata.      |
|oldFileId     |   OldFileId     |   A régi fájllal társított azonosító, például a inode.      |
| oldFileModificationTime | OldFileModificationTime |A régi fájl utolsó módosításának időpontja. |
| oldFileName |  OldFileName |A régi fájl neve. |
| oldFilePath | OldFilePath | A régi fájl teljes elérési útja, beleértve a fájlnevet. <br>Például `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` vagy `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |A régi fájl engedélyei. |
|oldFileSize | OldFileSize | A régi fájl mérete.|
| oldFileType | OldFileType | A régi fájl fájltípusa, például egy cső, egy szoftvercsatorna stb.|
| ki | SentBytes | A kimenő forgalomban továbbított bájtok száma. |
| Eredmény | Eredmény | Az esemény eredménye, például: `success` vagy `failure` .|
|proto    |  Protokoll       | Átviteli protokoll, amely a használt 4. rétegbeli protokollt azonosítja. <br><br>A lehetséges értékek közé tartoznak a protokollok neve, például a `TCP` vagy a `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|CEF kulcs neve  |CommonSecurityLog neve  |Description  |
|---------|---------|---------|
|Ok     |  Ok      |A naplózási esemény létrehozásának oka. <br><br>Például `Bad password` vagy `Unknown user`.         |
|Kérés     |   RequestURL      | A HTTP-kérelemhez használt URL-cím, beleértve a protokollt is. Például: `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   A kérelemhez társított felhasználói ügynök.      |
| requestContext | RequestContext | A kérelemből származó tartalmat, például a HTTP-hivatkozót ismerteti. |
| requestCookies | RequestCookies |A kérelemhez társított cookie-k. |
| requestMethod | RequestMethod | Az URL-cím elérésére szolgáló metódus. <br><br>Az érvényes értékek közé tartoznak például a (z), és hasonló metódusok `POST` `GET` . |
| RT | ReceiptTime | Az az idő, amikor a tevékenységhez kapcsolódó esemény érkezett. |
|Súlyosság     |  <a name="logseverity"></a> LogSeverity       |  Az esemény fontosságát leíró karakterlánc vagy egész szám.<br><br> Érvényes karakterlánc-értékek:,,, `Unknown` `Low` `Medium` `High` , `Very-High` <br><br>Az érvényes egész értékek a következők:<br> - `0`-`3` = Alacsony <br>- `4`-`6` = Közepes<br>- `7`-`8` = Magas<br>- `9`-`10` = Very-High |
| Shost    | SourceHostName        |Meghatározza azt a forrást, amelyre az esemény hivatkozik egy IP-hálózaton. A formátumnak a forrás-csomóponthoz társított teljes tartománynévnek (DQDN) kell lennie, amikor egy csomópont elérhető. Például `host` vagy `host.domain.com`. |
| SMAC | SourceMacAddress | Forrás MAC-címe. |
| sntdom | SourceNTDomain | A forráscím Windows-tartományneve. |
| sourceDnsDomain | SourceDnsDomain | A teljes FQDN DNS-tartomány része. |
| sourceServiceName | SourceServiceName | Az esemény generálásához felelős szolgáltatás. |
| sourceTranslatedAddress | SourceTranslatedAddress | Meghatározza azt a lefordított forrást, amelyet az esemény az IP-hálózatban hivatkozik. |
| sourceTranslatedPort | SourceTranslatedPort | A forrás port fordítás után, például egy tűzfallal. <br>Érvényes portszámok: `0`  -  `65535` . |
| SPID | SourceProcessId | Az eseményhez társított forrásoldali folyamat azonosítója.|
| spriv | SourceUserPrivileges | A forrás felhasználó jogosultságai. <br><br>Az érvényes értékek a következők: `Administrator` , `User` , `Guest` |
| sproc | SourceProcessName | Az esemény forrásoldali folyamatának neve.|
| SPT | SourcePort | A forrásport száma. <br>Érvényes portszámok: `0`  -  `65535` . |
| src | SourceIP |Az a forrás, amelyet egy esemény egy IP-hálózaton, IPv4-címként hivatkozik. |
| start | StartTime | Az az idő, amikor az esemény a tevékenységre hivatkozik. |
| suid | SourceUserID | Azonosítja a forrás felhasználót azonosító alapján. |
| suser | SourceUserName | Azonosítja a forrás felhasználó nevét. |
| típus | EventType | Eseménytípus. Az értékek a következők: <br>- `0`: alapesemény <br>- `1`: összesített <br>- `2`: korrelációs esemény <br>- `3`: művelet esemény <br><br>**Megjegyzés**: ez az esemény nem hagyható el az alapesemények esetében. |
| | | |

## <a name="custom-fields"></a>Egyéni mezők

A következő táblázatok a beépített mezők egyikére nem érvényes CEF-kulcsok és CommonSecurityLog-mezők nevét képezik le az ügyfelek számára.

### <a name="custom-ipv6-address-fields"></a>Egyéni IPv6-címek mezői

Az alábbi táblázat az egyéni adathoz elérhető *IPv6-* CEF kulcs-és CommonSecurityLog tartalmazza.

|CEF kulcs neve  |CommonSecurityLog neve  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Egyéni számok mezői

Az alábbi *táblázat az egyéni* adathoz rendelkezésre álló CEF kulcs-és CommonSecurityLog tartalmazza.

|CEF kulcs neve  |CommonSecurityLog neve  |
|---------|---------|
|     CN1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     CN2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     CN3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Egyéni sztring mezők

Az alábbi táblázat a CEF kulcs-és CommonSecurityLog tartalmazza az egyéni adathoz elérhető *sztring* mezőkhöz.

|CEF kulcs neve  |CommonSecurityLog neve  |
|---------|---------|
|     CS1    |     <sup> [1](#use-sparingly) . DeviceCustomString1</sup>    |
|     cs1Label    |     <sup> [1](#use-sparingly) . DeviceCustomString1Label</sup>    |
|     CS2    |     <sup> [1](#use-sparingly) . DeviceCustomString2</sup>   |
|     cs2Label    |     <sup> [1](#use-sparingly) . DeviceCustomString2Label</sup> |
|     CS3    |     <sup> [1](#use-sparingly) . DeviceCustomString3</sup>  |
|     cs3Label    |     <sup> [1](#use-sparingly) . DeviceCustomString3Label</sup> |
|     CS4    |     <sup> [1](#use-sparingly) . DeviceCustomString4</sup> |
|     cs4Label    |     <sup> [1](#use-sparingly) . DeviceCustomString4Label</sup>  |
|     CS5    |     <sup> [1](#use-sparingly) . DeviceCustomString5</sup>   |
|     cs5Label    |     <sup> [1](#use-sparingly) . DeviceCustomString5Label</sup>    |
|     CS6    |     <sup> [1](#use-sparingly) . DeviceCustomString6</sup> |
|     cs6Label    |     <sup> [1](#use-sparingly) . DeviceCustomString6Label</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> javasoljuk, hogy a **DeviceCustomString** mezőket takarékosan használja, és ha lehetséges, használjon konkrétabb, beépített mezőket.
> 

### <a name="custom-timestamp-fields"></a>Egyéni timestamp-mezők

Az alábbi táblázat az egyéni adathoz elérhető *timestamp* mezők CEF kulcs-és CommonSecurityLog tartalmazza.

|CEF kulcs neve  |CommonSecurityLog neve  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Egyéni egész adatmezők

Az alábbi táblázat az egyéni adatokhoz elérhető *egész számok* CEF kulcs-és CommonSecurityLog tartalmazza.

|CEF kulcs neve  |CommonSecurityLog neve  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Dúsítási mezők

Az Azure Sentinel a következő **CommonSecurityLog** -mezőket adja hozzá a forrás-eszközöktől kapott eredeti események dúsításához, és nem rendelkezik leképezésekkel a CEF kulcsokban:

### <a name="threat-intelligence-fields"></a>Fenyegetés intelligencia mezői

|CommonSecurityLog mező neve  |Description  |
|---------|---------|
|   **IndicatorThreatType**  |  A [MaliciousIP](#MaliciousIP) -fenyegetés típusa a fenyegetési intelligencia csatornájának megfelelően.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | Felsorolja az üzenetben szereplő összes olyan IP-címet, amely összefügg az aktuális veszélyforrások felderítési adatcsatornával. |
|  **MaliciousIPCountry**   | A [MaliciousIP](#MaliciousIP) ország a rekordok betöltésének időpontjában a földrajzi információk alapján.        |
| **MaliciousIPLatitude**    |   A [MaliciousIP](#MaliciousIP) hosszúsága a rekord betöltésének időpontjában a földrajzi információk alapján.      |
| **MaliciousIPLongitude**    |  A [MaliciousIP](#MaliciousIP) hosszúsága a rekord betöltésének időpontjában a földrajzi információk alapján.       |
| **ReportReferenceLink**    |    A fenyegetési intelligencia jelentésre mutató hivatkozás.     |
|  **ThreatConfidence**   |   A fenyegetési intelligencia [MaliciousIP](#MaliciousIP) függően a fenyegetés megbízhatósága.      |
| **ThreatDescription**    |   A [MaliciousIP](#MaliciousIP) fenyegetés leírása a veszélyforrások intelligencia-hírcsatornájának megfelelően.      |
| **ThreatSeverity** | A fenyegetés súlyossága a [MaliciousIP](#MaliciousIP)a rekord betöltésének időpontjában a fenyegetési intelligencia csatornájának megfelelően. |
|     |         |

### <a name="additional-enrichment-fields"></a>További alkoholtartalom-növelési mezők

|CommonSecurityLog mező neve  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  Mindig üres, a CiscoASA-vel való integráció támogatott. <br>A log súlyossági értékekkel kapcsolatos részletekért tekintse meg a [LogSeverity](#logseverity) mezőt.       |
|**RemoteIP**     |     A távoli IP-cím. <br>Ez az érték a [CommunicationDirection](#communicationdirection) mezőn alapul, ha lehetséges.     |
|**TávoliPORT**     |   A távoli port. <br>Ez az érték a [CommunicationDirection](#communicationdirection) mezőn alapul, ha lehetséges.      |
|**SimplifiedDeviceAction**     |   Leegyszerűsíti a [DeviceAction](#deviceaction) értékét az értékek statikus készletére, miközben az eredeti értéket a [DeviceAction](#deviceaction) mezőben tartja. <br>Például: `Denied`  >  `Deny` .      |
|**SourceSystem**     | Mindig **OpsManager**-ként van definiálva.        |
|     |         |

## <a name="next-steps"></a>Következő lépések

További információ: [a külső megoldás összekötése a Common Event Format használatával](connect-common-event-format.md).
