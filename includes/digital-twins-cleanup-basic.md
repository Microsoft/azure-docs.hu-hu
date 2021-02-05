---
author: baanders
description: fájl belefoglalása Azure Digital Twins-példányok tisztításához
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575685"
---
* **Ha nincs szüksége az oktatóanyagban létrehozott erőforrásokra**, törölheti az Azure Digital Twins-példányt és a cikk összes többi erőforrását az az [Group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) paranccsal. Ezzel törli az összes Azure-erőforrást egy erőforráscsoporthoz, valamint magát az erőforráscsoportot is.
    
    > [!IMPORTANT]
    > Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön.
    
    Nyissa meg [Azure Cloud Shell](https://shell.azure.com), és futtassa a következő parancsot az erőforráscsoport és a benne található összes elem törléséhez.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```