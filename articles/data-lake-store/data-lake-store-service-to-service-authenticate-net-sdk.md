---
title: .NET – szolgáltatás – szolgáltatás hitelesítése – Data Lake Storage Gen1
description: Ismerje meg, hogyan valósítható meg a szolgáltatások közötti hitelesítés a Azure Data Lake Storage Gen1 használatával Azure Active Directory .NET SDK használatával
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010299"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Szolgáltatások közötti hitelesítés a Azure Data Lake Storage Gen1 .NET SDK használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Ebből a cikkből megtudhatja, hogyan használhatja a .NET SDK-t a szolgáltatások közötti hitelesítésre Azure Data Lake Storage Gen1 használatával. A .NET SDK használatával Data Lake Storage Gen1 végfelhasználói hitelesítéshez lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
* A **Visual Studio 2013-es vagy újabb** verzióját. Az alábbi utasítások a Visual Studio 2019-et használják.

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást**. A Data Lake Storage Gen1 Azure Active Directory használatával kell végrehajtania a [szolgáltatások közötti hitelesítéshez](data-lake-store-service-to-service-authenticate-using-active-directory.md)szükséges lépéseket.

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása
1. A Visual Studióban kattintson a **fájl** menüre, majd az **új**, majd a **projekt** elemre.
2. Válassza a **konzol alkalmazás (.NET-keretrendszer)** elemet, majd kattintson a **tovább** gombra.
3. A **Project Name (projekt neve**) mezőben adja meg a nevet `CreateADLApplication` , majd válassza a **Létrehozás** lehetőséget.

4. Adja hozzá a NuGet-csomagokat a projekthez.

   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   2. A **NuGet csomagkezelő** lapján ellenőrizze, hogy a **csomag forrása** **nuget.org** értékre van-e beállítva, és hogy be van-e jelölve az **előzetes verzió** jelölőnégyzet.
   3. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a 2.1.3-as előzetes verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.2.12-es verziót használja.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Új Azure Data Lake fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.

5. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Szolgáltatások közötti hitelesítés az ügyfél titkos kódjával
Adja hozzá ezt a kódrészletet a .NET-ügyfélalkalmazás alkalmazásához. Cserélje le a helyőrző értékeket egy Azure AD-webalkalmazásból beolvasott értékekre (előfeltételként felsorolva). Ez a kódrészlet lehetővé teszi, hogy az alkalmazást **nem interaktív módon** hitelesítse az Azure ad-webalkalmazáshoz tartozó ügyfél-titkos kulcs és az Data Lake Storage Gen1 használatával.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Az előző kódrészlet egy segítő függvényt használ `GetCreds_SPI_SecretKey` . Ennek a segítő függvénynek a kódja itt érhető el a [githubon](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Szolgáltatások közötti hitelesítés tanúsítvánnyal

Adja hozzá ezt a kódrészletet a .NET-ügyfélalkalmazás alkalmazásához. Cserélje le a helyőrző értékeket egy Azure AD-webalkalmazásból beolvasott értékekre (előfeltételként felsorolva). Ez a kódrészlet lehetővé teszi, hogy az alkalmazást **nem interaktív módon** hitelesítse a Data Lake Storage Gen1 egy Azure ad-webalkalmazáshoz tartozó tanúsítvány használatával. Az Azure AD-alkalmazások létrehozásával kapcsolatos utasításokért lásd: [egyszerű szolgáltatásnév létrehozása tanúsítványokkal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Az előző kódrészlet egy segítő függvényt használ `GetCreds_SPI_Cert` . Ennek a segítő függvénynek a kódja itt érhető el a [githubon](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használható a szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .NET SDK-val történő hitelesítéshez. A következő cikkekből megtudhatja, hogyan használhatja a .NET SDK-t a Data Lake Storage Gen1-vel való együttműködésre.

* [Fiókkezelés Data Lake Storage Gen1 a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Az adatműveletek Data Lake Storage Gen1 a .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)
