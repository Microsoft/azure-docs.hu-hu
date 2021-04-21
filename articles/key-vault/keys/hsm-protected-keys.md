---
title: HSM által & kulcsok átvitele – Azure Key Vault
description: Megtudhatja, hogyan tervezheti meg, hozhatja létre és továbbíthat saját HSM által védett kulcsokat a Azure Key Vault. Más néven BYOK vagy saját kulcs.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: d2719f473caa8d7fa6ce1480a35ddc6cca8a33d6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815629"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM által védett kulcsok importálása a Key Vaultba

A biztonság érdekében az Azure Key Vault hardveres biztonsági modulokban (HSM-ekben) importálhat vagy hozhat létre kulcsokat, amelyek soha nem hagyják el a HSM határait. Ennek a megoldásnak a neve *saját kulcs használata*, angol betűszóval BYOK. Azure Key Vault nCipher nShield HSM-család (FIPS 140-2 2. szint érvényesítve) használatával védi a kulcsokat.

Ez a funkció nem érhető el a Azure China 21Vianet.

> [!NOTE]
> További információ a [Azure Key Vault: Mi a Azure Key Vault?](../general/overview.md)  
> A HSM által védett kulcsok kulcstartóját is tartalmazó első lépésekről a [What is Azure Key Vault? (Mi](../general/overview.md)az a Azure Key Vault? .

## <a name="supported-hsms"></a>Támogatott HSM-ek

A HSM által védett kulcsok Key Vault két különböző módszerrel támogatottak a használt HSM-től függően. Az alábbi táblázat segítségével meghatározhatja, hogy melyik módszert használja a HSM-hez a HSM-hez, majd továbbíthatja a HSM által védett kulcsokat a Azure Key Vault. 

|Szállító neve|Szállító típusa|Támogatott HSM-modellek|Támogatott HSM-kulcsátviteli módszer|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Gyártó<br/>HSM mint szolgáltatás|<ul><li>nShield HSM-család</li><li>nShield mint szolgáltatás</ul>|**1. módszer:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (elavult). Ez a módszer <strong>2021. június 30.</strong> után nem lesz támogatott<br/>**2. módszer:** [Új BYOK metódus használata](hsm-protected-keys-byok.md) (ajánlott)|
|Thales|Gyártó|<ul><li>Luna HSM 7 család 7.3-as vagy újabb belső vezérlőprogrammal</li></ul>| [Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Fortanix|Gyártó<br/>HSM mint szolgáltatás|<ul><li>Self-Defending szolgáltatás (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Marvell|Gyártó|Minden LiquidSecurity HSM a<ul><li>Belső vezérlőprogram 2.0.4-es vagy újabb verziója</li><li>Belső vezérlőprogram 3.2-es vagy újabb verziója</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Titkosítási funkciók|ISV (Vállalati kulcskezelő rendszer)|Több HSM márka és modell, beleértve a következőket:<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Részletekért [lásd: Cryptomathic site (Titkosítási webhely)](https://www.cryptomathic.com/azurebyok)|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Securosys SA|Gyártó<br/>HSM mint szolgáltatás|Primus HSM-család, Securosys Clouds HSM|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Vállalati kulcskezelő rendszer)|Több HSM-márka és modell, köztük<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Részletekért [lásd: StorMagic-webhely](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|IBM|Gyártó|IBM 476x, CryptoExpress|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Utimaco|Gyártó<br/>HSM mint szolgáltatás|u.trust Anchor, CryptoServer|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Következő lépések

* Tekintse át [Key Vault biztonsági áttekintését](../general/security-features.md) a kulcsok biztonságának, tartósságának és monitorozásának biztosításához.
* Az új [BYOK-metódus](./byok-specification.md) teljes leírását a BYOK specifikációja tartalmazza