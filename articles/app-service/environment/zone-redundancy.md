---
title: A rendelkezésre állási zónák támogatása App Service környezetekben
description: Megtudhatja, hogyan helyezheti üzembe a App Service környezeteket, hogy alkalmazásai feleslegesek legyenek a zónában.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624725"
---
# <a name="availability-zone-support-for-app-service-environments"></a>A rendelkezésre állási zónák támogatása App Service környezetekben

App Service környezetek (központilag) üzembe helyezhetők Availability Zones (AZ) rendszerbe.  A belső terheléselosztó (ILB) ASE egy adott Azure-régión belül is üzembe helyezhetők. Ha a ILB-bevezetést egy adott adott eszközhöz rögzíti, a ILB-beadási szolgáltatás által használt erőforrások vagy a megadott, vagy a zóna redundáns módon lesznek telepítve.  

Az az-ba explicit módon központilag telepített ILB-bevezetési forrásnak minősül, mivel a ILB-bevezetési szolgáltatás egy adott zónához van rögzítve. A következő ILB-beadási függőségek a megadott zónába lesznek rögzítve:

- a belső terheléselosztó IP-címe
- a szolgáltató által a webalkalmazások kezeléséhez és futtatásához használt számítási erőforrások

A többhelyes ILB-kiegészítő szolgáltatásban üzembe helyezett webalkalmazások távoli fájlokra épülő tárterülete redundáns tárterületet (ZRS) használ.

Hacsak nem következik be a cikkben ismertetett lépéseket, a ILB-ASE nem lesznek automatikusan kihelyezve a zónákba. Egy nyilvános IP-címmel rendelkező külső beadási szolgáltatás nem rögzíthető egy adott rendelkezésre állási zónához. 

A zóna ILB ASE a következő régiókban hozhatók létre:

- Kelet-Ausztrália
- Közép-Kanada
- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA 2. keleti régiója (– EUAP)
- Közép-Franciaország 
- Kelet-Japán
- Észak-Európa
- Nyugat-Európa
- Délkelet-Ázsia
- Az Egyesült Királyság déli régiója
- USA 2. nyugati régiója

A régión belüli ILB-előállítók által üzembe helyezett alkalmazások továbbra is futtatják és szolgálják majd a forgalmat a Központon, még akkor is, ha az azonos régióban lévő más zónák áramkimaradást szenvednek  Lehetséges, hogy a nem futásidejű viselkedések, beleértve a következőket: az alkalmazás-szolgáltatási csomag skálázása, az alkalmazások létrehozása, az alkalmazások konfigurációja és az alkalmazások közzététele továbbra is hatással lehet a többi rendelkezésre állási zónában lévő kimaradásra. A zónákhoz tartozó ILB-bevezetési zóna rögzített üzembe helyezése csak a már üzembe helyezett alkalmazások folyamatos üzemidőét biztosítja.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>App Service Environment üzembe helyezése rendelkezésre állási zónában ##

A ILB ASE kell létrehozni az ARM-sablonok használatával. Miután egy zóna-sablonnal létrehozta a zónákhoz tartozó ILB, a Azure Portal és a CLI használatával megtekintheti és kezelheti.  Egy ARM-sablonra csak a zóna ILB bevezető létrehozásakor van szükség.

Az ARM-sablonban csak az új ***Zones** _ tulajdonságot kell megadnia, hogy megadják a zónákhoz tartozó ILB. A _ *_Zones_** tulajdonság értékét "1", "2" vagy "3" értékre kell állítani attól függően, hogy milyen logikai rendelkezésre állási zónában kell rögzítenie a ILB-előállítók által rögzített értéket.

Az alábbi példa ARM-sablon az új ***zónák*** tulajdonságot mutatja be, amely azt határozza meg, hogy a ILB beadása a 2. zónára legyen rögzítve.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Ahhoz, hogy az alkalmazások zónája redundáns legyen, üzembe kell helyeznie két ILB-ASE. A két Zona ILB-ASE külön rendelkezésre állási zónákban kell lennie. Ezután telepítenie kell az alkalmazásokat az egyes ILB-ASE. Az alkalmazások létrehozása után konfigurálnia kell egy terheléselosztási megoldást. Az ajánlott megoldás egy, a zóna ILB-ASE felsőbb rétegében [redundáns Application Gateway](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) üzembe helyezése. 

## <a name="in-region-data-residency"></a>Régióbeli adattárolás ##

A rendelkezésre állási zónában üzembe helyezett ILB-ASE csak azon a régión belül tárolják az ügyféladatokat, ahol a ILB-központot üzembe helyezte. A webhelyhez tartozó fájlok tartalma, valamint az ügyfél által megadott beállítások és a App Service tárolt titkos kulcsok abban a régióban maradnak, ahol a ILB-t központilag telepítették.

Az egyrégiós adattárolást az ügyfelek az "App Service Environment üzembe helyezése rendelkezésre állási zónában" című szakaszban ismertetett lépéseket követve biztosítják. Egy App Service Environment ezen lépések alapján történő konfigurálásával a rendelkezésre állási zónában üzembe helyezett App Service Environment megfelel a régiókban tárolt adattárolási követelményeknek, beleértve a [Azure biztonsági és adatkezelési központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.

Az alábbi lépések végrehajtásával ellenőrizheti, hogy az App Service Environment megfelelően van-e konfigurálva egyetlen régióban tárolt adattároláshoz: 

1. A [erőforrás-kezelő](https://resources.azure.com)használatával navigáljon a app Service Environment ARM-erőforráshoz.  A ASE a *szolgáltatók/Microsoft. Web/hostingEnvironments* területen találhatók.
2. Ha egy *Zones* tulajdonság létezik az ARM JSON-szintaxis nézetében, és egyetlen értékű JSON-tömböt tartalmaz, amelynek értéke "1", "2" vagy "3", akkor a zonally üzembe helyezése és az ügyféladatok ugyanabban a régióban maradnak.
2. Ha a *Zones tulajdonság nem* létezik, vagy a tulajdonság nem rendelkezik a korábban megadott értékkel, akkor a zonally nincs telepítve, és az ügyféladatok nem kizárólag ugyanabban a régióban tárolódnak.
