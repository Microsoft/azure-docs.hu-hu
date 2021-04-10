---
title: Saját kulcs specifikációjának használata – Azure Key Vault | Microsoft Docs
description: Ez a dokumentum a saját kulcs specifikációját írja le.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581023"
---
# <a name="bring-your-own-key-specification"></a>Saját kulcs használatának specifikációja

Ez a dokumentum ismerteti a HSM-védelemmel ellátott kulcsok az ügyfelek helyszíni HSM a Key Vaultba való importálásának specifikációit.

## <a name="scenario"></a>Eset

A Key Vault ügyfél biztonságosan átviheti a kulcsot az Azure-on kívüli helyszíni HSM-ből a HSM-alapú biztonsági Azure Key Vaultba. Key Vaulton kívül generált kulcs importálásának folyamata általában Bring Your Own Key (BYOK) néven ismert.

A követelmények a következők:
* Az átvinni kívánt kulcs nem létezik egy HSM-en kívül egyszerű szöveges formában.
* A HSM-en kívül az átvinni kívánt kulcsot mindig a Azure Key Vault HSM által őrzött kulcs védi

## <a name="terminology"></a>Terminológia

|Kulcs neve|Kulcs típusa|Forrás|Leírás|
|---|---|---|---|
|Key Exchange-kulcs (KEK)|RSA|Azure Key Vault HSM|HSM-es biztonsági másolattal rendelkező RSA-kulcspár, amely Azure Key Vaultban lett létrehozva
Becsomagolási kulcs|AES|Szállítói HSM|Egy [ideiglenes] AES-kulcs, amely a HSM-on keresztül lett létrehozva
Célként megadott kulcs|RSA, EC, AES (csak felügyelt HSM)|Szállítói HSM|A Azure Key Vault HSM-re továbbítandó kulcs

**Kulcscsere kulcsa**: az ügyfél által a Key vaultban előállított HSM-alapú kulcs, amelyben a BYOK kulcs importálva lesz. Ennek a KEK-nek a következő tulajdonságokkal kell rendelkeznie:

* Ez egy RSA-HSM-kulcs (4096 bites vagy 3072 bites vagy 2048 bites)
* Rögzített key_ops (csak "import") lesz, amely lehetővé teszi, hogy csak a BYOK során legyen használatban.
* Ugyanabban a tárolóban kell lennie, ahol a célként megadott kulcsot importálni kívánja

## <a name="user-steps"></a>Felhasználói lépések

A kulcsok átviteléhez a felhasználó a következő lépéseket hajtja végre:

1. KEK előállítása.
2. A KEK nyilvános kulcsának beolvasása.
3. A HSM gyártó által biztosított BYOK eszköz használata – importálja a KEK-t a cél HSM-be, és exportálja a KEK által védett célként megadott kulcsot.
4. Importálja a védett célként megadott kulcsot Azure Key Vaultba.

Az ügyfelek a HSM gyártó által biztosított BYOK eszközt és dokumentációt használják a 3. lépés végrehajtásához. Létrehoz egy Key átviteli blobot (egy ". byok" fájlt).


## <a name="hsm-constraints"></a>HSM-korlátozások

A meglévő HSM korlátozásokat alkalmazhat a kezelt kulcsra, beleértve a következőket:
* Előfordulhat, hogy a HSM-alapú exportálás engedélyezéséhez konfigurálni kell a HSM-et.
* Előfordulhat, hogy a célként megadott kulcsot CKA_EXTRACTABLE kell megjelölni a HSM számára az ellenőrzött exportálás engedélyezéséhez.
* Bizonyos esetekben előfordulhat, hogy a KEK és a becsomagolási kulcsot CKA_TRUSTEDként kell megjelölni. Ez lehetővé teszi a kulcsoknak a HSM-ben való becsomagolását.

A forrás HSM konfigurációja általában a specifikáció hatókörén kívül esik. A Microsoft arra vár, hogy a HSM-gyártó a BYOK eszközéhez kapcsolódó dokumentációt készítsen, hogy tartalmazza az ilyen konfigurációs lépéseket.

> [!NOTE]
> Az alább ismertetett 1., 2. és 4. lépés más felületek, például a Azure PowerShell és az Azure Portal használatával is elvégezhető. Emellett programozott módon is elvégezhető a Key Vault SDK-ban található egyenértékű függvények használatával.

### <a name="step-1-generate-kek"></a>1. lépés: KEK előállítása

Az az kulcstartó **kulcs létrehozása** paranccsal hozzon létre KEK-et az importálásra beállított fő műveletekkel. Jegyezze fel az alábbi parancs által visszaadott "Kid" azonosítójú kulcsot.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>2. lépés: a KEK nyilvános kulcsának beolvasása

