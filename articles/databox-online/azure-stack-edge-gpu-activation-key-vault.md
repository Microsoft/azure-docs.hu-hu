---
title: Azure Key Vault integrációja Azure Stack Edge erőforrás-és eszköz-aktiválással
description: A cikk azt ismerteti, hogy a Azure Key Vault hogyan van társítva a titkos felügyelethez Azure Stack Edge Pro-eszköz aktiválása során.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c18f875dde43ab6df512375152d76a31c30a9e77
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439867"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Azure Key Vault integráció az Azure Stack Edge-vel 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault integrálva van a Azure Stack Edge-erőforrással a titkos felügyelethez. Ez a cikk részletesen ismerteti, hogyan jön létre a Azure Key Vault az Azure Stack Edge-erőforráshoz az eszköz aktiválása során, majd a titkos felügyelethez. 


## <a name="about-key-vault-and-azure-stack-edge"></a>A Key Vault és a Azure Stack Edge bemutatása

Azure Key Vault Cloud Service a tokenekhez, jelszavakhoz, tanúsítványokhoz, API-kulcsokhoz és egyéb titkokhoz való hozzáférés biztonságos tárolására és szabályozására szolgál. Key Vault emellett megkönnyíti az adattitkosításhoz használt titkosítási kulcsok létrehozását és szabályozását. További információ az engedélyezett tranzakciókról és a kapcsolódó díjakról: [Azure Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

Az Azure Stack Edge szolgáltatás esetében a használt titkok egyike a csatorna integritási kulcsa (a (). Ez a kulcs lehetővé teszi a titkos kódok titkosítását. A Key Vault integrálásával a kiszolgálás biztonságosan tárolódik a Key vaultban. További információ: [titkos kulcsok és kulcsok biztonságos tárolása](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Key Vault létrehozása

A Key Vault a Azure Stack Edge-erőforráshoz jön létre az aktiválási kulcs generálásának folyamata során. 

- Azure Stack Edge-erőforrás létrehozásakor regisztrálnia kell a *Microsoft.* kulcstartó erőforrás-szolgáltatót. Az erőforrás-szolgáltató automatikusan regisztrálva van, ha tulajdonosa vagy közreműködői hozzáfér az előfizetéshez. A Key Vault ugyanabban az előfizetésben és az erőforráscsoporthoz van létrehozva, mint az Azure Stack Edge-erőforrás. 

- Azure Stack Edge-erőforrás létrehozásakor létrejön egy Managed Service Identity (MSI) is, amely az erőforrás élettartamának megőrzését és a felhőben lévő erőforrás-szolgáltatóval való kommunikációt is megőrzi. 

    Ha az MSI engedélyezve van, az Azure megbízható identitást hoz létre az Azure Stack Edge-erőforráshoz.

- Miután létrehozta az Azure Stack Edge-erőforrást, és létrehoz egy aktiválási kulcsot a Azure Portalból, létrejön egy kulcstartó. Ez a kulcstartó a titkos felügyelethez használatos, és mindaddig fennáll, amíg az Azure Stack Edge-erőforrás létezik. 

    ![Az aktiválási kulcs létrehozása során létrehozott Key Vault](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- Dönthet úgy is, hogy elfogadja az alapértelmezett kulcs nevét, vagy megadhatja a kulcstartó egyéni nevét. A kulcstároló nevének 3 – 24 karakter hosszúnak kell lennie. Már használatban lévő kulcstartó nem használható. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![Azure Stack Edge-erőforrás létrehozása során létrehozott MSI](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Az Azure Key Vault tallózásához lépjen a Azure Stack Edge-erőforrás **Tulajdonságok** elemére, és válassza ki a kulcstároló nevét. 

- A véletlen törlés megakadályozása érdekében az erőforrás-zárolás engedélyezve van a kulcstartón. A Key vaultban a Soft delete is engedélyezve van, amely lehetővé teszi, hogy a Key Vault 90 napon belül visszaállítható legyen, ha véletlen törlés van. További információ: Azure Key Vault a [Soft-delete áttekintése](../key-vault/general/soft-delete-overview.md)

    Ha a Key vaultot véletlenül törölte, és a 90 napos kiürítési időtartam nem telt el, a [kulcstartó helyreállításához](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)kövesse az alábbi lépéseket. 

- Ha már rendelkezik egy meglévő Azure Stack Edge-erőforrással, mielőtt a Azure Key Vault integrálva volt az Azure Stack Edge-erőforrással, nem érinti a rendszer. Továbbra is használhatja meglévő Azure Stack Edge-erőforrását. 

- Az Azure Stack Edge-erőforrás törlése után a Azure Key Vault is törlődik az erőforrással. A rendszer megerősítést kér. Ha nem szeretné törölni ezt a kulcstartót, dönthet úgy, hogy nem adja meg a beleegyezett. Csak az Azure Stack Edge-erőforrás törlődik, így a kulcstartó sértetlen marad. 

- Ha ezt a kulcstartót más kulcsok tárolására használták, akkor a törlés 90 napon belül továbbra is visszaállíthatja. A védelmi időszak kiürítése során a kulcstároló neve nem használható új kulcstartó létrehozásához.

Ha a Key vaulttal és az eszköz aktiválásával kapcsolatos problémákba ütközik, tekintse meg az [eszköz aktiválásával kapcsolatos problémák elhárítása](azure-stack-edge-gpu-troubleshoot-activation.md)című témakört.

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Következő lépések

- További információ az [aktiválási kulcs létrehozásáról](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).