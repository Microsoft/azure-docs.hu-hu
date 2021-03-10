---
title: Tanúsítványalapú hitelesítés Azure Cosmos DB és Active Directory
description: Megtudhatja, hogyan konfigurálhat egy Azure AD-identitást tanúsítványalapú hitelesítésre a kulcsok Azure Cosmos DBból való eléréséhez.
author: voellm
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 84cbc681d0974e91561daf8918dff389226fa7aa
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553969"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Tanúsítványalapú hitelesítés Azure AD-identitáshoz Azure Cosmos DB-fiók kulcsainak eléréséhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A tanúsítványalapú hitelesítéssel az ügyfélalkalmazás ügyféltanúsítvánnyal hitelesíthető az Azure Active Directory (Azure AD) használatával. A tanúsítványalapú hitelesítést olyan gépen végezheti el, amelyen identitásra van szükség, például egy helyszíni gépen vagy egy Azure-beli virtuális gépen. Az alkalmazás elolvashatja Azure Cosmos DB kulcsait anélkül, hogy a kulcsokat közvetlenül az alkalmazásban kelljen volna beolvasnia. Ez a cikk bemutatja, hogyan hozhat létre egy minta Azure AD-alkalmazást, hogyan konfigurálhatja tanúsítványalapú hitelesítésre, bejelentkezhet az Azure-ba az új alkalmazás-identitás használatával, majd lekéri a kulcsokat az Azure Cosmos-fiókból. Ez a cikk a Azure PowerShell használatával állítja be az identitásokat, és egy C#-minta alkalmazást biztosít, amely hitelesíti és hozzáfér a kulcsokhoz az Azure Cosmos-fiókból.  

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a Azure PowerShell [legújabb verzióját](/powershell/azure/install-az-ps) .

