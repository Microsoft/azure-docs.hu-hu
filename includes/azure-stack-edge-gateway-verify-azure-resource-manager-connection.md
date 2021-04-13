---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305556"
---
Ellenőrizze, hogy a következő lépések használhatók-e az eszköz ügyfélről való eléréséhez.

Ellenőrizze, hogy az ügyfél tud-e csatlakozni a helyi Azure Resource Managerhoz. 

1. Helyi eszköz API-k hívása a hitelesítéshez:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Adja meg a felhasználónevet `EdgeArmUser` és a jelszót Azure Resource Manager használatával történő kapcsolódáshoz. Ha nem emlékszik a jelszóra, [állítsa Alaphelyzetbe Azure Resource Manager jelszavát](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) , és ezt a jelszót használja a bejelentkezéshez.