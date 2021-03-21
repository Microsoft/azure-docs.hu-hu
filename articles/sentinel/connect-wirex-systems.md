---
title: WireX Network kriminalisztikai platform (NFP) összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a WireX Systems NFP adatösszekötőt a WireX-NFP naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek WireX NFP, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.openlocfilehash: 9029b945eabd05b34306393b513e26ee9c1563f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541571"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>A WireX Network kriminalisztikai platform (NFP) berendezésének összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> A WireX Systems NFP-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a WireX Systems Network kriminalisztikai platform (NFP) berendezés az Azure Sentinelhez. A WireX NFP-adatösszekötővel könnyedén csatlakoztathatja a NFP-naplókat az Azure Sentinel szolgáltatással, így megtekintheti a munkafüzetek adatait, felhasználhatja egyéni riasztások létrehozására, és a vizsgálat javítására is felépítheti. 

> [!NOTE] 
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>WireX NFP-naplók küldése az Azure Sentinelnek

A naplók Azure Sentinelbe való beszerzéséhez konfigurálja a WireX Systems NFP készüléket, hogy CEF formátumban küldjön syslog-üzeneteket egy Linux-alapú naplózási kiszolgálónak (amely a rsyslog vagy a syslog-ng rendszert futtatja). Ezen a kiszolgálón telepítve lesz a Log Analytics ügynök, és az ügynök továbbítja a naplókat az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a **WireX hálózati kriminalisztikai platform (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **utasítások** lapon a **konfiguráció**:

    1. **1. Linux syslog-ügynök konfigurációja** – ezt a lépést akkor hajtsa végre, ha még nem fut a naplózási továbbító, vagy ha még egy másikra van szüksége. További részletekért és magyarázatért tekintse meg az [1. lépés: a naplózási továbbító üzembe helyezése](connect-cef-agent.md) az Azure Sentinel dokumentációjában.

    1. **2. a Common Event Format (CEF) naplóinak továbbítása a syslog-ügynökhöz** – a WireX NFP-megoldás megfelelő konfigurálásához lépjen kapcsolatba az [WireX-támogatással](https://wirexsystems.com/contact-us/) . Ennek a konfigurációnak a következő elemeket kell tartalmaznia:
        - Napló célhelye – a naplófájl-továbbító kiszolgáló állomásneve és/vagy IP-címe
        - Protokoll és port – TCP 514 (ha ez nem ajánlott, ne feledje, hogy a párhuzamos módosítást a naplófájl-továbbító kiszolgálón a syslog démonon végezze el)
        - Napló formátuma – CEF
        - Naplók típusai – a WireX által javasolt összes

    1. **3. a csatlakozás** ellenőrzése – ellenőrizze az adatok betöltését úgy, hogy átmásolja a parancsot az összekötő lapra, és futtatja a napló-továbbítón. Részletesebb útmutatást és magyarázatot a [3. lépés: a kapcsolat ellenőrzése](connect-cef-verify.md) az Azure Sentinel dokumentációjában talál.

        Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

Log Analytics WireX NFP-adatlekérdezéséhez másolja a következőt a lekérdezési ablakba, és alkalmazzon más szűrőket a választott módon:

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

További lekérdezési mintákért tekintse meg az összekötő oldal **következő lépések** lapját.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a WireX Systems NFP az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.