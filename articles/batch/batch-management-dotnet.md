---
title: Fiók erőforrásainak kezelése a Batch Management .NET-kódtár használatával
description: Azure Batch fiók-erőforrások létrehozása, törlése és módosítása a Batch Management .NET-könyvtárral.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896731"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Batch-fiókok és kvóták kezelése a Batch Management ügyféloldali kódtáraval a .NET-hez

A Batch-fiókok létrehozásával, törlésével, a kulcskezelő szolgáltatással és a kvóta-felderítéssel automatizálható [a Azure batch](/dotnet/api/overview/azure/batch) -alkalmazások terhelésének csökkentése.

- **Hozzon létre és töröljön batch-fiókokat** bármely régión belül. Ha például egy független szoftvergyártó (ISV) olyan szolgáltatást biztosít ügyfeleinek, amelyben a számlázáshoz külön batch-fiókot rendelnek hozzá, a fiókok létrehozására és törlésére vonatkozó képességeket adhat az ügyfél-portálhoz.
- A **fiók kulcsainak programozott módon történő beolvasása és újragenerálása** bármely batch-fiókhoz. Ez segíthet a biztonsági szabályzatok betartásában, amelyek kényszerítik az ismétlődő átütemezést vagy a fiók kulcsainak lejáratát. Ha több batch-fiókkal rendelkezik különböző Azure-régiókban, akkor ez a rollover-folyamat automatizálása növeli a megoldás hatékonyságát.
- **Tekintse meg a fiók kvótáit** , és végezze el a próba-és hibaüzenetet, és határozza meg, hogy melyik batch-fiókoknak milyen korlátai vannak. A fiók kvótáinak a feladatok elindítása, készletek létrehozása vagy számítási csomópontok hozzáadása előtt történő ellenőrzésével proaktív módon módosíthatja a számítási erőforrások létrehozásának helyét vagy időpontját. Megadhatja, hogy mely fiókoknál van szükség kvóta növelésére a fiókok további erőforrásainak lefoglalása előtt.
- **Más Azure-szolgáltatások funkcióinak kombinálása** teljes funkcionalitású felügyeleti élményhez a Batch Management .net, a [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)és a [Azure Resource Manager](../azure-resource-manager/management/overview.md) együttes használatával, ugyanabban az alkalmazásban. Ezekkel a szolgáltatásokkal és API-kkal a zökkenőmentes hitelesítési élmény, az erőforráscsoportok létrehozására és törlésére, valamint a fent ismertetett képességek teljes körű felügyeleti megoldásra való képességét biztosíthatja.

> [!NOTE]
> Ez a cikk a Batch-fiókok, a kulcsok és a kvóták programozott felügyeletére összpontosít, de [a Azure Portal használatával](batch-account-create-portal.md)számos ilyen tevékenységet is végrehajthat.

## <a name="create-and-delete-batch-accounts"></a>Batch-fiókok létrehozása és törlése

A Batch Management API egyik elsődleges funkciója a [Batch-fiókok](accounts.md) létrehozása és törlése egy Azure-régióban. Ehhez használja a [BatchManagementClient. Account. CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) és a [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync), illetve a szinkron társaikat.

A következő kódrészlet létrehoz egy fiókot, beolvassa az újonnan létrehozott fiókot a Batch szolgáltatásból, majd törli azt. Ebben a kódrészletben és a cikk többi részében a `batchManagementClient` [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient)teljes mértékben inicializált példánya.

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> A Batch Management .NET-függvénytárat és annak BatchManagementClient osztályt használó alkalmazásokhoz a szolgáltatás-rendszergazda vagy a felügyelni kívánt batch-fiókot birtokló előfizetés szükséges. További információ: Azure Active Directory szakasz és a [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) -kód minta.

## <a name="retrieve-and-regenerate-account-keys"></a>Fiókok kulcsainak beolvasása és újragenerálása

Szerezze be az elsődleges és a másodlagos fiókok kulcsait az előfizetésben található összes batch-fiókból a [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)használatával. Ezeket a kulcsokat a [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync)használatával lehet újragenerálni.

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Létrehozhat egy egyszerűsített munkafolyamatot a felügyeleti alkalmazásaihoz. Először szerezze be a [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)-mel felügyelni kívánt batch-fiókhoz tartozó fiók kulcsát. Ezt követően ezt a kulcsot használja a Batch .NET-függvénytár [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) osztályának inicializálásához, amelyet a rendszer a [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)inicializálásakor használ.

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Az Azure-előfizetés és a Batch-fiók kvótáinak megtekintése

Az Azure-előfizetések és az egyes Azure-szolgáltatások (például a Batch) minden alapértelmezett kvótával rendelkeznek, amelyek korlátozzák a bennük található egyes entitások számát. Az Azure-előfizetések alapértelmezett kvótái az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md)című részben olvashatók. A Batch szolgáltatás alapértelmezett kvótái esetében lásd: [kvóták és korlátozások a Azure batch szolgáltatáshoz](batch-quota-limit.md). A Batch Management .NET-kódtár használatával megtekintheti ezeket a kvótákat az alkalmazásaiban. Ez lehetővé teszi a kiosztási döntések meghozatalát a fiókok vagy számítási erőforrások, például a készletek és a számítási csomópontok hozzáadása előtt.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>A Batch-fiókokra vonatkozó kvóták Azure-előfizetésének keresése

