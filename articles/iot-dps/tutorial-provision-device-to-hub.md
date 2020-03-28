---
title: Oktatóanyag – Eszköz kiépítése az Azure IoT Hub-eszközkiépítési szolgáltatáshasználatával
description: Ez az oktatóanyag bemutatja, hogyan építheti ki az eszközt egyetlen IoT-központba az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) használatával
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 3fe2fa8b094830e2d15c1cebce782381b4ca7bc7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74975040"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Oktatóanyag: Az eszköz kiépítése egy IoT hubra az Azure IoT Hub-eszközkiépítési szolgáltatás használatával

Az előző oktatóanyagban bemutattuk, hogyan állíthat be egy eszközt úgy, hogy az a Device Provisioning Service-hez csatlakozzon. Ebben az oktatóanyagban megtudhatja, hogyan regisztrálhatja az eszközt ezzel a szolgáltatással egyetlen IoT Hubon automatikus regisztráció és **_regisztrációs listák_** használatával. Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Az eszköz regisztrálása
> * Az eszköz elindítása
> * Az eszköz sikeres regisztrálásának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy [a kiépítendő eszköz az Azure IoT Hub Device Provisioning Service használatával történő beállítását](./tutorial-set-up-device.md) ismertető oktatóanyagban leírt módon konfigurálta az eszközt.

Amennyiben nem ismeri az automatikus regisztrálás folyamatát, a folytatás előtt olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Az eszköz regisztrálása

