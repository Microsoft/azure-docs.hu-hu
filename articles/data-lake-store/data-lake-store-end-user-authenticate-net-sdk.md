---
title: Végfelhasználói hitelesítés – .NET és Data Lake Storage Gen1 – Azure
description: Ismerje meg, hogyan érheti el a végfelhasználói hitelesítést a Azure Data Lake Storage Gen1 a Azure Active Directory és a .NET SDK használatával
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89004782"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Végfelhasználói hitelesítés a Azure Data Lake Storage Gen1 .NET SDK használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebből a cikkből megtudhatja, hogyan használhatja a .NET SDK-t a végfelhasználói hitelesítésre Azure Data Lake Storage Gen1 használatával. A szolgáltatások közötti hitelesítéshez a .NET SDK-val Data Lake Storage Gen1 használatával lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
* A **Visual Studio 2013-es vagy újabb** verzióját. Az alábbi utasítások a Visual Studio 2019-et használják.

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "natív" alkalmazást**. A Data Lake Storage Gen1 a Azure Active Directory használatával végre kell hajtania a [végfelhasználói hitelesítéshez](data-lake-store-end-user-authenticate-using-active-directory.md)szükséges lépéseket.

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

5. **Program megnyitása. cs**
6. Cserélje le a using utasításokat a következő sorokra:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Adja hozzá ezt a kódrészletet a .NET-ügyfélalkalmazás alkalmazásához. Cserélje le a helyőrző értékeket az Azure AD natív alkalmazásból beolvasott értékekre (az előfeltételként megadott módon). Ez a kódrészlet lehetővé teszi, hogy az alkalmazást **interaktívan** hitelesítse Data Lake Storage Gen1okkal, ami azt jelenti, hogy meg kell adnia az Azure-beli hitelesítő adatait.

Az egyszerű használat érdekében az alábbi kódrészlet az ügyfél-azonosító és az átirányítási URI alapértelmezett értékeit használja, amelyek bármely Azure-előfizetéshez érvényesek. A következő kódrészletben csak a bérlői azonosító értékét kell megadnia. A bérlő AZONOSÍTÓját a [bérlői azonosító lekérése](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)című részben leírtak alapján kérheti le.
    
- Cserélje le a Main () függvényt a következő kódra:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Néhány tudnivaló az előző kódrészletről:

* Az előző kódrészlet segítő függvényeket `GetTokenCache` és `GetCreds_User_Popup` . Ezen segítő függvények kódja elérhető [itt a githubon](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Az oktatóanyag gyorsabb elvégzéséhez a kódrészlet egy natív alkalmazás ügyfél-AZONOSÍTÓját használja, amely alapértelmezés szerint elérhető az összes Azure-előfizetéshez. Így **a kódrészletet változtatás nélkül használhatja az alkalmazásában**.
* Ha azonban a saját Azure AD-tartományát és alkalmazásügyfél-azonosítóját szeretné használni, létre kell hoznia egy natív Azure AD-alkalmazást, majd a létrehozott alkalmazáshoz használnia kell az Azure AD-bérlőazonosítót, az ügyfél-azonosítót és az átirányítási URI-t. Útmutatásért lásd: [Active Directory alkalmazás létrehozása végfelhasználói hitelesítéshez Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) .

  
## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használható a végfelhasználói hitelesítés a Azure Data Lake Storage Gen1 .NET SDK-val történő hitelesítéséhez. A következő cikkekből megtudhatja, hogyan használhatja a .NET SDK-t a Azure Data Lake Storage Gen1-vel való együttműködésre.

* [Fiókkezelés Data Lake Storage Gen1 a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Az adatműveletek Data Lake Storage Gen1 a .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)

