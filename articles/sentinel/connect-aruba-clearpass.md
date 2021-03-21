---
title: Aruba ClearPass-adatkapcsolatának összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogy az Aruba ClearPass-összekötő segítségével hogyan hívhatja le az Aruba ClearPass-naplókat az Azure Sentinelbe. Tekintse meg az Aruba ClearPass-adatkészleteket a munkafüzetekben, hozzon létre riasztásokat, és javítsa a vizsgálatot.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 1b03e2d8715b3d80425ef181dd908978d5df4cf2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745704"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Az Aruba ClearPass és az Azure Sentinel összekötése

> [!IMPORTANT]
> Az Aruba ClearPass-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható az Aruba ClearPass-berendezés az Azure Sentinelhez. Az Aruba ClearPass-adatösszekötővel egyszerűen csatlakoztathatja az Aruba ClearPass-naplóit az Azure Sentinel használatával, így megtekintheti a munkafüzetek adatait, lekérdezheti az egyéni riasztásokat, és beépítheti azt a vizsgálat javítására. Az Aruba ClearPass és az Azure Sentinel közötti integráció a CEF-formázott syslog, a Linux-alapú naplózási továbbító és a Log Analytics ügynök használatát teszi lehetővé. Egy Kusto függvényen alapuló, egyéni kialakítású naplózási elemzőt is használ.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Aruba ClearPass-naplók küldése az Azure Sentinelnek

A naplók Azure Sentinelbe való beszerzéséhez konfigurálja az Aruba ClearPass-berendezését, hogy a syslog-üzeneteket CEF formátumban küldje egy Linux-alapú naplózási kiszolgálónak (amelyen rsyslog vagy syslog-ng fut). Ezen a kiszolgálón telepítve lesz a Log Analytics ügynök, és az ügynök továbbítja a naplókat az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza az **Aruba ClearPass (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **utasítások** lapon a **konfiguráció**:

    1. **1. Linux syslog-ügynök konfigurációja** – ezt a lépést akkor hajtsa végre, ha még nem fut a naplózási továbbító, vagy ha még egy másikra van szüksége. További részletekért és magyarázatért tekintse meg az [1. lépés: a naplózási továbbító üzembe helyezése](connect-cef-agent.md) az Azure Sentinel dokumentációjában.

    1. A **2. résznél. Az Aruba ClearPass-naplók továbbítása syslog-ügynökhöz** – kövesse az Aruba utasításait a [ClearPass konfigurálásához](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Ennek a konfigurációnak a következő elemeket kell tartalmaznia:
        - Napló célhelye – a naplófájl-továbbító kiszolgáló állomásneve és/vagy IP-címe
        - Protokoll és port – TCP 514 (ha ez nem ajánlott, ne feledje, hogy a párhuzamos módosítást a naplófájl-továbbító kiszolgálón a syslog démonon végezze el)
        - Napló formátuma – CEF
        - Naplózási típusok – az összes elérhető vagy minden megfelelő

    1. **3. alatt. Csatlakozás ellenőrzése** – az adatok betöltésének ellenőrzéséhez másolja a parancsot az összekötő lapra, és futtassa azt a napló-továbbítón. Részletesebb útmutatást és magyarázatot a [3. lépés: a kapcsolat ellenőrzése](connect-cef-verify.md) az Azure Sentinel dokumentációjában talál.

        Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

Ez az adatösszekötő egy Kusto függvény alapján egy elemzőtől függ, hogy a várt módon működjön-e. [Kövesse az alábbi lépéseket](https://aka.ms/sentinel-arubaclearpass-parser) a **ArubaClearPass** Kusto függvény alias létrehozásához.

Ezután az Aruba ClearPass-adatLog Analytics-beli lekérdezéséhez írja be a `ArubaClearPass` parancsot a lekérdezési ablak elejére.

Néhány hasznos lekérdezési minta a Connector lap **következő lépések** lapján található.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Aruba ClearPass az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
