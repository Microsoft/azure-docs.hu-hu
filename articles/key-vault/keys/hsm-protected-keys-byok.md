---
title: HSM által & kulcsok átvitele – BYOK – Azure Key Vault
description: Ez a cikk segítséget nyújt a HSM által védett saját kulcsok tervezésében, létrehozásában és átvitelében a Azure Key Vault. Más néven bring your own key (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: f1b5d6499594e9026e1615be5361c52c9ce2f4ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774808"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>HSM által védett kulcsok importálása Key Vault (BYOK)

A hardveres biztonsági modul (HSM Azure Key Vault kulcs importálása vagy létrehozása nagyobb biztonságot nyújt; A kulcs soha nem hagyja el a HSM határait. Ezt a forgatókönyvet gyakran saját kulcs (BYOK) *néven is nevezik.* Key Vault nCipher nShield HSM-család (FIPS 140-2 2. szint érvényesítve) használatával védi a kulcsokat.

A cikkben található információk segítségével megtervheti, létrehozhatja és áthelyezheti saját HSM által védett kulcsait a Azure Key Vault.

> [!NOTE]
> Ez a funkció nem érhető el a Azure China 21Vianet. 
> 
> Ez az importálási módszer csak a támogatott [HSM-ek esetén érhető el.](#supported-hsms) 

További információért és az Key Vault használatának első Key Vault oktatóanyagért (beleértve a HSM által védett kulcsokhoz való kulcstartók létrehozásáról) lásd: Mi az a [Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Áttekintés

Az alábbi áttekintés a folyamatról nyújt áttekintést. A teljesítendött lépéseket a cikk későbbi, részletesen ismerteti.

* A Key Vault hozzon létre egy kulcsot (más néven *kulcscserekulcsot* (KEK). A KEK-nek olyan RSA-HSM-kulcsnak kell lennie, amely csak a `import` kulcsművelettel rendelkezik. Csak Key Vault prémium szintű termékváltozat támogatja az RSA-HSM-kulcsokat.
* Töltse le a nyilvános KEK-kulcsot .pem-fájlként.
* A nyilvános KEK-kulcs átvitele egy helyszíni HSM-hez csatlakoztatott offline számítógépre.
* Az offline számítógépen a HSM szállítója által biztosított BYOK eszközzel hozzon létre egy BYOK-fájlt. 
* A célkulcs KEK-el van titkosítva, amely addig titkosítva marad, amíg át nem kerül a Key Vault HSM-be. Csak a kulcs titkosított verziója hagyja el a helyszíni HSM-et.
* A HSM-Key Vault létrehozott KEK nem exportálható. A HSM-ek kikényszeríteni azt a szabályt, hogy a KEK nem létezik a HSM Key Vault kívül.
* A KEK-nek ugyanabban a kulcstartóban kell lennie, ahol a célkulcs importálva lesz.
* Amikor feltölti a BYOK-fájlt az Key Vault-be, a Key Vault HSM a KEK titkos kulccsal visszafejti a célkulcsanyagot, és importálja HSM-kulcsként. Ez a művelet teljes egészében a HSM Key Vault belül történik. A célkulcs mindig a HSM védelmi határán belül marad.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi táblázat a BYOK használatának előfeltételeit sorolja Azure Key Vault:

| Követelmény | További információ |
| --- | --- |
| Azure-előfizetés |Kulcstartó létrehozásához a Azure Key Vault szüksége lesz egy Azure-előfizetésre. [Regisztráljon egy ingyenes próbaverzióra.](https://azure.microsoft.com/pricing/free-trial/) |
| Egy Key Vault prémium termékváltozat A HSM által védett kulcsok importálása |További információ a szolgáltatási szintekről és képességekről a Azure Key Vault: [Key Vault díjszabása.](https://azure.microsoft.com/pricing/details/key-vault/) |
| Egy HSM a támogatott HSM-listában, valamint egy BYOK-eszköz és a HSM szállítója által biztosított utasítások | Rendelkeznie kell a HSM-hez szükséges engedélyekkel, valamint a HSM használatának alapszintű ismeretével. Lásd: [Támogatott HSM-ek.](#supported-hsms) |
| Az Azure CLI 2.1.0-s vagy újabb verziója | Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)|

## <a name="supported-hsms"></a>Támogatott HSM-ek

|Szállító neve|Szállító típusa|Támogatott HSM-modellek|További információ|
|---|---|---|---|
|nCipher|Gyártó<br/>HSM mint szolgáltatás|<ul><li>nShield HSM-család</li><li>nShield mint szolgáltatás</ul>|[nCipher new BYOK tool and documentation (Új BYOK-eszköz és -dokumentáció)](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Gyártó|<ul><li>Luna HSM 7 család 7.3-as vagy újabb belső vezérlőprogrammal</li></ul>| [Luna BYOK eszköz és dokumentáció](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Gyártó<br/>HSM mint szolgáltatás|<ul><li>Self-Defending szolgáltatás (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS-kulcsok exportálása felhőszolgáltatókba BYOK-hoz – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Gyártó|Minden LiquidSecurity HSM a<ul><li>Belső vezérlőprogram 2.0.4-es vagy újabb verziója</li><li>Belső vezérlőprogram 3.2-es vagy újabb verziója</li></ul>|[A Andl BYOK eszköz és dokumentációja](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Titkosítási funkciók|ISV (Vállalati kulcskezelő rendszer)|Több HSM márka és modell, beleértve a következőket:<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>A [részletekért lásd: Cryptomathic site (Titkosítási webhely)](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK eszköz és dokumentáció](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Gyártó<br/>HSM mint szolgáltatás|Primus HSM-család, Securosys Clouds HSM|[Primus BYOK eszköz és dokumentáció](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Vállalati kulcskezelő rendszer)|Több HSM márka és modell, beleértve a következőket:<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>A [részletekért lásd: StorMagic-webhely](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS és Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Gyártó|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Gyártó<br/>HSM mint szolgáltatás|u.trust Anchor, CryptoServer|[Utimaco BYOK eszköz és integrációs útmutató](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Támogatott kulcstípusok

|Kulcs neve|Kulcs típusa|Kulcsméret/görbe|Forrás|Description|
|---|---|---|---|---|
|Kulcscserekulcs (KEK)|RSA| 2048 bites<br />3072 bites<br />4096 bites|Azure Key Vault HSM|Egy HSM-háttéren létrehozott RSA-kulcspár a Azure Key Vault|
|Célkulcs|
||RSA|2048 bites<br />3072 bites<br />4096 bites|Szállítói HSM|A HSM-be Azure Key Vault kulcs|
||EC|P-256<br />P-384<br />P-521|Szállítói HSM|A HSM-be Azure Key Vault kulcs|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Kulcs létrehozása és átvitele a Key Vault HSM-be

A kulcs létrehozása és átvitele egy Key Vault HSM-be:

* [1. lépés: KEK létrehozása](#step-1-generate-a-kek)
* [2. lépés: A nyilvános KEK-kulcs letöltése](#step-2-download-the-kek-public-key)
* [3. lépés: A kulcs létrehozása és előkészítése az átvitelhez](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. lépés: A kulcs átvitele a Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1. lépés: KEK létrehozása

A KEK egy olyan RSA-kulcs, amely egy HSM Key Vault jön létre. A KEK az importálni kívánt kulcs (a célkulcs) *titkosítására* használható.

A KEK-nek a következőnek kell lennie:
- RsA-HSM-kulcs (2048 bites; 3072 bites; vagy 4096 bites)
- Ugyanabban a kulcstartóban jön létre, ahol a célkulcsot importálni kívánja
- Létrehozva az engedélyezett kulcsműveletekkel. `import`

> [!NOTE]
> A KEK-nek "import" művelettel kell lennie az egyetlen engedélyezett kulcsműveletként. Az "importálás" minden más kulcsfontosságú művelettel kölcsönösen kizárható.

Az [az keyvault key create paranccsal](/cli/azure/keyvault/key#az_keyvault_key_create) hozzon létre egy KEK-et, amely kulcsműveletei a következőre vannak beállítva: `import` . Rögzítse a következő parancs által visszaadott kulcsazonosítót ( `kid` ). (A `kid` [3.](#step-3-generate-and-prepare-your-key-for-transfer)lépésben megadott értéket fogja használni.)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>2. lépés: A nyilvános KEK-kulcs letöltése

Az [az keyvault key download használatával](/cli/azure/keyvault/key#az_keyvault_key_download) töltse le a nyilvános KEK-kulcsot egy .pem-fájlba. Az importált célkulcs titkosítása a nyilvános KEK-kulccsal történik.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

A KEKforBYOK.publickey.pem fájl átvitele offline számítógépre. Erre a fájlra szüksége lesz a következő lépésben.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3. lépés: A kulcs létrehozása és előkészítése az átvitelhez

A BYOK eszköz letöltéséhez és telepítéséhez tekintse meg a HSM gyártójának dokumentációját. Kövesse a HSM szállítójának utasításait a célkulcs létrehozásához, majd hozzon létre egy kulcsátviteli csomagot (BYOK-fájlt). A BYOK eszköz az 1. lépésből származó és a `kid` [2.](#step-2-download-the-kek-public-key) lépésben letöltött KEKforBYOK.publickey.pem fájlt fogja használni a titkosított célkulcs BYOK-fájlban való létrehozásához. [](#step-1-generate-a-kek)

A BYOK-fájl átvitele a csatlakoztatott számítógépre.

> [!NOTE] 
> Az RSA 1024 bites kulcsok importálása nem támogatott. Az elliptikus görbe kulcs P-256K görbével történő importálása nem támogatott.
> 
> **Ismert probléma:** Az RSA 4K célkulcs Luna HSM-ről való importálása csak a 7.4.0-s vagy újabb belső vezérlőprogrammal támogatott.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4. lépés: A kulcs átvitele a Azure Key Vault

A kulcs importálásának befejezéséhez átvitelt kell végrehajtania a kulcsátviteli csomaggal (egy BYOK-fájllal) a leválasztott számítógépről az internetkapcsolattal csatlakozó számítógépre. Az [az keyvault key import paranccsal](/cli/azure/keyvault/key#az_keyvault_key_import) töltse fel a BYOK-fájlt az Key Vault HSM-be.

RSA-kulcs importáláshoz használja a következő parancsot. A --kty paraméter nem kötelező, és az alapértelmezett értéke "RSA-HSM".
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

EC-kulcs importálása során meg kell adnia a kulcs típusát és a görbe nevét.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Ha a feltöltés sikeres, az Azure CLI megjeleníti az importált kulcs tulajdonságait.

## <a name="next-steps"></a>Következő lépések

Most már használhatja ezt a HSM által védett kulcsot a kulcstartóban. További információért tekintse meg ezt az [árat és a funkciók összehasonlítását.](https://azure.microsoft.com/pricing/details/key-vault/)
