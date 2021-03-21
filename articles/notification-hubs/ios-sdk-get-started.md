---
title: Leküldéses értesítések küldése iOS-re az Azure Notification Hubs és az iOS SDK használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure Notification Hubs és az Apple push Notification szolgáltatás leküldéses értesítések küldésére iOS-eszközökre.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581735"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Oktatóanyag: leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az Azure Notification Hubst, és hogyan konfigurálhatja a hitelesítő adatokat egy iOS-eszközre az [Apple push Notification szolgáltatás (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)használatával. 

Az oktatóanyag futtatása a következő Objective C és Swift iOS-oktatóanyagok előfeltétele, és az alábbi lépéseket ismerteti:

- A tanúsítvány-aláírási kérelem fájljának előállítása.
- Kérje le az alkalmazást leküldéses értesítésekre.
- Hozzon létre egy létesítési profilt az alkalmazáshoz.
- Értesítési központ létrehozása.
- Konfigurálja az értesítési központot APNS-információkkal.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

A következőkre is szüksége lesz:

- Aktív [Apple fejlesztői](https://developer.apple.com/) fiók.
- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy iPhone vagy iPad, amely az iOS 10-es vagy újabb verzióját futtatja.
- A fizikai eszköz regisztrálva van az [Apple Portalon](https://developer.apple.com/) , és a tanúsítványhoz van társítva.

Ha még nem ismeri a szolgáltatást, olvassa el az [Azure Notification Hubs áttekintését](notification-hubs-push-notification-overview.md) .

> [!NOTE]
> Az értesítési központ csak a sandbox hitelesítési mód használatára lesz konfigurálva. Az éles számítási feladatokhoz ne használja ezt a hitelesítési módot.

## <a name="generate-the-certificate-signing-request-file"></a>A tanúsítvány-aláírási kérelem fájljának előállítása

Az Apple Push Notification szolgáltatás (APNS) tanúsítványokat használ a leküldéses értesítések hitelesítéséhez. Kövesse ezeket az utasításokat az értesítések küldéséhez és fogadásához szükséges leküldéses tanúsítvány létrehozásához. További információért lásd az [Apple Push Notification szolgáltatás](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) hivatalos dokumentációját.

A tanúsítvány-aláírási kérelem (CSR) fájljának létrehozása, amelyet az Apple az aláírt leküldéses tanúsítvány létrehozásához használ:

1. Futtassa a Kulcskarika-elérés eszközt Mac számítógépén. Megnyitható a **segédprogramok** mappából vagy a Kezdőpanel **másik** mappájából is.

2. Válassza a **kulcstartó-hozzáférés**, majd a **tanúsítvány** kibontása elemet, majd válassza **a tanúsítvány kérése a** hitelesítésszolgáltatótól lehetőséget.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Képernyőfelvétel: a tanúsítvány kérése a hitelesítésszolgáltató menüjéből.":::

   > [!NOTE]
   > Alapértelmezés szerint a kulcstartó-hozzáférés kiválasztja a lista első elemét. Ez akkor lehet probléma, ha a **tanúsítványok** kategória és az **Apple Worldwide fejlesztői kapcsolatok hitelesítésszolgáltatója** nem a lista első eleme. Győződjön meg arról, hogy rendelkezik egy nem kulcsfontosságú elemmel, vagy az **Apple Worldwide fejlesztői kapcsolatok hitelesítésszolgáltató** kulcs van kiválasztva a CSR (tanúsítvány-aláírási kérelem) létrehozása előtt.

3. Válassza ki a **felhasználói e-mail-címét**, írja be a **köznapi név** értékét, győződjön meg róla, hogy a **lemezre mentve** van, majd válassza a **Folytatás** lehetőséget. Hagyja üresen a **CA e-mail-címét** , mert nincs rá szükség.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Szükséges tanúsítványinformációk":::

4. Adja meg a CSR-fájl nevét a **Mentés másként** lehetőségnél, válassza ki a helyet a **hol**, majd válassza a **Mentés** lehetőséget.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Fájlnév kiválasztása":::

   Ez a művelet menti a CSR-fájlt a kiválasztott helyen. Az alapértelmezett hely az **asztali**. Jegyezze meg a fájlhoz választott helyet.

Ezután regisztrálja alkalmazását az Apple-szel, engedélyezze a leküldéses értesítéseket, és töltse fel az exportált CSR-t egy leküldéses tanúsítvány létrehozásához.

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

Ha leküldéses értesítéseket szeretne küldeni egy iOS-alkalmazásnak, regisztrálja alkalmazását az Apple-szel, és regisztráljon a leküldéses értesítésekre is.

1. Ha még nem regisztrálta az alkalmazást, keresse meg az [iOS-es kiépítési portált](https://go.microsoft.com/fwlink/p/?LinkId=272456) az Apple fejlesztői központban. Jelentkezzen be a portálra az Apple ID azonosítójával, és válassza az **azonosítók** lehetőséget. Ezután válassza **+** az új alkalmazás regisztrálását.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Alkalmazás-azonosítók lap":::

2. Az **új azonosító regisztrálása** képernyőn válassza az alkalmazás- **azonosítók** választógombot. Ezután válassza a **Folytatás** elemet.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Új azonosító oldal regisztrálása":::

3. Frissítse a következő három értéket az új alkalmazáshoz, majd kattintson a **Folytatás** gombra:

   - **Leírás**: adjon meg egy leíró nevet az alkalmazásnak.
   - **Csomag azonosítója**: adja meg az űrlap **szervezeti azonosítójának** egy köteg-azonosítóját. a terméknév az [alkalmazás-terjesztési útmutatóban](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)említett. A **szervezet azonosítójának** és a **terméknév** értékének meg kell egyeznie a Xcode-projekt létrehozásakor használt szervezeti azonosítóval és terméknév-névvel. Az alábbi képernyőképen a **NotificationHubs** érték a szervezet azonosítója, a **GetStarted** pedig a termék neve lesz. Győződjön meg arról, hogy a **csomag azonosítójának** értéke megegyezik a Xcode projekt értékével, hogy a Xcode a megfelelő közzétételi profilt használja.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Alkalmazás-azonosító regisztrálása":::

   - **Leküldéses értesítések**: a **képességek** szakaszban keresse meg a **leküldéses értesítések** lehetőséget.

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Új alkalmazás-azonosító regisztrálása":::

      Ez a művelet létrehozza az alkalmazás AZONOSÍTÓját, és kéri, hogy erősítse meg az adatokat. Válassza a **Folytatás** lehetőséget, majd válassza a **regisztráció** lehetőséget az új alkalmazás azonosítójának megerősítéséhez.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Új alkalmazás AZONOSÍTÓjának megerősítése":::

      Miután kiválasztotta a **regisztrációt**, megjelenik az új alkalmazás-azonosító a **tanúsítványok, azonosítók & profilok** lapon.

4. A **tanúsítványok, azonosítók & profilok** lapon az **azonosítók** területen keresse meg az imént létrehozott app ID sort, és válassza ki annak sorát az **alkalmazás-azonosító konfigurációjának szerkesztése** képernyő megjelenítéséhez.

## <a name="create-a-certificate-for-notification-hubs"></a>Tanúsítvány létrehozása Notification Hubshoz

> [!NOTE]
> Az iOS 13 kiadásával csak a jogkivonat-alapú hitelesítés használatával fogadhat csendes értesítéseket. Ha tanúsítványalapú hitelesítést használ a APNS hitelesítő adataihoz, a jogkivonat-alapú hitelesítés használatára kell váltania.

Egy tanúsítványra van szükség ahhoz, hogy az értesítési központ működjön a **APNS**. Ezt kétféleképpen teheti meg:

- Hozzon létre egy **. P12** fájlt, amely közvetlenül a Notification Hubs tölthető fel.

- Hozzon létre egy **. P8** -fájlt, amely [jogkivonat-alapú hitelesítéshez](notification-hubs-push-notification-http2-token-authentication.md) használható (az újabb módszer).

A második lehetőség számos előnnyel jár a tanúsítványok használata során, a [APNS-hez tartozó jogkivonat-alapú (http/2) hitelesítéssel](notification-hubs-push-notification-http2-token-authentication.md)dokumentálva. Mindkét megközelítéshez azonban lépéseket kell megadnia.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>1. lehetőség: hozzon létre egy. P12 leküldéses tanúsítványt, amely közvetlenül a Notification Hubs tölthető fel

1. Görgessen le a **leküldéses értesítések** lehetőséghez, majd válassza a **Konfigurálás** lehetőséget a tanúsítvány létrehozásához.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="Alkalmazásazonosító":::

2. Megjelenik az **Apple push Notification szolgáltatás SSL-tanúsítványainak** ablaka. Kattintson a **tanúsítvány létrehozása** gombra a **fejlesztési SSL-tanúsítvány** szakaszban.

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Tanúsítvány létrehozása":::

   Megjelenik az **új tanúsítvány létrehozása** képernyő.

   > [!NOTE]
   > Ez az oktatóprogram fejlesztési tanúsítványt használ. Ugyanez a folyamat használatos a termelési tanúsítvány regisztrálásához is. Ügyeljen arra, hogy az értesítések küldésekor ugyanazt a tanúsítványt használja.

3. Válassza a **fájl kiválasztása** elemet, keresse meg azt a helyet, ahol az első feladatból mentette a CSR-fájlt, majd kattintson duplán a tanúsítvány nevére a betöltéshez. Ezután válassza a **Folytatás** elemet.

4. Miután a portál létrehozza a tanúsítványt, kattintson a **Letöltés** gombra. Mentse a tanúsítványt, és jegyezze meg a helyet, ahová a rendszer mentette.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Tanúsítvány letöltése":::

   A rendszer letölti és menti a tanúsítványt a **letöltések** mappába.

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Tanúsítványfájl keresése":::

   Alapértelmezés szerint a letöltött fejlesztési tanúsítvány neve **aps_development. cer**.

5. Kattintson duplán a letöltött leküldéses tanúsítvány **APS \_ Development. cer** elemre. Ez a művelet telepíti az új tanúsítványt a kulcsláncba, ahogy az alábbi képen látható:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Kulcstartó-hozzáférés":::

   Bár a tanúsítványban szereplő név eltérő lehet, a név az **Apple Development iOS Leküldéses szolgáltatásaival** előtaggal lesz ellátva.

6. A kulcslánc-hozzáférési oldalon kattintson a jobb egérgombbal az új leküldéses tanúsítványra, melyet a **Certificates** (Tanúsítványok) kategóriában létrehozott. Válassza az **Exportálás** lehetőséget, nevezze el a fájlt, válassza ki a **. P12** formátumot, majd válassza a **Mentés** lehetőséget.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Tanúsítvány exportálása":::

   Megadhatja, hogy a tanúsítvány jelszavas védelemmel legyen ellátva, de ez nem kötelező. Kattintson **az OK** gombra, ha meg szeretné kerülni a jelszó létrehozását. Jegyezze fel az exportált .p12 tanúsítvány nevét és helyét. A rendszer a APNS használatával engedélyezi a hitelesítést.

   > [!NOTE]
   > A. P12-fájl neve és helye eltérő lehet a jelen oktatóanyagban található képpel.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>2. lehetőség: a jogkivonat-alapú hitelesítéshez használható. P8-tanúsítvány létrehozása

1. Jegyezze fel az alábbi adatokat:

   - **Alkalmazás azonosítójának előtagja** (ez a **csapat azonosítója**)
   - **Csomagazonosító**

2. Vissza a **tanúsítványok, azonosítók & profilok** területen kattintson a **kulcsok** elemre. Ha már van konfigurált kulcs a **APNS**-hez, újra használhatja a. P8-tanúsítványt, amelyet a létrehozása után közvetlenül letöltött. Ha igen, figyelmen kívül hagyhatja a 3 – 5. lépést.

3. Az **+** új kulcs létrehozásához kattintson a gombra (vagy a **kulcs létrehozása** gombra).

4. Adjon meg egy megfelelő **Kulcsnév** értéket, tekintse meg az **Apple push Notifications szolgáltatás (APNS)** beállítást, majd kattintson a **Folytatás** gombra, majd a következő képernyőn **regisztráljon** .

5. Kattintson a **Letöltés** elemre, majd helyezze át a **. P8** fájlt (a előtaggal `AuthKey_` ) egy biztonságos helyi könyvtárba, majd kattintson a **kész** gombra.

   > [!IMPORTANT]
   > Ügyeljen arra, hogy a. P8 fájlt biztonságos helyen tárolja (és mentse a biztonsági mentést). A kulcs letöltése után nem tölthető le újra; a kiszolgáló másolata el lesz távolítva.

6. A **kulcsok** lapon kattintson az imént létrehozott kulcsra (vagy egy meglévő kulcsra, ha inkább a használatát választotta).

7. Jegyezze fel a **kulcs azonosítójának** értékét.

8. Nyissa meg a. P8-tanúsítványt az Ön által választott megfelelő alkalmazásban, például a [Visual Studio Code](https://code.visualstudio.com/)-ban, majd jegyezze fel a kulcs értékét. Ez az érték **-----BEGIN Private key-----** és **-----END titkos kulcs-----** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Ez az a jogkivonat-érték, amelyet később a Notification Hubs konfigurálásához fog használni.

Ezeknek a lépéseknek a végén a következő információkat kell használnia az [értesítési központ APNS-információkkal való konfigurálásának](#configure-the-notification-hub-with-apns-information)későbbi használatához:

- **Csoport azonosítója** (lásd: 1. lépés)
- **Köteg azonosítója** (lásd: 1. lépés)
- **Kulcs azonosítója** (lásd: 7. lépés)
- **Jogkivonat értéke** (a. P8 kulcs értéke, lásd a 8. lépést)

## <a name="create-a-provisioning-profile"></a>Létesítési profil létrehozása

1. Térjen vissza az [iOS-es kiépítési portálra](https://go.microsoft.com/fwlink/p/?LinkId=272456), válassza a **tanúsítványok, azonosítók & profilok** lehetőséget, válassza a **profilok** elemet a bal oldali menüben, majd válassza **+** az új profil létrehozása lehetőséget. Megjelenik az **új létesítési Profil regisztrálása** képernyő.

2. Válassza az **iOS-alkalmazások fejlesztése** lehetőséget a **fejlesztés** alatt a létesítési profil típusa területen, majd válassza a **Folytatás** lehetőséget.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Üzembehelyezési profillista":::

3. Ezután válassza ki az **alkalmazás-azonosító** legördülő LISTÁBÓL létrehozott azonosítót, majd válassza a **Folytatás** lehetőséget.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Alkalmazás AZONOSÍTÓjának kiválasztása":::

4. A **tanúsítványok kiválasztása** ablakban válassza ki a kód aláírásához használt fejlesztési tanúsítványt, és válassza a **Folytatás** lehetőséget. Ez a tanúsítvány nem a létrehozott leküldéses tanúsítvány. Ha az egyik nem létezik, létre kell hoznia. Ha egy tanúsítvány létezik, ugorjon a következő lépésre. Fejlesztési tanúsítvány létrehozása, ha az egyik nem létezik:

   1. Ha **nem érhető el tanúsítvány**, válassza a **tanúsítvány létrehozása** lehetőséget.
   2. A **szoftver** szakaszban válassza az **Apple Development** elemet. Ezután válassza a **Folytatás** elemet.
   3. Az **új tanúsítvány létrehozása** képernyőn válassza a **fájl kiválasztása** lehetőséget.
   4. Keresse meg a korábban létrehozott **tanúsítvány-aláírási kérelem** tanúsítványát, jelölje ki, majd kattintson a **Megnyitás** gombra.
   5. Válassza a **Folytatás** lehetőséget.
   6. Töltse le a fejlesztési tanúsítványt, és jegyezze fel a mentési helyét.

5. Térjen vissza a **tanúsítványok, azonosítók & profilok** lapra, válassza a bal oldali menüben a **profilok** lehetőséget, majd válassza **+** az új profil létrehozása lehetőséget. Megjelenik az **új létesítési Profil regisztrálása** képernyő.

6. A **tanúsítványok kiválasztása** ablakban válassza ki az imént létrehozott fejlesztési tanúsítványt. Ezután válassza a **Folytatás** elemet.

7. Ezután válassza ki a teszteléshez használni kívánt eszközöket, majd válassza a **Folytatás** lehetőséget.

8. Végül válassza ki a profil nevét a **létesítési profil nevében**, majd válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Kiépítési profil nevének kiválasztása":::

9. Az új létesítési profil létrehozásakor válassza a **Letöltés** lehetőséget. Jegyezze meg a helyet, ahol a mentésre kerül.

10. Keresse meg a létesítési profil helyét, majd kattintson rá duplán a Xcode fejlesztői gépre való telepítéséhez.

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása

Ebben a szakaszban létrehoz egy értesítési központot, és konfigurálja a hitelesítést a APNS a. P12 push-tanúsítvány vagy a jogkivonat-alapú hitelesítés használatával. Ha egy már létrehozott értesítési központot szeretne használni, ugorjon az 5. lépésre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget, majd a **mobil** szakaszban válassza a **Notification Hubs** lehetőséget. A szolgáltatás neve melletti csillag ikonra kattintva adja hozzá a szolgáltatást a **Kedvencek** szakaszhoz a bal oldali menüben. Miután hozzáadta **Notification Hubs** a **kedvencekhez**, válassza ki.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure Portal":::

3. A **Notification Hubs** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Eszköztár hozzáadása gomb":::

4. A **Notification Hubs** oldalon hajtsa végre a következő lépéseket:

   1. Adjon meg egy nevet az **értesítési központban**.
   2. Adjon meg egy nevet az **új névtér létrehozása** mezőben. A névtér egy vagy több értesítési központot tartalmaz.
   3. Válasszon egy értéket a **hely** legördülő listából. Ez az érték határozza meg azt a helyet, amelyben létre kívánja hozni az értesítési központot.
   4. Válasszon ki egy meglévő erőforráscsoportot az **erőforráscsoporthoz**, vagy hozzon létre egy új erőforráscsoportot.
   5. Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Tulajdonságok beállítása":::

5. Válassza az **értesítések** (harang ikon) lehetőséget, majd válassza **az Ugrás az erőforráshoz** lehetőséget. A listát a **Notification Hubs** oldalon is frissítheti, és kiválaszthatja a hubot.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Portál értesítései":::

6. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Vegye figyelembe, hogy a két kapcsolódási karakterlánc elérhető az Ön számára. A leküldéses értesítések kezeléséhez később szüksége lesz rájuk.

   > [!IMPORTANT]
   > Ne használja az **DefaultFullSharedAccessSignature** szabályzatot az alkalmazásban. Ezt csak a háttérben való használatra szántuk.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Kapcsolati sztringek":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Az értesítési központ konfigurálása APNS-adatokkal

A **Notification Services** alatt válassza az **Apple (APNS)** lehetőséget, majd kövesse a megfelelő lépéseket a [tanúsítvány létrehozása Notification Hubs](#create-a-certificate-for-notification-hubs) szakaszban korábban kiválasztott megközelítés alapján.

> [!NOTE]
> Csak akkor használjon **éles** **alkalmazási módot** , ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak.

### <a name="option-1-use-a-p12-push-certificate"></a>1. lehetőség:. P12 leküldéses tanúsítvány használata

1. Válassza a **Tanúsítvány** elemet.

2. Válassza a fájl ikont.

3. Válassza ki a korábban exportált. P12 fájlt, majd kattintson a **Megnyitás** gombra.

4. Ha szükséges, a helyes jelszót kell megadnia.

5. Válassza a **Védőfal** módot.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Konfigurálás":::

6. Kattintson a **Mentés** gombra.

### <a name="option-2-use-token-based-authentication"></a>2. lehetőség: jogkivonat-alapú hitelesítés használata

1. Válassza a **token** lehetőséget.

2. Adja meg a korábban beszerzett következő értékeket:

   - **Kulcs azonosítója**
   - **Csomagazonosító**
   - **Csoport azonosítója**
   - **Jogkivonat**

3. Válassza ki a **homokozót**

4. Kattintson a **Mentés** gombra.

Most már konfigurálta az értesítési központot a APNS. Az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez is szükség van a kapcsolódási karakterláncokra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott és konfigurált egy értesítési központot az Azure-ban, és úgy konfigurálta, hogy engedélyezze az értesítések küldését az alkalmazásnak Apple Push Notification Service (APNS) használatával. Ezután létrehozunk egy minta iOS-alkalmazást, és integráljuk az Azure Notifications hubok SDK-t úgy, hogy az a Azure Portalon keresztül küldött leküldéses értesítéseket fogadhasson. Folytassa a következő oktatóanyaggal a választott nyelv alapján:

- [Oktatóanyag: leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával](ios-sdk-current.md)
