---
title: VMware HCX hálózati szegmensek
description: A VMware HCX-hez négy hálózat szükséges.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251465"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Különböző módokon konfigurálhatja a VMware HCX hálózati szegmenseket a helyszínen. A következő egy olyan egyszerű konfigurációt vázol fel, amely támogatja a tesztelési vagy kisméretű üzemi használati esetet.  Több száz vagy több ezer számítási feladat tervezésekor előfordulhat, hogy ezt a konfigurációt a Migrálás igényeitől függően módosítani kell.  

A VMware HCX üzembe helyezésének előkészítése a próba-vagy kisméretű üzemi használati eset támogatásához azonosítsa a következőket:

- **Felügyeleti hálózat:** A VMware HCX helyszíni üzembe helyezése során azonosítania kell egy felügyeleti hálózatot a VMware HCX számára.  Általában ugyanaz a felügyeleti hálózat, amelyet a helyszíni VMware-fürt használ.  Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. Előfordulhat, hogy nagyobb számra van szüksége, attól függően, hogy az üzemelő példány a pilóta vagy a kis használati eset fölé esik-e.

  > [!NOTE]
  > Nagy környezetek előkészítése a helyszíni VMware-fürthöz használt felügyeleti hálózat helyett, hozzon létre egy új/26 hálózatot, és jelentse a hálózatot a helyszíni VMware-fürtnek.  Ezt követően legfeljebb 10 szolgáltatás-rácsvonalat és 60 hálózati extenderet hozhat létre (a Service Mesh-1 szolgáltatásban). Az Azure VMware megoldás privát felhők használatával **nyolc** hálózatot is kiterjesztheti.
  >

- **Kimenő hálózat:** A VMware HCX helyszíni üzembe helyezése során azonosítania kell egy kimenő hálózatot a VMware HCX számára. Használja ugyanazt a hálózatot, amelyet a felügyeleti hálózathoz fog használni. 

- **vMotion hálózat:** A VMware HCX helyszíni üzembe helyezése során meg kell adnia egy vMotion-hálózatot a VMware HCX számára.  Ez általában ugyanaz a hálózat, amelyet a helyszíni VMware-fürt vMotion használ.  Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. Előfordulhat, hogy nagyobb számra van szüksége, attól függően, hogy az üzemelő példány a pilóta vagy a kis használati eset fölé esik-e.

   A vMotion-hálózatnak elérhetőnek kell lennie egy elosztott virtuális kapcsolón vagy vSwitch0. Ha nem, módosítsa a környezetet a befogadáshoz.

   > [!NOTE]
   > Számos VMware-környezet nem átirányított hálózati szegmenseket használ a vMotion, ami nem jelent problémát.
  
- **Replikációs hálózat:** A VMware HCX helyszíni üzembe helyezése során meg kell határoznia egy replikációs hálózatot. Ugyanazt a hálózatot használja, mint amelyet a felügyeleti és a kimenő hálózatokhoz használ.  Ha a helyszíni fürt gazdagépei dedikált replikációs VMkernel használnak, foglaljon le **két** IP-címet ebben a hálózati szegmensben, és használja a replikálási VMkernel hálózatot a replikációs hálózathoz.
