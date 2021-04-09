---
title: Common Event Format (CEF) kulcs és CommonSecurityLog mező leképezése
description: Ez a cikk a CEF kulcsokat képezi le az Azure Sentinel CommonSecurityLog található megfelelő mezők neveire.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 6c23fe86af030d371e12914062bb9558e8db3484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776208"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF és CommonSecurityLog mező leképezése

Az alábbi táblázatok a Common Event Format (CEF) mezőneveket használják az Azure Sentinel CommonSecurityLog használt nevekre, és hasznosak lehetnek, ha egy CEF-adatforrással dolgozik az Azure Sentinelben.

További információ: [a külső megoldás összekötése a Common Event Format használatával](connect-common-event-format.md).

## <a name="a---c"></a>A – C

|CEF kulcs neve  |CommonSecurityLog mező neve  |Leírás  |
|---------|---------|---------|
| Act    |    <a name="deviceaction"></a> DeviceAction     |  Az eseményben említett művelet.       |
|   App  |    ApplicationProtocol     |  Az alkalmazásban használt protokoll, például HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAP és így tovább.   |
| CNT    |    EventCount     |  Az eseményhez tartozó szám, amely azt mutatja, hogy hányszor tartották meg ugyanazt az eseményt.       |
| | | |

## <a name="d"></a>T

|CEF kulcs neve  |CommonSecurityLog neve  |Leírás  |
|---------|---------|---------|
|Eszköz szállítója     |  DeviceVendor       | Karakterlánc, amely az eszköz termék-és verzió-definíciókkal együtt egyedileg azonosítja a küldő eszköz típusát.       |
|Eszköz terméke     |   DeviceProduct      |   Karakterlánc, amely az eszköz gyártójának és verziójának definícióit együtt egyedileg azonosítja a küldő eszköz típusát.        |
|Eszköz verziója     |   DeviceVersion      |      Karakterlánc, amely az eszköz termék-és szállító-definíciókkal együtt egyedileg azonosítja a küldő eszköz típusát.     |
|DeviceEventClassID     |   DeviceEventClassID     |   Egy karakterlánc vagy egész szám, amely egyedi azonosítóként szolgál az esemény típusaként.      |
| destinationDnsDomain    | DestinationDnsDomain        |   A teljes tartománynév (FQDN) DNS-része.      |
| destinationServiceName | DestinationServiceName | Az eseményt célzó szolgáltatás. Például: `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Meghatározza az esemény által hivatkozott lefordított célhelyet egy IP-hálózaton, IPv4-alapú IP-címként. |
| destinationTranslatedPort | DestinationTranslatedPort | Port, fordítás után, például egy tűzfal. <br>Érvényes portszámok: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | A megfigyelt kommunikáció irányával kapcsolatos bármilyen információ. Érvényes értékek: <br>- `0` = Bejövő <br>- `1` = Kimenő |
| deviceDnsDomain | DeviceDnsDomain | A teljes tartománynév (FQDN) DNS-tartomány része |
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

|CEF kulcs neve  |CommonSecurityLog neve  |Leírás  |
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

|CEF kulcs neve  |CommonSecurityLog neve  |Leírás  |
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

|CEF kulcs neve  |CommonSecurityLog neve  |Leírás  |
|---------|---------|---------|
|Ok     |  Ok      |A naplózási esemény létrehozásának oka. <br><br>Például `Bad password` vagy `Unknown user`.         |
|Kérés     |   RequestURL      | A HTTP-kérelemhez használt URL-cím, beleértve a protokollt is. Például: `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   A kérelemhez társított felhasználói ügynök.      |
| requestContext | RequestContext | A kérelemből származó tartalmat, például a HTTP-hivatkozót ismerteti. |
| requestCookies | RequestCookies |A kérelemhez társított cookie-k. |
| requestMethod | RequestMethod | Az URL-cím elérésére szolgáló metódus. <br><br>Az érvényes értékek közé tartoznak például a (z), és hasonló metódusok `POST` `GET` . |
| RT | ReceiptTime | Az az idő, amikor a tevékenységhez kapcsolódó esemény érkezett. |
|Súlyosság     |  <a name="logseverity"></a> LogSeverity       |  Az esemény fontosságát leíró karakterlánc vagy egész szám.<br><br> Érvényes karakterlánc-értékek:,,, `Unknown` `Low` `Medium` `High` , `Very-High` <br><br>Az érvényes egész értékek a következők: `0` - `3` = alacsony, `4` - `6` = közepes, `7` - `8` = magas, `9` - `10` = Very-High |
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
| típus | EventType | Eseménytípus. Az értékek a következők: <br>- `0`: alapesemény <br>- `1`: összesített <br>- `2`: korrelációs esemény <br>- `3`: művelet esemény <br><br>**Megjegyzés**: ez az esemény nem hagyható el az alapesemények esetében. |
| | | |

## <a name="unmapped-fields"></a>Nem leképezett mezők

A következő **CommonSecurityLog** -mezők nevei nem rendelkeznek leképezésekkel a CEF kulcsokban:


|CommonSecurityLog mező neve  |Leírás  |
|---------|---------|
|**OriginalLogSeverity**     |  Mindig üres, a CiscoASA-vel való integráció támogatott. <br>A log súlyossági értékekkel kapcsolatos részletekért tekintse meg a [LogSeverity](#logseverity) mezőt.       |
|**RemoteIP**     |     A távoli IP-cím. <br>Ez az érték a [CommunicationDirection](#communicationdirection) mezőn alapul, ha lehetséges.     |
|**TávoliPORT**     |   A távoli port. <br>Ez az érték a [CommunicationDirection](#communicationdirection) mezőn alapul, ha lehetséges.      |
|**SimplifiedDeviceAction**     |   Leegyszerűsíti a [DeviceAction](#deviceaction) értékét az értékek statikus készletére, miközben az eredeti értéket a [DeviceAction](#deviceaction) mezőben tartja. <br>Például: `Denied`  >  `Deny` .      |
|     |         |


## <a name="next-steps"></a>Következő lépések

További információ: [a külső megoldás összekötése a Common Event Format használatával](connect-common-event-format.md).
