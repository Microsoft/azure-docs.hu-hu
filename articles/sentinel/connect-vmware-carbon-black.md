---
title: A VMware Carbon Black Cloud Endpoint standard-információinak összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatók a VMware Carbon Black Cloud Endpoint standard-adatkészletei az Azure Sentinel szolgáltatáshoz.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 2c26931db617109c36c19b73bd3ec9263f4b40cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096446"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>A VMware Carbon Black Cloud Endpoint standard összekötése az Azure Sentinel és az Azure Function használatával

> [!IMPORTANT]
> A VMware Carbon Black Cloud Endpoint standard adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A VMware Carbon Black Cloud Endpoint standard Connector segítségével könnyedén csatlakoztathatja az összes [VMware Carbon Black Endpoint standard](https://www.carbonblack.com/products/endpoint-standard/) biztonsági megoldás naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A VMware Carbon Black és az Azure Sentinel közötti integráció a Azure Functions használatával kéri le a naplózási adataikat a REST API használatával.


> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>A VMware Carbon Black Cloud Endpoint standard konfigurálása és összekapcsolása

A Azure Functions az eseményeket és naplókat közvetlenül a VMware Carbon Black Cloud Endpoint standardból integrálhatja és lehívhatja, és továbbíthatja őket az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** lehetőségre, és válassza a **VMware Carbon Black** Connector elemet.
2. Válassza az **összekötő megnyitása lapot**.
3. Kövesse a **VMware Carbon Black** oldalon megjelenő utasításokat.


## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **CarbonBlackAuditLogs_CL**, a **CarbonBlackNotifications_CL** és a * * * * CarbonBlackEvents_CL * * * * tábla alatt log Analytics jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése
Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a VMware Carbon Black Cloud Endpoint standard az Azure Sentinelhez az Azure Function Apps használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

