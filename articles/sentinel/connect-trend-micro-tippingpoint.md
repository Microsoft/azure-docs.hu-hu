---
title: A Trend Micro TippingPoint és az Azure Sentinel összekötése | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Trend Micro TippingPoint adatösszekötőt a TippingPoint SMS-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek TippingPoint, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 5c7491a0e0ba2a3bf604988c613e1fd8937f277d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752171"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>A Trend Micro TippingPoint-megoldás összekapcsolhatók az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Trend Micro TippingPoint-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Trend Micro TippingPoint Threat Protection rendszer-megoldás az Azure Sentinelhez. A Trend Micro TippingPoint adatösszekötővel könnyedén csatlakoztathatja a TippingPoint Security Management System (SMS) naplóit az Azure Sentinel szolgáltatással, így megtekintheti az adatokat a munkafüzetekben, felhasználhatja egyéni riasztások létrehozásához és a vizsgálat javítására.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Trend Micro TippingPoint-naplók küldése az Azure Sentinelnek

A naplók Azure Sentinelbe való beszerzéséhez konfigurálja a TippingPoint TPS-megoldást, hogy CEF formátumban küldjön syslog-üzeneteket egy Linux-alapú naplózási kiszolgálónak (amely a rsyslog vagy a syslog-ng rendszert futtatja). Ezen a kiszolgálón telepítve lesz a Log Analytics ügynök, és az ügynök továbbítja a naplókat az Azure Sentinel-munkaterületre. Az összekötő elemző függvényt használ a kapott adat normalizált sémába való átalakításához. 

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a **Trend Micro TippingPoint (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **utasítások** lapon a **konfiguráció**:

    1. **1. Linux syslog-ügynök konfigurációja** – ezt a lépést akkor hajtsa végre, ha még nem fut a naplózási továbbító, vagy ha még egy másikra van szüksége. További részletekért és magyarázatért tekintse meg az [1. lépés: a naplózási továbbító üzembe helyezése](connect-cef-agent.md) az Azure Sentinel dokumentációjában.

    1. A **2. résznél. Trend Micro TippingPoint SMS-naplók továbbítása a syslog-ügynöknek** – ennek a konfigurációnak a következő elemeket kell tartalmaznia:
        - Napló célhelye – a naplófájl-továbbító kiszolgáló állomásneve és/vagy IP-címe
        - Protokoll és port – **TCP 514** (ha ez nem ajánlott, ne feledje, hogy a párhuzamos módosítást a naplófájl-továbbító kiszolgálón a syslog démonon végezze el)
        - Napló formátuma – **ARCSIGHT CEF Format v 4.2**
        - Naplózási típusok – az összes elérhető

    1. **3. alatt. Csatlakozás ellenőrzése** – az adatok betöltésének ellenőrzéséhez másolja a parancsot az összekötő lapra, és futtassa azt a napló-továbbítón. Részletesebb útmutatást és magyarázatot a [3. lépés: a kapcsolat ellenőrzése](connect-cef-verify.md) az Azure Sentinel dokumentációjában talál.

        Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

A Trend Micro TippingPoint-adatokat Log Analytics-ben való beszerzéséhez le kell kérdezni az elemző függvényt a tábla helyett. Másolja a következőt a lekérdezési ablakba, és alkalmazzon más szűrőket a választott módon:

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

További lekérdezési mintákért tekintse meg az összekötő oldal **következő lépések** lapját.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Trend Micro TippingPoint az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
