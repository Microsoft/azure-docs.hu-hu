---
title: Diagnosztikai eszköz a StorSimple 8000-eszköz hibáinak megoldásához | Microsoft Docs
description: Ez a cikk áttekintést nyújt a diagnosztikai eszközről, és leírja, hogyan használható az eszköz StorSimple-eszközzel.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bf0570cd62a2c329407cfb6dd087020f53cdc991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954069"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Az 8000 sorozatú eszköz problémáinak elhárításához használja a StorSimple Diagnostics eszközt

## <a name="overview"></a>Áttekintés

A StorSimple Diagnostics eszköz a rendszer-, a teljesítmény-, a hálózati és a hardver-összetevők állapotával kapcsolatos problémákat diagnosztizálja egy StorSimple-eszköz esetében. A diagnosztikai eszközt különböző helyzetekben lehet használni. Ilyen forgatókönyvek például a számítási feladatok tervezése, a StorSimple-eszközök üzembe helyezése, a hálózati környezet felmérése és az operatív eszköz teljesítményének meghatározása. Ez a cikk áttekintést nyújt a diagnosztikai eszközről, és leírja, hogyan használható az eszköz StorSimple-eszközzel.

A diagnosztikai eszköz elsődleges célja a StorSimple 8000 Series helyszíni eszközök (8100 és 8600).

## <a name="run-diagnostics-tool"></a>Diagnosztika eszköz futtatása

Ez az eszköz a StorSimple-eszköz Windows PowerShell-felületén keresztül is futtatható. Az eszköz helyi felületét kétféleképpen érheti el:

* [A PuTTY használatával csatlakozhat az eszköz soros konzolhoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Távolról férhet hozzá az eszközhöz a Windows PowerShell StorSimple-bővítményeon keresztül](storsimple-8000-remote-connect.md).

Ez a cikk azt feltételezi, hogy az eszköz soros konzolját a PuTTY használatával csatlakoztatta.

#### <a name="to-run-the-diagnostics-tool"></a>A diagnosztikai eszköz futtatása

Miután csatlakozott az eszköz Windows PowerShell-felületéhez, hajtsa végre a következő lépéseket a parancsmag futtatásához.
1. Jelentkezzen be az eszköz soros konzolján a [Putty használata az eszköz soros konzolhoz való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)című témakör lépéseit követve.

2. Írja be a következő parancsot:

    `Invoke-HcsDiagnostics`

    Ha a hatókör-paraméter nincs megadva, a parancsmag végrehajtja az összes diagnosztikai tesztet. A tesztek közé tartozik a rendszerek, a hardver-összetevők állapota, a hálózat és a teljesítmény. 
    
    Csak egy adott teszt futtatásához adja meg a hatókör paramétert. Ha például csak a hálózati tesztet szeretné futtatni, írja be a következőt:

    `Invoke-HcsDiagnostics -Scope Network`

3. További elemzéshez válassza ki és másolja ki a PuTTY ablak kimenetét szövegfájlba.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>A diagnosztikai eszköz használatára vonatkozó forgatókönyvek

A diagnosztika eszköz használatával elháríthatja a rendszeren a hálózat, a teljesítmény, a rendszerek és a hardver állapotát. Íme néhány lehetséges forgatókönyv:

* **Offline eszköz** – a StorSimple 8000 Series eszköz offline állapotban van. A Windows PowerShell felületéről azonban úgy tűnik, hogy mindkét vezérlő működik.
    * Ezzel az eszközzel megadhatja a hálózati állapotot.
         
         > [!NOTE]
         > Ne használja ezt az eszközt a teljesítmény-és hálózati beállítások értékeléséhez az eszközön a regisztráció előtt (vagy konfigurálás a telepítővarázsló segítségével). A telepítővarázsló és a regisztráció során érvényes IP-cím van hozzárendelve az eszközhöz. Ezt a parancsmagot olyan eszközön futtathatja, amely nincs regisztrálva a hardver állapotához és a rendszerhez. Használja a Scope paramétert, például:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Állandó eszközökkel kapcsolatos problémák** – úgy tűnik, hogy megmaradnak az eszközön észlelt problémák. Előfordulhat például, hogy a regisztráció sikertelen. Előfordulhat, hogy az eszköz sikeres regisztrálása és a művelet ideje alatt problémák léptek fel az eszközön.
    * Ebben az esetben az előzetes hibaelhárításhoz használja ezt az eszközt, mielőtt bejelentkezik a Microsoft ügyfélszolgálata. Azt javasoljuk, hogy futtassa ezt az eszközt, és rögzítse az eszköz kimenetét. Ezután megadhatja ezt a kimenetet a hibaelhárítás meggyorsításához.
    * Ha hardveres összetevők vagy fürtök meghibásodása van, jelentkezzen be egy Support request.

