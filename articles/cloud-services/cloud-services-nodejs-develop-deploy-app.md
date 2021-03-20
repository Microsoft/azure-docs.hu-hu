---
title: Node.js – Első lépések útmutató
description: Megtudhatja, hogyan lehet létrehozni egy egyszerű Node.js webalkalmazást, és hogyan telepítheti azt egy Azure-felhőszolgáltatásban.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9889e0e95db84b4dbc5856ba6425f0f303161068
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741621"
---
# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service-classic"></a>Node.js-alkalmazás létrehozása és üzembe helyezése Azure Cloud Service-ben (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy egyszerű, Azure-felhőszolgáltatásban futó Node.js alkalmazást. A Cloud Services a méretezhető felhőalapú alkalmazások építőeleme az Azure-ban. Lehetővé teszik az alkalmazás előtér- és háttér-összetevőinek elkülönítését, valamint egymástól független kezelését és kibővítését.  A Cloud Services egy robusztus, dedikált virtuális gépet biztosít az egyes szerepkörök megbízható üzemeltetéséhez.

További információk a Cloud Servicesről, valamint annak összevetése az Azure Websites és a Virtual Machines szolgáltatással: [Az Azure Websites, a Cloud Services és a Virtual Machines összevetése].

> [!TIP]
> Egyszerű webhelyet szeretne készíteni? Ha csak egy egyszerű webhely előterét kívánja futtatni, fontolja meg egy [egyszerűsített webalkalmazás használatát]. Könnyedén frissíthet Cloud Service szolgáltatásra, ha a webalkalmazás növekszik és a követelmények változnak.

Az oktatóanyag utasításait követve egy webes szerepkörben lévő egyszerű webalkalmazást fog létrehozni. A Compute Emulator használatával fogja elvégezni az alkalmazás helyi tesztelését, majd PowerShell parancssori eszközökkel a telepítését.

Az alkalmazás egy egyszerű „hello world” alkalmazás:

