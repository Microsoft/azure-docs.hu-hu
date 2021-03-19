---
title: Felügyelt identitás használata a Azure Stream Analytics-feladatok hitelesítéséhez Power BI kimenetre
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások a Azure Stream Analytics-feladatok hitelesítéséhez Power BI kimenetre.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 7c1ddbbbd8198cf769e89cfa824de370184a992c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589684"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Felügyelt identitás használata a Azure Stream Analytics-feladatok hitelesítéséhez Power BI

A Power BI kimenetének [felügyelt identitás-hitelesítése](../active-directory/managed-identities-azure-resources/overview.md) stream Analytics feladatok közvetlen elérését teszi lehetővé Power bi fiókjában lévő munkaterülethez. Ez a funkció lehetővé teszi Stream Analytics feladatok teljes körű automatizálását, mivel már nem szükséges, hogy a felhasználó interaktív módon jelentkezzen be a Power BIba a Azure Portal használatával. Emellett a hosszú ideig futó, Power BI írást végző feladatok már jobban támogatottak, mivel nem kell rendszeresen újraengedélyezni a feladatot.

Ez a cikk bemutatja, hogyan engedélyezheti a felügyelt identitást egy Stream Analytics-feladatokhoz tartozó Power BI kimenet (ek) számára a Azure Portalon és egy Azure Resource Manager-telepítésen keresztül.

## <a name="prerequisites"></a>Előfeltételek

A funkció használatához a következők szükségesek:

- Egy [Pro-licenccel](/power-bi/service-admin-purchasing-power-bi-pro)rendelkező Power bi-fiók.

- Egy frissített munkaterület a Power BI fiókjában. További részletekért tekintse [meg Power bi a funkció bejelentését](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) .

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Stream Analytics-feladatok létrehozása a Azure Portal használatával

1. Hozzon létre egy új Stream Analytics feladatot, vagy nyisson meg egy meglévő feladatot a Azure Portalban. A képernyő bal oldalán található menüsávban válassza a **configure (Konfigurálás**) alatt található **felügyelt identitás** elemet. Győződjön meg arról, hogy a "rendszerhez rendelt felügyelt identitás használata" beállítás ki van választva, majd kattintson a képernyő alján található **Save (Mentés** ) gombra.

   ![Stream Analytics felügyelt identitás konfigurálása](./media/common/stream-analytics-enable-managed-identity.png)

2. A kimenet konfigurálása előtt adja meg a Stream Analytics-feladatot a Power BI-munkaterülethez a jelen cikk [Power bi munkaterülethez való hozzáférésének megadása a stream Analyticsi feladatok számára](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) című szakaszának utasításait követve.

