---
title: Az Azure Percept DK beállítása
description: A fejlesztői csomag összekapcsolását az Azure-ba, és üzembe helyezheti az első AI-modelljét
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 9567ec2458a01825568cb853728f71db10228ee3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608278"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Az Azure Percept DK beállítása és az első AI-modell üzembe helyezése

Fejezze be az Azure Percept DK telepítési felületét a fejlesztői csomag konfigurálásához és az első AI-modell üzembe helyezéséhez. Miután meggyőződött róla, hogy az Azure-fiókja kompatibilis az Azure Percept, az alábbiakat fogja megállapítani:

- A fejlesztői csomag összekötése Wi-Fi hálózattal
- SSH-bejelentkezés beállítása a fejlesztői csomaghoz való távoli hozzáféréshez
- Új IoT Hub létrehozása az Azure Percept való használathoz
- A fejlesztői csomag összekapcsolhatók a IoT Hub és az Azure-fiókkal

Ha a folyamat során bármilyen problémát tapasztal, tekintse meg a lehetséges megoldások [telepítési hibaelhárítási útmutatóját](./how-to-troubleshoot-setup.md) .

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Percept DK (fejlesztői csomag).
- Windows, Linux vagy OS X rendszerű gazdagép, Wi-Fi-képességgel és egy webböngészővel.
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Az Azure-fióknak **tulajdonos** vagy **közreműködő** szerepkörrel kell rendelkeznie az előfizetésen belül. Az Azure-fiók szerepkörének vizsgálatához kövesse az alábbi lépéseket. Az Azure szerepkör-definíciókkal kapcsolatos további információkért tekintse meg az [Azure szerepköralapú hozzáférés-vezérlési dokumentációját](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles).

    > [!CAUTION]
    > Ha több Azure-fiókkal rendelkezik, a böngésző egy másik fiókból is gyorsítótárazhatja a hitelesítő adatokat. A félreértések elkerülése érdekében javasoljuk, hogy zárja be az összes nem használt böngészőablakot, és jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a telepítési élmény megkezdése előtt. A megfelelő fiókkal való bejelentkezéssel kapcsolatos további információkért tekintse meg a [telepítési hibaelhárítási útmutatót](./how-to-troubleshoot-setup.md) .

### <a name="check-your-azure-account-role"></a>Azure-fiók szerepkörének megtekintése

Kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy az Azure-fiókja "tulajdonos" vagy "közreműködő"-e az előfizetésen belül:

1. Lépjen a [Azure Portalra](https://portal.azure.com/) , és jelentkezzen be ugyanazzal az Azure-fiókkal, amelyet az Azure Percept használni szeretne.

1. Kattintson az **előfizetések** ikonra (a sárga kulcshoz hasonlóan néz ki).

1. Válassza ki az előfizetését a listából. Ha nem látja az előfizetést, győződjön meg arról, hogy a megfelelő Azure-fiókkal van bejelentkezve. Ha új előfizetést szeretne létrehozni, kövesse [az alábbi lépéseket](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. Az előfizetés menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Kattintson **a saját hozzáférés megtekintése** elemre.
1. Keresse meg a szerepkört:
    - Ha a szerepköre **olvasóként** van felsorolva, vagy ha olyan üzenetet kap, amely szerint nem rendelkezik a szerepkörök megtekintéséhez szükséges engedéllyel, akkor a fiókja szerepkörének megemeléséhez követnie kell a megfelelő folyamatot a szervezetben.
    - Ha a szerepköre **tulajdonosként** vagy **közreműködőként** szerepel, a fiókja az Azure Percept fog működni, és folytathatja a telepítési folyamatot.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Az Azure Percept DK telepítési felületének elindítása

1. A gazdagép számítógépét közvetlenül a fejlesztői csomag Wi-Fi hozzáférési pontjához kapcsolja. Ha más Wi-Fi hálózathoz szeretne csatlakozni, nyissa meg a hálózati és internetes beállításokat a számítógépen, kattintson a következő hálózatra, majd adja meg a hálózati jelszót, ha a rendszer kéri:

    - **Hálózat neve**: a fejlesztői csomag operációs rendszerének verziójától függően az Wi-Fi hozzáférési pont neve **scz-XXXX** vagy **APD-XXXX** (ahol az "xxxx" a fejlesztői csomag MAC-címe utolsó négy számjegye)
    - **Password (jelszó**): a fejlesztői csomaggal kapott üdvözlő kártyán található.

    > [!WARNING]
    > Amikor csatlakozik az Azure Percept DK Wi-Fi hozzáférési ponthoz, a gazdaszámítógép átmenetileg elveszti a kapcsolatát az internettel. Az aktív videokonferencia-hívások, a webes streaming vagy más hálózati élmények megszakadnak.

1. A fejlesztői csomag Wi-Fi hozzáférési pontjához való csatlakozás után a gazdaszámítógép automatikusan elindítja a telepítési folyamatot egy új böngészőablakban, a **. New. Device/** a címsorban. Ha a lap nem nyílik meg automatikusan, indítsa el a telepítési folyamatot a következő címen: [http://10.1.1.1](http://10.1.1.1) . Győződjön meg arról, hogy a böngészője ugyanazzal az Azure-fiók hitelesítő adataival van bejelentkezve, amelyet az Azure Percept használni szeretne.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Kezdőlap.":::

    > [!CAUTION]
    > A telepítési élmény webszolgáltatás 30 percen belül le fog állni a használatból. Ha ez történik, indítsa újra a fejlesztői készletet, hogy elkerülje a fejlesztői csomag Wi-Fi hozzáférési pontjához való kapcsolódási problémákat.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Az Azure Percept DK telepítési felületének befejezése

1. Az **üdvözlő** képernyőn kattintson a **tovább** gombra.

1. A **hálózati kapcsolat** lapon kattintson a **Kapcsolódás új WiFi-hálózathoz** lehetőségre.

    Ha már csatlakoztatta a fejlesztői készletet a Wi-Fi hálózathoz, kattintson a **kihagyás** gombra.

1. Válassza ki a Wi-Fi hálózatot a rendelkezésre álló hálózatok listájából, majd kattintson a **Csatlakozás** gombra. Ha a rendszer kéri, adja meg a hálózati jelszavát.

1. Miután a fejlesztői csomag sikeresen csatlakozott a hálózatához, a lapon megjelenik a fejlesztői csomaghoz rendelt IPv4-címe. **Jegyezze fel az oldalon megjelenő IPv4-címeket.** Szüksége lesz az IP-címére, amikor SSH-kapcsolaton keresztül csatlakozik a fejlesztői csomaghoz a hibaelhárításhoz és az eszközök frissítéseihez.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Másolja az IP-címet.":::

    > [!NOTE]
    > Az IP-cím az egyes eszközök rendszerindításával változhat.

1. Olvassa el a licencszerződést, válassza az **elolvastam és elfogadom a licencszerződést** (a szerződés aljára kell görgetni), és kattintson a **tovább** gombra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="A végfelhasználói licencszerződés elfogadása.":::

1. Adja meg az SSH-fiók nevét és jelszavát, és **jegyezze fel a bejelentkezési adatait későbbi használatra**.

    > [!NOTE]
    > Az SSH (Secure Shell) egy olyan hálózati protokoll, amely lehetővé teszi, hogy távolról csatlakozzon a fejlesztői csomaghoz egy gazdaszámítógépen keresztül.

1. A következő lapon kattintson a **telepítő új eszközként** lehetőségre egy új eszköz Azure-fiókban való létrehozásához.

1. Az eszköz kódjának másolásához kattintson a **Másolás** gombra. Ezután kattintson **Az Azure-ba való bejelentkezés** lehetőségre.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Az eszköz kódjának másolása.":::

1. Megnyílik egy új böngészőablak, amely a **kód beírása** gombra kattint. Illessze be a kódot az ablakba, és kattintson a **tovább** gombra. Ne zárjuk be az **üdvözlő** lapot a telepítési felülettel.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Adja meg az eszköz kódját.":::

1. Jelentkezzen be az Azure Percept a fejlesztői csomaggal használni kívánt Azure-fiók hitelesítő adataival. Ha elkészült, hagyja nyitva a böngésző fület.

    > [!CAUTION]
    > Előfordulhat, hogy a böngésző automatikusan gyorsítótárazza a többi hitelesítő adatot. Ellenőrizze, hogy a megfelelő fiókkal jelentkezik-e be.

    Miután sikeresen bejelentkezett az Azure Százalékba az eszközön, térjen vissza az **üdvözlő** lapra a telepítési élmény folytatásához.

1. Amikor az **eszköz kiosztása az Azure IoT hub** lapra megjelenik az **üdvözlőlapon** , hajtsa végre az alábbi műveletek egyikét:

    - Ha már rendelkezik egy IoT Hub, amelyet az Azure Percept szeretne használni, és az ezen az oldalon található, akkor válassza ki, majd ugorjon a 15. lépésre.
    - Ha nincs IoT Hub vagy újat szeretne létrehozni, kattintson az **új Azure-IoT hub létrehozása** lehetőségre.

    > [!IMPORTANT]
    > Ha IoT Hub van, de nem jelenik meg a listában, előfordulhat, hogy a helytelen hitelesítő adatokkal jelentkezett be az Azure Percept. Segítségért tekintse meg a [telepítési hibaelhárítási útmutatót](./how-to-troubleshoot-setup.md) .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Válasszon ki egy IoT Hub.":::

1. Új IoT Hub létrehozásához hajtsa végre a következő mezőket:

    - Válassza ki azt az Azure-előfizetést, amelyet az Azure Percept fog használni.
    - Válasszon ki egy meglévő erőforráscsoportot. Ha az egyik nem létezik, kattintson az **új létrehozása** elemre, és kövesse az utasításokat.
    - Válassza ki a fizikai helyéhez legközelebb eső Azure-régiót.
    - Adja meg az új IoT Hub nevét.
    - Válassza ki az S1 (standard) árképzési szintet.

    > [!NOTE]
    > Ha a háttérben futó AI-alkalmazásoknál nagyobb [átviteli sebességre](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) van szüksége, a IoT hub bármikor [frissítheti az Azure Portal magasabb színvonalú szintjére](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) . A B és az F rétegek nem támogatják az Azure-Percept.

1. IoT Hub üzemelő példány eltarthat néhány percig. A telepítés befejezésekor kattintson a **regisztráció** elemre.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub sikeresen telepítve.":::

1. Adja meg a fejlesztői csomag eszközének nevét, majd kattintson a **tovább** gombra.

1. Várjon, amíg az eszköz moduljai le lesznek letöltve – ez eltarthat néhány percig.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="A telepítő véglegesítése.":::

1. Az **eszköz telepítésének befejezése után** lapon a fejlesztői csomag sikeresen kapcsolódott a IoT Hubhoz, és letöltötte a szükséges szoftvert. A fejlesztői csomag automatikusan leválasztja a Wi-Fi elérési pontról a következő két értesítés eredményét:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="A telepítési élmény megszakítása figyelmeztetés.":::

1. Csatlakoztassa a gazdagép számítógépét a 2. lépésben a fejlesztői készlet csatlakoztatott Wi-Fi hálózathoz.

1. Kattintson **a Folytatás gombra a Azure Portal**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Nyissa meg az Azure Percept studiót.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>A fejlesztői csomag video stream megtekintése és a minta modell üzembe helyezése

1. Az [Azure Percept Studio áttekintő oldala](https://go.microsoft.com/fwlink/?linkid=2135819) az indítási pont, amellyel számos különböző munkafolyamathoz férhet hozzá a kezdő és a haladó szintű AI-megoldás fejlesztéséhez. A kezdéshez kattintson a bal oldali menü **eszközök** elemére.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Az eszközök listájának megtekintése.":::

1. Ellenőrizze, hogy a fejlesztői csomag **csatlakoztatva** van-e a listához, és kattintson rá az eszköz oldalának megtekintéséhez.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Jelölje ki az eszközét.":::

1. Kattintson **az eszköz adatfolyamának megtekintése** elemre. Ha első alkalommal tekinti meg az eszköz videó streamjét, megjelenik egy értesítés arról, hogy az új modell üzembe helyezése a jobb felső sarokban történik. Ez eltarthat néhány percig.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Tekintse meg a videó streamjét.":::

    A modell üzembe helyezése után egy másik értesítés jelenik meg a **stream megtekintése** hivatkozásra kattintva. A hivatkozásra kattintva megtekintheti a videó streamet az Azure Percept-jövőkép kameráról egy új böngészőablakban. A fejlesztői készlet előre be van töltve egy AI-modellel, amely automatikusan elvégzi számos gyakori objektum objektum-észlelését.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Lásd: objektumok észlelése.":::

1. Az Azure Percept Studio számos minta AI-modellel is rendelkezik. Ha modellt szeretne telepíteni a fejlesztői csomagba, térjen vissza az eszköz oldalára, és kattintson **a minta modell üzembe helyezése** lehetőségre.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Fedezze fel az előre elkészített modelleket.":::

1. Válasszon ki egy mintát a könyvtárból, majd kattintson a **telepítés eszközre** elemre.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Lásd: objektumok észlelése működés közben.":::

1. Miután a modell sikeresen üzembe lett, megjelenik egy értesítés a képernyő jobb felső sarkában látható **stream (megtekintés** ) hivatkozással. Ha meg szeretné tekinteni a modellre vonatkozó következtetést, kattintson az értesítésben szereplő hivatkozásra, vagy térjen vissza az eszköz lapra, és kattintson az **eszköz adatfolyamának megtekintése** elemre. A fejlesztői csomagon korábban már futó modellek az új modellel lesznek lecserélve.

## <a name="video-walkthrough"></a>Bemutató videó

A fent ismertetett lépések vizuális bemutatójának megtekintéséhez tekintse meg a következő videót (a telepítési élmény 0:50-kor kezdődik):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kód nélküli jövőkép megoldás létrehozása](./tutorial-nocode-vision.md)