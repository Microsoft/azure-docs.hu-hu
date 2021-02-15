---
title: Az F5 BIG-IP-alapú adatkapcsolatok Azure Sentinelhez való összekötése | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az F5 BIG-IP-alapú adatmennyiségek az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100092791"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Az F5 BIG-IP berendezés összekötése 

Az F5 BIG-IP Connector segítségével könnyedén csatlakoztathatja az összes F5 BIG-IP-naplót az Azure Sentinel használatával, megtekintheti a munkafüzeteket, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Ez nagyobb betekintést nyújt a szervezet hálózatára, és javítja a biztonsági műveletek képességeit. Az F5 BIG-IP és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-f5-big-ip"></a>Az F5 BIG-IP konfigurálása és összekapcsolása 

Az F5 BIG-IP képes integrálni és exportálni a naplókat közvetlenül az Azure Sentinelbe.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, majd válassza az **F5 Big-IP** , majd az **összekötő lap megnyitása** lehetőséget. 
1. Az F5 BIG-IP összekapcsolásához JSON-deklarációt kell közzétennie a rendszer API-végpontján. Ennek módjával kapcsolatos útmutatásért lásd: [az F5 Big-IP integrálása az Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)használatával.
8. Az F5 BIG-IP Connector lapon másolja a munkaterület-azonosítót és az elsődleges kulcsot, és illessze be őket az [adatok Azure log Analyticsba való továbbítása](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)alatt.
1. Az F5 BIG-IP utasítások elvégzése után az Azure Sentinel Connector oldalán láthatja a csatlakoztatott adattípusokat.
1. Ha az F5 BIG-IP-eseményhez Log Analytics vonatkozó sémát szeretné használni, keresse meg **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL** és **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 



## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az F5 BIG-IP az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


