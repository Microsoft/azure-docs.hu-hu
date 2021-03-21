---
title: Cserélje le az Azure Stack Edge Pro-eszközét | Microsoft Docs
description: Ismerteti, hogyan lehet helyettesítő Azure Stack Edge Pro-eszközt beolvasni.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2196c9463569dc43092b38de58e0103104efed0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443471"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro-eszköz cseréje

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk az Azure Stack Edge Pro-eszköz cseréjét ismerteti. Szükség van egy helyettesítő eszközre, ha a meglévő eszköz hardveres hibával rendelkezik, vagy frissítésre van szüksége. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Támogatási jegy megnyitása a hardveres probléma megoldásához
> * Új megrendelés létrehozása egy helyettesítő eszközhöz a Azure Portal
> * Telepítse, aktiválja a helyettesítő eszközt
> * Az eredeti eszköz visszaadása

## <a name="open-a-support-ticket"></a>Támogatási jegy megnyitása

Ha a meglévő eszköz hardverhiba miatt nem sikerül, nyisson meg egy támogatási jegyet a következő lépésekkel:

1. Nyisson meg egy támogatási jegyet Microsoft ügyfélszolgálata jelezve, hogy vissza kívánja adni az eszközt. Válassza ki a **Azure stack Edge Pro hardveres** probléma típusát, és válassza ki a **hardveres problémák** altípust.  

    ![Támogatási jegy megnyitása](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Egy Microsoft ügyfélszolgálata mérnök felveszi Önnel a kapcsolatot annak megállapításához, hogy egy mező kiváltási egysége (cserélhető) képes-e a probléma megoldására, és elérhető-e a példány számára. Ha a rendszer nem érhető el, vagy ha az eszköz hardveres frissítésre van szüksége, a támogatás végigvezeti egy új megrendelés megrendelésének és a régi eszköz visszaadásának lépésein.

## <a name="create-a-new-order"></a>Új megrendelés létrehozása

Hozzon létre egy új erőforrást a helyettesítő eszköz aktiválásához az [új erőforrás létrehozása](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)című témakör lépéseit követve.

> [!NOTE]
> Egy helyettesítő eszköz meglévő erőforráson való aktiválása nem támogatott. Az új erőforrás új megrendelésnek tekintendő. Az eszköz elszállítása után a számlázás 14 nappal kezdődik.

## <a name="install-and-activate-the-replacement-device"></a>A helyettesítő eszköz telepítése és aktiválása

A következő lépésekkel telepítheti és aktiválhatja a helyettesítő eszközt:

1. [Telepítse az eszközt](azure-stack-edge-deploy-install.md).
2. [Aktiválja az eszközt](azure-stack-edge-deploy-connect-setup-activate.md) a korábban létrehozott új erőforrással.

## <a name="return-your-existing-device"></a>Meglévő eszköz visszaküldése

Az eredeti eszköz visszaküldéséhez kövesse az összes lépést:

1. [Az eszközön lévő adat törlése](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. Az [eszköz visszaküldésének kezdeményezése](azure-stack-edge-return-device.md#initiate-device-return) az eredeti eszközhöz.
3. [Pickup beosztása](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Miután az eszköz Beérkezett a Microsoftnál, [törölheti a](azure-stack-edge-return-device.md#delete-the-resource) visszaadott eszközhöz társított erőforrást.


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan adhat [vissza Azure stack Edge Pro-eszközt](azure-stack-edge-return-device.md).
