---
title: Beléptetési alkalmazás készítése az Androidhoz a reakcióval
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan állíthatja be a fejlesztési környezetet, és hogyan helyezhet üzembe egy Face beléptetési alkalmazást az ügyfelek beleegyezett.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 218579176b807bbdae85646f27eaa7f301d4b9a6
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428269"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Beléptetési alkalmazás készítése az Androidhoz a reakcióval

Ez az útmutató bemutatja, hogyan kezdheti meg a minta Face beléptetési alkalmazást. Az alkalmazás bemutatja az ajánlott eljárásokat a felhasználók Arcfelismerés szolgáltatásba való beléptetéséhez és a nagy pontosságú adatok beszerzéséhez. Az integrált rendszerek olyan beléptetési alkalmazást használhatnak, mint például az érintés nélküli hozzáférés-vezérlés, az identitás-ellenőrzés, a látogatottság nyomon követése, a személyre szabási kioszk vagy a személyazonosság-ellenőrzés, az adatok alapján.

Indításakor az alkalmazás részletes beleegyezési képernyőt jelenít meg a felhasználók számára. Ha a felhasználó beleegyezik, az alkalmazás kérni fogja a felhasználónevet és a jelszót, majd az eszköz kamerájának használatával rögzíti a kiváló minőségű arcképet.

A minta-beléptetési alkalmazást a JavaScript és a reakciós natív keretrendszer használatával kell megírni. Jelenleg Android-eszközökön helyezhető üzembe. További üzembe helyezési lehetőségek jönnek a jövőben.

## <a name="prerequisites"></a>Előfeltételek 

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/).  
* Ha már rendelkezik Azure-előfizetéssel, [hozzon létre egy arc-erőforrást](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz** lehetőséget.  
  * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához.  
  * Helyi fejlesztéshez és teszteléshez illessze be az API-kulcsot és a végpontot a konfigurációs fájlba. A végső telepítéshez tárolja az API-kulcsot biztonságos helyen, és soha ne a kódban.  

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás, például Azure Key Vault használata. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

1. A [minta beléptetési alkalmazás](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)git-tárházának klónozása.
1. A fejlesztési környezet beállításához kövesse a natív dokumentáció megválaszolása <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> natív dokumentációt </a> . Válassza a **NATÍV CLI** -gyors reagálás a fejlesztési operációs rendszerként lehetőséget, és válassza az **Android** lehetőséget célként szolgáló operációs rendszerként. Fejezze be a **függőségek** és az **androidos fejlesztői környezet** telepítését ismertető szakaszt.
1. Nyissa meg a env.jsfájlt a kívánt szövegszerkesztőben, például a [Visual Studio Code](https://code.visualstudio.com/)-ban, és adja hozzá a végpontot és a kulcsot. A végpontot és a kulcsot a Azure Portal az erőforrás **Áttekintés** lapján érheti el. Ez a lépés csak helyi tesztelési célokra szolgál, a &mdash; távoli tárházban nem kell bejelentkeznie a Face API-kulcsba.
1. Futtassa az alkalmazást az Android rendszerű virtuális eszköz emulátorával Android Studio vagy a saját Android-eszközéről. Ha egy fizikai eszközön szeretné tesztelni az alkalmazást, kövesse a kapcsolódó <a href="https://reactnative.dev/docs/running-on-device"  title=" reagáló natív dokumentációt a "  target="_blank"> natív dokumentációban </a> .  


## <a name="create-an-enrollment-experience"></a>Regisztrálási élmény létrehozása  

Most, hogy beállította a minta-beléptetési alkalmazást, testreszabhatja a saját regisztrációs élményének igényeihez.

Előfordulhat például, hogy az Ön beleegyező oldalára szeretné felvenni a helyzet-specifikus adatokat:

> [!div class="mx-imgBorder"]
> ![alkalmazás-engedélyezési oldal](./media/enrollment-app/1-consent-1.jpg)

A szolgáltatás képminőség-ellenőrzéseket biztosít, amelyek segítségével megadhatja, hogy a rendszerkép megfelelő minőségű-e az ügyfél regisztrálásához, vagy kísérletet tesz a felismerésre. Ez az alkalmazás bemutatja, hogyan érheti el a kereteket az eszköz kamerájában, kiválaszthatja a legmagasabb minőségi kereteket, és regisztrálhatja az észlelt arcot a Face API szolgáltatásban. 

Az Arcfelismerés számos problémáját az alacsony minőségű hivatkozási képek okozzák. A modell teljesítményének romlását befolyásoló tényezők:
* Oldalméret (arcok, amelyek távol vannak a kamerától)
* Szembenéző tájolás (a kamera felől megfordult vagy megdöntött arcok)
* Gyenge megvilágítási feltételek (akár kis fény, akár háttérvilágítás), ahol a rendszerkép valószínűleg nem megfelelő, vagy túl sok zajt tartalmaz
* Elzáródás (részlegesen rejtett vagy akadályozatlan arcok), beleértve a kalapokat vagy a vastag peremű szemüvegeket is.
* Életlenítés (például a fénykép készítésekor a gyors Face mozgással). 

> [!div class="mx-imgBorder"]
> ![az alkalmazás lemezkép-rögzítési utasításának lapja](./media/enrollment-app/4-instruction.jpg)

Figyelje meg, hogy az alkalmazás a felhasználó regisztrációjának törlését és az újbóli regisztrálás lehetőségét is biztosítja.

> [!div class="mx-imgBorder"]
> ![profil kezelése lap](./media/enrollment-app/10-manage-2.jpg)

Ha ki szeretné terjeszteni az alkalmazás funkcióit a teljes regisztrációs élményre, olvassa el az [áttekintést](enrollment-overview.md) további funkciók megvalósításához és az ajánlott eljárásokhoz.

## <a name="deploy-the-enrollment-app"></a>A beléptetési alkalmazás üzembe helyezése

### <a name="android"></a>Android

Először győződjön meg arról, hogy az alkalmazás készen áll az éles környezetben való üzembe helyezésre: távolítsa el az összes kulcsot vagy titkot az alkalmazás kódjából, és győződjön meg arról, hogy követte az [ajánlott biztonsági eljárásokat](../cognitive-services-security.md?tabs=command-line%2ccsharp).

Ha készen áll az alkalmazás éles környezetben való kiadására, létrehoz egy kiadásra kész APK-fájlt, amely az Android-alkalmazások csomag fájlformátuma. Ezt az APK-fájlt titkos kulccsal kell aláírni. Ezzel a kiadással megkezdheti az alkalmazás közvetlen terjesztését az eszközökön. 

A kiadási <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" dokumentáció előkészítésének előkészítése "  target="_blank"> </a> című témakörben megtudhatja, hogyan hozhatja ki a titkos kulcsot, hogyan aláírhatja az alkalmazást, és hogyan hozhatja ki a kiadási apk-t.  

Miután létrehozott egy aláírt APK-t, tekintse meg az alkalmazás közzététele az alkalmazás közzététele című <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> </a> dokumentációt, amelyből többet tudhat meg az alkalmazás kiadásáról.

## <a name="next-steps"></a>Következő lépések  

Ebből az útmutatóból megtudhatta, hogyan állíthatja be a fejlesztési környezetet, és megkezdheti a minta-beléptetési alkalmazás megkezdését. Ha most ismerkedik a natív reagálással, olvassa el az [első lépéseket ismertető dokumentációt](https://reactnative.dev/docs/getting-started) , ahol további háttér-információkat tudhat meg. Hasznos lehet a [Face API](Overview.md)megismerése is. A fejlesztés megkezdése előtt olvassa el a beléptetési alkalmazás dokumentációjának egyéb fejezeteit.