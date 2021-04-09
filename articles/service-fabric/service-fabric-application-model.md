---
title: Azure Service Fabric-alkalmazás modellje
description: Alkalmazások és szolgáltatások modellezése és leírása az Azure Service Fabric alkalmazás-és szolgáltatás-jegyzékfájlok használatával.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85963733"
---
# <a name="model-an-application-in-service-fabric"></a>Alkalmazás modellezése Service Fabric
Ez a cikk áttekintést nyújt az Azure Service Fabric alkalmazás modelljéről, valamint arról, hogyan határozhat meg egy alkalmazást és szolgáltatást a manifest Files használatával.

## <a name="understand-the-application-model"></a>Az alkalmazás modelljének megismerése
Az alkalmazások olyan összetevő-szolgáltatások gyűjteményei, amelyek egy adott függvényt vagy függvényt hajtanak végre. A szolgáltatás teljes és önálló funkciót hajt végre, és a többi szolgáltatástól függetlenül is elindítható és futtatható.  A szolgáltatás kód, konfiguráció és adatmennyiségből áll. Minden egyes szolgáltatás esetében a kód a végrehajtható fájlokból tevődik össze, a konfiguráció pedig olyan szolgáltatás-beállításokból áll, amelyek futáskor tölthetők be, és az adatmennyiség a szolgáltatás által felhasználható tetszőleges statikus információból áll. A hierarchikus alkalmazás modelljének minden összetevője egymástól függetlenül telepíthető és frissíthető.

![A Service Fabric alkalmazás modellje][appmodel-diagram]

Az alkalmazás típusa egy alkalmazás kategorizálása, és egy csomag típusú szolgáltatásokból áll. A szolgáltatás típusa egy szolgáltatás kategorizálása. A kategorizálás különböző beállításokkal és konfigurációkkal rendelkezhet, de az alapvető funkciók változatlanok maradnak. A szolgáltatás példányai ugyanazon szolgáltatástípus különböző szolgáltatás-konfigurációs variációi.  

Az alkalmazások és szolgáltatások osztályait (vagy "típusait") az XML-fájlok (az alkalmazás-jegyzékfájlok és a szolgáltatási jegyzékfájlok) írják le.  A jegyzékfájlok leírják az alkalmazásokat és a szolgáltatásokat, és azokat a sablonokat, amelyekkel a fürt rendszerkép-tárolójából lehet létrehozni alkalmazásokat.  A jegyzékfájlok részletesen szerepelnek az [alkalmazás-és szolgáltatás-jegyzékfájlokban](service-fabric-application-and-service-manifests.md). A ServiceManifest.xml és ApplicationManifest.xml fájl sémájának definíciója telepítve van a Service Fabric SDK-val és a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Az XML-sémát a [ServiceFabricServiceModel. XSD séma dokumentációjában](service-fabric-service-model-schema.md)dokumentálja.

A különböző alkalmazási példányok kódja külön folyamatként fut, még akkor is, ha ugyanazon a Service Fabric csomóponton futtatja őket. Emellett az egyes alkalmazás-példányok életciklusa egymástól függetlenül kezelhető (például frissített). A következő ábra azt mutatja be, hogyan állnak az alkalmazások típusai a különböző típusú szolgáltatásokból, amelyek pedig kód-, konfigurációs és adatcsomagokból állnak. A diagram leegyszerűsítése érdekében csak a kód/konfiguráció/adatcsomagok jelennek meg `ServiceType4` , de mindegyik szolgáltatástípus tartalmaz néhányat vagy mindegyiket.

![Service Fabric az alkalmazások típusai és a szolgáltatások típusai][cluster-imagestore-apptypes]

A fürtben aktív szolgáltatástípus egy vagy több példánya is lehet. Például az állapot-nyilvántartó szolgáltatás példányai vagy replikái a fürt különböző csomópontjain található replikák replikálásával nagy megbízhatóságot érnek el. A replikáció lényegében redundanciát biztosít a szolgáltatás számára akkor is, ha a fürt egyik csomópontja meghibásodik. A [particionált szolgáltatás](service-fabric-concepts-partitioning.md) tovább osztja az állapotát (és az adott állapothoz való hozzáférési mintákat) a fürt csomópontjai között.

Az alábbi ábrán az alkalmazások és a szolgáltatási példányok, a partíciók és a replikák közötti kapcsolat látható.

![Partíciók és replikák egy szolgáltatáson belül][cluster-application-instances]

> [!TIP]
> A fürtben található alkalmazások elrendezését a http:// &lt; yourclusteraddress &gt; : 19080/explorerben elérhető Service Fabric Explorer eszköz használatával tekintheti meg. További információ: [a fürt megjelenítése a Service Fabric Explorersal](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Következő lépések
- Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
- A szolgáltatás [állapotának](service-fabric-concepts-state.md), [particionálásának](service-fabric-concepts-partitioning.md)és [rendelkezésre állásának](service-fabric-availability-services.md)megismerése.
- További információ arról, hogyan vannak meghatározva az alkalmazások és szolgáltatások az [alkalmazás-és szolgáltatás-jegyzékfájlokban](service-fabric-application-and-service-manifests.md).
- Az [alkalmazás-üzemeltetési modellek](service-fabric-hosting-model.md) a központilag telepített szolgáltatások és a szolgáltatás-gazdagépek replikái (vagy példányai) közötti kapcsolatot írják le.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


