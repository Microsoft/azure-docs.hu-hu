---
title: HSM-védelemmel ellátott kulcsok generálása és átvitele Azure Key Vault felügyelt HSM-Azure Key Vault | Microsoft Docs
description: Ez a cikk segítséget nyújt a saját HSM-védelemmel ellátott kulcsoknak a felügyelt HSM-sel való használatra történő megtervezésében, létrehozásában és átvitelében. Más néven a saját kulcs használata (BYOK).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: dd5b38a858ceba12f5d48f1782da5b85228c4b06
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212110"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>HSM-védelemmel ellátott kulcsok importálása a felügyelt HSM-be (BYOK)

 Azure Key Vault felügyelt HSM támogatja a helyszíni hardveres biztonsági modulban (HSM) generált kulcsok importálását. a kulcsok soha nem hagyják el a HSM védelmi határt. Ezt a forgatókönyvet gyakran nevezik a *saját kulcsának* (BYOK). A felügyelt HSM a Marvell LiquidSecurity HSM-adaptereket használja (az FIPS 140-2 3. szint ellenőrzött) a kulcsok megóvása érdekében.

A cikkben található információk segítségével megtervezheti, létrehozhatja és átviheti a saját HSM-védelemmel ellátott kulcsait a felügyelt HSM-sel való használatra.

