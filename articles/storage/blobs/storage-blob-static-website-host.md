---
title: 'Oktatóanyag: statikus webhely üzemeltetése a blob Storage-ban – Azure Storage'
description: Megtudhatja, hogyan konfigurálhat egy Storage-fiókot a statikus webhelyek üzemeltetéséhez, és hogyan helyezhet üzembe statikus webhelyeket az Azure Storage-ban.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 97679ec48ee9120005118b6cacaf37e45657db08
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906630"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Oktatóanyag: statikus webhely üzemeltetése Blob Storage

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe statikus webhelyeket az Azure Storage-ban. Ha elkészült, egy olyan statikus webhellyel fog rendelkezni, amelyet a felhasználók nyilvánosan el tudnak érni. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Statikus webhely üzemeltetésének konfigurálása
> * "Helló világ!" alkalmazás webhely üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Győződjön meg arról, hogy létrehoz egy általános célú v2-es Storage-fiókot. A statikus webhelyek semmilyen más típusú Storage-fiókban nem érhetők el.

Ez az oktatóanyag a [Visual Studio Code](https://code.visualstudio.com/download), a programozók számára ingyenes eszközt, a statikus webhely felépítését és az Azure Storage-fiókba való üzembe helyezését használja.

A Visual Studio Code telepítése után telepítse az Azure Storage Preview bővítményt. Ez a bővítmény az Azure Storage felügyeleti funkcióit integrálja a Visual Studio Code-ban. A bővítmény használatával helyezheti üzembe statikus webhelyét az Azure Storage-ban. A bővítmény telepítése:

1. Indítsa el a Visual Studio Code-ot.
2. Az eszköztáron kattintson a **bővítmények**elemre. Keressen rá az *Azure Storage*szolgáltatásra, és válassza ki az **Azure Storage** -bővítményt a listából. Ezután kattintson a **telepítés** gombra a bővítmény telepítéséhez.

    ![Az Azure Storage bővítmény telepítése a VS Code-ban](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A kezdéshez jelentkezzen be a [Azure Portalba](https://portal.azure.com/) .

## <a name="configure-static-website-hosting"></a>Statikus webhely üzemeltetésének konfigurálása

Első lépésként konfigurálja a Storage-fiókot egy statikus webhely üzemeltetéséhez a Azure Portalban. Ha a fiókját statikus webhely-üzemeltetésre konfigurálja, az Azure Storage automatikusan létrehoz egy *$web*nevű tárolót. A *$web* tároló a statikus webhely fájljait fogja tartalmazni. 

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) a böngészőben. 
1. Keresse meg a Storage-fiókját, és jelenítse meg a fiók áttekintését.
1. Válassza a **statikus webhely** lehetőséget a statikus webhelyek konfigurációs oldalának megjelenítéséhez.
1. Válassza az engedélyezve lehetőséget a statikus webhely-üzemeltetési **funkció** engedélyezéséhez a Storage-fiókhoz.
1. Az **index dokumentum neve** mezőben adja meg az *index. html*alapértelmezett index lapját. Az alapértelmezett index lap akkor jelenik meg, ha a felhasználó a statikus webhely gyökerére navigál.  
1. A **hiba-dokumentum elérési útja** mezőben a *404. html*alapértelmezett hibaüzenetét kell megadnia. Az alapértelmezett hiba lap akkor jelenik meg, ha a felhasználó olyan oldalra próbál navigálni, amely nem szerepel a statikus webhelyén.
1. Kattintson a **Mentés** gombra. A Azure Portal mostantól megjeleníti a statikus webhely végpontját. 

    ![Statikus webhely üzemeltetésének engedélyezése Storage-fiókhoz](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>"Helló világ!" alkalmazás webhely üzembe helyezése

Ezután hozzon létre egy "Helló világ!" alkalmazás weblapot a Visual Studio Code-ban, és telepítse azt az Azure Storage-fiókjában üzemeltetett statikus webhelyre.

1. Hozzon létre egy *mywebsite* nevű üres mappát a helyi fájlrendszerben. 
1. Indítsa el a Visual Studio Code-ot, és nyissa meg az imént létrehozott mappát az **Explorer** paneljén.

    ![Mappa megnyitása a Visual Studio Code-ban](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Hozza létre az alapértelmezett index fájlt a *mywebsite* mappában, és nevezze el az *index. html*nevet.

    ![Az alapértelmezett indexfájl létrehozása a Visual Studio Code-ban](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Nyissa meg az *index. html* fájlt a szerkesztőben, illessze be a következő szöveget a fájlba, majd mentse:

    ```
    <h1>Hello World!</h1>
    ```

1. Hozza létre az alapértelmezett hibaüzenetet, és nevezze el *404. html*néven.
1. Nyissa meg a *404. html* fájlt a szerkesztőben, illessze be a következő szöveget a fájlba, majd mentse:

    ```
    <h1>404</h1>
    ```

1. Kattintson a jobb gombbal a *mywebsite* mappára az **Explorer** panelen, és válassza a **üzembe helyezés statikus webhelyre** ... lehetőséget a webhely üzembe helyezéséhez. A rendszer kérni fogja, hogy jelentkezzen be az Azure-ba az előfizetések listájának lekéréséhez.

1. Válassza ki azt a Storage-fiókot tartalmazó előfizetést, amelyhez engedélyezte a statikus webhely üzemeltetését. Ezután válassza ki a Storage-fiókot, amikor a rendszer kéri.

A Visual Studio Code ekkor feltölti a fájlokat a webes végpontba, és megjeleníti a sikeres állapotjelző sávot. Indítsa el a webhelyet, és tekintse meg az Azure-ban.

![Statikus webhely üzembe helyezésének megtekintése az Azure-ban](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Sikeresen elvégezte az oktatóanyagot, és üzembe helyezett egy statikus webhelyet az Azure-ban.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan konfigurálhatja az Azure Storage-fiókját a statikus webhelyek üzemeltetéséhez, és hogyan hozhat létre és helyezhet üzembe statikus webhelyeket egy Azure-végponton.

Következő lépésként megtudhatja, hogyan konfigurálhat egy egyéni tartományt a statikus webhellyel.

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md)
