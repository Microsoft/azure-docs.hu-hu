---
title: Azure Analysis Services magas rendelkezésre állás | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy a Azure Analysis Services hogyan biztosít magas rendelkezésre állást a szolgáltatás megszakadásakor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e750dce804ea93f3d3068ffd36bc7a73a50906a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573354"
---
# <a name="analysis-services-high-availability"></a>Analysis Services magas rendelkezésre állás

Ez a cikk a Azure Analysis Services-kiszolgálók magas rendelkezésre állásának biztosítását ismerteti. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Magas rendelkezésre állás biztosítása a szolgáltatás megszakadásakor

Ritka esetben az Azure-adatközpontok meghibásodása is lehet. Leállás esetén az üzleti megszakadást okoz, amely akár néhány percet is igénybe vehet, vagy akár órákig is tarthat. A magas rendelkezésre állást a kiszolgáló redundanciával leggyakrabban a rendszer éri el. A Azure Analysis Services segítségével további, másodlagos kiszolgálókat hozhat létre egy vagy több régióban. Redundáns kiszolgálók létrehozásakor a kiszolgálókon tárolt adatok és metaadatok biztosításához szinkronizálva van egy olyan régióban lévő kiszolgálóval, amely offline állapotú, a következőket teheti:

* Modellek üzembe helyezése redundáns kiszolgálókon más régiókban. Ehhez a módszerhez az elsődleges kiszolgálón és a redundáns kiszolgálókon is fel kell dolgozni az adatait párhuzamosan, biztosítva az összes kiszolgáló szinkronizálását.

* Az elsődleges kiszolgálóról származó [biztonsági mentési](analysis-services-backup.md) adatbázisok és a redundáns kiszolgálók visszaállítása. Automatizálhatja például az Azure Storage-ba való éjszakai biztonsági mentést, és más régiókban lévő redundáns kiszolgálókra is visszaállíthatja azokat. 

Mindkét esetben, ha az elsődleges kiszolgáló áramkimaradást tapasztal, módosítania kell a jelentéskészítő ügyfelek kapcsolati karakterláncait, hogy egy másik regionális adatközpontban kapcsolódjanak a kiszolgálóhoz. Ennek a változásnak a végső megoldásnak kell lennie, és csak akkor, ha katasztrofális regionális adatközpont-kimaradás történik. Valószínűbb, hogy az elsődleges kiszolgálót üzemeltető adatközpont-kimaradás a kapcsolatok minden ügyfélen való frissítése előtt újra elérhetővé válik. 

Ha nem szeretné, hogy a jelentéskészítő ügyfeleken ne kelljen módosítani a kapcsolódási karakterláncokat, hozzon létre egy kiszolgálói [aliast](analysis-services-server-alias.md) az elsődleges kiszolgálóhoz. Ha az elsődleges kiszolgáló leáll, módosíthatja az aliast úgy, hogy egy másik régióban lévő redundáns kiszolgálóra mutasson. Az aliast az elsődleges kiszolgálón lévő Endpoint Health-ellenőrzés kódolásával automatizálhatja a kiszolgálónévhez. Ha az állapot-ellenőrzés sikertelen, akkor ugyanaz a végpont egy másik régióban lévő redundáns kiszolgálóra irányíthatja. 

## <a name="related-information"></a>Kapcsolódó információk

  [biztonsági mentése és visszaállítása](analysis-services-backup.md)  
[Azure Analysis Services kezelése](analysis-services-manage.md)   
[Alias-kiszolgálók nevei](analysis-services-server-alias.md) 

