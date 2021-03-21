---
title: Fájlok feltöltése az eszközökről az Azure IoT Hub a .NET-tel | Microsoft Docs
description: Fájlok feltöltése egy eszközről a felhőbe az Azure IoT Device SDK for .NET használatával. A feltöltött fájlok tárolása egy Azure Storage blob-tárolóban történik.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 43cafb8c5efe0581fe7c4136aa41980b3d817be2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981408"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Fájlok feltöltése az eszközről a felhőbe IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag a [felhőből az eszközre küldött üzenetek küldésére](iot-hub-csharp-csharp-c2d.md) szolgáló kódot a IoT hub oktatóanyag segítségével mutatja be, amellyel megtudhatja, hogyan használhatja a IoT hub fájlfeltöltés funkcióit. A következőket mutatja be:

* A fájlok feltöltéséhez biztonságosan biztosítson egy Azure Blob URI-t tartalmazó eszközt.

* A fájl feltöltésével kapcsolatos értesítéseket használva aktiválja a fájlt az alkalmazás hátterében lévő IoT Hub.

A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-dotnet.md) gyors üzembe helyezése és a [felhőből az eszközre irányuló üzenetek küldése az IoT hub](iot-hub-csharp-csharp-c2d.md) oktatóanyaggal mutatja be a IoT hub alapszintű eszközről a felhőbe és a felhőből az eszközre való üzenetkezelés funkcióját. Az [üzenet-útválasztás konfigurálása IoT hub](tutorial-routing.md) oktatóanyagmal az eszközről a felhőbe irányuló üzenetek megbízható tárolásának módját ismerteti Microsoft Azure Blob Storage-ban. Bizonyos helyzetekben azonban nem lehet könnyedén leképezni az eszközök által a IoT Hub által elfogadott viszonylag kis eszközről a felhőbe küldött üzeneteket. Például:

* Képeket tartalmazó nagyméretű fájlok

* Videók

* Nagy gyakorisággal vett vibrációs adatelemzés

* Az előfeldolgozott adatmennyiség valamilyen formája

