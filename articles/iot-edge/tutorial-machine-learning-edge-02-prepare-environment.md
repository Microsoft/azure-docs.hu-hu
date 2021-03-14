---
title: 'Oktatóanyag: környezet beállítása – Machine Learning Azure IoT Edge'
description: 'Oktatóanyag: a környezet előkészítése a gépi tanuláshoz készült modulok fejlesztésére és üzembe helyezésére a peremhálózati környezetben.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 723d7b6ae5b96cf6dc622be1b1bb98065151787f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463153"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Oktatóanyag: környezet beállítása a gépi tanuláshoz IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ez a cikk segít felkészülni a környezet fejlesztésére és üzembe helyezésére. Először állítson be egy fejlesztői gépet minden szükséges eszközzel. Ezután hozza létre a szükséges Felhőbeli erőforrásokat az Azure-ban.

Az oktatóanyag ezen szakaszában a következőket sajátíthatja el:

> [!div class="checklist"]
>
> * Hozzon létre egy virtuális gépet a fejlesztéshez.
> * Hozzon létre egy IoT Hub és egy felhőalapú tárhelyet a fejlesztési környezetének használatához.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. A sorozat minden cikke az előző cikkben található munkára épül. Ha ezt a cikket közvetlenül megérkezett, tekintse meg az [első cikket](tutorial-machine-learning-edge-01-intro.md) a sorozatban.

## <a name="set-up-the-development-vm"></a>A fejlesztési virtuális gép beállítása

Ezt a lépést általában egy felhőalapú fejlesztő hajtja végre. Néhány szoftver hasznos lehet egy adattudós számára is.

Létrehoztunk egy PowerShell-szkriptet, amely létrehoz egy Azure-beli virtuális gépet a már konfigurált előfeltételek közül. Az általunk létrehozott virtuális gépnek képesnek kell lennie a [beágyazott virtualizálás](../virtual-machines/windows/nested-virtualization.md)kezelésére, ezért a [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) gép méretét választotta.

A fejlesztői virtuális gép a következőket fogja beállítani:

* Windows 10
* [Csokoládés](https://chocolatey.org/)
* [Docker asztali Windows rendszerhez](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [A git Hitelesítőadat-kezelő a Windows rendszerhez](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/)
* [VS Code-bővítmények](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

A fejlesztői virtuális gép nem feltétlenül szükséges – az összes fejlesztői eszköz futtatható a helyi gépen. Javasoljuk azonban, hogy a virtuális gép használatával biztosítson egy szintű játékteret.

A virtuális gép létrehozása és konfigurálása körülbelül 30 percet vesz igénybe.

1. A Machine Learning klónozása vagy letöltése a helyi számítógépre [IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) .

1. Nyissa meg a PowerShellt rendszergazdaként, és navigáljon a gyökérkönyvtár alatt található **\IoTEdgeAndMlSample\DevVM** könyvtárhoz, ahol letöltötte a kódot. A forrás gyökérkönyvtárát a következő módon fogjuk megtekinteni: `srcdir` .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   A DevVM könyvtár tartalmazza az oktatóanyag elvégzéséhez megfelelő Azure-beli virtuális gép létrehozásához szükséges fájlokat.

1. Futtassa a következő parancsot a parancsfájlok végrehajtásának engedélyezéséhez. Ha **a rendszer kéri, válassza az igen** lehetőséget.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Create-AzureDevVM.ps1 futtatása.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Ha a rendszer kéri, adja meg az alábbi információkat:

    * **Azure-előfizetés azonosítója**: az előfizetés-azonosítója, amely az [Azure-előfizetésekben](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) található a portálon.
    * **Erőforráscsoport neve**: egy új vagy meglévő erőforráscsoport neve az Azure-ban.
    * **Hely**: válassza ki azt az Azure-helyet, ahová a virtuális gépet létre kívánja hozni. Például: "West US 2" vagy "Észak-Európa". További információ: Azure-beli [telephelyek](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Felhasználónév**: adjon meg egy emlékezetes nevet a virtuális gép rendszergazdai fiókjához.
    * **Password (jelszó**): állítsa be a virtuális gép rendszergazdai fiókjához tartozó jelszót.

   A szkript több percig fut, ahogy a következő lépéseket hajtja végre:

    1. Telepíti a [Azure PowerShell az modult](/powershell/azure/new-azureps-module-az).
    1. Felszólítja, hogy jelentkezzen be az Azure-ba.
    1. Megerősíti a virtuális gép létrehozásához szükséges információkat. Nyomja le az **y** vagy az **ENTER** billentyűt a folytatáshoz.
    1. Létrehozza az erőforráscsoportot, ha az nem létezik.
    1. Üzembe helyezi a virtuális gépet.
    1. Engedélyezi a Hyper-V használatát a virtuális gépen.
    1. Telepíti a szoftverek fejlesztésére és a minta tárház klónozására vonatkozó szükséges szoftvereket.
    1. Újraindítja a virtuális gépet.
    1. RDP-fájlt hoz létre az asztalon a virtuális géphez való csatlakozáshoz.

   Ha a rendszer felszólítja a virtuális gép nevének újraindítására, akkor a szkript kimenetéről másolhatja a nevét. A kimenet a virtuális géphez való csatlakozáshoz használt RDP-fájl elérési útját is megjeleníti.

### <a name="set-auto-shutdown-schedule"></a>Automatikus leállítási ütemterv beállítása

A Cost csökkentése érdekében a fejlesztési virtuális gép a 1900 PST-re beállított automatikus leállítási ütemtervtel lett létrehozva. Előfordulhat, hogy frissítenie kell ezt a beállítást a helytől és az ütemtervtől függően. A leállítási ütemterv frissítése:

1. A Azure Portal navigáljon a parancsfájl által létrehozott virtuális gépre.

1. A bal oldali ablaktábla **műveletek** területén válassza az **automatikus leállítás** lehetőséget.

1. Igény szerint módosítsa az **ütemezett leállítást** és az **időzónát** , majd válassza a **Mentés** lehetőséget.

## <a name="connect-to-the-development-vm"></a>Kapcsolódás a fejlesztési virtuális géphez

Most, hogy létrehozott egy virtuális gépet, be kell fejeznie az oktatóanyag befejezéséhez szükséges szoftver telepítését.

1. Kattintson duplán arra az RDP-fájlra, amelyet a parancsfájl hozott létre az asztalon.

1. Ekkor megjelenik egy párbeszédpanel, amely azt jelzi, hogy a távoli kapcsolatok közzétevője ismeretlen. Ez elfogadható, ezért válassza a **kapcsolat** lehetőséget.

1. Adja meg a virtuális gép létrehozásához megadott rendszergazdai jelszót, majd kattintson **az OK** gombra.

1. A rendszer kérni fogja, hogy fogadja el a virtuális gép tanúsítványát. Válassza az **Igen** lehetőséget.

## <a name="install-visual-studio-code-extensions"></a>Visual Studio Code-bővítmények telepítése

Most, hogy csatlakozott a fejlesztői géphez, adjon hozzá néhány hasznos bővítményt a Visual Studio Code-hoz, hogy egyszerűbbé váljon a fejlesztési élmény.

1. Kapcsolódjon a fejlesztői virtuális géphez, nyisson meg egy PowerShell-ablakot, és navigáljon a **C:\source\IoTEdgeAndMlSample\DevVM** könyvtárhoz. Ezt a könyvtárat a virtuális gépet létrehozó parancsfájl hozta létre.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Futtassa a következő parancsot a parancsfájlok végrehajtásának engedélyezéséhez. Ha **a rendszer kéri, válassza az igen** lehetőséget.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Futtassa a Visual Studio Code Extensions parancsfájlt.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. A szkript néhány percig fut a VS Code-bővítmények telepítése során:

    * Azure IoT-eszközök
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub és tárterület beállítása

Ezeket a lépéseket általában egy felhőalapú fejlesztő hajtja végre.

Az Azure IoT Hub bármely IoT-alkalmazás szíve, mivel kezeli a IoT-eszközök és a felhő közötti biztonságos kommunikációt. Ez a fő koordinációs pont a IoT Edge Machine learning megoldás működéséhez.

* A IoT Hub útvonalak használatával irányítja a bejövő adatok IoT-eszközökről más alsóbb rétegbeli szolgáltatásokba. Kihasználjuk IoT Hub útvonalak előnyeit, hogy az eszközről az Azure Storage-ba küldjük az eszközöket. Az Azure Storage-ban a Azure Machine Learning a hátralévő hasznos élettartam (RUL) besorolás betanításához használja az eszközön tárolt adatmennyiséget.

* Az oktatóanyag későbbi részében a IoT Hub használatával konfigurálhatja és kezelheti Azure IoT Edge eszközét.

Ebben a szakaszban egy parancsfájl használatával létrehoz egy Azure IoT hubot és egy Azure Storage-fiókot. Ezután a Azure Portal egy olyan útvonalat állít be, amely továbbítja a hub által az Azure Storage-tárolóba fogadott adattovábbítási útvonalakat. Ezek a lépések körülbelül 10 percet vesznek igénybe.

1. Kapcsolódjon a fejlesztői virtuális géphez, nyisson meg egy PowerShell-ablakot, és navigáljon a **IoTHub** könyvtárhoz.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Futtassa a létrehozási parancsfájlt. Használja ugyanazokat az értékeket az előfizetés-AZONOSÍTÓhoz, a helyhez és az erőforráscsoporthoz, mint a fejlesztési virtuális gép létrehozásakor.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * A rendszer kéri majd, hogy jelentkezzen be az Azure-ba.
    * A parancsfájl megerősíti a hub és a Storage-fiók létrehozásához szükséges információkat. Nyomja le az **y** vagy az **ENTER** billentyűt a folytatáshoz.

A szkript futása körülbelül két percet vesz igénybe. Ha elkészült, a parancsfájl az IoT hub és a Storage-fiók nevét adja meg.

## <a name="review-route-to-storage-in-iot-hub"></a>Áttekintheti a tárolási útvonalat IoT Hub

Az IoT hub létrehozásának részeként az előző szakaszban futtatott szkript létrehozta az egyéni végpontot és egy útvonalat is. IoT Hub útvonalak egy lekérdezési kifejezésből és egy végpontból állnak. Ha egy üzenet megfelel a kifejezésnek, az adatküldés a társított végpont útvonalán történik. A végpontok lehetnek Event Hubs, Service Bus várólisták és témakörök. Ebben az esetben a végpont egy blob-tároló egy Storage-fiókban. A szkripttel létrehozott útvonal áttekintéséhez használja a Azure Portal.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com) , és keresse meg az oktatóanyaghoz használt erőforráscsoportot.

1. Az erőforrások listájában válassza ki a parancsfájl által létrehozott IoT Hub. A név egy véletlenszerű karakterrel végződő névvel fog rendelkezni, például: `IotEdgeAndMlHub-jrujej6de6i7w` .

1. A bal oldali ablaktábla **üzenetkezelés** területén válassza az üzenet- **Útválasztás** lehetőséget.

1. Az **üzenet-útválasztás** lapon válassza az **Egyéni végpontok** fület.

1. Bontsa ki a **Storage** szakaszt:

   ![Ellenőrizze, hogy az turbofanDeviceStorage szerepel-e az egyéni végpontok listájában](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   A **turbofanDeviceStorage** az egyéni végpontok listájában látható. Figyelje meg a végpont alábbi jellemzőit:

   * A létrehozott blob Storage-tárolóra mutat, amelyet a `devicedata` **tároló neve** jelez.
   * A **Fájlnév formátuma** a név utolsó elemének partíciója. Ezt a formátumot úgy találjuk, hogy az oktatóanyag későbbi részében Azure Notebooks a fájl műveleteihez.
   * Az **állapota** kifogástalannak kell lennie.

1. Válassza az **Útvonalak** lapot.

1. Válassza ki a **turbofanDeviceDataToStorage** nevű útvonalat.

1. Az **útvonalak részletei** lapon vegye figyelembe, hogy az útvonal végpontja az **turbofanDeviceStorage** végpont.

   ![A turbofanDeviceDataToStorage útvonal részleteinek áttekintése](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Tekintse meg az **útválasztási lekérdezést**, amely **igaz** értékre van beállítva. Ez a beállítás azt jelenti, hogy az összes eszköz telemetria-üzeneteinek egyezniük kell ezzel az útvonallal; Ezért minden üzenet el lesz küldve a **turbofanDeviceStorage** -végpontnak.

1. Mivel nem történt módosítás, csak zárjuk be ezt a lapot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag egy olyan készlet része, amelyben az egyes cikkek az előzőekben végzett munkára épülnek. Várjon, amíg az összes erőforrást el nem végezte, amíg el nem végzi a végleges oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehoztunk egy IoT Hub, és konfiguráltunk egy útvonalat egy Azure Storage-fiókhoz. A következő lépésben szimulált eszközökből származó adatok lesznek elküldve a IoT Hub a Storage-fiókba. Az oktatóanyag későbbi részében, miután konfiguráltuk IoT Edge eszközt és modulokat, újra felkeresjük az útvonalakat, és egy kicsit többet is megvizsgálunk az útválasztási lekérdezésben.

Folytassa a következő cikkel a figyeléshez szimulált eszköz létrehozásához.

> [!div class="nextstepaction"]
> [Eszköz-adategységek előállítása](tutorial-machine-learning-edge-03-generate-data.md)