Töltse le a KEK nyilvános kulcs részét, és tárolja egy PEM-fájlba.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>3. lépés: Key átviteli blob előállítása HSM gyártó által biztosított BYOK eszköz használatával

Az ügyfél a HSM gyártó által biztosított BYOK eszközt használja a kulcs átvitelére szolgáló blob (". BYOK" fájlként tárolt) létrehozásához. A KEK nyilvános kulcsa (a. PEM-fájl) az eszköz egyik bemenete lesz.

#### <a name="key-transfer-blob"></a>Kulcs átvitelének Blobja
Hosszú távon a Microsoft szeretné használni a PKCS # 11 CKM_RSA_AES_KEY_WRAP mechanizmust, hogy átvigye a célként megadott kulcsot Azure Key Vault, mivel ez a mechanizmus egyetlen blobot hoz létre, és ami még fontosabb, a köztes AES-kulcsot a két HSM kezeli, és garantáltan elmúlónak kell lennie. Ez a mechanizmus jelenleg nem érhető el néhány HSM, de a célként megadott CKM_AES_KEY_WRAP_PAD kulcs AES-kulccsal való védelmének és az AES-kulcsnak a CKM_RSA_PKCS_OAEP általi védelmének kombinációja egyenértékű blobot eredményez.

A célként megadott kulcs szöveges formátuma a kulcs típusától függ: 
* Egy RSA-kulcshoz, a titkos kulcs ASN. 1 DER Encoding [RFC3447] a PKCS # 8 [RFC5208] 
* Egy EK-kulcshoz a PKCS # 8 [RFC5208] által burkolt titkos kulcs ASN. 1 DER kódolása [RFC5915].
* Oktett-kulcs esetén a kulcs nyers bájtjai

Az egyszerű szöveges kulcs bájtjai a CKM_RSA_AES_KEY_WRAP mechanizmus használatával alakíthatók át:
* A rendszer ideiglenes AES-kulcsot hoz létre és titkosít az RSA-OAEP és SHA1 használatával.
* A kódolt egyszerű szöveges kulcsot az AES Key wrap és a kitöltés használatával titkosítja az AES-kulccsal.
* A rendszer összefűzi a titkosított AES-kulcsot és a titkosított egyszerű szöveges kulcsot, hogy a végső rejtjelezett blobot hozza létre.

Az adatátviteli blob formátuma a JSON web encryption Compact szerializálást (RFC7516) használja elsősorban olyan járműként, amely a szükséges metaadatokat továbbítja a szolgáltatásnak a megfelelő visszafejtés érdekében.

Ha CKM_RSA_AES_KEY_WRAP_PAD van használatban, az átviteli blob JSON-szerializálása a következő lesz:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* Kid = KEK kulcs azonosítója. A következőhöz hasonló Key Vault kulcsok: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* ALG = algoritmus. 
* a dir = Direct mód, azaz a hivatkozott kölyök a rejtjelezett közvetlen védelemmel való ellátására szolgál, amely a CKM_RSA_AES_KEY_WRAP pontos ábrázolása.
* Generator = egy tájékoztató mező, amely a BYOK eszköz nevét és verzióját, valamint a forrás HSM gyártóját és modelljét jelöli. Ezek az információk hibaelhárítási és támogatási célokra szolgálnak.

A JSON-blobot egy ". byok" kiterjesztésű fájlban tárolja a rendszer, így a Azure PowerShell/CLI-ügyfelek megfelelően kezelik, ha a "Add-AzKeyVaultKey" (PSH) vagy az "az kulcstartó kulcs importálása" (CLI) parancsokat használják.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>4. lépés: a kulcs átvitelére szolgáló blob feltöltése a HSM-kulcs importálásához

Az ügyfél átviszi a kulcs átvitelének Blobját (". byok") egy online munkaállomásra, majd futtatja az **az kulcstartó kulcs importálása** parancsot, hogy a blobot új HSM-alapú kulcsként importálja Key Vaultba. 

RSA-kulcs importálásához használja ezt a parancsot:
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
Egy EK-kulcs importálásához meg kell adnia a kulcs típusát és a görbe nevét.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


A fenti parancs végrehajtásakor egy REST API kérés küldését eredményezi a következő módon:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Kérelem törzse RSA-kulcs importálásakor:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Kérelem törzse egy EK-kulcs importálásakor:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

a "key_hsm" érték a KeyTransferPackage-ContosoFirstHSMkey. byok teljes tartalma Base64 formátumban kódolva.

## <a name="references"></a>Hivatkozások
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)

## <a name="next-steps"></a>Következő lépések
* Részletes BYOK utasítások: [HSM-védelemmel ellátott kulcsok importálása Key Vaultba (BYOK)](hsm-protected-keys-byok.md)

