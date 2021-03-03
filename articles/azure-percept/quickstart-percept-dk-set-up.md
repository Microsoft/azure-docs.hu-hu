---
title: Az Azure Percept DK beállítása
description: A fejlesztői csomag összekapcsolását az Azure-ba, és üzembe helyezheti az első AI-modelljét
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 3540204d66bb589c567514f92a9a8acb2159e343
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665516"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Az Azure Percept DK beállítása és az első AI-modell üzembe helyezése

Ismerkedjen meg az Azure Percept DK és az Azure Percept Studio használatával az Azure Percept DK telepítési felülettel az eszköz csatlakoztatásához az Azure-hoz és az első AI-modell üzembe helyezéséhez. Miután meggyőződött róla, hogy az Azure-fiókja kompatibilis az Azure Percept Studio szolgáltatással, befejezte a telepítési folyamatot annak biztosítására, hogy az Azure Percept DK-t úgy konfigurálja, hogy az a fogalmakat is létrehozza.

Ha problémákat tapasztal a gyorskonfigurálás során, tekintse meg a lehetséges megoldások [hibaelhárítási](./troubleshoot-dev-kit.md) útmutatóját.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Percept DK.
- Egy Windows-, Linux-vagy OS X-alapú gazdaszámítógép Wi-Fi-képességgel és egy webböngészővel.
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Az Azure-fióknak a tulajdonos vagy a közreműködő szerepkörrel kell rendelkeznie az előfizetésben. További információ az Azure szerepkör-definíciókkal kapcsolatban

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

Az alábbi lépéseket követve ellenőrizheti, hogy az Azure-fiókja "tulajdonos" vagy "közreműködő"-e az előfizetésben.

1. Lépjen a Azure Portalra, és jelentkezzen be ugyanazzal az Azure-fiókkal, amelyet az Azure Percept Studióval szeretne használni. 

    > [!NOTE]
    > Ha több Azure-fiókkal rendelkezik, a böngésző egy másik fiókból is gyorsítótárazhatja a hitelesítő adatokat. A megfelelő fiókkal való bejelentkezéssel kapcsolatos további információkért tekintse meg a hibaelhárítási útmutatót.

