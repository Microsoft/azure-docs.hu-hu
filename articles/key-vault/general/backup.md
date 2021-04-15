---
title: Biztonsági alkalmazáson tárolt titkos kulcs, kulcs vagy tanúsítvány Azure Key Vault | Microsoft Docs
description: Ezzel a dokumentummal biztonsági mentése segít a titkos kulcsok, kulcsok vagy tanúsítványok biztonsági Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 399f55d379f99a784fed97d7e9f72c456eb1f914
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484812"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault biztonsági mentés

Ez a dokumentum bemutatja, hogyan lehet biztonsági mentése a kulcstartóban tárolt titkos kulcsokról, kulcsokról és tanúsítványokról. A biztonsági mentés célja, hogy offline másolatot biztosítson az összes titkos kulcsról, ha nem valószínű, hogy elveszíti a kulcstartóhoz való hozzáférést.

## <a name="overview"></a>Áttekintés

Azure Key Vault a rendszer automatikusan biztosít olyan szolgáltatásokat, amelyek segítenek fenntartani a rendelkezésre állást és megelőzni az adatvesztést. A titkos kulcsokról csak akkor kell biztonsági mentetni, ha kritikus üzleti indoka van. A titkos kulcsok key vaultban való biztonsági mentése olyan üzemeltetési kihívásokat okozhat, mint például a naplók, engedélyek és biztonsági másolatok több készletének karbantartása, amikor a titkos kulcsok lejárnak vagy elforgatva vannak.

Key Vault fenntartja a rendelkezésre állást katasztrófahelyzetekben, és automatikusan átveszi a kéréseket egy párosított régióba a felhasználó beavatkozása nélkül. További információ: Azure Key Vault rendelkezésre állás [és redundancia.](./disaster-recovery-guidance.md)

Ha védelmet szeretne a titkos kulcsok véletlen vagy rosszindulatú törlése ellen, konfigurálja a kulcstartóban a törlés és végleges törlés elleni védelmet. További információ: Azure Key Vault [törlés áttekintése.](./soft-delete-overview.md)

## <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> Key Vault nem támogatja a kulcs, titkos kulcs vagy tanúsítványobjektum több mint 500 korábbi verziójának biztonsági mentését. Ha egy kulcsról, titkos kulcsról vagy tanúsítványobjektumról próbál biztonsági másolatot készíteni, az hibát eredményezhet. Kulcs, titkos kulcs vagy tanúsítvány korábbi verziói nem törölhetők.

Key Vault jelenleg nem biztosít lehetőséget egy teljes kulcstartó biztonsági végrehajtásához egyetlen műveletben. Ha a dokumentumban felsorolt parancsokat egy kulcstartó automatikus biztonsági mentésére próbálja használni, az hibákat eredményezhet, és a Microsoft vagy az Azure Key Vault csapata nem támogatja. 

Vegye figyelembe a következő következményeket is:

* A több verzióval rendelkező titkos kulcsok biztonságiása időkorreklú hibákat okozhat.
* A biztonsági másolatok egy adott időponthoz készült pillanatképet hoznak létre. A titkos kulcsok a biztonsági mentés során újulnak meg, ami a titkosítási kulcsok eltérését okozhatja.
* Ha túllépi a kérelmek másodpercenkénti Key Vault-szolgáltatási korlátait, a rendszer le fogja korlátozza a kulcstartót, és a biztonsági mentés sikertelen lesz.

## <a name="design-considerations"></a>Kialakítási szempontok

Amikor biztonsági másolatot készít egy Key Vault-objektumról, például egy titkos kulcsról, kulcsról vagy tanúsítványról, a biztonsági mentési művelet titkosított blobként fogja letölteni az objektumot. Ezt a blobot nem lehet visszafejteni az Azure-n kívül. Ha ebből a blobból használható adatokat keres, vissza kell állítania a blobot egy kulcstartóba ugyanabban az Azure-előfizetésben és [Azure-beli földrajzi helyen.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Előfeltételek

Egy Key Vault-objektum biztonsági biztonsági ának a következővel kell lennie: 

* Közreműködői szintű vagy magasabb szintű engedélyek egy Azure-előfizetésben.
* Egy elsődleges kulcstartó, amely tartalmazza azokat a titkos kulcsokat, amelyekről biztonsági szeretne biztonsági adatokat.
* Egy másodlagos kulcstartó, amelyben a titkos kulcsok vissza lesznek állítani.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Biztonsági mentés és visszaállítás a Azure Portal

Kövesse az ebben a szakaszban található lépéseket az objektumok biztonsági mentéséhez és visszaállításához a Azure Portal.

### <a name="back-up"></a>Biztonsági mentés

1. Nyissa meg az Azure Portalt.
2. Válassza ki a kulcstartót.
3. Ugrás arra az objektumra (titkos kulcsra, kulcsra vagy tanúsítványra), amelyről biztonsági szeretne biztonsági mentni.

    ![Képernyőkép a Kulcsok beállítás és egy kulcstartó objektumának kiválasztásáról.](../media/backup-1.png)

4. Jelölje ki az objektumot.
5. Válassza a **Biztonsági mentés letöltése lehetőséget.**

    ![Képernyőkép a Kulcstartó Biztonsági másolat letöltése gombjának kiválasztásáról.](../media/backup-2.png)
    
6. Válassza a **Letöltés** lehetőséget.

    ![Képernyőkép a Kulcstartó Letöltés gombjának kiválasztásáról.](../media/backup-3.png)
    
7. Tárolja a titkosított blobot egy biztonságos helyen.

### <a name="restore"></a>Visszaállítás

1. Nyissa meg az Azure Portalt.
2. Válassza ki a kulcstartót.
3. Ugrás a visszaállítani kívánt objektumtípusra (titkos kulcs, kulcs vagy tanúsítvány).
4. Válassza a **Biztonsági másolat visszaállítása lehetőséget.**

    ![Képernyőkép a Biztonsági másolat visszaállítása kulcstartóban való kiválasztásáról.](../media/backup-4.png)
    
5. Ugrás arra a helyre, ahol a titkosított blobot tárolta.
6. Válassza az **OK** lehetőséget.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Biztonsági mentés és visszaállítás az Azure CLI-ről vagy Azure PowerShell

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```azurepowershell
## Log in to Azure
Connect-AzAccount

## Set your subscription
Set-AzContext -Subscription '{AZURE SUBSCRIPTION ID}'

## Back up a certificate in Key Vault
Backup-AzKeyVaultCertificate -VaultName '{Certificate Name}' -Name '{Key Vault Name}'

## Back up a key in Key Vault
Backup-AzKeyVaultKey -VaultName '{Key Name}' -Name '{Key Vault Name}'

## Back up a secret in Key Vault
Backup-AzKeyVaultSecret -VaultName '{Key Vault Name}' -Name '{Secret Name}'

## Restore a certificate in Key Vault
Restore-AzKeyVaultCertificate -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a key in Key Vault
Restore-AzKeyVaultKey -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a secret in Key Vault
Restore-AzKeyVaultSecret -VaultName '{Key Vault Name}' -InputFile '{File Path}'
```
---

## <a name="next-steps"></a>Következő lépések

Kapcsolja be [a naplózást és a monitorozást](./logging.md) a Key Vault.
