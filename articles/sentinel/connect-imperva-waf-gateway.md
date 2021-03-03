---
title: A inperverz WAF Gateway berendezés összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a nem perverz WAF Gateway-összekötőt a WAF-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti az inperverz WAF-adatlemezeket, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698433"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>A inperverz WAF-átjáró berendezésének összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> Az inperverz WAF Gateway Connector jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a inperverz WAF Gateway készülék az Azure Sentinelhez. A inperverz WAF Gateway adatösszekötővel könnyedén csatlakoztathatja a WAF-átjáró naplóit az Azure Sentinel használatával, így megtekintheti az adatait a munkafüzetekben, felhasználhatja egyéni riasztások létrehozására, és beépítheti azt a vizsgálat javítására. A inperverz WAF Gateway és az Azure Sentinel közötti integráció a CEF-formázott syslog, a Linux-alapú naplózási továbbító és a Log Analytics ügynök használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Inperverza WAF-átjáró naplófájljainak küldése az Azure Sentinelnek

A naplók Azure Sentinelbe való beszerzéséhez konfigurálja a nem perverz WAF Gateway készüléket, hogy CEF formátumban küldjön syslog-üzeneteket egy Linux-alapú naplózási kiszolgálónak (rsyslog vagy syslog-ng futtatásával). Ezen a kiszolgálón telepítve lesz a Log Analytics ügynök, és az ügynök továbbítja a naplókat az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a következőt: **inperverz WAF Gateway (előzetes verzió)**, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **utasítások** lapon a **konfiguráció**:

    1. **1. Linux syslog-ügynök konfigurációja** – ezt a lépést akkor hajtsa végre, ha még nem fut a naplózási továbbító, vagy ha még egy másikra van szüksége. További részletekért és magyarázatért tekintse meg az [1. lépés: a naplózási továbbító üzembe helyezése](connect-cef-agent.md) az Azure Sentinel dokumentációjában.

    1. A **2. résznél. Common Event Format-(CEF-) naplók továbbítása a syslog-ügynökhöz** – ehhez az összekötőhöz egy **műveleti felület** **szükséges, amelyet** a **SecureSphere MX** felügyeleti konzolon kell létrehozni. Kövesse a következő témakört: a nem [PERVERZ WAF Gateway riasztási naplózásának engedélyezése az Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)szolgáltatásban. Ennek a konfigurációnak a következő elemeket kell tartalmaznia:
        - Napló célhelye – a naplófájl-továbbító kiszolgáló állomásneve és/vagy IP-címe
        - Protokoll és port – TCP 514
        - Napló formátuma – CEF
        - Naplózási típusok – az összes elérhető

    1. **3. alatt. Csatlakozás ellenőrzése** – az adatok betöltésének ellenőrzéséhez másolja a parancsot az összekötő lapra, és futtassa azt a napló-továbbítón. Részletesebb útmutatást és magyarázatot a [3. lépés: a kapcsolat ellenőrzése](connect-cef-verify.md) az Azure Sentinel dokumentációjában talál.

        Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

Ha Log Analytics szeretné lekérdezni a inperverz WAF-átjárót, másolja a következőt a lekérdezési ablakba, és alkalmazzon más szűrőket a választott módon:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

További hasznos lekérdezési példákért tekintse meg az összekötő oldal **következő lépések** lapját.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a inperverz WAF-átjáró az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.