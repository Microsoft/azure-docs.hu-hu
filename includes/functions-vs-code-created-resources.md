---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493480"
---
+ Egy [erőforráscsoport](../articles/azure-resource-manager/management/overview.md), amely a kapcsolódó erőforrások logikai tárolója.
+ Szabványos [Azure Storage-fiók](../articles/storage/common/storage-account-create.md), amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
+ Egy használati terv, amely a kiszolgáló nélküli Function alkalmazás mögöttes gazdagépét határozza meg. 
+ Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az azonos üzemeltetési csomagban található erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.
+ A Function alkalmazáshoz csatlakoztatott Application Insights-példány, amely a kiszolgáló nélküli függvény használatát követi nyomon.