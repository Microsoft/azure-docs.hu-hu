---
title: HSM által védett kulcsok &ának generálása – BYOK – Azure Key Vault
description: Ennek a cikknek a segítségével megtervezheti, létrehozhatja és átviheti a saját HSM-védelemmel ellátott kulcsait a Azure Key Vault használatával való használatra. Más néven a saját kulcs használata (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 4a3eaddd160acb8d4d2ae9f0da43ce6cb0236055
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198149"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>HSM-védelemmel ellátott kulcsok importálása a Key Vaultba (BYOK)

A Azure Key Vault használata esetén a rendszer a hardveres biztonsági modulban (HSM) is importálhat vagy létrehozhat egy kulcsot. a kulcs soha nem hagyja el a HSM határt. Ezt a forgatókönyvet gyakran nevezik a *saját kulcsának* (BYOK). A Key Vault a HSM (FIPS 140-2 2. szint) nCipher nShield-családját használja a kulcsok elleni védelemhez.

A cikkben található információk segítségével megtervezheti, létrehozhatja és átviheti a saját HSM-védelemmel ellátott kulcsait a Azure Key Vault használatával való használatra.

> [!NOTE]
> Ez a funkció az Azure China 21Vianet esetében nem érhető el. 
> 
> Ez az importálási módszer csak a [támogatott HSM](#supported-hsms)esetében érhető el. 

További információért és a Key Vault használatának megkezdéséhez (például a Key Vault HSM-védelemmel ellátott kulcsokhoz való létrehozásával) kapcsolatos oktatóanyagért lásd: [Mi az Azure Key Vault?](../general/overview.md)

## <a name="overview"></a>Áttekintés

A folyamat áttekintése. A végrehajtáshoz szükséges konkrét lépéseket a cikk későbbi részében ismertetjük.

* A Key Vaultban állítson elő egy kulcsot (ún. *Key Exchange Key* (KEK)). A KEK-nek olyan RSA-HSM-kulcsnak kell lennie, amely csak a `import` fő művelettel rendelkezik. Csak Key Vault Premium SKU támogatja az RSA-HSM-kulcsokat.
* Töltse le a KEK nyilvános kulcsát. PEM-fájlként.
* Vigye át a KEK nyilvános kulcsát egy offline számítógépre, amely egy helyszíni HSM-hez csatlakozik.
* Az offline számítógépen a HSM gyártója által biztosított BYOK eszközt használva hozzon létre egy BYOK-fájlt. 
* A célként megadott kulcs egy KEK-mel van titkosítva, amely titkosítva marad, amíg át nem kerül a Key Vault HSM-be. Csak a kulcs titkosított verziója hagyhatja el a helyszíni HSM-et.
* Egy Key Vault HSM-ben generált KEK nem exportálható. A HSM kényszeríti azt a szabályt, hogy a KEK tiszta verziója nem létezik Key Vault HSM-en kívül.
* A KEK-nek ugyanabban a kulcstartóban kell lennie, ahol a célként megadott kulcsot importálni kívánja.
* Ha a BYOK-fájlt feltölti a Key Vaultba, a Key Vault HSM a KEK titkos kulcsával visszafejti a célként megadott kulcsot, és azt HSM-kulcsként importálja. Ez a művelet teljes egészében egy Key Vault HSM-ben történik. A célként megadott kulcs mindig a HSM védelmi határán marad.

## <a name="prerequisites"></a>Előfeltételek

A következő táblázat a BYOK használatának előfeltételeit sorolja fel Azure Key Vaultban:

| Követelmény | További információ |
| --- | --- |
| Azure-előfizetés |A Key Vault Azure Key Vaultban való létrehozásához Azure-előfizetésre van szükség. [Regisztráljon az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/). |
| Egy Key Vault Premium SKU a HSM-védelemmel ellátott kulcsok importálásához |További információ a Azure Key Vault szolgáltatási szintjeiről és képességeiről: [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/). |
| Egy HSM a támogatott HSM-listáról és egy BYOK eszközről és a HSM-gyártó által biztosított utasításokról | A HSM használatára vonatkozó engedélyekkel és alapszintű ismeretekkel kell rendelkeznie. Lásd: [támogatott HSM](#supported-hsms). |
| Azure CLI-verzió 2.1.0 vagy újabb verziója | Lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).|

## <a name="supported-hsms"></a>Támogatott HSM

|Szállító neve|Szállító típusa|Támogatott HSM-modellek|További információ|
|---|---|---|---|
|nCipher|Gyártó<br/>HSM szolgáltatásként|<ul><li>HSM nShield családja</li><li>nShield szolgáltatásként</ul>|[nCipher új BYOK-eszköz és-dokumentáció](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Gyártó|<ul><li>Luna HSM 7 család belső vezérlőprogram 7,3-es vagy újabb verziójával</li></ul>| [Luna BYOK eszköz és dokumentáció](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Gyártó<br/>HSM szolgáltatásként|<ul><li>Self-Defending kulcskezelő szolgáltatás (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS-kulcsok exportálása a BYOK a felhőalapú szolgáltatók számára – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Gyártó|Az összes LiquidSecurity-HSM a<ul><li>Belső vezérlőprogram verziója 2.0.4 vagy újabb</li><li>Belső vezérlőprogram 3,2-es vagy újabb verziója</li></ul>|[Marvell BYOK eszköz és dokumentáció](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[Részletekért lásd a Cryptomathic-webhelyet](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK eszköz és dokumentáció](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Gyártó<br/>HSM szolgáltatásként|Primus HSM-család, Securosys felhők HSM|[Primus BYOK eszköz és dokumentáció](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[Részletekért lásd a StorMagic-webhelyet](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS és Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Gyártó|IBM 476x, CryptoExpress|[IBM nagyvállalati kulcskezelő alaprendszer](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Gyártó<br/>HSM szolgáltatásként|u. Trust-horgony, CryptoServer|[Utimaco BYOK eszköz és integrációs útmutató](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Támogatott kulcstípusok

|Kulcs neve|Kulcs típusa|Kulcs mérete/görbe|Forrás|Leírás|
|---|---|---|---|---|
|Key Exchange-kulcs (KEK)|RSA| 2 048 bites<br />3 072 bites<br />4 096 bites|Azure Key Vault HSM|HSM-alapú RSA-kulcspár generálása Azure Key Vault|
|Célként megadott kulcs|
||RSA|2 048 bites<br />3 072 bites<br />4 096 bites|Szállítói HSM|A Azure Key Vault HSM-re továbbítandó kulcs|
||EC|P-256<br />P-384<br />P-521|Szállítói HSM|A Azure Key Vault HSM-re továbbítandó kulcs|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>A kulcs előállítása és átvitele a Key Vault HSM-be

Kulcs létrehozása és átvitele egy Key Vault HSM-be:

* [1. lépés: KEK létrehozása](#step-1-generate-a-kek)
* [2. lépés: a KEK nyilvános kulcsának letöltése](#step-2-download-the-kek-public-key)
* [3. lépés: a kulcs előállítása és előkészítése az átvitelhez](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. lépés: a kulcs átvitele Azure Key Vaultre](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1. lépés: KEK létrehozása

A KEK egy Key Vault HSM-ben generált RSA-kulcs. A KEK az importálni kívánt kulcs (a *célként* megadott kulcs) titkosítására szolgál.

A KEK-nek a következőket kell tennie:
- RSA-HSM-kulcs (2 048 bites; 3 072 bites; vagy 4 096 bites)
- Ugyanabban a kulcstartóban lett létrehozva, amelyben importálni kívánja a célként megadott kulcsot
- Létrehozva az engedélyezett Key Operations beállítással `import`

> [!NOTE]
> A KEK-nek az "import" értéknek kell lennie az egyetlen engedélyezett kulcs-műveletként. az "import" kölcsönösen kizárható minden más kulcsfontosságú művelettel.

Az az [kulcstartó kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create) paranccsal hozzon létre egy KEK-t, amely a következőhöz van beállítva: `import` . Jegyezze fel a `kid` következő parancs által visszaadott kulcs-azonosítót (). (A `kid` [3. lépésben](#step-3-generate-and-prepare-your-key-for-transfer)szereplő értéket fogja használni.)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>2. lépés: a KEK nyilvános kulcsának letöltése

Az az [kulcstartó kulcs letöltése](/cli/azure/keyvault/key#az-keyvault-key-download) paranccsal töltse le a KEK nyilvános kulcsát egy. PEM-fájlba. Az importált célként megadott kulcsot a KEK nyilvános kulcsával titkosítja a rendszer.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Vigye át a KEKforBYOK. PUBLICKEY. PEM fájlt a kapcsolat nélküli számítógépre. A következő lépésben szüksége lesz erre a fájlra.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3. lépés: a kulcs előállítása és előkészítése az átvitelhez

A BYOK eszköz letöltéséhez és telepítéséhez tekintse meg a HSM gyártójának dokumentációját. A HSM gyártójától kapott utasításokat követve hozzon létre egy célként megadott kulcsot, majd hozzon létre egy kulcs-átviteli csomagot (egy BYOK-fájlt). A BYOK eszköz az `kid` [1. lépést](#step-1-generate-a-kek) és a [2. lépésben](#step-2-download-the-kek-public-key) letöltött KEKforBYOK. PUBLICKEY. PEM fájlt fogja használni egy BYOK-fájlban lévő titkosított célként generált kulcs létrehozásához.

Vigye át a BYOK-fájlt a csatlakoztatott számítógépre.

> [!NOTE] 
> Az RSA 1 024 bites kulcsok importálása nem támogatott. Az elliptikus görbe kulcsának a P-256K való importálása nem támogatott.
> 
> **Ismert probléma**: a Luna HSM-ből származó RSA 4k-cél kulcsának importálása csak a belső vezérlőprogram 7.4.0 vagy újabb verziója esetén támogatott.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4. lépés: a kulcs átvitele Azure Key Vaultre

A kulcs importálásának befejezéséhez vigye át a kulcs-átviteli csomagot (egy BYOK-fájlt) a leválasztott számítógépről az internethez csatlakozó számítógépre. Az az [kulcstartó kulcs importálása](/cli/azure/keyvault/key#az-keyvault-key-import) paranccsal töltse fel a BYOK-fájlt a Key Vault HSM-be.

RSA-kulcs importálásához használja az alábbi parancsot. A--KTY paraméter nem kötelező, és az alapértelmezett érték az "RSA-HSM".
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Egy EK-kulcs importálásához meg kell adnia a kulcs típusát és a görbe nevét.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Ha a feltöltés sikeres, az Azure CLI megjeleníti az importált kulcs tulajdonságait.

## <a name="next-steps"></a>Következő lépések

Ezt a HSM-védelemmel ellátott kulcsot már használhatja a kulcstartóban. További információkért tekintse meg [ezt az árat és a szolgáltatás összehasonlítását](https://azure.microsoft.com/pricing/details/key-vault/).



