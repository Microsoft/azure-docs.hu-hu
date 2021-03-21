---
title: A Microsoft segítségével történő társviszony-létesítés előfeltételei
titleSuffix: Azure
description: A Microsoft segítségével történő társviszony-létesítés előfeltételei
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586840"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>A Microsoft segítségével történő társviszony-létesítés előfeltételei

Győződjön meg arról, hogy az alábbi előfeltételek teljesülnek, mielőtt új társítást kér, vagy az Azure-erőforrásra konvertálja az örökölt kapcsolatot.

## <a name="azure-related-prerequisites"></a>Az Azure-hoz kapcsolódó előfeltételek
* **Microsoft Azure fiók:** Ha nem rendelkezik Microsoft Azure fiókkal, hozzon létre egy [Microsoft Azure fiókot](https://azure.microsoft.com/free). A társítások beállításához érvényes és aktív Microsoft Azure előfizetés szükséges, mivel a rendszer az Azure-előfizetésekben lévő erőforrásokként modellezi a társításokat. Fontos megjegyezni, hogy:
    * A peering beállításához használt Azure-erőforrástípusok mindig ingyenes Azure-termékek, azaz nem számítunk fel díjat **Azure-fiók** létrehozásához vagy előfizetés létrehozásához, illetve az Azure-erőforrások **PeerAsn** és-társításhoz való hozzáféréséhez. Ez nem tévesztendő össze az Ön és a Microsoft közötti közvetlen együttműködésre irányuló egyetértési szerződéssel, a feltételeit, amelyekhez kifejezetten tárgyaljuk a csapatot. Ha bármilyen kérdése van, vegye fel a kapcsolatot a [Microsoft-partnerekkel](mailto:peering@microsoft.com) .
    * Ugyanezt az Azure-előfizetést használhatja más Azure-termékek vagy felhőalapú szolgáltatások elérésére, amelyek ingyenesen vagy díjkötelesek lehetnek. Ha egy fizetős termékhez fér hozzá, díjat számítunk fel.
    * Ha új Azure-fiókot és/vagy előfizetést hoz létre, az ingyenes Azure-kreditre jogosult lehet egy próbaidőszak alatt, amelyet az Azure Cloud Services kipróbálására használhat fel. Ha érdekli, további információért látogasson el [Microsoft Azure fiókba](https://azure.microsoft.com/free) .

* **TÁRSKÖZI ASN társítása:** A társítás kérelmezése előtt először kapcsolja össze az ASN-t és a kapcsolattartási adatokat az előfizetéséhez. Kövesse az [TÁRSKÖZI ASN társítása az Azure-előfizetéshez](howto-subscription-association-powershell.md)című témakör utasításait.

## <a name="other-prerequisites"></a>Egyéb előfeltételek
* **PeeringDB-profil:** A társak számára a [PeeringDB](https://www.peeringdb.com)teljes és naprakész profilja szükséges. Ezt az információt a regisztrációs rendszeren a partner adatainak, például a NOC-információknak, a technikai kapcsolattartási adatoknak, valamint azok jelenlétének ellenőrzéséhez használjuk.

## <a name="next-steps"></a>Következő lépések

* [Hozzon létre vagy módosítson egy közvetlen társat a portál használatával](howto-direct-portal.md).
* [Örökölt közvetlen társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-direct-portal.md)
* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-exchange-portal.md)