Ezeket a fájlokat a rendszer általában a felhőben dolgozza fel a felhőben olyan eszközökkel, mint a [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Ha egy eszközről kell fájlokat feltölteni, továbbra is használhatja IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén két .NET konzolos alkalmazást futtat:

* **SimulatedDevice**. Ez az alkalmazás feltölt egy fájlt a Storage-ba az IoT hub által biztosított SAS URI használatával. Ez az alkalmazás egy módosított verziója, amelyet a [felhőből az eszközre küldött üzenetek küldése IoT hub](iot-hub-csharp-csharp-c2d.md) oktatóanyaggal készített.

* **ReadFileUploadNotification**. Ez az alkalmazás fogadja az IoT hub fájlfeltöltés-értesítéseit.

> [!NOTE]
> IoT Hub számos eszköz-platformot és nyelvet támogat, beleértve a C, a Java, a Python és a JavaScript használatát az Azure IoT Device SDK-k segítségével. Az eszköz Azure IoT Hubhoz való csatlakoztatásának részletes ismertetését az [Azure IoT fejlesztői központban](https://azure.microsoft.com/develop/iot) találja.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése egy eszköz alkalmazásból

Ebben a szakaszban a [felhőből az eszközre irányuló üzenetek küldése](iot-hub-csharp-csharp-c2d.md) az IoT hub-ból című témakörben található, a felhőből az eszközre irányuló üzenetek fogadásához létrehozott IoT hub.

1. A Visual Studio Megoldáskezelőban kattintson a jobb gombbal a **SimulatedDevice** projektre, majd válassza a  >  **meglévő elem hozzáadása elemet**. Keressen egy képfájlt, és foglalja bele a projektbe. Ez az oktatóanyag feltételezi, hogy a rendszerkép neve `image.jpg` .

1. Kattintson a jobb gombbal a képre, majd válassza a **Tulajdonságok parancsot**. Győződjön meg arról, hogy a **Másolás a kimeneti könyvtárba** beállítás a **mindig másolás**.

    ![A rendszerkép tulajdonságának a másolás a kimeneti könyvtárba való frissítésének helye](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. A **program. cs** fájlban adja hozzá a következő utasításokat a fájl elejéhez:

    ```csharp
    using System.IO;
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    private static async Task SendToBlobAsync(string fileName)
    {
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        await deviceClient.GetFileUploadSasUriAsync(new FileUploadSasUriRequest { BlobName = fileName });
        var blob = new CloudBlockBlob(sas.GetBlobUri());
        await blob.UploadFromFileAsync(fileName);
        await deviceClient.CompleteFileUploadAsync(new FileUploadCompletionNotification { CorrelationId = sas.CorrelationId, IsSuccess = true });

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    A `UploadToBlobAsync` metódus a feltöltött fájl fájlnevét és stream-forrását veszi fel, és kezeli a feltöltést a tárolóba. A konzol alkalmazás megjeleníti a fájl feltöltéséhez szükséges időt.

1. Adja hozzá a következő sort a **Main** metódushoz, közvetlenül előtte `Console.ReadLine()` :

    ```csharp
    await SendToBlobAsync("image.jpg");
    ```

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. A termelési kódban az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults)szolgáló újrapróbálkozási házirendeket (például exponenciális leállítási) kell végrehajtania.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely a fájlfeltöltés-értesítési üzeneteket fogadja a IoT hub-ból, amelyet a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md)hozott létre. A fájlfeltöltés-értesítési üzenetek fogadásához a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltés-értesítés fogadása

Ebben a szakaszban olyan .NET-konzol alkalmazást ír, amely a IoT Hub címről érkező fájlfeltöltés-értesítési üzeneteket fogad.

1. Az aktuális Visual Studio-megoldásban válassza a **fájl**  >  **új**  >  **projekt** lehetőséget. Az **új projekt létrehozása** területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább** gombra.

1. Nevezze el a projekt *ReadFileUploadNotification*. A **megoldás** területen válassza **a Hozzáadás a megoldáshoz** lehetőséget. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

    ![A ReadFileUploadNotification-projekt konfigurálása a Visual Studióban](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. Megoldáskezelő kattintson a jobb gombbal a **ReadFileUploadNotification** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.

1. A **NuGet csomagkezelő** területén válassza a **Tallózás** lehetőséget. Keresse meg és válassza ki a **Microsoft. Azure. Devices** elemet, majd válassza a **telepítés** lehetőséget.

    Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT Service SDK NuGet csomagra](https://www.nuget.org/packages/Microsoft.Azure.Devices/) mutató hivatkozást a **ReadFileUploadNotification** projektben.

1. A projekt **program. cs** fájljában adja hozzá a következő utasítást a fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A `{iot hub connection string}` helyőrző értékét cserélje le a IoT hub-beli, a korábban a [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)során átmásolt karakterláncra:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Megjegyzés: Ez a fogadási minta ugyanaz, mint a felhőből az eszközre irányuló üzenetek fogadása az eszköz alkalmazásból.

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

1. A Solutions Explorerben kattintson a jobb gombbal a megoldásra, majd válassza az **indítási projektek beállítása** lehetőséget.

1. Az **Általános tulajdonságok**  >  **indítási projekt** területen válassza a **több indítási projekt** elemet, majd válassza a **ReadFileUploadNotification** és a **SimulatedDevice** **indítási** műveletét. A módosítások mentéséhez kattintson az **OK** gombra.

1. Nyomja le az **F5** billentyűt. Mindkét alkalmazásnak el kell indulnia. Látnia kell a feltöltést egy adott konzol alkalmazásban, és a másik konzol alkalmazás által fogadott feltöltési értesítési üzenetet. A [Azure Portal](https://portal.azure.com/) vagy a Visual Studio Server Explorer segítségével megkeresheti a feltöltött fájl jelenlétét az Azure Storage-fiókban.

    ![A kimeneti képernyőt ábrázoló képernyőfelvétel](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a IoT Hub fájlfeltöltés funkcióit az eszközökről történő fájlfeltöltés egyszerűsítése érdekében. Továbbra is megismerheti IoT Hub funkcióit és forgatókönyveit a következő cikkekkel:

* [IoT hub programozott módon történő létrehozása](iot-hub-rm-template-powershell.md)

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)