---
title: C# IoT Edge GPU-Azure Stack Edge Pro-| Microsoft Docs
description: Megtudhatja, hogyan fejleszthet olyan C#IoT Edge modult, amely üzembe helyezhető Azure Stack Edge Pro GPU-eszközén.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 1aab6fa7a2ea659b489a2e65e2a6a79070edc6b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873763"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-azure-stack-edge-pro"></a>C#-IoT Edge a fájlok áthelyezéséhez a Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy IoT Edge modult a saját Azure Stack Edge Pro üzembe helyezéséhez. Azure Stack Edge Pro egy olyan tárolási megoldás, amely lehetővé teszi az adatok feldolgozását és hálózaton keresztüli elküldését az Azure-ba.

A modulokat Azure IoT Edge a saját Azure Stack Edge Pro az Azure-ba való adatokat. A cikkben használt modul implementálja azt a logikát, amely egy fájlt másol egy helyi megosztásból egy felhőalapú megosztásba az Azure Stack Edge Pro eszközén.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hozzon létre egy tároló-beállításjegyzéket a modulok (Docker-rendszerképek) tárolására és kezelésére.
> * Hozzon létre egy IoT Edge modult a saját Azure Stack Edge Pro üzembe helyezéséhez.


## <a name="about-the-iot-edge-module"></a>A IoT Edge modul

A Azure Stack Edge Pro eszköz üzembe helyezheti és futtathatja a IoT Edge modulokat. Az Edge-modulok lényegében Olyan Docker-tárolók, amelyek egy adott feladatot hajt végre, például üzeneteket küldünk egy eszközről, átalakítunk egy üzenetet, vagy üzenetet küldünk egy IoT Hub. Ebben a cikkben létre fog hozni egy modult, amely fájlokat másol egy helyi megosztásból egy felhőalapú megosztásba az Azure Stack Edge Pro eszközén.

1. A fájlok a helyi megosztásba vannak írva a Azure Stack Edge Pro eszközén.
2. A fájl eseménygenerátora létrehoz egy fájleseményt a helyi megosztásba írt összes fájlhoz. A fájlesemények a fájlok módosításakor is létrejönnek. A rendszer ezután elküldi a fájleseményeket a IoT Edge (a IoT Edge futtatáskor).
3. Az IoT Edge egyéni modul feldolgozza a fájleseményt, hogy létrehoz egy fájlesemény-objektumot, amely a fájl relatív elérési útját is tartalmazza. A modul létrehoz egy abszolút elérési utat a relatív fájlútvonal használatával, és átmásolja a fájlt a helyi megosztásból a felhőalapú megosztásba. A modul ezután törli a fájlt a helyi megosztásból.

![A Azure IoT Edge modul működése a Azure Stack Edge Pro](./media/azure-stack-edge-gpu-create-iot-edge-module/how-module-works-1.png)

