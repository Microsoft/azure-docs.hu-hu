---
title: Az Azure Functions árképzése
description: Megtudhatja, hogyan működik a számlázás a Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: cde1c5ee45edb4472bed992ee9ac4075f8ad05ff
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002157"
---
# <a name="azure-functions-pricing"></a>Az Azure Functions árképzése

Azure Functions háromféle díjszabási csomaggal rendelkezik. Válassza azt, amelyik leginkább megfelel az igényeinek:

- **Felhasználási terv**: az Azure biztosítja az összes szükséges számítási erőforrást. Nem kell aggódnia az erőforrás-kezeléssel kapcsolatban, és csak a kód futási idejéért kell fizetnie.

- **Prémium csomag**: megadhatja az előre bemelegítő példányok számát, amelyek mindig online állapotban vannak, és azonnal válaszolnak. A függvény futtatásakor az Azure minden további szükséges számítási erőforrást biztosít. A folyamatosan üzemelő, előre bemelegítő példányok és az Azure-ban használt további példányok kell fizetnie.

- **App Service terv**: a függvények futtatása ugyanúgy, mint a webalkalmazások. Ha a többi alkalmazáshoz App Service használ, a függvények külön díj nélkül is futtathatók ugyanazon a csomagon.

További információt a szolgáltatási csomagokról [az Azure Functions szolgáltatási csomagok áttekintésében](functions-scale.md) talál. Az árképzés további részleteiért lásd [a Functions díjszabási oldalát](https://azure.microsoft.com/pricing/details/functions/).
