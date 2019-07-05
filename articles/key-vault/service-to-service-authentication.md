---
title: Szolgáltatások közötti hitelesítés az Azure Key Vaultba .NET használatával
description: A Microsoft.Azure.Services.AppAuthentication könyvtár segítségével hitelesíti az Azure Key Vaultba .NET használatával.
keywords: az Azure key-vault helyi hitelesítő adatok
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 79d4254de40ef787b30eb4f483c86383a928ee1f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566225"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Szolgáltatások közötti hitelesítés az Azure Key Vaultba .NET használatával

Hitelesítés az Azure Key Vaultba kell egy Azure Active Directory (AD) hitelesítő adatok, vagy egy közös titkos kulcsot, vagy egy tanúsítványt. 

Az ilyen hitelesítő adatok kezelése nehézkes lehet és csábító szeretné a hitelesítő adatok egy alkalmazásba fel őket a forrás- vagy konfigurációs fájlok.  A `Microsoft.Azure.Services.AppAuthentication` .NET-kódtár leegyszerűsíti a probléma. Helyi fejlesztés során hitelesítsék magukat a fejlesztői hitelesítő adatokat használ. A megoldás ezt követően telepíti az Azure-ba, amikor a tár automatikusan vált, amennyiben az alkalmazás hitelesítő adatait.    Helyi fejlesztés során fejlesztői hitelesítő adatok használatával használata biztonságosabb, mert nem kell létrehozni az Azure AD hitelesítő adatait, vagy fájlmegosztási hitelesítő adatokat a fejlesztők közötti.

A `Microsoft.Azure.Services.AppAuthentication` könyvtár kezeli a hitelesítés automatikusan, amely viszont lehetővé teszi, hogy a megoldás ahelyett, hogy a hitelesítő adatok összpontosíthat.  Helyi fejlesztés a Microsoft Visual Studio, az Azure parancssori felület vagy az Azure AD integrált hitelesítést támogatja. Amikor telepít egy Azure-erőforrás, amely támogatja a felügyelt identitás, automatikusan használja-e a szalagtár [felügyelt identitások az Azure-erőforrások](../active-directory/msi-overview.md). Nincs kódírásra vagy konfigurálásra módosításokra szükség. A kódtár emellett támogatja az Azure AD közvetlenül is felhasználható [ügyfél-hitelesítő adatok](../azure-resource-manager/resource-group-authenticate-service-principal.md) Ha egy felügyelt identitás nem érhető el, vagy ha a fejlesztői biztonsági környezetben nem lehet megállapítani a helyi fejlesztés során.

## <a name="using-the-library"></a>A kódtár használatával

A .NET-alkalmazásokban, a legegyszerűbb módszer egy felügyelt identitás használata keresztül, a `Microsoft.Azure.Services.AppAuthentication` csomagot. Első lépések a következő:

