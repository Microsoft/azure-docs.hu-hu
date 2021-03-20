---
title: VMware vész-helyreállítási konfigurációs kiszolgáló követelményei Azure Site Recovery
description: Ez a cikk az Azure-ba történő VMware vész-helyreállítási konfigurációs kiszolgáló üzembe helyezésének támogatását és követelményeit ismerteti Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997808"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>A VMware Azure-ba történő vészhelyreállításához használt konfigurációs kiszolgálóra vonatkozó követelmények

Ha [Azure site Recoveryt](site-recovery-overview.md) használ a VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba történő helyreállításához, a helyszíni konfigurációs kiszolgálót is üzembe kell helyeznie.

- A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt. Emellett az adatreplikálást is kezeli.
- [További](vmware-azure-architecture.md) információ a konfigurációs kiszolgáló összetevőiről és folyamatairól.

## <a name="configuration-server-deployment"></a>Konfigurációs kiszolgáló telepítése

A VMware virtuális gépek Azure-ba való vész-helyreállításához a konfigurációs kiszolgálót VMware VM-ként kell telepíteni.

- A Site Recovery egy olyan PETESEJT-sablont biztosít, amelyet a Azure Portalból tölt le, és az vCenter Serverba importálhatja a konfigurációs kiszolgáló virtuális gépe beállításához.
- Ha a konfigurációs kiszolgálót a petesejtek sablonnal telepíti, a virtuális gép automatikusan megfelel a jelen cikkben felsorolt követelményeknek.
- Javasoljuk, hogy a konfigurációs kiszolgálót a petesejtek sablonnal állítsa be. Ha azonban a VMware virtuális gépek esetében vész-helyreállítást állít be, és nem tudja használni a petesejtek sablont, a konfigurációs kiszolgálót a [megadott utasítások](physical-azure-set-up-source.md)alapján is telepítheti.
- Ha a konfigurációs kiszolgálót a helyszíni fizikai gépek Azure-ba való vész-helyreállítására telepíti, kövesse a [jelen cikkben](physical-azure-set-up-source.md)található utasításokat. 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Következő lépések
Állítsa be a [VMWare virtuális gépek](vmware-azure-tutorial.md) vész-helyreállítását az Azure-ba.
