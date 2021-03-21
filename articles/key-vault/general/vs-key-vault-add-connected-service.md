---
title: Key Vault támogatás hozzáadása a ASP.NET-projekthez a Visual Studio-Azure Key Vault használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá Key Vault támogatást egy ASP.NET vagy ASP.NET Core webalkalmazáshoz.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792395"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault hozzáadása a webalkalmazáshoz a Visual Studio csatlakoztatott szolgáltatásainak használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhatja létre a webes projektekhez szükséges titkokat a Visual Studióban, akár ASP.NET Core, akár bármilyen ASP.NET-projektet használ, hogy könnyen hozzá tudjon adni mindent, amire szüksége van a Azure Key Vault használatának megkezdéséhez. A Visual Studio csatlakoztatott szolgáltatások funkciójának használatával a Visual Studio automatikusan felveheti az összes olyan NuGet-csomagot és konfigurációs beállítást, amelyhez csatlakoznia kell az Azure-beli Key Vaulthoz.

A csatlakoztatott szolgáltatások által a projektben a Key Vault engedélyezéséhez szükséges módosítások részleteiért lásd: [Key Vault csatlakoztatott szolgáltatás – mi történt a ASP.net 4.7.1 Project](#how-your-aspnet-framework-project-is-modified) vagy [Key Vault Connected Service szolgáltatással – mi történt a ASP.net Core projekttel](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Előfeltételek

- **Egy Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztráljon egy [ingyenes fiókra](https://azure.microsoft.com/pricing/free-trial/).
- A **Visual Studio 2019 16,3** -es vagy újabb verziója [már letölthető](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>Key Vault támogatás hozzáadása a projekthez

Mielőtt elkezdené, győződjön meg róla, hogy be van jelentkezve a Visual studióba. Jelentkezzen be ugyanazzal a fiókkal, amelyet az Azure-előfizetéséhez használ. Ezután nyisson meg egy ASP.NET 4.7.1 vagy újabb verziót, vagy ASP.NET Core 2,0 webes projektet, és hajtsa végre a következő lépéseket:

1. **Megoldáskezelőban** kattintson a jobb gombbal arra a projektre, amelyhez hozzá kívánja adni a Key Vault-támogatást , majd válassza a  >  **csatlakoztatott szolgáltatás** hozzáadása Hozzáadás lehetőséget  >  .
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.
1. Az elérhető szolgáltatások menüben válassza a **Azure Key Vault** lehetőséget, majd kattintson a **tovább** gombra.

   ![Válassza a "Azure Key Vault" lehetőséget](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Válassza ki a használni kívánt előfizetést, majd válasszon ki egy meglévő Key Vault, és kattintson a **Befejezés** gombra. 

   ![Az előfizetés kiválasztása](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

Most már létrejött a kapcsolat a Key Vaultval, és a Titkok a kódban is elérhetők. A következő lépések eltérnek attól függően, hogy ASP.NET 4.7.1 vagy ASP.NET Core használ-e.

## <a name="access-your-secrets-in-code-aspnet-core"></a>A titkok elérése a kódban (ASP.NET Core)

1. Nyisson meg egy lapozófájlt, például az *index. cshtml. cs* fájlt, és írja be a következő kódot:
   1. Adjon meg egy hivatkozást a `Microsoft.Extensions.Configuration` következő direktíva használatával:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Adja hozzá a konfigurációs változót.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Adja hozzá ezt a konstruktort, vagy cserélje le a meglévő konstruktort a következőre:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Frissítse a(z) `OnGet` metódust. Frissítse az itt látható helyőrző értékét a fenti parancsokban létrehozott titkos névvel.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Ha szeretné megerősíteni az értéket futásidőben, adja hozzá a `ViewData["Message"]` *. cshtml* fájlhoz megjelenítendő kódot, hogy megjelenjen a titok egy üzenetben.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Az alkalmazást helyileg futtatva ellenőrizheti, hogy a titkos kulcs beszerzése sikeresen megtörtént-e a Key Vault.

## <a name="access-your-secrets-aspnet"></a>Hozzáférés a titkokhoz (ASP.NET)
Beállíthatja a konfigurációt úgy, hogy az web.config-fájlnak a `appSettings` valós értékkel lecserélt elemében lévő dummy érték legyen. Ezt az adatstruktúra segítségével érheti el `ConfigurationManager.AppSettings` .

1. Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza a NuGet-csomagok kezelése lehetőséget. A Tallózás lapon keresse meg és telepítse a [Microsoft.Configuration.ConfigurationBuilders. Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)
 
1. Nyissa meg web.config fájlt, és írja be a következő kódot:
    1. Hozzáadás `configSections` és `configBuilders` :
        ```xml
         <configSections>
            <section
                name="configBuilders"
                type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
                restartOnExternalChanges="false"
                requirePermission="false" />
         </configSections>
         <configBuilders>
            <builders>
            <add
                    name="AzureKeyVault"
                    vaultName="vaultname"
                    type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
                    vaultUri="https://vaultname.vault.azure.net" />
            </builders>
         </configBuilders>
        ```
    1. Keresse meg a appSettings címkét, adjon hozzá egy attribútumot `configBuilders="AzureKeyVault"` , és adjon hozzá egy sort:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. Szerkessze a `About` metódust a *HomeController. cs*-ban, hogy megjelenjen az érték a megerősítéshez.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Futtassa az alkalmazást helyileg a hibakereső alatt, váltson a **Névjegy** lapra, és ellenőrizze, hogy megjelenik-e az érték a Key Vault.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Key Vault egy másik Microsoft-fiók fut, mint a Visual studióba (például a Key Vault fut a munkahelyi fiókján, de a Visual Studio használja a saját fiókját), akkor hibaüzenet jelenik meg a program. cs fájljában, hogy a Visual Studio nem fér hozzá a Key Vaulthoz. A probléma megoldása:

1. Lépjen a [Azure Portal](https://portal.azure.com) , és nyissa meg a Key Vault.

1. Válassza a **hozzáférési szabályzatok**, majd a **hozzáférési házirend hozzáadása** lehetőséget, és válassza ki azt a fiókot, amelyet a rendszerbiztonsági tagként jelentkezett be.

1. A Visual Studióban válassza **a**  >  **Fiókbeállítások** lehetőséget.
Válassza a **fiók hozzáadása** lehetőséget a **minden fiók** szakaszban. Jelentkezzen be azzal a fiókkal, amelyet a hozzáférési szabályzatának elsődlegesen választott.

1. Válassza az **eszközök**  >  **lehetőséget**, és keresse meg az **Azure-szolgáltatás hitelesítése** elemet. Ezután válassza ki azt a fiókot, amelyet az imént hozzáadott a Visual studióhoz.

Az alkalmazás hibakeresése után a Visual Studio csatlakozik ahhoz a fiókhoz, amelyen a Key Vault található.

## <a name="how-your-aspnet-core-project-is-modified"></a>A ASP.NET Core-projekt módosítása

Ez a szakasz egy ASP.NET-projekt pontos módosításait mutatja be, amikor hozzáadja a Key Vault csatlakoztatott szolgáltatást a Visual Studióval.

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core-referenciák hozzáadva

A Project file .NET-referenciákat és a NuGet-csomagok hivatkozásait érinti.

| Típus | Referencia |
| --- | --- |
| NuGet | Microsoft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Hozzáadott fájlok ASP.NET Core

- `ConnectedService.json` hozzáadva, amely a csatlakoztatott szolgáltatóval, a verzióval és a dokumentáció hivatkozásával kapcsolatos információkat rögzíti.

### <a name="project-file-changes-for-aspnet-core"></a>A Project fájl módosításai ASP.NET Core

- Hozzáadta a csatlakoztatott szolgáltatások ItemGroup és `ConnectedServices.json` fájlját.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>A ASP.NET Core változásainak launchsettings.js

- A következő környezeti változó bejegyzéseket adta hozzá a IIS Express profilhoz és a webes projekt nevével egyező profilhoz:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Azure-beli változások ASP.NET Core

- Létrehozott egy erőforráscsoportot (vagy használt egy meglévőt).
- Létrehozott egy Key Vault a megadott erőforráscsoporthoz.

## <a name="how-your-aspnet-framework-project-is-modified"></a>A ASP.NET Framework-projekt módosítása

Ez a szakasz egy ASP.NET-projekt pontos módosításait mutatja be, amikor hozzáadja a Key Vault csatlakoztatott szolgáltatást a Visual Studióval.

### <a name="added-references-for-aspnet-framework"></a>A ASP.NET-keretrendszerhez hozzáadott referenciák

A Project file .NET-referenciákat és a `packages.config` (NuGet-hivatkozásokat) érinti.

| Típus | Referencia |
| --- | --- |
| NET NuGet | Azure. Identity |
| NET NuGet | Azure. Security. kulcstartó. kulcsok |
| NET NuGet | Azure. Security. kulcstartó. Secrets |

> [!IMPORTANT] 
> Alapértelmezés szerint az Azure. Identity 1.1.1 telepítve van, amely nem támogatja a Visual Studio hitelesítő adatait. A csomag-referenciát manuálisan is frissítheti 1.2 + Visual Studio-hitelesítő adatok használatával.

### <a name="added-files-for-aspnet-framework"></a>Hozzáadott fájlok a ASP.NET-keretrendszerhez

- `ConnectedService.json` hozzáadva, amely a csatlakoztatott szolgáltatóval, verziójával és a dokumentációra mutató hivatkozással kapcsolatos adatokat rögzíti.

### <a name="project-file-changes-for-aspnet-framework"></a>A Project fájl változásai a ASP.NET-keretrendszerben

- Hozzáadta a csatlakoztatott szolgáltatások ItemGroup és ConnectedServices.jsa fájlhoz.
- A [hozzáadott hivatkozások](#added-references-for-aspnet-framework) szakaszban leírt .net-szerelvényekre mutató hivatkozások.

## <a name="next-steps"></a>Következő lépések

Ha követte ezt az oktatóanyagot, a Key Vault engedélyei a saját Azure-előfizetéssel való futtatásra vannak beállítva, de előfordulhat, hogy éles környezetben nem kívánatos. Létrehozhat egy felügyelt identitást, amellyel kezelheti Key Vault hozzáférését az alkalmazáshoz. Tekintse meg, [Hogyan hitelesítheti Key Vault](./authentication.md) és [hozzárendelhet egy Key Vault hozzáférési szabályzatot](./assign-access-policy-portal.md).

A Key Vault fejlesztésről a [Key Vault fejlesztői útmutatójában](developers-guide.md)olvashat bővebben.