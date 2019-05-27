---
title: Az első függvény létrehozása az Azure-ban a Visual Studióval
description: Hozzon létre és tegyen közzé HTTP-triggerrel aktivált Azure Functions-függvényt a Visual Studióval.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/17/2018
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 1c07da0121b8e350c55b139901d56cfe2133386d
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864503"
---
# <a name="create-your-first-function-using-visual-studio"></a>Az első függvény létrehozása a Visual Studio használatával

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást.

Ebből a cikkből megismerheti, hogyan helyi létrehozásához és a egy "hello world" függvény tesztelése az Azure Functions Visual Studio 2019 eszközei használatával. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök érhetők el a Visual Studio 2019 az Azure development workload munkafolyamatának részét képező.

![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

A témakör tartalmaz [egy videót](#watch-the-video) is, amely ugyanezeket az alapszintű lépéseket mutatja be.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítés [Visual Studio 2019](https://azure.microsoft.com/downloads/) és ellenőrizze, hogy a **Azure-fejlesztési** számítási feladatot is telepítve van.

* Győződjön meg arról, hogy rendelkezik a [legújabb Azure Functions-eszközökkel](functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

A Visual Studio létrehoz egy projektet, benne egy olyan osztállyal, amely tartalmazza a kiválasztott függvénytípus sablonkódját. A metódus **FunctionName** attribútuma adja meg a függvény nevét. A **HttpTrigger** attribútum adja meg, hogy a függvényt egy HTTP-kérelem aktiválja. A sablonkód elküld egy HTTP-választ, amely tartalmaz egy értéket a kérelem szövegtörzséből vagy a lekérdezési sztringből. A függvényhez további bemeneti és kimeneti kötések adhatók hozzá, ha a metóduson a megfelelő attribútumokat alkalmazza. További információkért lásd az [Azure Functions C#-fejlesztői referenciaanyagának](functions-dotnet-class-library.md) [Eseményindítók és kötések](functions-dotnet-class-library.md#triggers-and-bindings) szakaszát.

Most, hogy már létrehozott egy függvényprojektet és egy HTTP-eseményindítóval aktivált függvényt, tesztelheti a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-debugging.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az URL-címhez fűzze hozzá a `?name=<YOUR_NAME>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz: 

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local.png)

4. A hibakeresés leállításához nyomja le a **Shift + F5** billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzétételéhez rendelkeznie kell egy függvényalkalmazással.az Azure-előfizetéséhez. Közvetlenül a Visual Studióból is létrehozhat függvényalkalmazást.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a Publish (Közzététel) profiloldalról. Cserélje ki a függvény helyi tesztelésekor használt `localhost:port` URL-címrészt az új alap URL-címmel. Ahogyan korábban, most is az URL-címhez fűzze hozzá a `?name=<YOUR_NAME>` lekérdezési sztringet, és hajtsa végre a kérelmet.

    A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az alábbiakban látható a böngészőben a távoli GET kérelemre a függvény által visszaadott válasz:

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="watch-the-video"></a>Videó megtekintése

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>További lépések

A Visual Studióval létrehozott és közzétett egy HTTP által aktivált egyszerű függvényt tartalmazó C#-függvényalkalmazást.

* [Megtudhatja, hogyan adhat hozzá olyan bemeneti és kimeneti kötéseket, amelyek más szolgáltatásokkal is integrálhatók.](functions-develop-vs.md#add-bindings)
* [További tudnivalók a függvények .NET-osztálytkódtárként való fejlesztéséről](functions-dotnet-class-library.md).
