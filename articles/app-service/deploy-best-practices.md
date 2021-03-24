---
title: Ajánlott üzembe helyezési eljárások
description: Ismerje meg a Azure App Service üzembe helyezésének főbb mechanizmusait. Nyelvspecifikus javaslatok és egyéb kikötések keresése.
keywords: Azure app Service, webalkalmazás, üzembe helyezés, üzembe helyezés, folyamatok, Build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: ac4e8d93e2e729db387b0f9cf81017b6c58b6750
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951334"
---
# <a name="deployment-best-practices"></a>Ajánlott eljárások az üzembe helyezéshez

Minden fejlesztői csapat egyedi követelményekkel rendelkezik, amelyekkel megnehezítik a hatékony üzembe helyezési folyamatok bármilyen felhőalapú szolgáltatásban való megvalósítását. Ez a cikk bemutatja az üzembe helyezésének három fő összetevőjét App Service: üzembe helyezési források, folyamatok létrehozása és üzembe helyezési mechanizmusok. Ez a cikk az egyes nyelvi stackekhez kapcsolódó ajánlott eljárásokat és tippeket is tárgyalja.

## <a name="deployment-components"></a>Üzembe helyezési összetevők

### <a name="deployment-source"></a>Központi telepítés forrása

A központi telepítési forrás az alkalmazás kódjának helye. Az éles alkalmazások esetében a központi telepítési forrás általában egy verziókövetés szoftverrel (például [GitHub, BitBucket vagy Azure Repos](deploy-continuous-deployment.md)) üzemeltetett tárház. Fejlesztési és tesztelési helyzetekben a központi telepítési forrás lehet [egy projekt a helyi gépen](deploy-local-git.md). A App Service a [OneDrive és a Dropbox mappákat](deploy-content-sync.md) is támogatja központi telepítési forrásként. A Felhőbeli mappák megkönnyítik a App Service használatának megkezdését, ezért általában nem ajánlott a forrást nagyvállalati szintű üzemi alkalmazásokhoz használni. 

### <a name="build-pipeline"></a>Buildelési folyamat

Ha úgy dönt, hogy egy központi telepítési forrást választ, a következő lépés a létrehozási folyamat kiválasztása. A létrehozási folyamat beolvassa a forráskódot a központi telepítési forrásból, és végrehajt egy sor lépést (például a kód fordítását, a HTML és a JavaScriptek futtatását, a tesztek futtatását és a csomagolási összetevőket), hogy az alkalmazás futtatható állapotba kerüljön. A létrehozási folyamat által végrehajtott parancsok a nyelvi veremtől függenek. Ezek a műveletek egy olyan Build-kiszolgálón hajthatók végre, mint például az Azure-folyamatok, vagy helyileg is végrehajtva.

### <a name="deployment-mechanism"></a>Üzembe helyezési mechanizmus

Az üzembe helyezési mechanizmus az a művelet, amellyel a létrehozott alkalmazást a webalkalmazás */Home/site/wwwroot* könyvtárába helyezheti. A */wwwroot* könyvtár a webalkalmazás összes példánya által megosztott csatlakoztatott tárolási hely. Ha az üzembe helyezési mechanizmus ezen a címtáron helyezi el az alkalmazást, a példányok értesítést kapnak az új fájlok szinkronizálásáról. App Service a következő üzembe helyezési mechanizmusokat támogatja:

