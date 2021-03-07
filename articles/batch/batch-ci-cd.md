---
title: Az Azure-folyamatok használata & HPC-megoldások üzembe helyezéséhez
description: Megtudhatja, hogyan helyezhet üzembe Build/kiadási folyamatot egy Azure Batchon futó HPC-alkalmazáshoz.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435545"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>HPC-megoldások létrehozása és üzembe helyezése az Azure-folyamatokkal

Az Azure DevOps által biztosított eszközök lefordíthatók a nagy teljesítményű számítástechnikai (HPC) megoldások automatizált létrehozására és tesztelésére. Az [Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines) -folyamatok számos modern folyamatos integrációs (CI) és folyamatos üzembe helyezési (CD) folyamatot biztosítanak a szoftverek létrehozásához, üzembe helyezéséhez, teszteléséhez és figyeléséhez. Ezek a folyamatok felgyorsítják a szoftverek kézbesítését, így a támogatási infrastruktúra és műveletek helyett a kódra koncentrálhat.

Ez a cikk azt ismerteti, hogyan állítható be a CI/CD-folyamatok az Azure Batch-on üzembe helyezett HPC-megoldásokhoz készült [Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines) -folyamatokkal.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához [Azure DevOps-szervezetre](/azure/devops/organizations/accounts/create-organization)van szükség. [Egy projektet is létre kell hoznia az Azure DevOps-ben](/azure/devops/organizations/projects/create-project).

A Kezdés előtt hasznos lehet a [verziókövetés](/azure/devops/user-guide/source-control) és a [Azure Resource Manager sablon szintaxisának](../azure-resource-manager/templates/template-syntax.md) alapvető ismerete.

## <a name="create-an-azure-pipeline"></a>Azure-folyamat létrehozása

Ebben a példában egy létrehozási és kiadási folyamatot hoz létre egy Azure Batch infrastruktúra üzembe helyezéséhez és egy alkalmazáscsomag kiadásához. Feltételezve, hogy a kód helyileg lett kifejlesztve, ez az általános telepítési folyamat:

![A folyamaton belüli üzembe helyezés folyamatát bemutató ábra](media/batch-ci-cd/DeploymentFlow.png)

Ez a példa számos Azure Resource Manager sablont és meglévő bináris fájlokat használ. Ezeket a példákat átmásolhatja a tárházba, és leküldheti őket az Azure DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>A Azure Resource Manager-sablonok ismertetése

Ez a példa számos Azure Resource Manager sablont használ a megoldás üzembe helyezéséhez. Az adott funkció megvalósításához három képességi sablon (az egységekhez vagy a modulokhoz hasonlóan) használható. Ezután a rendszer a teljes körű megoldási sablont (deployment.js) használja a mögöttes képességi sablonok telepítéséhez. Ez a [csatolt sablon-struktúra ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) lehetővé teszi, hogy minden egyes képesség sablon egyedileg legyen tesztelve és újra felhasználható legyen a különböző megoldásokban.

