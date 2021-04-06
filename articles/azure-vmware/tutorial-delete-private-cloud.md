---
title: Oktatóanyag – Azure VMware-megoldás saját felhőből való törlése
description: Megtudhatja, hogyan törölhet egy olyan Azure-beli VMware-megoldást, amelyre már nincs szüksége.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462099"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás saját felhőből való törlése

Ha rendelkezik olyan Azure VMware-megoldással, amelyet már nem kell használnia, törölheti. A privát felhő egy elkülönített hálózati tartományt, egy vagy több kiosztott vSphere-fürtöt tartalmaz dedikált kiszolgálói gazdagépeken és több virtuális gépen (VM). Ha töröl egy privát felhőt, az összes virtuális gép, az adatmennyiség és a fürt törölve lesz. A dedikált Azure VMware-megoldás gazdagépei biztonságosan törlődnek, és az ingyenes készletbe kerülnek vissza. A hálózati címtartomány kiépítve is törlődik.  

> [!CAUTION]
> A privát felhő törlése visszafordíthatatlan művelet. A privát felhő törlése után az adatok nem állíthatók helyre, mivel leállítja az összes futó munkaterhelést és összetevőt, és megsemmisíti az összes titkos Felhőbeli adat-és konfigurációs beállítást, beleértve a nyilvános IP-címeket is.

## <a name="prerequisites"></a>Előfeltételek

Ha a virtuális gépek és az azokhoz tartozó adatlemezek később szükségesek, ügyeljen arra, hogy a privát felhő törlése előtt készítsen biztonsági mentést az adatbázisról.  Nem lehet helyreállítani a virtuális gépeket és az azokhoz tartozó adatokat.


## <a name="delete-the-private-cloud"></a>A privát felhő törlése

1. Nyissa meg az Azure VMware Solutions konzolt a [Azure Portal](https://portal.azure.com).

2. Válassza ki a törölni kívánt privát felhőt.
 
3. Adja meg a privát felhő nevét, és válassza az **Igen** lehetőséget. 

>[!NOTE]
>A törlési folyamat néhány órát is igénybe vehet.  
