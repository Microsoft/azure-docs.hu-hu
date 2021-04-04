---
title: Adattitkosítás – Azure CLI – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan állíthatja be és kezelheti az Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosítását az Azure CLI használatával.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240613"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Adattitkosítás Azure Database for PostgreSQL önálló kiszolgáló számára az Azure CLI használatával

Ismerje meg, hogyan állíthatja be és kezelheti az Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosítását az Azure CLI használatával.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* Hozzon létre egy Key vaultot és egy, az ügyfél által felügyelt kulcshoz használandó kulcsot. A Key vaulton engedélyezze a kiürítést és a helyreállítható törlést is.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* A létrehozott Azure Key Vault hozzon létre egy kulcsot, amelyet a rendszer a Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosításához fog használni.

   ```azurecli-interactive
   az keyvault key create --name <key_name> -p software --vault-name <vault_name>
   ```

* Meglévő kulcstartó használatához a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/general/soft-delete-overview.md)

      ```azurecli-interactive
      az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
      ```

  * [Védett kiürítés](../key-vault/general/soft-delete-overview.md#purge-protection)

      ```azurecli-interactive
      az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
      ```

* A kulcsnak az alábbi attribútumokkal kell rendelkeznie felhasználó által felügyelt kulcsként:
  * Nincs lejárati dátum
  * Nincs letiltva
  * **Lekérési**, **becsomagolási** és **kicsomagolási** műveletek végrehajtása

## <a name="set-the-right-permissions-for-key-operations"></a>A megfelelő engedélyek beállítása a kulcsfontosságú műveletekhez

1. A felügyelt identitást kétféleképpen lehet lekérdezni a Azure Database for PostgreSQL egyetlen kiszolgálóra.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Hozzon létre egy új Azure Database for PostgreSQL-kiszolgálót egy felügyelt identitással.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Egy meglévő Azure Database for PostgreSQL-kiszolgáló frissítése egy felügyelt identitás beszerzéséhez.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Állítsa be a **legfontosabb engedélyeket** (**Get**, **wrap**, **dewrap**) a **rendszerbiztonsági tag** számára, amely a PostgreSQL egykiszolgálós kiszolgáló neve.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Adattitkosítás beállítása Azure Database for PostgreSQL önálló kiszolgálóhoz

1. Engedélyezheti az adattitkosítást a Azure Database for PostgreSQL egyetlen kiszolgálón a Azure Key Vault létrehozott kulccsal.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    Kulcs URL-címe:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Adattitkosítás használata visszaállítási vagy replika kiszolgálók esetén

Miután Azure Database for PostgreSQL egy kiszolgálót a Key Vault tárolt ügyfél felügyelt kulcsával, a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel vagy replika (helyi/régió) művelettel teheti meg. Tehát egy titkosított PostgreSQL egykiszolgálós kiszolgáló esetén a következő lépésekkel hozhat létre titkosított visszaállított kiszolgálót.

### <a name="creating-a-restoredreplica-server"></a>Visszaállított/replika kiszolgáló létrehozása

* [Visszaállítási kiszolgáló létrehozása](howto-restore-server-cli.md)
* [Olvasási replika kiszolgáló létrehozása](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>A kiszolgáló visszaállítása után ellenőrizze újra a visszaállított kiszolgáló adattitkosítását.

*   A replika-kiszolgáló identitásának kiosztása
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   A visszaállított/replika kiszolgálóhoz használandó meglévő kulcs lekérése

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   A visszaállított/replika kiszolgáló új identitására vonatkozó házirend beállítása

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* A visszaállított/replika kiszolgáló újbóli ellenőrzése a titkosítási kulccsal

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>További képesség a Azure Database for PostgreSQL egyetlen kiszolgálóhoz használt kulcshoz

### <a name="get-the-key-used"></a>A használt kulcs lekérése

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

Kulcs URL-címe: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>A használt kulcs listázása

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>A használt kulcs eldobása

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Adattitkosítás engedélyezése Azure Resource Manager sablon használatával

A Azure Portalon kívül a Azure Database for PostgreSQL egyetlen kiszolgálón is engedélyezheti az adattitkosítást az új és a meglévő kiszolgálók Azure Resource Manager sablonjaival.

### <a name="for-a-new-server"></a>Új kiszolgáló esetén

Az egyik előre létrehozott Azure Resource Manager-sablon használatával kiépítheti a kiszolgálót az adattitkosítás engedélyezésével: [példa adattitkosításra](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Ez a Azure Resource Manager sablon létrehoz egy Azure Database for PostgreSQL önálló kiszolgálót, és a **kulcstartót** és a kulcsot adja át paraméterként **az** adattitkosítás engedélyezéséhez a kiszolgálón.

### <a name="for-an-existing-server"></a>Meglévő kiszolgáló esetén
Emellett Azure Resource Manager-sablonokkal is engedélyezheti az adattitkosítást a meglévő Azure Database for PostgreSQL önálló kiszolgálókon.

* Adja meg a korábban a tulajdonságok objektum alatt másolt Azure Key Vault kulcs erőforrás-AZONOSÍTÓját `Uri` .

* Használja az *2020-01-01-Preview API-* verziót.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

 További információ az adattitkosításról: [Azure Database for PostgreSQL egykiszolgálós adattitkosítás az ügyfél által felügyelt kulccsal](concepts-data-encryption-postgresql.md).
