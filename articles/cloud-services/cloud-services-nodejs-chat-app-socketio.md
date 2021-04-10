---
title: Alkalmazás Node.js Socket.io használatával – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan üzemeltetheti a szoftvercsatornát. IO-alapú csevegési alkalmazás az Azure-ban. A Socket.IO valós idejű kommunikációt biztosít egy node.js-kiszolgáló és-ügyfél számára.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: abc02769d7d978e14975d90ae0f98547bdc4faf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743321"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service-classic"></a>Node.js csevegési alkalmazás létrehozása az Azure Cloud Service Socket.IO (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Socket.IO valós idejű kommunikációt biztosít a node.js-kiszolgáló és az ügyfelek között. Ez az oktatóanyag végigvezeti a szoftvercsatorna üzemeltetésének lépésein. IO-alapú csevegési alkalmazás az Azure-ban. További információ a Socket.IO: [socket.IO](https://socket.io).

A befejezett alkalmazás képernyőképe a következő:

![Az Azure-ban üzemeltetett szolgáltatást megjelenítő böngészőablak][completed-app]

## <a name="prerequisites"></a>Előfeltételek
A jelen cikkben szereplő példa sikeres végrehajtásához győződjön meg arról, hogy a következő termékek és verziók telepítve vannak:

* A [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) telepítése
* [Node.js](https://nodejs.org/download/) telepítése
* A [Python-verzió 2.7.10](https://www.python.org/) telepítése

## <a name="create-a-cloud-service-project"></a>Cloud Service-projekt létrehozása
A következő lépésekkel hozza létre a Cloud Service-projektet, amely a Socket.IO alkalmazást fogja tárolni.

1. A **Start menüből** vagy a **kezdőképernyőn** keressen rá a **Windows PowerShell** kifejezésre. Végül kattintson a jobb gombbal a **Windows PowerShell** elemre, és válassza **a Futtatás rendszergazdaként** lehetőséget.

    ![Azure PowerShell ikon][powershell-menu]
2. Hozzon létre egy **c: \\ Node** nevű könyvtárat.

    ```powershell
    PS C:\> md node
    ```

3. Könyvtárak módosítása a **c: \\ Node** könyvtárba

    ```powershell
    PS C:\> cd node
    ```

4. A következő parancsok megadásával hozzon létre egy nevű új megoldást `chatapp` és egy nevű feldolgozói szerepkört `WorkerRole1` :

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    A következő választ fogja látni:

    ![A New-azureservice és a Add-azurenodeworkerrolecmdlets kimenete](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>A csevegési példa letöltése
Ebben a projektben a csevegés példáját fogjuk használni a [socket.IO GitHub-adattárból]. A példa letöltéséhez és a korábban létrehozott projekthez való hozzáadásához hajtsa végre a következő lépéseket.

1. Hozzon létre egy helyi másolatot a tárházból a **klónozás** gomb használatával. A projekt letöltéséhez használhatja a **zip** gombot is.

   ![A böngészőablakban megtekinthető https://github.com/LearnBoost/socket.io/tree/master/examples/chat zip-Letöltés ikon kiemelve](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navigáljon a helyi tárház címtár-struktúrájához, amíg meg nem érkezik a **példákat tartalmazó \\ csevegési** könyvtárba. Másolja a könyvtár tartalmát a **C: \\ Node \\ chatapp \\ WorkerRole1** könyvtárba, amelyet korábban hozott létre.

   ![Explorer – az \\ archívumból kinyert példákat tartalmazó csevegő könyvtár tartalmának megjelenítése][chat-contents]

   A fenti képernyőképen szereplő Kiemelt elemek a **példákat tartalmazó \\ csevegési** könyvtárból másolt fájlok.
3. A **C: \\ Node \\ chatapp \\ WorkerRole1** könyvtárban törölje a **server.js** fájlt, majd nevezze át a **app.js** fájlt **server.jsre**. Ezzel eltávolítja az **Add-AzureNodeWorkerRole** parancsmag által korábban létrehozott alapértelmezett **server.js** fájlt, és lecseréli azt a csevegési példa alkalmazás fájljába.

### <a name="modify-serverjs-and-install-modules"></a>Server.js módosítása és modulok telepítése
Mielőtt tesztelni szeretné az alkalmazást az Azure emulatorban, kisebb módosításokat kell végeznie. Hajtsa végre a következő lépéseket a server.js fájlba:

1. Nyissa meg a **server.js** fájlt a Visual Studióban vagy bármely szövegszerkesztőben.
2. Keresse meg a **modul függőségei** szakaszt server.js elején, és módosítsa a **sio = require ('..) tartalmú sort. //.. lib//socket. IO ")** a **sio = require (' socket. io ')** a következő ábrán látható módon:

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. Annak biztosítása érdekében, hogy az alkalmazás figyelje a megfelelő portot, nyissa meg server.js a Jegyzettömbben vagy a kedvenc szerkesztőjében, majd módosítsa a következő sort úgy, hogy a **3000** -et a **Process. env. portra** cseréli, ahogy az alábbi ábrán látható:

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

A **server.js** módosításainak mentése után a következő lépésekkel telepítheti a szükséges modulokat, majd tesztelheti az alkalmazást az Azure emulatorban:

1. A **Azure PowerShell** használatával módosítsa a könyvtárakat a **C: \\ Node \\ chatapp \\ WorkerRole1** könyvtárba, és az alábbi parancs használatával telepítse az alkalmazás által igényelt modulokat:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   Ezzel telepíti a package.jsfájljában felsorolt modulokat. A parancs végrehajtásának befejeződése után a következőhöz hasonló kimenetnek kell megjelennie:

   ![A NPM telepítési parancsának kimenete][The-output-of-the-npm-install-command]
2. Mivel ez a példa eredetileg a Socket.IO GitHub-tárházának része volt, és a relatív elérési úton közvetlenül hivatkozott a Socket.IO-könyvtárra, a Socket.IO nem hivatkoztak a fájl package.js, ezért a következő parancs kiadásával kell telepíteni:

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>Tesztelés és üzembe helyezés
1. Indítsa el az emulátort a következő parancs kiadásával:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > Ha problémák merülnek fel az emulátor indításával kapcsolatban, például: Start-AzureEmulator: váratlan hiba történt.  Részletek: váratlan hiba történt a (z) System. ServiceModel. Channels. ServiceChannel kommunikációs objektum nem használható kommunikációra, mert hibás állapotban van.
   >
   > Telepítse újra a AzureAuthoringTools v 2.7.1 és a AzureComputeEmulator v 2,7 – ellenőrizze, hogy a verzió megfelel-e.

2. Nyisson meg egy böngészőt, és navigáljon a gombra **http://127.0.0.1** .
3. Amikor megnyílik a böngészőablak, adjon meg egy becenevet, majd nyomja le az ENTER billentyűt.
   Ez lehetővé teszi az üzenetek adott becenévként való közzétételét. A többfelhasználós funkciók teszteléséhez nyissa meg a további böngészőablakokat ugyanazzal az URL-címmel, és adjon meg különböző beceneveket.

   ![Két böngészőablak, amely a Felhasználó1 és a Felhasználó2 által küldött csevegési üzeneteket jeleníti meg](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Az alkalmazás tesztelése után állítsa le az emulátort a következő parancs kiadásával:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Az alkalmazás Azure-ban való üzembe helyezéséhez használja a **publish-AzureServiceProject** parancsmagot. Például:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > Ügyeljen arra, hogy egyedi nevet használjon, ellenkező esetben a közzétételi folyamat sikertelen lesz. Az üzembe helyezés befejezését követően megnyílik a böngésző, és navigáljon az üzembe helyezett szolgáltatáshoz.
   >
   > Ha hibaüzenet jelenik meg arról, hogy a megadott előfizetés neve nem létezik az importált közzétételi profilban, le kell töltenie és importálnia kell az előfizetéséhez tartozó közzétételi profilt az Azure-ba való üzembe helyezés előtt. Tekintse meg az **alkalmazás üzembe helyezése az Azure** -ban című szakaszt a [Node.js alkalmazások Azure Cloud Service-be való felépítéséről és üzembe helyezéséről](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

   ![Az Azure-ban üzemeltetett szolgáltatást megjelenítő böngészőablak][completed-app]

   > [!NOTE]
   > Ha hibaüzenet jelenik meg arról, hogy a megadott előfizetés neve nem létezik az importált közzétételi profilban, le kell töltenie és importálnia kell az előfizetéséhez tartozó közzétételi profilt az Azure-ba való üzembe helyezés előtt. Tekintse meg az **alkalmazás üzembe helyezése az Azure** -ban című szakaszt a [Node.js alkalmazások Azure Cloud Service-be való felépítéséről és üzembe helyezéséről](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

Az alkalmazás mostantól fut az Azure-ban, és a csevegési üzeneteket továbbíthatja különböző ügyfelek között a Socket.IO használatával.

> [!NOTE]
> Az egyszerűség kedvéért ez a minta az ugyanahhoz a példányhoz csatlakozó felhasználók közötti csevegésre korlátozódik. Ez azt jelenti, hogy ha a Cloud Service két feldolgozói szerepkör-példányt hoz létre, a felhasználók csak az ugyanahhoz a feldolgozói szerepkörhöz csatlakozó másokkal csatlakozhatnak. Az alkalmazás több szerepkör-példánnyal való működésének méretezéséhez olyan technológiákat használhat, mint például a Service Bus a Socket.IO-tároló állapotának megosztására a példányok között. Példaként tekintse meg a Service Bus Queues és a témakörök használati mintáit az [Azure SDK-ban Node.js GitHub-tárházban](https://github.com/WindowsAzure/azure-sdk-for-node).
>
>

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Azure Cloud Service-ben üzemeltetett alapszintű csevegési alkalmazást. Az alkalmazás Azure-webhelyen való üzemeltetésével kapcsolatos további információkért lásd: [Node.js csevegési alkalmazás létrehozása az socket.IO-mel egy Azure][chatwebsite]-webhelyen.

További információ: a [Node.js fejlesztői központ](/azure/developer/javascript/)is.

[chatwebsite]: ./cloud-services-nodejs-develop-deploy-app.md

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-adattár]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png