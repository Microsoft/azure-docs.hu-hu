---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: cf67ea58c4c13756fdc6e437883f12124ca47eb9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105754"
---
1. A [Azure Portal](https://portal.azure.com/)válassza ki a Azure stack Edge-erőforrást, majd lépjen az **Áttekintés** elemre. Az eszköznek online állapotban kell lennie. Nyissa meg a **Cloud Storage-átjáró > Storage-fiókok** lapot.

2. Válassza ki a **+ Storage-fiók hozzáadása** lehetőséget az eszköz parancssáv-sávján. 

   ![Storage-fiók hozzáadása](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. Az **Edge Storage-fiók hozzáadása** panelen adja meg a következő beállításokat:

    1. Adja meg az eszközön az Edge Storage-fiók egyedi nevét. A Storage-fiókok nevei csak kisbetűket és betűket tartalmazhatnak. A speciális karakterek használata nem engedélyezett. A Storage-fiók nevének egyedinek kell lennie az eszközön belül (az eszközökön kívül).

    2. Adja meg a Storage-fiókban tárolt adatokra vonatkozó információk opcionális leírását.  
    
    3. Alapértelmezés szerint a Edge Storage-fiók a felhőben egy Azure Storage-fiókra van leképezve, a Storage-fiókból származó adatok pedig automatikusan a felhőbe kerülnek. Adja meg azt az Azure Storage-fiókot, amelyhez a peremhálózati tárolóeszköz hozzá van rendelve.

    4. Hozzon létre egy új tárolót, vagy válasszon egy meglévő tárolóból az Azure Storage-fiókban. A rendszer automatikusan feltölti az eszközről az Edge Storage-fiókba írt összes olyan adatforrást, amely a hozzárendelt Azure Storage-fiókban található kiválasztott tárolóba kerül.

    5. Miután megadta a Storage-fiók összes beállítását, kattintson a **Hozzáadás** gombra a peremhálózati Storage-fiók létrehozásához. A rendszer értesítést küld, ha a peremhálózati Storage-fiók létrehozása sikeres volt. Ekkor megjelenik az új Edge Storage-fiók a Azure Portal a Storage-fiókok listájában.

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. Ha ezt az új Storage-fiókot választja, és a **hozzáférési kulcsok** lehetőségre kattint, megkeresheti a blob Service-végpontot és a hozzá tartozó Storage-fiók nevét. Másolja ezeket az adatokat, mivel ezek az értékek és a hozzáférési kulcsok is segítenek csatlakozni az Edge Storage-fiókhoz.

    ![2. Storage-fiók hozzáadása](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    A hozzáférési kulcsokat úgy érheti el, ha [a Azure Resource Manager használatával csatlakozik az eszköz helyi API-](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md)khoz.