Ennek a lépésnek a részét képezi az eszköz egyedi biztonsági összetevőinek a Device Provisioning Service-hez adása. Ezek a biztonsági összetevők az eszköz [igazolási mechanizmusán](concepts-device.md#attestation-mechanism) alapulnak a következő módon:

- TPM-alapú eszközökhöz a következőkre van szükség:
    - Az egyes TPM-lapkákhoz vagy szimulációkhoz tartozó egyedi *ellenőrzőkulcs*, amely a TPM-lapka gyártójától szerezhető be.  További információkért olvassa el a [TPM-ellenőrzőkulcsot ismertető](https://technet.microsoft.com/library/cc770443.aspx) szakaszt.
    - A névtérben/hatókörben lévő eszköz egyedi azonosítására használt *regisztrációs azonosító*. Ez az azonosító nem feltétlenül egyezik az eszköz azonosítójával. Az azonosító minden eszközhöz kötelező. TPM-alapú eszközök esetén a regisztrációs azonosító magából a TPM-ből származhat, például a TPM-ellenőrzőkulcs SHA-256 kivonata lehet.

      [![A TPM regisztrációs adatai a portálon](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- X.509-alapú eszközökhöz a következőkre van szükség:
    - Az [X.509-lapkához vagy -szimulációhoz kiadott tanúsítvány](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx)*.pem* vagy *.cer* fájlformátumban. Egyéni regisztrációhoz az X.509 rendszerhez az *eszközönkénti aláírt tanúsítványt* kell használnia, míg a regisztrációs csoportokesetében a *főtanúsítványt kell használnia.* 

      [![Egyéni regisztráció hozzáadása az X.509-es tanúsítványhoz a portálon](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

A következő két módon regisztrálható az eszköz a Device Provisioning Service-ben:

- **Regisztrációs csoportok** Közös igazolási mechanizmussal rendelkező eszközök csoportja. Nagy számú, azonos kívánt kezdeti konfigurációval rendelkező eszközhöz vagy azonos bérlőt célzó eszközökhöz érdemes regisztrációs csoportot használni. A regisztrációs csoportok identitásigazolásáról további információt a [Biztonság](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) részben talál.

    [![Csoportregisztráció hozzáadása az X.509-es tanúsítványhoz a portálon](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Egyéni regisztrációk** A Device Provisioning Service-szel regisztrálható egyetlen eszközhöz tartozó bejegyzés. Az egyéni regisztrációk x509-tanúsítványokat vagy SAS-tokeneket használhatnak (valós vagy virtuális TPM-ben) igazolási mechanizmusként. Az egyedi kezdeti konfigurációt igénylő vagy az igazolási mechanizmusként kizárólag TPM-en vagy virtuális TPM-en keresztül SAS-tokeneket használó eszközökhöz egyéni regisztrációk használatát javasoljuk. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

Most regisztrálja az eszközt a Device Provisioning Service-példánnyal az eszköz igazolási mechanizmusán alapuló kötelező biztonsági összetevők használatával: 

1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson a **Minden erőforrás** gombra, és nyissa meg a Device Provisioning Service-t.

2. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza az **Egyéni regisztrációk** vagy a **Regisztrációs csoportok** fület az eszköz beállításának megfelelően. Kattintson a felül lévő **Hozzáadás** gombra. Válassza a **TPM** vagy az **X.509** lehetőséget igazolási *mechanizmusként*, és írja be a korábban említett megfelelő biztonsági összetevőket. Beírhat egy új **IoT Hub-eszközazonosítót**. Ha végzett, kattintson a **Mentés** gombra. 

3. Az eszköz sikeres regisztrációja után annak a következőképpen kell megjelennie a portálon:

    ![Sikeres TPM-regisztráció a portálon](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

A regisztrációt követően a kiépítési szolgáltatás megvárja, amíg az eszköz elindul, és később csatlakozik hozzá. Az eszköz első indításakor az ügyfél SDK-kódtár a lapkával együttműködve kicsomagolja az eszközön lévő biztonsági összetevőket, és ellenőrzi a Device Provisioning Service-szel meglévő regisztrációt. 

## <a name="start-the-iot-device"></a>IoT-eszköz elindítása

Az IoT-eszköz lehet valós vagy szimulált eszközt. Mivel az IoT-eszköz most már regisztrálva van egy Device Provisioning Service-példánnyal, az eszköz mostantól elindulhat és meghívhatja a kiépítési szolgáltatást, amely elvégzi az azonosítást az igazolási mechanizmus használatával. Amint a kiépítési szolgáltatás felismerte az eszközt, hozzárendeli egy IoT hubhoz. 

TPM- és az X.509-igazolást használó szimulált eszközre is talál példákat a C, a Java, a C#, a Node.js és Python esetében. A TPM-et és az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-t használó szimulált eszköz például az [Eszköz első rendszerindításának szimulálása](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device) című szakaszban található folyamatot követi. Ugyanez az eszköz az X.509-tanúsítványigazolás használata esetén ezt a [rendszerindítással foglalkozó](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device) szakaszt venné alapul.

Tekintse meg az [MXChip Iot DevKit használati útmutatóját](how-to-connect-mxchip-iot-devkit.md) a valós eszköz példájához.

Indítsa el az eszközt, hogy az eszköz ügyfélalkalmazása elindíthassa a Device Provisioning Service-szel való regisztrációt.  

## <a name="verify-the-device-is-registered"></a>Az eszköz sikeres regisztrálásának ellenőrzése

Az eszköz indítása után a következő műveleteket kell elvégezni:

1. Az eszköz regisztrációs kérést küld a Device Provisioning Service-nek.
2. TPM-eszközök esetén a Device Provisioning Service visszaküld egy regisztrációs kihívást, amelyre az eszköz válaszol. 
3. Sikeres regisztráció esetén a Device Provisioning Service visszaküldi az eszköznek az IoT Hub URI-jét, az eszközazonosítót és a titkosított kulcsot. 
4. Az eszközön lévő IoT Hub-ügyfélalkalmazás ekkor csatlakozik a hubhoz. 
5. A hubhoz való sikeres csatlakozás esetén látnia kell az eszközt az IoT Hub **IoT-eszközök** kezelőjében. 

    ![Sikeres csatlakozás a hubhoz a portálon](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

További információt a [prov_dev_client_sample.c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c)létesítő eszközügyfél-minta című témakörben talál. A minta bemutatja egy szimulált eszköz kiépítését TPM, X.509 tanúsítványok és szimmetrikus kulcsok használatával. A minta használatával kapcsolatos részletes útmutatásért tekintse vissza a [TPM](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device), [X.509](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509)és [Symmetric kulcsigazolási](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-symm-key) rövid útmutatót.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az eszköz regisztrálása
> * Az eszköz elindítása
> * Az eszköz sikeres regisztrálásának ellenőrzése

A következő oktatóanyag azt mutatja be, hogyan regisztrálhat több eszközt az elosztott terhelésű hubok között. 

> [!div class="nextstepaction"]
> [Eszközök regisztrálása elosztott terhelésű IoT Hubokon](./tutorial-provision-multiple-hubs.md)
