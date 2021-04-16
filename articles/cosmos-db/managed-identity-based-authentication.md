---
title: Hozzáférés az Azure Cosmos DB adataihoz egy rendszer által hozzárendelt felügyelt identitással
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directory (Azure AD) rendszer által hozzárendelt felügyelt identitást (felügyeltszolgáltatás-identitást) a Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: e4a41d508d15c3d8f41cc727776f233cc56c0817
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480936"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Rendszer által hozzárendelt felügyelt identitások használata a Azure Cosmos DB eléréséhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ebben a cikkben egy *robusztus,* kulcsrotációtól független megoldást fog beállítani a kulcskulcsok felügyelt identitások Azure Cosmos DB [való eléréséhez.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) A cikkben használt példa a Azure Functions, de bármilyen szolgáltatást használhat, amely támogatja a felügyelt identitásokat. 

Megtudhatja, hogyan hozhat létre olyan függvényalkalmazást, amely anélkül Azure Cosmos DB adatokat, hogy bármilyen Azure Cosmos DB lenne. A függvényalkalmazás percenként fel fog ébreszteni, és feljegyz egy tengerhalhal-tároló aktuális hőmérsékletét. Az időzítő által aktivált függvényalkalmazás beállítását az Időzítő által aktivált függvény létrehozása az Azure-ban cikkben [olvashatja](../azure-functions/functions-create-scheduled-function.md) el.

A forgatókönyv leegyszerűsítése érdekében már konfigurálva van egy [Time To Live](./time-to-live.md) beállítás a régebbi hőmérsékleti dokumentumok tisztítására. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Rendszer által hozzárendelt felügyelt identitás hozzárendelése függvényalkalmazáshoz

Ebben a lépésben hozzárendel egy rendszer által hozzárendelt felügyelt identitást a függvényalkalmazáshoz.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg az **Azure-függvény panelt,** és nyissa meg a függvényalkalmazást. 

1. Nyissa meg **a Platform funkciói** Identitás  >  **lapját:** 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Képernyőkép a függvényalkalmazás platformfunkcióiról és identitási beállításairól.":::

1. Az **Identitás lapon** kapcsolja be a **rendszeridentitás** **állapotát, és** válassza a **Mentés lehetőséget.** Az **Identitás panelnek** a következőképpen kell kinéznie:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Képernyőkép a rendszeridentitás Állapotáról Be állapotra állítva.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Hozzáférés megadása az Azure Cosmos-fiókhoz

Ebben a lépésben hozzárendel egy szerepkört a függvényalkalmazás rendszer által hozzárendelt felügyelt identitásához. Azure Cosmos DB több beépített szerepköre is van, amelyek hozzárendelhetőek a felügyelt identitáshoz. Ehhez a megoldáshoz a következő két szerepkört fogja használni:

