---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244766"
---
Hozzunk létre egy **keresési kifejezésként** nevű titkot, amelynek a **sikere!**. A titkos kód lehet egy jelszó, egy SQL-kapcsolódási karakterlánc vagy bármely egyéb olyan információ, amelyet a biztonságos és az alkalmazás számára elérhetőnek kell tartania. 

Ha titkos kulcsot szeretne felvenni az újonnan létrehozott Key vaultba, használja az Azure CLI az kulcstartó [Secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) parancsot:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```