Mielőtt létrehoz egy batch-fiókot egy régióban, megtekintheti az Azure-előfizetését, és megtekintheti, hogy hozzá tud-e adni egy fiókot az adott régióban.

Az alábbi kódrészletben először használjuk a **ListAsync** -t az előfizetésen belüli összes batch-fiók gyűjteményének beszerzéséhez. A gyűjtemény beszerzését követően megállapítjuk, hogy hány fiók van a célként megadott régióban. Ezután a **GetQuotasAsync** használatával szerezheti be a Batch-fiók kvótáját, és meghatározhatja, hogy hány fiók (ha van ilyen) hozható létre ebben a régióban.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

A fenti kódrészletben a `creds` **TokenCredentials** egy példánya. Ha meg szeretné tekinteni az objektum létrehozásának példáját, tekintse meg a [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) -kód mintát a githubon.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Batch-fiók beadása számítási erőforrások kvótái számára

A Batch-megoldás számítási erőforrásainak növelése előtt ellenőrizheti, hogy a lefoglalni kívánt erőforrások nem lépik túl a fiók kvótáit. Az alábbi kódrészletben kinyomtatjuk a nevű batch-fiók kvótájának adatait `mybatchaccount` . A saját alkalmazásban az ilyen információk segítségével meghatározhatja, hogy a fiók képes-e kezelni a létrehozandó további erőforrásokat.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Habár az Azure-előfizetések és-szolgáltatások esetében alapértelmezett kvóták vannak, a határértékek nagy része [a Azure Portal kvóta növelésének igénylésével](batch-quota-limit.md#increase-a-quota)állítható be.

## <a name="use-azure-ad-with-batch-management-net"></a>Az Azure AD használata a Batch Management .NET használatával

A Batch Management .NET-függvénytár egy Azure erőforrás-szolgáltatói ügyfél, és a [Azure Resource Manager](../azure-resource-manager/management/overview.md) együtt használható a fiók erőforrásainak programozott kezeléséhez. Az Azure AD-nek szüksége van az Azure erőforrás-szolgáltatói ügyfélen keresztül küldött kérések hitelesítésére, beleértve a Batch Management .NET könyvtárat és a Azure Resource Manager. További információ az Azure AD és a Batch Management .NET könyvtár használatáról: a [Batch-megoldások hitelesítésének Azure Active Directory használata](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Minta projekt a GitHubon

A Batch Management .NET működés közbeni megtekintéséhez tekintse meg a [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) -minta projektet a githubon. A AccountManagement minta alkalmazás a következő műveleteket mutatja be:

1. Biztonsági jogkivonat beszerzése az Azure AD-ből a [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md)használatával. Ha a felhasználó még nincs bejelentkezve, a rendszer kérni fogja az Azure-beli hitelesítő adataikat.
2. Az Azure AD-től kapott biztonsági jogkivonat segítségével hozzon létre egy [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) az Azure lekérdezéséhez a fiókhoz társított előfizetések listájának megjelenítéséhez. A felhasználó kiválaszthat egy előfizetést a listából, ha több előfizetést is tartalmaz.
3. A kiválasztott előfizetéshez társított hitelesítő adatok beolvasása.
4. Hozzon létre egy [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) objektumot a hitelesítő adatok használatával.
5. Hozzon létre egy erőforráscsoportot egy [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) objektum használatával.
6. [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) -objektum használata több batch-fiók műveleteinek elvégzéséhez:
   - Hozzon létre egy batch-fiókot az új erőforráscsoporthoz.
   - Szerezze be az újonnan létrehozott fiókot a Batch szolgáltatásból.
   - Nyomtassa ki az új fiókhoz tartozó fiók kulcsait.
   - Új elsődleges kulcs újralétrehozása a fiókhoz.
   - Nyomtassa ki a fiók kvótájának adatait.
   - Nyomtassa ki az előfizetés kvótájának adatait.
   - Az előfizetésen belüli összes fiók nyomtatása.
   - Törölje az újonnan létrehozott fiókot.
7. Törölje az erőforráscsoportot.

A minta alkalmazás sikeres futtatásához először regisztrálnia kell a Azure Portal Azure AD-Bérlővel, és engedélyt kell adnia a Azure Resource Manager API-nak. Kövesse a [Batch-felügyeleti megoldások Active Directory használatával végzett hitelesítésének](batch-aad-auth-management.md)lépéseit.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](quick-run-dotnet.md) vagy [Python](quick-run-python.md) segítségével való fejlesztésének alapjait. Ezek a rövid útmutató végigvezeti egy olyan minta alkalmazáson, amely a Batch szolgáltatás használatával több számítási csomóponton hajt végre munkaterhelést, és az Azure Storage-t használja a munkaterhelés-fájlok előkészítéséhez és lekéréséhez. git genny
