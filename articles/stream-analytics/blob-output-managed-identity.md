---
title: A blob kimenetének hitelesítése felügyelt identitással Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások a Azure Stream Analytics-feladatok Azure Blob Storage-kimenetre való hitelesítéséhez.
author: kim-ale
ms.author: kimal
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 369348133f7395f5db5b5923bd438cec8e4ad733
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954378"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>A felügyelt identitás (előzetes verzió) használatával hitelesítheti a Azure Stream Analytics feladatot az Azure-ban Blob Storage

Az Azure Blob Storage-hoz való kimenet [felügyelt identitás-hitelesítése](../active-directory/managed-identities-azure-resources/overview.md) (előzetes verzió) lehetővé teszi, hogy a stream Analytics feladatok közvetlen hozzáférést biztosítanak egy Storage-fiókhoz a kapcsolati karakterlánc használata helyett. A fokozott biztonság mellett ez a funkció lehetővé teszi, hogy az Azure-ban egy Virtual Network (VNET) lévő Storage-fiókba írja az adatok.

Ez a cikk bemutatja, hogyan engedélyezheti a felügyelt identitást egy Stream Analytics-feladatokhoz tartozó blob-kimenet (ek) számára a Azure Portal és egy Azure Resource Manager központi telepítés használatával.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Hozza létre a Stream Analytics feladatot a Azure Portal használatával

1. Hozzon létre egy új Stream Analytics feladatot, vagy nyisson meg egy meglévő feladatot a Azure Portalban. A képernyő bal oldalán található menüsávban válassza a **configure (Konfigurálás**) alatt található **felügyelt identitás** elemet. Győződjön meg arról, hogy a "rendszer által hozzárendelt felügyelt identitás használata" lehetőség van kiválasztva, majd kattintson a képernyő alján található **Save (Mentés** ) gombra.

   ![Stream Analytics felügyelt identitás konfigurálása](./media/common/stream-analytics-enable-managed-identity.png)

