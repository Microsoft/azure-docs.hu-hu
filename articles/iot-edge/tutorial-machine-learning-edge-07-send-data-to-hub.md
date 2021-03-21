---
title: 'Oktatóanyag: az eszközbeállítások transzparens átjárón keresztüli küldése – Machine Learning on Azure IoT Edge'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a fejlesztői gépet szimulált IoT Edge eszközként az adatIoT Hubba való adatküldéshez egy transzparens átjáróként konfigurált eszközön keresztül.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 25320fb8cde0cc5a38f1edf4019a2f0e07cefe14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461096"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Oktatóanyag: az adatküldés transzparens átjárón keresztül

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ebben a cikkben ismét szimulált eszközként használjuk a fejlesztői virtuális gépet. Azonban ahelyett, hogy közvetlenül a IoT Hub küld adatokat, az eszköz átküldi az adatokat a IoT Edge eszközre, amely transzparens átjáróként van konfigurálva.

A IoT Edge eszköz működését a szimulált eszköz adatküldése közben Figyeljük. Ha az eszköz futása befejeződött, tekintse meg a Storage-fiókban tárolt információt, és ellenőrizze, hogy minden a várt módon működik-e.

Ezt a lépést általában egy felhő vagy egy eszköz fejlesztője hajtja végre.

Az oktatóanyag ezen szakaszában a következőket sajátíthatja el:

> [!div class="checklist"]
>
> * Leaf-eszköz létrehozása és futtatása.
> * Győződjön meg arról, hogy a generált adatai tárolása az Azure Blob Storage-ban történik.
> * Ellenőrizze, hogy a Machine learning-modell besorolta-e az eszköz adattípusát.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. A sorozat minden cikke az előző cikkben található munkára épül. Ha ezt a cikket közvetlenül megérkezett, tekintse meg az [első cikket](tutorial-machine-learning-edge-01-intro.md) a sorozatban.

## <a name="review-device-harness"></a>Eszköz-hám áttekintése

Használja újra a [DeviceHarness projektet](tutorial-machine-learning-edge-03-generate-data.md) az alsóbb rétegbeli (vagy levél) eszköz szimulálásához. Az átlátszó átjáróhoz való csatlakozáshoz két további dolog szükséges:

* Regisztrálja a tanúsítványt, hogy az alsóbb rétegbeli IoT eszköz megbízzon a IoT Edge futtatókörnyezet által használt hitelesítésszolgáltatóban. Ebben az esetben az alsóbb rétegbeli eszköz a fejlesztői virtuális gép.
* Adja hozzá a peremhálózati átjáró teljes tartománynevét (FQDN) az eszköz kapcsolódási karakterláncához.

Tekintse meg a kódot, és figyelje meg, hogy a két elem hogyan lett implementálva.

1. Nyissa meg a fejlesztői gépen a Visual Studio Code-ot.

1. A **fájl**  >  **megnyitása mappát...** nyissa meg a C: \\ Source \\ IoTEdgeAndMlSample \\ DeviceHarness.

1. Tekintse meg a program. cs InstallCertificate () metódusát.

1. Vegye figyelembe, hogy ha a kód megkeresi a tanúsítvány elérési útját, meghívja a CertificateManager. InstallCACert metódust a tanúsítvány telepítéséhez a gépen.

1. Most tekintse meg a GetIotHubDevice metódust a TurbofanDevice osztályban.

1. Ha a felhasználó a "-g" kapcsoló használatával adja meg az átjáró teljes tartománynevét, a rendszer ezt az értéket adja át a `gatewayFqdn` változónak, amely az eszköz kapcsolódási karakterláncához lesz hozzáfűzve.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Leaf-eszköz létrehozása és futtatása

1. Ha a DeviceHarness projekt továbbra is meg van nyitva a Visual Studio Code-ban, hozza létre a projektet. A **terminál** menüben válassza a **Build feladat futtatása** és a **Létrehozás** lehetőséget.

1. Keresse meg a peremhálózati átjáró teljes tartománynevét (FQDN) úgy, hogy megkeresi a IoT Edge eszközét (Linux rendszerű virtuális gépet) a Azure Portal, és a **DNS-név** értékét az Áttekintés lapról másolja.

1. Ha még nem fut, indítsa el a IoT-eszközt (Linux rendszerű virtuális gép).

1. Nyissa meg a Visual Studio Code Terminalt. A **terminál** menüben válassza az **új terminál** lehetőséget, majd futtassa a következő parancsot, és cserélje le `<edge_device_fqdn>` az IOT Edge eszközről másolt DNS-névre (linuxos virtuális gép):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. Az alkalmazás megkísérli a tanúsítvány telepítését a fejlesztői gépre. Ha igen, fogadja el a biztonsági figyelmeztetést.

1. Amikor a rendszer kéri a IoT Hub kapcsolódási karakterláncot, kattintson a három pontra (**..**.) az Azure IoT hub eszközök paneljén, majd válassza a **Másolás IoT hub a kapcsolódási karakterlánc** lehetőséget. Illessze be az értéket a terminálba.

