---
title: VMware HCX hálózati szegmensek
description: A VMware HCX-hez négy hálózat szükséges.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 1f0fcc1ddeeb26702a297035e6ae2a2f73dd71d6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462294"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

A VMware HCX négy hálózatra van szükség:

- **Felügyeleti hálózat:** Általában ugyanaz a felügyeleti hálózat, amelyet az Azure VMware megoldás-fürt használ. Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.

   > [!NOTE]
   > Az ajánlott módszer egy/26 hálózat létrehozása, mivel legfeljebb 10 szolgáltatás-rácsvonalat és 60 hálózati Extendert használhat (-1/Service mesh). Az Azure VMware megoldás privát felhők használatával **nyolc** hálózatot is kiterjesztheti.
   >
   
- **vMotion hálózat:** Általában ugyanazt a hálózatot használják a vMotion az Azure VMware Solution-fürtön.  Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.  

   A vMotion-hálózatnak elérhetőnek kell lennie egy elosztott virtuális kapcsolón vagy vSwitch0. Ha nem, módosítsa a környezetet.

   > [!NOTE]
   > Ez a hálózat lehet privát (nem irányított).

- **Kimenő hálózat:** Létre kell hoznia egy új hálózatot a VMware HCX kimenő hálózathoz, és ki kell terjesztenie a vSphere-fürtre egy porttartomány használatával. Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.  

   > [!NOTE]
   > Az ajánlott módszer egy/26 hálózat létrehozása, mivel legfeljebb 10 szolgáltatás-rácsvonalat és 60 hálózati Extendert használhat (-1/Service mesh). Az Azure VMware megoldás privát felhők használatával **nyolc** hálózatot is kiterjesztheti.
   >
   
- **Replikációs hálózat:** Ez nem kötelező. Hozzon létre egy új hálózatot a VMware HCX-replikációhoz, és terjessze ki a hálózatot a vSphere-fürtön egy porttartomány használatával. Legalább **két** IP-címet azonosítson ezen a hálózati szegmensen a VMware HCX. A telepítéstől függően nagyobb számra lehet szükség.

   > [!NOTE]
   > Ez a konfiguráció csak akkor lehetséges, ha a helyszíni fürt gazdagépei dedikált replikációs VMkernel-hálózatot használnak.  Ha a helyszíni fürthöz nincs definiálva dedikált replikációs VMkernel hálózat, nem kell létrehoznia ezt a hálózatot.
