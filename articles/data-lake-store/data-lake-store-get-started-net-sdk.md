---
title: Azure Data Lake Storage Gen1-fiók kezelése a .NET-tel
description: Ismerje meg, hogyan használhatja a .NET SDK-t a Azure Data Lake Storage Gen1 Fiókkezelés műveleteihez.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c4ed092604e9511023a7025a69dc7a78652ea56
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430479"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Fiókkezelés Azure Data Lake Storage Gen1 a .NET SDK használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ebből a cikkből megtudhatja, hogyan hajthat végre Fiókkezelés-műveleteket Azure Data Lake Storage Gen1 a .NET SDK használatával. A fiókkezelés műveletei közé tartozik a Data Lake Storage Gen1 fiók létrehozása, az Azure-előfizetésben lévő fiókok listázása, a fiókok törlése stb.

Az adatkezelési műveletek .NET SDK használatával történő végrehajtásával kapcsolatos útmutatásért tekintse meg a [Data Lake Storage Gen1 filesystem Operations on Data Lake Storage Gen1 .net SDK](data-lake-store-data-operations-net-sdk.md)-t.

## <a name="prerequisites"></a>Előfeltételek
* A **Visual Studio 2013-es vagy újabb** verzióját. Az alábbi utasítások a Visual Studio 2019-et használják.

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

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

6. Deklarálja a változókat, majd adja meg a helyőrzők értékét. Arról is győződjön meg, hogy a megadott helyi elérési út és fájlnév létezik a számítógépen.

    ```csharp
    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
                
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _subId;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                _location = "East US 2";
                _subId = "<SUBSCRIPTION-ID>";                    
            }
        }
    }
    ```

A cikk fennmaradó részéből megtudhatja, hogyan használhatja az elérhető .NET-metódusokat az olyan műveletek elvégzésére, mint a hitelesítés, a fájlok feltöltése stb.

## <a name="authentication"></a>Hitelesítés

* Az alkalmazás végfelhasználói hitelesítéséhez lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md).
* Az alkalmazás szolgáltatások közötti hitelesítéséhez lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Ügyfélobjektum létrehozása
A következő kódrészlet létrehozza a Data Lake Storage Gen1-fiók ügyféloldali objektumát, amely a szolgáltatáshoz tartozó Fiókkezelés-kérelmek kibocsátására szolgál, például fiók létrehozása, fiók törlése stb.

```csharp
// Create client objects and set the subscription ID
_adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
```
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása
Az alábbi kódrészlet egy Data Lake Storage Gen1 fiókot hoz létre az Azure-előfizetésben, amelyet a Data Lake Storage Gen1 Account Client objektum létrehozásakor adott meg.

```csharp
// Create Data Lake Storage Gen1 account
var adlsParameters = new DataLakeStoreAccount(location: _location);
_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
```

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Az előfizetésen belüli összes Data Lake Storage Gen1 listázása
Adja hozzá a következő metódust az osztálydefinícióhoz. Az alábbi kódrészlet egy adott Azure-előfizetésben lévő összes Data Lake Storage Gen1-fiókot listázza.

```csharp
// List all Data Lake Storage Gen1 accounts within the subscription
public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
{
    var response = _adlsClient.Account.List(_adlsAccountName);
    var accounts = new List<DataLakeStoreAccountBasic>(response);

    while (response.NextPageLink != null)
    {
        response = _adlsClient.Account.ListNext(response.NextPageLink);
        accounts.AddRange(response);
    }

    return accounts;
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 fiók törlése
A következő kódrészlet törli a korábban létrehozott Data Lake Storage Gen1 fiókot.

```csharp
// Delete Data Lake Storage Gen1 account
_adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
```

## <a name="see-also"></a>Lásd még
* [Fájlrendszer-műveletek Data Lake Storage Gen1 a .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK-dokumentáció](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Következő lépések
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)