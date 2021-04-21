---
title: Rotációs oktatóanyag olyan erőforrásokhoz, amelyek egy hitelesítési hitelesítőadat-készletet tárolnak a Azure Key Vault
description: Ebből az oktatóanyagból megtudhatja, hogyan automatizálhatja a titkos kulcs rotációját olyan erőforrásoknál, amelyek egyetlen hitelesítési hitelesítő adatkészletet használnak.
services: key-vault
author: msmbaldwin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: e66be3b0e3ecae5caa1a76294cc8b8dc11a5f207
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748663"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Titkos kulcs rotációja automatizálható az egy hitelesítési hitelesítő adatokból álló készletet használó erőforrásoknál

Az Azure-szolgáltatásokban való hitelesítés legjobb módja [a](../general/authentication.md)felügyelt identitás használata, de vannak olyan forgatókönyvek, ahol ez nem lehetséges. Ilyen esetekben hozzáférési kulcsokat vagy titkos kulcsokat használ a rendszer. Rendszeres időközönként váltsa át a hozzáférési kulcsokat vagy titkos kulcsokat.

Ez az oktatóanyag bemutatja, hogyan automatizálhatja a titkos kulcsok rendszeres rotációját olyan adatbázisok és szolgáltatások esetén, amelyek egy hitelesítési hitelesítőadat-készletet használnak. Ez az oktatóanyag SQL Server a Azure Key Vault tárolt jelszavakat egy olyan függvény használatával, amelyet egy Azure Event Grid aktivál:


:::image type="content" source="../media/rotate-1.png" alt-text="A rotációs megoldás diagramja":::

1. A titkos kulcs lejárati dátuma előtt 30 nappal Key Vault a "lejárati" eseményt a Event Grid.
1. Event Grid ellenőrzi az esemény-előfizetéseket, és HTTP POST használatával hívja meg az eseményre előfizetett függvényalkalmazás-végpontot.
1. A függvényalkalmazás megkapja a titkos adatokat, létrehoz egy új véletlenszerű jelszót, és létrehoz egy új verziót a titkos adatokat az új jelszóval a Key Vault.
1. A függvényalkalmazás frissíti SQL Server új jelszóval.

> [!NOTE]
> Késés lehet a 3. és a 4. lépés között. Ez idő alatt a Key Vault titkos adatokat nem fogja tudni hitelesíteni a SQL Server. A lépések bármelyikének meghibásodása esetén a rendszer két Event Grid újra.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault
* SQL Server

Az alábbi üzembe helyezési hivatkozás használható, ha még nem Key Vault és SQL Server:

