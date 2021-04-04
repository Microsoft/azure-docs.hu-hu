---
title: Azure VMware-megoldás CloudSimple-szolgáltatással
description: Ismerje meg a CloudSimple szolgáltatást az áttekintésével. A szolgáltatás létrehozása lehetővé teszi a csomópontok megvásárlását, a csomópontok fenntartását és a privát felhők létrehozását.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8f32197eda4fc7632e883fd21dd6e1caa0dbd24b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898742"
---
# <a name="cloudsimple-service-overview"></a>A CloudSimple szolgáltatás áttekintése

A CloudSimple szolgáltatás lehetővé teszi, hogy a CloudSimple az Azure VMware-megoldást használja.  A szolgáltatás létrehozása lehetővé teszi a csomópontok megvásárlását, a csomópontok fenntartását és a privát felhők létrehozását.  A CloudSimple szolgáltatást minden olyan Azure-régióban létre kell hoznia, ahol a CloudSimple szolgáltatás elérhető. A szolgáltatás a CloudSimple által definiált Azure VMware-megoldás peremhálózati hálózatát határozza meg. A peremhálózati hálózat olyan szolgáltatásokat támogat, amelyek VPN-, ExpressRoute-és internetkapcsolattal rendelkeznek a privát Felhőkhöz.

## <a name="gateway-subnet"></a>Átjáró alhálózata

Az átjáró-alhálózatok CloudSimple szolgáltatáshoz szükségesek, és egyediek ahhoz a régióhoz, amelyben létrehozták. Az átjáró-alhálózat a peremhálózati hálózat létrehozásakor használatos, és egy/28 CIDR blokkot igényel.  Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal. A CloudSimple-mel kommunikáló hálózatok közé tartoznak a helyszíni hálózatok és az Azure Virtual Network.  Az átjáró-alhálózat nem törölhető A létrehozásuk után.  Az átjáró-alhálózat törlődik a szolgáltatás törlésekor.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre CloudSimple szolgáltatást az Azure](quickstart-create-cloudsimple-service.md)-ban.
