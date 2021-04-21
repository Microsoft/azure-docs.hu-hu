---
author: baanders
description: fájlba foglalhatja a Azure Digital Twins tisztítását
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800254"
---
* **Ha nincs szüksége** az oktatóanyagban létrehozott erőforrásokra, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti a Azure Digital Twins-példányt és az összes többi erőforrást a cikkből. Ezzel törli az erőforráscsoport összes Azure-erőforrását, valamint magát az erőforráscsoportot is.
    
    > [!IMPORTANT]
    > Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön.
    
    Nyissa [Azure Cloud Shell,](https://shell.azure.com)és futtassa a következő parancsot az erőforráscsoport és minden benne található fájl törléséhez.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```