* **Alacsony eszköz teljesítménye** – a StorSimple-eszköz lassú.
    * Ebben az esetben futtassa ezt a parancsmagot a hatókör paraméterrel a teljesítmény beállításnál. A kimenet elemzése. A felhő írási és olvasási késleltetését kapja. A jelentett késések maximálisan elérhető célként való használata, a belső adatfeldolgozás során felszámított tényező, majd a számítási feladatok üzembe helyezése a rendszeren. További információt [a hálózati teszt használata az eszközök teljesítményének megoldásához](#network-test)című témakörben olvashat.


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnosztikai tesztek és minták kimenetei

### <a name="hardware-test"></a>Hardveres teszt

Ez a teszt meghatározza a hardver-összetevők, az USM belső vezérlőprogram és a rendszeren futó lemez belső vezérlőprogram állapotát.

* A jelentett hardver-összetevők azok az összetevők, amelyek nem tudták végrehajtani a tesztet, vagy nincsenek jelen a rendszeren.
* Az USM belső vezérlőprogram és a lemez belső vezérlőprogram-verzióit a rendszer a 0. vezérlő, a vezérlő 1 és a megosztott összetevők jelentésére adja. A hardver-összetevők teljes listáját a következő lépésekben végezheti el:

    * [Összetevők az elsődleges házban](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [EBOD ház összetevői](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Ha a hardveres teszt nem tudta lekérdezni az összetevőket, [Jelentkezzen be egy szolgáltatásbeli kérelembe Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>A hardveres tesztek egy 8100-es eszközön való futtatásának mintája

Az alábbi példa egy StorSimple 8100-eszköz kimenetét jeleníti meg. Az 8100-es modell eszközén nincs jelen a EBOD ház. Ezért a EBOD-vezérlő összetevői nincsenek jelezve.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Rendszerteszt

Ez a teszt a rendszerinformációkat, a rendelkezésre álló frissítéseket, a fürt adatait és az eszköz szolgáltatási információit jelenti.

* A Rendszerinformáció magában foglalja a modellt, az eszköz sorozatszámát, az időzónát, a vezérlő állapotát, valamint a rendszeren futó részletes szoftververzió-verziót. A kimenetként jelentett különböző rendszerparaméterek megismeréséhez lépjen a [rendszerinformációk értelmezése](#appendix-interpreting-system-information)elemre.

* A frissítés rendelkezésre állása jelentést készít arról, hogy a rendszeres és a karbantartási módok elérhetők-e, valamint a hozzájuk tartozó csomagok nevét. Ha `RegularUpdates` és így `MaintenanceModeUpdates` van `false` , ez azt jelzi, hogy a frissítések nem érhetők el. Az eszköz naprakész.
* A fürt adatai tartalmazzák az összes HCS FRISSÍTŐÜGYNÖK-fürt és a hozzájuk tartozó állapotok különböző logikai összetevőinek információit. Ha a jelentés ezen szakaszában a kapcsolat nélküli fürtszolgáltatás jelenik meg, forduljon a [Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).
* A szolgáltatás adatai tartalmazzák az eszközön futó összes HCS FRISSÍTŐÜGYNÖK és CiS-szolgáltatás nevét és állapotát. Ezek az információk hasznosak lehetnek a Microsoft ügyfélszolgálata az eszköz problémáinak elhárításában.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>A Rendszerteszt kimenetének mintája egy 8100-eszközön

Íme egy példa a Rendszerteszt egy 8100-es eszközön való futtatásának kimenetére.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Hálózati teszt

Ez a teszt érvényesíti a hálózati adapterek, a portok, a DNS és az NTP-kiszolgáló kapcsolatának állapotát, a TLS/SSL-tanúsítványt, a Storage-fiók hitelesítő adatait, a frissítési kiszolgálókhoz való kapcsolódást és a webproxy kapcsolatát a StorSimple-eszközön.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Példa a hálózati teszt kimenetére, ha csak a DATA0 engedélyezve van

Az alábbi példa az 8100-es eszköz kimenetét jeleníti meg. A kimenetben az alábbiak láthatók:
* Csak az adat0 és az 1. adathálózati adapterek engedélyezettek és konfigurálhatók.
* A portálon nincs engedélyezve a 2-5-es érték.
* A DNS-kiszolgáló konfigurációja érvényes, és az eszköz csatlakozhat a DNS-kiszolgálón keresztül.
* Az NTP-kiszolgáló kapcsolata is rendben van.
* A 80-es és a 443-es port nyitva van. Az 9354-as port azonban le van tiltva. A [rendszerhálózati követelmények](./storsimple-8000-system-requirements.md)alapján meg kell nyitnia ezt a portot a Service Bus-kommunikációhoz.
* A TLS/SSL-tanúsítvány érvényes.
* Az eszköz kapcsolódni tud a Storage-fiókhoz: _myss8000storageacct_.
* A frissítési kiszolgálókhoz való kapcsolódás érvényes.
* A webproxy nincs konfigurálva ezen az eszközön.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Példa a hálózati teszt kimenetére, ha a DATA0 és a DATA1 engedélyezve van

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Teljesítményteszt

Ez a teszt a felhő teljesítményét a Felhőbeli írási-olvasási késések használatával jelenti az eszközön. Ezzel az eszközzel lehet létrehozni a Felhőbeli teljesítmény alapkonfigurációját, amelyet a StorSimple érhet el. Az eszköz jelentést készít a maximális teljesítményről (az írási és olvasási késések esetében a legjobb esetben), amelyet a kapcsolatban kaphat.

Mivel az eszköz a maximálisan elérhető teljesítményt jelenti, a számítási feladatok telepítésekor a jelentett írási és olvasási késéseket is használhatja célként.

A teszt szimulálja az eszközön a különböző típusú kötetekhez társított blob-méreteket. A helyileg rögzített kötetek normál rétegű és biztonsági mentései 64 KB-os blob-méretet használnak. Az archiválási lehetőséggel rendelkező többszintű kötetek esetében az 512 KB blob adatméretet használja. Ha az eszközön többplatformos és helyileg rögzített kötetek vannak konfigurálva, akkor a rendszer csak a 64 KB-os blob-adatméretnek megfelelő tesztet futtatja.

Az eszköz használatához hajtsa végre a következő lépéseket:

1.  Először is hozzon létre egy többszintű kötetet és egy többszintű kötetet az archivált lehetőség bejelölve. Ez a művelet gondoskodik arról, hogy az eszköz a 64 KB-os és 512 KB-os blob-méretek esetén is futtatja a teszteket

2. Futtassa a parancsmagot a kötetek létrehozása és konfigurálása után. Típus:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Jegyezze fel az eszköz által jelentett írható és olvasható késéseket. Ez a teszt több percet is igénybe vehet, mielőtt jelentést készít az eredményekről.

4. Ha a kapcsolódási késések mind a várt tartományba tartoznak, akkor az eszköz által jelentett késések maximálisan elérhető célként használhatók a munkaterhelések telepítésekor. A belső adatfeldolgozáshoz kapcsolódó terhelési tényező.

    Ha a diagnosztikai eszköz által jelentett írási-olvasási késések magasak:

    1. Konfigurálja Storage Analytics a blob Services számára, és elemezze a kimenetet az Azure Storage-fiók késésének megismeréséhez. Részletes útmutatásért lásd: [Storage Analytics engedélyezése és konfigurálása](../storage/blobs/monitor-blob-storage.md). Ha ezek a késések is nagyok és hasonlók az StorSimple Diagnostics eszköztől kapott számokhoz, akkor az Azure Storage szolgáltatásban kell naplóznia egy szolgáltatási kérelmet.

    2. Ha a tárolási fiók késése alacsony, forduljon a hálózati rendszergazdához, és vizsgálja meg a hálózat késésével kapcsolatos problémákat.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>A teljesítményteszt kimenete egy 8100-es eszközön fut.

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Függelék: rendszerinformációk értelmezése

Itt látható egy táblázat, amely leírja, hogy a rendszerinformációk különböző Windows PowerShell-paraméterei hogyan jelennek meg a rendszerben. 

| PowerShell-paraméter    | Description  |
|-------------------------|------------------|
| Instance ID             | Minden vezérlő egyedi azonosítóval vagy a hozzá társított GUID azonosítóval rendelkezik.|
| Name                    | Az eszköz rövid neve, amelyet az eszköz telepítése során a Azure Portal konfigurál. Az alapértelmezett felhasználóbarát név az eszköz sorozatszáma. |
| Modellezés                   | A StorSimple 8000 sorozatú eszköz modellje. A modell 8100 vagy 8600 lehet.|
| Sorozatszám            | Az eszköz sorozatszáma a gyárban van hozzárendelve, és 15 karakter hosszú. Például: 8600-SHX0991003G44HT azt jelzi, hogy:<br> 8600 – az eszköz modellje.<br>SHX – a gyártási hely.<br> 0991003 – egy adott termék. <br> G44HT – az utolsó 5 számjegy növekszik az egyedi sorozatszámok létrehozásához. Ez nem lehet szekvenciális készlet.|
| TimeZone                | Az eszköz üzembe helyezése során a Azure Portal konfigurált eszköz időzónája.|
| CurrentController       | Az a vezérlő, amelyhez a StorSimple-eszköz Windows PowerShell-felületén keresztül csatlakozik.|
| ActiveController        | Az eszközön aktív vezérlő, amely az összes hálózati és lemezes műveletet vezérli. Ez lehet 0. vezérlő vagy 1. vezérlő.  |
| Controller0Status       | A vezérlő 0 állapota az eszközön. A vezérlő állapota lehet normális, helyreállítási módban, vagy nem érhető el.|
| Controller1Status       | A vezérlő 1 állapota az eszközön.  A vezérlő állapota lehet normális, helyreállítási módban, vagy nem érhető el.|
| SystemMode              | A StorSimple-eszköz általános állapota. Az eszköz állapota lehet normál, karbantartás vagy leszerelt (a Azure Portal inaktiváltnak felel meg).|
| FriendlySoftwareVersion | Az eszköz szoftverének verziójának megfelelő felhasználóbarát karakterlánc. A 4. frissítést futtató rendszerek esetében a felhasználóbarát szoftververzió a StorSimple 8000 Series Update 4,0.|
| HcsSoftwareVersion      | Az eszközön futó HCS FRISSÍTŐÜGYNÖK szoftver verziója. Például a StorSimple 8000 Series Update 4,0-es verziójának megfelelő HCS FRISSÍTŐÜGYNÖK-szoftververzió a 6.3.9600.17820. |
| ApiVersion              | A HCS FRISSÍTŐÜGYNÖK eszköz Windows PowerShell API-verziója.|
| VhdVersion              | A gyári rendszerkép szoftverének verziója, amelyet az eszköz a szolgáltatással szállított. Ha alaphelyzetbe állítja az eszközt a gyári beállításokra, akkor futtatja ezt a verziót.|
| OSVersion               | Az eszközön futó Windows Server operációs rendszer szoftveres verziója. A StorSimple-eszköz a 6.3.9600-nek megfelelő Windows Server 2012 R2-alapú.|
| CisAgentVersion         | A StorSimple-eszközön futó CIS-ügynök verziója. Ez az ügynök segít kommunikálni az Azure-ban futó StorSimple Manager szolgáltatással.|
| MdsAgentVersion         | A StorSimple-eszközön futó MDS-ügynöknek megfelelő verzió. Ez az ügynök áthelyezi az adatait a monitoring and Diagnostics szolgáltatásba (MDS).|
| Lsisas2Version          | A StorSimple-eszköz LSI-illesztőprogramjainak megfelelő verzió.|
| Kapacitás                | Az eszköz teljes kapacitása bájtban megadva.|
| RemoteManagementMode    | Azt jelzi, hogy az eszköz távolról felügyelhető-e Windows PowerShell-felületén keresztül. |
| FipsMode                | Azt jelzi, hogy engedélyezve van-e a Egyesült Államok Federal Information Processing standard (FIPS) mód az eszközön. Az FIPS 140 szabvány a bizalmas adatok védelméhez jóváhagyott titkosítási algoritmusokat határoz meg az USA szövetségi kormányzati számítógépes rendszereinek használatára. A 4-es vagy újabb frissítést futtató eszközök esetében a FIPS mód alapértelmezés szerint engedélyezve van. |

## <a name="next-steps"></a>Következő lépések

* A [Invoke-HcsDiagnostics parancsmag szintaxisának](/previous-versions/windows/powershell-scripting/mt795371(v=wps.630))megismerése.

* További információ a StorSimple-eszköz [üzembe helyezési problémáinak elhárításáról](./storsimple-8000-troubleshoot-deployment.md) .