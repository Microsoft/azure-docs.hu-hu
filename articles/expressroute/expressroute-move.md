---
title: 'ExpressRoute: áramkörök áthelyezése a Klasszikusból a Azure Resource Managerba'
description: Ismerje meg, mi történik, ha egy Azure ExpressRoute-áramkört helyez át a Klasszikusból a Azure Resource Manager üzemi modellbe.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97807941"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>ExpressRoute-kapcsolatcsoportok áthelyezése a klasszikusból a Resource Manager-alapú üzemi modellbe
Ez a cikk áttekintést nyújt arról, hogy mi történik, ha egy Azure ExpressRoute-áramkört helyez át a Klasszikusból a Azure Resource Manager üzemi modellbe.

Egyetlen ExpressRoute áramkört is használhat a klasszikus és a Resource Manager-alapú üzemi modellben üzembe helyezett virtuális hálózatok összekapcsolásához.

![Mindkét üzemi modellben működő virtuális hálózatokhoz csatlakozó ExpressRoute-kapcsolatcsoport](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>A klasszikus üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportok
A klasszikus üzemi modellben létrehozott ExpressRoute-áramköröket először át kell telepíteni a Resource Manager-alapú üzemi modellbe. Csak ezután engedélyezheti a kapcsolódást a klasszikus és a Resource Manager-alapú üzemi modellekhez. A kapcsolódás nem vész el vagy szakad meg a kapcsolat áthelyezésekor. A klasszikus üzemi modellben található, az előfizetésen és az előfizetésen belüli összes hálózati kapcsolat megmarad.

Az áthelyezés sikeres befejezését követően a ExpressRoute áramkör pontosan ugyanúgy fog működni, mint a Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-áramkör. Ezek után a Resource Manager-alapú üzemi modellben létrehozhatja a kapcsolatokat a virtuális hálózatokhoz.

Miután áthelyezte a ExpressRoute áramkört a Resource Manager-alapú üzemi modellbe, csak a Resource Manager-alapú üzemi modellben kezelheti. A társítások kezeléséhez, az áramkör tulajdonságainak frissítéséhez és az áramkör törléséhez kapcsolódó műveletek csak a Resource Manager-alapú üzemi modellben érhetők el. Az üzembe helyezési modellekhez való hozzáférés kezelésével kapcsolatos további részletekért tekintse meg a következő szakaszt.

Nem kell bevonnia a kapcsolat szolgáltatóját, hogy átvigye az áramkört a Resource Manager-alapú üzemi modellbe.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>A Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportok
Engedélyezheti, hogy a Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportok mindkét üzemi modellből elérhetők legyenek. Az előfizetés bármely ExpressRoute-áramköre beállítható úgy, hogy mindkét üzemi modellből hozzáférhessen.

* A Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-áramkörök alapértelmezés szerint nem férnek hozzá a klasszikus üzemi modellhez.
* A klasszikus üzemi modellből a Resource Manager-alapú üzemi modellbe áthelyezett ExpressRoute-áramkörök alapértelmezés szerint mindkét üzembe helyezési modellből elérhetők.
* Egy ExpressRoute áramkör mindig hozzáfér a Resource Manager-alapú üzemi modellhez, függetlenül attól, hogy az a Resource Managerben vagy a klasszikus üzemi modellben lett létrehozva. A virtuális hálózatok [összekapcsolására](expressroute-howto-linkvnet-arm.md)vonatkozó utasításokat követve hozhat létre kapcsolatokat a virtuális hálózatokhoz.
* A klasszikus üzemi modellhez való hozzáférést az ExpressRoute-kapcsolatcsoport **allowClassicOperations** paramétere vezérli.

> [!IMPORTANT]
> A [szolgáltatásokra vonatkozó korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md) oldalon ismertetett összes kvóta érvényben van. Egy szabványos kapcsolatcsoport például legfeljebb 10 virtuális hálózati kapcsolattal/csatlakozással rendelkezhet a klasszikus és a Resource Manager-alapú üzemi modellekben.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>A klasszikus üzemi modellekhez való hozzáférés vezérlése
Engedélyezheti, hogy egy ExpressRoute áramkör mindkét üzemi modellben a virtuális hálózatokra hivatkozzon. Ehhez állítsa be a **allowClassicOperations** paramétert a ExpressRoute áramkörön.

Az **allowClassicOperations** paraméter TRUE értékre állítása lehetővé teszi, hogy mindkét üzemi modellből csatlakoztasson virtuális hálózatokat az ExpressRoute-kapcsolatcsoporthoz. 
* Ha a virtuális hálózatokat a klasszikus üzemi modellben szeretné összekapcsolni, tekintse meg a [virtuális hálózatok összekapcsolása a klasszikus üzemi modellel](expressroute-howto-linkvnet-classic.md)című témakört.
* A virtuális hálózatok Resource Manager-alapú üzemi modellben való összekapcsolásáról lásd: [virtuális hálózatok összekapcsolása a Resource Manager-alapú üzemi modellben](expressroute-howto-linkvnet-arm.md).

Ha az **allowClassicOperations** paramétert FALSE értékre állítja, azzal blokkolhatja a kapcsolatcsoport elérését a klasszikus üzemi modellből. A klasszikus üzembe helyezési modellben hivatkozott összes virtuális hálózat azonban továbbra is megmarad. A ExpressRoute áramkör nem látható a klasszikus üzembehelyezési modellben.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Támogatott műveletek a klasszikus üzemi modellben
Az ExpressRoute-kapcsolatcsoportokon a következő klasszikus műveletek támogatottak, ha az **allowClassicOperations** TRUE értékre van állítva:

* Az ExpressRoute-kapcsolatcsoport információinak lekérése
* Virtuális hálózati kapcsolatok létrehozása/frissítése/lekérése/törlése klasszikus virtuális hálózatokkal
* Virtuális hálózati kapcsolatok hitelesítéseinek létrehozása/frissítése/lekérése/törlése előfizetések közötti kapcsolatokhoz

Ha azonban a **allowClassicOperations** értéke TRUE (igaz), a következő klasszikus műveletek nem hajthatók végre:

* Border Gateway Protocol- (BGP-) társviszonyok létrehozása/frissítése/lekérése/törlése Azure privát, Azure nyilvános és Microsoft társviszony-létesítéshez
* ExpressRoute-kapcsolatcsoportok törlése

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Kommunikáció a klasszikus és a Resource Manager-alapú üzemi modellek között
Az ExpressRoute-kapcsolatcsoport hídként működik a klasszikus és a Resource Manager-alapú üzemi modellek között A virtuális hálózatok közötti forgalom mindkét üzembe helyezési modell esetében áthaladhat a ExpressRoute áramkörön, ha mindkét virtuális hálózat ugyanahhoz az áramkörhöz van társítva.

Az összesített átviteli sebességet a virtuális hálózati átjáró átviteli kapacitása korlátozza. A forgalom nem adja meg a kapcsolati szolgáltató hálózatait vagy a hálózatait ilyen esetekben. A virtuális hálózatok közötti forgalom teljes mértékben a Microsoft hálózatán keresztül halad át.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure nyilvános és Microsoft társviszony-létesítési erőforrások elérése
Továbbra is zavartalanul elérheti a jellemzően Azure nyilvános társviszonyon és Microsoft-társviszonyon keresztül elérhető erőforrásokat.  

## <a name="whats-supported"></a>Támogatott műveletek
Ez a szakasz az ExpressRoute-kapcsolatcsoportok esetében támogatott műveleteket ismerteti:

* Egyetlen ExpressRoute-kapcsolatcsoportot használhat a klasszikus és a Resource Manager-alapú üzemi modellekbe telepített virtuális hálózatok eléréséhez.
* Áthelyezhet egy ExpressRoute-kapcsolatcsoportot a klasszikusból a Resource Manager-alapú üzemi modellbe. Áthelyezés után a ExpressRoute áramkör továbbra is ugyanúgy működik, mint bármely más, a Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-áramkör.
* Csak az ExpressRoute-kapcsolatcsoportot tudja áthelyezni. A kapcsolatcsoportok kapcsolatai, a virtuális hálózatok és a VPN-átjárók nem helyezhetők át ezzel a művelettel.
* Miután egy ExpressRoute-kapcsolatcsoportot áthelyezett a Resource Manager-alapú üzemi modellbe, az ExpressRoute-kapcsolatcsoport életciklusát csak a Resource Manager-alapú üzemi modell segítségével kezelheti. Ez azt jelenti, hogy olyan műveleteket futtathat, mint például a Hozzáadás/frissítés vagy a társítások törlése, az áramkör tulajdonságainak (például a sávszélesség, az SKU és a számlázási típus) frissítése, és csak a Resource Manager-alapú üzemi modellben lévő áramkörök törlése.
* Az ExpressRoute-kapcsolatcsoport hídként működik a klasszikus és a Resource Manager-alapú üzemi modellek között A virtuális gépek közötti forgalom a klasszikus virtuális hálózatokban és a Resource Manager virtuális hálózatokban található virtuális gépek között a ExpressRoute keresztül kommunikálhat, ha mindkét virtuális hálózat ugyanahhoz a ExpressRoute-áramkörhöz van társítva.
* Az előfizetések közötti kapcsolat a klasszikus és a Resource Manager-alapú üzemi modellekben is támogatott.
* Miután áthelyezett egy ExpressRoute-kapcsolatcsoportot a klasszikusból az Azure Resource Manager-alapú modellbe, [áttelepítheti az ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatokat](expressroute-migration-classic-resource-manager.md) is.

## <a name="whats-not-supported"></a>Nem támogatott műveletek
Ez a szakasz az ExpressRoute-kapcsolatcsoportok esetében nem támogatott műveleteket ismerteti:

* ExpressRoute-kapcsolatcsoportok életciklusának kezelése a klasszikus üzemi modellből.
* Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) támogatja a klasszikus üzembe helyezési modellt. Az Azure RBAC-vezérlőket nem futtathatja egy áramkörre a klasszikus üzemi modellben. Az előfizetés bármelyik rendszergazdája/társrendszergazdája csatlakoztathat virtuális hálózatokat a kapcsolatcsoporthoz, illetve leválaszthatja azokat.

## <a name="configuration"></a>Konfiguráció
Kövesse az [ExpressRoute-kapcsolatcsoportok áthelyezése a klasszikusból a Resource Manager-alapú üzemi modellbe](expressroute-howto-move-arm.md) című szakaszban ismertetett utasításokat.

## <a name="next-steps"></a>Következő lépések
* [ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatok áttelepítése a klasszikusból az Azure Resource Manager-alapú modellbe](expressroute-migration-classic-resource-manager.md)
* További információkért lásd: [ExpressRoute-kapcsolatcsoportok kiépítési munkafolyamatai és kapcsolatcsoport-állapotok](expressroute-workflows.md).
* Az ExpressRoute-kapcsolat konfigurálása:
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-arm.md)

