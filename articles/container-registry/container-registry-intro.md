---
title: Privát Docker-tárolójegyzékek az Azure-ban – áttekintés
description: Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ce870bfb8d29f7a808962e4d273388ab31186f10
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828596"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Az Azure-beli privát Docker-tárolójegyzékek bemutatása

Az Azure Container Registry egy felügyelt [Docker jegyzékszolgáltatás](https://docs.docker.com/registry/), amely a nyílt forráskódú Docker Registry 2.0 technológiára épül. Az Azure-beli tároló-beállításjegyzékek létrehozásával és fenntartásával tárolhatja és kezelheti privát [Docker-tárolóinak](https://www.docker.com/what-docker) rendszerképeit.

Az Azure-beli tároló-beállításjegyzékek használhatja a meglévő tárolófejlesztési és üzembe helyezési folyamatok, illetve [ACR feladatok](#azure-container-registry-tasks) hozhat létre tárolórendszerképeket az Azure-ban. Igény szerinti vagy teljesen automatizált összeállítás a forráskód véglegesítésével és az alapszintű rendszerkép frissítésével.

A Dockerrel és a tárolókkal kapcsolatos háttér-információkért lásd a [Docker áttekintő ismertetését](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Használati esetek

Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) és [Docker Swarm](https://docs.docker.com/swarm/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve az [Azure Kubernetes Service (AKS)](../aks/index.yml), az [App Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például megcélozhat egy tároló-beállításjegyzéket egy olyan folyamatos integrációs és üzembe helyezési eszközből, mint az [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) vagy a [Jenkins](https://jenkins.io/).

Alkalmazás-lemezképek automatikusan újraépítheti a saját alaplemezképek frissítésekor ACR feladatok konfigurálása, illetve a rendszerképek létrehozásának automatizálása, ha a csapata véglegesíti a kódot egy Git-tárházba. Többlépéses feladatok automatizálásához készítése, tesztelése és a javítással, a felhőben, párhuzamosan több tároló-rendszerkép létrehozása.

Az Azure biztosítja az eszközöket, beleértve az Azure parancssori felület, az Azure portal és API-támogatás kezelése az Azure container registryk. Szükség esetén telepítse a [Docker-bővítmény a Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) és a [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítmény használata az Azure container registryk. Lekéréses és a egy Azure container registry rendszerképek leküldése vagy ACR feladatok, mindezt a Visual Studio Code-ot.

## <a name="key-concepts"></a>Fő fogalmak

* **Beállításjegyzék** – Létrehozhat egy vagy több tároló-beállításjegyzéket Azure-előfizetésében. Beállításjegyzékek három termékváltozatban érhetők el: [Alapszintű, Standard és prémium szintű](container-registry-skus.md), amelyek mindegyike támogatja a webhook-integrációt, a beállításjegyzék-hitelesítés az Azure Active Directory és a törlési funkció. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. Haladó szintű replikációs és tárolórendszerkép-elosztási forgatókönyvekhez használja a Prémium szintű beállításjegyzékek [georeplikációs](container-registry-geo-replication.md) funkcióját. A teljes tartománynév `myregistry.azurecr.io` formában van.

  Ön [hozzáférésének](container-registry-authentication.md) továbbíthat egy tárolóregisztrációs adatbázisba, egy Azure-identitás, az Azure Active Directory-alapú használatával [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md), vagy rendszergazdai fiókkal. Jelentkezzen be a beállításjegyzék használatával az Azure CLI vagy a standard szintű `docker login` parancsot.

* **Tárház** -beállításjegyzék tartalmaz egy vagy több tárat, amely virtuális azonos nevű, de eltérő címkéket vagy emésztett tárolórendszerképek csoportjai. Az Azure Container Registry támogatja a többszintű adattárnévtereket. A többszintű névterekkel csoportba rendezheti egy adott alkalmazáshoz vagy alkalmazások gyűjteményéhez kapcsolódó rendszerképek gyűjteményeit az egyes fejlesztői és üzemeltetői csoportok számára. Példa:

  * A(z) `myregistry.azurecr.io/aspnetcore:1.0.1` egy, a teljes vállalatban elérhető rendszerképet jelöl
  * A(z) `myregistry.azurecr.io/warrantydept/dotnet-build` egy .NET-alkalmazások felépítéséhez használt rendszerképet jelöl, amely a jótállási részlegen van megosztva
  * A(z) `myregistry.azurecr.io/warrantydept/customersubmissions/web` egy, az ügyfélbeadványok alkalmazásban csoportosított webes rendszerképet jelöl, amely a jótállási részleg tulajdona

* **Kép** – az adattárakban tárolt egyes rendszerképek egy Docker-kompatibilis tároló csak olvasható pillanatkép. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan. Docker-tárolórendszerképekhez mellett az Azure Container Registry tárolja [kapcsolódó tartalom formátumok](container-registry-image-formats.md) például [Helm-diagramok](container-registry-helm-repos.md) és a készített rendszerképekből a [Open Container kezdeményezés (OCI) kép Specifikáció formázása](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Tároló** – A tároló egy szoftveralkalmazást határoz meg annak függőségeivel együtt egy teljes fájlrendszerbe csomagolva, beleértve a kódot, a futtatókörnyezetet, a rendszereszközöket és a könyvtárakat. A Docker-tárolókat a tároló-beállításjegyzékekből előhívott Windows- vagy Linux-rendszerképek alapján futtathatja. Az egy gépen futó tárolók osztoznak az operációs rendszer kernelén. A Docker-tárolók teljes mértékben használhatók az összes nagyobb Linux-disztribúción, MacOS-en és Windowson is.

## <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Az [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) az Azure Container Registry egy szolgáltatáscsomagja, amely lehetővé teszi a Docker-tárolórendszerképek zökkenőmentes és hatékony összeállítását az Azure-ban. Az ACR Tasks használatával kiterjesztheti a felhőbe a belső fejlesztési ciklust a `docker build`-műveletek Azure-ba való áthelyezése által. Konfigurálhat összeállítási feladatokat a tároló operációs rendszerének és a keretrendszer javítási folyamatának automatizálására, valamint a rendszerképek automatikus összeállítására, ha a csoport kódot véglegesít a forráskezelőben.

[Több lépésből álló feladatokat](container-registry-tasks-overview.md#multi-step-tasks) adja meg lépés alapú feladatdefiníció és végrehajtási létrehozásához, teszteléséhez és a javítással tárolórendszerképeket a felhőben. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

## <a name="next-steps"></a>További lépések

* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [Operációs rendszer és keretrendszer javításának automatizálása az ACR Tasksszal](container-registry-tasks-overview.md)