> [!NOTE]
> Ez a funkció az Azure China 21Vianet esetében nem érhető el. Ez az importálási módszer csak a [támogatott HSM](#supported-hsms)esetében érhető el. 

További információkért és a felügyelt HSM használatának első lépéseiért tekintse meg a [Mi a felügyelt HSM?](overview.md)című témakört.

## <a name="overview"></a>Áttekintés

A folyamat áttekintése. A végrehajtáshoz szükséges konkrét lépéseket a cikk későbbi részében ismertetjük.

* A felügyelt HSM-ben állítson elő egy kulcsot (ún. *Key Exchange Key* , KEK). A KEK-nek olyan RSA-HSM-kulcsnak kell lennie, amely csak a `import` fő művelettel rendelkezik. 
* Töltse le a KEK nyilvános kulcsát. PEM-fájlként.
* Vigye át a KEK nyilvános kulcsát egy offline számítógépre, amely egy helyszíni HSM-hez csatlakozik.
* Az offline számítógépen a HSM gyártója által biztosított BYOK eszközt használva hozzon létre egy BYOK-fájlt. 
* A célként megadott kulcs egy KEK-mel van titkosítva, amely titkosítva marad, amíg át nem kerül a felügyelt HSM-be. Csak a kulcs titkosított verziója hagyhatja el a helyszíni HSM-et.
* A felügyelt HSM-ben létrehozott KEK nem exportálható. A HSM kényszeríti azt a szabályt, amely szerint a KEK tiszta verziója nem létezik a felügyelt HSM-en kívül.
* A KEK-nek ugyanabban a felügyelt HSM-ben kell lennie, ahol a célként megadott kulcsot importálni kívánja.
* Ha a BYOK-fájlt feltölti a felügyelt HSM-be, a felügyelt HSM a KEK titkos kulcsával visszafejti a célként megadott kulcs anyagát, és azt HSM-kulcsként importálja. Ez a művelet teljes egészében a HSM-ben történik. A célként megadott kulcs mindig a HSM védelmi határán marad.


## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő Azure CLI-parancsok használatához a következő elemek szükségesek:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure CLI verziója 2.12.0 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.
* Felügyelt HSM az előfizetés [támogatott HSM listája](#supported-hsms) . Lásd [: gyors útmutató: felügyelt HSM kiépítése és aktiválása az Azure CLI használatával](quick-create-cli.md) a FELÜGYELt HSM üzembe helyezéséhez és aktiválásához.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felülettel szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

Ha további információt szeretne a bejelentkezési lehetőségekről a CLI-n keresztül, tekintse meg az [Azure CLI-vel való bejelentkezést](/cli/azure/authenticate-azure-cli) ismertető részt.

## <a name="supported-hsms"></a>Támogatott HSM

|Szállító neve|Szállító típusa|Támogatott HSM-modellek|További információ|
|---|---|---|---|
|nCipher|Gyártó<br/>HSM szolgáltatásként|<ul><li>HSM nShield családja</li><li>nShield szolgáltatásként</ul>|[nCipher új BYOK-eszköz és-dokumentáció](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Gyártó|<ul><li>Luna HSM 7 család belső vezérlőprogram 7,3-es vagy újabb verziójával</li></ul>| [Luna BYOK eszköz és dokumentáció](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Gyártó<br/>HSM szolgáltatásként|<ul><li>Self-Defending kulcskezelő szolgáltatás (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS-kulcsok exportálása a BYOK a felhőalapú szolgáltatók számára – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Gyártó|Az összes LiquidSecurity-HSM a<ul><li>Belső vezérlőprogram verziója 2.0.4 vagy újabb</li><li>Belső vezérlőprogram 3,2-es vagy újabb verziója</li></ul>|[Marvell BYOK eszköz és dokumentáció](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[Részletekért lásd a Cryptomathic-webhelyet](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK eszköz és dokumentáció](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Gyártó, HSM szolgáltatásként|Primus HSM-család, Securosys felhők HSM|[Primus BYOK eszköz és dokumentáció](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[Részletekért lásd a StorMagic-webhelyet](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS és Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Gyártó|IBM 476x, CryptoExpress|[IBM nagyvállalati kulcskezelő alaprendszer](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Gyártó<br/>HSM szolgáltatásként|u. Trust-horgony, CryptoServer|[Utimaco BYOK eszköz és integrációs útmutató](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Támogatott kulcstípusok

|Kulcs neve|Kulcs típusa|Kulcs mérete/görbe|Forrás|Description|
|---|---|---|---|---|
|Key Exchange-kulcs (KEK)|RSA| 2 048 bites<br />3 072 bites<br />4 096 bites|Managed HSM|A felügyelt HSM-ben generált HSM-alapú RSA kulcspár|
|Célként megadott kulcs|
||RSA|2 048 bites<br />3 072 bites<br />4 096 bites|Szállítói HSM|A felügyelt HSM-re továbbítandó kulcs|
||EC|P-256<br />P-384<br />P-521|Szállítói HSM|A felügyelt HSM-re továbbítandó kulcs|
||Szimmetrikus kulcs (Oct-HSM)|128 bites<br />192 bites<br />256 bites|Szállítói HSM|A felügyelt HSM-re továbbítandó kulcs|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Kulcs előállítása és átvitele a felügyelt HSM-be

Kulcs létrehozása és átvitele felügyelt HSM-be:

* [1. lépés: KEK létrehozása](#step-1-generate-a-kek)
* [2. lépés: a KEK nyilvános kulcsának letöltése](#step-2-download-the-kek-public-key)
* [3. lépés: a kulcs előállítása és előkészítése az átvitelhez](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. lépés: a kulcs átvitele a felügyelt HSM-be](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>1. lépés: KEK létrehozása

A KEK egy felügyelt HSM-ben generált RSA-kulcs. A KEK az importálni kívánt kulcs (a *célként* megadott kulcs) titkosítására szolgál.

A KEK-nek a következőket kell tennie:
- RSA-HSM-kulcs (2 048 bites; 3 072 bites; vagy 4 096 bites)
- Ugyanabban a felügyelt HSM-ben jött létre, amelyben importálni kívánja a célként megadott kulcsot
- Létrehozva az engedélyezett Key Operations beállítással `import`

> [!NOTE]
> A KEK-nek az "import" értéknek kell lennie az egyetlen engedélyezett kulcs-műveletként. az "import" kölcsönösen kizárható minden más kulcsfontosságú művelettel.

Az az [kulcstartó kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create) paranccsal hozzon létre egy KEK-t, amely a következőhöz van beállítva: `import` . Jegyezze fel a `kid` következő parancs által visszaadott kulcs-azonosítót (). (A `kid` [3. lépésben](#step-3-generate-and-prepare-your-key-for-transfer)szereplő értéket fogja használni.)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>2. lépés: a KEK nyilvános kulcsának letöltése

Az az [kulcstartó kulcs letöltése](/cli/azure/keyvault/key#az-keyvault-key-download) paranccsal töltse le a KEK nyilvános kulcsát egy. PEM-fájlba. Az importált célként megadott kulcsot a KEK nyilvános kulcsával titkosítja a rendszer.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Vigye át a KEKforBYOK. PUBLICKEY. PEM fájlt a kapcsolat nélküli számítógépre. A következő lépésben szüksége lesz erre a fájlra.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3. lépés: a kulcs előállítása és előkészítése az átvitelhez

A BYOK eszköz letöltéséhez és telepítéséhez tekintse meg a HSM gyártójának dokumentációját. A HSM gyártójától kapott utasításokat követve hozzon létre egy célként megadott kulcsot, majd hozzon létre egy kulcs-átviteli csomagot (egy BYOK-fájlt). A BYOK eszköz az `kid` [1. lépést](#step-1-generate-a-kek) és a [2. lépésben](#step-2-download-the-kek-public-key) letöltött KEKforBYOK. PUBLICKEY. PEM fájlt fogja használni egy BYOK-fájlban lévő titkosított célként generált kulcs létrehozásához.

Vigye át a BYOK-fájlt a csatlakoztatott számítógépre.

> [!NOTE] 
> Az RSA 1 024 bites kulcsok importálása nem támogatott. Jelenleg egy elliptikus görbe (EC) kulcs importálása nem támogatott.
>
> **Ismert probléma**: a Luna HSM-ből származó RSA 4k-cél kulcsának importálása csak a belső vezérlőprogram 7.4.0 vagy újabb verziója esetén támogatott.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>4. lépés: a kulcs átvitele a felügyelt HSM-be

A kulcs importálásának befejezéséhez vigye át a kulcs-átviteli csomagot (egy BYOK-fájlt) a leválasztott számítógépről az internethez csatlakozó számítógépre. Az az [kulcstartó kulcs importálása](/cli/azure/keyvault/key#az-keyvault-key-import) paranccsal töltse fel a BYOK-fájlt a FELÜGYELt HSM-be.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Ha a feltöltés sikeres, az Azure CLI megjeleníti az importált kulcs tulajdonságait.

## <a name="next-steps"></a>Következő lépések

Ezt a HSM-védelemmel ellátott kulcsot már használhatja a felügyelt HSM-ben. További információkért tekintse meg [ezt az árat és a szolgáltatás összehasonlítását](https://azure.microsoft.com/pricing/details/key-vault/).