1. Bontsa ki a főmenüt a képernyő bal felső sarkában, és kattintson az "előfizetések" lehetőségre, vagy válassza az "előfizetések" lehetőséget a kezdőlapon lévő ikonok menüjében. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Válassza ki az előfizetését a listából. Ha nem látja az előfizetést a listában, győződjön meg arról, hogy a megfelelő Azure-fiókkal van bejelentkezve. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Ha új előfizetést szeretne létrehozni, kövesse [az alábbi lépéseket](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. Az előfizetés menüben válassza a hozzáférés-vezérlés (IAM) lehetőséget.
1. Kattintson a "hozzáférés megtekintése" gombra
1. A szerepkör ellenõrzése
    - Ha az "olvasó" szerepkört jeleníti meg, vagy ha olyan üzenetet kap, amely szerint nem rendelkezik a szerepkörök megtekintéséhez szükséges engedéllyel, akkor a fiókja szerepkörének megemelt állapotba lépéséhez követnie kell a megfelelő folyamatot a szervezetben.
    - Ha a szerepkör tulajdonosként vagy közreműködőként jelenik meg, a fiók az Azure Percept Studióval fog működni. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Az Azure Percept DK telepítési felületének elindítása

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. A gazdagépet közvetlenül a fejlesztői csomag Wi-Fi-hozzáférési pontjához kapcsolja. Ez ugyanúgy történik, mint bármely más Wi-Fi-hálózathoz való csatlakozáshoz,
    - **hálózatnév**: scz-XXXX (ahol az "xxxx" a dev Kit Mac hálózati címe utolsó négy számjegye)
    - **Password (jelszó**): a fejlesztői csomaggal kapott üdvözlő kártyán található.

    > [!WARNING]
    > Az Azure Percept DK Wi-Fi hozzáférési ponthoz kapcsolódva a gazdaszámítógép átmenetileg elveszti a kapcsolatát az internettel. Az aktív videokonferencia-hívások, a webes streaming vagy más hálózati élmények megszakadnak, amíg az Azure Percept DK telepítési felületének 3. lépése be nem fejeződik.

1. A fejlesztői csomag Wi-Fi-hozzáférési pontjához való csatlakozás után a gazdaszámítógép automatikusan elindítja az Azure Percept DK telepítési felületét egy új böngészőablakban. Ha nem nyílik meg automatikusan, a böngészőablak megnyitásával és a alkalmazásban való navigálással manuálisan indíthatja el [http://10.1.1.1](http://10.1.1.1) . 

1. Most, hogy elindította az Azure Percept telepítési felületét, készen áll a telepítési élmény folytatására. 

    > [!NOTE]
    > Az Azure Percept DK telepítési élmény webszolgáltatás a használat után 30 percen belül leáll, a telepítési élmény befejezésekor. Ha ez történik, javasoljuk, hogy indítsa újra a fejlesztői készletet, hogy elkerülje a fejlesztői csomag Wi-Fi hozzáférési pontjának kapcsolódási problémáit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Az Azure Percept DK telepítési felületének befejezése

1. Első lépések – az üdvözlőképernyőn kattintson a **tovább** gombra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Kezdőlap.":::

1. Csatlakozás Wi-Fi-hez – a hálózati kapcsolat lapon kattintson a **Csatlakozás új WiFi-hálózathoz** lehetőségre a fejlesztői készlet Wi-Fi-hálózathoz való csatlakoztatásához.

    Ha korábban már csatlakoztatta ezt a dev-csomagot a Wi-Fi-hálózathoz, vagy ha már csatlakozott az Azure Percept DK telepítési felületéhez a Wi-Fi-hálózaton keresztül, kattintson a **kihagyás** hivatkozásra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Csatlakozás Wi-Fi-hez.":::

1. Válassza ki a Wi-Fi-hálózatát az elérhető kapcsolatok közül, és kapcsolódjon a csatlakozáshoz. (A helyi Wi-Fi jelszót kell megadni)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Válassza a Wi-Fi hálózat lehetőséget."::: 

1. Másolja ki az IP-címet – ha a fejlesztői készlet sikeresen csatlakozott a hálózatához, jegyezze fel az oldalon megjelenő **IPv4-címet** . Ezt az **IP-címet később kell megadnia ebben a gyors üzembe helyezési útmutatóban**. 

    > [!NOTE]
    > Az IP-cím az egyes eszközök rendszerindításával változhat.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Másolja az IP-címet.":::

1. Tekintse át és fogadja el a licencszerződést – olvassa el a licencszerződést, jelölje be az **elolvastam és** Elfogadom a licencszerződést (a szerződés aljára kell görgetni), majd kattintson a **tovább** gombra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="A végfelhasználói licencszerződés elfogadása.":::

1. Az SSH bejelentkezési fiók létrehozása – SSH beállítása a fejlesztői készlet való távoli hozzáféréshez. Hozzon létre egy SSH-felhasználónevet és-jelszót. 

    > [!NOTE]
    > Az SSH (Secure Shell) a gazdagép és a fejlesztői csomag közötti biztonságos kommunikációhoz használt hálózati protokoll. Az SSH-val kapcsolatos további információkért tekintse meg [ezt a cikket](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="SSH-fiók létrehozása."::: 

1. A fejlesztői csomag kapcsolódási folyamatának megkezdése – a következő képernyőn kattintson a **Csatlakozás új eszközzel** lehetőségre a fejlesztői csomag Azure IoT hubhoz való csatlakoztatásának megkezdéséhez. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Kapcsolódjon az Azure-hoz."::: 

1. Másolja az eszköz kódját – a **Másolás** hivatkozásra kattintva másolja az eszköz kódját. Ezután kattintson **Az Azure-ba való bejelentkezés** elemre. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Az eszköz kódjának másolása."::: 

1. Illessze be az eszköz kódját – az új böngésző lap megnyílik egy ablak, amely a másolt eszköz kódját kéri. Illessze be a kódot az ablakba, és kattintson a **tovább** gombra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Adja meg az eszköz kódját."::: 

1. Bejelentkezés az Azure-ba – a következő ablakban be kell jelentkeznie a gyorskonfigurálás elején ellenőrzött Azure-fiókkal. Adja meg a bejelentkezési hitelesítő adatokat, és kattintson a **tovább** gombra. 

    > [!NOTE]
    > Előfordulhat, hogy a böngésző automatikusan gyorsítótárazza a többi hitelesítő adatot. Ellenőrizze, hogy a megfelelő fiókkal jelentkezik-e be.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Jelentkezzen be az Azure-ba.":::
 
1. Ne zárjuk be a telepítési élmény böngésző fület ebben a lépésben – miután bejelentkezett, megjelenik egy képernyő, amely tudomásul veszi, hogy be van jelentkezve. Bár sok ablak bezárását is **javasolja, javasoljuk, hogy ne zárjunk be minden Windows**-t. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Sikeres bejelentkezés."::: 

1. Térjen vissza a böngésző lapra, amely a telepítési folyamatot üzemelteti.
1. Válassza ki a IoT Hub lehetőséget
    - Ha már van IoT Hub, és a lapon szerepel ezen az oldalon, kiválaszthatja, majd **a 17. lépésre ugorhat**.
    - Ha nem rendelkezik IoT Hub vagy újat szeretne létrehozni, lépjen a lista aljára, és kattintson az **új Azure-IoT hub létrehozása** lehetőségre.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Válasszon ki egy IoT Hub."::: 

1. Hozza létre az új IoT Hub – töltse ki az összes mezőt ezen a lapon. 
    - Válassza ki azt az Azure-előfizetést, amelyet az Azure Percept Studio használatával fog használni
    - Válasszon ki egy meglévő erőforráscsoportot. Ha az egyik nem létezik, kattintson az új létrehozása gombra, és kövesse az utasításokat. 
    - Válassza ki az Azure-régiót. 
    - Adja meg az új IoT Hub nevét
    - Válassza ki a díjszabási szintet (ez általában megegyezik az előfizetéssel)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Hozzon létre egy új IoT Hub."::: 

1. Várjon, amíg a IoT Hub üzembe helyezése bekerül – eltarthat néhány percig

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="IoT Hub üzembe helyezése."::: 

1. A fejlesztői csomag regisztrálása – ha a telepítés befejeződött, kattintson a **regisztrálás** gombra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub sikeresen telepítve."::: 

1. A fejlesztői csomag neve – adja meg a fejlesztői csomag eszközének nevét, majd kattintson a **tovább** gombra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Adja meg az eszköz nevét."::: 

1. Várjon, amíg az alapértelmezett AI-modellek le lesznek letöltve – ez eltarthat néhány percig

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="A telepítő véglegesítése."::: 

1. Lásd: a vízió AI működés közben – a fejlesztői készlet sikeresen csatolva lett az Azure-IoT Hubhoz, és megkapta az alapértelmezett látási AI-objektum észlelési modelljét. Az Azure Percept-alapú kamera mostantól a felhőhöz való kapcsolódás nélkül képes az objektum észlelésére. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Kattintson az előnézet videó kimenete lehetőségre."::: 
       
    > [!NOTE]
    > Ezen a ponton a bevezetési folyamat és az eszköz Wi-Fi-hozzáférése le lesz kapcsolva, de a fejlesztői csomag csatlakoztatva marad az internethez.   A fejlesztői csomag újraindításával újraindíthatja a bevezetési folyamatot, amely lehetővé teszi, hogy visszalépjen a bevezetéshez, és újra csatlakoztatja az eszközt egy másik, azonos vagy egy másik Azure-előfizetéshez társított IOT hubhoz.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="A telepítési élmény megszakítása figyelmeztetés."::: 

1. Folytassa az Azure Portalon – lépjen vissza a telepítési élmény ablakba, és kattintson a **Folytatás a Azure Portal** gombra, hogy megkezdje az egyéni AI-modellek létrehozását az Azure Percept Studióban.

    > [!NOTE]
    > Győződjön meg arról, hogy a gazdaszámítógép már nem csatlakozik a fejlesztői csomag hozzáférési pontjához a WiFi-beállításokban, és most újra csatlakozik a helyi WiFi-hez.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Nyissa meg az Azure Percept studiót."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Megtekintheti az eszközt az Azure Percept Studióban, és üzembe helyezheti a közös előre elkészített minta alkalmazásokat

1. Az eszközök listájának megtekintése az Azure Percept – áttekintés oldalon. Az Azure Percept áttekintő oldala az indítási pont, amellyel számos különböző munkafolyamathoz férhet hozzá a kezdő és a speciális AI Edge-modellhez és a megoldások fejlesztéséhez

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Az eszközök listájának megtekintése.":::
    
1. Ellenőrizze, hogy az imént létrehozott eszköz csatlakoztatva van-e, majd kattintson rá.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Jelölje ki az eszközét.":::

1. Tekintse meg az eszköz streamjét, hogy megtekintse a fejlesztői készlet kameráját. Egy alapértelmezett objektum-észlelési modell van üzembe helyezve a box-ban, és számos gyakori objektumot fog észlelni.

    > [!NOTE]
    > az első alkalommal, amikor ezt új eszközön végzi el, értesítést kap arról, hogy az új modul üzembe helyezése a jobb felső sarokban történik.  A verseny megkezdése után elindíthatja az ablakot a kamera videó stream használatával. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Tekintse meg a videó streamjét.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Lásd: objektumok észlelése.":::

1. Fedezze fel az előre elkészített minta AI-modelleket.   Az Azure szabály Studio számos gyakori, előre elkészített mintát tartalmaz, amelyek egyetlen kattintással üzembe helyezhetők.   A "modell üzembe helyezése" lehetőség kiválasztásával megtekintheti és telepítheti ezeket.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Fedezze fel az előre elkészített modelleket.":::
    
1. Helyezzen üzembe egy új, előre elkészített mintát a csatlakoztatott eszközön. Válasszon ki egy mintát a könyvtárból, és kattintson az "üzembe helyezés eszközre" elemre.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-1-select-prebuilt.png" alt-text="Válasszon egy előre elkészített elemet.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Lásd: objektumok észlelése működés közben.":::

## <a name="next-steps"></a>Következő lépések

Egy hasonló folyamat követésével kipróbálhatja az [előre elkészített beszédfelismerési modelleket](./tutorial-no-code-speech.md).