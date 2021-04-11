---
title: Az Azure térbeli horgonyok áttekintése
description: Ismerje meg, hogy az Azure térbeli horgonyok hogyan segíthetnek a többplatformos vegyes valóságok fejlesztésében.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0b4c3b281f2eac6263ef578dee5419bd9f2eb8a7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058561"
---
# <a name="azure-spatial-anchors-overview"></a>Az Azure térbeli horgonyok áttekintése

Üdvözli az Azure térbeli horgonyok. Az Azure térbeli alapjai lehetővé teszik a fejlesztők számára, hogy az alapvető képességekkel rendelkezzenek a térbelien tudatos, vegyes valóságot használó alkalmazások létrehozásához. Ezek az alkalmazások támogathatják a Microsoft HoloLens, az ARKit-t támogató iOS-alapú eszközöket és az ARCore-t támogató Android-alapú eszközöket. Az Azure Spatial Anchorsben a fejlesztők vegyesvalóság-platformokat használhatnak a terek érzékeléséhez, a fontos helyek pontos kijelöléséhez, valamint a támogatott eszközökről származó fontos helyek felidézéséhez.
Ezeket a pontos érdeklődési pontokat térbeli Horgonyoknak nevezzük.

![Platformfüggetlen](./media/cross-platform.png)

## <a name="examples"></a>Példák

Néhány példa a térbeli horgonyok által engedélyezett használati esetekre:

- [Többfelhasználós élmény](tutorials/tutorial-share-anchors-across-devices.md). Az Azure térbeli horgonyok megkönnyítik a felhasználók számára, hogy részt vegyenek a többfelhasználós vegyes valóság alkalmazásokban. A két személy például elindíthatja a Mixed Reality Chess játékot úgy, hogy egy virtuális sakk-táblát helyez el egy táblán. Ezt követően az eszközük a táblázatban látható módon megtekinthető és használható a virtuális sakk-táblával együtt.

- [Módszer – keresés](concepts/anchor-relationships-way-finding.md). A fejlesztők összekapcsolhatják a térbeli horgonyokat, és egymás között kapcsolatokat hoznak létre. Előfordulhat például, hogy egy alkalmazás olyan élményt tartalmaz, amely két vagy több olyan hasznos ponttal rendelkezik, amelyekkel a felhasználónak működnie kell a feladat elvégzéséhez. Ezek a pontok a kapcsolódó módon hozhatók létre. Később, amikor a felhasználó elvégezte a többlépéses feladatot, az alkalmazás kérheti az aktuális közelében lévő horgonyokat, hogy a feladat következő lépése felé irányítsa a felhasználót.

- [Virtuális tartalmak megőrzése a valós világban](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Egy alkalmazás lehetővé teheti a felhasználók számára, hogy egy konferenciaterem falán helyezik üzembe a virtuális naptárakat, így a felhasználók telefonos alkalmazást vagy HoloLens-eszközt is láthatnak. Ipari környezetben a felhasználók környezeti információkat kaphatnak egy gépről, ha egy támogatott eszköz kamerájával felé mutatnak.

Az Azure Spatial Anchors egy felügyelt szolgáltatásból és a támogatott eszközplatformok ügyféloldali SDK-iból áll. A következő szakaszokban részletesen ismertetjük az Azure térbeli horgonyokat használó alkalmazások létrehozásának első lépéseit.

## <a name="next-steps"></a>Következő lépések

Hozza létre első alkalmazását az Azure térbeli Horgonyokkal.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
