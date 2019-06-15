---
title: (ELAVULT) A sysdig segítségével egy Azure Container Service-fürt monitorozása
description: Azure tárolószolgáltatás-fürt megfigyelése a Sysdig segítségével.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 39cd5a3fe7db0c442608e7943e7cbd6e3198edfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509559"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>(ELAVULT) A sysdig segítségével egy Azure Container Service-fürt monitorozása

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben Sysdig-ügynököket telepítünk az Azure Container Service-fürt összes ügynökcsomópontjára. Ehhez a konfiguráláshoz Sysdig-fiókra van szüksége. 

## <a name="prerequisites"></a>Előfeltételek
[Helyezzen üzembe](container-service-deployment.md) és [csatlakoztasson](../container-service-connect.md) egy, az Azure Container Service által konfigurált fürtöt. Ismerkedjen meg a [Marathon felhasználói felülettel](container-service-mesos-marathon-ui.md). Lépjen a [ https://app.sysdigcloud.com ](https://app.sysdigcloud.com) Sysdig-felhőfiók beállításához. 

## <a name="sysdig"></a>Sysdig
A Sysdig egy olyan megfigyelő szolgáltatás, amelynek a segítségével megfigyelheti a fürtben található tárolókat. A Sysdig a hibaelhárításban nyújtott segítségről ismert, de a processzor, a hálózatkezelés, a memória és az I/O megfigyelésére alkalmas alapvető mérőszámokkal is rendelkezik. A Sysdig megkönnyíti annak áttekintését, hogy mely tárolók működnek a legtöbbet, és melyek használják leginkább a processzort, illetve a legtöbb memóriát. Ez a nézet az „Overview” (Áttekintés) részben található, amelynek jelenleg még csak a bétaverziója érhető el. 

![Sysdig felhasználói felület](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Üzemelő Sysdig-példány konfigurálása a Marathonnal
Ezek a lépések bemutatják, hogyan konfigurálhat és helyezhet üzembe Sysdig-alkalmazásokat a fürtön a Marathonnal. 

A DC/OS felhasználói felületen keresztüli eléréséhez [ http://localhost:80/ ](http://localhost:80/) egyszer a DC/OS felhasználói felületen navigáljon a "Universe", amely a bal alsó sarokban található, és keressen a "Sysdig" kifejezésre.

![Sysdig a DC/OS Universe rendszerben](./media/container-service-monitoring-sysdig/sysdig1.png)

A konfiguráció befejezéséhez Sysdig-felhőfiókra vagy ingyenes próbafiókra van szüksége. Miután bejelentkezett a Sysdig felhő webhelyére, kattintson a felhasználónevére, és az oldalon meg kell jelennie a hívóbetűjének („Access Key”). 

![Sysdig API-kulcs](./media/container-service-monitoring-sysdig/sysdig2.png) 

Ezután írja be a hívóbetűt a DC/OS Universe rendszerben található Sysdig-konfigurációba. 

![Sysdig-konfiguráció a DC/OS Universe rendszerben](./media/container-service-monitoring-sysdig/sysdig3.png)

Most állítsa a példányszámot 10000000 értékre, hogy amikor új csomópontot ad a fürthöz, a Sysdig mindig automatikusan üzembe helyezzen egy ügynököt az új csomóponthoz. Ez egy ideiglenes megoldás, hogy a Sysdig a fürtben található összes új ügynöknél üzembe legyen helyezve. 

![Sysdig-konfiguráció a DC/OS Universe-példányokban](./media/container-service-monitoring-sysdig/sysdig4.png)

Miután telepítette a csomagot, lépjen vissza a Sysdig felhasználói felületére, ahol áttekintheti a fürtben lévő tárolók különböző használati mérőszámait. 

Telepíthet kimondottan a Mesoshoz és a Marathonhoz készült irányítópultokat is az [új irányítópult varázsló](https://app.sysdigcloud.com/#/dashboards/new) segítségével.
