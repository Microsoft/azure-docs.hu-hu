---
title: Service Fabric-alkalmazás üzembe helyezése egy Azure-fürtön
description: Megtudhatja, hogyan helyezhet üzembe egy meglévő alkalmazást egy újonnan létrehozott Azure Service Fabric-fürtön a Visual studióból.
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: e35b655dc8b735214de891884fe40fb951dd16cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91441291"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Oktatóanyag: Service Fabric-alkalmazás üzembe helyezése egy fürtön az Azure-ban

Ez az oktatóanyag egy sorozat második része. Azt mutatja be, hogy hogyan helyezhetők üzembe az Azure Service Fabric-alkalmazások egy új fürtön az Azure-ban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy fürtöt.
> * Alkalmazás üzembe helyezése egy távoli fürtön Visual Studio használatával.

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [.Net Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md).
> * Az alkalmazás üzembe helyezése egy távoli fürtön.
> * [HTTPS-végpont hozzáadása ASP.NET Core kezelőfelületi szolgáltatáshoz](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [A CI/CD konfigurálása az Azure Pipelines használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Az alkalmazás figyelésének és diagnosztizálásának beállítása](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Telepítse a Visual Studio 2019](https://www.visualstudio.com/)alkalmazást, és telepítse az **Azure fejlesztési** és **ASP.net, valamint a webes fejlesztési** feladatokat.
* [Telepítse a Service FABRIC SDK](service-fabric-get-started.md)-t.

> [!NOTE]
> Előfordulhat, hogy az ingyenes fiók nem teljesíti a virtuális gép létrehozásához szükséges követelményeket. Ez megakadályozza az oktatóanyag befejezését. Továbbá egy nem munkahelyi vagy nem iskolai fiók is kaphat engedélyeket a tanúsítványnak a fürthöz társított kulcstartóban való létrehozásakor. Ha a tanúsítvány létrehozásával kapcsolatos hibát tapasztal, használja helyette a portált a fürt létrehozásához. 

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Ha nem hozta létre a mintául szolgáló szavazóalkalmazást [az oktatóanyag-sorozat első részében](service-fabric-tutorial-create-dotnet-app.md), akkor le is töltheti. A parancssorablakban futtassa a következő kódot a minta alkalmazás-tárház helyi gépre történő klónozásához.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Nyissa meg az alkalmazást a Visual Studióban, futtassa rendszergazdaként, és hozza létre az alkalmazást.

## <a name="create-a-cluster"></a>Fürt létrehozása

Most, hogy az alkalmazás készen áll, hozzon létre egy Service Fabric fürtöt, majd telepítse az alkalmazást a fürtön. A [Service Fabric-fürt](./service-fabric-deploy-anywhere.md) olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait.

Ebben az oktatóanyagban egy új, három csomópontos tesztelési fürtöt hoz létre a Visual Studio IDE-ben, majd közzéteszi az alkalmazást a fürtön. A termelési fürtök létrehozásával kapcsolatos információkért tekintse meg a [fürt létrehozása és kezelése című oktatóanyagot](service-fabric-tutorial-create-vnet-and-windows-cluster.md) . Az alkalmazást telepítheti egy meglévő, a [Azure Portal](https://portal.azure.com)által korábban létrehozott fürtre is, a [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) vagy az [Azure CLI](./scripts/cli-create-cluster.md) parancsfájlok használatával vagy egy [Azure Resource Manager sablonból](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> A szavazati alkalmazás és sok más alkalmazás a Service Fabric fordított proxy használatával kommunikál a szolgáltatások között. A Visual studióból létrehozott fürtök esetében a fordított proxy alapértelmezés szerint engedélyezve van. Ha meglévő fürtön végez üzembe helyezést, engedélyeznie kell [a fordított proxyt a fürtben](service-fabric-reverseproxy-setup.md) a szavazati alkalmazás működéséhez.


### <a name="find-the-votingweb-service-endpoint"></a>A VotingWeb szolgáltatásvégpontjának megkeresése

A szavazó alkalmazás előtér-webszolgáltatása egy adott portot figyel (8080, ha követte a [jelen oktatóanyag-sorozat első részében](service-fabric-tutorial-create-dotnet-app.md)ismertetett lépéseket. Az alkalmazások Azure-beli fürtön való üzembe helyezésekor a fürt és az alkalmazás is Azure-terheléselosztó mögött fut. Az alkalmazás portját egy szabály használatával kell megnyitni az Azure Load balancerben. A szabály bejövő forgalmat küld a terheléselosztó és a webszolgáltatás között. Ezt a portot a **VotingWeb/PackageRoot/ServiceManifest.xml** fájlban találhatja meg az **Endpoint** elemben. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Jegyezze fel a szolgáltatási végpontot, amely egy későbbi lépésben szükséges.  Ha egy meglévő fürtön végez üzembe helyezést, nyissa meg ezt a portot úgy, hogy létrehoz egy terheléselosztási szabályt és egy mintavételt az Azure Load balancerben egy [PowerShell-parancsfájl](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) használatával vagy a fürthöz tartozó terheléselosztó segítségével a [Azure Portal](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Tesztelési fürt létrehozása az Azure-ban
A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget.

A **csatlakoztatási végpont** területen válassza az **új fürt létrehozása** lehetőséget.  Ha egy meglévő fürtön végzi a telepítést, válassza ki a fürt végpontját a listából.  Megnyílik a Service Fabric-fürt létrehozása párbeszédpanel.

A **fürt** lapon adja meg a **fürt nevét** (például "mytestcluster"), válassza ki az előfizetését, válasszon ki egy régiót a fürt számára (például az USA déli középső régiója), adja meg a fürtcsomópontok számát (három csomópontot javasolt egy tesztelési fürthöz), és adjon meg egy erőforráscsoportot (például "mytestclustergroup"). Kattintson a **Tovább** gombra.

![A képernyőképen a fürt létrehozása Service Fabric fürt létrehozása párbeszédpanel fürt lapja látható.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

A **tanúsítvány** lapon adja meg a fürt tanúsítványának jelszavát és kimeneti elérési útját. Az önaláírt tanúsítványok PFX-fájlként jönnek létre, és a megadott kimeneti elérési útra lesznek mentve.  A tanúsítvány a csomópontok közötti és az ügyfél és a csomópont közötti biztonsághoz egyaránt használatos.  Ne használjon önaláírt tanúsítványt az üzemi fürtökhöz.  Ezt a tanúsítványt a Visual Studio a fürttel való hitelesítésre és az alkalmazások központi telepítésére használja. Válassza a **tanúsítvány importálása** lehetőséget a pfx et currentuser\my tanúsítványtárolóba telepítéséhez a számítógépén.  Kattintson a **Tovább** gombra.

![A képernyőképen a Service Fabric-fürt létrehozása párbeszédpanel tanúsítvány lapja látható.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

A **virtuális gép részletei** lapon adja meg a fürt rendszergazdai fiókjának **felhasználónevét** és **jelszavát** .  Válassza ki a fürt csomópontjaihoz tartozó **virtuálisgép-rendszerképet** és a fürt minden egyes csomópontjának **virtuális gép méretét** .  Kattintson a **speciális** fülre.

![A képernyőképen a Service Fabric-fürt létrehozása párbeszédpanel V M részletek lapja látható.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

A **portok** területen adja meg a VotingWeb szolgáltatás végpontját az előző lépéstől (például 8080).  A fürt létrehozásakor ezek az alkalmazások portjai az Azure Load balancerben nyílnak meg a fürtre irányuló forgalom továbbításához.  A fürt létrehozásához kattintson a **Létrehozás** gombra, amely több percet is igénybe vehet.

![Képernyőfelvétel: a Service Fabric-fürt létrehozása párbeszédpanel Speciális lapja.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Az alkalmazás közzététele a fürtön

Ha az új fürt elkészült, közvetlenül a Visual studióból is üzembe helyezheti a szavazati alkalmazást.

A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget. Megjelenik a **Publish** (Közzététel) párbeszédpanel.

A **csatlakoztatási végpont** területen válassza ki az előző lépésben létrehozott fürt végpontját.  Például: "mytestcluster.southcentral.cloudapp.azure.com:19000". Ha a **speciális kapcsolatok paramétereit** választja, a tanúsítvány információit automatikusan ki kell tölteni.  
![Service Fabric-alkalmazás közzététele](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Válassza a **Közzététel** lehetőséget.

Az alkalmazás üzembe helyezése után nyisson meg egy böngészőt, és adja meg a fürt címeit, majd a **következőket: 8080**. Vagy adja meg egy másik konfigurált port számát. Például: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Ezután megjelenik a fürtön futó alkalmazás az Azure-ban. A szavazás weboldalán próbáljon hozzáadni és törölni szavazási lehetőségeket, valamint szavazni ezek közül egyre vagy többre.

![Service Fabric szavazási minta](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Következő lépések
Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Hozzon létre egy fürtöt.
> * Alkalmazás üzembe helyezése egy távoli fürtön Visual Studio használatával.

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [HTTPS engedélyezése](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
