---
title: Dupla titkosítás az adatáttitkosításhoz
titleSuffix: Azure HDInsight
description: Ez a cikk azt a két titkosítási réteget ismerteti, amelyek a fürtökön található, Azure HDInsight adatokhoz érhetők el.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 226516b1178f14789570b45b68cfdbf56f63bbd7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775150"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight adatok kettős titkosítása

Ez a cikk az adattitkosítás módszereit ismerteti az Azure HDInsight fürtökben. Az adattitkosítás a HDInsight-fürt virtuális gépeinek felügyelt lemezei (adatlemezek, operációsrendszer-lemezek és ideiglenes lemezek) titkosítását jelenti. 

Ez a dokumentum nem foglalkozik az Azure Storage-fiókban tárolt adatokkal. A fürtök egy vagy több csatlakoztatott Azure Storage-fiókkal is lehetnek, ahol a titkosítási kulcsok a Microsoft vagy az ügyfél által is felügyeltek lehetnek, de a titkosítási szolgáltatás más. Az Azure Storage-titkosítással kapcsolatos további információkért lásd: [Azure Storage encryption for data at rest (Az Azure Storage titkosítása az adattároláshoz).](../storage/common/storage-service-encryption.md)

## <a name="introduction"></a>Bevezetés

Az Azure-ban három fő felügyeltlemez-szerepkör van: az adatlemez, az operációsrendszer-lemez és az ideiglenes lemez. További információ a felügyelt lemezek különböző típusairól: Bevezetés az [Azure managed disks szolgáltatásba.](../virtual-machines/managed-disks-overview.md) 

A HDInsight több titkosítástípust támogat két különböző rétegben:

- Kiszolgálóoldali titkosítás (SSE) – Az SSE-t a tárolási szolgáltatás végzi. A HDInsightban az SSE az operációsrendszer-lemezek és az adatlemezek titkosítására használható. Alapértelmezés szerint engedélyezve van. Az SSE egy 1. rétegben használt titkosítási szolgáltatás.
- Titkosítás a gazdagépen platform által felügyelt kulccsal – Az SSE-hez hasonlóan ezt a titkosítástípust a tárolási szolgáltatás végzi. Ez azonban csak ideiglenes lemezekhez használható, és alapértelmezés szerint nincs engedélyezve. A gazdagépen való titkosítás egyben az 1. réteg titkosítási szolgáltatása is.
- Titkosítás az ügyfelek által felügyelt kulccsal – Ez a titkosítástípus adatokon és ideiglenes lemezeken is használható. Alapértelmezés szerint nincs engedélyezve, és az ügyfélnek meg kell adnia a saját kulcsát az Azure Key Vaulton keresztül. Az adattitkosítás 2. rétegben található titkosítási szolgáltatás.

Ezeket a típusokat az alábbi táblázat foglalja össze.

