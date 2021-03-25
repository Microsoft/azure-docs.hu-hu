---
title: A Broadcom Symantec adatveszteség-megelőzési (DLP) adatvédelmek összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Broadcom Symantec DLP adatösszekötőt a Symantec DLP-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a Symantec DLP-beli adatfájljait a munkafüzetekben, riasztásokat hozhat létre, és növelheti a vizsgálatot.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 4be182b3eee59f7f9d2ef704a3d0f57c6718b45d
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044974"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>A Broadcom Symantec adatvesztés-megelőzési (DLP) szolgáltatásának összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> A Broadcom Symantec DLP-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Broadcom Symantec DLP-berendezés az Azure Sentinelhez. A Broadcom Symantec DLP adatösszekötővel könnyedén csatlakoztathatja a Symantec DLP-naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Ez a funkció nagyobb betekintést nyújt a szervezete adataiba, ahol utazik, és javítja a biztonsági műveletek képességeit. A Broadcom Symantec DLP és az Azure Sentinel közötti integráció a CEF-formázott syslog, a Linux-alapú naplózási továbbító és a Log Analytics ügynök használatát teszi lehetővé. Egy Kusto függvényen alapuló, egyéni kialakítású naplózási elemzőt is használ.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Broadcom Symantec DLP-naplók küldése az Azure Sentinelnek

A naplók Azure Sentinelbe való beszerzéséhez konfigurálja a Broadcom Symantec DLP-berendezését, hogy CEF formátumban küldjön syslog-üzeneteket egy Linux-alapú naplózási kiszolgálónak (amelyen rsyslog vagy syslog-ng fut). Ezen a kiszolgálón telepítve lesz a Log Analytics ügynök, és az ügynök továbbítja a naplókat az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a **Broadcom Symantec DLP (előzetes verzió)** összekötőt, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **utasítások** lapon a **konfiguráció**:

    1. **1. Linux syslog-ügynök konfigurációja** – ezt a lépést akkor hajtsa végre, ha még nem fut a naplózási továbbító, vagy ha még egy másikra van szüksége. További részletekért és magyarázatért tekintse meg az [1. lépés: a naplózási továbbító üzembe helyezése](connect-cef-agent.md) az Azure Sentinel dokumentációjában.

    1. A **2. résznél. Symantec DLP-naplók továbbítása syslog-ügynökre** – kövesse a Broadcom utasításait a [Symantec DLP konfigurálásához](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Ennek a konfigurációnak a következő elemeket kell tartalmaznia:
        - Napló célhelye – a naplófájl-továbbító kiszolgáló állomásneve és/vagy IP-címe
        - Protokoll és port – TCP 514 (ha ez nem ajánlott, ne feledje, hogy a párhuzamos módosítást a naplófájl-továbbító kiszolgálón a syslog démonon végezze el)
        - Napló formátuma – CEF
        - Naplózási típusok – az összes elérhető vagy minden megfelelő

    1. **3. alatt. Csatlakozás ellenőrzése** – az adatok betöltésének ellenőrzéséhez másolja a parancsot az összekötő lapra, és futtassa azt a napló-továbbítón. Részletesebb útmutatást és magyarázatot a [3. lépés: a kapcsolat ellenőrzése](connect-cef-verify.md) az Azure Sentinel dokumentációjában talál.

        Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

Ez az adatösszekötő egy Kusto függvény alapján egy elemzőtől függ, hogy a várt módon működjön-e. [Kövesse az alábbi lépéseket](https://aka.ms/sentinel-symantecdlp-parser) a **SymantecDLP** Kusto függvény alias létrehozásához.

Ezután a Log Analytics-ban található Broadcom Symantec DLP-beli adatlekérdezés lekéréséhez írja be a `SymantecDLP` parancsot a lekérdezési ablak elejére.

Néhány hasznos lekérdezési minta a Connector lap **következő lépések** lapján található.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Symantec DLP az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.