---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701414"
---
1. A függvény futtatásához nyomja le az <kbd>F5</kbd> billentyűt a Visual Studióban. Előfordulhat, hogy engedélyeznie kell egy tűzfal-kivételt, hogy az eszközök kezelni tudják a HTTP-kérelmeket. A függvény helyi futtatásakor a rendszer soha nem kényszeríti ki az engedélyezési szinteket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Fűzze hozzá a lekérdezési karakterláncot `?name=<YOUR_NAME>` Ehhez az URL-címhez, és futtassa a kérést. Az alábbi képen látható a böngészőben a függvény által visszaadott helyi GET kérelemre adott válasz: 

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. A hibakeresés leállításához nyomja le az F5 <kbd>billentyűt</kbd> + <kbd></kbd> a Visual Studióban.
