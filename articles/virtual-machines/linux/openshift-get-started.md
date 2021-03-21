---
title: OpenShift az Azure-ban – áttekintés
description: A OpenShift áttekintése az Azure-ban.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: aba01fc2317372438bc0d93a6618d518ab03ed0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672313"
---
# <a name="openshift-in-azure"></a>OpenShift az Azure-ban

A OpenShift egy nyílt és bővíthető tároló-alkalmazási platform, amely a Docker-t és a Kubernetes a vállalat számára teszi elérhetővé.  

A OpenShift tartalmazza a Kubernetes és a felügyeletet. A következőket engedélyező fejlesztői központú és műveleti központú eszközöket adja hozzá:

- Gyors alkalmazásfejlesztés.
- Egyszerű üzembe helyezés és skálázás.
- A csoportok és alkalmazások hosszú távú életciklus-karbantartása.

A OpenShift több verziója érhető el.  Ezen verziók közül csak kettő érhető el az Azure-ban üzembe helyezett ügyfelek számára: OpenShift Container platform and OKD (korábban OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift egy teljes körűen felügyelt OpenShift, amely az Azure-ban fut. Ezt a szolgáltatást a Microsoft és a Red Hat közösen kezeli és támogatja. További részletekért tekintse meg az [Azure Red Hat OpenShift szolgáltatás](../../openshift/index.yml) dokumentációját.

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

A Container platform egy nagyvállalati használatra kész, a Red Hat által támogatott [kereskedelmi verzió](https://www.openshift.com) . Ebben a verzióban az ügyfelek megvásárolják a OpenShift-tároló platformhoz szükséges jogosultságokat, és felelősek a teljes infrastruktúra telepítéséhez és kezeléséhez.

Mivel az ügyfelek a teljes platformot használják, a saját helyszíni adatközpontban vagy nyilvános felhőben (például az Azure-ban) is telepíthetik azt.

## <a name="okd"></a>OKD

A OKD egy [nyílt forráskódú](https://www.okd.io/) , OpenShift által támogatott felső szintű projekt. A OKD telepíthető CentOS vagy Red Hat Enterprise Linux (RHEL) rendszerre.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli OpenShift általános előfeltételeinek konfigurálása](./openshift-container-platform-3x-prerequisites.md)
- [OpenShift-tároló platform üzembe helyezése az Azure-ban](./openshift-container-platform-3x.md)
- [OpenShift-tároló platform Self-Managed Marketplace-ajánlat üzembe helyezése](./openshift-container-platform-3x-marketplace-self-managed.md)
- [OpenShift üzembe helyezése Azure Stack](./openshift-azure-stack.md)
- [Üzembe helyezés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-telepítés hibáinak megoldása](./openshift-container-platform-3x-troubleshooting.md)
