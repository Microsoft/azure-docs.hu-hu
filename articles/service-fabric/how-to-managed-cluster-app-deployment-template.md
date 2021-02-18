---
title: Service Fabric felügyelt fürt (előzetes verzió) alkalmazás üzembe helyezése ARM-sablonnal
description: Alkalmazás üzembe helyezése Azure Service Fabric felügyelt fürtön (előzetes verzió) Azure Resource Manager sablonnal.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b2d73180de61f44850b9c52a7ac24b0c23c36db2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642396"
---
# <a name="deploy-a-service-fabric-managed-cluster-preview-application-using-arm-template"></a>Service Fabric felügyelt fürt (előzetes verzió) alkalmazás üzembe helyezése ARM-sablonnal

Az Azure Service Fabric-alkalmazások a Service Fabric felügyelt fürtön való üzembe helyezéséhez több lehetőség is rendelkezésre áll. A Azure Resource Manager használatát javasoljuk. Ha a Resource Managert használja, lekérdezheti az alkalmazásokat és a szolgáltatásokat a JSON-ban, majd telepítheti őket a fürttel azonos Resource Manager-sablonban. Ha a PowerShell vagy az Azure CLI használatával telepíti és felügyeli az alkalmazásokat, a Resource Manager használata esetén nem kell megvárnia, hogy a fürt készen álljon; az alkalmazás regisztrálása, kiépítése és üzembe helyezése egyetlen lépésben történhet. A Resource Manager használata a legjobb módszer a fürtben az alkalmazás életciklusának kezelésére. További információt az [ajánlott eljárások: az infrastruktúra mint kód](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)című témakörben talál.

Az alkalmazások erőforrásként való kezelése a Resource Managerben a következő területeken nyújt segítséget:

* Naplózási nyomvonal: a Resource Manager naplózza az összes műveletet, és megőrzi a részletes tevékenység naplóját. A műveletnapló segítségével nyomon követheti az alkalmazásokon és a fürtön végrehajtott módosításokat.
* Szerepköralapú hozzáférés-vezérlés: kezelheti a fürtökhöz való hozzáférést és a fürtön üzembe helyezett alkalmazásokat ugyanazzal a Resource Manager-sablonnal.
* Felügyeleti hatékonyság: a Resource Manager használatával egyetlen helyet (a Azure Portal) biztosít a fürt és a kritikus fontosságú alkalmazások központi telepítésének kezeléséhez.

Ebből a dokumentumból megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Alkalmazás-erőforrások üzembe helyezése a Resource Manager használatával.
> * Az alkalmazás erőforrásainak frissítése a Resource Manager használatával.
> * Alkalmazás-erőforrások törlése.

## <a name="deploy-application-resources"></a>Alkalmazás-erőforrások központi telepítése

Az alkalmazások és szolgáltatások a Resource Manager alkalmazás-erőforrás-modell használatával történő üzembe helyezésének magas szintű lépései a következők:
1. Az alkalmazás kódjának becsomagolása.
1. Töltse fel a csomagot.
1. Egy Resource Manager-sablonban lévő csomag helyének hivatkozása alkalmazás-erőforrásként. 