![A webböngészőben megjelenő „Hello World” weboldal][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Előfeltételek
> [!NOTE]
> A jelen oktatóanyagban szereplő Azure PowerShell használatához Windows rendszer szükséges.

* [Azure PowerShell]telepítése és konfigurálása.
* Töltse le és telepítse az [Azure SDK for .NET 2,7] csomagot. A telepítőben válassza a következőket:
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## <a name="create-an-azure-cloud-service-project"></a>Azure Cloud Service-projektet létrehozása
Hajtsa végre az alábbi feladatokat egy új Azure Cloud Service-projekt létrehozásához alapszintű Node.js szerkezettel:

1. Futtassa a **Windows PowerShell** eszközt rendszergazdaként: a **Start menüben** vagy a **Kezdőképernyőn** keressen a **Windows PowerShell** kifejezésre.
2. A [PowerShell összekapcsolása] az előfizetéssel.
3. A projekt létrehozásához adja meg a következő PowerShell-parancsmagot:

   ```powershell
   New-AzureServiceProject helloworld
   ```

   ![A New-AzureService helloworld parancs eredménye][The result of the New-AzureService helloworld command]

   A **New-AzureServiceProject** parancsmag létrehoz egy alapszintű struktúrát egy Node.js-alkalmazás közzétételéhez egy Cloud Service szolgáltatásban. Az Azure-ban való közzétételhez szükséges konfigurációs fájlokat tartalmaz. A parancsmag emellett a munkakönyvtárat a szolgáltatás könyvtárára módosítja.

   A parancsmag a következő fájlokat hozza létre:

   * **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** és **ServiceDefinition.csdef**: az alkalmazás közzétételéhez szükséges Azure-specifikus fájlok. További információkért lásd: [Üzemeltetett szolgáltatás létrehozása az Azure-ban – áttekintés].
   * **deploymentSettings.json**: Az Azure PowerShell telepítési parancsmagok által használt helyi beállításokat tárolja.

4. Új webes szerepkör hozzáadásához adja meg az alábbi parancsot:

   ```powershell
   Add-AzureNodeWebRole
   ```

   ![Az Add-AzureNodeWebRole parancs kimenete][The output of the Add-AzureNodeWebRole command]

   Az **Add-AzureNodeWebRole** parancsmag létrehoz egy alapszintű Node.js-alkalmazást. Továbbá a **.csfg**- és **.csdef**-fájlok módosításával konfigurációs bejegyzéseket ad hozzá az új szerepkörhöz.

   > [!NOTE]
   > Ha nem ad meg egy nevet a szerepkörhöz, alapértelmezett név lesz használva. Az első parancsmag-paraméterként megadhat egy nevet: `Add-AzureNodeWebRole MyRole`

A Node.js-alkalmazás a **server.js**-fájlban van meghatározva, amely a webes szerepkör könyvtárában található (alapértelmezés szerint **WebRole1**). A kód itt látható:

```js
var http = require('http');
var port = process.env.port || 1337;
http.createServer(function (req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello World\n');
}).listen(port);
```

Ez a kód lényegében megegyezik a [nodejs.org] webhelyen található „Hello World” példával, azt leszámítva, hogy a felhőkörnyezet által hozzárendelt portszámot használja.

## <a name="deploy-the-application-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. [Aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF), vagy [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="download-the-azure-publishing-settings"></a>Az Azure közzétételi beállítások letöltése
Az alkalmazás közzétételéhez az Azure-ban először le kell töltenie a közzétételi beállításokat az Azure-előfizetéséhez.

1. Futtassa a következő Azure PowerShell-parancsmagot:

    ```powershell
    Get-AzurePublishSettingsFile
    ```

   Ezáltal a böngészője megnyitja a közzétételi beállítások letöltése oldalt. A rendszer arra kérheti, hogy jelentkezzen be egy Microsoft-fiókkal. Ebben az esetben használja az Azure-előfizetéséhez társított fiókot.

   Mentse a letöltött profilt egy olyan fájlhelyre, amelyhez könnyen hozzáfér.
2. Futtassa a következő parancsmagot a letöltött közzétételi profil importálásához:

    ```powershell
    Import-AzurePublishSettingsFile [path to file]
    ```

    > [!NOTE]
    > A közzétételi beállítások importálása után érdemes törölni a letöltött .publishSettings-fájlt, ugyanis olyan információkat tartalmaz, amelyekkel mások hozzáférhetnek a fiókjához.

### <a name="publish-the-application"></a>Az alkalmazás közzététele
A közzétételhez futtassa a következő parancsokat:

```powershell
$ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))
Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch
```

* A **-ServiceName** megadja a központi telepítés nevét. Ennek egyedi névnek kell lennie, máskülönben a közzétételi folyamat meghiúsul. A **Get-Date** parancs hozzátold egy dátum/idő sztringet, amely egyedivé teheti a nevet.
* A **-Location** megadja az adatközpontot, amelyben az alkalmazás üzemel. Az elérhető adatközpontok listájáért használja a **Get-AzureLocation** parancsmagot.
* A **-Launch** megnyit egy ablakot a böngészőben, majd az üzemeltetett szolgáltatásokra lép a telepítés befejezése után.

Miután a közzététel sikeresen megtörtént, a következőhöz hasonló válasz jelenik meg:

![A Publish-AzureService parancs kimenete][The output of the Publish-AzureService command]

> [!NOTE]
> Az első közzététel alkalmával több percet is igénybe vehet, mire az alkalmazás települ, és elérhetővé válik.

A telepítés befejezése után megnyílik egy ablak a böngészőben, amely megjeleníti a felhőszolgáltatást.

![A „hello world” oldalt megjelenítő böngészőablak – az URL-cím azt jelzi, hogy az oldal az Azure-ban üzemel.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Az alkalmazás most már az Azure-ban fut.

A **Publish-AzureServiceProject**-parancsmag az alábbi lépéseket végzi el:

1. Létrehoz egy telepítendő csomagot. A csomag az alkalmazás mappájában lévő összes fájlt tartalmazza.
2. Létrehoz egy új **tárfiókot**, ha még nem létezik. Az Azure-tárfiók az alkalmazáscsomag tárolására szolgál a telepítés során. A telepítés befejezése után nyugodtan törölheti a tárfiókot.
3. Létrehoz egy új **felhőszolgáltatást**, ha még nem létezik. A **felhőszolgáltatás** az a tároló, amelyben az alkalmazás üzemel az Azure-ba való telepítéskor. További információkért lásd: [Üzemeltetett szolgáltatás létrehozása az Azure-ban – áttekintés].
4. Közzéteszi a telepítési csomagot az Azure-ban.

## <a name="stopping-and-deleting-your-application"></a>Az alkalmazás leállítása és törlése
Érdemes lehet letiltani az alkalmazást a telepítést követően a további költségek elkerülése érdekében. Az Azure a webesszerepkör-példányok esetében óránként számol fel díjat a felhasznált kiszolgálóidő után. A kiszolgálóidő felhasználása az alkalmazás üzembe helyezésétől kezdődik, még akkor is, ha a példányok nem futnak, és leállított állapotban vannak.

1. Állítsa le az előző szakaszban létrehozott szolgáltatástelepítést a Windows PowerShell-ablakban az alábbi parancsmag használatával:

    ```powershell
    Stop-AzureService
    ```

   A szolgáltatás leállítása eltarthat néhány percig. Miután a szolgáltatás leállt, kap egy üzenetet, amely tájékoztatja a leállásról.

   ![A Stop-AzureService parancs állapota][The status of the Stop-AzureService command]
2. A szolgáltatás törléséhez hívja meg a következő parancsot:

    ```powershell
    Remove-AzureService
    ```

   Ha a rendszer rákérdez, írja be az **Y** karaktert a szolgáltatás törléséhez.

   A szolgáltatás törlése eltarthat néhány percig. Miután megtörtént a szolgáltatás törlése, kap egy üzenetet, amely tájékoztatást ad erről.

   ![A Remove-AzureService parancs állapota][The status of the Remove-AzureService command]

   > [!NOTE]
   > A szolgáltatás törlésével nem törlődik a szolgáltatás első közzétételekor létrehozott tárfiók, ezért továbbra is fizetnie kell a felhasznált tárterület után. Ha a tárterületet semmi más nem használja, megfontolhatja a törlését.

## <a name="next-steps"></a>Következő lépések
További információk: [Node.js fejlesztői központ].

<!-- URL List -->

[Az Azure Websites, a Cloud Services és a Virtual Machines összevetése]: /azure/architecture/guide/technology-choices/compute-decision-tree
[egyszerűsített webalkalmazás használatát]: ../app-service/quickstart-nodejs.md
[Azure PowerShell]: /powershell/azure/
[Azure SDK for .NET 3.0]: https://www.microsoft.com/download/details.aspx?id=54917
[PowerShell összekapcsolása]: /powershell/azure/
[nodejs.org]: https://nodejs.org/
[Üzemeltetett szolgáltatás létrehozása az Azure-ban – áttekintés]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js fejlesztői központ]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png



