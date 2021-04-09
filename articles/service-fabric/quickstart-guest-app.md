---
title: Meglévő alkalmazás gyors üzembe helyezése fürtön
description: Meglévő Node.js-alkalmazás üzemeltetése egy Azure Service Fabric-fürtön a Visual Studio használatával.
ms.topic: conceptual
ms.date: 12/06/2017
ms.custom: devx-track-js
ms.openlocfilehash: dfd1fe9db54925bc17a53a7686fa34d5ea53cd5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013598"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Node.js-alkalmazás üzemeltetése az Azure Service Fabricban

Ez a rövid útmutató segítséget nyújt egy meglévő alkalmazás (ebben a példában a Node.js) Azure-on futó Service Fabric-fürtön történő üzembe helyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md). Ide tartozik a Service Fabric SDK és a Visual Studio 2019 vagy a 2015 telepítése.

Egy meglévő Node.js-alkalmazással is rendelkeznie kell, amelyet üzembe helyezhet. Ez a rövid útmutató egy egyszerű Node.js-webhelyet használ, amely [innen][download-sample] tölthető le. Miután a következő lépésben létrehozza a projektet, csomagolja ki ezt a fájl a következő mappába: `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\`.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot][create-account].

## <a name="create-the-service"></a>A szolgáltatás létrehozása

Indítsa el a Visual studiót **rendszergazdaként**.

Projekt létrehozása `CTRL`+`SHIFT`+`N` használatával

Az **Új projekt** párbeszédpanelen válassza a **Felhő > Service Fabric-alkalmazás** elemet.

Nevezze el az alkalmazás **MyGuestApp** , és kattintson a **Létrehozás** gombra.

>[!IMPORTANT]
>A Node.js gyakran túllépi a Windows elérési utakra vonatkozó 260 karakteres korlátját. Használjon rövid elérési utat a projekthez, például: **c:\code\svc1**. Igény szerint az **[alábbi utasításokat](https://stackoverflow.com/a/41687101/1664231)** követve engedélyezheti a hosszú fájlelérési utakat a Windows 10 rendszerben.
   
![A Visual Studio Új projekt párbeszédpanelje][new-project]

A következő párbeszédpanelen bármilyen típusú Service Fabric-szolgáltatást létrehozhat. Ebben a rövid útmutatóban válassza az **Vendég végrehajtható** lehetőséget.

Adja a **MyGuestService** nevet a szolgáltatásnak, adja meg a következő értékeket a jobb oldali beállításokhoz:

| Beállítás                   | Érték |
| ------------------------- | ------ |
| Kódcsomag mappája       | _&lt;az Node.js alkalmazást tartalmazó mappa&gt;_ |
| Kódcsomag viselkedése     | A mappa tartalmának másolása a projektbe |
| Program                   | node.exe |
| Argumentumok                 | server.js |
| Munkamappa            | CodePackage |

Kattintson az **OK** gombra.

![A Visual Studio Új szolgáltatás párbeszédpanelje][new-service]

A Visual Studio létrehozza az alkalmazásprojektet és az aktorszolgáltatás-projektet, és megjeleníti őket a Megoldáskezelőben.

Az Application Project (**MyGuestApp**) nem tartalmaz közvetlenül semmilyen kódot. A projekt szolgáltatási projektekre hivatkozik. Emellett három más típusú tartalmat is tartalmaz:

* **Profilok közzététele**  
Különböző környezetek eszközbeállításai.

* **Parancsfájlok**  
Az alkalmazás üzembe helyezéséhez/frissítéséhez szükséges PowerShell-szkript.

* **Alkalmazásdefiníció**  
Tartalmazza az alkalmazásjegyzéket az *ApplicationPackageRoot* területen. A társított alkalmazások *ApplicationParameters* területen található paraméterfájljai határozzák meg az alkalmazást, és teszik lehetővé az adott környezetnek megfelelően történő konfigurálást.
    
A szolgáltatási projekt tartalmának áttekintéséhez lásd: [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-quick-start.md).

## <a name="set-up-networking"></a>A hálózat beállítása

A példában üzembe helyezett Node.js-alkalmazás a **80**-as portot használja, és a Service Fabricnak tudnia kell, hogy ennek a portnak elérhetőnek kell lennie.

Nyissa meg a **ServiceManifest.xml** fájlt a projektben. A jegyzékfájl alján van egy `<Resources> \ <Endpoints>` már definiált bejegyzés. Módosítsa ezt a bejegyzést a `Port`, a `Protocol` és a `Type` hozzáadásával. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ha lenyomja az **F5** billentyűt, és futtatja a projektet, a rendszer telepíti a helyi fürtre. Azonban helyezzük üzembe inkább az Azure-ban.

Kattintson a jobb gombbal a projektre, és válassza a **Közzététel** lehetőséget, amely egy párbeszédpanelt nyit meg az Azure-on történő közzétételhez.

![Service Fabric-szolgáltatáshoz tartozó Közzététel az Azure-ba párbeszédpanel][publish]

Válassza ki a **PublishProfiles\Cloud.xml** célprofilt.

Ha korábban még nem tette meg, válassza ki az üzembe helyezés céljaként szolgáló Azure-fiókot. Ha még nem rendelkezik ilyen fiókkal, [regisztráljon egyet][create-account].

A **Kapcsolati végpont** területen válassza ki az üzembe helyezés céljaként szolgáló Service Fabric-fürtöt. Ha még nem rendelkezik ilyennel, válassza az **&lt; új fürt létrehozása.. &gt; .** lehetőséget, amely megnyitja a webböngésző ablakát a Azure Portal. További információért lásd: [Fürt létrehozása a portálon](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal). 

A Service Fabric-fürt létrehozásakor ügyeljen rá, hogy az **Egyéni végpontok** beállítás értéke **80** legyen.

![Service Fabric-csomóponttípus konfigurálása egyéni végponttal][custom-endpoint]

Az új Service Fabric-fürt létrehozása eltarthat egy ideig. Miután létrejött, lépjen vissza a közzététel párbeszédpanelre, és válassza a **&lt; frissítés &gt;** lehetőséget. Válassza ki a legördülő listában megjelenő új fürtöt.

Kattintson a **Közzététel** gombra, és várjon, amíg az üzembe helyezés befejeződik.

Ez eltarthat néhány percig. Az üzembe helyezés után még néhány percet várnia kell, amíg az alkalmazás teljes mértékben elérhető lesz.

## <a name="test-the-website"></a>A webhely tesztelése

A szolgáltatás közzététel után tesztelje a szolgáltatást egy webböngészőben. 

Először nyissa meg az Azure Portalt, majd keresse meg a Service Fabric-szolgáltatást.

Ellenőrizze a szolgáltatás címének Áttekintés paneljét. Használja az _Ügyfélkapcsolati végpont_ tulajdonságban szereplő tartománynevet. Például: `http://mysvcfab1.westus2.cloudapp.azure.com`.

![A Service Fabric áttekintési panelje az Azure Portalon][overview]

Navigáljon erre a címre, ahol megjelenik a `HELLO WORLD` válasz.

## <a name="delete-the-cluster"></a>A fürt törlése

Ne felejtse el törölni az ehhez a rövid útmutatóhoz létrehozott összes erőforrást, mivel ezekért az erőforrásokért kell fizetnie.

## <a name="next-steps"></a>Következő lépések
További információk a [futtatható vendégalkalmazásokról](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