1. A következőhöz hasonló kimenetet fog látni:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Jegyezze fel a "GatewayHostName" kifejezést az eszköz kapcsolódási karakterláncához, ami azt eredményezi, hogy az eszköz az IoT Edge transzparens átjárón keresztül kommunikál a IoT Hub.

## <a name="check-output"></a>Kimenet keresése

### <a name="iot-edge-device-output"></a>IoT Edge eszköz kimenete

A avroFileWriter modul kimenete könnyen megfigyelhető a IoT Edge eszköz megkeresésével.

1. SSH-t a IoT Edge virtuális gépre.

1. Keresse meg a lemezre írt fájlokat.

   ```bash
   find /data/avrofiles -type f
   ```

1. A parancs kimenete az alábbi példához hasonlóan fog kinézni:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   A Futtatás időzítésének függvényében több fájl is lehet.

1. Figyeljen az időbélyegekre. A avroFileWriter modul feltölti a fájlokat a felhőbe, ha az utolsó módosítás ideje több mint 10 percet vesz igénybe (lásd \_ \_ a uploader.py-ben a AVROFILEWRITER modul módosított fájljának időtúllépését).

1. A 10 perc eltelte után a modulnak fel kell töltenie a fájlokat. Ha a feltöltés sikeres, törli a fájlokat a lemezről.

### <a name="azure-storage"></a>Azure Storage

Megfigyelheti a levélben tárolt adatok eredményét a Storage-fiókok megtekintésével, ahol az adatok továbbítása várható.

1. A fejlesztői gépen nyissa meg a Visual Studio Code-ot.

1. A Tallózás ablak "AZURE STORAGE" paneljén navigáljon a fában, és keresse meg a Storage-fiókját.

1. Bontsa ki a **blob-tárolók** csomópontot.

1. Az oktatóanyag előző részében elvégzett munkából azt várjuk, hogy az **ruldata** -tárolónak tartalmaznia kell az RUL-val rendelkező üzeneteket. Bontsa ki a **ruldata** csomópontot.

1. Egy vagy több, a következőhöz hasonló blob-fájl jelenik meg: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` .

1. Kattintson a jobb gombbal az egyik fájlra, és válassza a **blob letöltése** lehetőséget a fájl a fejlesztői gépre való mentéséhez.

1. Ezután bontsa ki a **uploadturbofanfiles** csomópontot. Az előző cikkben ezt a helyet a avroFileWriter modul által feltöltött fájlok céljának kell beállítani.

1. Kattintson a jobb gombbal a fájlokra, majd válassza a **blob letöltése** elemet a fejlesztői gépre való mentéshez.

### <a name="read-avro-file-contents"></a>Avro-fájl tartalmának olvasása

Egy egyszerű parancssori segédprogramot tartalmazott egy Avro-fájl olvasásához és a fájlban lévő üzenetek JSON-karakterláncának visszaküldéséhez. Ebben a szakaszban a telepítését és futtatását fogjuk telepíteni.

1. Nyisson meg egy terminált a Visual Studio Code-ban (**terminál**  >  **új terminál**).

1. A hubavroreader telepítése:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. A hubavroreader használatával olvassa el a **ruldata**-ból letöltött Avro-fájlt.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Vegye figyelembe, hogy az üzenet törzse a várt módon jelenik meg az eszköz azonosítója és az előre jelzett RUL alapján.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Futtassa ugyanezt a parancsot a **uploadturbofanfiles**-ből letöltött Avro-fájl átadásával.

1. A várt módon ezek az üzenetek az eredeti üzenet összes érzékelő-és működési beállítását tartalmazzák. Ezeket az adattípusokat felhasználhatja a RUL modell fejlesztésére a peremhálózati eszközön.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja meg a teljes körű oktatóanyag által használt erőforrásokat, várjon, amíg meg nem történik a létrehozott erőforrások tisztítása. Ellenkező esetben a következő lépésekkel törölheti őket:

1. Törölje a fejlesztői virtuális gép tárolására létrehozott erőforráscsoportot (ka) t, IoT Edge VM, IoT Hub, Storage-fiók, Machine learning Workspace szolgáltatás (és létrehozott erőforrások: tároló-beállításjegyzék, Application Insights, kulcstartó, Storage-fiók).

1. Törölje a Machine learning-projektet [Azure Notebooksban](https://notebooks.azure.com).

1. Ha helyileg klónozotta a tárházat, akkor a helyi tárházra hivatkozó PowerShell-vagy VS Code-alapú Windows-t zárjuk le, majd töröljük a tárház könyvtárat.

1. Ha helyileg hozott létre tanúsítványokat, törölje a c: \\ edgeCertificates mappát.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben azt használtuk, hogy fejlesztési virtuális gépe szimulálja az érzékelőt és a működési adatokat a IoT Edge eszközre küldött levélben. A rendszer ellenőrizte, hogy az eszköz moduljai a peremhálózati eszköz valós idejű működésének vizsgálatával és a Storage-fiókba feltöltött fájlok megtekintésével irányították, besorolták, megtartották és feltöltötték az adatforrásokat.

A IoT Edge képességeinek megismeréséhez próbálkozzon a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [IoT Edge-eszközök hierarchiájának létrehozása (előzetes verzió)](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)