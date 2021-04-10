---
title: Oktatóanyag – tanúsítvány importálása Key Vault a Azure Portal használatával | Microsoft Docs
description: Oktatóanyag, amely bemutatja, hogyan importálhat tanúsítványokat Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: e909b9ac3b26eabc37f2547439890c7ee30d07a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728521"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Oktatóanyag: tanúsítvány importálása Azure Key Vault

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben az oktatóanyagban létrehoz egy kulcstartót, majd a használatával importál egy tanúsítványt. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](../general/overview.md) szakaszban talál.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Tanúsítvány importálása Key Vault a portál használatával.
> * Tanúsítvány importálása Key Vault a parancssori felület használatával.
> * Tanúsítvány importálása Key Vault a PowerShell használatával.


Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](../general/basic-concepts.md). 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása** lehetőséget.
2. A keresőmezőbe írja be a **Key Vault** kifejezést.
3. Az eredmények listájában válassza a **Key Vault** lehetőséget.
4. A Key Vault szakaszban kattintson a **Létrehozás** gombra.
5. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:
    - **Név**: Egy egyedi nevet kell megadnia. Ebben a rövid útmutatóban **például a-vaultot** használjuk. 
    - **Előfizetés**: Válassza ki az előfizetést.
    - Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet.
    - A **Hely** legördülő menüből válassza ki a helyet.
    - A többi beállítást hagyja az alapértelmezett értéken.
6. A fenti adatok megadása után válassza a **Létrehozás** elemet.

Jegyezze fel az alábbi két tulajdonságot:

* Tár **neve**: a példában ez **például a-Vault**. Ezt a nevet fogja majd más lépésekben is használni.
* **Tár URI-ja**: A példában ez a https://example-vault.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Jelenleg csak az Azure-fiókja jogosult arra, hogy műveleteket végezzen ezen az új kulcstartón.

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Tanúsítvány importálása Key Vault

Ha tanúsítványt szeretne importálni a tárolóba, rendelkeznie kell egy, a lemezen található PEM-vagy PFX-tanúsítványfájl-fájllal. Ebben az esetben egy **ExampleCertificate** nevű tanúsítványt fogunk importálni.

> [!IMPORTANT]
> Az Azure Key Vault által támogatott tanúsítványformátumok a PFX és a PEM. 
> - a. PEM fájlformátum egy vagy több X509 tartalmaz.
> - a. pfx fájlformátum egy Archív fájlformátum, amely több kriptográfiai objektumot tárol egyetlen fájlban, például a tartományhoz tartozó kiszolgálói tanúsítványt, a hozzá tartozó titkos kulcsot, és szükség esetén egy közbenső HITELESÍTÉSSZOLGÁLTATÓT is tartalmazhat.  

1. A Key Vault tulajdonságok lapon válassza a **tanúsítványok** lehetőséget.
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A **tanúsítvány létrehozása** képernyőn válassza ki a következő értékeket:
    - **Tanúsítvány létrehozásának módszere**: importálás.
    - **Tanúsítvány neve**: ExampleCertificate.
    - **Tanúsítványfájl feltöltése**: válassza ki a fájlt a lemezről.
    - **Password** (jelszó): jelszóval védett tanúsítványfájl feltöltésekor itt adja meg a jelszót. Ellenkező esetben hagyja üresen. A tanúsítványfájl sikeres importálása után a Key Vault eltávolítja ezt a jelszót.
4. Kattintson a **Létrehozás** lehetőségre.

![Tanúsítvány tulajdonságai](../media/certificates/tutorial-import-cert/cert-import.png)

A tanúsítvány **importálási** módszerrel való hozzáadásával az Azure Key Vault automatikusan kitölti a tanúsítvány paramétereit (például érvényességi időtartam, kiállító neve, aktiválás dátuma stb.).

Miután megkapta az üzenetet, hogy a tanúsítvány importálása sikeresen megtörtént, a tulajdonságok megtekintéséhez kattintson rá a listában. 

![A tanúsítvány tulajdonságainak megtekintését bemutató képernyőkép.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Tanúsítvány importálása az Azure CLI-vel

Tanúsítvány importálása egy megadott kulcstartóba. Ha egy meglévő érvényes tanúsítványt szeretne importálni, amely titkos kulcsot tartalmaz, a Azure Key Vaultba, az importálandó fájl lehet PFX vagy PEM formátumú. Ha a tanúsítvány PEM formátumú, a PEM-fájlnak tartalmaznia kell a kulcsot, valamint a x509 tanúsítványokat. Ehhez a művelethez a tanúsítványokra/importálásra vonatkozó engedély szükséges.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

További információ a [paraméterekről](/cli/azure/keyvault/certificate#az-keyvault-certificate-import).

A tanúsítvány importálása után megtekintheti a tanúsítványt a [tanúsítvány megjelenítése](/cli/azure/keyvault/certificate#az-keyvault-certificate-show) lehetőség használatával.


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Most létrehozott egy kulcstartót, importált egy tanúsítványt, és megtekintette a tanúsítvány tulajdonságait.

## <a name="import-a-certificate-using-azure-powershell"></a>Tanúsítvány importálása Azure PowerShell használatával

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

További információ a [paraméterekről](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Key Vault, és importált egy tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- További információ a [tanúsítvány-létrehozás kezeléséről Azure Key Vault](./create-certificate-scenarios.md)
- Példák a [tanúsítványok importálására REST API](/rest/api/keyvault/importcertificate/importcertificate) -k használatával
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)