---
title: Eszköz telemetria küldése az Azure IoT Central rövid útmutatóba (Python)
description: Ebben a rövid útmutatóban a Pythonhoz készült Azure IoT Hub Device SDK-val küld telemetria az eszközről a IoT Centralba.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d0dcca0c4be801f385a48afcd41b6a547bab3fbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100654894"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Gyors útmutató: telemetria küldése az eszközről az Azure IoT Centralba (Python)

**A** következőkre vonatkozik: [eszköz-alkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban egy alapszintű IoT-fejlesztési munkafolyamatot sajátíthat el. Először az Azure IoT Central használatával hozzon létre egy felhőalapú alkalmazást. Ezután az Azure IoT Python SDK-val szimulált eszközt hozhat létre, csatlakozhat IoT Centralhoz, és elküldheti az eszközről a felhőbe irányuló telemetria. 

## <a name="prerequisites"></a>Előfeltételek
- [Python 3.7 +](https://www.python.org/downloads/). A Python más verziói esetében lásd: az [Azure IoT eszköz funkciói](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    A Python verziójának naprakészen tartása érdekében futtassa a parancsot `python --version` . Ha a Python 2 és a Python 3 is telepítve van, és Python 3 környezetet használ, telepítse az összes tárat a használatával `pip3` . `pip3`A Futtatás biztosítja, hogy a kódtárak a Python 3 futtatókörnyezetre legyenek telepítve.
    > [!IMPORTANT]
    > A Python-telepítőben válassza a Python elérési **útvonalhoz való hozzáadásának** lehetőségét. Ha már telepítette a Python 3,7-es vagy újabb verzióját, győződjön meg róla, hogy a Python telepítési mappáját hozzáadta a `PATH` környezeti változóhoz.

## <a name="create-an-application"></a>Alkalmazás létrehozása
Ebben a szakaszban egy IoT Central alkalmazást hoz létre. IoT Central egy portál-alapú IoT-alkalmazás platform, amely segít csökkenteni a IoT-megoldások fejlesztésének és felügyeletének összetettségét és költségeit.

Azure IoT Central-alkalmazás létrehozása:
1. Tallózással keresse meg az [Azure IoT Central](https://apps.azureiotcentral.com/) , és jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával.
1. Navigáljon az **egyéni alkalmazások** **kiépítéséhez** és kiválasztásához.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="IoT Central Kezdőlap":::
1. Az **alkalmazás neve** mezőben adjon meg egy egyedi nevet, vagy használja a generált nevet.
1. Az **URL-cím** mezőben adjon meg egy emlékezetes alkalmazás URL-előtagját, vagy használja a generált URL-előtagot.
1. Hagyja meg az **alkalmazás sablonját** *Egyéni alkalmazásként*. Előfordulhat, hogy a legördülő menü más lehetőségeket is tartalmaz, ha a fiókban már vannak sablonok.
1. Válasszon ki egy **díjszabási csomagot** . 
    - Ha hét napig ingyen szeretné használni az alkalmazást, válassza az **ingyenes** lehetőséget. Az ingyenes alkalmazást normál díjszabásra konvertálhatja, mielőtt lejár.
    - Opcionálisan a standard díjszabási csomagot is kiválaszthatja. Ha a normál díjszabást választja, további lehetőségek jelennek meg, és meg kell adnia egy **könyvtárat**, egy **Azure-előfizetést** és egy **helyet**. A díjszabással kapcsolatos információkért lásd: az [Azure IoT Central díjszabása](https://azure.microsoft.com/pricing/details/iot-central/). 
        - A **könyvtár** az a Azure Active Directory, amelyben létrehozza az alkalmazást. A Azure Active Directory felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Ha nincs Azure Active Directory, akkor létrejön egy Azure-előfizetés létrehozásakor.
        - Az **Azure-előfizetések** lehetővé teszik az Azure-szolgáltatások példányainak létrehozását. IoT Central az előfizetéshez tartozó erőforrásokat. Ha nem rendelkezik Azure-előfizetéssel, [ingyenesen létrehozhat egyet](https://azure.microsoft.com/free/). Miután létrehozta az előfizetést, térjen vissza a IoT Central **új alkalmazás** lapra. Az új előfizetés megjelenik az **Azure-előfizetés** legördülő menüjében.
        - A **hely** az az [Azure-földrajz](https://azure.microsoft.com/global-infrastructure/geographies/) , amelyben létrehoz egy alkalmazást. Az optimális teljesítmény érdekében válasszon olyan helyet, amely fizikailag legközelebb van az eszközéhez. A hely kiválasztása után az alkalmazás nem helyezhető át másik helyre.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Új alkalmazás IoT Central párbeszédpanel":::
1. Válassza a **Létrehozás** lehetőséget.
    
    Miután IoT Central létrehozza az alkalmazást, a rendszer átirányítja az alkalmazás-irányítópultra.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="Új alkalmazás-irányítópult IoT Central":::

## <a name="add-a-device"></a>Eszköz hozzáadása
Ebben a szakaszban új eszközt ad hozzá a IoT Central alkalmazáshoz. Az eszköz egy eszköz sablonjának egy példánya, amely egy valós vagy szimulált eszközt jelöl, amelyet az alkalmazáshoz szeretne csatlakozni. 

Új eszköz létrehozása:
1. A bal oldali panelen válassza az **eszközök**, majd az **+ új** lehetőséget. Ekkor megnyílik az új eszköz párbeszédpanel.
1. Hagyja meg az **eszköz sablonját** , hogy ne legyen *hozzárendelve*.

    > [!NOTE]
    > Az egyszerűség kedvéért ebben a rövid útmutatóban olyan szimulált eszközt kell összekötni, amely nem hozzárendelt sablont használ. Ha továbbra is a IoT Central használatával felügyeli az eszközöket, megismerheti az eszközök sablonjait. Az eszközök sablonjainak használatáról az útmutató [: szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](../iot-central/core/quick-create-simulated-device.md)című témakörben talál további információt.
1. Adjon meg egy felhasználóbarát **eszköznév** és egy **eszköz azonosítóját**. Igény szerint a generált értékeket is használhatja.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="IoT Central új eszköz párbeszédpanel":::
1. Válassza a **Létrehozás** lehetőséget.

    A létrehozott eszköz megjelenik a **minden eszköz** listában.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="IoT Central minden eszköz lista":::
    
Az új eszköz kapcsolati adatainak beolvasása:
1. A **minden eszköz** listában kattintson duplán a csatolt eszköz nevére a részletek megjelenítéséhez. 
1. A felső menüben válassza a **Kapcsolódás** lehetőséget.

    Az **eszköz kapcsolata** párbeszédpanel a kapcsolat részleteit jeleníti meg:  :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IoT Central eszköz kapcsolatának részletei":::
1. Másolja a következő értékeket az **eszköz kapcsolódási** párbeszédpaneléről egy biztonságos helyre. Ezeket a következő szakaszban fogja használni az eszköz IoT Central csatlakoztatásához.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Üzenetek küldése és a telemetria figyelése
Ebben a szakaszban a Python SDK-val létrehoz egy szimulált eszközt, és telemetria küld a IoT Central alkalmazásnak. 

1. Nyisson meg egy terminált a Windows CMD vagy a PowerShell vagy a bash használatával (Windows vagy Linux rendszerhez). A terminál segítségével telepítheti a Python SDK-t, módosíthatja a környezeti változókat, és futtathatja a Python-kód mintát.

1. Másolja az [Azure IoT PYTHON SDK-eszközök mintáit](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) a helyi gépre.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Navigáljon az *Azure-IOT-SDK-Python/Azure-IOT-Device/Samples* könyvtárba.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Telepítse az Azure IoT Python SDK-t.

    ```console
    pip install azure-iot-device
    ```

1. Állítsa be az alábbi környezeti változók mindegyikét, hogy a szimulált eszköz csatlakozhasson IoT Centralhoz. A, a és a esetében a `ID_SCOPE` `DEVICE_ID` `DEVICE_KEY` IoT Central *eszköz csatlakoztatása* párbeszédpanelen mentett értékeket használja.

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > A Windows CMD esetében nincsenek a kapcsolatok sztringjét vagy más változó értékeket körülvevő idézőjelek.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux vagy Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. A terminálban futtassa a (* simple_send_temperature.................. Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld IoT Centralnak.

    A Python-minta futtatása a terminálról:
    ```console
    python ./simple_send_temperature.py
    ```

    A Python-kódot a Python IDE-ből is futtathatja a minta használatával:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Mivel a Python-kód üzenetet küld az eszközéről a IoT Central alkalmazásnak, az üzenetek az eszköz **nyers adatok** lapján jelennek meg IoT Centralban. Előfordulhat, hogy frissítenie kell a lapot a legutóbbi üzenetek megjelenítéséhez.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Képernyőfelvétel IoT Central nyers adatkimenetről":::

Az eszköz mostantól biztonságosan csatlakozik, és telemetria küld az Azure IoT.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az oktatóanyagban létrehozott IoT Central-erőforrásokra, törölheti őket a IoT Central portálról. Ha azt tervezi, hogy folytatja az útmutatóban szereplő dokumentáció folytatását, megtarthatja a létrehozott alkalmazást, és újra felhasználhatja azt más mintákhoz.

Az Azure IoT Central minta alkalmazás és annak összes eszköze és erőforrásának eltávolítása:
1. Válassza ki  >  **az alkalmazás** felügyeletét.
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás munkafolyamatát ismertette az eszköz felhőbe való biztonságos csatlakoztatásához és az eszközről a felhőbe irányuló telemetria küldéséhez. Az Azure IoT Central használatával hoz létre egy alkalmazást és egy eszközt, majd az Azure IoT Python SDK használatával létrehozta a szimulált eszközt, és elküldte a telemetria. A telemetria figyeléséhez IoT Central is használt.

A következő lépésként Fedezze fel az Azure IoT Python SDK-t az Application Samples használatával.

- [Aszinkron minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Ez a könyvtár aszinkron Python-mintákat tartalmaz a további IoT hub forgatókönyvekhez.
- [Szinkron minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Ez a könyvtár a Python 2,7-es vagy a Python 3.5 +-hez készült szinkron kompatibilitási forgatókönyvekkel használható Python-mintákat tartalmaz.
- [IoT Edge minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Ez a könyvtár Python-mintákat tartalmaz az Edge-modulok és az alsóbb rétegbeli eszközök használatához.
