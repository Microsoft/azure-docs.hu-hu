---
title: HSM által védett kulcsok létrehozása és átvitele Azure Key Vault HSM-hez – Azure Key Vault | Microsoft Docs
description: Ez a cikk segítséget nyújt saját HSM által védett kulcsok tervezésében, létrehozásában és átvitelében a Managed HSM-hez való használathoz. Más néven bring your own key (BYOK).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: cc9037db3289d7fb3287a8994a8ff6a68fc0583a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790580"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>HSM által védett kulcsok importálása a Managed HSM-be (BYOK)

 Azure Key Vault Managed HSM támogatja a hardveres biztonsági modulban (HSM) létrehozott kulcsok importálását; A kulcsok soha nem hagyják el a HSM védelmi határát. Ezt a forgatókönyvet gyakran saját kulcs (BYOK) *néven is nevezik.* A Managed HSM a Keysl LiquidSecurity HSM-adaptereket (FIPS 140-2 3. szint szerint érvényesítve) használja a kulcsok védelméhez.

Az ebben a cikkben található információk segítségével megtervheti, létrehozhatja és átemelheti saját HSM által védett kulcsait a Managed HSM-hez.

> [!NOTE]
> Ez a funkció nem érhető el a Azure China 21Vianet. Ez az importálási módszer csak a támogatott [HSM-ek esetén érhető el.](#supported-hsms) 

További információért és a Managed HSM használatának első lépésekért lásd: Mi az a [Managed HSM?](overview.md).

## <a name="overview"></a>Áttekintés

Az alábbi áttekintés a folyamatról nyújt áttekintést. A teljesítendött lépéseket a cikk későbbi, részletesen ismerteti.

* A Managed HSM-ban hozzon létre egy kulcsot (más néven *kulcscserekulcsot* (KEK). A KEK-nek olyan RSA-HSM-kulcsnak kell lennie, amely csak a `import` kulcsművelettel rendelkezik. 
* Töltse le a nyilvános KEK-kulcsot .pem-fájlként.
* A nyilvános KEK-kulcs átvitele egy helyszíni HSM-hez csatlakoztatott offline számítógépre.
* Az offline számítógépen a HSM szállítója által biztosított BYOK eszközzel hozzon létre egy BYOK-fájlt. 
* A célkulcs KEK-el van titkosítva, amely addig titkosítva marad, amíg át nem kerül a Managed HSM-be. Csak a kulcs titkosított verziója hagyja el a helyszíni HSM-et.
* A Managed HSM-ban létrehozott KEK nem exportálható. A HSM-ek kikényszeríteni azt a szabályt, hogy a KEK nem létezik a Managed HSM-en kívül.
* A KEK-nek ugyanabban a felügyelt HSM-ben kell lennie, ahol a célkulcs importálva lesz.
* Amikor feltölti a BYOK-fájlt a Managed HSM-be, a Managed HSM a KEK titkos kulccsal visszafejti a célkulcsanyagot, és HSM-kulcsként importálja azt. Ez a művelet teljes egészében a HSM-en belül történik. A célkulcs mindig a HSM védelmi határán belül marad.


## <a name="prerequisites"></a>Előfeltételek

A cikkben található Azure CLI-parancsok csak a következő elemek használatával használhatók:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure CLI 2.12.0-s vagy újabb verziója. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.
* Egy felügyelt HSM az [előfizetésben támogatott HSM-ek](#supported-hsms) listája. Lásd: [Rövid útmutató: Felügyelt HSM](quick-create-cli.md) kiépítése és aktiválása az Azure CLI használatával egy felügyelt HSM üzembe helyezéshez és aktiválásához.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Az Azure-ba a cli használatával való bejelentkezéshez írja be a következőt:

```azurecli
az login
```

A CLI-n keresztüli bejelentkezési lehetőségekkel kapcsolatos további információkért nézze meg az [Azure CLI-n keresztüli bejelentkezést](/cli/azure/authenticate-azure-cli)

## <a name="supported-hsms"></a>Támogatott HSM-ek

|Szállító neve|Szállító típusa|Támogatott HSM-modellek|További információ|
|---|---|---|---|
|nCipher|Gyártó<br/>HSM mint szolgáltatás|<ul><li>nShield HSM-család</li><li>nShield mint szolgáltatás</ul>|[Új BYOK eszköz és dokumentáció titkosítása](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Gyártó|<ul><li>Luna HSM 7 család 7.3-as vagy újabb belső vezérlőprogrammal</li></ul>| [Luna BYOK eszköz és dokumentáció](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Gyártó<br/>HSM mint szolgáltatás|<ul><li>Self-Defending szolgáltatás (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS-kulcsok exportálása felhőszolgáltatókba BYOK-hoz – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Gyártó|Minden LiquidSecurity HSM a<ul><li>Belső vezérlőprogram 2.0.4-es vagy újabb verziója</li><li>Belső vezérlőprogram 3.2-es vagy újabb verziója</li></ul>|[A Andl BYOK eszköz és dokumentációja](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Titkosítási funkciók|ISV (Vállalati kulcskezelő rendszer)|Több HSM márka és modell, beleértve a következőket:<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Részletekért [lásd: Cryptomathic site (Titkosítási webhely)](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK eszköz és dokumentáció](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Gyártó, HSM mint szolgáltatás|Primus HSM-család, Securosys Clouds HSM|[Primus BYOK eszköz és dokumentáció](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Vállalati kulcskezelő rendszer)|Több HSM-márka és modell, köztük<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Részletekért [lásd: StorMagic-webhely](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS és Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Gyártó|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Gyártó<br/>HSM mint szolgáltatás|u.trust Anchor, CryptoServer|[Utimaco BYOK eszköz és integrációs útmutató](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Támogatott kulcstípusok

|Kulcs neve|Kulcs típusa|Kulcsméret/görbe|Forrás|Description|
|---|---|---|---|---|
|Kulcscserekulcs (KEK)|RSA| 2048 bites<br />3072 bites<br />4096 bites|Managed HSM|A Managed HSM-ben létrehozott HSM-háttér RSA-kulcspár|
|Célkulcs|
||RSA|2048 bites<br />3072 bites<br />4096 bites|SzállítóI HSM|A managed HSM-be átvihető kulcs|
||EC|P-256<br />P-384<br />P-521|SzállítóI HSM|A managed HSM-be átvihető kulcs|
||Szimmetrikus kulcs (oct-HSM)|128 bites<br />192 bites<br />256 bites|Szállítói HSM|A Managed HSM-be átvihető kulcs|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Kulcs létrehozása és átvitele a Managed HSM-be

A kulcs létrehozása és átvitele egy managed HSM-be:

* [1. lépés: KEK létrehozása](#step-1-generate-a-kek)
* [2. lépés: A nyilvános KEK-kulcs letöltése](#step-2-download-the-kek-public-key)
* [3. lépés: A kulcs létrehozása és előkészítése az átvitelhez](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. lépés: A kulcs átvitele a Managed HSM-be](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>1. lépés: KEK létrehozása

A KEK egy felügyelt HSM-ben létrehozott RSA-kulcs. A KEK az importálni kívánt kulcs (a célkulcs) *titkosítására* használható.

A KEK-nek a következőnek kell lennie:
- RsA-HSM-kulcs (2048 bites; 3072 bites; vagy 4096 bites)
- Ugyanannak a felügyelt HSM-nek a létrehozása, ahol importálni kívánja a célkulcsot
- Létrehozva az engedélyezett kulcsműveletekkel. `import`

> [!NOTE]
> A KEK-nek "import" művelettel kell lennie az egyetlen engedélyezett kulcsműveletként. Az "importálás" minden más kulcsfontosságú művelettel kölcsönösen kizárható.

Az [az keyvault key create paranccsal](/cli/azure/keyvault/key#az_keyvault_key_create) hozzon létre egy KEK-et, amely kulcsműveletei a következőre vannak beállítva: `import` . Rögzítse a következő parancs által visszaadott kulcsazonosítót ( `kid` ). (A `kid` [3.](#step-3-generate-and-prepare-your-key-for-transfer)lépésben megadott értéket fogja használni.)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>2. lépés: A nyilvános KEK-kulcs letöltése

Az [az keyvault key download használatával](/cli/azure/keyvault/key#az_keyvault_key_download) töltse le a nyilvános KEK-kulcsot egy .pem-fájlba. Az importált célkulcs titkosítása a nyilvános KEK-kulccsal történik.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

A KEKforBYOK.publickey.pem fájl átvitele offline számítógépre. Erre a fájlra szüksége lesz a következő lépésben.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3. lépés: A kulcs létrehozása és előkészítése az átvitelhez

A BYOK eszköz letöltéséhez és telepítéséhez tekintse meg a HSM gyártójának dokumentációját. Kövesse a HSM szállítójának utasításait a célkulcs létrehozásához, majd hozzon létre egy kulcsátviteli csomagot (BYOK-fájlt). A BYOK eszköz az 1. lépésből származó és a `kid` [2.](#step-2-download-the-kek-public-key) lépésben letöltött KEKforBYOK.publickey.pem fájlt fogja használni a titkosított célkulcs BYOK-fájlban való létrehozásához. [](#step-1-generate-a-kek)

A BYOK-fájl átvitele a csatlakoztatott számítógépre.

> [!NOTE] 
> Az RSA 1024 bites kulcsok importálása nem támogatott. Az elliptikus görbe (EC) kulcs importálása jelenleg nem támogatott.
>
> **Ismert probléma:** Az RSA 4K célkulcs Luna HSM-ről való importálása csak a 7.4.0-s vagy újabb belső vezérlőprogrammal támogatott.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>4. lépés: A kulcs átvitele a Managed HSM-be

A kulcs importálásának befejezéséhez átvitelt kell végrehajtania a kulcsátviteli csomaggal (egy BYOK-fájllal) a leválasztott számítógépről az internetkapcsolattal csatlakozó számítógépre. Az [az keyvault key import paranccsal](/cli/azure/keyvault/key#az_keyvault_key_import) töltse fel a BYOK-fájlt a Managed HSM-be.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Ha a feltöltés sikeres, az Azure CLI megjeleníti az importált kulcs tulajdonságait.

## <a name="next-steps"></a>Következő lépések

Most már használhatja ezt a HSM által védett kulcsot a Managed HSM-ban. További információért tekintse meg ezt az [árat és a funkciók összehasonlítását.](https://azure.microsoft.com/pricing/details/key-vault/)
