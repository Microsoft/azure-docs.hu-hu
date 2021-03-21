---
title: Állítsa be a konfigurációs kiszolgálót a fizikai kiszolgálók Azure-ba történő helyreállításához a Azure Site Recovery használatával | Microsoft Docs "
description: Ez a cikk azt ismerteti, hogyan állíthatja be a helyszíni konfigurációs kiszolgálót a helyszíni fizikai kiszolgálók Azure-ba való vész-helyreállításához.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 327e995a8fe2f66903548fba054804768d2538ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001555"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>A fizikai kiszolgálók Azure-ba történő vészhelyreállítást végző konfigurációs kiszolgáló beállítása

Ez a cikk azt ismerteti, hogyan állíthatja be a helyszíni környezetet a Windows vagy Linux rendszerű fizikai kiszolgálók Azure-ba történő replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már rendelkezik az alábbiakkal:
- A [Azure Portal](https://portal.azure.com "Azure Portal")Recovery Services-tárolója.
- Egy fizikai számítógép, amelyre telepíteni kívánja a konfigurációs kiszolgálót.
- Ha letiltotta a TLS 1,0-et azon a számítógépen, amelyre a konfigurációs kiszolgálót telepíti, győződjön meg arról, hogy a TLs 1,2 engedélyezve van, és hogy a .NET-keretrendszer 4,6-as vagy újabb verziója telepítve van a gépen (erős kriptográfia engedélyezve van). [További információ](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>A konfigurációs kiszolgáló minimális követelményei
A következő táblázat a konfigurációs kiszolgáló minimális hardver-, szoftver-és hálózati követelményeit sorolja fel.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> A konfigurációs kiszolgáló nem támogatja a HTTPS-alapú proxykiszolgálók használatát.

## <a name="choose-your-protection-goals"></a>Válassza ki a védelmi célokat

1. A Azure Portal nyissa meg a **Recovery Services** -tárolók panelt, és válassza ki a tárolót.
2. A tároló **erőforrás** menüjében kattintson a **első lépések**  >  **site Recovery**  >  **1. lépés: az infrastruktúra előkészítése**  >  **védelmi cél** elemre.

    ![A védelmi cél kiválasztásának helyét bemutató képernyőkép.](./media/physical-azure-set-up-source/choose-goals.png)
3. A **védelem célja** területen válassza **Az Azure-t** , és **ne virtualizált/egyéb** beállítást, majd kattintson **az OK** gombra.

    ![Célok megválasztása](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

1. Ha nem rendelkezik konfigurációs kiszolgálóval, a **forrás előkészítése** területen kattintson a **+ konfigurációs kiszolgáló** elemre a hozzáadáshoz.

   ![A konfigurációs kiszolgáló kijelölését bemutató képernyőkép.](./media/physical-azure-set-up-source/plus-config-srv.png)
2. A **kiszolgáló hozzáadása** panelen győződjön meg arról, hogy a **konfigurációs kiszolgáló** megjelenik a **kiszolgáló típusa mezőben**.
4. Töltse le a Site Recovery egyesített telepítési telepítőfájlt.
5. Töltse le a tároló regisztrációs kulcsát. Az egyesített telepítő futtatásakor szüksége lesz a regisztrációs kulcsra. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/physical-azure-set-up-source/set-source2.png)
6. A konfigurációs kiszolgálóként használt gépen futtassa **Azure site Recovery egyesített telepítőt** a konfigurációs kiszolgáló, a Process Server és a fő célkiszolgáló telepítéséhez.

#### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery egyesített telepítő futtatása

> [!TIP]
> A konfigurációs kiszolgáló regisztrációja meghiúsul, ha a számítógép rendszeróráján lévő idő több mint öt percen belül helyi idő alatt van. A telepítés megkezdése előtt szinkronizálja a rendszeridőt egy [időkiszolgálóval](/windows-server/networking/windows-time-service/windows-time-service-top) .

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló parancssor használatával telepíthető. [További információ](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Gyakori problémák

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Következő lépések

A következő lépés magában foglalja [a cél környezet beállítását](physical-azure-set-up-target.md) az Azure-ban.
