---
title: Azure Stack Edge Pro-eszköz alaphelyzetbe állítása és újraaktiválása | Microsoft Docs
description: Megtudhatja, hogyan törölheti az adatait, majd újraaktiválhatja Azure Stack Edge Pro-eszközét.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2020
ms.author: alkohli
ms.openlocfilehash: 95fbe5dc4a934fee10c558f640bc24f261203a33
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102443519"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro-eszköz alaphelyzetbe állítása és újraaktiválása

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan lehet alaphelyzetbe állítani, újrakonfigurálni és újraaktiválni egy Azure Stack Edge Pro-eszközt, ha problémákat tapasztal az eszközön, vagy valamilyen más okból kell megkezdenie a frissítést.

Miután visszaállította az eszközt az adateltávolításhoz, újra kell aktiválnia az eszközt új erőforrásként. Az eszköz alaphelyzetbe állítása eltávolítja az eszköz konfigurációját, ezért az eszközt a helyi webes felületen keresztül kell konfigurálnia.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Adatok törlése az eszközön lévő adatlemezekről
> * Az eszköz újraaktiválása új megrendelés létrehozásával, az eszköz újrakonfigurálásával és aktiválásával

## <a name="reset-data-from-the-device"></a>Adatok alaphelyzetbe állítása az eszközről

Az eszközön lévő adatlemezek adatainak törléséhez alaphelyzetbe kell állítania az eszközt.

Az Alaphelyzetbe állítás előtt hozzon létre egy másolatot a helyi adatmennyiségről az eszközön, ha szükséges. Az adatok az eszközről egy Azure Storage-tárolóba másolhatók.

>[!IMPORTANT]
> Az eszköz alaphelyzetbe állítása törli az eszközről az összes helyi adatát és munkaterhelését, és ez nem vonható vissza. Csak akkor állítsa alaphelyzetbe az eszközt, ha újra szeretne kezdeni az eszközzel.

Alaphelyzetbe állíthatja az eszközt a helyi webes felületen vagy a PowerShellben. PowerShell-utasításokért lásd: [az eszköz alaphelyzetbe állítása](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>Eszköz újraaktiválása

Az eszköz alaphelyzetbe állítása után újra kell aktiválnia az eszközt új erőforrásként. Új megrendelés elhelyezése után újra kell konfigurálnia, majd újra kell aktiválnia az új erőforrást.

A meglévő eszköz újraaktiválásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új rendelést a meglévő eszközhöz az [új erőforrás létrehozása](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)című témakör lépéseit követve. A **szállítási címek** lapon válassza a **már van eszköz** elemet.

   ![Nem adható meg új eszköz a szállítási címben](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Szerezze be az aktiválási kulcsot](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key).

1. [Kapcsolódjon az eszközhöz](azure-stack-edge-gpu-deploy-connect.md).

1. [Konfigurálja a hálózatot az eszközhöz](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Adja meg az eszközbeállítások beállításait](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Konfigurálja a tanúsítványokat](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Aktiválja az eszközt](azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Következő lépések

- Útmutató [Azure stack Edge Pro-eszközhöz való kapcsolódáshoz](azure-stack-edge-gpu-deploy-connect.md).
