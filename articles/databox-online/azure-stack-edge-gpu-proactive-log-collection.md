---
title: Az Azure Stack Edge Pro-eszközön futó proaktív naplók megismerése
description: Leírja, hogyan történik az proaktív naplók gyűjtése egy Azure Stack Edge Pro-eszközön, és hogyan lehet letiltani.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: bdd44bf81e34b60ee648b71c6dc3bde5a96d3deb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443012"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proaktív naplók gyűjtése az Azure Stack Edge-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A proaktív naplók a Azure Stack peremhálózati eszközön rendszerállapot-jelölőket gyűjtenek, amelyek segítségével hatékonyan elháríthatja az eszközök problémáit. A proaktív naplózási gyűjtemény alapértelmezés szerint engedélyezve van. Ez a cikk ismerteti a naplózott, a Microsoft által kezelt adatkezelési műveleteket, valamint a proaktív naplók letiltásának vagy engedélyezésének módját. 

A cikkben található információk Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkoznak.

## <a name="about-proactive-log-collection"></a>A proaktív naplók gyűjtésének ismertetése

A Microsoft ügyfélszolgálati és mérnöki csapata rendszernaplókat használ a Azure Stack peremhálózati eszközön a művelet során esetlegesen felmerülő problémák hatékony azonosításához és javításához. Az előjelzéses naplózási gyűjtemény olyan metódus, amely riasztást küld a Microsoftnak, hogy az ügyfél Azure Stack peremhálózati berendezése hibát/eseményt észlelt. A nyomon követett eseményeknél tekintse meg a [proaktív naplók gyűjtésével kapcsolatos mutatókat](#proactive-log-collection-indicators) . A hibával kapcsolatos támogatási naplók automatikusan feltöltve lettek a Microsoft által felügyelt és felügyelt Azure Storage-fiókba. A Microsoft ügyfélszolgálata és a Microsoft mérnökök ezeket a támogatási naplókat tekintik át a probléma megoldásához szükséges legjobb műveletek meghatározásához.

> [!NOTE]
> Ezeket a naplókat csak hibakeresési célokra használják, és problémák esetén támogatást biztosítanak az ügyfeleknek.


## <a name="enabling-proactive-log-collection"></a>Proaktív naplók gyűjtésének engedélyezése

A proaktív naplózási gyűjtemény alapértelmezés szerint engedélyezve van. Ha a helyi felhasználói felületen kísérli meg az eszköz aktiválását, letilthatja az előjelzéses naplózási gyűjteményt. 

1. Az eszköz helyi webes FELÜLETén nyissa meg az **első lépések** lapot.

2. Az **aktiválási** csempén válassza az **aktiválás** lehetőséget. 

    ![Helyi webes felhasználói felület "Cloud details" 1. oldal](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. Az **aktiválás** ablaktáblán:

   1. Adja meg azt az **aktiválási kulcsot** , amelyet az [Azure stack Edge Pro R aktiválási kulcsának beszerzéséhez](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)kapott.

      Az aktiválást követően a proaktív naplózási gyűjtemény alapértelmezés szerint engedélyezve van, ami lehetővé teszi a Microsoft számára, hogy az eszköz állapota alapján gyűjtsön naplókat. Ezek a naplók egy Azure Storage-fiókba lesznek feltöltve. 

      Letilthatja az előjelzéses naplók gyűjtését, hogy a Microsoft ne gyűjtsön naplókat.

   1. Ha le szeretné tiltani az előjelzéses naplózási gyűjteményt az eszközön, válassza a **Letiltás** lehetőséget.

   1. Válassza az **aktiválás** lehetőséget.

   ![Helyi webes felhasználói felület "Cloud details" 2. oldal](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>Proaktív naplók gyűjtési mutatói

Az előjelzéses naplózási gyűjtemény engedélyezése után a rendszer automatikusan feltölti a naplókat, ha a következő események valamelyike észlelhető az eszközön:  


|Riasztás/hiba/feltétel  |Description  |
|---------|---------|
|AcsUnhealthyCondition     |Az Azure konzisztens szolgáltatások állapota nem kifogástalan.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge ügynök nem fut.         |
|UpdateInstallFailedEvent | Nem lehetett telepíteni a frissítést.        |

 
A Microsoft továbbra is új eseményeket ad az előző listához. Új eseményekhez nem szükséges további beleegyezni. Tekintse meg ezt az oldalt, és ismerkedjen meg a legnaprakészebb proaktív naplók gyűjtési mutatókkal.    
 

## <a name="other-log-collection-methods"></a>Egyéb naplók gyűjtési módszerei

Az előjelzéses naplózási gyűjteményen kívül a rendszer az észlelt konkrét problémákra vonatkozó naplókat gyűjt, a többi naplózási gyűjtemény pedig sokkal mélyebben ismeri a rendszerállapotot és a működést. Ezeket a naplókat általában egy támogatási kérelem során vagy a Microsoft a telemetria-adatok alapján, az eszközről indította el.

## <a name="handling-data"></a>Az adatkezelés

Ha engedélyezve van az előjelzéses naplók gyűjtése, az ügyfél beleegyezik abba, hogy a Microsoft a Azure Stack Edge-eszközről gyűjti a naplókat az itt leírtak szerint. Az ügyfél emellett tudomásul veszi és elfogadja a naplók feltöltését és megőrzését egy, a Microsoft által kezelt és felügyelt Azure Storage-fiókban.

A Microsoft az információkat csak a rendszerállapot és a problémák elhárításához használja fel. Az adatkezelési, reklámozási vagy egyéb kereskedelmi célra nem használhatók az ügyfelek beleegyezett engedélye nélkül. 

A Microsoft által gyűjtött adatok a szokásos adatvédelmi gyakorlatnak megfelelően kezelhetők. Ha az ügyfél úgy dönt, hogy visszavonja az előjelzéses naplókhoz való beleegyezését, a visszavonást megelőzően beleegyezéssel gyűjtött adatokat a rendszer nem érinti.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [gyűjthet egy támogatási csomagot](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).