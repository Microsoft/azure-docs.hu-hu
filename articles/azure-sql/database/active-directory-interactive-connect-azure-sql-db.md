---
title: A ActiveDirectoryInteractive csatlakozik az SQL-hez
description: C#-kód – példa magyarázatokkal a Azure SQL Databasehoz való csatlakozáshoz a SqlAuthenticationMethod. ActiveDirectoryInteractive mód használatával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 93831ec4c1dc3e34c2ea144e71b67dae711ee870
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841648"
---
# <a name="connect-to-azure-sql-database-with-azure-ad-multi-factor-authentication"></a>Kapcsolódás Azure SQL Database az Azure AD-vel Multi-Factor Authentication
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk egy C# programot tartalmaz, amely a Azure SQL Databasehoz kapcsolódik. A program interaktív mód hitelesítést használ, amely támogatja az [Azure AD multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

További információ az SQL-eszközök Multi-Factor Authentication támogatásáról: [Azure Active Directory támogatás a SQL Server Data Toolsban (SSDT)](/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL Database Multi-Factor Authentication

A .NET-keretrendszer verziójának 4.7.2 kezdődően az Enum [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) új értékkel rendelkezik: `ActiveDirectoryInteractive` . Egy ügyfél C#-programban az enumerálási érték arra utasítja a rendszerét, hogy a Azure Active Directory (Azure AD) interaktív módot használja, amely támogatja a Multi-Factor Authentication a Azure SQL Databasehoz való kapcsolódáshoz. A programot futtató felhasználó a következő párbeszédpaneleket látja:

* Egy párbeszédpanel, amely egy Azure AD-felhasználónevet jelenít meg, és kéri a felhasználó jelszavát.

   Ha a felhasználó tartománya az Azure AD-vel összevont, ez a párbeszédpanel nem jelenik meg, mert nincs szükség jelszóra.

   Ha az Azure AD-szabályzat Multi-Factor Authentication a felhasználóra, a következő két párbeszédpanel jelenik meg.

* Amikor a felhasználó első alkalommal halad a Multi-Factor Authenticationon, a rendszer megjelenít egy párbeszédpanelt, amely felszólítja, hogy egy mobiltelefon-telefonszámot küldjön szöveges üzenetek küldésére. Minden üzenet tartalmazza azt az *ellenőrző kódot* , amelyet a felhasználónak meg kell adnia a következő párbeszédpanelen.

* Egy párbeszédpanel, amely egy Multi-Factor Authentication ellenőrző kódot kér, amelyet a rendszer a mobiltelefonnal továbbított.

További információ arról, hogyan konfigurálhatja az Azure AD-t a Multi-Factor Authentication megköveteléséhez: [Bevezetés az Azure ad multi-Factor Authentication a felhőben](../../active-directory/authentication/howto-mfa-getstarted.md).

A párbeszédpanelek képernyőképei a [többtényezős hitelesítés konfigurálása SQL Server Management Studio és az Azure ad-](authentication-mfa-ssms-configure.md)hoz című témakörben találhatók.

> [!TIP]
> A .NET-keretrendszer API-jai a [.NET API-böngésző eszköz lapját](/dotnet/api/)is megkereshetik.
>
> Kereshet közvetlenül a nem [kötelező? kifejezés = &lt; keresési érték &gt; paraméterrel](/dotnet/api/?term=SqlAuthenticationMethod)is.

## <a name="configure-your-c-application-in-the-azure-portal"></a>A C#-alkalmazás konfigurálása a Azure Portal

A Kezdés előtt létre kell hoznia és elérhetővé kell tennie egy [logikai SQL-kiszolgálót](logical-servers.md) .

### <a name="register-your-app-and-set-permissions"></a>Az alkalmazás regisztrálása és az engedélyek beállítása

Az Azure AD-hitelesítés használatához a C# programnak Azure AD-alkalmazásként kell regisztrálnia. Egy alkalmazás regisztrálásához egy Azure ad-rendszergazdának vagy egy, az Azure AD- *alkalmazás fejlesztői* szerepkörhöz rendelt felhasználónak kell lennie. A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [rendszergazdai és nem rendszergazdai szerepkörök kiosztása a felhasználókhoz Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

Az alkalmazás regisztrációjának elvégzése létrehoz egy alkalmazást, és megjeleníti az **alkalmazás azonosítóját**. A programnak tartalmaznia kell ezt az azonosítót a kapcsolódáshoz.

Az alkalmazáshoz szükséges engedélyek regisztrálása és beállítása:

1. A Azure Portal válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.

    ![Alkalmazásregisztráció](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Az alkalmazás regisztrációjának létrehozása után a rendszer létrehozza és megjeleníti az **alkalmazás azonosítójának** értékét.

    ![Alkalmazás azonosítója megjelenítve](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Válassza az **API-engedélyek** engedély  >  **hozzáadása** lehetőséget.

    ![A regisztrált alkalmazás engedélyeinek beállításai](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Válassza ki a **szervezetem által használt API** -k > típus **Azure SQL Database** a keresési >, és válassza a **Azure SQL Database** lehetőséget.

    ![Hozzáférés hozzáadása a Azure SQL Database API-hoz](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Válassza a **delegált engedélyek** lehetőséget  >  **user_impersonation**  >  **engedélyek hozzáadása** elemet.

    ![Engedélyek delegálása az API-hoz Azure SQL Database](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Azure AD-rendszergazda beállítása a kiszolgálóhoz

A C#-program futtatásához a [logikai SQL Server](logical-servers.md) -rendszergazdának hozzá kell rendelnie egy Azure ad-rendszergazdát a kiszolgálóhoz.

Az **SQL Server** lapon válassza a rendszergazda   >  **készlet rendszergazdája** Active Directory.

Az Azure AD-rendszergazdákkal és a Azure SQL Database-felhasználókkal kapcsolatos további információkért tekintse meg a [Azure Active Directory hitelesítés konfigurálása és kezelése a SQL Database](authentication-aad-configure.md#provision-azure-ad-admin-sql-database)segítségével című témakör képernyőképeit.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Nem rendszergazda felhasználó hozzáadása egy adott adatbázishoz (nem kötelező)

Egy [logikai SQL Server](logical-servers.md) Azure ad-rendszergazdája futtathatja a C# példa programot. Egy Azure AD-felhasználó futtathatja a programot, ha az adatbázisában vannak. Egy Azure AD SQL-rendszergazda vagy egy olyan Azure AD-felhasználó, aki már létezik az adatbázisban, és rendelkezik `ALTER ANY USER` engedéllyel az adatbázishoz, hozzáadhat egy felhasználót.

Az SQL-paranccsal hozzáadhat egy felhasználót az adatbázishoz [`Create User`](/sql/t-sql/statements/create-user-transact-sql) . Például: `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

További információ: [Azure Active Directory hitelesítés használata SQL Database, felügyelt példány vagy Azure szinapszis Analytics használatával történő hitelesítéshez](authentication-aad-overview.md).

## <a name="new-authentication-enum-value"></a>Új hitelesítési enumerálási érték

A C# példa a [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) névtérre támaszkodik. Különösen fontos a Multi-Factor Authentication a felsorolás `SqlAuthenticationMethod` , amelynek értékei a következők:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   A Multi-Factor Authentication megvalósításához használja ezt az értéket egy Azure AD-felhasználónévvel. Ez az érték a jelen cikk fókusza. Interaktív élményt nyújt a felhasználói jelszó párbeszédpanelek megjelenítésével, majd a Multi-Factor Authentication érvényesítéséhez, ha Multi-Factor Authentication van kiszabva ezen a felhasználón. Ez az érték a .NET-keretrendszer 4.7.2 verziójától kezdődően érhető el.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Ez az érték egy *összevont* fiók esetében használható. Összevont fiók esetén a felhasználónevet a Windows-tartomány ismeri. Ez a hitelesítési módszer nem támogatja a Multi-Factor Authentication.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Használja ezt az értéket olyan hitelesítéshez, amelyhez Azure AD-Felhasználónév és-jelszó szükséges. Azure SQL Database a hitelesítést. Ez a metódus nem támogatja a Multi-Factor Authentication.

> [!NOTE]
> Ha a .NET Core-t használja, a [Microsoft. SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) névteret kell használnia. További információkért tekintse meg a következő [blogot](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

## <a name="set-c-parameter-values-from-the-azure-portal"></a>C#-paraméterek értékének beállítása a Azure Portalból

Ahhoz, hogy a C# program sikeresen fusson, megfelelő értékeket kell hozzárendelni a statikus mezőkhöz. Itt láthatók például az értékeket tartalmazó mezők. A Azure Portal hely is látható, ahol megszerezheti a szükséges értékeket.

| Statikus mező neve | Példaérték | Hol Azure Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-kiszolgálók**  >  **Szűrés név szerint** |
| AzureAD_UserID | "auser \@ ABC.onmicrosoft.com" | **Azure Active Directory**  >  **Felhasználó**  >  **Új vendég felhasználó** |
| Initial_DatabaseName | MyDatabase | **SQL-kiszolgálók**  >  **SQL-adatbázisok** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **Keresés név alapján**  >  **Alkalmazás azonosítója** |
| RedirectUri | új URI (" https://mywebserver.com/ ") | **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **Keresés név alapján**  >  *[Saját-alkalmazás-regisztráció]*  >  **Beállítások**  >  **RedirectURIs**<br /><br />Ebben a cikkben minden érvényes érték a RedirectUri, mert itt nem használható. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Ellenőrzés SQL Server Management Studio

A C# program futtatása előtt érdemes megtekinteni, hogy a telepítés és a konfigurációk helyesek-e a SQL Server Management Studioban (SSMS). A C#-programok meghibásodása a forráskódra szűkíthető.

### <a name="verify-server-level-firewall-ip-addresses"></a>Kiszolgáló szintű tűzfal IP-címeinek ellenőrzése

Futtassa a SSMS ugyanabból a számítógépről, ugyanabban az épületben, ahol a C# programot szeretné futtatni. Ehhez a teszthez bármilyen **hitelesítési** mód van. Ha azt tapasztalja, hogy a kiszolgáló nem fogadja el az IP-címét, tekintse meg a [kiszolgálói szintű és az adatbázis szintű tűzfalszabályok](firewall-configure.md) súgóját.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Azure Active Directory Multi-Factor Authentication ellenőrzése

Futtassa újra a SSMS, ezúttal a **hitelesítés** **Azure Active Directory-Universal értékre van beállítva az MFA-val**. Ehhez a beállításhoz a 17,5-es vagy újabb SSMS-verzió szükséges.

További információ: [multi-Factor Authentication konfigurálása a SSMS és az Azure ad-hez](authentication-mfa-ssms-configure.md).

> [!NOTE]
> Ha Ön vendég felhasználó az adatbázisban, meg kell adnia az adatbázishoz tartozó Azure ad-tartománynevet is: válassza az   >  **ad-tartománynév vagy a bérlői azonosító** lehetőséget. Ha meg szeretné keresni a tartománynevet a Azure Portalban, válassza a **Azure Active Directory**  >  **Egyéni tartománynevek** lehetőséget. A C# példa programban nem szükséges tartománynevet biztosítani.

## <a name="c-code-example"></a>C#-kód – példa

> [!NOTE]
> Ha a .NET Core-t használja, a [Microsoft. SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) névteret kell használnia. További információkért tekintse meg a következő [blogot](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

A példában szereplő C#-program a [*Microsoft. IdentityModel. clients. ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory) dll-szerelvényre támaszkodik.

A csomag telepítéséhez a Visual Studióban válassza a **Project**  >  **NuGet-csomagok kezelése** lehetőséget. Keresse meg és telepítse a **Microsoft. IdentityModel. clients. ActiveDirectory**.

Ez egy példa a C# forráskódra.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  .
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  .
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Ez egy példa a C#-teszt kimenetére.

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)