|Fürt típusa |Operációsrendszer-lemez (felügyelt lemez) |Adatlemez (felügyelt lemez) |Ideiglenes adatlemez (helyi SSD) |
|---|---|---|---|
|Kafka, HBase gyorsított írási folyamatokkal|1. réteg: [SSE-titkosítás alapértelmezés](../virtual-machines/managed-disks-overview.md#encryption) szerint|1. réteg: [SSE-titkosítás](../virtual-machines/managed-disks-overview.md#encryption) alapértelmezés szerint, 2. réteg: Nem kötelező titkosítás a CMK használatával|1. réteg: Választható titkosítás a gazdagépen a PMK, 2. réteg használatával: Nem kötelező titkosítás a CMK használatával|
|Minden más fürt (Spark, Interactive, Hadoop, HBase gyorsított írások nélkül)|1. réteg: [SSE-titkosítás alapértelmezés](../virtual-machines/managed-disks-overview.md#encryption) szerint|N/A|1. réteg: Választható titkosítás a gazdagépen a PMK, 2. réteg használatával: Nem kötelező titkosítás a CMK használatával|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Titkosítás az ügyfelek által felügyelt kulcsokkal

Az ügyfél által kezelt kulcstitkosítás egy egylépéses folyamat, amely további költségek nélkül kezelhető a fürtök létrehozása során. Mindössze engedélyeznie kell egy felügyelt identitást a Azure Key Vault és hozzá kell adni a titkosítási kulcsot a fürt létrehozásakor.

A fürt egyes csomópontjaiban lévő adatlemezek és ideiglenes lemezek is szimmetrikus adattitkosítási kulccsal (DEK) vannak titkosítva. Az adattitkosítási kulcsot a kulcstartó kulcstitkosítási kulcsával (KEK) védheti. A titkosítási és visszafejtési folyamatokat teljes egészében a Azure HDInsight.

A fürt virtuális gépéhez csatolt operációsrendszer-lemezekhez csak egy titkosítási réteg (PMK) érhető el. Javasoljuk, hogy az ügyfelek ne másoljanak bizalmas adatokat az operációsrendszer-lemezekre, ha CMK-titkosításra van szükség a forgatókönyvükhöz.

Ha a Key Vault tűzfala engedélyezve van a lemeztitkosítási kulcsot tároló kulcstartón, a fürt üzembe helyezéséhez használt régió HDInsight regionális erőforrás-szolgáltatói IP-címeit hozzá kell adni a Key Vault tűzfalkonfigurációjához. Erre azért van szükség, mert a HDInsight nem megbízható Azure Key Vault-szolgáltatás.

A kulcstartó kulcsait Azure Portal Azure CLI használatával biztonságosan elforgathatja. A kulcs elforgatása után a HDInsight-fürt percek alatt elkezdi használni az új kulcsot. Engedélyezze a [Soft Delete](../key-vault/general/soft-delete-overview.md) kulcsvédelmi funkciókat a zsarolóprogram-forgatókönyvek és a véletlen törlés elleni védelem érdekében. A védelmi funkció nélküli kulcstartók nem támogatottak.

### <a name="get-started-with-customer-managed-keys"></a>Ügyfél által kezelt kulcsok – első lépések

Felhasználó által felügyelt kulcsokat engedélyező HDInsight-fürt létrehozásához a következő lépéseket fogjuk követni:

1. Felügyelt identitások létrehozása Azure-erőforrásokhoz
1. Create Azure Key Vault
1. Kulcs létrehozása
1. Hozzáférési szabályzat létrehozása
1. HDInsight-fürt létrehozása engedélyezett ügyfél által felügyelt kulccsal
1. A titkosítási kulcs rotása

Az egyes lépéseket a következő szakaszok részletesen ismertetik.

### <a name="create-managed-identities-for-azure-resources"></a>Felügyelt identitások létrehozása Azure-erőforrásokhoz

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást a Key Vault.

A [konkrét lépésekért lásd:](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) Felhasználó által hozzárendelt felügyelt identitás létrehozása. A felügyelt identitások a felügyelt identitások Azure HDInsight a felügyelt identitások a [Azure HDInsight.](hdinsight-managed-identities.md) Mindenképpen mentse a felügyelt identitás erőforrás-azonosítóját, amikor hozzáadja azt a Key Vault hozzáférési szabályzathoz.

### <a name="create-azure-key-vault"></a>Új Azure Key Vault

Kulcstartó létrehozása. A [konkrét lépésekért Azure Key Vault](../key-vault/general/quick-create-portal.md) lásd: Create Azure Key Vault ( Alkalmazás létrehozása).

A HDInsight csak a Azure Key Vault. Ha saját kulcstartóval is van, importálhatja a kulcsokat a Azure Key Vault. Ne feledje, hogy a kulcstartón engedélyezni kell a **soft delete** beállítást. A meglévő kulcsok importálására vonatkozó további információkért látogasson el a Tudnivalók a kulcsokról, titkos kulcsokról [és tanúsítványokról.](../key-vault/general/about-keys-secrets-certificates.md)

### <a name="create-key"></a>Kulcs létrehozása

1. Az új kulcstartóban lépjen a Beállítások  >  **kulcsok**  >  **+ Generálás/Importálás elemre.**

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Új kulcs létrehozása a Azure Key Vault":::

1. Adjon meg egy nevet, majd válassza a **Létrehozás lehetőséget.** Tartsa meg  az **RSA alapértelmezett kulcstípusát.**

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="létrehoz egy kulcsnevet":::

1. Amikor visszatér a Kulcsok **lapra,** válassza ki a létrehozott kulcsot.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="kulcstartókulcsok listája":::

1. Válassza ki a verziót a **Kulcsverzió lap megnyitásához.** Ha saját kulcsot használ a HDInsight-fürttitkosításhoz, meg kell adnia a kulcs URI-ját. Másolja ki **a kulcsazonosítót,** és mentse valahová, amíg készen nem áll a fürt létrehozására.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="kulcsazonosító lekérte":::

### <a name="create-access-policy"></a>Hozzáférési szabályzat létrehozása

1. Az új kulcstartóban lépjen a **Beállítások hozzáférési**  >  **szabályzatok**  >  **+ Hozzáférési szabályzat hozzáadása elemre.**

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="Új Azure Key Vault hozzáférési szabályzat létrehozása":::

1. A Hozzáférési **szabályzat hozzáadása lapon** adja meg a következő információkat:

    |Tulajdonság |Leírás|
    |---|---|
    |Kulcsengedélyek|Válassza **a Get (Lekért)** **, Unwrap Key (Kulcs kicsomagzása)** és **Wrap Key (Kulcs becsomagolása) lehetőséget.**|
    |Titkos engedélyek|Válassza **a Get (Lekért)**, **Set**(Beállítás) és **Delete (Törlés) lehetőséget.**|
    |Rendszerbiztonsági tag kiválasztása|Válassza ki a korábban létrehozott, felhasználó által hozzárendelt felügyelt identitást.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="A Rendszerbiztonsági tag kiválasztása beállítás Azure Key Vault hozzáférési szabályzathoz":::

1. Válassza a **Hozzáadás** lehetőséget.

1. Kattintson a **Mentés** gombra.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Hozzáférési Azure Key Vault mentése":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Fürt létrehozása ügyfél által felügyelt kulcs lemeztitkosítással

Most már készen áll egy új HDInsight-fürt létrehozására. Az ügyfél által kezelt kulcsok csak a fürt létrehozása során alkalmazhatók új fürtökre. A titkosítás nem távolítható el az ügyfél által felügyelt kulcsfürtökből, és nem lehet ügyfél által felügyelt kulcsokat hozzáadni a meglévő fürtökhöz.

A 2020. novemberi kiadástól kezdve a HDInsight támogatja a fürtök létrehozását verziószámmal és verzióval nem támogatott kulcs URI-ekkel. Ha verziószámmentes kulcs URI-val hozza létre a fürtöt, a HDInsight-fürt megpróbálja automatikusan elforgatni a kulcsokat, amikor a kulcs frissül a Azure Key Vault. Ha verziószámos kulcs URI-val hozza létre a fürtöt, manuális kulcsrotálást kell végrehajtania a titkosítási kulcs rotációja ( ) [útmutatóban tárgyaltak szerint.](#rotating-the-encryption-key)

A 2020. novemberi kiadás előtt létrehozott fürtök esetén manuálisan kell elforgatni a kulcsokat a verziószámos kulcs URI-felhasználásával.

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A fürt létrehozása során a következő módon használhat verziószámos vagy verzió nélküli kulcsot:

- **Verziószámos** – A fürt létrehozása során adja meg a teljes **kulcsazonosítót,** beleértve a kulcs verzióját is. Például: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **Verzió nélküli** – A fürt létrehozása során csak a **kulcsazonosítót adja meg.** Például: `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

Emellett hozzá kell rendelnie a felügyelt identitást a fürthöz.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="Új fürt létrehozása":::

#### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az alábbi példa bemutatja, hogyan használható az Azure CLI egy új Apache Spark fürt létrehozásához engedélyezett lemeztitkosítással. További információ: [Azure CLI az hdinsight create.](/cli/azure/hdinsight#az_hdinsight_create) A paraméter `encryption-key-version` használata nem kötelező.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

Az alábbi példa bemutatja, hogyan hozhat létre új Azure Resource Manager fürtöt egy Apache Spark lemeztitkosítással. További információ: [Mik azok az ARM-sablonok?](../azure-resource-manager/templates/overview.md). A Resource Manager-sablon `diskEncryptionKeyVersion` tulajdonsága nem kötelező.

Ez a példa a PowerShell használatával hívja meg a sablont.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Az erőforrás-kezelési sablon `azuredeploy.json` tartalma:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>A titkosítási kulcs rotása

A futó fürtön használt titkosítási kulcsokat az azure Azure Portal Azure CLI használatával módosíthatja. Ehhez a művelethez a fürtnek hozzá kell férnie az aktuális kulcshoz és a kívánt új kulcshoz is, különben a kulcs elforgatása művelet sikertelen lesz. A 2020. novemberi kiadás után létrehozott fürtök esetén kiválaszthatja, hogy az új kulcsnak legyen-e verziója. A 2020. novemberi kiadás előtt létrehozott fürtök esetén a titkosítási kulcs rotozásakor verziószámos kulcsot kell használnia.

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A kulcs elforgatása az alap key vault URI-ját szükséges. Ha ezzel végzett, a portálon a HDInsight-fürt tulajdonságainak  szakaszában kattintson a Kulcs módosítása elemre a Lemeztitkosítási kulcs **URL-címe területen.** Írja be az új kulcs URL-címét, és küldje el a címet a kulcs elforgatásaként.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="lemeztitkosítási kulcs elforgatása":::

#### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az alábbi példa bemutatja, hogyan forgatható le egy meglévő HDInsight-fürt lemeztitkosítási kulcsa. További információ: [Azure CLI az hdinsight rotate-disk-encryption-key](/cli/azure/hdinsight#az_hdinsight_rotate_disk_encryption_key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Ügyfél által kezelt kulcstitkosítással kapcsolatos gyakori kérdések

**Hogyan fér hozzá a HDInsight-fürt a kulcstartóhoz?**

A HDInsight a Azure Key Vault a HDInsight-fürthöz hozzárendelt felügyelt identitással fér hozzá a Azure Key Vault példányhoz. Ez a felügyelt identitás a fürt létrehozása előtt vagy közben is létre lehet hozva. Emellett hozzáférést kell ad a felügyelt identitásnak ahhoz a kulcstartóhoz, amelyben a kulcs tárolva van.

**Ez a funkció minden HDInsight-fürt számára elérhető?**

Az ügyfél által felügyelt kulcstitkosítás a Spark 2.1 és 2.2 kivételével minden fürttípushoz elérhető.

**Használhatok több kulcsot a különböző lemezek vagy mappák titkosításához?**

Nem, az összes felügyelt és erőforráslemezt ugyanaz a kulcs titkosítja.

**Mi történik, ha a fürt nem fér hozzá a kulcstartóhoz vagy a kulcshoz?**

Ha a fürt nem fér hozzá a kulcshoz, figyelmeztetések jelennek meg az Apache Ambari portálon. Ebben az állapotban a **Kulcs módosítása művelet** sikertelen lesz. A kulcshoz való hozzáférés visszaállítása után az Ambari figyelmeztetései el fognak menni, és az olyan műveletek, mint a kulcsrotáció, sikeresen elvégezhetők.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="kulcselérési Ambari-riasztás":::

**Hogyan állítható helyre a fürt a kulcsok törlésekor?**

Mivel csak a helyreállítható törlést engedélyező kulcsok támogatottak, ha a kulcsokat helyreállítják a kulcstartóban, a fürtnek újra hozzá kell férni a kulcsokhoz. A kulcs Azure Key Vault az [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) vagy [az az-keyvault-key-recover műveletben.](/cli/azure/keyvault/key#az_keyvault_key_recover)


**Ha egy fürt fel van skálázva, az új csomópontok zökkenőmentesen támogatják az ügyfél által kezelt kulcsokat?**

Igen. A fürtnek hozzáférésre van szüksége a kulcstartóban a felskálás során. A rendszer ugyanazt a kulcsot használja a fürt felügyelt lemezei és erőforráslemezei titkosítására is.

**Elérhetők az ügyfél által kezelt kulcsok a saját helyen?**

A HDInsight ügyfél által felügyelt kulcsai minden nyilvános felhőben és országos felhőben elérhetők.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Titkosítás a gazdagépen platform által felügyelt kulcsokkal

### <a name="enable-in-the-azure-portal"></a>Engedélyezés a Azure Portal

A gazdagépen való titkosítás a fürt létrehozásakor engedélyezhető a Azure Portal.

> [!Note]
> Ha a titkosítás engedélyezve van a gazdagépen, nem adhat hozzá alkalmazásokat a HDInsight-fürthöz az Azure Marketplace-ről.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Engedélyezze a titkosítást a gazdagépen.":::

Ez a beállítás engedélyezi [a TITKOSÍTÁSt a gazdagépen](../virtual-machines/disks-enable-host-based-encryption-portal.md) a HDInsight virtuális gépek ideiglenes adatlemezei számára a PMK használatával. A gazdagépen [](../virtual-machines/disks-enable-host-based-encryption-portal.md) való titkosítás csak bizonyos, korlátozott régiókban található virtuálisgép-SKUs esetében támogatott, a HDInsight pedig a következő csomópontkonfigurációkat és [SKUs-okat támogatja.](./hdinsight-supported-node-configuration.md)

A HDInsight-fürthöz megfelelő virtuálisgép-méretért lásd: A megfelelő virtuálisgép-méret kiválasztása [a Azure HDInsight fürthöz.](hdinsight-selecting-vm-size.md) A Zookeeper-csomópont alapértelmezett virtuálisgép-termékváltozata, ha a titkosítás engedélyezve van a gazdagépen, a DS2V2 lesz.

### <a name="enable-using-powershell"></a>Engedélyezés a PowerShell használatával

A következő kódrészlet bemutatja, hogyan hozhat létre olyan új Azure HDInsight fürtöt, amely a PowerShell használatával engedélyezi a titkosítást a gazdagépen. A funkció engedélyezéséhez `-EncryptionAtHost $true` a paramétert használja.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Engedélyezés az Azure CLI használatával

A következő kódrészlet bemutatja, hogyan hozhat létre egy új Azure HDInsight fürtöt, amely titkosítást engedélyez a gazdagépen az Azure CLI használatával. A funkció engedélyezéséhez `--encryption-at-host true` a paramétert használja.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Key Vault: Mi a Azure Key Vault?](../key-vault/general/overview.md)
* [A vállalati biztonság áttekintése a Azure HDInsight.](./domain-joined/hdinsight-security-overview.md)
