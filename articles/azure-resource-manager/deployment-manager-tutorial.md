---
title: Az Azure Deployment Manager Resource Manager-sablonokkal való használata | Microsoft Docs
description: Az Azure Deployment Managerben használhat Resource Manager-sablonokat az Azure-erőforrások üzembe helyezéséhez.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: db130da9943007e647adf77411b456914af9886f
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307025"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Oktatóanyag: Az Azure Deployment Manager Resource Manager-sablonokkal való használata (nyilvános előzetes verzió)

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe alkalmazásait több régióban az [Azure Deployment Manager](./deployment-manager-overview.md) használatával. Ha gyorsabb megközelítésre van szüksége, az [Azure Telepítéskezelő](https://github.com/Azure-Samples/adm-quickstart) rövid útmutatója létrehozza a szükséges konfigurációkat az előfizetésében, és testreszabja az összetevőket több régióban történő alkalmazás üzembe helyezéséhez. A rövid útmutató ugyanazokat a feladatokat hajtja végre, mint az oktatóanyagban.

Telepítéskezelő használatához két sablont kell létrehoznia:

* **Topológiasablon**: Az alkalmazást alkotó Azure-erőforrásokat és a telepítési helyüket írja le.
* **Bevezetési sablon**: Az alkalmazások telepítése során végrehajtandó lépéseket írja le.

> [!IMPORTANT]
> Ha az előfizetése Kanári-re van megjelölve az új Azure-funkciók kipróbálásához, akkor csak az Azure telepítéskezelőt használhatja a Kanári-régiókban való üzembe helyezéshez. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A forgatókönyv megismerése
> * Az oktatóanyag fájljainak letöltése
> * Az összetevők előkészítése
> * A felhasználói felügyelt identitás létrehozása
> * A szolgáltatástopológia-sablon létrehozása
> * A bevezetési sablon létrehozása
> * A sablonok üzembe helyezése
> * A telepítés ellenőrzése
> * Az újabb verzió üzembe helyezése
> * Az erőforrások eltávolítása

További források:

* Az [Azure telepítéskezelő REST API referenciája](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Oktatóanyag: az állapot-ellenőrzési szolgáltatás használata az Azure telepítéskezelőban](./deployment-manager-tutorial-health-check.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Némi gyakorlat az [Azure Resource Manager-sablonok](./resource-group-overview.md) kialakításában.
* Azure PowerShell. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Deployment Manager-parancsmagok. Az előzetes verziójú parancsmagok telepítéséhez a PowerShellGet legújabb verziójára lesz szüksége. A legújabb verzió beszerzéséről lásd [a PowerShellGet telepítését](/powershell/scripting/gallery/installing-psget) ismertető cikket. A PowerShellGet telepítése után zárja be a PowerShell-ablakot. Nyisson meg egy új, emelt szintű PowerShell-ablakot, és használja a következő parancsot:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>A forgatókönyv megismerése

A szolgáltatástopológia-sablon a szolgáltatást alkotó Azure-erőforrásokat és a telepítési helyüket írja le. A szolgáltatástopológia definíciója az alábbi hierarchiát követi:

* Szolgáltatástopológia
  * Szolgáltatások
    * Szolgáltatási egységek

Az alábbi ábra az oktatóanyagban használt szolgáltatástopológiát mutatja be:

![Azure Deployment Manager-oktatóanyag – a forgatókönyv ábrája](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Két szolgáltatás lett lefoglalva az Egyesült Államok nyugati és keleti régiójában.  Mindkét szolgáltatás két szolgáltatási egységgel rendelkezik – egy webalkalmazással az előtér- és egy tárfiókkal a háttérrendszer számára. A szolgáltatási egységek definíciói a webalkalmazások és a tárfiókok létrehozásához szükséges sablon- és paraméterfájlokra mutató hivatkozásokat tartalmaznak.

## <a name="download-the-tutorial-files"></a>Az oktatóanyag fájljainak letöltése

1. Töltse le az oktatóanyagban használt [sablonokat és összetevőket](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip).
2. Tömörítse ki a fájlokat a helyi számítógépen.

A gyökérmappában két mappa található:

* **ADMTemplates**: A Deployment Manager-sablonokat tartalmazza, nevezetesen a következőket:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: A sablonösszetevőket és a bináris összetevőket tartalmazza. Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).

Megjegyzendő, hogy két készletnyi sablon található itt.  Az egyik készlet a szolgáltatástopológia és a bevezetés üzembe helyezéséhez használt Deployment Manager-sablonokat, a másik a szolgáltatási egységek által a webes szolgáltatások és a tárfiókok létrehozásához meghívott sablonokat tartalmazza.

## <a name="prepare-the-artifacts"></a>Az összetevők előkészítése

A letöltés ArtifactStore mappája két mappát tartalmaz:

![Azure Deployment Manager-oktatóanyag – az összetevőforrás ábrája](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* A **templates** mappa: A sablonösszetevőket tartalmazza. Az **1.0.0.0** és az **1.0.0.1** jelöli a bináris összetevők két verzióját. Mindkét verzióban külön mappája van mindegyik szolgáltatásnak (az Egyesült Államok keleti és a nyugati régiójában futó szolgáltatásnak). Mindegyik szolgáltatás rendelkezik egy sablon- és egy paraméterfájllal a tárfiók létrehozásához, és egy másik ilyen fájlpárossal a webalkalmazás készítéséhez. A webalkalmazás-sablon egy tömörített csomagot hív meg, amely a webalkalmazás fájljait tartalmazza. A tömörített fájl egy bináris összetevő, amely a binaries mappában található.
* A **binaries** mappa: A bináris összetevőket tartalmazza. Az **1.0.0.0** és az **1.0.0.1** jelöli a bináris összetevők két verzióját. Mindegyik verzióban az egyik zip-fájl az Egyesült Államok nyugati régiójában, a másik a keleti régiójában lévő webalkalmazás létrehozására szolgál.

A két verzió (1.0.0.0 és 1.0.0.1) a [változatok üzembe helyezését](#deploy-the-revision) szolgálja. Bár a sablonösszetevőknek és a bináris összetevőknek is két-két verziója van, csak a bináris összetevőknek térnek el a verziói. A gyakorlatban a bináris összetevők gyakrabban frissülnek, mint a sablonösszetevők.

1. Nyissa meg a következő fájlt egy szövegszerkesztőben: **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json**. Ez egy egyszerű sablon egy tárfiók létrehozásához.
2. Nyissa meg a következő fájlt: **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Azure Deployment Manager oktatóanyag – webalkalmazás-létrehozási sablon](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    A sablon egy telepítőcsomagot hív meg, amely a webalkalmazás fájljait tartalmazza. Ebben az oktatóanyagban a tömörített csomag csak az index. html fájlt tartalmazza.
3. Nyissa meg a következő fájlt: **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![Azure Deployment Manager-oktatóanyag – webalkalmazás-létrehozási sablon paraméterei, containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    A deployPackageUri értéke a telepítőcsomag elérési útja. A paraméter tartalmaz egy **$containerRoot** változót. A $containerRoot értékét a [bevezetési sablon](#create-the-rollout-template) adja meg az összetevőforrás SAS-helye, az összetevő gyökere és a deployPackageUri azonosító összefűzésével.
4. Nyissa meg a következő fájlt: **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    A html-fájl tartalmazza a helyet és a verzióinformációkat. Az 1.0.0.1 mappában lévő bináris fájl a „Version 1.0.0.1” (1.0.0.1-es verzió) értéket mutatja. A szolgáltatás telepítése után ezekhez az oldalakhoz tallózhat.
5. Tekintse át az összetevők fájljait. Segítenek jobban megérteni a forgatókönyvet.

A sablonösszetevőket a szolgáltatástopológia-sablon, a bináris összetevőket a bevezetési sablon használja. A topológiasablon és a bevezetési sablon is meghatároz egy összetevőforrásként szolgáló Azure-erőforrást, amely az üzemelő példányban használt sablon- és bináris összetevőkre irányítja a Resource Managert. Az oktatóanyag egyszerűsége érdekében ugyanaz a tárfiók tárolja a sablonösszetevőket és a bináris összetevőket. Mindkét összetevőforrás ugyanarra a tárfiókra mutat.

Futtassa a következő PowerShell-parancsfájlt egy erőforráscsoport létrehozásához, egy tároló létrehozásához, egy blob-tároló létrehozásához, a letöltött fájlok feltöltéséhez, majd hozzon létre egy SAS-tokent.

> [!IMPORTANT]
> a PowerShell-parancsfájlban található **projektnév** az ebben az oktatóanyagban üzembe helyezett Azure-szolgáltatások nevének előállítására szolgálnak. A különböző Azure-szolgáltatások eltérő követelményekkel rendelkeznek a neveknél. Az üzembe helyezés sikerességének biztosításához válasszon egy 12 karakternél rövidebb nevet, amely csak kisbetűket és számokat tartalmazhat.
> Mentse a projekt neve másolatát. Ugyanazt a projektnév használja az oktatóanyagon keresztül.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)  -Protocol HttpsOrHttp

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Készítsen másolatot az URL-címről az SAS-jogkivonattal. Ez az URL-cím szükséges ahhoz, hogy feltöltse a mezőket a két paraméter fájljában, a topológia paramétereinek fájlját és a bevezetési paramétereket tartalmazó fájlt.

Nyissa meg a tárolót a Azure Portalból, és ellenőrizze, hogy a **bináris** fájlok és a **sablonok** mappái és a fájlok feltöltése is megtörténik-e.

## <a name="create-the-user-assigned-managed-identity"></a>A felhasználó által hozzárendelt felügyelt identitás létrehozása

Az oktatóanyag későbbi részében üzembe helyezünk egy bevezetést. Az üzembehelyezési műveletek végrehajtásához (például a webalkalmazások és a tárfiók üzembe helyezéséhez) szükség van egy felhasználó által hozzárendelt felügyelt identitásra. Ennek az identitásnak hozzáférést kell adni az Azure-előfizetéshez, amelyben a szolgáltatás üzembe lesz helyezve, és megfelelő jogosultságokkal kell rendelkeznie az összetevők üzembe helyezésének végrehajtásához.

Létre kell hoznia egy felhasználó által hozzárendelt felügyelt identitást, és konfigurálnia az előfizetése hozzáférés-vezérlését.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
2. Hozzon létre egy [felhasználó által hozzárendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. A portál bal oldali menüjében válassza az **Előfizetések** lehetőséget, majd válassza ki az előfizetést.
4. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása**elemet.
5. Adja meg vagy válassza ki a következő értékeket:

    ![Azure Deployment Manager-oktatóanyag – felhasználó által hozzárendelt felügyelt identitás hozzáférés-vezérlése](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Szerepkör**: Rendeljen hozzá elegendő jogosultságot az összetevők (a webalkalmazások és a tárfiókok) üzembe helyezéséhez. Ebben az oktatóanyagban válassza a **Közreműködő** szerepkört. A gyakorlatban azonban a jogosultságokat érdemes a minimálisra korlátozni.
    * **Hozzáférés hozzárendelése a következőhöz**: Válassza a **Felhasználó által hozzárendelt felügyelt identitás** lehetőséget.
    * Válassza ki a felhasználó által hozzárendelt felügyelt identitást, amelyet az oktatóanyag korábbi részében hozott létre.
6. Kattintson a **Mentés** gombra.

## <a name="create-the-service-topology-template"></a>A szolgáltatástopológia-sablon létrehozása

Nyissa meg a következő fájlt: **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>A paraméterek

A sablon a következő paramétereket tartalmazza:

* **projektnév**: ezzel a névvel lehet létrehozni a telepítéskezelő erőforrások nevét. Például a "jdoe" használatával a szolgáltatás topológiájának neve **jdoe**ServiceTopology.  Az erőforrásnevek ennek a sablonnak a változókat tartalmazó szakaszában definiálhatók.
* **azureResourcelocation**: Az oktatóanyag egyszerűsítése érdekében mindegyik erőforrás ezen a helyen található, hacsak másként nem jelezzük.
* **artifactSourceSASLocation**: Annak a blobtárolónak a SAS URI-ja, amely a szolgáltatási egység sablonját és paramétereit tárolja az üzembe helyezéshez.  Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
* **templateArtifactRoot**: A relatív elérési út a sablonokat és paramétereket tároló blobtárolótól. Az alapértelmezett érték **templates/1.0.0.0**. Ne módosítsa ezt az értéket, hacsak nem szeretné módosítani a mappastruktúrát [az összetevők előkészítését](#prepare-the-artifacts) ismertető szakaszban foglaltak szerint. Ebben az oktatóanyagban relatív elérési utakat használunk.  A teljes elérési út a következők összefűzésével áll elő: **artifactSourceSASLocation**, **templateArtifactRoot** és **templateArtifactSourceRelativePath** (vagy **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID**: Annak az előfizetésnek az azonosítója, amelyben a Deployment Manager-erőforrások telepítve és számlázva lesznek. Ebben az oktatóanyagban használja a saját előfizetése azonosítóját.

### <a name="the-variables"></a>A változók

A változók szakasz definiálja az erőforrások nevét, a két szolgáltatás (**Service WUS** és **Service EUS**) Azure-helyeit, valamint az összetevők elérési útját:

![Azure Deployment Manager-oktatóanyag – topológiasablon változói](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Vesse össze az összetevők elérési útját a tárfiókba feltöltött mappastruktúrával. Figyelje meg, hogy az összetevők elérési útjai relatívak. A teljes elérési út a következők összefűzésével áll elő: **artifactSourceSASLocation**, **templateArtifactRoot** és **templateArtifactSourceRelativePath** (vagy **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Az erőforrások

A gyökérszinten két erőforrás van definiálva: *egy összetevőforrás* és *egy szolgáltatástopológia*.

Az összetevőforrás definíciója:

![Azure Deployment Manager-oktatóanyag – topológiasablon erőforrásainak összetevőforrása](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Az alábbi képernyőképen a szolgáltatástopológiának, a szolgáltatásoknak és a szolgáltatási egységek definícióinak csak egyes részei láthatók:

![Azure Deployment Manager-oktatóanyag – topológiasablon erőforrásainak szolgáltatástopológiája](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* Az **artifactSourceId** az összetevőforrás erőforrást társítja a szolgáltatástopológia erőforráshoz.
* **dependsOn**: Minden szolgáltatástopológia-erőforrás függ az összetevőforrás-erőforrástól.
* Az **artifacts** elem a sablonösszetevőkre mutat.  Itt relatív elérési utakat használunk. A teljes elérési út a következők összefűzésével áll elő: artifactSourceSASLocation (az összetevőforrásban definiálva), artifactRoot (az összetevőforrásban definiálva) és templateArtifactSourceRelativePath (vagy parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Topológia-paraméterek fájlja

Hozzon létre egy paraméterfájlt, amely a topológiasablonnal használható.

1. Nyissa meg a következő fájlt a Visual Studio Code-ban vagy valamilyen szövegszerkesztőben: **\ADMTemplates\CreateADMServiceTopology.Parameters**.
2. Töltse ki a paraméterek értékeit:

    * **projektnév**: adjon meg egy 4-5 karakterből álló karakterláncot. Ez a név egyedi Azure-erőforrásnevek létrehozására szolgál.
    * **azureResourceLocation**: Ha nem ismerné az Azure-helyeket, ebben az oktatóanyagban használja a **centralus** helyet.
    * **artifactSourceSASLocation**: Adja meg annak a gyökérkönyvtárnak (a blobtárolónak) az SAS URI-ját, amely a szolgáltatási egység sablonját és paramétereit tárolja az üzembe helyezéshez.  Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
    * **templateArtifactRoot**: Hacsak nem módosítja az összetevők mappastruktúráját, használja a **templates/1.0.0.0** értéket az oktatóanyagban.

> [!IMPORTANT]
> A topológiasablon és a bevezetési sablon egyes paraméterei közösek. Ezeknek a paramétereknek egyező értékekkel kell rendelkezniük. Ezek a paraméterek a következők: **projektnév**, **azureResourceLocation**és **artifactSourceSASLocation** (a jelen oktatóanyagban ugyanazt a Storage-fiókot használják).

## <a name="create-the-rollout-template"></a>A bevezetési sablon létrehozása

Nyissa meg a következő fájlt: **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>A paraméterek

A sablon a következő paramétereket tartalmazza:

![Azure Deployment Manager-oktatóanyag – bevezetési sablon paraméterei](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **projektnév**: ezzel a névvel lehet létrehozni a telepítéskezelő erőforrások nevét. Például a "jdoe" használatával a bevezetési név **jdoe**bevezetést.  A nevek ennek a sablon a változókat tartalmazó szakaszában vannak definiálva.
* **azureResourcelocation**: Az oktatóanyag egyszerűsítése érdekében mindegyik Deployment Manager-erőforrás ezen a helyen található, hacsak másként nem jelezzük.
* **artifactSourceSASLocation**: Annak a gyökérkönyvtárnak (a blobtárolónak) a SAS URI-ja, amely a szolgáltatási egység sablonját és paramétereit tárolja az üzembe helyezéshez.  Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
* **binaryArtifactRoot**: Az alapértelmezett érték **binaries/1.0.0.0**. Ne módosítsa ezt az értéket, hacsak nem szeretné módosítani a mappastruktúrát [az összetevők előkészítését](#prepare-the-artifacts) ismertető szakaszban foglaltak szerint. Ebben az oktatóanyagban relatív elérési utakat használunk.  A teljes elérési út a következők összefűzésével áll elő: **artifactSourceSASLocation**, **binaryArtifactRoot** és a **deployPackageUri**, amely a CreateWebApplicationParameters.json fájlban van megadva.  Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
* **managedIdentityID**: A felhasználó által hozzárendelt felügyelt identitás, amely a telepítési műveleteket végrehajtja. Lásd: [A felhasználó által hozzárendelt felügyelt identitás létrehozása](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>A változók

A változók szakasz definiálja az erőforrások nevét. Győződjön meg arról, hogy a szolgáltatástopológia, a szolgáltatások és a szolgáltatási egységek nevei egyeznek a [topológiasablonban](#create-the-service-topology-template) definiált nevekkel.

![Azure Deployment Manager-oktatóanyag – bevezetési sablon változói](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Az erőforrások

A gyökérszinten három erőforrás van definiálva: egy összetevőforrás, egy lépcső és egy bevezetés.

Az összetevőforrás definíciója egyezik a topológiasablonban megadott definícióval.  További információ: [A szolgáltatástopológia-sablon létrehozása](#create-the-service-topology-template).

Az alábbi képernyőképen a várakozási lépcső definíciója látható:

![Azure Deployment Manager-oktatóanyag – bevezetési sablon erőforrásainak várakozási lépcsője](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Az időtartam az [ISO 8601 szabványt](https://en.wikipedia.org/wiki/ISO_8601#Durations) követi. A **PT1M** (nagybetűkkel kell megadni) például 1 perces várakozást jelöl.

Az alábbi képernyőképen a bevezetés definíciójának csak egyes részei láthatók:

![Azure Deployment Manager-oktatóanyag – bevezetési sablon erőforrásainak bevezetése](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: A bevezetési erőforrás az összetevőforrás erőforrástól és az összes definiált lépéstől is függ.
* **artifactSourceId**: Az összetevőforrás erőforrást társítja a bevezetési erőforráshoz.
* **targetServiceTopologyId**: A szolgáltatástopológia-erőforrást társítja a bevezetési erőforráshoz.
* **deploymentTargetId**: A szolgáltatástopológia-erőforrás szolgáltatási egység erőforrás-azonosítója.
* **preDeploymentSteps** és **postDeploymentSteps**: A bevezetés lépéseit tartalmazzák. A sablonban egy várakozási lépés lesz meghívva.
* **dependsOnStepGroups**: A lépéscsoportok közötti függőségeket konfigurálja.

### <a name="rollout-parameters-file"></a>Bevezetési paraméterek fájlja

Hozzon létre egy paraméterfájlt, amely a bevezetési sablonnal használható.

1. Nyissa meg a következő fájlt a Visual Studio Code-ban vagy valamilyen szövegszerkesztőben: **\ADMTemplates\CreateADMRollout.Parameters**.
2. Töltse ki a paraméterek értékeit:

    * **projektnév**: adjon meg egy 4-5 karakterből álló karakterláncot. Ez a név egyedi Azure-erőforrásnevek létrehozására szolgál.
    * **azureResourceLocation**: Azure-beli hely megadására.
    * **artifactSourceSASLocation**: Adja meg annak a gyökérkönyvtárnak (a blobtárolónak) az SAS URI-ját, amely a szolgáltatási egység sablonját és paramétereit tárolja az üzembe helyezéshez.  Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
    * **binaryArtifactRoot**: Hacsak nem módosítja az összetevők mappastruktúráját, használja a **binaries/1.0.0.0** értéket az oktatóanyagban.
    * **managedIdentityID**: Adja meg a felhasználó által hozzárendelt felügyelt identitást. Lásd: [A felhasználó által hozzárendelt felügyelt identitás létrehozása](#create-the-user-assigned-managed-identity). A szintaxis a következő:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> A topológiasablon és a bevezetési sablon egyes paraméterei közösek. Ezeknek a paramétereknek egyező értékekkel kell rendelkezniük. Ezek a paraméterek a következők: **projektnév**, **azureResourceLocation**és **artifactSourceSASLocation** (a jelen oktatóanyagban ugyanazt a Storage-fiókot használják).

## <a name="deploy-the-templates"></a>A sablonok üzembe helyezése

A sablonok az Azure PowerShell használatával telepíthetők.

1. Futtassa a szkriptet a szolgáltatástopológia üzembe helyezéséhez.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Ha ezt a parancsfájlt egy másik PowerShell-munkamenetből futtatja, amelyből az összetevők [előkészítése](#prepare-the-artifacts) parancsfájlt futtatta, először újra fel kell töltenie a változókat, köztük a **$resourceGroupName** és a **$filepath**.

    > [!NOTE]
    > `New-AzResourceGroupDeployment` egy aszinkron hívás. A sikeres üzenet csak azt jelenti, hogy a központi telepítés sikeresen elindult. Az üzembe helyezés ellenőrzéséhez tekintse meg az eljárás 2. és 4. lépését.

2. Ellenőrizze, hogy a szolgáltatástopológia és az alapjául szolgáló erőforrások sikeresen létrejöttek-e az Azure Portal használatával:

    ![Azure Deployment Manager-oktatóanyag – üzembe helyezett szolgáltatástopológia-erőforrások](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Az erőforrások megjelenítéséhez be kell jelölnie a **Rejtett típusok megjelenítése** jelölőnégyzetet.

3. <a id="deploy-the-rollout-template"></a>A bevezetési sablon üzembe helyezése:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. A bevezetés állapotát a következő PowerShell-szkripttel ellenőrizheti:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    A Deployment Manager PowerShell-parancsmagjait telepíteni kell ahhoz, hogy a parancsmagot futtatni lehessen. Lásd az előfeltételek című témakört. A-verbose kapcsoló használatával megtekintheti a teljes kimenetet.

    Az alábbi példa mutatja a futtatási állapotot:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    A bevezetés sikeres üzembe helyezése után két további erőforráscsoport (szolgáltatásonként egy-egy) létrehozása is látható.

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Tallózással keresse meg az újonnan létrehozott webalkalmazásokat a bevezetés üzembe helyezése során létrehozott új erőforráscsoportok alatt.
3. Nyissa meg a webalkalmazást egy webböngészőben. Ellenőrizze a helyet és a verziót az index.html fájlban.

## <a name="deploy-the-revision"></a>A változat üzembe helyezése

Ha új verzió (1.0.0.1) érhető el a webalkalmazáshoz, az alábbi eljárással helyezheti újra üzembe a webalkalmazást.

1. Nyissa meg a CreateADMRollout.Parameters.json fájlt.
2. Frissítse a **binaryArtifactRoot** paramétert a **binaries/1.0.0.1** értékre.
3. Helyezze újra üzembe a bevezetést [a sablonok üzembe helyezését](#deploy-the-rollout-template) ismertető szakasz utasításai szerint.
4. Ellenőrizze a telepítést [a vonatkozó szakaszban](#verify-the-deployment) foglaltak szerint. A weblap most az 1.0.0.1-es verziót mutatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezővel szűkítse a keresést az oktatóanyagban létrehozott erőforráscsoportokra. 3–4 erőforrásnak kell lennie:

    * **&lt;projektnév > RG**: a telepítéskezelő erőforrásait tartalmazza.
    * **&lt;projektnév > ServiceWUSrg**: a ServiceWUS által meghatározott erőforrásokat tartalmazza.
    * **&lt;projektnév > ServiceEUSrg**: a ServiceEUS által meghatározott erőforrásokat tartalmazza.
    * A felhasználó által meghatározott felügyelt identitás erőforráscsoportja.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.
5. Ennek a két lépésnek az ismétlésével törölje az oktatóanyagban létrehozott több erőforráscsoportot is.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag az Azure Deployment Manager használatát mutatta be. Az Azure telepítéskezelő állapotának monitorozásához lásd [: oktatóanyag: az állapot-ellenőrzés használata az azure telepítéskezelőban](./deployment-manager-tutorial-health-check.md).