- Kudu-végpontok: a [kudu](https://github.com/projectkudu/kudu/wiki) a nyílt forráskódú fejlesztői hatékonyságnövelő eszköz, amely külön folyamatként fut a Windows app Serviceban, és a Linux app Service második tárolója. A kudu kezeli a folyamatos üzembe helyezéseket, és HTTP-végpontokat biztosít a központi telepítéshez, például zipdeploy.
- FTP és webtelepítés: a [webhely vagy a felhasználói hitelesítő adatok](deploy-configure-credentials.md)használatával [FTP-n keresztül](deploy-ftp.md) vagy webtelepítéssel tölthet fel fájlokat. Ezek a mechanizmusok nem haladnak át a kudu.  

Az olyan üzembe helyezési eszközök, mint például az Azure-folyamatok, a Jenkins és a szerkesztő beépülő modulok, a következő telepítési mechanizmusok valamelyikét használják.

## <a name="use-deployment-slots"></a>Üzembe helyezési pontok használata

Amikor csak lehetséges, használja az [üzembe helyezési](deploy-staging-slots.md) pontokat új üzemi buildek telepítésekor. Standard szintű App Service csomag vagy jobb használata esetén üzembe helyezheti az alkalmazást egy átmeneti környezetben, ellenőrizheti a módosításokat, és elvégezheti a füst tesztelését. Ha elkészült, az átmeneti és az üzemi tárolóhelyek is felcserélhetők. A swap művelet bemelegíti a szükséges munkavégző példányokat, hogy az megfeleljen az üzemi méretnek, így kiküszöbölve az állásidőt.

### <a name="continuously-deploy-code"></a>Kód folyamatos üzembe helyezése

Ha a projekt a teszteléshez, a MINŐSÉGBIZTOSÍTÁShoz és az előkészítéshez kijelölt ágakat tartalmaz, akkor ezeket az ágakat folyamatosan üzembe kell helyezni egy átmeneti tárolóhelyen. (Ez az úgynevezett Gitflow- [kialakítás](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Ez lehetővé teszi, hogy az érintettek könnyedén értékeljék és tesztelik az ág üzembe helyezését. 

A folyamatos üzembe helyezést soha nem szabad engedélyezni az éles tárolóhelyen. Ehelyett a termelési ág (gyakran a fő) üzembe helyezését nem éles tárolóhelyre kell telepíteni. Ha készen áll az alapág kiadására, cserélje le az üzemi tárolóhelyre. Éles környezetbe történő váltás – az éles környezetbe való telepítés helyett – megakadályozza az állásidőt, és lehetővé teszi a módosítások visszaállítását újbóli cserével. 

![Diagram, amely a fejlesztési, átmeneti és fő ágak, valamint a rendszerbe telepített tárolóhelyek közötti folyamatot mutatja.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Tárolók folyamatos üzembe helyezése

A Docker vagy más tároló-beállításjegyzékből származó egyéni tárolók esetében telepítse a lemezképet egy átmeneti tárolóhelyre, és cserélje ki az állásidőt az éles környezetbe. Az automatizálás összetettebb, mint a kód központi telepítése, mert a rendszerképet egy tároló-beállításjegyzékbe kell küldenie, és frissítenie kell a programkódot a webappban.

Minden olyan ág esetében, amelyet üzembe szeretne helyezni egy tárolóhelyre, állítsa be az automatizálást úgy, hogy a következő műveleteket végezze el az egyes kötelezettségeken a fiókirodában.

1. Hozza **létre és címkézze fel a rendszerképet**. A build folyamat részeként címkézze fel a képet a git commit ID, timestamp vagy más azonosítható információval. A legjobb, ha nem használja az alapértelmezett "legújabb" címkét. Ellenkező esetben nehéz nyomon követni a jelenleg üzembe helyezett kódot, ami sokkal nehezebbé teszi a hibakeresést.
1. **A címkézett Rendszerkép leküldése**. Miután létrehozta és címkézte a rendszerképet, a folyamat leküldi a rendszerképet a tároló-beállításjegyzékbe. A következő lépésben az üzembe helyezési pont lekéri a címkézett képet a tároló-beállításjegyzékből.
1. **Frissítse az üzembe helyezési pontot az új rendszerkép címkével**. A tulajdonság frissítésekor a hely automatikusan újraindul, és lekéri az új tároló képét.

![A tárolóhelyek használatának vizualizációja](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Az általános automatizálási keretrendszerek alább találhatók példák.

### <a name="use-azure-devops"></a>Az Azure DevOps használata

A App Service a tárolók számára [beépített folyamatos kézbesítést](deploy-continuous-deployment.md) biztosít a központi telepítési központban. Navigáljon az alkalmazáshoz a [Azure Portalban](https://portal.azure.com/) , és válassza a **központi telepítés** **pontban az üzembehelyezési központ** elemet. A tárház és az ág kiválasztásához kövesse az utasításokat. Ez egy DevOps-létrehozási és-kiadási folyamatot konfigurál a tároló automatikus létrehozásához, címkézéséhez és üzembe helyezéséhez, amikor új véglegesíti a kijelölt ágat.

### <a name="use-github-actions"></a>GitHub-műveletek használata

A tároló üzembe helyezését [a GitHub-műveletekkel](./deploy-ci-cd-custom-container.md)is automatizálhatja.  Az alábbi munkafolyamat-fájl létrehozza és címkézi a tárolót a commit AZONOSÍTÓval, leküldi a tároló-beállításjegyzékbe, és frissíti a megadott tárolóhelyet az új rendszerkép címkével.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Más Automation-szolgáltatók használata

A korábban felsorolt lépések más Automation-segédprogramokra, például a CircleCI vagy a Travis CI-re vonatkoznak. Az utolsó lépésben azonban az Azure CLI-vel kell frissítenie az üzembe helyezési pontokat az új képcímkék használatával. Ha az Azure CLI-t az Automation-parancsfájlban szeretné használni, akkor a következő paranccsal hozhatja ki az egyszerű szolgáltatásnevet.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

A szkriptben jelentkezzen be a használatával `az login --service-principal` , és adja meg a rendszerbiztonsági tag információit. Ezután `az webapp config container set` a használatával beállíthatja a tároló nevét, a címkét, a beállításjegyzék URL-címét és a beállításjegyzék jelszavát. Az alábbiakban néhány hasznos hivatkozást talál a Container CI-folyamat létrehozásához.

- [Bejelentkezés az Azure CLI-be a Circle CI-ben](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Language-Specific megfontolások

### <a name="java"></a>Java

Használja a kudu [zipdeploy/](deploy-zip.md) API-t a jar-alkalmazások üzembe helyezéséhez, valamint a wardeployjal és a War [-](deploy-zip.md#deploy-war-file) alkalmazásokhoz. Ha Jenkins-t használ, ezeket az API-kat közvetlenül az üzembe helyezési fázisban is használhatja. További információkért tekintse meg [ezt a cikket](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli).

### <a name="node"></a>Csomópont

Alapértelmezés szerint a kudu végrehajtja a Node-alkalmazás () létrehozási lépéseit `npm install` . Ha olyan Build szolgáltatást használ, mint például az Azure DevOps, akkor a kudu-Build szükségtelen. A kudu-Build letiltásához hozzon létre egy alkalmazás-beállítást, amelynek értéke a következő: `SCM_DO_BUILD_DURING_DEPLOYMENT` `false` .

### <a name="net"></a>.NET 

Alapértelmezés szerint a kudu végrehajtja a .NET-alkalmazás () létrehozási lépéseit `dotnet build` . Ha olyan Build szolgáltatást használ, mint például az Azure DevOps, akkor a kudu-Build szükségtelen. A kudu-Build letiltásához hozzon létre egy alkalmazás-beállítást, amelynek értéke a következő: `SCM_DO_BUILD_DURING_DEPLOYMENT` `false` .

## <a name="other-deployment-considerations"></a>Egyéb üzembe helyezési megfontolások

### <a name="local-cache"></a>Helyi gyorsítótár

Azure App Service tartalom tárolása az Azure Storage-ban történik, és tartós módon van felkészülve, mint a tartalom megosztása. Egyes alkalmazások esetében azonban csak nagy teljesítményű, írásvédett, magas rendelkezésre állású tartalom-tárolóra lehet szükség. Ezek az alkalmazások kihasználhatják a [helyi gyorsítótár](overview-local-cache.md)használatát. A helyi gyorsítótár nem ajánlott olyan tartalomkezelési webhelyekhez, mint a WordPress.

A leállás megakadályozása érdekében mindig a helyi gyorsítótárat használja az [üzembe helyezési tárolóhelyekkel](deploy-staging-slots.md) együtt. A szolgáltatások együttes használatával kapcsolatos információkért tekintse meg [ezt a szakaszt](overview-local-cache.md#best-practices-for-using-app-service-local-cache) .

### <a name="high-cpu-or-memory"></a>Magas CPU vagy memória

Ha a App Service csomag a rendelkezésre álló CPU vagy memória több mint 90%-át használja, előfordulhat, hogy az alapul szolgáló virtuális gép nem tudja feldolgozni az üzemelő példányt. Ebben az esetben a példányok számának ideiglenes skálázásával végezze el az üzembe helyezést. Miután az üzembe helyezés befejeződött, visszaállíthatja a példányszámot az előző értékre.

Az ajánlott eljárásokkal kapcsolatos további információkért látogasson el a [app Service Diagnostics](./overview-diagnostics.md) webhelyre, ahol az erőforrásra vonatkozó, gyakorlatban alkalmazható ajánlott eljárásokat talál.

- Navigáljon a webalkalmazáshoz a [Azure Portal](https://portal.azure.com).
- Kattintson a bal oldali navigációs sávon található **problémák diagnosztizálásához és megoldásához** , amely megnyitja app Service diagnosztikát.
- Válassza az **ajánlott eljárások** Kezdőlap csempét.
- Kattintson az **ajánlott eljárások a rendelkezésre állás & teljesítmény** vagy **ajánlott eljárások az optimális konfigurációhoz** lehetőségre az alkalmazás aktuális állapotának megtekintéséhez az ajánlott eljárásokkal kapcsolatban.

Ezzel a hivatkozással közvetlenül is megnyithatja App Service diagnosztikát az erőforráshoz: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .