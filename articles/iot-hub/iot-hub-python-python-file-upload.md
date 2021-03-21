---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubba a Python használatával | Microsoft Docs
description: Fájlok feltöltése egy eszközről a felhőbe a Pythonhoz készült Azure IoT Device SDK-val. A feltöltött fájlok tárolása egy Azure Storage blob-tárolóban történik.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 77d51b2c839a64567838fa4d6308d203a6bb8b82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501141"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Fájlok feltöltése az eszközről a felhőbe IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez a cikk bemutatja, hogyan tölthet fel egy fájlt az [Azure Blob Storage](../storage/index.yml)-ba a [IoT hub fájlfeltöltés funkciójának](iot-hub-devguide-file-upload.md) használatával. Ez az oktatóanyag a következőket mutatja be:

* Biztonságos tárolási tárolót biztosít a fájlok feltöltéséhez.

* A Python-ügyféllel feltöltheti a fájlokat az IoT hub használatával.

Az [eszközről az IoT hub-ra való telemetria küldése](quickstart-send-telemetry-python.md) az eszközről a felhőbe irányuló alapszintű üzenetküldési funkciókat mutatja be IoT hub. Bizonyos helyzetekben azonban nem lehet könnyedén leképezni az eszközök által a IoT Hub által elfogadott viszonylag kis eszközről a felhőbe küldött üzeneteket. Ha egy eszközről szeretne fájlokat felvenni, továbbra is használhatja IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén futtassa a Python-konzol alkalmazást:

* **FileUpload.py**, amely a Python-eszköz SDK-val feltölt egy fájlt a tárolóba.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése egy eszköz alkalmazásból

Ebben a szakaszban létrehozza az eszköz alkalmazást, hogy feltöltse a fájlt az IoT hub-ba.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-Device** csomag telepítéséhez. Ezzel a csomaggal hangolhatja össze a fájlfeltöltés az IoT hub használatával.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. A parancssorban futtassa a következő parancsot az [**Azure. Storage. blob**](https://pypi.org/project/azure-storage-blob/) csomag telepítéséhez. Ezzel a csomaggal hajthatja végre a fájl feltöltését.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Hozzon létre egy tesztoldalt, amelyet fel fog tölteni a blob Storage-ba.

1. Egy szövegszerkesztővel hozzon létre egy **FileUpload.py** -fájlt a munkamappában.

1. Adja hozzá a következő `import` utasításokat és változókat a **FileUpload.py** fájl elejéhez.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. A fájlban cserélje le a helyére `[Device Connection String]` az IoT hub-eszközhöz tartozó kapcsolatok sztringjét. Cserélje le a helyére a `[Full path to local file]` létrehozott tesztüzenet elérési útját, vagy bármely olyan fájlt, amelyet fel szeretne tölteni az eszközön.

1. Hozzon létre egy függvényt, amely feltölti a fájlt a blob Storage-ba:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Ez a függvény elemzi az átadott *blob_info* struktúrát az [Azure. Storage. blob. BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient)inicializálásához használt URL-cím létrehozásához. Ezután feltölti a fájlt az Azure Blob Storage-ba az ügyfél használatával.

1. Adja hozzá a következő kódot az ügyfél összekapcsolásához és a fájl feltöltéséhez:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Ez a kód egy aszinkron **IoTHubDeviceClient** hoz létre, és a következő API-kat használja a IoT hub-ban lévő fájlfeltöltés kezeléséhez:

    * **get_storage_info_for_blob** beolvassa az IoT hub adatait a korábban létrehozott társított Storage-fiókkal. Ezen információk közé tartozik az állomásnév, a tároló neve, a blob neve és az SAS-token. A rendszer átadja a tárolási adatokat a **store_blob** függvénynek (az előző lépésben létrehozott), így a függvény **BlobClient** az Azure Storage-ban is hitelesíthetők. A **get_storage_info_for_blob** metódus egy correlation_id is visszaad, amelyet a **notify_blob_upload_status** metódus használ. A correlation_id IoT Hub, hogy melyik blobot szeretné megjelölni.

    * **notify_blob_upload_status** értesíti a blob Storage-művelet állapotának IoT hub. Átadja a **get_storage_info_for_blob** metódus által beszerzett correlation_id. Ezt a IoT Hub használja, hogy értesítse a fájlfeltöltés-feladat állapotáról szóló értesítést figyelő bármely szolgáltatást.

1. Mentse és zárjuk be a **UploadFile.py** fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Most már készen áll az alkalmazás futtatására.

1. Futtassa a következő parancsot egy parancssorban a munkamappájában:

    ```cmd/sh
    python FileUpload.py
    ```

2. Az alábbi képernyőképen a **FileUpload** alkalmazás kimenete látható:

    ![Szimulált eszköz alkalmazás kimenete](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. A portál használatával megtekintheti a feltöltött fájlt a konfigurált tárolóban:

    ![Feltöltött fájl](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a IoT Hub fájlfeltöltés funkcióit az eszközökről történő fájlfeltöltés egyszerűsítése érdekében. A IoT hub funkcióit és forgatókönyveit továbbra is megismerheti a következő cikkekkel:

* [IoT hub programozott módon történő létrehozása](iot-hub-rm-template-powershell.md)

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

További információ az Azure Blob Storage az alábbi hivatkozásokkal:

* [Az Azure Blob Storage dokumentációja](../storage/blobs/index.yml)

* [Azure Blob Storage a Python API-hoz – dokumentáció](/python/api/overview/azure/storage-blob-readme)