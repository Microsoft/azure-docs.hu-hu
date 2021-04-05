---
title: 'Oktatóanyag: felügyelt identitás használata az Azure SQL Database-Windows-Azure AD eléréséhez'
description: Egy oktatóanyag, amely végigvezeti a Windows rendszerű virtuális gépekhez rendelt felügyelt identitások használatával való Azure SQL Database elérésének folyamatán.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856512"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Oktatóanyag: Hozzáférés az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a rendszerszintű identitást a Windows rendszerű virtuális gépekhez (VM) a Azure SQL Database eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
>
> * A virtuális gép hozzáférésének biztosítása Azure SQL Database
> * Azure AD-hitelesítés engedélyezése
> * A virtuális gép rendszerhez hozzárendelt identitását képviselő tartalmazott felhasználó létrehozása az adatbázisban
> * Hozzáférési jogkivonat beszerzése a virtuálisgép-identitás használatával és lekérdezése Azure SQL Database

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Engedélyezés

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Hozzáférés biztosítása

Ahhoz, hogy a virtuális gép hozzáférhessen a Azure SQL Database lévő adatbázishoz, használhat meglévő [logikai SQL Servert](../../azure-sql/database/logical-servers.md) , vagy létrehozhat egy újat. Ha új kiszolgálót és adatbázist szeretne létrehozni az Azure Portalon, kövesse ennek az [Azure SQL rövid útmutatónak](../../azure-sql/database/single-database-create-quickstart.md) a lépéseit. Az [Azure SQL dokumentációjában](/azure/sql-database/) olyan rövid útmutatók is vannak, amelyek az Azure CLI-t és az Azure PowerShellt használják.

Két lépés kell hozzá, hogy a VM hozzá tudjon férni egy adatbázishoz:

1. Engedélyezze az Azure AD-hitelesítést a-kiszolgálón.
2. Létre kell hozni egy, a virtuális gép rendszerhez hozzárendelt identitását képviselő **tartalmazott felhasználót** az adatbázisban.

### <a name="enable-azure-ad-authentication"></a>Azure AD-hitelesítés engedélyezése

**Az [Azure ad-hitelesítés konfigurálása](../../azure-sql/database/authentication-aad-configure.md):**

1. Az Azure Portal bal oldali navigációs sávjában válassza az **SQL-kiszolgálók** elemet.
2. Kattintson az Azure AD-hitelesítéshez engedélyezni kívánt SQL-kiszolgálóra.
3. A panel **Beállítások** szakaszában kattintson az **Active Directory-rendszergazda** elemre.
4. Kattintson a **Rendszergazda beállítása** elemre a parancssávon.
5. Válassza ki azt az Azure AD felhasználói fiókot, amelyet a kiszolgáló rendszergazdájává szeretne tenni, és kattintson a **Kiválasztás** lehetőségre.
6. Kattintson a **Mentés** gombra a parancssávon.

### <a name="create-contained-user"></a>Foglalt felhasználó létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre egy tárolt felhasználót a virtuális gép rendszerhez rendelt identitását képviselő adatbázisban. Ehhez a lépéshez [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) szükséges. Mielőtt hozzálátna, hasznos lehet áttekinteni az Azure AD-integráció hátterével foglalkozó következő cikkeket:

- [Univerzális hitelesítés a SQL Database és az Azure szinapszis Analytics szolgáltatással (SSMS-támogatás az MFA-hoz)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy Azure szinapszis Analytics használatával](../../azure-sql/database/authentication-aad-configure.md)

Az SQL-ADATBÁZIShoz egyedi HRE megjelenítendő nevek szükségesek. Ezzel a HRE-fiókoknak, például a felhasználóknak, csoportoknak és egyszerű szolgáltatásoknak (alkalmazásoknak), valamint a felügyelt identitás számára engedélyezett virtuális gépek nevének egyedileg definiálva kell lennie a HRE a megjelenítendő nevükkel kapcsolatban. Az SQL DB ellenőrzi a HRE megjelenítendő nevét az ilyen felhasználók T-SQL-létrehozása során, és ha az nem egyedi, a parancs sikertelenül kéri az adott fiók egyedi HRE-megjelenítési nevének megadását.

**Egy foglalt felhasználó létrehozása:**