![A csatolt sablon szerkezetét Azure Resource Manager sablonok használatával ábrázoló diagram.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Ez a sablon egy Azure Storage-fiókot határoz meg, amely szükséges ahhoz, hogy az alkalmazást a Batch-fiókba lehessen telepíteni. Részletes információkért tekintse meg a [Resource Manager-sablonokra vonatkozó útmutató a Microsoft. Storage-erőforrások típusaihoz](/azure/templates/microsoft.storage/allversions)című témakört.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

A következő sablon egy [Azure batch fiókot](accounts.md)határoz meg. A Batch-fiók platformként működik számos alkalmazás különböző [készleteken](nodes-and-pools.md#pools)való futtatásához. Részletes információkért tekintse meg a [Resource Manager-sablonokra vonatkozó dokumentációt Microsoft.BatCH típusú erőforrásokhoz](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

A következő sablon létrehoz egy batch-készletet a Batch-fiókban. Részletes információkért tekintse meg a [Resource Manager-sablonokra vonatkozó dokumentációt Microsoft.BatCH típusú erőforrásokhoz](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

A végső sablon Orchestrator működik, és a három mögöttes képességgel rendelkező sablont telepíti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>A HPC-megoldás ismertetése

Ahogy korábban említettük, a minta számos Azure Resource Manager sablont és meglévő bináris fájlokat használ. Ezeket a példákat átmásolhatja a tárházba, és leküldheti őket az Azure DevOps.

Ebben a megoldásban az FFmpeg-t használja alkalmazáscsomagként. Ha még nem rendelkezik ezzel a lehetőséggel, [letöltheti az FFmpeg-csomagot](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) .

![Képernyőfelvétel az adattár struktúrájáról.](media/batch-ci-cd/git-repository.jpg)

A tárház négy fő szakaszt tartalmaz:

- A Azure Resource Manager sablonokat tartalmazó **ARM-templates** mappa
- **HPC-Application** mappa, amely az [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08)Windows 64 bites verzióját tartalmazza.
- A **folyamatokat tartalmazó mappa,** amely egy YAML-fájlt tartalmaz, amely meghatározza a létrehozási folyamatot.
- Nem kötelező: **ügyfél-alkalmazás** mappa, amely a Azure Batch .net-fájl az FFmpeg-mintával [való feldolgozásának](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) másolata. Ez az alkalmazás nem szükséges ehhez a cikkhez.


> [!NOTE]
> Ez csak egy példa a rendszerértékre történő rendszerszerkezetre. Ezt a módszert arra használjuk, hogy az alkalmazás, az infrastruktúra és a folyamat kódja ugyanabban a tárházban legyen tárolva.

Most, hogy beállította a forráskódot, megkezdheti az első felépítést.

## <a name="continuous-integration"></a>Folyamatos integráció

Az Azure DevOps Services szolgáltatáson belüli [Azure-folyamatok](/azure/devops/pipelines/get-started/)segítenek a létrehozási, tesztelési és üzembe helyezési folyamat megvalósításában az alkalmazásaihoz.

A folyamat ezen szakaszában a tesztek általában a kód érvényesítésére és a szoftver megfelelő részeinek összeállítására futnak. A tesztek száma és típusai, valamint az Ön által futtatott további feladatok a szélesebb körű Build-és kiadási stratégiától függenek.

## <a name="prepare-the-hpc-application"></a>A HPC-alkalmazás előkészítése

Ebben a szakaszban a **HPC-Application** mappában fog működni. Ez a mappa tartalmazza azt a szoftvert (FFmpeg), amely a Azure Batch fiókon belül fut.

1. Navigáljon az Azure DevOps-szervezetének Azure-folyamatok buildek szakaszába. Hozzon létre egy **új** folyamatot.

    ![Képernyőkép az új folyamat képernyőjéről.](media/batch-ci-cd/new-build-pipeline.jpg)

1. A build folyamat létrehozásához két lehetőség közül választhat:

    a. [Használja a vizuális tervezőt](/azure/devops/pipelines/get-started-designer). Ehhez válassza a "Visual Designer használata" lehetőséget az **új folyamat** lapon.

    b. [YAML-buildek használata](/azure/devops/pipelines/get-started-yaml). Az új **folyamat** lapon az Azure Repos vagy a GitHub lehetőségre kattintva hozhat létre új YAML-folyamatot. Azt is megteheti, hogy az alábbi példát a forrás vezérlőelemben tárolja, és egy meglévő YAML-fájlra hivatkozik a vizuális tervező kiválasztásával, majd a YAML sablon használatával.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Ha a buildet igény szerint konfigurálta, válassza a **mentés & üzenetsor** lehetőséget. Ha engedélyezve van a folyamatos integráció (az **Eseményindítók** szakaszban), a Build automatikusan elindul, amikor új véglegesíti a tárházat, és megfelel a buildben megadott feltételeknek.

    ![Képernyőkép egy meglévő összeállítási folyamatról.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Tekintse meg az élő frissítéseket a Build Azure DevOps való fejlesztésének előrehaladásával az Azure-folyamatok **Build** szakaszának megnyitásával. Válassza ki a megfelelő buildet a Build definíciójában.

    ![Képernyőkép a buildből származó élő kimenetekről az Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Ha egy ügyfélalkalmazás használatával hajtja végre a HPC-megoldást, létre kell hoznia egy külön Build-definíciót az alkalmazáshoz. Az [Azure-folyamatok](/azure/devops/pipelines/get-started/index) dokumentációjában számos útmutatók találhatók.

## <a name="continuous-deployment"></a>Folyamatos üzembe helyezés

Az Azure-folyamatokat az alkalmazás és a mögöttes infrastruktúra üzembe helyezésére is felhasználjuk. A [kiadási](/azure/devops/pipelines/release) folyamatok lehetővé teszik a folyamatos üzembe helyezést és a kiadási folyamat automatizálását.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Az alkalmazás és a mögöttes infrastruktúra üzembe helyezése

Az infrastruktúra üzembe helyezésének számos lépése van. Mivel ez a megoldás [csatolt sablonokat](../azure-resource-manager/templates/linked-templates.md)használ, ezeknek a sablonoknak elérhetőnek kell lenniük egy nyilvános végpontról (http vagy https). Ez lehet egy adattár a GitHubon, vagy egy Azure Blob Storage-fiók vagy egy másik tárolási hely. A feltöltött sablon összetevői biztonságban maradhatnak, mivel azok privát módban is tárolhatók, de a közös hozzáférésű aláírás (SAS) jogkivonatának valamilyen formájával érhetők el.

Az alábbi példa bemutatja, hogyan helyezhet üzembe egy infrastruktúrát sablonokkal egy Azure Storage-blob használatával.

1. Hozzon létre egy **új kiadási definíciót**, majd válasszon ki egy üres definíciót. Nevezze át az újonnan létrehozott környezetet a folyamathoz kapcsolódóra.

    ![Képernyőkép a kezdeti kiadási folyamatról.](media/batch-ci-cd/Release-0.jpg)

1. A HPC-alkalmazás kimenetének lekéréséhez hozzon létre egy függőséget a Build folyamaton.

    > [!NOTE]
    > Jegyezze fel a **forrás aliast**, mivel erre akkor van szükség, amikor a feladatok a kiadás definícióján belül jönnek létre.

    ![Képernyőfelvétel: a megfelelő build-folyamat HPCApplicationPackage mutató összetevő-hivatkozás.](media/batch-ci-cd/Release-1.jpg)

1. Hozzon létre egy hivatkozást egy másik összetevőre, ezúttal egy Azure-tárházra. Ez a tárházban tárolt Resource Manager-sablonok eléréséhez szükséges. Mivel a Resource Manager-sablonok nem igénylik a fordítást, nem szükséges leküldeni őket egy Build folyamaton keresztül.

    > [!NOTE]
    > Ezt követően jegyezze fel a **forrás aliast**, mivel később szükség lesz rá.

    ![Képernyőfelvétel: az Azure Repos-ra mutató hivatkozás.](media/batch-ci-cd/Release-2.jpg)

1. Navigáljon a **változók** szakaszhoz. A folyamat során több változót is létre kell hoznia, így nem kell ugyanazt az információt több feladatba újra beírnia. Ez a példa a következő változókat használja:

   - **applicationStorageAccountName**: a HPC alkalmazás bináris fájljainak tárolására szolgáló Storage-fiók neve
   - **batchAccountApplicationName**: az alkalmazás neve a Batch-fiókban
   - **batchAccountName**: a Batch-fiók neve
   - **batchAccountPoolName**: a feldolgozást végző virtuális gépek készletének neve
   - **batchApplicationId**: egyedi azonosító a Batch-alkalmazáshoz
   - **batchApplicationVersion**: a Batch-alkalmazás szemantikai verziója (azaz az FFmpeg bináris fájljai)
   - **hely**: a telepítendő Azure-erőforrások helye
   - **resourceGroupName**: a létrehozandó erőforráscsoport neve, valamint az erőforrások üzembe helyezésének helye
   - **storageAccountName**: a társított Resource Manager-sablonok tárolására szolgáló Storage-fiók neve

   ![Képernyőfelvétel: az Azure-folyamatok kiadására beállított változók.](media/batch-ci-cd/Release-4.jpg)

1. Navigáljon a fejlesztői környezet feladataihoz. Az alábbi pillanatképben hat feladatot láthat. Ezek a feladatok: letölti a tömörített FFmpeg-fájlokat, üzembe helyez egy Storage-fiókot a beágyazott Resource Manager-sablonok tárolásához, másolja ezeket a Resource Manager-sablonokat a Storage-fiókba, telepítse a Batch-fiókot és a szükséges függőségeket, hozzon létre egy alkalmazást a Azure Batch fiókban, és töltse fel az alkalmazáscsomag a Azure Batch fiókba.

    ![A HPC-alkalmazás Azure Batchba való kiadásához használt feladatokat ábrázoló képernyőkép.](media/batch-ci-cd/Release-3.jpg)

1. Adja hozzá a **letöltési folyamat (előzetes verzió)** feladatot, és állítsa be a következő tulajdonságokat:
    - **Megjelenítendő név:** ApplicationPackage letöltése az ügynöknek
    - A **letölteni kívánt összetevő neve:** HPC-Application
    - **Letöltés elérési útja**: $ (System. DefaultWorkingDirectory)

1. Hozzon létre egy Storage-fiókot a Azure Resource Manager-sablonok tárolásához. A megoldásban meglévő Storage-fiókot lehet használni, de ez az önálló minta és a tartalom elkülönítése egy dedikált Storage-fiókkal is elvégezhető.

    Adja hozzá az **Azure-erőforráscsoport telepítési** feladatát, és állítsa be a következő tulajdonságokat:
    - **Megjelenítendő név:** A Storage-fiók üzembe helyezése Resource Manager-sablonokhoz
    - **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    - **Művelet**: erőforráscsoport létrehozása vagy frissítése
    - **Erőforráscsoport**: $ (resourceGroupName)
    - **Hely**: $ (hely)
    - **Sablon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-templates/storageAccount.json
    - **Felülbírálja a sablon paramétereit**:-accountName $ (storageAccountName)

1. Töltse fel az összetevőket a forrás vezérlőelemből a Storage-fiókba az Azure-folyamatok használatával. Az Azure-folyamatok feladatának részeként a Storage-fiók tárolójának URI-ja és az SAS-token az Azure-folyamatok egyik változóján is kiosztható, így az ügynök fázisában újra felhasználhatók lesznek.

    Adja hozzá az **Azure file Copy** feladatot, és állítsa be a következő tulajdonságokat:
    - **Forrás:** $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-templates/
    - **Azure-kapcsolattípus**: Azure Resource Manager
    - **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    - **Cél típusa**: Azure Blob
    - **RM Storage-fiók**: $ (storageAccountName)
    - **Tároló neve**: sablonok
    - **Storage Container URI**: templateContainerUri
    - **Storage-tároló sas-tokenje**: templateContainerSasToken

1. Telepítse a Orchestrator sablont. Ez a sablon paramétereket tartalmaz a Storage-fiók tárolójának URI-ja és az SAS-token számára. A Resource Manager-sablonban szükséges változók a kiadás definíciójának változók szakaszában találhatók, vagy egy másik Azure-folyamatból (például az Azure Blob Copy feladat részeként) lettek beállítva.

    Adja hozzá az **Azure-erőforráscsoport telepítési** feladatát, és állítsa be a következő tulajdonságokat:
    - **Megjelenítendő név:** Azure Batch üzembe helyezése
    - **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    - **Művelet**: erőforráscsoport létrehozása vagy frissítése
    - **Erőforráscsoport**: $ (resourceGroupName)
    - **Hely**: $ (hely)
    - **Sablon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-templates/deployment.json
    - A **sablon paramétereinek felülbírálása**:`-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Gyakori eljárás a Azure Key Vault feladatok használata. Ha az Azure-előfizetéshez kapcsolódó egyszerű szolgáltatásnév megfelelő hozzáférési szabályzatokkal rendelkezik, akkor letöltheti a titkokat egy Azure Key Vault, és változóként is használható a folyamatában. A titok neve a társított értékkel lesz megadva. A sshPassword titka például a $ (sshPassword) kifejezéssel hivatkozhat a kiadás definíciójában.

1. A következő lépések meghívja az Azure CLI-t. Az első a Azure Batch és a társított csomagok feltöltésére szolgáló alkalmazás létrehozásához használatos.

    Adja hozzá az **Azure CLI** -feladatot, és állítsa be a következő tulajdonságokat:
    - **Megjelenítendő név:** Alkalmazás létrehozása Azure Batch fiókban
    - **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    - **Parancsfájl helye**: beágyazott parancsfájl
    - **Beágyazott parancsfájl**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. A második lépés a társított csomagok az alkalmazásba (ebben az esetben az FFmpeg-fájlokra) való feltöltésére szolgál.

    Adja hozzá az **Azure CLI** -feladatot, és állítsa be a következő tulajdonságokat:
    - **Megjelenítendő név:** Csomag feltöltése Azure Batch fiókba
    - **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    - **Parancsfájl helye**: beágyazott parancsfájl
    - **Beágyazott parancsfájl**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > Az alkalmazáscsomag verziószáma változóra van állítva. Ez lehetővé teszi a csomag korábbi verzióinak felülírását, és lehetővé teszi a Azure Batchba leküldett csomag verziószámának manuális vezérlését.

1. Új kiadás létrehozásához válassza a **kiadás > új kiadás létrehozása** lehetőséget. Az aktiválás után válassza ki az új kiadásra mutató hivatkozást az állapot megtekintéséhez.

1. Tekintse meg az ügynök élő kimenetét a környezete alatti **naplók** gombra kattintva.

    ![A kiadás állapotát bemutató képernyőkép.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>A környezet tesztelése

A környezet beállítása után ellenőrizze, hogy a következő tesztek sikeresen befejeződtek-e.

Kapcsolódjon az új Azure Batch-fiókhoz az Azure CLI használatával egy PowerShell-parancssorból.

- Jelentkezzen be az Azure-fiókjába, `az login` és kövesse az utasításokat a hitelesítéshez.
- Most hitelesítse a Batch-fiókot: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Az elérhető alkalmazások listázása

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>A készlet érvényességének ellenőrzéséhez

```azurecli
az batch pool list
```

Jegyezze fel a `currentDedicatedNodes` parancs kimenetének értékét. Ezt az értéket a következő tesztben kell beállítani.

#### <a name="resize-the-pool"></a>A készlet átméretezése

Méretezze át a készletet úgy, hogy elérhetők legyenek a feladatok és a tevékenységek teszteléséhez szükséges számítási csomópontok. a készlet lista parancsával ellenőrizze, hogy az aktuális állapot, amíg az átméretezés be nem fejeződik, és rendelkezésre áll-e csomópontok

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatja, hogyan dolgozhat egy batch-fiókkal egy egyszerű alkalmazás használatával.

- [Párhuzamos számítási feladatok futtatása Azure Batch a Python API használatával](tutorial-parallel-python.md)
- [Párhuzamos számításifeladat-futtatás az Azure Batchben a .NET API használatával](tutorial-parallel-dotnet.md)
