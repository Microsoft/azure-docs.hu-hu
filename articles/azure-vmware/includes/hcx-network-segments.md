---
title: VMware HCX hálózati szegmensek
description: A VMware HCX-hez négy hálózat szükséges.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622139"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Különböző módokon konfigurálhatja a VMware HCX hálózati szegmenseket a helyszínen. A következő egy olyan egyszerű konfigurációt vázol fel, amely támogatja a tesztelési vagy kisméretű üzemi használati esetet.  Több száz vagy több ezer számítási feladat tervezésekor előfordulhat, hogy ezt a konfigurációt a Migrálás igényeitől függően módosítani kell.  

A VMware HCX üzembe helyezésének előkészítése a próba-vagy kisméretű üzemi használati eset támogatásához azonosítsa a következőket:

- **Felügyeleti hálózat:** A VMware HCX helyszíni üzembe helyezése során meg kell határoznia egy felügyeleti hálózatot.  Általában ugyanaz a felügyeleti hálózat, amelyet a helyszíni VMware-fürt használ.  Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. Előfordulhat, hogy nagyobb számra van szüksége, attól függően, hogy az üzemelő példány a pilóta vagy a kis használati eset fölé esik-e.

> [!NOTE]
   > Az ajánlott módszer egy/26 hálózat létrehozása, mivel legfeljebb 10 szolgáltatás-rácsvonalat és 60 hálózati Extendert használhat (-1/Service mesh). Az Azure VMware megoldás privát felhők használatával **nyolc** hálózatot is kiterjesztheti.
   >
   
- **vMotion hálózat:** A VMware HCX helyszíni telepítésekor meg kell adnia egy vMotion hálózatot.  Ez általában ugyanaz a hálózat, amelyet a helyszíni VMware-fürt vMotion használ.  Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. Előfordulhat, hogy nagyobb számra van szüksége, attól függően, hogy az üzemelő példány a pilóta vagy a kis használati eset fölé esik-e.

   A vMotion-hálózatnak elérhetőnek kell lennie egy elosztott virtuális kapcsolón vagy vSwitch0. Ha nem, módosítsa a környezetet a befogadáshoz.

   > [!NOTE]
   > Számos VMware-környezet nem átirányított hálózati szegmenseket használ a vMotion, ami nem jelent problémát.

- **Kimenő hálózat:** A VMware HCX helyszíni üzembe helyezésekor meg kell adnia egy kimenő hálózatot. Használja a kimenő hálózatként definiált felügyeleti hálózatot.
   
- **Replikációs hálózat:** A VMware HCX helyszíni üzembe helyezése során meg kell határoznia egy replikációs hálózatot. Használja a replikációs hálózatként definiált felügyeleti hálózatot.  Ha a helyszíni fürt gazdagépei dedikált replikációs VMkernel használnak, foglaljon le **két** IP-címet ebben a hálózati szegmensben, és használja a replikálási VMkernel hálózatot a replikációs hálózathoz.
