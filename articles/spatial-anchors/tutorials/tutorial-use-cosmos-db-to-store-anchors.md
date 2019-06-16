---
title: Oktatóanyag – megosztás Azure térbeli horgonyok munkamenetek és az eszközök között egy Azure Cosmos DB-háttéralkalmazáshoz |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan Azure térbeli horgonyok azonosítók megosztása egy háttér-Service-szel és az Azure Cosmos DB a Unity Android vagy iOS-eszközök.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: fc172b5327d72687fea7d13ddb706ecc7ab630b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135344"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Oktatóanyag: Megosztás Azure térbeli horgonyok munkamenetek és az eszközök között egy Azure Cosmos DB-háttéralkalmazáshoz

Ebben az oktatóanyagban, megtudhatja, hogyan használandó [Azure térbeli horgonyok](../overview.md) horgonyokat létrehozni egy munkamenet során, és keresse meg azokat a munkamenetben egy másik, ugyanarra az eszközre, illetve egy másikat. A második munkamenet Előfordulhat például, egy másik napon. Ezek azonos kapcsolati alapokat is ugyanazon a helyen, és egyszerre több eszközön található.

![GIF szemléltető objektum adatmegőrzés](./media/persistence.gif)

[Az Azure térbeli horgonyok](../overview.md) platformfüggetlen fejlesztői szolgáltatás, amely segítségével hozzon létre vegyes valóság tapasztalatok objektumok helyükre kivonatuk eszközök idővel. Ha elkészült, lesz egy alkalmazást, amely két vagy több eszközhöz is telepíthető. Egy példány által létrehozott térbeli horgonyok fog másokkal a azonosítókról az Azure Cosmos DB használatával.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Telepítse az ASP.NET Core-webalkalmazás az Azure-ban, amely a központi jellegűek, megosztására használható az Azure Cosmos DB tárolja őket.
> * Adja meg a AzureSpatialAnchorsLocalSharedDemo jelenet kihasználásához a megosztás horgonyok webalkalmazást az Azure-gyorssablonok a Unity minta.
> * Egy vagy több eszköz telepítenek egy alkalmazást, és futtathatja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Érdemes megjegyezni, hogy, bár fogja használni az Azure Cosmos DB és a Unity ebben az oktatóanyagban, azaz csak, amelyek különböző eszközökön térbeli horgonyok azonosítók megosztása egy példát. Lehetőség van felhasználó más nyelv és a egy cél elérése érdekében a háttér-technológiákkal. Az ASP.NET Core-webalkalmazás a jelen oktatóanyagban használt is, a .NET Core SDK 2.2-es van szükség. Rendben fut a Web Apps for Windows, de nem Linux rendszeren jelenleg futnak webalkalmazásokat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Másolás a `Connection String` mert, szüksége lesz rá.

## <a name="download-the-unity-sample-project"></a>Töltse le a Unity mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatás üzembe helyezése

Nyissa meg a Visual Studióban, és a nyissa meg a projektre a a `Sharing\SharingServiceSample` mappát.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Konfigurálja a szolgáltatást, hogy az Azure Cosmos DB-adatbázist használja

A **Megoldáskezelőben**, nyissa meg `SharingService\Startup.cs`.

Keresse meg `#define INMEMORY_DEMO` a fájl- és megjegyzés, amely ki sor elején. Mentse a fájlt.

A **Megoldáskezelőben**, nyissa meg `SharingService\appsettings.json`.

Keresse meg a `StorageConnectionString` tulajdonságot, és adja meg kell egyeznie a `Connection String` érték, amely a másolt a [hozzon létre egy adatbázis-fiók lépés](#create-a-database-account). Mentse a fájlt.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Cosmos DB felhasználta megosztani a forráshorgony azonosítók különböző eszközökön. Azure térbeli horgonyok egy új Unity HoloLens-alkalmazás használatával kapcsolatos további információkért folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Új Android-alkalmazás indítása](./tutorial-new-unity-hololens-app.md)