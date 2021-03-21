---
title: Windows rendszerű virtuális asztali felhasználói kapcsolat késése – Azure
description: A Windows rendszerű virtuális asztali felhasználók kapcsolati késése.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574594"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Felhasználói kapcsolat késésének meghatározása a Windows rendszerű virtuális asztalon

A Windows rendszerű virtuális asztal globálisan elérhető. A rendszergazdák bármilyen Azure-régióban létrehozhatnak virtuális gépeket (VM). A kapcsolatok késése a felhasználók és a virtuális gépek helyétől függően változhat. A Windows rendszerű virtuális asztali szolgáltatások folyamatosan új földrajzi területekre kerülnek a késés javítása érdekében.

A [Windows rendszerű virtuális asztali élmény kalkulátor eszköz](https://azure.microsoft.com/services/virtual-desktop/assessment/) segítségével meghatározhatja a virtuális gépek késésének optimális helyét. Azt javasoljuk, hogy az eszközt két-három hónapon belül használja, hogy az optimális hely ne változzon meg, mivel a Windows rendszerű virtuális asztal kikerül az új területekre.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>A Windows rendszerű virtuális asztali élmény kalkulátor eszköz eredményeinek értelmezése

A Windows rendszerű virtuális asztali számítógépeken a 150 ms-ig terjedő késés nem befolyásolhatja a renderelést vagy videót nem érintő felhasználói élményt. A 150 MS és a 200 MS közötti késésnek a Text Processing értéknek kell lennie. A 200 MS-nál nagyobb késés hatással lehet a felhasználói élményre. 

Emellett a Windows rendszerű virtuális asztali kapcsolat attól függ, hogy a felhasználó milyen számítógépről használja a szolgáltatást. A felhasználók a következő esetek egyikében elveszíthetik a kapcsolatokat, vagy megtapasztalják a bemeneti késleltetést:

 - A felhasználónak nincs stabil helyi internetkapcsolata, és a késés meghaladja a 200 MS-ot.
 - A hálózat telített vagy korlátozott.

Javasoljuk, hogy a lehető legközelebbi virtuális gépeket válassza ki a felhasználók számára. Ha például a felhasználó Indiában található, de a virtuális gép a Egyesült Államokban van, akkor késés lesz, amely hatással lesz az általános felhasználói élményre. 

## <a name="azure-front-door"></a>Azure Front Door

A Windows virtuális asztal az [Azure bejárati ajtaján](https://azure.microsoft.com/services/frontdoor/) keresztül irányítja át a felhasználói kapcsolatokat a legközelebbi Windows virtuális asztali átjáróra a forrás IP-címe alapján. A Windows virtuális asztal mindig az ügyfél által választott Windows-alapú virtuális asztali átjárót fogja használni.

## <a name="next-steps"></a>Következő lépések

- Az optimális késés eléréséhez tekintse meg a [Windows rendszerű virtuális asztali élmény kalkulátor eszközét](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- A díjszabási csomagokért lásd: a [Windows rendszerű virtuális asztali díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- A Windows rendszerű virtuális asztalok üzembe helyezésének első lépéseihez tekintse meg [az oktatóanyagot](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
