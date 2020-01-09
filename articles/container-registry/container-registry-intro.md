---
title: Felügyelt tárolói nyilvántartások
description: Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít.
author: stevelas
ms.topic: overview
ms.date: 12/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 863b93497505443b79f41f580150a4dbf790a6f2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445733"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Az Azure-beli privát Docker-tárolójegyzékek bemutatása

A Azure Container Registry egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely a nyílt forráskódú Docker beállításjegyzék 2,0-es számán alapul. Azure-beli tároló-beállításjegyzékek létrehozása és karbantartása a privát Docker-tárolók rendszerképeinek és a kapcsolódó összetevők tárolásához és kezeléséhez.

Az Azure Container-jegyzékeket meglévő tároló-fejlesztési és üzembe helyezési folyamataival használhatja, vagy Azure Container Registry feladatok használatával készíthet tároló-lemezképeket az Azure-ban. Igény szerint építhető, vagy teljesen automatizálható az eseményindítók, például a forráskód-véglegesítés és az alaprendszerkép frissítései.

A Docker-és a beállításjegyzék-fogalmakkal kapcsolatos további információkért tekintse meg a [Docker áttekintését](https://docs.docker.com/engine/docker-overview/) , valamint a [jegyzékek, a adattárak és a lemezképek ismertetését](container-registry-concepts.md).

## <a name="use-cases"></a>Használati példák

Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) és [Docker Swarm](https://docs.docker.com/swarm/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve az [Azure Kubernetes Service (AKS)](../aks/index.yml), az [App Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például megcélozhat egy tároló-beállításjegyzéket egy folyamatos integrációs és kézbesítési eszközről, például [Azure-folyamatokból](/azure/devops/pipelines/ecosystems/containers/acr-template) vagy [Jenkins](https://jenkins.io/)-ből.

Az ACR-feladatok konfigurálásával automatikusan újraépítheti az alkalmazás lemezképeit az alaplemezképek frissítésekor, vagy automatizálhatja a lemezképeket, amikor a csapata egy git-tárházban véglegesíti a kódot. Többlépéses feladatok létrehozásával automatizálhatja a felhőben párhuzamosan több tároló lemezképének létrehozását, tesztelését és javítását.

Az Azure olyan eszközöket biztosít, mint az Azure parancssori felület, a Azure Portal és az API-támogatás az Azure Container-nyilvántartások kezeléséhez. Telepítse a [Docker-bővítményt a Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) -hoz, és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményét az Azure-beli tároló-beállításjegyzékkel való együttműködéshez. Lemezképek lekérése és leküldése egy Azure Container registrybe, vagy az ACR-feladatok futtatása a Visual Studio Code-ban.

## <a name="key-features"></a>Fő funkciók

* **Beállításjegyzékbeli SKU** – hozzon létre egy vagy több tároló-beállításjegyzéket az Azure-előfizetésében. A beállításjegyzékek három SKU-ban érhetők el: [alapszintű, standard és prémium](container-registry-skus.md), amelyek mindegyike támogatja a webhook-integrációt, a beállításjegyzék hitelesítését Azure Active Directory és a törlési funkciót. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. Haladó szintű replikációs és tárolórendszerkép-elosztási forgatókönyvekhez használja a Prémium szintű beállításjegyzékek [georeplikációs](container-registry-geo-replication.md) funkcióját. 

* **Biztonság és hozzáférés** – jelentkezzen be egy beállításjegyzékbe az Azure CLI vagy a standard `docker login` parancs használatával. Azure Container Registry a tároló lemezképeit HTTPS-kapcsolaton keresztül továbbítja, és támogatja a TLS-t az ügyfélkapcsolatok biztonságossá tételéhez. 

  > [!IMPORTANT]
  > 2020. január 13-án Azure Container Registry a TLS 1,2 használatához a kiszolgálók és alkalmazások összes biztonságos kapcsolata szükséges. A TLS 1,0 és 1,1 támogatása megszűnik.

  Egy tároló-beállításjegyzékhez való hozzáférést egy Azure-identitás, egy Azure Active Directory-alapú [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md)vagy egy megadott rendszergazdai fiók használatával [szabályozhatja](container-registry-authentication.md) . Szerepköralapú hozzáférés-vezérlés (RBAC) használatával felhasználók vagy rendszerek részletes engedélyeit rendelheti hozzá egy beállításjegyzékhez.

  A prémium SKU biztonsági funkciói közé tartozik a Képcímke-aláírás [megbízhatósága](container-registry-content-trust.md) , valamint a [tűzfalak és virtuális hálózatok (előzetes verzió)](container-registry-vnet.md) a beállításjegyzékhez való hozzáférés korlátozása érdekében. Azure Security Center opcionálisan integrálható a Azure Container Registry a [rendszerképek vizsgálatára](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) , amikor egy lemezképet egy beállításjegyzékbe küldenek.

* **Támogatott lemezképek és** összetevők – egy adattárba csoportosítva minden rendszerkép egy Docker-kompatibilis tároló írásvédett pillanatképe. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan. A Docker-tároló rendszerképein kívül a Azure Container Registry a [kapcsolódó tartalom formátumait](container-registry-image-formats.md) , például [Helm-diagramokat](container-registry-helm-repos.md) és rendszerképeket is tartalmaz, amelyek az [Open Container Initiative (OCI) képformátum-specifikációra](https://github.com/opencontainers/image-spec/blob/master/spec.md)épülnek.

* **Automatizált rendszerkép-buildek** – [Azure Container Registry feladatok](container-registry-tasks-overview.md) (ACR-feladatok) használatával egyszerűsítheti a lemezképek létrehozását, tesztelését, leküldését és üzembe helyezését az Azure-ban. Az ACR-feladatok segítségével például kiterjesztheti a fejlesztési belső hurkot a felhőbe `docker build` műveletek Azure-ba való kiszervezésével. Konfigurálhat összeállítási feladatokat a tároló operációs rendszerének és a keretrendszer javítási folyamatának automatizálására, valamint a rendszerképek automatikus összeállítására, ha a csoport kódot véglegesít a forráskezelőben.

  A [többlépéses tevékenységek](container-registry-tasks-overview.md#multi-step-tasks) a Felhőbeli tároló-lemezképek létrehozásához, teszteléséhez és javításához szükséges lépéseken alapuló feladatok meghatározását és végrehajtását teszik lehetővé. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

## <a name="next-steps"></a>Következő lépések

* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [A tárolók és a karbantartás ACR-feladatokkal való automatizálása](container-registry-tasks-overview.md)