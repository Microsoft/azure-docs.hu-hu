---
title: IT-szolgáltatásmenedzsmenti csatoló áttekintése
description: Ez a cikk a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) áttekintését tartalmazza.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93b6160b495b609e23e10b3f709d130792067423
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803782"
---
# <a name="it-service-management-connector-overview"></a>IT-szolgáltatásmenedzsmenti csatoló áttekintése

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT-szolgáltatásmenedzsmenti csatoló (ITSMC) lehetővé teszi, hogy az Azure-t egy támogatott informatikai szolgáltatás-felügyeleti (ITSM) termékhez vagy szolgáltatáshoz kapcsolódjon.

Az Azure-szolgáltatások, például az Azure Log Analytics és Azure Monitor eszközöket biztosítanak az Azure-beli és nem Azure-beli erőforrásokkal kapcsolatos problémák észleléséhez, elemzéséhez és hibaelhárításához. A problémához kapcsolódó munkaelemek azonban általában egy ITSM-termékben vagy-szolgáltatásban találhatók. A ITSMC kétirányú kapcsolatot biztosít az Azure és a ITSM eszközök között, így gyorsabban megoldhatja a problémákat.

## <a name="configuration-steps"></a>Konfigurációs lépések

A ITSMC a következő ITSM-eszközökkel támogatja a kapcsolatokat:

-   ServiceNow
-   System Center Service Manager
-   Megjelenő
-   Cherwell

   >[!NOTE]
> Javasoljuk, hogy a Cherwell és a felkínált ügyfeleinknek a [webhook műveleteit](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) a Cherwell és az Előa végpontot használják egy másik megoldásként az integrációhoz.

A ITSMC a következőket teheti:

-  Munkaelemeket hozhat létre a ITSM eszközben az Azure-riasztások (metrikus riasztások, műveletnapló-riasztások és Log Analytics riasztások) alapján.
-  Lehetőség van arra is, hogy szinkronizálja az incidenst, és módosítsa a ITSM eszközről az Azure Log Analytics-munkaterületre.

A jogi feltételekkel és az adatvédelmi szabályzattal kapcsolatos információkért lásd: a [Microsoft adatvédelmi nyilatkozata](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Az alábbi lépések végrehajtásával kezdheti meg a ITSMC használatát:

1. [A ITSM-környezet beállítása az Azure-beli riasztások fogadására.](./itsmc-connections.md)
1. [Az Azure ITSM-megoldás konfigurálása](./itsmc-definition.md#add-it-service-management-connector)
1. [Konfigurálja az Azure ITSM-összekötőt a ITSM-környezethez.](./itsmc-definition.md#create-an-itsm-connection)
1. [A műveleti csoport konfigurálása a ITSM-összekötő kihasználása érdekében.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>További lépések

* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)