1. Adja hozzá hivatkozásokat az [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-csomagok az alkalmazáshoz. 

2. Adja hozzá a következő kódot:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

A `AzureServiceTokenProvider` osztály gyorsítótárazza a memóriában, és lekéri az Azure ad-ből lejárata előtt. Ennek következtében, már nem rendelkezik a lejárati hívása előtt ellenőrizze a `GetAccessTokenAsync` metódust. Ha meg szeretné használni a jogkivonat csak a metódust hívja. 

A `GetAccessTokenAsync` módszerhez egy erőforrás-azonosítója. További tudnivalókért lásd: [mely Azure-szolgáltatások felügyelt identitások támogatása az Azure-erőforrások](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Helyi fejlesztés hitelesítés

A helyi fejlesztési két elsődleges hitelesítési forgatókönyv közül választhat: [hitelesítése az Azure-szolgáltatások](#authenticating-to-azure-services), és [egyéni szolgáltatások való hitelesítése](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Hitelesítés az Azure-szolgáltatások

Helyi gépek nem támogatják a felügyelt identitások az Azure-erőforrásokhoz.  Ennek eredményeképpen a `Microsoft.Azure.Services.AppAuthentication` könyvtár a fejlesztői hitelesítő adatait használja a helyi fejlesztési környezet futtatásához. Telepítésekor a megoldás az Azure-ba, a tár használja egy felügyelt identitás váltson át egy OAuth 2.0 ügyfél-hitelesítő adatok megadási folyamatában.  Ez azt jelenti, hogy nélkül lehet tesztelni ugyanazt a kódot helyileg és távolról aggódjon.

A helyi fejlesztési `AzureServiceTokenProvider` jogkivonatok segítségével beolvassa **Visual Studio**, **Azure parancssori felület** (CLI), vagy **az Azure AD integrált hitelesítés**. Minden beállítást a rendszer megpróbálkozik egymás után, és a tár használja az első lehetőség, hogy sikeres volt. Ha nincs lehetőség a működik, egy `AzureServiceTokenProviderException` részletes információkkal együtt, kivételhiba fordul elő.

### <a name="authenticating-with-visual-studio"></a>A Visual Studióval hitelesítése

A Visual Studio használatával hitelesíti a következő előfeltételeket tartalmazza:

1. [A Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) vagy újabb.

2. A [for Visual Studio App hitelesítési kiterjesztés](https://go.microsoft.com/fwlink/?linkid=862354), a Visual Studio 2017 Update 5 külön bővítményeként érhető el, és az a 6. frissítés és újabb verzióiban a termék. 6\. frissítés vagy újabb, illetve az alkalmazás hitelesítési bővítmény telepítését az Azure fejlesztői eszközök a Visual Studio telepítőjében kiválasztásával ellenőrizheti.
 
Jelentkezzen be a Visual Studióban, és használjon **eszközök**&nbsp;>&nbsp;**beállítások**&nbsp;>&nbsp;**Azure-szolgáltatás Hitelesítési** ki kell választania egy fiókot a helyi fejlesztési. 

Ha hibákba ütközne a Visual studióval, például a jogkivonat-szolgáltató fájl kapcsolatos hibákat el figyelmesen ezeket a lépéseket. 

Azt is szükség lehet hitelesítse magát újra a fejlesztői jogkivonatot. Ehhez nyissa meg **eszközök**&nbsp;>&nbsp;**beállítások**>**Azure&nbsp;szolgáltatás&nbsp;hitelesítés**  , és keressen egy **újra hitelesíteni kell** a kiválasztott fiók alatti hivatkozásra.  Válassza ki azt a hitelesítéshez. 

### <a name="authenticating-with-azure-cli"></a>Hitelesítés az Azure CLI-vel

Helyi fejlesztés az Azure CLI használata:

1. Telepítés [Azure CLI-vel v2.0.12](/cli/azure/install-azure-cli) vagy újabb. Korábbi verziók frissítése. 

2. Használat **az bejelentkezési** bejelentkezni az Azure-bA.

Használat `az account get-access-token` hozzáférés ellenőrzéséhez.  Ha hibaüzenetet kap, ellenőrizze, hogy 1. lépés sikeresen befejeződött-e. 

Ha az alapértelmezett címtár az Azure CLI nincs telepítve, akkor előfordulhat, hogy megjelenik egy hibaüzenet, jelentéskészítési `AzureServiceTokenProvider` Azure CLI-hez az elérési út nem található.  Használja a **AzureCLIPath** környezeti változót, az Azure CLI telepítési mappa megadása. `AzureServiceTokenProvider` hozzáadja a megadott könyvtárban a **AzureCLIPath** környezeti változót, a **elérési út** környezeti változót, ha szükséges.

Ha bejelentkezett az Azure CLI-t több fiókot, vagy a fiók több előfizetést is hozzáféréssel rendelkezik, adja meg az adott előfizetés használni szeretne.  Ehhez használja:

```
az account set --subscription <subscription-id>
```

Ez a parancs létrehoz kimeneti csak a hibával kapcsolatban.  A jelenlegi beállítások ellenőrzéséhez használja:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>Hitelesítés az Azure AD-hitelesítés

Az Azure AD-hitelesítés használatához ellenőrizze, hogy:

- A helyszíni active Directoryban [szinkronizálja az Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- A kód egy tartományhoz csatlakoztatott számítógépen fut.


### <a name="authenticating-to-custom-services"></a>Egyéni szolgáltatások való hitelesítése

Ha egy szolgáltatás hívások Azure-szolgáltatások, az előző lépések működik, mert a Azure-szolgáltatások lehetővé teszik a felhasználók és az alkalmazások elérését.  

Egy szolgáltatás, amely meghív egy egyéni szolgáltatás létrehozásakor használja az Azure AD ügyfél-hitelesítő adatok a helyi fejlesztési hitelesítéshez.  Két lehetőség van: 

1.  Használjon szolgáltatásnevet az Azure-ba való bejelentkezéshez:

    1.  [Egyszerű szolgáltatás létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Azure CLI használatával jelentkezzen be:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Mivel az egyszerű szolgáltatás nem jogosult az elérésére, egy előfizetésre, használja a `--allow-no-subscriptions` argumentum.

2.  A környezeti változók használatával adja meg a szolgáltatás egyszerű adatait.  További információkért lásd: [használatával egy egyszerű szolgáltatást az alkalmazás futtatása](#running-the-application-using-a-service-principal).

Az Azure-ba, bejelentkezés után `AzureServiceTokenProvider` használja az egyszerű szolgáltatás a helyi fejlesztési jogkivonat beszerzésére.

Ez csak helyi fejlesztési vonatkozik. Telepítésekor a megoldás az Azure-ba, a tár átvált egy felügyelt identitás a hitelesítéshez.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Az alkalmazás felügyelt identitás vagy felhasználó által hozzárendelt identitás használatával fut 

Amikor futtatja a kódot egy Azure App Service-ben vagy egy Azure virtuális Gépen engedélyezve van egy felügyelt identitás, a tár automatikusan használja a felügyelt identitás. 

Azt is megteheti előfordulhat, hogy a hitelesítést egy felhasználó által hozzárendelt identitással. További információ a felhasználó által hozzárendelt identitások: [kapcsolatos felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Egy felhasználó által hozzárendelt identitással hitelesítést, a kapcsolati karakterláncban adja meg az ügyfél-Azonosítót a felhasználó által hozzárendelt identitás kell. A kapcsolati karakterlánc szerepel a [kapcsolati karakterlánc támogatási](#connection-string-support) szakaszt.

## <a name="running-the-application-using-a-service-principal"></a>Egyszerű szolgáltatás használatával az alkalmazás futtatása 

Elképzelhető, hogy hitelesítéséhez az Azure AD-ügyfél hitelesítő adatok létrehozásához szükséges. Gyakori példák:

- A kód a helyi fejlesztési környezetet, de nem a fejlesztői identitás alatt fut.  A Service Fabric, például használja a [NetworkService fiók](../service-fabric/service-fabric-application-secret-management.md) a helyi fejlesztési.
 
- A kód fut a helyi fejlesztési környezetet, és a egy egyéni szolgáltatás, ezért nem használható a fejlesztői személyazonosság hitelesítéséhez. 
 
- A kódja fut. a még nem támogatja a felügyelt identitások Azure-erőforrások, például az Azure Batch számítási Azure-erőforrások.

Nincsenek három elsődleges módszer használatával egy egyszerű szolgáltatást az alkalmazás futtatásához. Bármelyiket használhatja, először [egyszerű szolgáltatás létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Helyi keystore egy tanúsítvány használatával jelentkezzen be Azure ad-ben

1. Hozzon létre egy egyszerű service-tanúsítványt, az Azure CLI használatával [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot. 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Ezzel létrehoz egy .pem-fájlt (titkos kulcs) fog tárolni a kezdőkönyvtárban. A tanúsítvány központi telepítését vagy a *LocalMachine* vagy *CurrentUser* tárolásához. 

    > [!Important]
    > A CLI-parancs létrehoz egy .pem fájlt, de a Windows a PFX-tanúsítványokat csak natív támogatást nyújt. Ehelyett hozzon létre egy PFX-tanúsítvány, használja az itt látható a PowerShell-parancsokat: [Egyszerű szolgáltatásnév létrehozása önaláírt tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Ezek a parancsok a tanúsítvány is üzembe helyezhetők.

1. Nevű környezeti változó értéke **AzureServicesAuthConnectionString** való:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Cserélje le *{AppId}* , *{Bérlőazonosító}* , és *{Thumbprint}* az 1. lépésben létrehozott értékekkel. Cserélje le *{CertificateStore}* mindkettővel `LocalMachine` vagy `CurrentUser`a telepítési terv alapján.

1. Futtassa az alkalmazást. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Egy közös titkos hitelesítő adatok használatával jelentkezzen be Azure ad-ben

1. Hozzon létre egy szolgáltatásnév és egy jelszót az [az ad sp create-for-rbac – jelszó](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). 

2. Nevű környezeti változó értéke **AzureServicesAuthConnectionString** való:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Cserélje le _{AppId}_ , _{Bérlőazonosító}_ , és _{ClientSecret}_ az 1. lépésben létrehozott értékekkel.

3. Futtassa az alkalmazást. 

Miután minden van beállítva, további kódmódosítás nélkül szükség.  `AzureServiceTokenProvider` a környezeti változót, és a tanúsítvány használatával az Azure AD-hitelesítést. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Tanúsítvány használata a Key Vault az Azure AD-ba való bejelentkezéshez

Ez a beállítás lehetővé teszi a szolgáltatásnév ügyféltanúsítvány tárolása a Key Vaultban, és ezzel az egyszerű szolgáltatásnév hitelesítése. Ez a következő esetekben előfordulhat, hogy használja:

* Helyi hitelesítés, ahol kell hitelesíteni, explicit szolgáltatásnévvel, és egyszerű szolgáltatás hitelesítő adatait biztonságos módon megtartja a key vaultban. Fejlesztői fiók a key vaulthoz hozzáféréssel kell rendelkeznie. 
* Hitelesítés az Azure, ahol szeretné használni az explicit hitelesítő adatok (például a több-bérlős forgatókönyvek), és egyszerű szolgáltatás hitelesítő adatait biztonságos módon megtartja a key vaultban. Felügyelt identitás a key vault hozzáféréssel kell rendelkeznie. 

A felügyelt identitás- vagy a developer identityvel beolvasni az ügyféltanúsítványt a Key vaultból engedéllyel kell rendelkeznie. A AppAuthentication könyvtár a lekért tanúsítványt használja, mint az egyszerű szolgáltatás ügyfél-hitelesítő adatok.

A szolgáltatás egyszerű hitelesítéshez használandó ügyféltanúsítvány

1. Egy szolgáltatásnév létrehozásához, és automatikusan tárolja az Azure CLI-vel a keyvault [az ad sp create-for-rbac – keyvault <keyvaultname> – tanúsítvány <certificatename> --létrehozása-cert--skip-hozzárendelés](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    A tanúsítvány azonosítóját egy URL-címet a következő formátumban lesznek. `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Cserélje le `{KeyVaultCertificateSecretIdentifier}` a kapcsolati karakterláncban a tanúsítvány-azonosítóval:

```
RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
```

Ha például a key vault "myKeyVault" hívása történt, és létrehozott egy "myCert" nevű tanúsítványt a tanúsítvány azonosítóját lenne `https://myKeyVault.vault.azure.net/secrets/myCert`, és a kapcsolati karakterlánc lenne `RunAs=App;AppId={TestAppId};TenantId={TenantId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert`.

## <a name="connection-string-support"></a>Kapcsolati karakterlánc-támogatás

Alapértelmezés szerint `AzureServiceTokenProvider` több módszert használ a jogkivonat beszerzésére. 

A folyamat szabályozásához átadott kapcsolati karakterlánc használata a `AzureServiceTokenProvider` konstruktor vagy a megadott a *AzureServicesAuthConnectionString* környezeti változót. 

A következő beállítások támogatottak:

| Kapcsolati karakterlánc lehetőség | Forgatókönyv | Megjegyzések|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Helyi fejlesztés | AzureServiceTokenProvider AzureCli használatával szerezze be a tokent. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Helyi fejlesztés | AzureServiceTokenProvider uses Visual Studio to get token. |
| `RunAs=CurrentUser` | Helyi fejlesztés | AzureServiceTokenProvider beszerezni a jogkivonatot az Azure AD integrált hitelesítést használ. |
| `RunAs=App` | [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider egy felügyelt identitás használatával szerezze be a tokent. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Felhasználó által hozzárendelt identitás az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider egy felhasználó által hozzárendelt identitás használatával szerezze be a tokent. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Egyéni szolgáltatások hitelesítés | KeyVaultCertificateSecretIdentifier = a tanúsítvány titkos kulcs azonosítóját. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Egyszerű szolgáltatás | `AzureServiceTokenProvider` a tanúsítványt használja, szerezze be a tokent az Azure ad-ből. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Egyszerű szolgáltatás | `AzureServiceTokenProvider` tanúsítványt használ, szerezze be a tokent az Azure ad-ből|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Egyszerű szolgáltatás |`AzureServiceTokenProvider` titkos kulcs használatával szerezze be a tokent az Azure ad-ből. |

## <a name="samples"></a>Minták

Megtekintheti a `Microsoft.Azure.Services.AppAuthentication` könyvtár működés közben, olvassa el a következő Kódminták.

1. [Titkos kulcs lekérése az Azure Key Vault futásidőben egy felügyelt identitás használata](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programozott módon telepítheti az Azure Resource Manager-sablon, egy felügyelt identitás az Azure virtuális gépből](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Minta .NET Core és a egy felügyelt identitás használata Azure-szolgáltatások meghívása az Azure Linux virtuális](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/index.yml).
- Tudjon meg többet [az Azure AD hitelesítési forgatókönyvei](../active-directory/develop/active-directory-authentication-scenarios.md).
