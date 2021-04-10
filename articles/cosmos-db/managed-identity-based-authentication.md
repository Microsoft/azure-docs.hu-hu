---
title: Hozzáférés az Azure Cosmos DB adataihoz egy rendszer által hozzárendelt felügyelt identitással
description: Megtudhatja, hogyan konfigurálhat egy Azure Active Directory (Azure AD) rendszerhez rendelt felügyelt identitást (felügyelt szolgáltatás identitása) a kulcsok Azure Cosmos DBból való eléréséhez.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d9845fad8c9013bd20499c45a8d1714e30e9dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98927405"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>A rendszer által hozzárendelt felügyelt identitások használata Azure Cosmos DB-adat eléréséhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ebben a cikkben egy *robusztus, kulcsfontosságú rotációs agnosztikus* megoldást állít be, amely a [felügyelt identitások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)használatával fér hozzá Azure Cosmos db kulcsokhoz. A cikkben szereplő példa Azure Functionst használ, de használhat bármely olyan szolgáltatást, amely támogatja a felügyelt identitásokat. 

Megtudhatja, hogyan hozhat létre olyan Function-alkalmazást, amely a Azure Cosmos DB kulcsok másolása nélkül fér hozzá Azure Cosmos DB az adathoz. A Function alkalmazás percenként felébred, és rögzíti egy akvárium Fish Tank aktuális hőmérsékletét. Ha meg szeretné tudni, hogyan állíthat be időzítő által aktivált Function alkalmazást, tekintse meg az [időzítő által aktivált függvény létrehozása az Azure-ban](../azure-functions/functions-create-scheduled-function.md) című cikket.

A forgatókönyv leegyszerűsítése érdekében az [élő beállítások élettartama](./time-to-live.md) már konfigurálva van a régebbi hőmérsékletű dokumentumok tisztítására. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Rendszerhez rendelt felügyelt identitás hozzárendelése egy Function-alkalmazáshoz

Ebben a lépésben hozzárendel egy rendszerhez rendelt felügyelt identitást a Function alkalmazáshoz.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg az **Azure-függvény** ablaktáblát, és lépjen a Function alkalmazáshoz. 

1. Nyissa meg a **platform szolgáltatások**  >  **identitás** fület: 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="A Function alkalmazás platform-funkcióit és identitási beállításait bemutató képernyőkép.":::

1. Az **identitás** **lapon kapcsolja be** a rendszeridentitás **állapotát** , majd válassza a **Mentés** lehetőséget. Az **identitás** ablaktáblájának a következőképpen kell kinéznie:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Képernyőfelvétel: a rendszeridentitás állapotának beállítása a következőre:.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Hozzáférés biztosítása az Azure Cosmos-fiókhoz

Ebben a lépésben hozzárendel egy szerepkört a Function alkalmazás rendszerhez rendelt felügyelt identitásához. Azure Cosmos DB több beépített szerepkörrel rendelkezik, amelyeket hozzárendelhet a felügyelt identitáshoz. Ebben a megoldásban a következő két szerepkört fogja használni:

|Beépített szerepkör  |Leírás  |
|---------|---------|
|[DocumentDB-fiók közreműködői](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Felügyelheti Azure Cosmos DB fiókokat. Olvasási/írási kulcsok lekérését teszi lehetővé. |
|[Cosmos DB fiók-olvasó szerepkör](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB fiókadatok olvasása. Lehetővé teszi az olvasási kulcsok lekérését. |

> [!IMPORTANT]
> A Azure Cosmos DB szerepköralapú hozzáférés-vezérlésének támogatása csak a vezérlési sík műveleteire vonatkozik. Az adatsíkok műveletei az elsődleges kulcsok vagy az erőforrás-tokenek révén biztonságosak. További információ: [biztonságos hozzáférés az adatkezeléshez](secure-access-to-data.md) cikk.

> [!TIP] 
> Szerepkörök hozzárendeléséhez csak a szükséges hozzáférést rendelje hozzá. Ha a szolgáltatás csak az adatok olvasását igényli, akkor rendelje hozzá a **Cosmos db fiók-olvasó** szerepkört a felügyelt identitáshoz. A minimális jogosultsági szintű hozzáférés fontosságával kapcsolatos további információkért tekintse meg a [privilegizált fiókok alacsonyabb kitettségét](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) ismertető cikket.

Ebben az esetben a Function alkalmazás beolvassa az akvárium hőmérsékletét, majd visszaírja azokat Azure Cosmos DB tárolóba. Mivel a Function alkalmazásnak meg kell írnia az adatfájlokat, hozzá kell rendelnie a **DocumentDB-fiók közreműködői** szerepkört. 

### <a name="assign-the-role-using-azure-portal"></a>Szerepkör kiosztása Azure Portal használatával

1. Jelentkezzen be a Azure Portalba, és lépjen a Azure Cosmos DB-fiókjához. Nyissa meg a **hozzáférés-vezérlés (iam)** ablaktáblát, majd a **szerepkör-hozzárendelések** lapot:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Képernyőfelvétel: a hozzáférés-vezérlés ablaktábla és a szerepkör-hozzárendelések lap.":::

1. Válassza a **+ Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

1. Megnyílik a **szerepkör-hozzárendelés hozzáadása** panel a jobb oldalon:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="A szerepkör-hozzárendelés hozzáadása panelt ábrázoló képernyőfelvétel.":::

   * **Szerepkör**: válassza ki a **DocumentDB fiók közreműködőjét**
   * **Hozzáférés hozzárendelése**: a **rendszerhez rendelt felügyelt identitás kiválasztása** szakaszban válassza a **függvényalkalmazás** lehetőséget.
   * **Select (kiválasztás**): a panel az előfizetésben található összes Function-alkalmazással lesz feltöltve, és **felügyelt rendszeridentitással** rendelkezik. Ebben az esetben válassza ki a **FishTankTemperatureService** Function alkalmazást: 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="A szerepkör-hozzárendelés hozzáadása ablaktábla példákkal való feltöltését bemutató képernyőkép.":::

1. Miután kiválasztotta a Function alkalmazást, válassza a **Mentés** lehetőséget.

### <a name="assign-the-role-using-azure-cli"></a>Szerepkör kiosztása az Azure CLI használatával

Ha az Azure CLI használatával szeretné hozzárendelni a szerepkört, nyissa meg a Azure Cloud Shell, és futtassa a következő parancsokat:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programozottan férhet hozzá a Azure Cosmos DB kulcsaihoz

Most már van egy olyan Function-alkalmazás, amely rendelkezik egy rendszerhez rendelt felügyelt identitással a **DocumentDB fiók közreműködői** szerepkörével a Azure Cosmos db engedélyekben. A következő Function app Code beolvassa a Azure Cosmos DB kulcsokat, létrehoz egy CosmosClient objektumot, beolvassa az akvárium hőmérsékletét, majd elmenti ezt a Azure Cosmos DBba.

Ez a példa a [Keys API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) -t használja a Azure Cosmos db-fiók kulcsainak eléréséhez.

> [!IMPORTANT] 
> Ha [a Cosmos db fiók-olvasó](#grant-access-to-your-azure-cosmos-account) szerepkört szeretné hozzárendelni, akkor a [csak olvasási KULCSokat tartalmazó API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys)-t kell használnia. Ez csak a csak olvasható kulcsokat fogja feltölteni.

A List Keys API az objektumot adja vissza `DatabaseAccountListKeysResult` . Ez a típus nincs definiálva a C#-tárakban. A következő kód az osztály implementációját mutatja be:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

A példa egy "TemperatureRecord" nevű egyszerű dokumentumot is használ, amely a következőképpen van meghatározva:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

A rendszer által hozzárendelt felügyelt identitási token beszerzéséhez a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) függvénytárat fogja használni. A jogkivonat lekérésének és a könyvtár további információinak megismeréséhez `Microsoft.Azure.Service.AppAuthentication` tekintse meg a [szolgáltatások közötti hitelesítéssel](/dotnet/api/overview/azure/service-to-service-authentication) foglalkozó cikket.


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Most már készen áll a [Function alkalmazás üzembe helyezésére](../azure-functions/create-first-function-vs-code-csharp.md).

## <a name="next-steps"></a>Következő lépések

* [Tanúsítványalapú hitelesítés Azure Cosmos DB és Azure Active Directory](certificate-based-authentication.md)
* [Azure Cosmos DB kulcsok védelme Azure Key Vault használatával](access-secrets-from-keyvault.md)
* [Azure Cosmos DB biztonsági alapterve](security-baseline.md)