Ha a fájl már a felhőalapú megosztásban van, a rendszer automatikusan feltölti az Azure Storage-fiókjába.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy Azure Stack Edge Pro futó eszköz.

    - Az eszközhöz egy társított erőforrás IoT Hub is tartozik.
    - Az eszközhöz Edge számítási szerepkör van konfigurálva.
    További információ: [Számítás konfigurálása](azure-stack-edge-j-series-deploy-configure-compute.md#configure-compute) saját Azure Stack Edge Pro.<!--Update link?-->

- A következő fejlesztési erőforrások:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Azure IoT Edge Code Visual Studio bővítménye.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Előfordulhat, hogy létre kell hoznia egy fiókot a szoftver letöltéséhez és telepítéséhez.

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Az Azure-beli tároló-beállításjegyzék egy privát Docker-tárolójegyzék az Azure-ban, amelyben tárolhatja és kezelheti privát Docker-tárolóinak rendszerképeit. A felhőben elérhető két népszerű Docker beállításjegyzék-szolgáltatás Azure Container Registry és Docker Hub. Ez a cikk a Container Registry.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza **a Create a resource > Containers > Container Registry** lehetőséget. Kattintson a **Létrehozás** lehetőségre.
3. Adja meg a következőt:

   1. Egy egyedi **regisztrációs adatbázisnév az** Azure-ban, amely 5–50 alfanumerikus karaktert tartalmaz.
   2. Válasszon ki egy **előfizetést.**
   3. Hozzon létre újat, vagy válasszon egy meglévő **erőforráscsoportot.**
   4. Válasszon egy **helyet.** Javasoljuk, hogy ez a hely ugyanaz legyen, mint a Azure Stack Edge erőforráshoz.
   5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
   6. Állítsa a termékváltozatot **Alapszintűre.**

      ![Tárolóregisztrációs adatbázis létrehozása](./media/azure-stack-edge-gpu-create-iot-edge-module/create-container-registry-1.png)
 
4. Válassza a **Létrehozás** lehetőséget.
5. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet.

    ![Hozzáférési kulcsok lekérte](./media/azure-stack-edge-gpu-create-iot-edge-module/get-access-keys-1.png)
 
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Később ezeket az értékeket használva közzéteheti a Docker-rendszerképet a regisztrációs adatbázisban, és hozzáadhatja a regisztrációs adatbázis hitelesítő adatait a Azure IoT Edge futásidejű adatbázishoz.


## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása

A következő lépések egy IoT Edge modulprojektet hoznak létre a .NET Core 2.1 SDK alapján. A projekt a Visual Studio Code-et és a Azure IoT Edge bővítményt használja.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat.

1. Az Visual Studio Code-ban válassza a **View > Command Palette** (Parancskatapaletta megtekintése) lehetőséget a VS Code parancskatapaletta megnyitásához.
2. A parancskatalógusban írja be és futtassa az **Azure: Sign in** parancsot, és az utasításokat követve jelentkezzen be az Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához:

    1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni.
    2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
    
        ![Új megoldás létrehozása – 1](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-1.png)

    3. Modulsablonként válassza a **C#** modult.
    4. Cserélje le az alapértelmezett modulnevet a hozzárendelni kívánt névre, ebben az esetben a **FileCopyModule névre.**
    
        ![Új megoldás létrehozása – 2](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-2.png)

    5. Adja meg az előző szakaszban létrehozott tároló-beállításjegyzéket az első modul rendszerképadattárának. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére.

        Az utolsó sztring így néz `<Login server name>/<Module name>` ki: . Ebben a példában a sztring a következő: `mycontreg2.azurecr.io/filecopymodule` .

        ![Új megoldás létrehozása – 3](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-3.png)

4. Nyissa meg **a Fájl > Mappa megnyitása gombra.**

    ![Új megoldás létrehozása – 4](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-4.png)

5. Keresse meg a korábban **létrehozott EdgeSolution** mappát, és mutasson rá. A VS Code-ablak betölti a IoT Edge megoldás-munkaterületet az öt legfelső szintű összetevővel. Ebben a cikkben nem szerkeszti a mappát, a `.vscode` **.gitignore** fájlt, az **.env** fájlt és a deployment.template.jsbe** fájlt.
    
    Csak a modules mappa összetevőt kell módosítania. Ez a mappa tartalmazza a modul C#-kódját és a Docker-fájlokat a modul tároló-rendszerképként való felépítéséhez.

    ![Új megoldás létrehozása – 5](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg a **Modulokat a FileCopyModule > Program.cs > modullal.**
2. A **FileCopyModule** névtér tetején adja hozzá a következő using utasításokat a később használt típusokhoz. **A Microsoft.Azure.Devices.Client.Transport.Mqtt egy** protokoll, amely üzeneteket küld a IoT Edge Hubnak.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Adja hozzá **az InputFolderPath és** **az OutputFolderPath** változót a Program osztályhoz.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Közvetlenül az előző lépés után adja hozzá a **FileEvent osztályt** az üzenet törzsének meghatározásához.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. Az **Init metódusban** a kód létrehoz és konfigurál egy **ModuleClient objektumot.** Ez az objektum lehetővé teszi, hogy a modul az MQTT protokollal csatlakozzon a helyi Azure IoT Edge a helyi modulhoz üzenetek küldésére és fogadására. Az Init metódusban használt kapcsolati sztringet az IoT Edge-futtatókörnyezet biztosítja a modulnak. A kód regisztrál egy FileCopy visszahívási hívást, hogy üzeneteket fogad IoT Edge központból az **input1 végponton** keresztül. Cserélje le **az Init metódust** a következő kódra.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Távolítsa el a **PipeMessage metódus** kódját, és a helyére szúrja be a **FileCopy kódját.**

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Mentse el ezt a fájlt.
8. A [projekthez meglévő kódmintát](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) is letölthet. Ezután ellenőrizheti a mentett fájlt a **minta program.cs** fájljában.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadott egy kódot a FileCopyModule modulhoz, amely fájlokat másol a helyi megosztásból a felhőalapú megosztásba. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VSCode-ban nyissa meg a Terminal > New Terminal (Terminál és új terminál) Visual Studio code integrált terminált.
2. Jelentkezzen be a Dockerbe a következő parancs az integrált terminálon való megadásával.

    `docker login <ACR login server> -u <ACR username>`

    Használja a tároló-beállításjegyzékből kimáselt bejelentkezési kiszolgálót és felhasználónevet.

    ![Megoldás összeállítása és IoT Edge leküldése](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Amikor a rendszer jelszót kér, írja be a jelszót. A bejelentkezési kiszolgáló, a felhasználónév és a jelszó  értékeit a tároló-beállításjegyzék hozzáférési kulcsait is lekérheti a Azure Portal.
 
3. A hitelesítő adatok megadása után lekkultálhatja a modul rendszerképét az Azure Container Registrybe. A VS Code Explorerben kattintson a jobb gombbal a fájlban **module.js** elemre, és válassza a **Build and Push IoT Edge solution (Build és leküldéses IoT Edge lehetőséget.**

    ![2. megoldás IoT Edge és leküldése](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Amikor a Visual Studio Code-nak a megoldás felépítésére való parancsot ad meg, két parancsot futtat az integrált terminálban: docker build és docker push. A két parancs létrehozza a kódot, tárolóba helyezi a CSharpModule.dll fájlt, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba.

    A rendszer kérni fogja, hogy válassza ki a modulplatformot. Válassza a *Linuxnak megfelelő amd64* lehetőséget.

    ![Platform kiválasztása](./media/azure-stack-edge-gpu-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Csak a Linux-modulok támogatottak.

    A következő figyelmeztetést figyelmen kívül hagyhatja:

    *Program.cs(77,44): CS1998 figyelmeztetés: Ez az aszinkron metódus nem rendelkezik "await" operátorokkal, és szinkron módon fog futni. Fontolja meg az "await" operátor használatát a nem blokkoló API-hívásokra való várakozáshoz, vagy az "await Task.Run(...)" használatát a processzorhoz kötött munka háttérszálon való végrehajtásához.*

4. A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A rendszerkép címe a fájl module.jsfájlban található információk alapján van `<repository>:<version>-<platform>` felépítve. Ebben a cikkben a következőnek kell lennie: `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` .

## <a name="next-steps"></a>Következő lépések

A modul üzembe helyezéséhez és futtatásához a Azure Stack Edge Pro a Modul [hozzáadása lépéseit.](azure-stack-edge-j-series-deploy-configure-compute.md#add-a-module)<!--Update link?-->
