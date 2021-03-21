---
title: A kettős titkosítás engedélyezése a REST-ben – Azure CLI által felügyelt lemezek
description: A felügyelt lemez adataihoz az Azure CLI használatával engedélyezze a kettős titkosítást.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 266ba4a6fbe0607fd09f86f5cd01addfa60252aa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558321"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>A kettős titkosítás engedélyezése a felügyelt lemezeken az Azure CLI használatával

Azure Disk Storage támogatja a kettős titkosítást a felügyelt lemezeken. A kettős titkosítással kapcsolatos fogalmakról, valamint a felügyelt lemezes titkosítási típusokról további információt a lemezes titkosításról szóló cikkben, a [Rest-titkosítást](../disk-encryption.md#double-encryption-at-rest) ismertető szakaszban talál.

## <a name="prerequisites"></a>Előfeltételek

Telepítse a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

## <a name="getting-started"></a>Első lépések

1. Hozzon létre egy Azure Key Vault és egy titkosítási kulcs egy példányát.

    A Key Vault-példány létrehozásakor engedélyeznie kell a Soft delete és a kiürítési védelmet. A helyreállítható törlés biztosítja, hogy a Key Vault a megadott megőrzési időtartam (90 nap alapértelmezett értéke) törölt kulcsát tárolja. A védelem kiürítése biztosítja, hogy a törölt kulcsok ne legyenek véglegesen törölve, amíg a megőrzési időszak megszűnik. Ezek a beállítások a véletlen törlés miatt védik az adatok elvesztését. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Hozzon létre egy DiskEncryptionSet a encryptionType set EncryptionAtRestWithPlatformAndCustomerKeys néven. Használja az **2020-05-01** -es API-verziót a Azure Resource Manager-(ARM-) sablonban. 
    
        ```azurecli
        az deployment group create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Adja meg a DiskEncryptionSet-erőforrás hozzáférését a kulcstartóhoz. 

        > [!NOTE]
        > Eltarthat néhány percig, amíg az Azure létrehozza a DiskEncryptionSet identitását a Azure Active Directoryban. Ha a következő parancs futtatásakor a "nem találja a Active Directory objektumot" hibaüzenet jelenik meg, várjon néhány percet, és próbálkozzon újra.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta és konfigurálta ezeket az erőforrásokat, a segítségével biztonságossá teheti a felügyelt lemezeket. Az alábbi hivatkozások olyan parancsfájlokat tartalmaznak, amelyek mindegyike megfelelő forgatókönyvekkel rendelkezik, amelyek segítségével biztonságossá teheti a felügyelt lemezeket.

- [Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Ügyfelek által felügyelt kulcsok engedélyezése kiszolgálóoldali titkosítással – példák](disks-enable-customer-managed-keys-cli.md#examples)