1. Indítsa el az SQL Server Management Studiót.
2. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen írja be a kiszolgáló nevét a **kiszolgáló neve** mezőbe.
3. Az **Authentication** (Hitelesítés) mezőben válassza ki az **Active Directory - Universal with MFA support** (Active Directory – univerzális, MFA-támogatással) lehetőséget.
4. A **User name** (Felhasználónév) mezőbe írja be azon Azure AD-fiók nevét, amelyet a kiszolgáló rendszergazdájaként szeretne beállítani (például helen@woodgroveonline.com).
5. Kattintson a **Beállítások** gombra.
6. A **Connect to database** (Csatlakozás az adatbázishoz) mezőbe írja be a konfigurálni kívánt, nem rendszerszintű adatbázis nevét.
7. Kattintson a **Csatlakozás** gombra. Fejezze be a bejelentkezést.
8. Az **Object Explorerben** bontsa ki a **Databases** (Adatbázisok) mappát.
9. Kattintson a jobb gombbal egy felhasználói adatbázisra, majd kattintson a **New Query** (Új lekérdezés) menüpontra.
10. A lekérdezési ablakban írja be a következő sort, és kattintson az eszköztár **Execute** (Végrehajtás) gombjára:

    > [!NOTE]
    > Az alábbi parancsban a `VMName` a virtuális gép neve, amelyen az előfeltételek szakaszban a rendszerhez hozzárendelt identitást engedélyezte.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    A parancsnak sikeresen futnia kell, és létre kell hoznia a virtuális gép rendszerhez hozzárendelt identitásának tartalmazott felhasználóját.
11. Törölje a lekérdezési ablakot, írja be a következő sort, és kattintson az eszköztár **Execute** (Végrehajtás) gombjára:

    > [!NOTE]
    > Az alábbi parancsban a `VMName` a virtuális gép neve, amelyen az előfeltételek szakaszban a rendszerhez hozzárendelt identitást engedélyezte.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    A parancsnak sikeresen futnia kell, így a tartalmazott felhasználó képes lesz a teljes adatbázis olvasására.

A virtuális gépen futó kód mostantól lekérheti a tokent a rendszerhez rendelt felügyelt identitás használatával, és a token használatával hitelesíti a kiszolgálót.

## <a name="access-data"></a>Adatok elérése

Ez a szakasz bemutatja, hogyan kérhet hozzáférési tokent a virtuális gép rendszer által hozzárendelt felügyelt identitásával, és hogyan hívhatja meg az Azure SQL-t. Az Azure SQL natív támogatást nyújt az Azure AD-hitelesítéshez, így közvetlenül is elfogadhatja az Azure-erőforrások felügyelt identitásaival beszerzett hozzáférési jogkivonatokat. Az SQL-lel létesített kapcsolat létrehozásához használja az **access token** metódust. Ez az Azure SQL és az Azure AD integrációjának része, és eltér attól a megoldástól, amikor a kapcsolati sztringen adja meg a hitelesítő adatokat.

A .NET-kód példa arra, hogy egy hozzáférési jogkivonat használatával nyitja meg az SQL-kapcsolatot. A kódnak a virtuális GÉPEN kell futnia ahhoz, hogy hozzáférhessen a virtuális gép rendszer által hozzárendelt felügyelt identitásának végpontához. A hozzáférési jogkivonat metódusának használatához a **.NET-keretrendszer 4,6** -es vagy újabb verziójára vagy a **.net Core 2,2** -es vagy újabb verziójára van szükség. Cserélje le az AZURE-SQL-SERVERNAME és a DATABASE értékét a megfelelő értékre. Figyelje meg, hogy az Azure SQL erőforrás-azonosítója a következő: `https://database.windows.net/` .

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

A PowerShell használatával is gyorsan tesztelhető a végpontok közötti beállítás anélkül, hogy alkalmazást kellene írnia és üzembe helyeznie a virtuális gépen.

1. A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg.
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a **PowerShellt** a távoli munkamenetben.
4. A PowerShell `Invoke-WebRequest` parancsával küldjön kérést a helyi felügyelt identitás végpontjára, hogy lekérjen egy hozzáférési jogkivonatot az Azure SQL-hez.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Konvertálja a választ JSON-objektumból PowerShell-objektummá.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Nyerje ki a hozzáférési jogkivonatot a válaszból.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Nyisson meg egy kapcsolódást a-kiszolgálóhoz. Ne felejtse el lecserélni az AZURE-SQL-SERVERNAME és a DATABASE értékét.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ezután hozzon létre és küldjön el egy lekérdezést a kiszolgálónak. Ne felejtse el lecserélni a TABLE értékét.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Vizsgálja meg a `$DataSet.Tables[0]` értékét, hogy áttekinthesse a lekérdezés eredményeit.

## <a name="disable"></a>Letiltás

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan használhat egy rendszerhez rendelt felügyelt identitást a Azure SQL Database eléréséhez. Ha többet szeretne megtudni Azure SQL Database lásd:

> [!div class="nextstepaction"]
> [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)
