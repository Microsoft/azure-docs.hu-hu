---
title: Nyilvános tartalom kezelése a privát tároló beállításjegyzékében
description: Eljárások és munkafolyamatok a Azure Container Registry a Docker hub és más nyilvános tartalmak nyilvános rendszerképein fennálló függőségek kezeléséhez
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024738"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Nyilvános tartalom kezelése Azure Container Registry

Ez a cikk áttekintést nyújt azokról a gyakorlatokról és munkafolyamatokról, amelyek helyi beállításjegyzéket, például egy [Azure Container registryt](container-registry-intro.md) használnak a nyilvános tartalmak, például a tárolók rendszerképeinek a Docker hub-ban való karbantartásához. 


## <a name="risks-with-public-content"></a>Kockázatok nyilvános tartalommal

A környezet a nyilvános tartalmak, például a nyilvános tárolók képei, a [Helm-diagramok](https://helm.sh/), a [nyílt házirendügynök](https://www.openpolicyagent.org/) -szabályzatok vagy más összetevők függőségeivel rendelkezhet. Futtathatja például az [Nginx](https://hub.docker.com/_/nginx) szolgáltatást a szolgáltatás-útválasztáshoz, vagy `docker build FROM alpine` közvetlenül a Docker hub-ból vagy más nyilvános beállításjegyzékből lehet képeket kihúzza. 

A megfelelő vezérlők nélkül a nyilvános beállításjegyzékbeli tartalommal kapcsolatos függőségek veszélyeztethetik a rendszerkép-fejlesztési és üzembe helyezési munkafolyamatokat. A kockázatok enyhítése érdekében a nyilvános tartalmak helyi másolatait a lehető leghamarabb meg kell őrizni. Részletekért tekintse [meg az Open Container Initiative blogját](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Hitelesítés a Docker hub használatával

Első lépésként, ha jelenleg a Docker hub nyilvános lemezképeit egy Build vagy üzembe helyezési munkafolyamat részeként kéri le, javasoljuk, hogy a névtelen lekéréses kérelem helyett [Docker hub-fiókkal végezzen hitelesítést](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) .

Gyakori névtelen lekéréses kérelmek esetében előfordulhat, hogy a hibák elkerülése érdekében a Docker-hubhoz hasonló, vagy az ahhoz való hitelesítéshez szükséges Docker-hibák jelennek meg `ERROR: toomanyrequests: Too Many Requests.` `You have reached your pull rate limit.` .

> [!NOTE]
> 2020. november 2. [között a letöltési sebességre vonatkozó korlátozások](https://docs.docker.com/docker-hub/download-rate-limit) a Docker ingyenes csomag fiókjaiban a Docker hub névtelen és hitelesített kéréseire vonatkoznak, és az IP-cím és a Docker-azonosító alapján érvényesítik azokat. 
>
> A lekéréses kérelmek számának becslése során vegye figyelembe, hogy ha a felhőalapú szolgáltatói szolgáltatásokat használja, vagy a vállalati NAT mögött dolgozik, több felhasználó fog megjelenni a Docker hub-ban az IP-címek részhalmazában. Ha hozzáadta a Docker fizetős fiókjának hitelesítését a Docker hub felé irányuló kérelmekhez, azzal elkerülhető, hogy a szolgáltatás megszakadjon a sávszélesség-korlátozás miatt.
>
> Részletekért lásd a [Docker díjszabását és előfizetéseit](https://www.docker.com/pricing) , valamint a [Docker szolgáltatási feltételeit](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Docker hub hozzáférési token

A Docker hub támogatja a [személyes hozzáférési jogkivonatokat](https://docs.docker.com/docker-hub/access-tokens/) a Docker-jelszó alternatívájaként a Docker hub-ban való hitelesítéskor. A tokenek olyan automatizált szolgáltatásokhoz ajánlottak, amelyek lekérik a képeket a Docker hub-ból. Több tokent is létrehozhat a különböző felhasználókhoz vagy szolgáltatásokhoz, és visszavonhatja a jogkivonatokat, ha már nincs rá szükség.

Ha jogkivonat használatával szeretne hitelesítést végezni `docker login` , hagyja ki a jelszót a parancssorban. Ha a rendszer kéri a jelszót, adja meg helyette a tokent. Ha engedélyezte a kétfaktoros hitelesítést a Docker hub-fiókhoz, akkor a Docker parancssori felületéről való bejelentkezéskor személyes hozzáférési jogkivonatot kell használnia.

### <a name="authenticate-from-azure-services"></a>Hitelesítés az Azure-szolgáltatásokból

Számos Azure-szolgáltatás, többek között a App Service és a Azure Container Instances támogatja a nyilvános beállításjegyzékből, például a Docker hub-ból a tárolók üzembe helyezéséhez szükséges képeket. Ha lemezképet kell központilag telepíteni a Docker hub-ból, javasoljuk, hogy konfigurálja a beállításokat a Docker hub-fiók használatával történő hitelesítéshez. Angol nyelvű Példák:

**APP SERVICE**

* **Rendszerkép forrása**: Docker hub
* **Tárház-hozzáférés**: privát
* **Bejelentkezés**: \<Docker Hub username>
* **Jelszó**: \<Docker Hub token>

Részletekért lásd: [Docker hub hitelesített lekérések app Serviceon](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Azure Container Instances**

* **Rendszerkép forrása**: Docker hub vagy más beállításjegyzék
* **Rendszerkép típusa**: Private
* **Rendszerkép-beállításjegyzék bejelentkezési kiszolgálója**: Docker.IO
* **Rendszerkép-beállításjegyzékbeli Felhasználónév**: \<Docker Hub username>
* **Rendszerkép beállításjegyzékbeli jelszava**: \<Docker Hub token>
* **Rendszerkép**: Docker.IO/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Lemezképek importálása Azure Container registrybe
 
A nyilvános lemezképek másolásának megkezdéséhez létrehozhat egy Azure Container registryt, ha még nem rendelkezik ilyennel. Hozzon létre egy beállításjegyzéket az [Azure CLI](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)vagy más eszközök használatával. 

Ajánlott egyszeri lépésként az alapképek és az egyéb nyilvános tartalmak [importálása](container-registry-import-images.md) az Azure Container registrybe. Az az [ACR import](/cli/azure/acr#az_acr_import) parancs az Azure CLI-ben támogatja az olyan nyilvános beállításjegyzékből származó képimportálást, mint például a Docker hub és a Microsoft Container Registry, valamint más privát tároló-beállításjegyzékből. 

`az acr import` nincs szükség helyi Docker-telepítésre. Az Azure CLI helyi telepítésével vagy közvetlenül a Azure Cloud Shell futtathatja. Minden operációsrendszer-típusból, többarchitektúrás lemezképből vagy OCI, például Helm-diagramokból származó képeket támogat.

Példa:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

A szervezet igényeitől függően egy dedikált beállításjegyzékbe vagy egy megosztott beállításjegyzékbeli adattárba is importálhat.

## <a name="automate-application-image-updates"></a>Alkalmazás-rendszerkép frissítéseinek automatizálása

Az alkalmazási lemezképek fejlesztőinek biztosítaniuk kell, hogy a kód a vezérlőn belüli helyi tartalomra hivatkozzanak. Egy `Docker FROM` Docker lévő utasításnak például egy privát alaprendszerkép-beállításjegyzékbeli rendszerképre kell hivatkoznia nyilvános beállításjegyzék helyett. 

A lemezképek importálására való kiterjesztéssel egy [Azure Container Registry feladat](container-registry-tasks-overview.md) állítható be, amely automatizálja az alkalmazás lemezképeit az alaplemezképek frissítésekor. Egy automatizált felépítési feladat nyomon követheti az [alapszintű](container-registry-tasks-base-images.md) és a [forráskód-frissítéseket](container-registry-tasks-overview.md#trigger-task-on-source-code-update)is.

Részletes példaként tekintse meg a [nyilvános tartalmak felhasználása és karbantartása Azure Container Registry feladatokkal](tasks-consume-public-content.md)című témakört. 

> [!NOTE]
> Egyetlen előre konfigurált feladat automatikusan újraépítheti az összes olyan alkalmazás-rendszerképet, amely egy függő alaprendszerképre hivatkozik. 
 
## <a name="next-steps"></a>Következő lépések
 
* További információ az [ACR-feladatokról](container-registry-tasks-overview.md) az Azure-beli tároló-lemezképek létrehozásához, futtatásához, leküldéséhez és javításához.
* Tekintse meg a [nyilvános tartalmak felhasználását és karbantartását Azure Container Registry feladatokkal](tasks-consume-public-content.md) egy automatizált kapuzás-munkafolyamathoz az alaplemezképek környezetbe való frissítéséhez. 
* A rendszerkép-buildek és-frissítések automatizálásával kapcsolatos további példákért tekintse meg az [ACR-feladatok oktatóanyagokat](container-registry-tutorial-quick-task.md) .
