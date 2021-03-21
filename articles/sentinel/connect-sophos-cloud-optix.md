---
title: A Sophos Cloud Optix-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogy a Sophos Cloud Optix-összekötővel hogyan hívhat le <PRODUCT NAME> naplókat az Azure Sentinel szolgáltatásba. Megtekintheti <PRODUCT NAME> a munkafüzetekben tárolt adatnézeteket, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700870"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>A Sophos Cloud Optix összekötése az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Sophos Cloud Optix-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A Sophos Cloud Optix Connector segítségével könnyedén csatlakoztathatja az összes Sophos Cloud Optix biztonsági megoldás naplóit az Azure Sentinelhez, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot.  Ez a funkció nagyobb betekintést nyújt a szervezet Felhőbeli biztonsági és megfelelőségi állapotára, és javítja a Felhőbeli biztonsági üzemeltetési képességeket. A Sophos Cloud Optix és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-sophos-cloud-optix"></a>A Sophos Cloud Optix konfigurálása és összekapcsolása

A Sophos Cloud Optix képes a naplók közvetlen integrálására és exportálására az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** lehetőségre, és válassza a **Sophos Cloud Optix (előzetes verzió)** lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Másolja és mentse a **munkaterület-azonosítót** és az **elsődleges kulcsot** az összekötő lapról.

1. Kövesse a Sophos utasításait a [Microsoft Azure Sentinel integrálásához](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (a harmadik lépéstől kezdve).

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplókban** az **egyéni naplók** szakaszban, a *SophosCloudOptix_CL* táblában.

A Sophos Cloud Optix-alapú lekérdezések lekérdezéséhez írja be `SophosCloudOptix_CL` a következőt a lekérdezési ablakba:. Néhány hasznos lekérdezési minta esetén tekintse meg az összekötő oldal **következő lépések** lapját és a [Sophos dokumentációját](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html).

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Sophos Cloud Optix az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
