---
title: React-alkalmazás összeállítása felhasználók Face-szolgáltatáshoz való hozzáadásához
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan állíthatja be a fejlesztési környezetet, és hogyan helyezhet üzembe egy Face-alkalmazást az ügyfelek beleegyezésének lekért támogatáshoz.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505105"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>React-alkalmazás összeállítása felhasználók Face-szolgáltatáshoz való hozzáadásához

Ez az útmutató bemutatja a minta Face regisztrációs alkalmazás első lépésekre vonatkozó útmutatóját. Az alkalmazás ajánlott eljárásokat mutat be arra, hogyan lehet értelmes beleegyezést szerezni a felhasználók arcfelismerési szolgáltatásba való felvételéhez és a nagy pontosságú arcadatok beszerzéséhez. Egy integrált rendszer egy ilyen alkalmazással érintés nélküli hozzáférés-vezérlést, személyazonosság-ellenőrzést, követési követést, személyre szabási kioszkot vagy személyazonosság-ellenőrzést nyújthat az arcadatok alapján.

Az alkalmazás indításakor megjelenik a felhasználók részletes hozzájárulási képernyője. Ha a felhasználó beleegyezést ad, az alkalmazás felhasználónevet és jelszót kér, majd egy kiváló minőségű arcképet rögzít az eszköz kamerájával.

A mintaalkalmazás a JavaScript és a React Native keretrendszer használatával íródott. Jelenleg Android-eszközökön telepíthető; A jövőben további üzembe helyezési lehetőségek is elérhetőek.

## <a name="prerequisites"></a>Előfeltételek 

