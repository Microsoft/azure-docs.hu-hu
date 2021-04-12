---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6745d1f7d97a8f4d08078bf93b61762ab04aad46
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "106072868"
---
Hozzunk létre egy **keresési kifejezésként** nevű titkot, amelynek a **sikere!**. A titkos kód lehet egy jelszó, egy SQL-kapcsolódási karakterlánc vagy bármely egyéb olyan információ, amelyet a biztonságos és az alkalmazás számára elérhetőnek kell tartania. 

Ha titkos kulcsot szeretne hozzáadni az újonnan létrehozott kulcstartóhoz, használja a következő parancsot:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$secret = ConvertTo-SecureString -String 'Success!' -AsPlainText
Set-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret -SecretValue $secret
```
---