---
title: A Akamai Security Events Collector összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Akamai Security Events Connectort a Akamai-megoldások biztonsági naplóinak Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek Akamai, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711960"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>A Akamai Security Events Collector összekötése az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Akamai biztonsági eseményeinek összekötője jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Akamai Security Events Collector az Azure Sentinel szolgáltatáshoz. A Akamai biztonsági események adatösszekötője lehetővé teszi a Akamai-naplók egyszerű összekapcsolását az Azure Sentinel használatával, így megtekintheti a munkafüzetek adatait, lekérdezheti az egyéni riasztásokat, és beépítheti azt a vizsgálat javítására. A Akamai Security Events Collector és az Azure Sentinel közötti integráció a CEF-formázott syslog, a Linux-alapú naplózási továbbító és a Log Analytics ügynök használatát teszi lehetővé. Egy Kusto függvényen alapuló, egyéni kialakítású naplózási elemzőt is használ.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Akamai biztonsági események naplófájljainak elküldése az Azure Sentinelbe

A naplók Azure Sentinelbe való beszerzéséhez konfigurálja a Akamai Security Events Collectort, hogy CEF formátumban küldjön syslog-üzeneteket egy Linux-alapú naplózási kiszolgálónak (rsyslog vagy syslog-ng futtatásával). Ezen a kiszolgálón telepítve lesz a Log Analytics ügynök, és az ügynök továbbítja a naplókat az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a **Akamai biztonsági események (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **utasítások** lapon a **konfiguráció**:

    1. **1. Linux syslog-ügynök konfigurációja** – ezt a lépést akkor hajtsa végre, ha még nem fut a naplózási továbbító, vagy ha még egy másikra van szüksége. Lásd [: 1. lépés: a naplózási továbbító üzembe helyezése](connect-cef-agent.md) az Azure Sentinel dokumentációjában a méretezési információk, részletesebb útmutatás és részletes magyarázat.

    1. A **2. résznél. Common Event Format (CEF) naplók továbbítása a syslog-ügynökhöz** – kövesse a Akamai utasításait az [Siem-integráció konfigurálásához](https://developer.akamai.com/tools/integrations/siem) és [egy CEF-összekötő beállításához](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Ez az összekötő a SIEM OPEN API segítségével közel valós időben fogadja el a Akamai-megoldások biztonsági eseményeit, és a JSON-ból CEF formátumba konvertálja őket.
    
        Ennek a konfigurációnak a következő elemeket kell tartalmaznia:
    
        - Napló célhelye – a naplófájl-továbbító kiszolgáló állomásneve és/vagy IP-címe
        - Protokoll és port – TCP 514 (ha ez nem ajánlott, ne feledje, hogy a párhuzamos módosítást a naplófájl-továbbító kiszolgálón a syslog démonon végezze el)
        - Napló formátuma – CEF
        - Naplózási típusok – az összes elérhető

    1. **3. alatt. Csatlakozás ellenőrzése** – az adatok betöltésének ellenőrzéséhez másolja a parancsot az összekötő lapra, és futtassa azt a napló-továbbítón. Részletesebb útmutatást és magyarázatot a [3. lépés: a kapcsolat ellenőrzése](connect-cef-verify.md) az Azure Sentinel dokumentációjában talál.

        Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

Ez az adatösszekötő egy Kusto függvény alapján egy elemzőtől függ, hogy a várt módon működjön-e. A következő lépésekkel állíthatja be a **AkamaiSIEMEvent** Kusto függvényt a lekérdezésekben és a munkafüzetekben való használatra.

1. Az Azure Sentinel navigációs menüjében válassza a **naplók** lehetőséget.

1. Másolja a következő lekérdezést, és illessze be a lekérdezési ablakba.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Kattintson a **Save (Mentés** ) legördülő listára, és kattintson a **Save (Mentés**) gombra. A **Mentés** panelen

    1. A **név** mezőben adja meg a **AkamaiSIEMEvent**.

    1. A **Mentés másként** területen válassza a **függvény** lehetőséget.

    1. A **függvény aliasa** területen adja meg a **AkamaiSIEMEvent**.

    1. A **Kategória** mezőben adja meg a **függvények** értéket.

    1. Kattintson a **Mentés** gombra.

    A Function apps általában 10 – 15 percet vesz igénybe.

Most már készen áll arra, hogy lekérdezze a Akamai-adatokat a `AkamaiSIEMEvent` lekérdezési ablak felső sorába való beírásával.

További lekérdezési mintákért tekintse meg az összekötő oldal **következő lépések** lapját.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakozhat Akamai biztonsági eseményekhez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.