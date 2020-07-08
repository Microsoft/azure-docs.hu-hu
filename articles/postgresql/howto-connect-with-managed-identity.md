---
title: Kapcsolat felügyelt identitással – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogyan csatlakozhat és végezhet hitelesítést felügyelt identitás használatával a hitelesítéshez Azure Database for PostgreSQL
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 01a27a9c98c1c429cdc381ba0c1e9ef4186c9e7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83664724"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Kapcsolódás felügyelt identitással Azure Database for PostgreSQL

Ez a cikk bemutatja, hogyan használható egy Azure-beli virtuális gép (VM) felhasználó által hozzárendelt identitása egy Azure Database for PostgreSQL-kiszolgáló elérésére. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A virtuális gép hozzáférésének biztosítása egy Azure Database for PostgreSQL-kiszolgálóhoz
> * Hozzon létre egy felhasználót a virtuális gép felhasználó által hozzárendelt identitását képviselő adatbázisban
> * Hozzáférési jogkivonat beszerzése a virtuálisgép-identitás használatával és a Azure Database for PostgreSQL-kiszolgáló lekérdezéséhez
> * Token lekérésének implementálása C#-alkalmazásban

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](../../articles/active-directory/managed-identities-azure-resources/overview.md). Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A szükséges erőforrás-létrehozási és szerepkör-kezelés végrehajtásához a fióknak a megfelelő hatókörben (az előfizetésben vagy az erőforráscsoportban) a tulajdonos engedélyekkel kell rendelkeznie. Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatban, tekintse meg [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel](../../articles/role-based-access-control/role-assignments-portal.md) részben leírtakat.
- Szüksége lesz egy Azure-beli virtuális gépre (például a Ubuntu Linux), amelyet az adatbázis felügyelt identitással való eléréséhez szeretne használni.
- Szüksége van egy Azure Database for PostgreSQL adatbázis-kiszolgálóra, amelyen konfigurálva van az [Azure ad-hitelesítés](howto-configure-sign-in-aad-authentication.md)
- A C# példájának követéséhez először végezze el a [Kapcsolódás a c#](connect-csharp.md) használatával című útmutatót.

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Felhasználóhoz rendelt felügyelt identitás létrehozása a virtuális géphez

Hozzon létre egy identitást az előfizetésben az az [Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) paranccsal. Használhatja ugyanazt az erőforráscsoportot, amelyben a virtuális gép fut, vagy egy másik.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Az identitásnak az alábbi lépésekben való konfigurálásához használja az az [Identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) parancsot az identitás erőforrás-azonosítójának és ügyfél-azonosítójának a változókban való tárolásához.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Most már hozzárendelheti a felhasználó által hozzárendelt identitást a virtuális géphez az az [VM Identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) paranccsal:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

A telepítés befejezéséhez jelenítse meg az ügyfél-azonosító értékét, amelyet a következő néhány lépésben kell megadnia:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>PostgreSQL-felhasználó létrehozása a felügyelt identitáshoz

Most kapcsolódjon az Azure AD-rendszergazda felhasználóként a PostgreSQL-adatbázishoz, és futtassa a következő SQL-utasításokat:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

A felügyelt identitás most már rendelkezik hozzáféréssel a felhasználónévvel való hitelesítéshez `myuser` (a helyére a választott nevet kell cserélni).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>A hozzáférési jogkivonat lekérése az Azure instance metadata szolgáltatásból

Az alkalmazás mostantól lekérhet egy hozzáférési jogkivonatot az Azure példány metaadatainak szolgáltatásból, és az adatbázissal való hitelesítéshez használhatja azt.

A jogkivonat lekérése egy HTTP-kérelem küldésével `http://169.254.169.254/metadata/identity/oauth2/token` és a következő paraméterek átadásával történik:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID`(a korábban lekért)

Egy olyan JSON-eredményt kap, amely egy `access_token` mezőt tartalmaz – ez a hosszú szöveges érték a felügyelt identitás-hozzáférési jogkivonat, amelyet jelszóként kell használni az adatbázishoz való csatlakozáskor.

Tesztelési célból a következő parancsokat futtathatja a rendszerhéjban. Megjegyzés:, `curl` `jq` és a- `psql` ügyfél telepítve van.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Ezzel csatlakozott a korábban konfigurált adatbázishoz.

## <a name="connecting-using-managed-identity-in-c"></a>Csatlakozás felügyelt identitás használatával C-ben #

Ez a szakasz bemutatja, hogyan kérhet hozzáférési tokent a virtuális gép felhasználó által hozzárendelt felügyelt identitásával, és hogyan hívhatja Azure Database for PostgreSQL. Azure Database for PostgreSQL natív módon támogatja az Azure AD-hitelesítést, így közvetlenül el tudja fogadni az Azure-erőforrások felügyelt identitásával kapott hozzáférési jogkivonatokat. A PostgreSQL-kapcsolat létrehozásakor a jelszó mezőben adja meg a hozzáférési jogkivonatot.

Az alábbi .NET-kód példa arra, hogy egy hozzáférési jogkivonat használatával nyisson meg egy kapcsolatot a PostgreSQL-lel. Ennek a kódnak a virtuális gépen kell futnia a virtuális gép felhasználó által hozzárendelt felügyelt identitási végpontjának eléréséhez. A hozzáférési jogkivonat metódusának használatához a .NET-keretrendszer 4,6-es vagy újabb verziójára vagy a .NET Core 2,2-es vagy újabb verziójára van szükség. Cserélje le a GAZDAGÉP, a felhasználó, az adatbázis és a CLIENT_ID értékeit.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
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
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

A futtatáskor a parancs a következőhöz hasonló kimenetet fog adni:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>További lépések

* Tekintse át a [Azure Active Directory hitelesítéssel](concepts-aad-authentication.md) kapcsolatos általános fogalmakat Azure Database for PostgreSQL
