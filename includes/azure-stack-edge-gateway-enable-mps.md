---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102517562"
---
1. Mielőtt hozzákezd, győződjön meg az alábbiakról:

    1. [A Azure stack Edge Pro-eszközt](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) konfigurálta és aktiválta egy Azure stack Edge-erőforrással az Azure-ban.
    1. [Ezen az eszközön konfigurálta a számítást a Azure Portal](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Kapcsolódjon a PowerShell felületéhez](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Az alábbi paranccsal engedélyezheti az MPS használatát az eszközön.

    ```powershell
    Start-HcsGpuMPS
    ```