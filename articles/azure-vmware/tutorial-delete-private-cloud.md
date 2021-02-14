---
title: Oktatóanyag – Azure VMware-megoldás saját felhőből való törlése
description: Megtudhatja, hogyan törölhet egy olyan Azure-beli VMware-megoldást, amelyre már nincs szüksége.
ms.topic: tutorial
ms.date: 02/09/2021
ms.openlocfilehash: b11b8f902691db4bd71fd3f52aaa67d46efea643
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101694"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás saját felhőből való törlése

Ha rendelkezik olyan Azure VMware-megoldással, amelyet már nem kell használnia, törölheti. A privát felhő egy elkülönített hálózati tartományt, egy vagy több kiosztott vSphere-fürtöt tartalmaz dedikált kiszolgálói gazdagépeken és több virtuális gépen (VM). Ha töröl egy privát felhőt, az összes virtuális gép, az adatmennyiség és a fürt törölve lesz. A dedikált gazdagépek biztonságosan törlődnek, és az ingyenes készletbe kerülnek vissza. Az ügyfél számára kiosztott hálózati tartomány is törlődik.  

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
