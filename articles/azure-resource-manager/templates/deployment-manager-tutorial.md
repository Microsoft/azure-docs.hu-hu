---
title: Sablonok üzembe helyezése az Azure Deployment Manager használatával
description: Ismerje meg, hogyan helyezhet üzembe Azure-erőforrásokat az Azure Deployment Manager Resource Manager-sablonok használatával.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627583"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Oktatóanyag: Az Azure Deployment Manager Resource Manager-sablonokkal való használata (nyilvános előzetes verzió)

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe alkalmazásait több régióban az [Azure Deployment Manager](./deployment-manager-overview.md) használatával. Ha gyorsabb megközelítésre van szüksége, az [Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart) rövid útmutatója létrehozza a szükséges konfigurációkat az előfizetésében, és testreszabja az összetevőket több régióban történő alkalmazás üzembe helyezéséhez. A rövid útmutató ugyanazokat a feladatokat hajtja végre, mint az oktatóanyagban.

Deployment Manager használatához két sablont kell létrehoznia:

* **Topológiasablon**: Az alkalmazást alkotó Azure-erőforrásokat és a telepítési helyüket írja le.
* **Bevezetési sablon**: Az alkalmazások telepítése során végrehajtandó lépéseket írja le.

> [!IMPORTANT]
> Ha az előfizetése Kanári-re van megjelölve az új Azure-funkciók kipróbálásához, akkor csak az Azure Deployment Managert használhatja a Kanári-régiókban való üzembe helyezéshez.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A forgatókönyv megismerése
> * Az oktatóanyag fájljainak letöltése
> * Az összetevők előkészítése
> * A felhasználói felügyelt identitás létrehozása
> * A szolgáltatástopológia-sablon létrehozása
> * A bevezetési sablon létrehozása
> * A sablonok üzembe helyezése
> * Az üzemelő példány ellenőrzése
> * Az újabb verzió üzembe helyezése
> * Az erőforrások eltávolítása

További források:

* Az [Azure Deployment Manager REST API referenciája](/rest/api/deploymentmanager/).
* [Oktatóanyag: az állapot-ellenőrzési szolgáltatás használata az Azure Deployment Managerban](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Némi gyakorlat az [Azure Resource Manager-sablonok](overview.md) kialakításában.
* Azure PowerShell. További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).
* Deployment Manager-parancsmagok. Az előzetes verziójú parancsmagok telepítéséhez a PowerShellGet legújabb verziójára lesz szüksége. A legújabb verzió beszerzéséről lásd [a PowerShellGet telepítését](/powershell/scripting/gallery/installing-psget) ismertető cikket. A PowerShellGet telepítése után zárja be a PowerShell-ablakot. Nyisson meg egy új, emelt szintű PowerShell-ablakot, és használja a következő parancsot:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>A forgatókönyv megismerése

A szolgáltatás-topológia sablonja ismerteti a szolgáltatást alkotó Azure-erőforrásokat és a telepítésük helyét. A szolgáltatástopológia definíciója az alábbi hierarchiát követi:

* Szolgáltatástopológia
  * Szolgáltatások
    * Szolgáltatási egységek

Az alábbi ábra az oktatóanyagban használt szolgáltatástopológiát mutatja be:

![Azure Deployment Manager-oktatóanyag – a forgatókönyv ábrája](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Az USA nyugati régiójában és az USA keleti régiójában két szolgáltatás van lefoglalva. Minden szolgáltatás két szolgáltatási egységgel rendelkezik: egy előtér-webalkalmazás és egy háttérbeli Storage-fiók. A szolgáltatási egység definíciói hivatkozásokat tartalmaznak a webalkalmazásokat és a Storage-fiókokat létrehozó sablon-és paraméter-fájlokra.

## <a name="download-the-tutorial-files"></a>Az oktatóanyag fájljainak letöltése

1. Töltse le az oktatóanyagban használt [sablonokat és összetevőket](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip).
1. Tömörítse ki a fájlokat a helyi számítógépen.

A gyökérmappában két mappa található:

* _ADMTemplates_: A Deployment Manager-sablonokat tartalmazza, nevezetesen a következőket:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: A sablonösszetevőket és a bináris összetevőket tartalmazza. Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).

A sablonoknak két halmaza van. Az egyik készlet a szolgáltatás topológiájának és bevezetésének üzembe helyezéséhez használt Deployment Manager-sablonok. A másik készletet a szolgáltatási egységek határozzák meg a webszolgáltatások és a Storage-fiókok létrehozásához.

## <a name="prepare-the-artifacts"></a>Az összetevők előkészítése

A letöltés ArtifactStore mappája két mappát tartalmaz:

![Azure Deployment Manager-oktatóanyag – az összetevőforrás ábrája](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* A _templates_ mappa: A sablonösszetevőket tartalmazza. A _1.0.0.0_ és az _1.0.0.1_ -es mappák a bináris összetevők két verzióját jelölik. Az egyes verziókon belül minden szolgáltatáshoz van egy mappa: _ServiceEUS_ (az USA keleti régiója) és a _ServiceWUS_ (Service West USA). Mindegyik szolgáltatás rendelkezik egy sablon- és egy paraméterfájllal a tárfiók létrehozásához, és egy másik ilyen fájlpárossal a webalkalmazás készítéséhez. A webalkalmazás-sablon egy tömörített csomagot hív meg, amely a webalkalmazás fájljait tartalmazza. A tömörített fájl egy bináris összetevő, amely a binaries mappában található.
* A _binaries_ mappa: A bináris összetevőket tartalmazza. A _1.0.0.0_ és az _1.0.0.1_ -es mappák a bináris összetevők két verzióját jelölik. Az egyes verziókon belül egy zip-fájl található a webalkalmazás létrehozásához az USA nyugati régiójában, a másik zip-fájl pedig a webalkalmazást az USA keleti régiójában hozza létre.

A két verzió (1.0.0.0 és 1.0.0.1) a [változatok üzembe helyezését](#deploy-the-revision) szolgálja. Bár a sablonösszetevőknek és a bináris összetevőknek is két-két verziója van, csak a bináris összetevőknek térnek el a verziói. A gyakorlatban a bináris összetevők gyakrabban frissülnek, mint a sablonösszetevők.

1. Nyissa meg a következő fájlt egy szövegszerkesztőben: _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_. Ez egy alapszintű sablon a Storage-fiók létrehozásához.
1. Nyissa meg a következő fájlt: _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Azure Deployment Manager oktatóanyag – webalkalmazás-létrehozási sablon](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    A sablon egy telepítőcsomagot hív meg, amely a webalkalmazás fájljait tartalmazza. Ebben az oktatóanyagban a tömörített csomag csak _index.html_ -fájlt tartalmaz.
1. Nyissa meg a következő fájlt: _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![Azure Deployment Manager-oktatóanyag – webalkalmazás-létrehozási sablon paraméterei, containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    A értéke a `deployPackageUri` központi telepítési csomag elérési útja. A paraméter `$containerRoot` változót tartalmaz. Az érték a bevezetési `$containerRoot` [sablonban](#create-the-rollout-template) az összetevő forrás sas helyének, az összetevő gyökerének és a. összefűzésével van megadva `deployPackageUri` .
1. Nyissa meg a következő fájlt: _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

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

    A HTML-ben a hely és a verzió adatai láthatók. A _1.0.0.1_ mappában található bináris fájl az 1.0.0.1-es _verziót_ jeleníti meg. A szolgáltatás telepítése után ezekhez az oldalakhoz tallózhat.
1. Tekintse át az összetevők fájljait. Segítenek jobban megérteni a forgatókönyvet.

A sablonösszetevőket a szolgáltatástopológia-sablon, a bináris összetevőket a bevezetési sablon használja. A topológiasablon és a bevezetési sablon is meghatároz egy összetevőforrásként szolgáló Azure-erőforrást, amely az üzemelő példányban használt sablon- és bináris összetevőkre irányítja a Resource Managert. Az oktatóanyag egyszerűsége érdekében ugyanaz a tárfiók tárolja a sablonösszetevőket és a bináris összetevőket. Mindkét összetevőforrás ugyanarra a tárfiókra mutat.

Futtassa a következő PowerShell-parancsfájlt egy erőforráscsoport létrehozásához, egy tároló létrehozásához, egy blob-tároló létrehozásához, a letöltött fájlok feltöltéséhez, majd hozzon létre egy SAS-tokent.

> [!IMPORTANT]
> `projectName` a PowerShell-szkriptben az ebben az oktatóanyagban üzembe helyezett Azure-szolgáltatások nevének előállítására szolgál. A különböző Azure-szolgáltatások eltérő követelményekkel rendelkeznek a neveknél. Az üzembe helyezés sikerességének biztosításához válasszon egy 12 karakternél rövidebb nevet, amely csak kisbetűket és számokat tartalmazhat.
> Mentse a projekt neve másolatát. Ugyanazt az `projectName` oktatóanyagot használja.

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

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Készítsen másolatot az URL-címről az SAS-jogkivonattal. Ez az URL-cím szükséges ahhoz, hogy feltöltse a mezőket a két paraméter fájljában: topológiai paraméterek fájl és bevezetési paraméterek fájl.

Nyissa meg a tárolót a Azure Portalból, és ellenőrizze, hogy a _bináris_ fájlok és a _sablonok_ mappái is megtalálhatók-e.

## <a name="create-the-user-assigned-managed-identity"></a>A felhasználó által hozzárendelt felügyelt identitás létrehozása

Az oktatóanyag későbbi részében üzembe helyezünk egy bevezetést. Az üzembehelyezési műveletek végrehajtásához (például a webalkalmazások és a tárfiók üzembe helyezéséhez) szükség van egy felhasználó által hozzárendelt felügyelt identitásra. Ennek az identitásnak hozzáférést kell adni az Azure-előfizetéshez, amelyben a szolgáltatás üzembe lesz helyezve, és megfelelő jogosultságokkal kell rendelkeznie az összetevők üzembe helyezésének végrehajtásához.

Létre kell hoznia egy felhasználó által hozzárendelt felügyelt identitást, és konfigurálnia az előfizetése hozzáférés-vezérlését.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. [Felhasználó által hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)létrehozása.
1. A portál bal oldali menüjében válassza az **Előfizetések** lehetőséget, majd válassza ki az előfizetést.
1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása** elemet.
1. Adja meg vagy válassza ki a következő értékeket:

    ![Azure Deployment Manager-oktatóanyag – felhasználó által hozzárendelt felügyelt identitás hozzáférés-vezérlése](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Szerepkör**: Rendeljen hozzá elegendő jogosultságot az összetevők (a webalkalmazások és a tárfiókok) üzembe helyezéséhez. Ebben az oktatóanyagban válassza a **Közreműködő** szerepkört. A gyakorlatban azonban a jogosultságokat érdemes a minimálisra korlátozni.
    * **Hozzáférés hozzárendelése**: válassza a **felhasználóhoz rendelt felügyelt identitás** elemet.
    * Válassza ki a felhasználó által hozzárendelt felügyelt identitást, amelyet az oktatóanyag korábbi részében hozott létre.
1. Kattintson a **Mentés** gombra.

## <a name="create-the-service-topology-template"></a>A szolgáltatástopológia-sablon létrehozása

Nyissa meg a következő fájlt: _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>A paraméterek

A sablon a következő paramétereket tartalmazza:

* `projectName`: Ezt a nevet használja a Deployment Manager erőforrások nevének létrehozásához. Például a **bemutató** használatával a szolgáltatás topológiájának neve a **bemutató** ServiceTopology. Az erőforrások nevei a sablon szakaszban vannak meghatározva `variables` .
* `azureResourcelocation`: Az oktatóanyag leegyszerűsítése érdekében az összes erőforrás megosztja ezt a helyet, kivéve, ha másként van megadva.
* `artifactSourceSASLocation`: A blob-tároló SAS URI-ja, ahol a Service Unit-sablont és a paramétereket tartalmazó fájlokat a rendszer a központi telepítéshez tárolja. Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
* `templateArtifactRoot`: A sablonok és paraméterek tárolására szolgáló blob-tároló eltolási útvonala. Az alapértelmezett érték _templates/1.0.0.0_. Ne módosítsa ezt az értéket, hacsak nem szeretné módosítani a mappastruktúrát [az összetevők előkészítését](#prepare-the-artifacts) ismertető szakaszban foglaltak szerint. Ebben az oktatóanyagban relatív elérési utakat használunk. A teljes elérési utat összefűzéssel `artifactSourceSASLocation` , `templateArtifactRoot` és `templateArtifactSourceRelativePath` (vagy `parametersArtifactSourceRelativePath` ) kell összeállítani.
* `targetSubscriptionID`: Az az előfizetés-azonosító, amelyre a Deployment Manager erőforrásokat telepíteni és számlázni kívánja. Ebben az oktatóanyagban használja a saját előfizetése azonosítóját.

### <a name="the-variables"></a>A változók

A változók szakasz az erőforrások, a két szolgáltatás Azure-helyeinek, `ServiceWUS` `ServiceEUS` valamint az összetevők elérési útját határozza meg:

![Azure Deployment Manager-oktatóanyag – topológiasablon változói](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Vesse össze az összetevők elérési útját a tárfiókba feltöltött mappastruktúrával. Figyelje meg, hogy az összetevők elérési útjai relatívak. A teljes elérési utat összefűzéssel `artifactSourceSASLocation` , `templateArtifactRoot` és `templateArtifactSourceRelativePath` (vagy `parametersArtifactSourceRelativePath` ) kell összeállítani.

### <a name="the-resources"></a>Az erőforrások

A gyökérszintű szinten két erőforrás van definiálva: *egy összetevő forrása* és *egy szolgáltatás-topológia*.

Az összetevőforrás definíciója:

![Azure Deployment Manager-oktatóanyag – topológiasablon erőforrásainak összetevőforrása](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Az alábbi képernyőképen a szolgáltatástopológiának, a szolgáltatásoknak és a szolgáltatási egységek definícióinak csak egyes részei láthatók:

![Azure Deployment Manager-oktatóanyag – topológiasablon erőforrásainak szolgáltatástopológiája](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Az összetevő-forrás erőforrás a szolgáltatás topológiájának erőforráshoz való hozzárendelésére szolgál.
* `dependsOn`: Az összes szolgáltatás-topológiai erőforrás az összetevő forrásának erőforrásaitól függ.
* `artifacts`: Mutasson a sablon összetevőire. Itt relatív elérési utakat használunk. A teljes elérési utat összefűzéssel (az összetevő forrása határozza meg), a (az összetevő `artifactSourceSASLocation` `artifactRoot` forrása) és `templateArtifactSourceRelativePath` (vagy) összefűzésével kell összeállítani `parametersArtifactSourceRelativePath` .

### <a name="topology-parameters-file"></a>Topológia-paraméterek fájlja

Hozzon létre egy paraméterfájlt, amely a topológiasablonnal használható.

1. Nyissa meg _\ADMTemplates\CreateADMServiceTopology.Parameters.jsa_ Visual Studio Code-ban vagy bármely szövegszerkesztőben.
1. Adja meg a paraméterek értékét:

    * `projectName`: Adjon meg egy 4-5 karakterből álló karakterláncot. Ez a név egyedi Azure-erőforrásnevek létrehozására szolgál.
    * `azureResourceLocation`: Ha még nem ismeri az Azure-telephelyeket, használja az oktatóanyag **CentralUS** .
    * `artifactSourceSASLocation`: Írja be az SAS URI-t a gyökérkönyvtárba (a blob-tárolóba), ahol a Service Unit-sablont és a paramétereket tartalmazó fájlokat a rendszer a központi telepítéshez tárolja.  Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
    * `templateArtifactRoot`: Ha nem módosítja az összetevők mappastruktúrát, az oktatóanyagban használja a _sablonok/1.0.0.0_ .

> [!IMPORTANT]
> A topológiasablon és a bevezetési sablon egyes paraméterei közösek. Ezeknek a paramétereknek egyező értékekkel kell rendelkezniük. Ezek a paraméterek a következők: `projectName` , és (az összetevők mindkét összetevője `azureResourceLocation` `artifactSourceSASLocation` ugyanazt a Storage-fiókot használja ebben az oktatóanyagban).

## <a name="create-the-rollout-template"></a>A bevezetési sablon létrehozása

Nyissa meg a következő fájlt: _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>A paraméterek

A sablon a következő paramétereket tartalmazza:

![Azure Deployment Manager-oktatóanyag – bevezetési sablon paraméterei](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Ezt a nevet használja a Deployment Manager erőforrások nevének létrehozásához. Például a **bemutató** használatakor a bevezetés neve a **bemutató** bevezetése. A nevek a sablon szakaszban vannak meghatározva `variables` .
* `azureResourcelocation`: Az oktatóanyag leegyszerűsítése érdekében az összes Deployment Manager erőforrás megosztja ezt a helyet, kivéve, ha másként van megadva.
* `artifactSourceSASLocation`: Az SAS URI-ja a gyökérkönyvtárhoz (a blob-tárolóhoz), ahol a Service Unit-sablont és a paramétereket tartalmazó fájlokat a rendszer a központi telepítéshez tárolja. Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
* `binaryArtifactRoot`: Az alapértelmezett érték _bináris/1.0.0.0_. Ne módosítsa ezt az értéket, hacsak nem szeretné módosítani a mappastruktúrát [az összetevők előkészítését](#prepare-the-artifacts) ismertető szakaszban foglaltak szerint. Ebben az oktatóanyagban relatív elérési utakat használunk. A teljes elérési utat összefűzéssel `artifactSourceSASLocation` , `binaryArtifactRoot` és a `deployPackageUri` megadott _CreateWebApplicationParameters.js_. Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
* `managedIdentityID`: A központi telepítési műveleteket végrehajtó, felhasználó által hozzárendelt felügyelt identitás. Lásd: [A felhasználó által hozzárendelt felügyelt identitás létrehozása](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>A változók

A `variables` szakasz az erőforrások nevét határozza meg. Győződjön meg arról, hogy a szolgáltatástopológia, a szolgáltatások és a szolgáltatási egységek nevei egyeznek a [topológiasablonban](#create-the-service-topology-template) definiált nevekkel.

![Azure Deployment Manager-oktatóanyag – bevezetési sablon változói](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Az erőforrások

A gyökérszinten három erőforrás van definiálva: egy összetevőforrás, egy lépcső és egy bevezetés.

Az összetevőforrás definíciója egyezik a topológiasablonban megadott definícióval. További információ: [A szolgáltatástopológia-sablon létrehozása](#create-the-service-topology-template).

A következő képernyőképen a `wait` lépés definíciója látható:

![Azure Deployment Manager-oktatóanyag – bevezetési sablon erőforrásainak várakozási lépcsője](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Az időtartam az [ISO 8601 szabványt](https://en.wikipedia.org/wiki/ISO_8601#Durations)használja. A **PT1M** (nagybetűkkel kell megadni) például 1 perces várakozást jelöl.

Az alábbi képernyőképen a bevezetés definíciójának csak egyes részei láthatók:

![Azure Deployment Manager-oktatóanyag – bevezetési sablon erőforrásainak bevezetése](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`: A bevezetési erőforrás az összetevő forrásának erőforrásaitól és a definiált lépésektől függ.
* `artifactSourceId`: Az összetevő forrás-erőforrásának bevezetési erőforráshoz való hozzárendelésére szolgál.
* `targetServiceTopologyId`: A szolgáltatási topológia erőforrásának bevezetési erőforráshoz való hozzárendelésére szolgál.
* `deploymentTargetId`: Ez a szolgáltatási topológia erőforrásának szolgáltatási egység erőforrás-azonosítója.
* `preDeploymentSteps` és `postDeploymentSteps` : a bevezetési lépéseket tartalmazza. A sablonban egy `wait` lépést kell meghívni.
* `dependsOnStepGroups`: Konfigurálja a lépés csoportok közötti függőségeket.

### <a name="rollout-parameters-file"></a>Bevezetési paraméterek fájlja

Hozzon létre egy paraméterfájlt, amely a bevezetési sablonnal használható.

1. Nyissa meg _\ADMTemplates\CreateADMRollout.Parameters.jsa_ Visual Studio Code-ban vagy bármely szövegszerkesztőben.
1. Adja meg a paraméterek értékét:

    * `projectName`: Adjon meg egy 4-5 karakterből álló karakterláncot. Ez a név egyedi Azure-erőforrásnevek létrehozására szolgál.
    * `azureResourceLocation`: Válasszon egy Azure-helyet.
    * `artifactSourceSASLocation`: Írja be az SAS URI-t a gyökérkönyvtárba (a blob-tárolóba), ahol a Service Unit-sablont és a paramétereket tartalmazó fájlokat a rendszer a központi telepítéshez tárolja.  Lásd: [Az összetevők előkészítése](#prepare-the-artifacts).
    * `binaryArtifactRoot`: Ha nem módosítja az összetevők mappastruktúrát, használja az oktatóanyag _bináris/1.0.0.0_ .
    * `managedIdentityID`: Adja meg a felhasználó által hozzárendelt felügyelt identitást. Lásd: [A felhasználó által hozzárendelt felügyelt identitás létrehozása](#create-the-user-assigned-managed-identity). A szintaxis a következő:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> A topológiasablon és a bevezetési sablon egyes paraméterei közösek. Ezeknek a paramétereknek egyező értékekkel kell rendelkezniük. Ezek a paraméterek a következők: `projectName` , és (az összetevők mindkét összetevője `azureResourceLocation` `artifactSourceSASLocation` ugyanazt a Storage-fiókot használja ebben az oktatóanyagban).

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

    Ha ezt a parancsfájlt egy másik PowerShell-munkamenetből futtatja, amelyből az összetevők [előkészítése](#prepare-the-artifacts) parancsfájlt futtatta, akkor először újra fel kell töltenie a változókat, köztük a és a elemet `$resourceGroupName` `$filePath` .

    > [!NOTE]
    > `New-AzResourceGroupDeployment` aszinkron hívás. A sikeres üzenet csak azt jelenti, hogy a központi telepítés sikeresen elindult. Az üzembe helyezés ellenőrzéséhez tekintse meg az eljárás 2. és 4. lépését.

1. Ellenőrizze, hogy a szolgáltatástopológia és az alapjául szolgáló erőforrások sikeresen létrejöttek-e az Azure Portal használatával:

    ![Azure Deployment Manager-oktatóanyag – üzembe helyezett szolgáltatástopológia-erőforrások](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Az erőforrások megjelenítéséhez be kell jelölnie a **Rejtett típusok megjelenítése** jelölőnégyzetet.

1. <a id="deploy-the-rollout-template"></a>Helyezze üzembe a bevezetési sablont:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. A bevezetés állapotát a következő PowerShell-szkripttel ellenőrizheti:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    A Deployment Manager PowerShell-parancsmagjait telepíteni kell ahhoz, hogy a parancsmagot futtatni lehessen. Lásd: [Előfeltételek](#prerequisites). A `-Verbose` paraméter használatával megtekintheti a teljes kimenetet.

    Az alábbi példa mutatja a futtatási állapotot:

    ```Output
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

    A bevezetés sikeres üzembe helyezését követően két további erőforráscsoport jön létre, egyet az egyes szolgáltatásokhoz.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Tallózással keresse meg az újonnan létrehozott webes alkalmazásokat a bevezetési telepítés által létrehozott új erőforráscsoportok területen.
1. Nyissa meg a webalkalmazást egy webböngészőben. Ellenőrizze a helyet és a verziót a _index.html_ fájlban.

## <a name="deploy-the-revision"></a>A változat üzembe helyezése

Ha új verzió (1.0.0.1) érhető el a webalkalmazáshoz, az alábbi eljárással helyezheti újra üzembe a webalkalmazást.

1. _CreateADMRollout.Parameters.jsmegnyitása a_ következőn:.
1. Frissítés `binaryArtifactRoot` _bináris/1.0.0.1-_ re.
1. Helyezze újra üzembe a bevezetést [a sablonok üzembe helyezését](#deploy-the-rollout-template) ismertető szakasz utasításai szerint.
1. Ellenőrizze a telepítést [a vonatkozó szakaszban](#verify-the-deployment) foglaltak szerint. A weblap most az 1.0.0.1-es verziót mutatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
1. A **Szűrés név alapján** mezővel szűkítse a keresést az oktatóanyagban létrehozott erőforráscsoportokra.

    * **&lt; projektnév>RG**: a Deployment Manager erőforrásait tartalmazza.
    * **&lt; projektnév>ServiceWUSrg**: a ServiceWUS által definiált erőforrásokat tartalmazza.
    * **&lt; projektnév>ServiceEUSrg**: a ServiceEUS által definiált erőforrásokat tartalmazza.
    * A felhasználó által meghatározott felügyelt identitás erőforráscsoportja.
1. Válassza ki az erőforráscsoport nevét.
1. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.
1. Ennek a két lépésnek az ismétlésével törölje az oktatóanyagban létrehozott több erőforráscsoportot is.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag az Azure Deployment Manager használatát mutatta be. Az Azure Deployment Manager állapotának monitorozásához lásd [: oktatóanyag: az állapot-ellenőrzés használata az azure Deployment Managerban](./deployment-manager-tutorial-health-check.md).
