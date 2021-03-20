---
title: Az Azure Cloud Services (klasszikus) kezelése Azure Automation használatával | Microsoft Docs
description: Ismerje meg, hogyan használható a Azure Automation szolgáltatás az Azure Cloud Services nagy léptékű kezeléséhez.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743491"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Az Azure Cloud Services (klasszikus) kezelése Azure Automation használatával

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.
Ez az útmutató bemutatja a Azure Automation szolgáltatást, valamint azt, hogy miként használható az Azure Cloud Services kezelésének egyszerűbbé tételéhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely egyszerűbbé teszi a felhőalapú felügyeletet a folyamatok automatizálásával. A Azure Automation, a hosszan futó, a manuális, a hibákra hajlamos és a gyakran ismétlődő feladatok használatával automatizálható a szervezet megbízhatóságának, hatékonyságának és időpontjának növelése.

A Azure Automation egy nagyon megbízható és jól elérhető munkafolyamat-végrehajtó motort biztosít, amely a szervezet növekedésének megfelelően méretezhető. Azure Automation a folyamatokat manuálisan, harmadik féltől származó rendszerek vagy ütemezett időközök alapján lehet kiváltani, hogy a feladatok a szükségesnél pontosan megtörténjenek.

Csökkentse a működési terhelést, és szabadítson fel informatikai/DevOps-személyzetet, hogy az üzleti értékeket felhasználva a Felhőbeli felügyeleti feladatok automatikus futtatásával Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hogyan segíthet Azure Automation az Azure Cloud Services kezelése?
Az Azure Cloud Services Azure Automation a [Azure PowerShell eszközökben](/powershell/)elérhető PowerShell-parancsmagok használatával kezelhetők. Azure Automation a Cloud Service PowerShell-parancsmagok elérhetők a dobozból, így az összes felhőalapú szolgáltatás-felügyeleti feladatot elvégezheti a szolgáltatáson belül. Ezeket a parancsmagokat Azure Automation is párosíthatja más Azure-szolgáltatások parancsmagokkal, így összetett feladatokat automatizálhat az Azure-szolgáltatások és a külső gyártók rendszerei között.

Néhány példa a Azure Automation használatára az Azure Cloud Services kezeléséhez:

* [Felhőalapú szolgáltatás folyamatos üzembe helyezése, amikor a cscfg vagy a cspkg frissül az Azure Blob Storage-ban](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Cloud Service-példányok újraindítása párhuzamosan, egyszerre egy frissítési tartomány](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Azure Automation alapjait és az Azure Cloud Services felügyeletének módját, az alábbi hivatkozásokat követve további információkat tudhat meg a Azure Automationról.

* [Az Azure Automation áttekintése](../automation/automation-intro.md)
* [Az első runbookom](../automation/learn/automation-tutorial-runbook-graphical.md)