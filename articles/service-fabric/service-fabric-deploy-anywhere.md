---
title: Az Azure és az önálló Service Fabric-fürtök áttekintése
description: Service Fabric-fürtöket a Windows Servert vagy Linuxot futtató virtuális gépeken és számítógépeken is létrehozhat. Ez azt jelenti, hogy minden olyan környezetben telepítheti és futtathatja Service Fabric alkalmazást, ahol a Windows Server vagy Linux rendszerű számítógépek, amelyek egymáshoz kapcsolódnak – helyszíni, Microsoft Azure vagy bármilyen felhőalapú szolgáltatóval.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576128"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Az Azure és az önálló Service Fabric-fürtök összehasonlítása Windows Serveren és Linuxon

A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. A fürt részét képező számítógépet vagy virtuális gépet fürtcsomópont-csomópontnak nevezzük. A fürtök több ezer csomópontra is méretezhetők. Ha új csomópontokat ad hozzá a fürthöz, Service Fabric a csomópontok számának megnövekedésével kiegyensúlyozza a szolgáltatás partíciójának replikáit és példányait. Az alkalmazások teljes teljesítményének növelése és a memória-hozzáférés csökkentése. Ha a fürt csomópontjait nem használják hatékonyan, csökkentheti a fürt csomópontjainak számát. Service Fabric újra kiegyenlíti a partíciók replikáit és példányait a csomópontok számának csökkenésével, hogy jobban használhassa a hardvert az egyes csomópontokon.

Service Fabric lehetővé teszi Service Fabric-fürtök létrehozását a Windows Servert vagy Linuxot futtató virtuális gépeken vagy számítógépeken. Ez azt jelenti, hogy minden olyan környezetben telepítheti és futtathatja Service Fabric-alkalmazásait, ahol a Windows Server vagy Linux rendszerű számítógépek összekapcsolhatók, akár helyszíni, Microsoft Azure vagy bármilyen felhőalapú szolgáltatóval.

## <a name="benefits-of-clusters-on-azure"></a>Az Azure-beli fürtök előnyei

Az Azure-ban integrációt biztosítunk más Azure-funkciókkal és-szolgáltatásokkal, így a fürt működésének és kezelésének egyszerűbbé és megbízhatóbbnak kell lennie.

* **Azure Portal:** A Azure Portal megkönnyíti a fürtök létrehozását és kezelését.
* **Azure Resource Manager:** A Azure Resource Manager használata lehetővé teszi, hogy a fürt által használt összes erőforrás könnyen kezelhető legyen egységként, és leegyszerűsítse a költségeket és a számlázást.
* **Fürt Service Fabric Azure-erőforrásként** A Service Fabric-fürt egy Azure-erőforrás, így modellezheti, mint az Azure más erőforrásait.
* **Integráció az Azure-infrastruktúrával** Az alkalmazások rendelkezésre állásának és megbízhatóságának javítása érdekében Service Fabric koordinálja az operációs rendszer, a hálózat és az egyéb verziófrissítések alapjául szolgáló Azure-infrastruktúrát.  
* **Diagnosztika:** Az Azure-ban integrációt biztosítunk az Azure Diagnostics szolgáltatással és Azure Monitor naplókkal.
* Automatikus **skálázás:** Az Azure-beli fürtökhöz beépített automatikus skálázási funkciókat biztosítunk a virtuálisgép-méretezési csoportokon keresztül. A helyszíni és más felhőalapú környezetekben saját automatikus skálázási funkciót kell létrehoznia, vagy manuálisan kell méreteznie azokat az API-k használatával, amelyek Service Fabric teszik lehetővé a fürtök méretezését.

## <a name="benefits-of-standalone-clusters"></a>Az önálló fürtök előnyei

* A fürt üzemeltetéséhez szabadon választhat bármely felhőalapú szolgáltatót.
* Service Fabric az alkalmazások az írást követően több üzemeltetési környezetben is futtathatók, minimális változtatás nélkül.
* Service Fabric alkalmazások létrehozásának ismerete az egyik üzemeltetési környezetből a másikba.
* Service Fabric-fürtök futtatásának és kezelésének működési élménye az egyik környezetből a másikba történik.
* A széles körű ügyfél-elérhetőséget az üzemeltetési környezet megkötései kötik.
* Egy további megbízhatósági réteg és a széleskörű leállások elleni védelem is létezik, mivel a szolgáltatásokat áthelyezheti egy másik központi telepítési környezetbe, ha egy adatközpont vagy egy felhőalapú szolgáltató áramszünet miatt megtalálhatók.

## <a name="next-steps"></a>Következő lépések

* Tekintse át az [Azure](service-fabric-azure-clusters-overview.md) -beli Service Fabric-fürtök áttekintését
* Tekintse át [Service Fabric önálló fürtök](service-fabric-standalone-clusters-overview.md) áttekintését
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése