---
title: 'Oktatóanyag: a ellenőrizhető hitelesítő adatok használatának első lépései egy minta alkalmazás használatával (előzetes verzió)'
description: Ebből az oktatóanyagból megtudhatja, hogyan adhat meg ellenőrizhető hitelesítő adatokat a minta alkalmazás és a tesztelési bérlő használatával
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 03/31/2021
ms.openlocfilehash: 4914df0df30ed990dd2c692f0f5b57586d2a75c6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106171918"
---
# <a name="tutorial-get-started-with-verifiable-credentials-using-a-sample-app-preview"></a>Oktatóanyag: a ellenőrizhető hitelesítő adatok használatának első lépései egy minta alkalmazás használatával (előzetes verzió)

Ebben az oktatóanyagban átvesszük az első ellenőrizhető hitelesítő adatok kijavításához szükséges lépéseket: ellenőrzött hitelesítő adatok szakértői kártya. Ezután ezzel a kártyával igazolhatja, hogy a hitelesítő adatokkal rendelkező hitelesítő adat szakértője a digitális hitelesítő adatoknak. Ismerkedjen meg Azure Active Directory ellenőrizhető hitelesítő adatokkal a ellenőrizhető hitelesítő adatok minta alkalmazás használatával, hogy kiadja az első ellenőrizhető hitelesítő adatait.

![Ez egy példaként szolgáló kártya képe](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

- A [NodeJS](https://nodejs.org/en/download/) 10,14-es vagy újabb verziója van telepítve a tesztelési rendszeren.
- A [git](https://git-scm.com/downloads) telepítése szükséges, ha a minta alkalmazást futtató adattár klónozását szeretné végezni.
- [Visual Studio Code](https://code.visualstudio.com/Download)
- A minta webhelyét üzemeltető rendszer.
- Microsoft Authenticator verziójú 6.2005.3599 vagy újabb verziót futtató mobileszköz.
- [NGROK](https://ngrok.com/) ingyenes.

## <a name="download-the-sample-code"></a>A mintakód letöltése

Ha hitelesített hitelesítő adatokat tartalmazó szakértői kártyát szeretne kiadni, egy webhelyet kell futtatnia a helyi gépen. A webhely egy ellenőrizhető hitelesítő adat-kiállítási folyamat kezdeményezésére szolgál. Egy NodeJS-ben írt egyszerű webhelyet adtunk meg, amelyet az oktatóanyag során használunk.

Először töltse le [a githubról](https://github.com/Azure-Samples/active-directory-verifiable-credentials)a githubról, vagy a tárházat a helyi gépre:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Érdemes megismerkedjen a minta webhelyein található kóddal. A `issuer` mappa tartalmazza a ellenőrizhető hitelesítő adatok kibocsátásához használt összes kódot. További részletek a minta [readme](https://github.com/Azure-Samples/active-directory-verifiable-credentials)-ban olvashatók.

## <a name="run-the-issuer-website"></a>A kiállító webhelyének futtatása

A lépéseket a Visual Studio Code-ból vagy az operációs rendszeren elérhető bármely parancssorból is futtathatja. 

1. Lépjen a `issuer` mappába. 

    ```terminal
    cd issuer
    ```

2. Ha az összes szükséges csomagot telepíteni kell, és el kell indítani a helyet.

   ```terminal
    npm install
    node app.js
    ```

3. A terminálon láthatja, hogy a kiállító alkalmazás figyeli a 8081-es portot. Most hozzon létre egy fordított proxyt a Ngrok, így a hitelesítő képes kommunikálni az alkalmazással. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Fordított proxy létrehozása a Ngrok

A minta webhely futtatásakor az eszköznek kommunikálnia kell a helyi gépen futó csomópont-kiszolgálóval. Javasoljuk, hogy a [ngrok](https://ngrok.com/) használatával egyszerűen elérhetővé tegye a helyi fejlesztési kiszolgálót az interneten keresztül.

1.  A **ngrok** letöltése és kinyerése után a következőket kell futtatni:

    ```terminal
     ngrok http 8081
    ```

Alapértelmezés szerint a minta webhely a porton fut `8081` . A **Ngrok** két továbbító URL-címet küld a kiszolgálónak. Másolja az URL-címet az `https://` előtaggal.

![a ngrok segítségével elérhetővé teheti az alkalmazás végponti pontjait az interneten keresztül](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Ha a PowerShellt használja, lehet, hogy be kell írnia, hogy `./ngrok` a parancs felismerhető legyen.

Most, hogy a helyi port elérhetővé vált az interneten az ngrok használatával, a minta hely automatikusan a ngrok által generált állomásnevet használja. Nyissa meg a böngészőt, és navigáljon a ngrok HTTPS-továbbítási URL-címéhez. A minta webhely kezdőlapjának sikeres megkereséséhez. Ha megnyílik az oldal, az eszköz képes kommunikálni a helyi kiszolgálón futó minta alkalmazással. Most már készen áll arra, hogy egy ellenőrzött hitelesítő adatokat tartalmazó szakértői kártyát bocsásson ki.

## <a name="issue-a-credential"></a>Hitelesítő adat kiadása

1. Telepítse a hitelesítő eszközt a mobileszközön. Microsoft Authenticator a ellenőrizhető hitelesítő adatok fogadására, tárolására és bemutatására szolgál az érdekelt felek számára.

2. Ezután állítson be egy ellenőrizhető hitelesítő adatot. **Kattintson** a **hitelesítő adatok beolvasása** gombra. Amikor a **hitelesítő adatok lekérése** gombra kattint, a minta webhely egy QR-kódot jelenít meg, amelyet a hitelesítő használatával lehet beolvasni. Ha a webhelyet a mobileszköz böngészőjében tekinti meg, a **hitelesítő adatok beolvasása** gombra kattintva megnyithatja a hitelesítő alkalmazást, és nem IGÉNYLI a QR-kód vizsgálatát.

   ![Hitelesítő adatok beolvasása gomb](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Ellenőrizze a webhely QR-kódját a hitelesítő használatával, vagy ha egy mobilon keresztül éri el a webhelyet, kattintson a hitelesítő adatok beolvasása gombra a mély hivatkozás elindításához. 

   ![QR-kód ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Figyelje meg, hogy a **Hozzáadás** gomb jelenleg szürkén jelenik meg. Válassza a **Bejelentkezés a fiókhoz** lehetőséget a kártya képe alatt.

   ![Bejelentkezés ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. A hitelesítő adatok szakértői kártyájának beszerzése előtt a használt bérlőnek meg kell adnia a hitelesítési adatokat. Ha ez az első alkalom, hogy az oktatóanyag nem rendelkezik hitelesítő adatokkal, hozzon létre egy új felhasználói fiókot a B2C-bérlőben.

   ![hitelesítés a folytatás előtt](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Miután bejelentkezett, a **Hozzáadás** gomb már nem szürkén jelenik meg. A **Hozzáadás** gombra kattintva fogadja el az új virtuális merevlemezt.

   ![A hitelesítés után válassza a Hozzáadás lehetőséget](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Gratulálunk! Most már rendelkezik egy ellenőrzött hitelesítő adatokkal rendelkező VC-val.

   ![Hitelesítő adatokkal foglalkozó szakértő VC hozzáadva](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Ezután itt az ideje, hogy ellenőrizze a hitelesítő adatait.

## <a name="validate-credentials"></a>Hitelesítő adatok érvényesítése

Most, hogy elvégezte az oktatóanyag kiállítási részét, és rendelkezik egy ellenőrizhető hitelesítő adatokkal a hitelesítő, ideje, hogy érvényesítse a saját ellenőrző alkalmazásban.

1.  Állítsa le a kiállítói ngrok szolgáltatás futtatását.

    ```terminal
     control+c
    ```


2. Egy másik terminál ablakban nyissa meg az ellenőrző alkalmazás mappáját, és futtassa azt hasonlóan a kiállító alkalmazás futtatásához.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Most futtassa a ngrok-t az ellenőrző 8082-as porton.

    ```terminal
    ngrok http 8082
    ```

4. Nyissa meg a ngrok HTTPS-továbbítási URL-címét a böngészőben, és koppintson a **hitelesítő adatok ellenőrzése** gombra.  

   ![hitelesítő adatok szakértője gombjának ellenőrzése](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Nyissa meg a hitelesítő, és vizsgálja meg a QR-kódot.

   ![QR-kód bevizsgálása a hitelesítő adatok ellenőrzéséhez](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > IOS-en a jobb felső sarokban, az Androidon pedig a jobb alsó sarokban látható. Olvassa be a QR-kódot.

6. Válassza az **Engedélyezés lehetőséget** a hitelesítő új engedély kérés képernyőjén. Az engedélyezés gomb megnyomásával egy ellenőrizhető bemutatót kell aláírnia a DID-val (decentralizált azonosítóval) annak bizonyítására, hogy a hitelesítő adatokat valóban szabályozza.

   ![új engedély kérése](media/get-started-verifiable-credentials/new-permission-request.png)

    A sikeres bemutató három dolog után frissült:

   1. A weblapnak ekkor meg kell jelennie a "gratulálok, a neve" + egy ellenőrzött hitelesítő adat szakértője! ".
   
    ![Gratulálunk, ellenőrizze újra](media/get-started-verifiable-credentials/congratulations.png)


   2. Az ellenőrző alkalmazás termináljának ugyanezt az üzenetet is meg kell jelennie a naplókból.
   
    ![alkalmazás tevékenység a parancssorban](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. A hitelesítő alkalmazásban a bemutató legutóbbi tevékenységéhez bejegyzésnek kell szerepelnie.

    ![Tevékenység a hitelesítő](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Az ellenőrző alkalmazás futtatása közben előfordulhat, hogy a ngrok nem működik, és olyan hibát jelez, hogy túl sok kapcsolat van. Ezt a fiókot a ngrok-Fiók regisztrálásával lehet elkerülni. 

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen elvégezte a gyors üzembe helyezési útmutatót, ideje létrehozni saját decentralizált azonosítóját az Azure AD-ben ellenőrizhető hitelesítő adatok szolgáltatásban, és kiadja a saját ellenőrizhető hitelesítő adatait.

>[!div class="nextstepaction"] 
>[Saját kiállító konfigurálása a ellenőrizhető hitelesítő adatok minta alkalmazás használatával](./enable-your-tenant-verifiable-credentials.md)
