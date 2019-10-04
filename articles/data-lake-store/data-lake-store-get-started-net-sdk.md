---
title: '.NET SDK: Fiókkezelési műveletek az Azure Data Lake Storage Gen1 |} A Microsoft Docs'
description: Fiókkezelési műveletek végrehajtása a Data Lake Storage Gen1 az Azure Data Lake Storage Gen1 .NET SDK használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8da40aa04381542c8c750c8d7e33c9a29879371d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65900877"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Fiókkezelési műveletek az Azure Data Lake Storage Gen1 .NET SDK használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ebben a cikkben megismerheti, hogyan hajthat végre a fiókkezelési műveletek az Azure Data Lake Storage Gen1 .NET SDK használatával. Fiókkezelési műveletek közé tartozik, egy Data Lake Storage Gen1 listázása a fiókokat az Azure-előfizetéssel, törlés, a fiókok, stb-fiók létrehozása.

Adatok felügyeleti műveletek végrehajtása a Data Lake Storage Gen1 .NET SDK-val kapcsolatos utasításokért lásd: [fájlrendszerműveletek a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
* **A Visual Studio 2013 vagy újabb**. Az alábbi utasítások a Visual Studio 2019 használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása
1. A Visual Studióban válassza ki a **fájl** menüben **új**, majd **projekt**.
2. Válasszon **Console App (.NET Framework)** , majd válassza ki **tovább**.
3. A **projektnév**, adja meg `CreateADLApplication`, majd válassza ki **létrehozás**.

4. Adja hozzá a NuGet-csomagokat a projekthez.

   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   2. Győződjön meg arról, hogy a **NuGet Package Manager** (NuGet-csomagkezelő) lapon a **Package source** (Csomag forrása) értéke **nuget.org**, és az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzet be van jelölve.
   3. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a 2.1.3-as előzetes verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.2.12-es verziót használja.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Új Azure Data Lake-fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.
5. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

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

6. Deklarálja a változókat, majd adja meg a helyőrzők értékét. Arról is győződjön meg, hogy a megadott helyi elérési út és fájlnév létezik a számítógépen.

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

A cikk fennmaradó részéből megtudhatja, hogyan használhatja az elérhető .NET-metódusokat az olyan műveletek elvégzésére, mint a hitelesítés, a fájlok feltöltése stb.

## <a name="authentication"></a>Hitelesítés

* Az alkalmazás végfelhasználói hitelesítésével kapcsolatban lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Ügyfélobjektum létrehozása
A következő kódrészlet létrehozza a Data Lake Storage Gen1 fiók ügyfélobjektumát, amely a szolgáltatásnak küldött fiókkezelési kérések kiadására, mint például a fiók létrehozása, fiók törlése stb.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Hozzon létre egy Data Lake Storage Gen1 fiókot
Az alábbi kódrészlet létrehoz egy Data Lake Storage Gen1 fiókot a Data Lake Storage Gen1 fiók ügyfélobjektumának létrehozásakor megadott Azure-előfizetésben.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Egy előfizetésen belüli összes Data Lake Storage Gen1 fiókok listázása
Adja hozzá a következő metódust az osztálydefinícióhoz. A következő kódrészlet listázza az összes Data Lake Storage Gen1 fiókot egy Azure-előfizetésen belül.

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

## <a name="delete-a-data-lake-storage-gen1-account"></a>Törölheti a Data Lake Storage Gen1
A következő kódrészlet törli a korábban létrehozott Data Lake Storage Gen1 fiókot.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Lásd még
* [Fájlrendszerműveletek a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK-referencia](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>További lépések
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
