---
title: A Azure Key Vault tárolt titkos kulcs, kulcs vagy tanúsítvány biztonsági mentése | Microsoft Docs
description: Ez a dokumentum segít a Azure Key Vault tárolt titkos kulcs, kulcs vagy tanúsítvány biztonsági mentésében.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b148ac83b89850cad66bcd7254d385e655cc2fb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968747"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault biztonsági mentés

Ebből a dokumentumból megtudhatja, hogyan készíthet biztonsági mentést a Key vaultban tárolt titkokról, kulcsokról és tanúsítványokról. A biztonsági mentés célja, hogy az összes titkos kulcs kapcsolat nélküli példányát adja meg a nem valószínű esetben, ha elveszti a kulcstartóhoz való hozzáférést.

## <a name="overview"></a>Áttekintés

A Azure Key Vault automatikusan biztosít olyan funkciókat, amelyek segítenek fenntartani a rendelkezésre állást, és megelőzni az adatvesztést. A titkok biztonsági mentése csak akkor, ha kritikus üzleti indoklással rendelkezik. A Key vaultban található titkos kulcsok biztonsági mentése olyan működési kihívásokat jelenthet, mint például a naplók, engedélyek és biztonsági másolatok több készletének fenntartása a titkok lejárta vagy elforgatása során.

Key Vault megtartja a rendelkezésre állást a katasztrófa-forgatókönyvekben, és a felhasználó beavatkozása nélkül automatikusan felveszi a kéréseket egy párosított régióba. További információ: [Azure Key Vault rendelkezésre állás és redundancia](./disaster-recovery-guidance.md).

Ha a titkos kódok véletlen vagy rosszindulatú törlésével szembeni védelmet szeretne védeni, konfigurálja a Key vaulton a helyreállítható törlési és kiürítő védelmi funkciókat. További információ: [Azure Key Vault Soft-delete áttekintése](./soft-delete-overview.md).

## <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> A Key Vault nem támogatja a kulcs-, titkos vagy tanúsítvány-objektum 500-nál több korábbi verziójának biztonsági mentését. A kulcsok, titkos kód vagy tanúsítvány objektum biztonsági mentésére tett kísérlet hibát okozhat. A kulcsok, titkos kódok vagy tanúsítványok korábbi verzióit nem lehet törölni.

Key Vault jelenleg nem biztosít egy teljes kulcstartó biztonsági mentését egyetlen művelettel. A jelen dokumentumban felsorolt parancsok a Key Vault automatikus biztonsági mentésére tett kísérletek hibát okozhatnak, és nem támogatják a Microsoft vagy a Azure Key Vault csapata. 

Vegye figyelembe a következő következményeket is:

* A több verziót tartalmazó titkok biztonsági mentése időtúllépési hibát okozhat.
* A biztonsági másolat egy időponthoz tartozó pillanatképet hoz létre. A Titkok a biztonsági mentés során megújítható, így a titkosítási kulcsok nem egyeznek.
* Ha túllépi a Key Vault szolgáltatási korlátait a másodpercenkénti kérelmeknél, a kulcstartó szabályozása megtörténik, és a biztonsági mentés sikertelen lesz.

## <a name="design-considerations"></a>Kialakítási szempontok

A Key Vault-objektumok (például titkos kulcsok, kulcsok vagy tanúsítványok) biztonsági mentésekor a biztonsági mentési művelet titkosított blobként tölti le az objektumot. Ezt a blobot nem lehet visszafejteni az Azure-on kívül. Ahhoz, hogy a blob használható legyen, vissza kell állítania a blobot az Azure-előfizetésben és az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/)található kulcstartóba.

## <a name="prerequisites"></a>Előfeltételek

A Key Vault-objektumok biztonsági mentéséhez a következőket kell tennie: 

* Közreműködő vagy magasabb szintű engedélyek egy Azure-előfizetéshez.
* Egy elsődleges kulcstartó, amely tartalmazza azokat a titkokat, amelyekről biztonsági másolatot szeretne készíteni.
* Másodlagos kulcstartó, ahol a titkos kulcsok vissza lesznek állítva.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Biztonsági mentés és visszaállítás a Azure Portal

Az ebben a szakaszban ismertetett lépéseket követve biztonsági másolatot készíthet és állíthat vissza objektumokat a Azure Portal használatával.

### <a name="back-up"></a>Biztonsági mentés

1. Nyissa meg az Azure Portalt.
2. Válassza ki a kulcstartót.
3. Nyissa meg a biztonsági mentéshez használni kívánt objektumot (titkos kulcs, kulcs vagy tanúsítvány).

    ![Képernyőfelvétel: a kulcsok beállításának és a Key Vault objektumának kijelölése.](../media/backup-1.png)

4. Válassza ki az objektumot.
5. Válassza a **biztonsági mentés letöltése** lehetőséget.

    ![A Key vaultban a Backup letöltése gomb kiválasztásának helyét bemutató képernyőkép.](../media/backup-2.png)
    
6. Válassza a **Letöltés** lehetőséget.

    ![Képernyőfelvétel: a Key vaultban a letöltés gomb kiválasztásának helyét jeleníti meg.](../media/backup-3.png)
    
7. Tárolja a titkosított blobot biztonságos helyen.

### <a name="restore"></a>Visszaállítás

1. Nyissa meg az Azure Portalt.
2. Válassza ki a kulcstartót.
3. Lépjen a visszaállítani kívánt objektum típusára (titkos kulcs, kulcs vagy tanúsítvány).
4. Válassza a **biztonsági mentés visszaállítása** lehetőséget.

    ![Képernyőfelvétel: a biztonsági mentés visszaállításának helye a kulcstartóban.](../media/backup-4.png)
    
5. Lépjen arra a helyre, ahol a titkosított blobot tárolta.
6. Válassza az **OK** lehetőséget.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Biztonsági mentés és visszaállítás az Azure CLI-ből vagy Azure PowerShell

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

Key Vault [naplózásának és figyelésének](./logging.md) bekapcsolása.