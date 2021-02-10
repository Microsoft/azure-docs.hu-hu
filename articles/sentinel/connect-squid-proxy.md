---
title: A squid-proxybeállítások összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Squid proxy adatösszekötőt a Squid proxy-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a squid-proxyk a munkafüzetekben tárolt szolgáltatásait, riasztásokat hozhat létre, és javíthatja a vizsgálatot
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090445"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>A Squid proxy csatlakozása az Azure Sentinelhez

> [!IMPORTANT]
> A Squid proxy-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Squid proxy berendezés az Azure Sentinelhez. A Squid proxy adatösszekötővel könnyedén csatlakoztathatja a squid-naplókat az Azure Sentinel használatával, így megtekintheti a munkafüzetek adatait, felhasználhatja egyéni riasztások létrehozására, és a vizsgálat javítására is felépítheti azt. A Squid proxy és az Azure Sentinel közötti integráció a Log Analytics ügynök általi helyi fájlok feldolgozását teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>A Squid-proxy naplófájljainak továbbítása a Log Analytics ügynöknek  

Konfigurálja a squid proxyt a naplófájlok Azure-munkaterületre való küldéséhez a Log Analytics ügynök használatával.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a **Squid proxy (előzetes verzió)** összekötőt, majd **nyissa meg az összekötő lapot**.

1. Kövesse a **Squid proxy** -összekötő oldalának utasításait:

    1. A Linux-ügynök telepítése és előkészítése

        - Válasszon egy Azure Linux rendszerű virtuális gépet vagy egy nem Azure-beli Linux-gépet (fizikai vagy virtuális).

    1. A gyűjteni kívánt naplók konfigurálása

        - A munkaterület speciális beállításainál vegyen fel egy egyéni napló típusát, töltsön fel egy mintát, és állítsa be utasításként.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplókban**, az **egyéni naplók** területen jelenik meg a `SquidProxy_CL` táblázatban.

Néhány hasznos minta lekérdezéshez tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Squid proxy az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