További információkért tekintse meg [az alkalmazás csomagját](service-fabric-package-apps.md#create-an-sfpkg).

Ezután létre kell hoznia egy Resource Manager-sablont, frissítenie kell a paramétereket tartalmazó fájlt az alkalmazás részleteivel, és telepítenie kell a sablont a Service Fabric-fürtön. [Ismerkedjen meg a mintákkal](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster).

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Egy alkalmazás Resource Manager-sablonból való üzembe helyezéséhez rendelkeznie kell egy Storage-fiókkal. A Storage-fiók az alkalmazás rendszerképének előkészítésére szolgál. 

Felhasználhat egy meglévő Storage-fiókot, vagy létrehozhat egy új Storage-fiókot az alkalmazások előkészítéséhez. Ha meglévő Storage-fiókot használ, akkor kihagyhatja ezt a lépést. 

![Tárfiók létrehozása][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>A Storage-fiók konfigurálása

A Storage-fiók létrehozása után létre kell hoznia egy BLOB-tárolót, amelyben az alkalmazások elhelyezhetők. A Azure Portal keresse meg azt az Azure Storage-fiókot, ahol az alkalmazásokat tárolni szeretné. Válassza a **Blobok**  >  **Hozzáadás tároló** elemet. 

A fürtben lévő erőforrások biztonságossá tétele a nyilvános hozzáférési szint **magánjellegűre** állításával lehetséges. A hozzáférést több módon is megadhatja:

* A blobokhoz és várólistákhoz való hozzáférés engedélyezése [Azure Active Directory](../storage/common/storage-auth-aad-app.md)használatával.
* Hozzáférés biztosítása az Azure Blob-és üzenetsor-adataihoz [az Azure Portal Azure RBAC](../storage/common/storage-auth-aad-rbac-portal.md)használatával.
* Hozzáférés delegálása [közös hozzáférésű aláírás](/rest/api/storageservices/delegate-access-with-shared-access-signature)használatával.

Az alábbi képernyőképen szereplő példa a Blobok névtelen olvasási hozzáférését használja.

![BLOB létrehozása][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Az alkalmazás előkészítése a Storage-fiókban

Az alkalmazások üzembe helyezése előtt a blob Storage-ban kell megadnia az alkalmazást. Ebben az oktatóanyagban manuálisan hozunk létre alkalmazáscsomag-csomagot. Ne feledje, hogy ez a lépés automatizálható. További információ: [alkalmazás becsomagolása](service-fabric-package-apps.md#create-an-sfpkg). 

Ebben az oktatóanyagban a [szavazási minta alkalmazást](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)használjuk.

1. A Visual Studióban kattintson a jobb gombbal a **szavazási** projektre, majd válassza a **csomag** elemet.

   ![Alkalmazáscsomag][PackageApplication]  
1. Nyissa meg a *.\service-Fabric-DotNet-quickstart\Voting\pkg\Debug* könyvtárat. A tartalom zip-fájlját egy *Voting.zip* nevű fájlba. A *ApplicationManifest.xml* fájlnak a zip-fájlban lévő gyökérkönyvtárban kell lennie.

   ![Zip-alkalmazás][ZipApplication]  
1. Nevezze át a fájlt úgy, hogy a. zip kiterjesztését a *. sfpkg* értékre módosítsa.

1. A Azure Portal a Storage-fiókja **alkalmazások** tárolójában válassza a **feltöltés**, majd a **szavazás. sfpkg** feltöltés lehetőséget. 

   ![Alkalmazáscsomag feltöltése][UploadAppPkg]

Az alkalmazás most már előkészítés alatt áll, és létrehozhatja az alkalmazás üzembe helyezéséhez szükséges Resource Manager-sablont.

### <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

A minta alkalmazás az alkalmazás üzembe helyezéséhez használható [Azure Resource Manager sablonokat](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) tartalmaz. A sablon fájlnevei *UserApp.jsbe* és *UserApp.Parameters.jsbe*.

> [!NOTE]
> A fájl *UserApp.Parameters.js* a fürt nevével kell frissíteni.
>
>

| Paraméter              | Leírás                                 | Példa                                                      | Megjegyzések                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Annak a fürtnek a neve, amelyre telepítve van | SF – cluster123                                                |                                                              |
| alkalmazás            | Az alkalmazás neve                 | Szavazás                                                       |
| version    | Az alkalmazás erőforrás-azonosítója, típusa és verziója.       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | Meg kell egyeznie ApplicationManifest.xml                 |              |
| serviceName            | A szolgáltatás neve         | VotingWeb                                             | ServiceType formátumúnak kell lennie            |
| serviceTypeName        | A szolgáltatás típusának neve                | VotingWebType                                                    | Meg kell egyeznie ServiceManifest.xml                 |
| appPackageUrl          | Az alkalmazás blob Storage URL-címe     | https: \/ /servicefabricapps.blob.Core.Windows.net/apps/voting.sfpkg | Az alkalmazáscsomag blob Storage-beli URL-címe (az URL-cím beállításához szükséges eljárást a cikk későbbi részében ismertetjük) |

```json
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

A **New-AzResourceGroupDeployment** parancsmag futtatásával telepítse az alkalmazást a fürtöt tartalmazó erőforráscsoporthoz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>A Service Fabric alkalmazás frissítése a Resource Manager használatával

> [!IMPORTANT]
> Az ARM JSON-definíción keresztül üzembe helyezett szolgáltatásokat el kell távolítani a megfelelő ApplicationManifest.xml fájl DefaultServices szakaszából.


A következő okok egyike miatt frissíthet egy Service Fabric-fürtre már telepített alkalmazást:

* Új szolgáltatás kerül be az alkalmazásba. A szolgáltatáshoz hozzá kell adni egy szolgáltatási definíciót *service-manifest.xml* és *application-manifest.xml* fájlokhoz, amikor egy szolgáltatás hozzá lett adva az alkalmazáshoz. Az alkalmazás új verziójának megjelenítéséhez az alkalmazás típusának verzióját is módosítania kell a 1.0.0-ból a (z) [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Egy meglévő szolgáltatás új verziója lesz hozzáadva az alkalmazáshoz. Ilyenek például az alkalmazás kódjának módosítása és az alkalmazás típusú verzió és a név frissítése. Ehhez a frissítéshez a következőhöz hasonló frissítést UserApp.Parameters.js:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-application-resources"></a>Alkalmazás-erőforrások törlése

A Resource Manager alkalmazás-erőforrás modelljével telepített alkalmazás törlése:

1. Az alkalmazás erőforrás-AZONOSÍTÓjának beszerzéséhez használja a [Get-AzResource](/powershell/module/az.resources/get-azresource) parancsmagot:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Az alkalmazás-erőforrások törléséhez használja a [Remove-AzResource](/powershell/module/az.resources/remove-azresource) parancsmagot:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Következő lépések

Információk beolvasása az alkalmazás-erőforrás modelljéről:

* [Alkalmazás modellezése Service Fabric](service-fabric-application-model.md)
* [Service Fabric alkalmazás-és szolgáltatás-jegyzékfájlok](service-fabric-application-and-service-manifests.md)
* [Ajánlott eljárások: infrastruktúra mint kód](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Alkalmazások és szolgáltatások kezelése Azure-erőforrásként](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png