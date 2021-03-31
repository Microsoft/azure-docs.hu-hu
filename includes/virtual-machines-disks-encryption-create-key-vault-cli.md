---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 5c17c1b7fed50c311d356aadcb7ca2837cc20abd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92741732"
---
1. Győződjön meg arról, hogy telepítette a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és bejelentkezett egy Azure-fiókba az az [login](/cli/azure/reference-index)paranccsal.

1. Hozzon létre egy Azure Key Vault és egy titkosítási kulcs egy példányát.

    A Key Vault-példány létrehozásakor engedélyeznie kell a Soft delete és a kiürítési védelmet. A helyreállítható törlés biztosítja, hogy a Key Vault a megadott megőrzési időtartam (90 nap alapértelmezett értéke) törölt kulcsát tárolja. A védelem kiürítése biztosítja, hogy a törölt kulcsok ne legyenek véglegesen törölve, amíg a megőrzési időszak megszűnik. Ezek a beállítások a véletlen törlés miatt védik az adatok elvesztését. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához.

    > [!IMPORTANT]
    > Ha ezt a lehetőséget választja, nem kell a Camel-esetet megadnia, ha további lemezeket rendel hozzá az erőforráshoz a Azure Portal.

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

1.    Hozzon létre egy DiskEncryptionSet egy példányát. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Adja meg a DiskEncryptionSet-erőforrás hozzáférését a kulcstartóhoz. 

        > [!NOTE]
        > Eltarthat néhány percig, amíg az Azure létrehozza a DiskEncryptionSet identitását a Azure Active Directoryban. Ha a következő parancs futtatásakor a "nem találja a Active Directory objektumot" hibaüzenet jelenik meg, várjon néhány percet, és próbálkozzon újra.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```