3. Navigáljon a stream Analytics-feladatok **kimenetek** szakaszába, válassza a **+ Hozzáadás** lehetőséget, majd válassza a **Power bi** lehetőséget. Ezután válassza az **Engedélyezés** gombot, és jelentkezzen be Power bi-fiókjával.

   ![Engedélyezés Power BI fiókkal](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Az engedélyezést követően a legördülő lista minden olyan munkaterülettel feltöltve lesz, amelyhez hozzáfér. Válassza ki azt a munkaterületet, amelyet az előző lépésben engedélyezett. Ezután válassza a **felügyelt identitás** "hitelesítési mód" lehetőséget. Végül kattintson a **Save (Mentés** ) gombra.

   ![Power BI kimenet konfigurálása felügyelt identitással](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Azure Resource Manager lehetővé teszi, hogy teljes mértékben automatizálja Stream Analytics feladatainak üzembe helyezését. A Resource Manager-sablonokat Azure PowerShell vagy az [Azure CLI](/cli/azure/)használatával is telepítheti. Az alábbi példák az Azure CLI-t használják.


1. A felügyelt identitással rendelkező **Microsoft. StreamAnalytics/streamingjobs-** erőforrások létrehozásához a Resource Manager-sablon Resource (erőforrás) szakaszában a következő tulajdonságot is használhatja:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Ez a tulajdonság azt jelzi, Azure Resource Manager a Stream Analytics feladatokhoz tartozó identitás létrehozása és kezelése. Az alábbi példa egy olyan Resource Manager-sablont mutat be, amely a felügyelt identitással rendelkező Stream Analytics feladatot helyez üzembe, valamint egy olyan Power BI kimeneti fogadót, amely felügyelt identitást használ:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Telepítse a fenti feladatot az erőforráscsoport **ExampleGroup** az alábbi Azure CLI-parancs használatával:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. A feladatot a létrehozás után a Azure Resource Manager használatával kérheti le a feladatok teljes definícióját.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    A fenti parancs a következőhöz hasonló választ ad vissza:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Ha azt tervezi, hogy Power BI REST API használatával hozzáadja a Stream Analytics feladatot a Power BI munkaterülethez, jegyezze fel a visszaadott "principalId" értéket.

3. Most, hogy létrehozta a feladatot, folytassa a jelen cikk [stream Analyticsi feladatok elérésének biztosítása a Power bi munkaterülethez](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) című szakaszát.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>A Stream Analytics-feladatok elérésének biztosítása a Power BI-munkaterülethez

Most, hogy létrehozta a Stream Analytics feladatot, hozzáférést biztosíthat egy Power BI-munkaterülethez. Miután megadta a feladathoz való hozzáférést, néhány percet is igénybe vehet, hogy az identitás propagálni lehessen.

### <a name="use-the-power-bi-ui"></a>A Power BI felhasználói felületének használata

   > [!Note]
   > Ahhoz, hogy a felhasználói felületen hozzá tudja adni a Stream Analytics feladatot a Power BI munkaterülethez, a Power BI felügyeleti portál **fejlesztői beállításainál** engedélyeznie kell a szolgáltatás egyszerű hozzáférését is. További részletekért tekintse meg az első [lépések a szolgáltatással](/power-bi/developer/embed-service-principal) című témakört.

1. Navigáljon a munkaterület hozzáférési beállításaihoz. További részletekért tekintse meg ezt a cikket: [hozzáférés biztosítása a munkaterülethez](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Írja be a Stream Analyticsi feladatainak nevét a szövegmezőbe, és válassza a **közreműködő** lehetőséget a hozzáférési szint mezőben.

3. Válassza a **Hozzáadás** lehetőséget, majd a panel bezárásához.

   ![Stream Analytics-feladatok hozzáadása Power BI munkaterülethez](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>A Power BI PowerShell-parancsmagok használata

1. Telepítse a Power BI `MicrosoftPowerBIMgmt` PowerShell-parancsmagokat.

   > [!Important]
   > Győződjön meg arról, hogy a parancsmagok 1.0.821 vagy újabb verzióját használja.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Jelentkezzen be Power BIba.

```powershell
Login-PowerBI
```

3. Adja hozzá a Stream Analytics feladatot közreműködőként a munkaterülethez.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Használja a Power BI REST API

A Stream Analytics feladatot felveheti közreműködőként a munkaterülethez a "felhasználói csoport hozzáadása" REST API közvetlen használatával. Az API teljes dokumentációja itt található: [csoportok – csoport hozzáadása felhasználó](/rest/api/power-bi/groups/addgroupuser).

**Kérésminta**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Kérelem törzse
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Felügyelt identitás eltávolítása

A Stream Analytics feladatokhoz létrehozott felügyelt identitást csak akkor törli a rendszer, ha a feladatot törlik. A felügyelt identitást nem lehet törölni a feladatok törlése nélkül. Ha már nem szeretné használni a felügyelt identitást, módosíthatja a kimenet hitelesítési módszerét. A felügyelt identitás továbbra is érvényben marad, amíg el nem törli a feladatot, és a rendszer akkor fogja használni, ha a felügyelt identitások hitelesítését újra használta.

## <a name="limitations"></a>Korlátozások
A szolgáltatás korlátai a következők:

- A klasszikus Power BI-munkaterületek nem támogatottak.

- Azure-fiókok Azure Active Directory nélkül.

- A több-bérlős hozzáférés nem támogatott. Az adott Stream Analytics feladathoz létrehozott egyszerű szolgáltatásnak ugyanabban a Azure Active Directory-bérlőben kell lennie, amelyben a feladatot létrehozták, és nem használható olyan erőforrással, amely egy másik Azure Active Directory bérlőben található.

- A [felhasználó által hozzárendelt identitás](../active-directory/managed-identities-azure-resources/overview.md) nem támogatott. Ez azt jelenti, hogy nem tudja beírni a saját egyszerű szolgáltatásnevet, amelyet a Stream Analytics feladataihoz kíván használni. Az egyszerű szolgáltatásnevet Azure Stream Analytics kell létrehozni.

## <a name="next-steps"></a>Következő lépések

* [Irányítópult-integráció Power BI Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [A Azure Stream Analytics kimenetének megismerése](./stream-analytics-define-outputs.md)