* Azure-előfizetés – [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/cognitive-services/)  
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Face-erőforrást [a](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Azure Portal a kulcs és a végpont lekért létrehozásához. Az üzembe helyezés után válassza az **Erőforráshoz ugrás lehetőséget.**  
  * Az alkalmazás Face API-hoz való csatlakoztatásához szüksége lesz a létrehozott erőforrás kulcsára és végpontjára.  
  * Helyi fejlesztéshez és teszteléshez beillesztheti az API-kulcsot és a végpontot a konfigurációs fájlba. A végső üzembe helyezéshez tárolja az API-kulcsot biztonságos helyen, és soha ne a kódban.  

> [!IMPORTANT]
> Ezek az előfizetői kulcsok a Cognitive Service API eléréséhez használhatók. Ne ossza meg a kulcsokat. Biztonságosan tárolhatja őket, például a Azure Key Vault. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. Az API-hívásokhoz csak egy kulcs szükséges. Az első kulcs újragenerálásakor használhatja a második kulcsot a szolgáltatáshoz való folyamatos hozzáféréshez.

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

1. Klónozza a mintaalkalmazás [git-adattárát.](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)
1. A fejlesztési környezet beállítását a <a href="https://reactnative.dev/docs/environment-setup"  title=" React Native react native dokumentációjában "  target="_blank"> találhatja </a> meg. Fejlesztői operációs rendszerként válassza a **React Native CLI Quickstart (React Natív CLI** rövid útmutató) lehetőséget, cél operációs rendszerként pedig az **Android** lehetőséget. Töltse ki a **Függőségek és az** **Android fejlesztési környezet telepítése szakaszt.**
1. Nyissa meg env.jsfájlt a kívánt szövegszerkesztőben, például a [Visual Studio Code](https://code.visualstudio.com/)-ban, és adja hozzá a végpontot és a kulcsot. A végpontot és a kulcsot a Azure Portal az erőforrás **Áttekintés** lapján. Ez a lépés csak helyi tesztelési célokra szolgál, és ne ellenőrizze a Face API-kulcsot a &mdash; távoli adattárban.
1. Futtassa az alkalmazást az androidos virtuáliseszköz-emulátorsal Android Studio vagy a saját Android-eszközével. Az alkalmazás fizikai eszközön való teszteléséhez kövesse a React Native react <a href="https://reactnative.dev/docs/running-on-device"  title=" "  target="_blank"> native </a> dokumentációját.  


## <a name="create-a-user-add-experience"></a>Felhasználói élmény létrehozása  

Most, hogy beállította a mintaalkalmazást, testre szabhatja a saját igényeinek megfelelően.

Előfordulhat például, hogy helyzetspecifikus információkat szeretne hozzáadni a hozzájárulási oldalon:

> [!div class="mx-imgBorder"]
> ![alkalmazás-hozzájárulási oldal](./media/enrollment-app/1-consent-1.jpg)

A szolgáltatás képminőség-ellenőrzéseket biztosít, hogy eldöntse, a kép megfelelő minőségű-e az ügyfél hozzáadásához vagy arcfelismerési kísérlethez. Ez az alkalmazás bemutatja, hogyan férhet hozzá a képkockákhoz az eszköz kamerájával, hogyan választhatja ki a legjobb minőségű képkockákat, és hogyan adjuk hozzá az észlelt arcot a Face API szolgáltatáshoz. 

Számos arcfelismerési problémát az alacsony minőségű referenciaképek okának okoz. Néhány tényező, amely ronthatja a modell teljesítményét:
* Arcméret (a kamerától távoli arcok)
* Arc tájolása (a kamerától elforgatott vagy dőlt arcok)
* Gyenge megvilágítási körülmények (alacsony fényviszony vagy háttérvilágítás), ahol előfordulhat, hogy a kép nem megfelelően van kitéve, vagy túl nagy a zaj
* Eltömöredés (részlegesen rejtett vagy ömlött arcok), beleértve az olyan kiegészítőket, mint a szemüveg vagy a vastag szemüveg)
* Elmosódás (például a fénykép gyors mozgása). 

> [!div class="mx-imgBorder"]
> ![alkalmazáskép-rögzítési utasítás oldala](./media/enrollment-app/4-instruction.jpg)

Figyelje meg, hogy az alkalmazás a felhasználói adatok törlésére és az újra hozzáadásra vonatkozó lehetőséget is kínál.

> [!div class="mx-imgBorder"]
> ![profilkezelési oldal](./media/enrollment-app/10-manage-2.jpg)

Az alkalmazás funkcióinak a teljes körű funkcionalitásra [](enrollment-overview.md) való kiterjesztéséhez olvassa el a megvalósítható további funkciók áttekintését és az ajánlott eljárásokat.

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

### <a name="android"></a>Android

Először győződjön meg arról, hogy az alkalmazás készen áll az éles környezetben való üzembe helyezésre: távolítsa el a kulcsokat és titkos kódokat az alkalmazás kódból, és győződjön meg arról, hogy követte az ajánlott [biztonsági eljárásokat.](../cognitive-services-security.md?tabs=command-line%2ccsharp)

Amikor készen áll az alkalmazás éles környezetben való kiadására, létre fog hozni egy kiadásra kész APK-fájlt, amely az Android-alkalmazások csomagfájlformátuma. Az APK-fájlt titkos kulccsal kell aláírni. Ebben a kiadási buildben közvetlenül megkezdheti az alkalmazás terjesztését az eszközökre. 

Kövesse a Prepare for release Prepare for release documentation (Felkészülés a kiadásra) dokumentációt a titkos kulcs létrehozásához, az alkalmazás aláírásához és a kiadási <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> </a> APK létrehozásához.  

Az aláírt APK létrehozása után az Alkalmazás közzététele Az alkalmazás közzététele dokumentációban talál további információt az alkalmazás <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> </a> kiadásának mikéntjéhez.

## <a name="next-steps"></a>Következő lépések  

Ebben az útmutatóban megtanulta, hogyan állíthatja be a fejlesztési környezetet, és hogyan kezdte el a mintaalkalmazást. Ha most ismerkedik a React Native [](https://reactnative.dev/docs/getting-started) alkalmazásokkal, olvassa el az első lépésekhez szükséges dokumentumokban további háttérinformációkat. Hasznos lehet megismerkedni a [Face API-val is.](Overview.md) A fejlesztés megkezdése előtt olvassa el a felhasználók hozzáadásának további szakaszait.