---
title: 'Végfelhasználói hitelesítés: .NET SDK-val rendelkező Azure Data Lake Storage Gen1 az Azure Active Directory |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a .NET SDK-val az Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c80e9953a24504c4ad324ce077b741e60a52b1fb
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908023"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a .NET SDK használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megismerkedhet a .NET SDK használata ehhez a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1. Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .NET SDK használatával, lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
* **A Visual Studio 2013 vagy újabb**. Az alábbi utasítások a Visual Studio 2019 használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre a "Natív" Azure Active Directory-alkalmazás**. El kell végeznie a lépések [végfelhasználói hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása
1. A Visual Studióban válassza ki a **fájl** menüben **új**, majd **projekt**.
2. Válasszon **Console App (.NET Framework)**, majd válassza ki **tovább**.
3. A **projektnév**, adja meg `CreateADLApplication`, majd válassza ki **létrehozás**.

4. Adja hozzá a NuGet-csomagokat a projekthez.

   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   2. Győződjön meg arról, hogy a **NuGet Package Manager** (NuGet-csomagkezelő) lapon a **Package source** (Csomag forrása) értéke **nuget.org**, és az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzet be van jelölve.
   3. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a 2.1.3-as előzetes verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.2.12-es verziót használja.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Új Azure Data Lake-fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.

5. Nyissa meg **Program.cs**
6. Cserélje le a használatával utasítások a következő sorokat:

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
Ez a kódrészlet adja hozzá a .NET-ügyfélalkalmazás. A helyőrző értékeket cserélje le az értékeket beolvasni az Azure AD natív alkalmazással (előfeltétel szerepel). Ez a kódrészlet lehetővé teszi az alkalmazás hitelesítéséhez **interaktív módon** a Data Lake Storage Gen1, ami azt jelenti, hogy adja meg Azure hitelesítő adatait kéri.

Egyszerűen használható az alábbi kódrészletben alapértelmezett értékeket használja az ügyfél-Azonosítóját és átirányítási URI-t, amely bármely Azure-előfizetés esetén érvényes. Az alábbi kódrészletben meg kell adnia az értéket a bérlő azonosítóját. A bérlő Azonosítóját adja meg az utasításokat követve kérheti [a Bérlőazonosító beszerzése](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    
- Cserélje le a Main() függvény a következő kódot:

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

Néhány tudnivaló a fenti kódrészlettel kapcsolatban:

* Az előző kódrészlet egy segédfüggvények `GetTokenCache` és `GetCreds_User_Popup`. Érhető el a kódot az alábbi segédfüggvények [Itt a Githubon](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* A kódrészlet a, az oktatóanyag gyorsabb teljesítése érdekében, hogy az összes Azure-előfizetés számára alapértelmezés szerint elérhető natív alkalmazás ügyfél-Azonosítót. Így **a kódrészletet változtatás nélkül használhatja az alkalmazásában**.
* Ha azonban a saját Azure AD-tartományát és alkalmazásügyfél-azonosítóját szeretné használni, létre kell hoznia egy natív Azure AD-alkalmazást, majd a létrehozott alkalmazáshoz használnia kell az Azure AD-bérlőazonosítót, az ügyfél-azonosítót és az átirányítási URI-t. Lásd: [Active Directory-alkalmazás végfelhasználói hitelesítésével létrehozása a Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) útmutatást.

  
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan végfelhasználói hitelesítés használata az Azure Data Lake Storage Gen1 hitelesítést, a .NET SDK használatával. Most már megtekintheti a következő cikkeket, hogy hogyan használhatja a .NET SDK használata Azure Data Lake Storage Gen1 beszélni.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [A Data Lake Storage Gen1 Adatműveletek .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)