* Ha nem rendelkezik Azure- [előfizetéssel](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="register-an-app-in-azure-ad"></a>Alkalmazás regisztrálása az Azure AD-ben

Ebben a lépésben egy minta webalkalmazást fog regisztrálni az Azure AD-fiókjában. Ezt az alkalmazást később a rendszer a Azure Cosmos DB-fiók kulcsainak olvasásához használja. Egy alkalmazás regisztrálásához kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg az Azure **Active Directory** panelt, lépjen a **Alkalmazásregisztrációk** panelre, és válassza az **új regisztráció** lehetőséget. 

   :::image type="content" source="./media/certificate-based-authentication/new-app-registration.png" alt-text="Új alkalmazás regisztrálása Active Directory":::

1. Töltse ki az **alkalmazás regisztrálása** űrlapot a következő részletekkel:  

   * **Név** – adja meg az alkalmazás nevét, amely lehet bármilyen név, például "PéldaAlkalmazás".
   * **Támogatott fióktípus** – **csak ebben a szervezeti könyvtárban** válassza a fiókok lehetőséget, hogy az aktuális könyvtárban lévő erőforrások hozzáférjenek ehhez az alkalmazáshoz. 
   * **Átirányítási URL-cím** – válassza a **web** típusú alkalmazást, és adjon meg egy URL-címet, ahol az alkalmazás üzemeltetve van, bármilyen URL-cím lehet. Ebben a példában egy teszt URL-címet is megadhat, például akkor `https://sampleApp.com` is, ha az alkalmazás nem létezik.

   :::image type="content" source="./media/certificate-based-authentication/register-sample-web-app.png" alt-text="Minta webalkalmazás regisztrálása":::

1. Az űrlap kitöltése után válassza a **regisztráció** lehetőséget.

1. Ha az alkalmazás regisztrálva van, jegyezze fel az **alkalmazás (ügyfél) azonosítóját** és az **objektumazonosítót**, majd a következő lépésekben fogja használni ezeket az adatokat. 

   :::image type="content" source="./media/certificate-based-authentication/get-app-object-ids.png" alt-text="Az alkalmazás és az objektum azonosítóinak beolvasása":::

## <a name="install-the-azuread-module"></a>A AzureAD modul telepítése

Ebben a lépésben telepíteni fogja az Azure AD PowerShell-modult. Ez a modul az előző lépésben regisztrált alkalmazás AZONOSÍTÓjának lekéréséhez és egy önaláírt tanúsítvány az alkalmazáshoz való hozzárendeléséhez szükséges. 

1. Windows PowerShell integrált parancsprogram-kezelési környezet megnyitása rendszergazdai jogosultságokkal. Ha még nem tette meg, telepítse az az PowerShell-modult, és kapcsolódjon az előfizetéséhez. Ha több előfizetéssel rendelkezik, beállíthatja az aktuális előfizetés környezetét az alábbi parancsokban látható módon:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. A [AzureAD](/powershell/module/azuread/) modul telepítése és importálása

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Jelentkezzen be az Azure AD-be

Jelentkezzen be az Azure AD-ba, ahol regisztrálta az alkalmazást. A Connect-AzureAD parancs használatával jelentkezzen be a fiókjába, és adja meg az Azure-fiókja hitelesítő adatait az előugró ablakban. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Nyissa meg a Windows PowerShell integrált parancsprogram-kezelési környezet egy másik példányát, és futtassa a következő parancsokat egy önaláírt tanúsítvány létrehozásához és a tanúsítványhoz tartozó kulcs beolvasásához:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Tanúsítvány alapú hitelesítő adat létrehozása 

Ezután futtassa a következő parancsokat az alkalmazás objektum-AZONOSÍTÓjának lekéréséhez és a tanúsítványalapú hitelesítő adatok létrehozásához. Ebben a példában a tanúsítványt egy év után kell lejárni, beállíthatja a szükséges befejezési dátumra.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

A fenti parancs az alábbi képernyőképhez hasonló kimenetet eredményez:

:::image type="content" source="./media/certificate-based-authentication/certificate-based-credential-output.png" alt-text="Tanúsítványalapú hitelesítő adatok létrehozási kimenete":::

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Az Azure Cosmos-fiók konfigurálása az új identitás használatára

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **hozzáférés-vezérlés (iam) panelt** .

1. Válassza a **Hozzáadás** és a **szerepkör-hozzárendelés hozzáadása** lehetőséget. Adja hozzá az előző lépésben a **közreműködő** szerepkörrel létrehozott PéldaAlkalmazás az alábbi képernyőképen látható módon:

   :::image type="content" source="./media/certificate-based-authentication/configure-cosmos-account-with-identify.png" alt-text="Az Azure Cosmos-fiók konfigurálása az új identitás használatára":::

1. Az űrlap kitöltése után válassza a **Mentés** lehetőséget.

## <a name="register-your-certificate-with-azure-ad"></a>Tanúsítvány regisztrálása az Azure AD-ben

A tanúsítvány alapú hitelesítő adatokat társíthatja az Azure AD-beli ügyfélalkalmazás az Azure Portalból. A hitelesítő adatok hozzárendeléséhez fel kell töltenie a tanúsítványfájl a következő lépésekkel:

Az ügyfélalkalmazás Azure-alkalmazásának regisztrációja:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg az Azure **Active Directory** panelt, nyissa meg a **Alkalmazásregisztrációk** ablaktáblát, és nyissa meg az előző lépésben létrehozott minta alkalmazást. 

1. Válassza a **tanúsítványok & a titkok** lehetőséget, majd **töltse fel a tanúsítványt**. Keresse meg az előző lépésben létrehozott tanúsítványfájl-fájlt a feltöltéshez.

1. Válassza a **Hozzáadás** lehetőséget. A tanúsítvány feltöltése után a rendszer megjeleníti az ujjlenyomatot, a kezdési dátumot és a lejárati értékeket.

## <a name="access-the-keys-from-powershell"></a>A kulcsok elérése a PowerShellből

Ebben a lépésben be kell jelentkeznie az Azure-ba az alkalmazás és a létrehozott tanúsítvány használatával, és elérheti az Azure Cosmos-fiókja kulcsait. 

1. Először törölje a fiókba való bejelentkezéshez használt Azure-fiók hitelesítő adatait. A hitelesítő adatok a következő paranccsal törölhetők:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Ezt követően ellenőrizze, hogy be tud-e jelentkezni Azure Portal az alkalmazás hitelesítő adataival, és hozzáfér a Azure Cosmos DB kulcsokhoz:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Az előző parancs megjeleníti az Azure Cosmos-fiók elsődleges és másodlagos elsődleges kulcsait. Megtekintheti az Azure Cosmos-fiók tevékenységi naplóját annak ellenőrzéséhez, hogy a kulcsok beolvasása kérelem sikeres volt-e, és hogy az eseményt a "PéldaAlkalmazás" alkalmazás kezdeményezte-e.

:::image type="content" source="./media/certificate-based-authentication/activity-log-validate-results.png" alt-text="A kulcsok beolvasása hívás ellenőrzése az Azure AD-ben":::

## <a name="access-the-keys-from-a-c-application"></a>A kulcsok elérése C#-alkalmazásból 

Ezt a forgatókönyvet úgy is ellenőrizheti, hogy C#-alkalmazásból fér hozzá a kulcsokhoz. A következő C# konzolos alkalmazás, amely Azure Cosmos DB kulcsokhoz fér hozzá a Active Directory regisztrált alkalmazás használatával. A kód futtatása előtt frissítse a tenantId, a clientID, a certName, az erőforráscsoport nevét, az előfizetés AZONOSÍTÓját, az Azure Cosmos-fiók nevét. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Ez a parancsfájl az elsődleges és másodlagos elsődleges kulcsokat az alábbi képernyőképen mutatja:

:::image type="content" source="./media/certificate-based-authentication/csharp-application-output.png" alt-text="csharp-alkalmazás kimenete":::

Az előző szakaszhoz hasonlóan megtekintheti az Azure Cosmos-fiókhoz tartozó műveletnapló adatait annak ellenőrzéséhez, hogy a "PéldaAlkalmazás" alkalmazás kezdeményezte-e a kulcsok kérése eseményt. 


## <a name="next-steps"></a>Következő lépések

* [Azure Cosmos-kulcsok védelme az Azure Key Vaulttal](access-secrets-from-keyvault.md)

* [Azure Cosmos DB biztonsági alapterve](security-baseline.md)