|Beépített szerepkör  |Leírás  |
|---------|---------|
|[DocumentDB-fiók közreműködője](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kezelheti a Azure Cosmos DB fiókokat. Lehetővé teszi az olvasási/írási kulcsok lekérését. |
|[Cosmos DB fiókolvasó szerepkör](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Olvashatja a Azure Cosmos DB adatait. Engedélyezi az olvasási kulcsok lekérését. |

> [!IMPORTANT]
> A szerepköralapú hozzáférés-vezérlés támogatása a Azure Cosmos DB a vezérlősík műveleteire vonatkozik. Az adatsíkműveleteket elsődleges kulcsok vagy erőforrás-jogkivonatok biztosítják. További tudnivalókért lásd az [adatokhoz való biztonságos hozzáférést.](secure-access-to-data.md)

> [!TIP] 
> Szerepkörök hozzárendelésekor csak a szükséges hozzáférést rendelje hozzá. Ha a szolgáltatás csak az adatok olvasását igényli, rendelje hozzá Cosmos DB fiókolvasó **szerepkört** a felügyelt identitáshoz. A legkevesebb jogosultsággal rendelkező hozzáférés fontosságáról a Kiemelt jogosultságú fiókok kisebb kitettsége cikkben [talál további információt.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Ebben a forgatókönyvben a függvényalkalmazás beolvassa azárium hőmérsékletét, majd visszaírja az adatokat egy másik Azure Cosmos DB. Mivel a függvényalkalmazásnak meg kell írnia az adatokat, hozzá kell rendelnie a **DocumentDB-fiók közreműködője szerepkört.** 

### <a name="assign-the-role-using-azure-portal"></a>Szerepkör hozzárendelése a Azure Portal

1. Jelentkezzen be a Azure Portal, és Azure Cosmos DB fiókjához. Nyissa meg **a Hozzáférés-vezérlés (IAM)** panelt, majd a **Szerepkör-hozzárendelések** lapot:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Képernyőkép a Hozzáférés-vezérlés panelről és a Szerepkör-hozzárendelések lapról.":::

1. Válassza a **+ Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

1. A **Szerepkör-hozzárendelés hozzáadása** panel jobb oldalon nyílik meg:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Képernyőkép a Szerepkör-hozzárendelés hozzáadása panelről.":::

   * **Szerepkör:** Válassza a **DocumentDB-fiók közreműködője lehetőséget**
   * **Hozzáférés hozzárendelése a következőhöz:** A Rendszer által hozzárendelt felügyelt identitás kiválasztása **alszakaszban** válassza a **Függvényalkalmazás lehetőséget.**
   * **Válassza** a lehetőséget: A panel az előfizetés összes olyan függvényalkalmazásával ki lesz töltve, amely felügyeltrendszer-identitással **rendelkezik.** Ebben az esetben válassza **aTankTemperatureService** függvényalkalmazást: 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Képernyőkép a példákkal kitöltve a Szerepkör-hozzárendelés hozzáadása panelről.":::

1. A függvényalkalmazás kiválasztása után válassza a **Mentés lehetőséget.**

### <a name="assign-the-role-using-azure-cli"></a>Szerepkör hozzárendelése az Azure CLI használatával

A szerepkör Azure CLI használatával való hozzárendeléshez nyissa meg a Azure Cloud Shell és futtassa a következő parancsokat:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programozott hozzáférés a Azure Cosmos DB kulcsokhoz

Most már van egy függvényalkalmazásunk, amely a **DocumentDB-fiók** közreműködője szerepkörrel rendelkező, rendszer által hozzárendelt felügyelt identitással rendelkezik a Azure Cosmos DB engedélyekben. A következő függvényalkalmazás-kód le fogja szerezni a Azure Cosmos DB kulcsokat, létrehoz egy CosmosClient objektumot, le fogja kapni azárium hőmérsékletét, majd menti ezt a Azure Cosmos DB.

Ez a példa a [Kulcsok listája API-t használja](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) a fiókkulcsok Azure Cosmos DB eléréséhez.

> [!IMPORTANT] 
> Ha hozzá szeretné rendelni a [Cosmos DB-olvasó](#grant-access-to-your-azure-cosmos-account) szerepkört, a Csak olvasható kulcsok listása [API-t kell használnia.](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys) Ez csak a csak olvasható kulcsokat tölti fel.

A Kulcsok listása API visszaadja a `DatabaseAccountListKeysResult` objektumot. Ez a típus nincs definiálva a C#-kódtárakban. Az alábbi kód ennek az osztálynak az implementációját mutatja be:  

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

A példa egy "TemperatureRecord" nevű egyszerű dokumentumot is használ, amely a következőképpen van definiálva:

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

A [Microsoft.Azure.Services.AppAuthentication kódtár](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) használatával fogja lekért rendszer által hozzárendelt felügyelt identitás jogkivonatát. A jogkivonat lekért további módjaiért és a kódtár további információiért tekintse meg a szolgáltatások között való `Microsoft.Azure.Service.AppAuthentication` [hitelesítéssel kapcsolatos cikket.](/dotnet/api/overview/azure/service-to-service-authentication)


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

Most már készen áll a [függvényalkalmazás üzembe helyezésére.](../azure-functions/create-first-function-vs-code-csharp.md)

## <a name="next-steps"></a>Következő lépések

* [Tanúsítványalapú hitelesítés Azure Cosmos DB és Azure Active Directory](certificate-based-authentication.md)
* [Az Azure Cosmos DB kulcsok biztonságossá Azure Key Vault](access-secrets-from-keyvault.md)
* [Biztonsági alapkonfiguráció a Azure Cosmos DB](security-baseline.md)