[![Az "Üzembe helyezés az Azure-ban" feliratú gombot bemutató kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Az **Erőforráscsoport alatt** válassza az **Új létrehozása lehetőséget.** Adjon nevet a csoportnak. Ebben az **oktatóanyagban az akvrotation** nevet használjuk.
1. Az **Sql-rendszergazdai bejelentkezés mezőbe írja** be az Sql-rendszergazda bejelentkezési nevét. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** elemre.

:::image type="content" source="../media/rotate-2.png" alt-text="Erőforráscsoport létrehozása":::

Most már lesz egy Key Vault és egy SQL Server példánya. Ezt a beállítást az Azure CLI-ban ellenőrizheti a következő parancs futtatásával:

```azurecli
az resource list -o table -g akvrotation
```

Az eredmény a következő kimenetnek fog kinézni:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Sql Server jelszóvétkvő függvény létrehozása és üzembe helyezése
> [!IMPORTANT]
> Az alábbi sablonhoz Key Vault, az SQL Servernek és az Azure-függvénynek ugyanabban az erőforráscsoportban kell lennie

Ezután hozzon létre egy függvényalkalmazást a többi szükséges összetevő mellett egy rendszer által felügyelt identitással, és telepítse az SQL Server jelszórotációs funkcióit

A függvényalkalmazáshoz a következő összetevőkre van szükség:
- Egy Azure App Service terv
- Egy sql-jelszóvoldást futtató függvényalkalmazás eseményindítóval és HTTP-eseményindítóval 
- A függvényalkalmazás eseményindító-kezeléséhez szükséges tárfiók
- Hozzáférési szabályzat a függvényalkalmazás identitásához a titkos kulcsok eléréséhez a Key Vault
- EventGrid-esemény-előfizetés **a SecretNearExpiry eseményhez**

1. Válassza az Azure-sablon üzembe helyezésére mutató hivatkozást: 

   [![Az "Üzembe helyezés az Azure-ban" feliratú gombot bemutató kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Az **Erőforráscsoport listában** válassza **az akvrotation lehetőséget.**
1. Az **Sql Server name (Sql Server neve)** mezőbe írja be az sql server name with password to rotate (Elforgatni kívánt jelszóval) sql server name (Sql Server neve) nevet
1. A név **Key Vault írja** be a kulcstartó nevét
1. A **Függvényalkalmazás neve mezőbe írja** be a függvényalkalmazás nevét
1. A Titkos **név mezőbe írja** be a jelszót tároló titkos adatokat
1. Az **Adattár URL-címe mezőbe** írja be a függvénykódot GitHub-hely ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

:::image type="content" source="../media/rotate-3.png" alt-text="Válassza az Áttekintés+létrehozás lehetőséget":::
  

Az előző lépések befejezése után lesz egy tárfiókja, egy kiszolgálófarmja és egy függvényalkalmazása. Ezt a beállítást az Azure CLI-ban ellenőrizheti a következő parancs futtatásával:

```azurecli
az resource list -o table -g akvrotation
```

Az eredmény az alábbihoz hasonló lesz:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

A függvényalkalmazások létrehozásáról és a felügyelt identitások Key Vault-hozzáférésről való használatával kapcsolatos információkért lásd: Függvényalkalmazás létrehozása a Azure Portal-ból, Felügyelt identitás használata az [App Service-hez](../../azure-functions/functions-create-function-app-portal.md)és az [Azure Functions-hoz,](../../app-service/overview-managed-identity.md)valamint Egy Key Vault-hozzáférési szabályzat hozzárendelése a Azure Portal [használatával.](../general/assign-access-policy-portal.md)

### <a name="rotation-function"></a>Rotációs függvény
Az előző lépésben üzembe helyezett függvény egy esemény használatával aktiválja a titkos adatok rotációját a Key Vault sql-adatbázis frissítésével. 

#### <a name="function-trigger-event"></a>Függvény eseményindítója

Ez a függvény beolvassa az eseményadatokat, és futtatja a rotációs logikát:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Titkos rotációs logika
Ez a rotációs módszer beolvassa az adatbázis adatait a titkos adatokat, létrehozza a titkos adat egy új verzióját, és frissíti az adatbázist az új titkos okkal:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
A teljes kódot megtalálja a [GitHubon.](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp)

## <a name="add-the-secret-to-key-vault"></a>Adja hozzá a titkos Key Vault
Állítsa be úgy a hozzáférési szabályzatot, hogy titkos *kulcsok kezelésére* vonatkozó engedélyeket adjon a felhasználóknak:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Hozzon létre egy új titkos adatokat olyan címkékkel, amelyek tartalmazzák SQL Server erőforrás-azonosítót, a SQL Server bejelentkezési nevet és a titkos kód érvényességi időszakát napokban. Adja meg a titkos kulcs nevét, az SQL-adatbázis kezdeti jelszavát (a példánkban "Simple123"), és adjon meg egy holnapra beállított lejárati dátumot.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

A titkos kulcs rövid lejárati dátummal való létrehozása 15 percen belül közzétesz egy eseményt, amely aktiválja a függvényt a titkos kulcs `SecretNearExpiry` váltogatása érdekében.

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

Annak ellenőrzéséhez, hogy a titkos kulcsok le **vannak-e** forgatva, a Titkos kulcsok  >  **Key Vault:**

:::image type="content" source="../media/rotate-8.png" alt-text="A titkos kulcsok elérésének Key Vault > képernyőképe.":::

Nyissa meg **az sqlPassword titkos** gombra, és tekintse meg az eredeti és a forgatt verziókat:

:::image type="content" source="../media/rotate-9.png" alt-text="Ugrás a titkos kulcsokra":::

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Az SQL-hitelesítő adatok ellenőrzéséhez hozzon létre egy webalkalmazást. Ez a webalkalmazás le fogja szerezni a titkos adatokat a Key Vault, kinyeri az SQL-adatbázis adatait és hitelesítő adatait a titkos adatból, és teszteli a kapcsolatot a SQL Server.

A webalkalmazáshoz a következő összetevőkre van szükség:
- Webalkalmazás rendszer által felügyelt identitással
- Hozzáférési szabályzat a titkos kulcsok eléréséhez Key Vault webalkalmazás felügyelt identitásán keresztül

1. Válassza az Azure-sablon üzembe helyezésére mutató hivatkozást: 

   [![Az "Üzembe helyezés az Azure-ban" feliratú gombot bemutató kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Válassza ki **az akvrotation** erőforráscsoportot.
1. Az **Sql Server name (Sql Server neve)** mezőbe írja be az sql server name with password to rotate (Elforgatni kívánt jelszóval) sql server name (Sql Server neve) nevet
1. A név **Key Vault írja** be a kulcstartó nevét
1. A Titkos **név mezőbe írja** be a jelszót tároló titkos adatokat
1. Az **Adattár URL-címe mezőbe írja** be a webalkalmazás-kód GitHub-helyét ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.


### <a name="open-the-web-app"></a>A webalkalmazás megnyitása

Ugrás az üzembe helyezett alkalmazás URL-címére:
 
'https://akvrotation-app.azurewebsites.net/'

Amikor az alkalmazás megnyílik a böngészőben, látni fogja **a** **Generált** titkos kulcs értékét és az Adatbázishoz csatlakoztatott true *értéket.*

## <a name="learn-more"></a>Tudjon meg többet

- Oktatóanyag: [Erőforrások rotációja két hitelesítőadat-készletből](tutorial-rotation-dual.md)
- Áttekintés: [Monitorozási Key Vault monitorozása Azure Event Grid](../general/event-grid-overview.md)
- How to: Receive email when a Key Vault secret changes (Hogyan lehet: [E-mail fogadása a Key Vault titkos kulcsának módosulás esetén)](../general/event-grid-logicapps.md)
- [Azure Event Grid eseménysémát a Azure Key Vault](../../event-grid/event-schema-key-vault.md)