2. Az Azure Blob Storage kimeneti tároló kimenet tulajdonságai ablakában válassza a hitelesítési mód legördülő listát, és válassza a **felügyelt identitás** lehetőséget. További információ a többi kimeneti tulajdonságról: a [Azure stream Analytics kimenetének megismerése](./stream-analytics-define-outputs.md). Amikor végzett, kattintson a **Mentés** gombra.

   ![Az Azure Blob Storage kimenetének konfigurálása](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Most, hogy létrehozta a feladatot, tekintse meg a jelen cikk a [stream Analytics feladatok elérésének biztosítása a Storage-fiókhoz](#give-the-stream-analytics-job-access-to-your-storage-account) című szakaszát.

## <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

A Azure Resource Manager használata lehetővé teszi, hogy teljes mértékben automatizálja Stream Analytics feladatainak üzembe helyezését. A Resource Manager-sablonokat Azure PowerShell vagy az [Azure CLI](/cli/azure/)használatával is telepítheti. Az alábbi példák az Azure CLI-t használják.


1. A felügyelt identitással rendelkező **Microsoft. StreamAnalytics/streamingjobs-** erőforrások létrehozásához a Resource Manager-sablon Resource (erőforrás) szakaszában a következő tulajdonságot is használhatja:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ez a tulajdonság azt jelzi, Azure Resource Manager a Stream Analytics feladatokhoz tartozó identitás létrehozása és kezelése. Az alábbi példa egy olyan Resource Manager-sablont mutat be, amely egy felügyelt identitással rendelkező Stream Analytics feladatot telepít, valamint egy olyan blob kimeneti fogadót, amely felügyelt identitást használ:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    A fenti feladatot az alábbi Azure CLI-parancs használatával telepítheti az erőforráscsoport **ExampleGroup** :

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. A feladatot a létrehozása után a Azure Resource Manager segítségével kérheti le a feladatok teljes definícióját.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    A fenti parancs a következőhöz hasonló választ ad vissza:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Jegyezze fel a **principalId** , amely a feladathoz tartozó felügyelt identitást azonosítja Azure Active Directoryban, és a következő lépésben fogja használni a stream Analytics-feladathoz való hozzáférést a Storage-fiókhoz.

3. Most, hogy létrehozta a feladatot, tekintse meg a jelen cikk a [stream Analytics feladatok elérésének biztosítása a Storage-fiókhoz](#give-the-stream-analytics-job-access-to-your-storage-account) című szakaszát.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>A Stream Analytics-feladatok elérésének biztosítása a Storage-fiókhoz

Két hozzáférési szint közül választhat, hogy megadja a Stream Analytics feladatot:

1. **Tároló szintű hozzáférés:** ez a beállítás hozzáférést biztosít egy adott meglévő tárolóhoz.
2. **Fiók szintű hozzáférés:** ez a beállítás általános hozzáférést biztosít a Storage-fiókhoz, beleértve az új tárolók létrehozását is.

Ha nincs szüksége a tárolók létrehozásához az Ön nevében, válassza a **tároló szintű hozzáférés** lehetőséget, mivel ez a beállítás megadja a minimálisan szükséges hozzáférési szintet a feladatnak. A Azure Portal és a parancssorban mindkét lehetőség alább látható.

### <a name="grant-access-via-the-azure-portal"></a>Hozzáférés biztosítása a Azure Portalon keresztül

#### <a name="container-level-access"></a>Tároló szintű hozzáférés

1. Navigáljon a tároló konfigurációs ablaktáblájához a Storage-fiókon belül.

2. Válassza ki a **Access Control (iam)** elemet a bal oldali oldalon.

3. A "szerepkör-hozzárendelés hozzáadása" szakaszban kattintson a **Hozzáadás** gombra.

4. A szerepkör-hozzárendelés ablaktáblán:

    1. A **szerepkör** beállítása a "Storage blob-adatközreműködői" elemre
    2. Győződjön meg arról, hogy a legördülő lista **elérésének kiosztása** "Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév" értékre van állítva.
    3. Írja be a Stream Analyticsi feladatok nevét a keresőmezőbe.
    4. Válassza ki a Stream Analytics feladatot, és kattintson a **Mentés** gombra.

   ![Tároló hozzáférésének engedélyezése](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Fiók szintű hozzáférés

1. Nyissa meg a tárfiókot.

2. Válassza ki a **Access Control (iam)** elemet a bal oldali oldalon.

3. A "szerepkör-hozzárendelés hozzáadása" szakaszban kattintson a **Hozzáadás** gombra.

4. A szerepkör-hozzárendelés ablaktáblán:

    1. A **szerepkör** beállítása a "Storage blob-adatközreműködői" elemre
    2. Győződjön meg arról, hogy a legördülő lista **elérésének kiosztása** "Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév" értékre van állítva.
    3. Írja be a Stream Analyticsi feladatok nevét a keresőmezőbe.
    4. Válassza ki a Stream Analytics feladatot, és kattintson a **Mentés** gombra.

   ![Fiókhoz való hozzáférés biztosítása](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Hozzáférés engedélyezése a parancssorból

#### <a name="container-level-access"></a>Tároló szintű hozzáférés

Ha hozzáférést szeretne biztosítani egy adott tárolóhoz, futtassa az alábbi parancsot az Azure CLI használatával:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Fiók szintű hozzáférés

Ha hozzáférést szeretne adni a teljes fiókhoz, futtassa az alábbi parancsot az Azure CLI használatával:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET-hozzáférés engedélyezése

A Storage-fiók **tűzfalának és virtuális hálózatának** konfigurálásakor engedélyezheti a hálózati forgalmat más megbízható Microsoft-szolgáltatásokból is. Ha a Stream Analytics felügyelt identitás használatával végzi a hitelesítést, igazolja, hogy a kérés megbízható szolgáltatásból származik. Az alábbi útmutatást követve engedélyezheti ezt a VNET hozzáférési kivételt.

1.    Navigáljon a Storage-fiók konfigurációs paneljén található "tűzfalak és virtuális hálózatok" panelre.
2.    Győződjön meg arról, hogy a "megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz" beállítás engedélyezve van.
3.    Ha engedélyezte, kattintson a **Mentés** gombra.

   ![VNET-hozzáférés engedélyezése](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>Felügyelt identitás eltávolítása

A Stream Analytics feladatokhoz létrehozott felügyelt identitást csak akkor törli a rendszer, ha a feladatot törlik. A felügyelt identitást nem lehet törölni a feladatok törlése nélkül. Ha már nem szeretné használni a felügyelt identitást, módosíthatja a kimenet hitelesítési módszerét. A felügyelt identitás továbbra is érvényben marad, amíg el nem törli a feladatot, és a rendszer akkor fogja használni, ha a felügyelt identitások hitelesítését újra használta.

## <a name="limitations"></a>Korlátozások
A szolgáltatás jelenlegi korlátai a következők:

1. Klasszikus Azure Storage-fiókok.

2. Azure-fiókok Azure Active Directory nélkül.

3. A több-bérlős hozzáférés nem támogatott. Az adott Stream Analytics feladathoz létrehozott egyszerű szolgáltatásnak ugyanabban a Azure Active Directory-bérlőben kell lennie, amelyben a feladatot létrehozták, és nem használható olyan erőforrással, amely egy másik Azure Active Directory bérlőben található.

4. A [felhasználó által hozzárendelt identitás](../active-directory/managed-identities-azure-resources/overview.md) nem támogatott. Ez azt jelenti, hogy a felhasználó nem tudja megadni a saját egyszerű szolgáltatásnevet, amelyet a Stream Analytics feladataihoz használhatnak. Az egyszerű szolgáltatásnevet Azure Stream Analytics kell létrehozni.

## <a name="next-steps"></a>Következő lépések

* [A Azure Stream Analytics kimenetének megismerése](./stream-analytics-define-outputs.md)
* [Egyéni blob kimeneti particionálás Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
