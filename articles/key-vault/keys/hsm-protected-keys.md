---
title: HSM által védett kulcsok &ának generálása – Azure Key Vault
description: Megtudhatja, hogyan tervezheti meg, generálhatja és továbbíthatja a saját HSM-védelemmel ellátott kulcsait a Azure Key Vault használatával való használatra. Más néven BYOK vagy saját kulcs használata.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/01/2021
ms.author: ambapat
ms.openlocfilehash: 86807899c06cb333238344b7168b5675e8f2e9a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366585"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM által védett kulcsok importálása a Key Vaultba

Ha további garanciára van szüksége, amikor Azure Key Vault használ, a hardveres biztonsági modulokban (HSM) olyan kulcsokat importálhat vagy generálhat, amelyek soha nem hagyják el a HSM-határt. Ennek a megoldásnak a neve *saját kulcs használata*, angol betűszóval BYOK. A Azure Key Vault a HSM (FIPS 140-2 2. szint) nCipher nShield-családját használja a kulcsok elleni védelemhez.

Ez a funkció az Azure China 21Vianet esetében nem érhető el.

> [!NOTE]
> További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../general/overview.md)  
> Az első lépéseket ismertető oktatóanyaghoz, amely magában foglalja a HSM-védelemmel ellátott kulcsok kulcstartójának létrehozását, lásd: [Mi az Azure Key Vault?](../general/overview.md)

## <a name="supported-hsms"></a>Támogatott HSM

A HSM-védelemmel ellátott kulcsok Key Vaultre való átvitele a használt HSM függően két különböző módszer használatával támogatott. Az alábbi táblázat segítségével meghatározhatja, hogy melyik módszert kell használni a HSM létrehozásához, majd átvinni a saját HSM-védelemmel ellátott kulcsait, hogy azok a Azure Key Vault használatával legyenek használatban. 

|Szállító neve|Szállító típusa|Támogatott HSM-modellek|Támogatott HSM-Key átvitel módszer|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Gyártó<br/>HSM szolgáltatásként|<ul><li>HSM nShield családja</li><li>nShield szolgáltatásként</ul>|**1. módszer:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (erős igazolással a kulcs importálásához és a HSM ellenőrzéséhez)<br/>**2. módszer:** [új BYOK metódus használata](hsm-protected-keys-byok.md) |
|Thales|Gyártó|<ul><li>Luna HSM 7 család belső vezérlőprogram 7,3-es vagy újabb verziójával</li></ul>| [Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Fortanix|Gyártó<br/>HSM szolgáltatásként|<ul><li>Self-Defending kulcskezelő szolgáltatás (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Marvell|Gyártó|Az összes LiquidSecurity-HSM a<ul><li>Belső vezérlőprogram verziója 2.0.4 vagy újabb</li><li>Belső vezérlőprogram 3,2-es vagy újabb verziója</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[Részletekért lásd a Cryptomathic-webhelyet](https://www.cryptomathic.com/azurebyok)|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Securosys SA|Gyártó<br/>HSM szolgáltatásként|Primus HSM-család, Securosys felhők HSM|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[Részletekért lásd a StorMagic-webhelyet](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|IBM|Gyártó|IBM 476x, CryptoExpress|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Utimaco|Gyártó<br/>HSM szolgáltatásként|u. Trust-horgony, CryptoServer|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Következő lépések

* A kulcsok biztonságának, tartósságának és figyelésének biztosításához tekintse át a [Key Vault biztonsági áttekintését](../general/security-overview.md) .
* Az új BYOK metódus teljes leírását a [BYOK-specifikációban](./byok-specification.md) találja.