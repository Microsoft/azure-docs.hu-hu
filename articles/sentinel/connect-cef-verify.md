---
title: Az Azure Sentinel kapcsolatának ellenőrzése | Microsoft Docs
description: Ellenőrizze a biztonsági megoldás kapcsolatát, és győződjön meg arról, hogy a CEF üzeneteket továbbítják az Azure Sentinelnek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: f9fb1c917a0719cb9d250b997329d3415b5872eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98747474"
---
# <a name="step-3-validate-connectivity"></a>3. lépés: a kapcsolat ellenőrzése

Miután telepítette a napló-továbbítót (az 1. lépésben), és úgy konfigurálta a biztonsági megoldást, hogy CEF üzeneteket küldjön (a 2. lépésben), kövesse ezeket az utasításokat a biztonsági megoldás és az Azure Sentinel közötti kapcsolat ellenőrzéséhez. 

## <a name="prerequisites"></a>Előfeltételek

- Emelt szintű engedélyekkel (sudo) kell rendelkeznie a log továbbító számítógépén.

- A naplózási továbbító számítógépén telepítve kell lennie a **python 2,7** vagy a **3** verziónak.<br>
A parancs használatával keresse meg a következőt: `python –version` .

- A folyamat egy pontján szükség lehet a munkaterület-AZONOSÍTÓra és a munkaterület elsődleges kulcsára. Ezeket a munkaterület-erőforrásban találja, az **ügynökök kezelése** területen.

## <a name="how-to-validate-connectivity"></a>A kapcsolat érvényesítése

1. Az Azure Sentinel navigációs menüjében nyissa meg a **naplók** elemet. Futtasson egy lekérdezést a **CommonSecurityLog** sémával, és ellenőrizze, hogy a biztonsági megoldásból kapott-e naplókat.<br>
Vegye figyelembe, hogy körülbelül 20 percet vesz igénybe, amíg a naplók meg nem jelennek a **log Analyticsban**. 

1. Ha nem látja az eredményeket a lekérdezésből, ellenőrizze, hogy az események a biztonsági megoldásból származnak-e, vagy próbáljon meg generálni, és ellenőrizze, hogy továbbítva vannak-e a kijelölt syslog-továbbító gépnek. 

