---
title: Rotációs oktatóanyag a Azure Key Vault tárolt hitelesítési hitelesítő adatokkal rendelkező erőforrásokhoz
description: Ebből az oktatóanyagból megtudhatja, hogyan automatizálható a titkos kód elforgatása olyan erőforrásokhoz, amelyek a hitelesítő adatok egy készletét használják.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: 526c3d2d85a3f2877f82b3b764f395c51f7c05c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99805230"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>A titkos kód elforgatásának automatizálása a hitelesítő adatok egy készletét használó erőforrásokhoz

Az Azure-szolgáltatásokhoz való hitelesítés legjobb módja egy [felügyelt identitás](../general/authentication.md)használata, de vannak olyan helyzetek, amikor ez nem lehetséges. Ezekben az esetekben a rendszer hozzáférési kulcsokat vagy titkos kódokat használ. Rendszeresen forgatni kell a hozzáférési kulcsokat vagy a titkokat.

Ez az oktatóanyag bemutatja, hogyan automatizálható a titkok rendszeres elforgatása olyan adatbázisokhoz és szolgáltatásokhoz, amelyek a hitelesítő adatok egy készletét használják. Pontosabban, ez az oktatóanyag a Azure Key Vaultokban tárolt jelszavakat SQL Server Azure Event Grid értesítés által aktivált függvény használatával forgatja el:


:::image type="content" source="../media/rotate-1.png" alt-text="Rotációs megoldás diagramja":::

1. Harminc nappal a titok lejárati dátuma előtt Key Vault közzéteszi a "közeljövőben" eseményt Event Grid.
1. Event Grid ellenőrzi az esemény-előfizetéseket, és HTTP POST használatával hívja meg az eseményre előfizetett Function app-végpontot.
1. A Function alkalmazás fogadja a titkos adatokat, létrehoz egy új véletlenszerű jelszót, és új verziót hoz létre a titkos kulcshoz a Key Vault új jelszavával.
1. A Function alkalmazás frissíti SQL Server az új jelszóval.

> [!NOTE]
> A 3. és a 4. lépések között lehet késés. Ebben az időszakban a Key Vault titkos kulcsa nem fog tudni hitelesíteni SQL Server. A lépések bármelyikének meghibásodása esetén a Event Grid két órán keresztül próbálkozik újra.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* SQL Server

Az alábbi telepítési hivatkozás használható, ha nincs meglévő Key Vault és SQL Server:

