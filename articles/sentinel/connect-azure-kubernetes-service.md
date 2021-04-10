---
title: Az Azure Kubernetes szolgáltatás (ak) diagnosztikai naplófájljainak összekötése az Azure Sentinel használatával
description: Útmutató az Azure Kubernetes Service Diagnostics-naplók Azure Sentinelhez való összekapcsolásához Azure Policy használatával.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507177"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Az Azure Kubernetes Service Diagnostics-naplók összekötése

Az Azure Kubernetes Service (ak) egy nyílt forráskódú, teljes körűen felügyelt Container-kezelő szolgáltatás, amely lehetővé teszi a Docker-tárolók és a tároló-alapú alkalmazások üzembe helyezését, méretezését és kezelését fürtözött környezetben.

Ez az összekötő lehetővé teszi, hogy az Azure Kubernetes szolgáltatás (ak) diagnosztikai naplóit az Azure Sentinelbe továbbítsa, így folyamatosan figyelheti a tevékenységeket az összes példányban. 

További információ az [Azure Kubernetes szolgáltatás monitorozásáról](../azure-monitor/containers/container-insights-overview.md) és az AK-beli [diagnosztikai telemetria](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Előfeltételek

AK-naplók beolvasása az Azure Sentinelbe:

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

- Ha a Azure Policy használatával szeretné alkalmazni a log streaming-szabályzatot az AK-beli erőforrásokra, a házirend-hozzárendelési hatókörhöz hozzá kell rendelnie a tulajdonosi szerepkört.

## <a name="connect-to-azure-kubernetes-service"></a>Kapcsolódás az Azure Kubernetes szolgáltatáshoz

Ez az összekötő a Azure Policy használatával egyetlen Azure Kubernetes szolgáltatásbeli naplózási konfigurációt alkalmaz a példányok egy gyűjteményére, amely hatókörként van meghatározva. Az Azure Kubernetes szolgáltatásból beolvasott naplózási típusok az összekötő lap bal oldalán, az **adattípusok** területen láthatók.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Válassza az **Azure Kubernetes szolgáltatás (ak)** lehetőséget az adatösszekötők katalógusában, majd válassza az **összekötő lap megnyitása** lehetőséget az előnézet ablaktáblán.

1. Az összekötő oldal **konfiguráció** szakaszában bontsa ki a **diagnosztikai naplók engedélyezése az Azure Kubernetes szolgáltatásban (ak)** elemet.

1. Jelölje be a **Azure Policy-hozzárendelés varázsló indítása** gombot.

    Megnyílik a szabályzat-hozzárendelés varázsló, amely készen áll az üzembe helyezés nevű új szabályzat létrehozására **– Az Azure Kubernetes szolgáltatás diagnosztikai beállításainak konfigurálása log Analytics munkaterületre**.

    1. Az **alapvető beállítások** lapon kattintson a három pontra a **hatókör** területen az előfizetés kiválasztásához (és opcionálisan egy erőforráscsoporthoz). Leírást is hozzáadhat a szolgáltatáshoz.

    1. A **Parameters (paraméterek** ) lapon válassza ki az Azure Sentinel-munkaterületet a **log Analytics munkaterület** legördülő listából. A fennmaradó legördülő mezők a rendelkezésre álló diagnosztikai naplók típusát jelölik. A betöltéshez használni kívánt összes naplózási típust "true"-ként hagyja.

    1. Ha alkalmazni szeretné a szabályzatot a meglévő erőforrásokon, válassza a **szervizelés** fület, és jelölje be a **szervizelési feladat létrehozása** jelölőnégyzetet.

    1. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás** gombra. A szabályzat most már hozzá van rendelve a kiválasztott hatókörhöz.

> [!NOTE]
>
> Ezzel az adatösszekötővel a kapcsolati állapot mutatói (az adatösszekötők katalógusában és az adattípusok neve melletti kapcsolati ikonokban lévő színcsíkok) csak akkor jelennek *meg, ha* az adatok betöltése az elmúlt két hétben egy bizonyos pontján történik. Ha két hét telt el az adatfeldolgozás nélkül, az összekötő leválasztva jelenik meg. Minél több információ érkezik, a *csatlakoztatott* állapot vissza fog térni.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Azure Kubernetes szolgáltatás az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
