---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 99dcfa46280c6fc00b27fa43fd6079c4ac32bd3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704680"
---
### <a name="delete-azure-resources"></a>Azure-erőforrások törlése 

Az Azure-erőforrások és -erőforráscsoportok törlése visszafordíthatatlan. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT hub-t egy olyan meglévő erőforráscsoport belsejében hozta létre, amely a megőrizni kívánt erőforrásokkal rendelkezik, akkor csak az IoT hub-erőforrást törölje, ne az erőforráscsoportot.

Az erőforrások törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. Válassza ki az IoT Edge teszterőforrásokat tartalmazó erőforráscsoport nevét. 

3. Tekintse át az erőforráscsoport erőforrásainak listáját. Ha mindet törölni szeretné, válassza az **Erőforráscsoport törlése** lehetőséget. Ha csak bizonyos elemeket szeretne törölni, az egyes erőforrásokra kattintva külön törölheti őket. 
