---
author: baanders
description: fájl belefoglalása Azure Digital Twins-példányok tisztításához
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244643"
---
* **Ha nincs szüksége az oktatóanyagban létrehozott erőforrásokra**, törölheti az Azure Digital Twins-példányt és a cikk összes többi erőforrását az az [Group delete](/cli/azure/group#az-group-delete) paranccsal. Ezzel törli az összes Azure-erőforrást egy erőforráscsoporthoz, valamint magát az erőforráscsoportot is.
    
    > [!IMPORTANT]
    > Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön.
    
    Nyissa meg [Azure Cloud Shell](https://shell.azure.com), és futtassa a következő parancsot az erőforráscsoport és a benne található összes elem törléséhez.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```