[![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget. Adjon nevet a csoportnak, az oktatóanyagban **akvrotation** használunk.
1. Az **SQL**-rendszergazdai bejelentkezés területen írja be az SQL-rendszergazda bejelentkezési nevét. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** elemre.

:::image type="content" source="../media/rotate-2.png" alt-text="Erőforráscsoport létrehozása":::

Most már rendelkezik egy Key Vault és egy SQL Server-példánnyal. A telepítőt az Azure CLI-ben ellenőrizheti a következő parancs futtatásával:

```azurecli
az resource list -o table -g akvrotation
```

Az eredmény a következő kimenettel fog kinézni:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>SQL Server-jelszó elforgatási funkciójának létrehozása és üzembe helyezése
> [!IMPORTANT]
> Az alábbi sablonhoz Key Vault, az SQL Server és az Azure függvénynek ugyanabban az erőforráscsoporthoz kell esnie

Ezután hozzon létre egy, a rendszer által felügyelt identitású Function alkalmazást a többi szükséges összetevőn kívül, és telepítse az SQL Server jelszó-elforgatási funkcióit.

A Function alkalmazáshoz a következő összetevők szükségesek:
- Egy Azure App Service terv
- SQL-jelszó-elforgatási függvényekkel rendelkező függvényalkalmazás eseményvezérelt eseményindítóval és http-eseményindítóval 
- A Function app trigger felügyeletéhez szükséges Storage-fiók
- Hozzáférési szabályzat függvényalkalmazás identitáshoz a titkos kódok eléréséhez Key Vault
- EventGrid esemény-előfizetés a **SecretNearExpiry** eseményhez

1. Válassza ki az Azure-sablon központi telepítési hivatkozását: 

   [![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Az **erőforráscsoport** listában válassza a **akvrotation** lehetőséget.
1. Az **SQL-kiszolgáló neve** mezőbe írja be az SQL Server nevét az elforgatáshoz használt jelszóval.
1. A **Key Vault neve** mezőbe írja be a Key Vault nevét
1. A **Függvényalkalmazás neve** mezőbe írja be a Function alkalmazás nevét.
1. A **titok neve** mezőbe írja be a titkos kód nevét, ahová a rendszer a jelszót tárolja.
1. A tárház **URL-címében** írja be a következőt: függvény kód GitHub helye ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

:::image type="content" source="../media/rotate-3.png" alt-text="Válassza a felülvizsgálat + létrehozás lehetőséget":::
  

Az előző lépések elvégzése után egy Storage-fiókkal, egy kiszolgálófarmhoz és egy Function-alkalmazással fog rendelkezni. A telepítőt az Azure CLI-ben ellenőrizheti a következő parancs futtatásával:

```azurecli
az resource list -o table -g akvrotation
```

Az eredmény a következő kimenethez hasonlóan fog kinézni:

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

A Function alkalmazás létrehozásával és a felügyelt identitások Key Vault való elérésével kapcsolatos információkért tekintse meg a [Function-alkalmazás létrehozása a Azure Portal](../../azure-functions/functions-create-function-app-portal.md), a [felügyelt identitás használata a App Service és a Azure functions](../../app-service/overview-managed-identity.md)számára című témakört, és [rendeljen hozzá egy Key Vault hozzáférési szabályzatot a Azure Portal használatával](../general/assign-access-policy-portal.md).

### <a name="rotation-function"></a>Rotációs függvény
Az előző lépésben üzembe helyezett esemény egy olyan eseményt használ, amely a Key Vault és az SQL-adatbázis frissítésével elindítja a titkos kód elforgatását. 

#### <a name="function-trigger-event"></a>Függvény eseményindítójának eseménye

Ez a függvény beolvassa az esemény-adatokat, és futtatja a rotációs logikát:

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
Ez a rotációs módszer beolvassa az adatbázis adatait a titkos kulcsból, létrehozza a titkos kulcs új verzióját, és frissíti az adatbázist az új titokkal:

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
A teljes kód megtalálható a [githubon](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>A titok hozzáadása Key Vault
Állítsa be a hozzáférési szabályzatot, és adja meg a felhasználók számára a *titkos kulcsok kezelésére* vonatkozó engedélyeket:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Hozzon létre egy új titkot olyan címkékkel, amelyek tartalmazzák a SQL Server erőforrás-azonosítót, a SQL Server bejelentkezési nevét, valamint a titok érvényességi idejét napokban. Adja meg a titkos kulcs nevét, a kezdeti jelszót az SQL Database-ből (példánkban "Simple123"), és adja meg a holnapra beállított lejárati dátumot.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Egy rövid lejárati dátummal rendelkező titkos kulcs létrehozásához `SecretNearExpiry` 15 percen belül közzé kell tenni egy eseményt, amely viszont aktiválja a függvényt a titok elforgatásához.

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

A titkos kód elforgatásának ellenőrzéséhez nyissa meg a **Key Vault**  >  **Secrets**:

:::image type="content" source="../media/rotate-8.png" alt-text="A Key Vault > titkok elérését bemutató képernyőkép.":::

Nyissa meg a titkos **sqlPassword** , és tekintse meg az eredeti és az elforgatott verziót:

:::image type="content" source="../media/rotate-9.png" alt-text="Ugrás a titkokra":::

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Az SQL-alapú hitelesítő adatok ellenőrzéséhez hozzon létre egy webalkalmazást. Ennek a webalkalmazásnak a titka a Key Vault, kinyeri az SQL Database adatait és a hitelesítő adatokat a titkos kulcsból, és teszteli a SQL Server kapcsolatát.

A webalkalmazáshoz a következő összetevők szükségesek:
- Rendszer által felügyelt identitással rendelkező webalkalmazás
- Hozzáférési szabályzat a titkok eléréséhez Key Vault webalkalmazás felügyelt identitásán keresztül

1. Válassza ki az Azure-sablon központi telepítési hivatkozását: 

   [![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Válassza ki a **akvrotation** erőforráscsoportot.
1. Az **SQL-kiszolgáló neve** mezőbe írja be az SQL Server nevét az elforgatáshoz használt jelszóval.
1. A **Key Vault neve** mezőbe írja be a Key Vault nevét
1. A **titok neve** mezőbe írja be a titkos kód nevét, ahol a jelszó tárolva van
1. A tárház **URL-címe** mezőbe írja be a következőt: webalkalmazás-kód GitHub helye ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.


### <a name="open-the-web-app"></a>A webalkalmazás megnyitása

Ugrás a telepített alkalmazás URL-címére:
 
'https://akvrotation-app.azurewebsites.net/'

Amikor az alkalmazás megnyílik a böngészőben, látni fogja a **generált titkos értéket** , és egy **adatbázishoz kapcsolódó** , *igaz* értéket.

## <a name="learn-more"></a>Tudjon meg többet

- Oktatóanyag: [az erőforrások rotációja két hitelesítő adatokkal](tutorial-rotation-dual.md)
- Áttekintés: [Key Vault figyelése Azure Event Grid](../general/event-grid-overview.md)
- Útmutató: [e-mailek fogadása a Key Vault titkos változásairól](../general/event-grid-logicapps.md)
- [Azure Key Vault Azure Event Gridi esemény sémája](../../event-grid/event-schema-key-vault.md)