1. Futtassa a következő szkriptet a log továbbítón (a munkaterület AZONOSÍTÓjának alkalmazása a helyőrző helyett) a biztonsági megoldás, a naplózási továbbító és az Azure Sentinel közötti kapcsolat vizsgálatához. Ez a szkript ellenőrzi, hogy a démon figyeli-e a megfelelő portokat, hogy a továbbítás megfelelően van-e konfigurálva, és hogy semmi sem blokkolja a démon és a Log Analytics ügynök közötti kommunikációt. Emellett a "TestCommonEventFormat" nevű ál-üzeneteket is elküldi a végpontok közötti kapcsolat vizsgálatához. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Előfordulhat, hogy egy olyan üzenetet kap, amely egy parancs futtatásával kijavít egy problémát a ***számítógép* mező leképezésével**. A részletekért tekintse [meg az érvényesítési parancsfájl magyarázatát](#mapping-command) .

    - Előfordulhat, hogy egy olyan üzenetet kap, amely egy parancs futtatásával orvosolja a **Cisco ASA-tűzfalak naplófájljainak elemzésével** kapcsolatos problémát. A részletekért tekintse [meg az érvényesítési parancsfájl magyarázatát](#parsing-command) .

## <a name="validation-script-explained"></a>Érvényesítési parancsfájl magyarázata

Az érvényesítési parancsfájl a következő ellenőrzéseket hajtja végre:

# <a name="rsyslog-daemon"></a>[rsyslog démon](#tab/rsyslog)

1. Ellenőrzi, hogy a fájl<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    létezik és érvényes.

1. Ellenőrzi, hogy a fájl tartalmazza-e a következő szöveget:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Ellenőrzi, hogy a Cisco ASA-tűzfal eseményeinek elemzése a várt módon van-e konfigurálva a következő parancs használatával: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Ha probléma merül fel az elemzés során, a parancsfájl hibaüzenetet küld, hogy **manuálisan futtassa a következő parancsot** (a munkaterület azonosítójának alkalmazása a helyőrző helyett). A parancs gondoskodik a megfelelő elemzésről, majd újraindítja az ügynököt.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Ellenőrzi, hogy a syslog forrás *számítógép* mezőjének megfelelően van-e leképezve a log Analytics ügynökben a következő parancs használatával: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Ha probléma merül fel a leképezéssel kapcsolatban, a parancsfájl egy hibaüzenetet jelenít meg a **következő parancs manuális futtatásához** (a munkaterület azonosítójának alkalmazása a helyőrző helyett). A parancs biztosítja a megfelelő leképezést, majd újraindítja az ügynököt.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Ellenőrzi, hogy vannak-e olyan biztonsági fejlesztések a gépen, amelyek blokkolják a hálózati forgalmat (például a gazda tűzfalat).

1. Ellenőrzi, hogy a syslog démon (rsyslog) megfelelően van-e konfigurálva ahhoz, hogy üzeneteket küldjön (amelyet CEF azonosít) a 25226-es TCP-porton található Log Analytics ügynöknek:

    - Konfigurációs fájl: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Újraindítja a syslog démont és a Log Analytics ügynököt:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy a szükséges kapcsolatok létrejöttek-e: TCP 514 az adatfogadáshoz, TCP 25226 a syslog démon és a Log Analytics ügynök közötti belső kommunikációhoz:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Ellenőrzi, hogy a syslog démon fogad-e az 514-es porton tárolt adatfogadást, és hogy az ügynök fogad-e adatfogadást a 25226-es

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. A rendszer a (z) 514-as porton küldi el a modell adatokat Az alábbi lekérdezés futtatásával meg kell jelennie az Azure Sentinel munkaterületen:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng démon](#tab/syslogng)

1. Ellenőrzi, hogy a fájl<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    létezik és érvényes.

1. Ellenőrzi, hogy a fájl tartalmazza-e a következő szöveget:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Ellenőrzi, hogy a Cisco ASA-tűzfal eseményeinek elemzése a várt módon van-e konfigurálva a következő parancs használatával: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Ha probléma merül fel az elemzés során, a parancsfájl hibaüzenetet küld, hogy **manuálisan futtassa a következő parancsot** (a munkaterület azonosítójának alkalmazása a helyőrző helyett). A parancs gondoskodik a megfelelő elemzésről, majd újraindítja az ügynököt.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Ellenőrzi, hogy a syslog forrás *számítógép* mezőjének megfelelően van-e leképezve a log Analytics ügynökben a következő parancs használatával: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Ha probléma merül fel a leképezéssel kapcsolatban, a parancsfájl egy hibaüzenetet jelenít meg a **következő parancs manuális futtatásához** (a munkaterület azonosítójának alkalmazása a helyőrző helyett). A parancs biztosítja a megfelelő leképezést, majd újraindítja az ügynököt.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Ellenőrzi, hogy vannak-e olyan biztonsági fejlesztések a gépen, amelyek blokkolják a hálózati forgalmat (például a gazda tűzfalat).

1. Ellenőrzi, hogy a syslog démon (syslog-ng) megfelelően van-e konfigurálva ahhoz, hogy üzeneteket küldjön a CEF (regex használatával) az Log Analytics ügynöknek a 25226-es TCP-porton:

    - Konfigurációs fájl: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Újraindítja a syslog démont és a Log Analytics ügynököt:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy a szükséges kapcsolatok létrejöttek-e: TCP 514 az adatfogadáshoz, TCP 25226 a syslog démon és a Log Analytics ügynök közötti belső kommunikációhoz:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Ellenőrzi, hogy a syslog démon fogad-e az 514-es porton tárolt adatfogadást, és hogy az ügynök fogad-e adatfogadást a 25226-es

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. A rendszer a (z) 514-as porton küldi el a modell adatokat Az alábbi lekérdezés futtatásával meg kell jelennie az Azure Sentinel munkaterületen:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
