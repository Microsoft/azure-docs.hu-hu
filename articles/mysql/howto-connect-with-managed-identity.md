---
title: Csatlakozás felügyelt identitással – Azure Database for MySQL
description: Ismerje meg, hogyan csatlakozhat és hitelesítheti magát felügyelt identitással a Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: c9c5c938650d1932349f17bde6b30c65718ef72a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774682"
---
# <a name="connect-with-managed-identity-to-azure-database-for-mysql"></a>Csatlakozás felügyelt identitással az Azure Database for MySQL-hez

Ez a cikk bemutatja, hogyan használhat felhasználó által hozzárendelt identitást egy Azure-beli virtuális géphez (VM) egy Azure Database for MySQL eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. 

Az alábbiak végrehajtásának módját ismerheti meg:

- Hozzáférés megadása a virtuális gép számára egy Azure Database for MySQL kiszolgálóhoz
- A virtuális gép felhasználó által hozzárendelt identitását képviselő felhasználó létrehozása az adatbázisban
- Hozzáférési jogkivonat lekérdezése a virtuálisgép-identitással, majd lekérdezése egy Azure Database for MySQL használatával
- A jogkivonat lekérésének megvalósítása egy C#-példaalkalmazásban

> [!IMPORTANT]
> A felügyelt identitással való csatlakozás csak a MySQL 5.7-es és újabb rendszer esetén érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](../../articles/active-directory/managed-identities-azure-resources/overview.md). Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A szükséges erőforrás-létrehozáshoz és szerepkörkezeléshez a fióknak "Tulajdonos" engedélyekkel kell rendelkeznie a megfelelő hatókörben (az előfizetésben vagy az erőforráscsoportban). Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatos információkhoz, tekintse meg az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez [szükséges Azure-szerepkörök hozzárendelését.](../../articles/role-based-access-control/role-assignments-portal.md)
- Szüksége van egy Azure-beli virtuális gépre (Ubuntu Linux), amely felügyelt identitással szeretné elérni az adatbázist
- Szüksége van egy Azure Database for MySQL-adatbáziskiszolgálóra, amely konfigurálva van [az Azure AD-hitelesítéssel](howto-configure-sign-in-azure-ad-authentication.md)
- A C#-példa követnie kell az útmutatót, amely bemutatja, hogyan csatlakozhat A [C# használatával](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása a virtuális géphez

Hozzon létre egy identitást az előfizetésben [az az identity create paranccsal.](/cli/azure/identity#az_identity_create) Használhatja ugyanazt az erőforráscsoportot, mint a virtuális gép, vagy egy másikat.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Az identitás konfigurálához a következő lépésekben használja az [az identity show](/cli/azure/identity#az_identity_show) parancsot az identitás erőforrás-azonosítójának és ügyfél-azonosítójának változókban való tárolására.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Most már hozzárendelheti a felhasználó által hozzárendelt identitást a virtuális géphez az [az vm identity assign paranccsal:](/cli/azure/vm/identity#az_vm_identity_assign)

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

A beállítás befejezéséhez mutassa meg az ügyfél-azonosító értékét, amelyre a következő néhány lépésben szüksége lesz:

```bash
echo $clientID
```

## <a name="creating-a-mysql-user-for-your-managed-identity"></a>MySQL-felhasználó létrehozása a felügyelt identitáshoz

Most csatlakozzon Az Azure AD rendszergazdai felhasználójaként a MySQL-adatbázishoz, és futtassa a következő SQL-utasításokat:

```sql
SET aad_auth_validate_oids_in_tenant = OFF;
CREATE AADUSER 'myuser' IDENTIFIED BY 'CLIENT_ID';
```

A felügyelt identitás most már hozzáféréssel rendelkezik a felhasználónévvel való hitelesítéskor (cserélje le a helyére `myuser` a választott nevet).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>A hozzáférési jogkivonat leolvasása az Azure Instance Metadata szolgáltatásból

Az alkalmazás mostantól lekérhet egy hozzáférési jogkivonatot az Azure Instance Metadata szolgáltatásból, és felhasználhatja azt az adatbázissal való hitelesítéshez.

A jogkivonat lekérése egy HTTP-kérés a címre való lekérésével és a következő paraméterek `http://169.254.169.254/metadata/identity/oauth2/token` átadása által történik:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (korábban lekért)

Egy mezőt tartalmazó JSON-eredményt fog kapni – ez a hosszú szöveges érték a felügyelt identitás hozzáférési jogkivonata, amely jelszóként használható az `access_token` adatbázishoz való csatlakozáskor.

Tesztelési célokra az alábbi parancsokat futtathatja a rendszerhéjban. Vegye figyelembe, hogy telepítenie kell a `curl` , és a `jq` `mysql` -ügyfelet.

```bash
# Retrieve the access token
accessToken=$(curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token)

# Connect to the database
mysql -h SERVER --user USER@SERVER --enable-cleartext-plugin --password=$accessToken
```

Most már csatlakozott a korábban konfigurált adatbázishoz.

## <a name="connecting-using-managed-identity-in-c"></a>Csatlakozás felügyelt identitással C-ben #

Ez a szakasz bemutatja, hogyan lehet hozzáférési jogkivonatot lehívni a virtuális gép felhasználó által hozzárendelt felügyelt identitásával, és hogyan lehet a jogkivonat használatával Azure Database for MySQL. Azure Database for MySQL támogatja az Azure AD-hitelesítést, így közvetlenül is elfogadhatja az Azure-erőforrások felügyelt identitásai használatával kapott hozzáférési jogkivonatokat. A MySQL-lel való kapcsolat létrehozásakor át kell adni a hozzáférési jogkivonatot a jelszó mezőben.

Az alábbi .NET-kód például a MySQL-lel való kapcsolat hozzáférési jogkivonattal való megnyitására mutat be példát. Ennek a kódnak a virtuális gépen kell futnia a virtuális gép felhasználó által hozzárendelt felügyelt identitásának végpontjának eléréséhez. .NET-keretrendszer hozzáférési jogkivonat-módszerhez 4.6-os vagy újabb, illetve .NET Core 2.2-es vagy újabb verzió szükséges. Cserélje le a HOST, USER, DATABASE és CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using MySql.Data.MySqlClient;

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

        static async Task Main(string[] args)
        {
            //
            // Get an access token for MySQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for MySQL is https://ossrdbms-aad.database.windows.net/
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
            // Open a connection to the MySQL server using the access token.
            //
            var builder = new MySqlConnectionStringBuilder
            {
                Server = Host,
                Database = Database,
                UserID = User,
                Password = accessToken,
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT VERSION()";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine("\nConnected!\n\nMySQL version: {0}", reader.GetString(0));
                        }
                    }
                }
            }
        }
    }
}
```

A futtatáskor ez a parancs a következő kimenetet adja:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

MySQL version: 5.7.27
```

## <a name="next-steps"></a>Következő lépések

* Tekintse át a Azure Active Directory [hitelesítés általános Azure Database for MySQL](concepts-azure-ad-authentication.md)
