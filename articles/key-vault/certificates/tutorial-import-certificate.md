---
title: Oktatóanyag – Tanúsítvány importálása a Key Vault Azure Portal | Microsoft Docs
description: Oktatóanyag, amely bemutatja, hogyan importálhat tanúsítványt a Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 0e75eebc7aa63efe6d28069bf7ca82234ac15a25
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749995"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Oktatóanyag: Tanúsítvány importálása a Azure Key Vault

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben az oktatóanyagban létrehoz egy kulcstartót, majd annak használatával importál egy tanúsítványt. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](../general/overview.md) szakaszban talál.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Importálhat egy tanúsítványt a Key Vault a portál használatával.
> * Importálhat egy tanúsítványt a Key Vault cli használatával.
> * Importálhat egy tanúsítványt a Key Vault PowerShell használatával.


Mielőtt hozzákezd, olvassa el [a Key Vault alapfogalmait.](../general/basic-concepts.md) 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

1. A Azure Portal menüben vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása lehetőséget.**
2. A Keresőmezőbe írja be a **következőt: Key Vault.**
3. Az eredmények listájában válassza a **Key Vault** lehetőséget.
4. A Key Vault szakaszban kattintson a **Létrehozás** gombra.
5. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:
    - **Név**: Egy egyedi nevet kell megadnia. Ebben a rövid útmutatóban az **Example-Vaultot használjuk.** 
    - **Előfizetés**: Válassza ki az előfizetést.
    - Az **Erőforráscsoport alatt válassza** az Új létrehozása **lehetőséget,** és adjon meg egy erőforráscsoport-nevet.
    - A **Hely** legördülő menüből válassza ki a helyet.
    - A többi beállítást hagyja az alapértelmezett értéken.
6. A fenti adatok megadása után válassza a **Létrehozás** elemet.

Jegyezze fel az alábbi két tulajdonságot:

* **Tároló neve:** A példában ez az **Example-Vault.** Ezt a nevet fogja majd más lépésekben is használni.
* **Tár URI-ja**: A példában ez a https://example-vault.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Jelenleg csak az Azure-fiókja jogosult arra, hogy műveleteket végezzen ezen az új kulcstartón.

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Tanúsítvány importálása a Key Vault

Ahhoz, hogy tanúsítványt importálni tudja a tárolóba, szüksége lesz egy PEM- vagy PFX-tanúsítványfájlra a lemezen. Ebben az esetben egy **ExampleCertificate nevű tanúsítványt importálunk.**

> [!IMPORTANT]
> Az Azure Key Vault által támogatott tanúsítványformátumok a PFX és a PEM. 
> - A .pem fájlformátum egy vagy több X509-tanúsítványfájlt tartalmaz.
> - A .pfx fájlformátum egy archivált fájlformátum, amely számos titkosítási objektumot tárol egyetlen fájlban, például kiszolgálói tanúsítványban (a tartomány számára kiadott), egy megfelelő titkos kulcsot, és opcionálisan tartalmazhat köztes hitelesítésszolgáltatót is.  

1. A tulajdonságokat Key Vault oldalon válassza a Tanúsítványok **lehetőséget.**
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A Tanúsítvány **létrehozása képernyőn** válassza a következő értékeket:
    - **Tanúsítvány-létrehozási módszer:** Importálás.
    - **Tanúsítvány neve:** ExampleCertificate.
    - **Tanúsítványfájl feltöltése:** válassza ki a tanúsítványfájlt a lemezről
    - **Jelszó:** Ha jelszóval védett tanúsítványfájlt tölt fel, itt adja meg a jelszót. Ellenkező esetben hagyja üresen. A tanúsítványfájl sikeres importálása után a Key Vault eltávolítja ezt a jelszót.
4. Kattintson a **Létrehozás** lehetőségre.

![Tanúsítvány tulajdonságai](../media/certificates/tutorial-import-cert/cert-import.png)

Ha importálási  módszerrel ad hozzá egy tanúsítványt, az Azure Key Vault automatikusan kitölti a tanúsítványparamétereket (például az érvényességi időszakot, a kiállító nevét, az aktiválás dátumát stb.).

Miután megjelenik az üzenet arról, hogy a tanúsítvány importálása sikeresen megtörtént, kattintson rá a listában a tulajdonságainak megtekintéséhez. 

![A tanúsítványtulajdonságok megtekintésének helyét bemutató képernyőkép.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Tanúsítvány importálása az Azure CLI használatával

Tanúsítvány importálása egy megadott kulcstartóba. Ha egy meglévő, titkos kulcsot tartalmazó tanúsítványt Azure Key Vault importálni, az importálni kívánt fájl PFX vagy PEM formátumban is lehet. Ha a tanúsítvány PEM formátumú, a PEM-fájlnak tartalmaznia kell a kulcsot és az x509-tanúsítványokat. Ehhez a művelethez a tanúsítványokra/importálásra vonatkozó engedély szükséges.

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

További információ a [paraméterekről:](/cli/azure/keyvault/certificate#az-keyvault-certificate-import).

A tanúsítvány importálása után a Tanúsítvány megjelenítése segítségével megtekintheti [a tanúsítványt](/cli/azure/keyvault/certificate#az-keyvault-certificate-show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Létrehozott egy kulcstartót, importált egy tanúsítványt, és megtekintette a tanúsítvány tulajdonságait.

## <a name="import-a-certificate-using-azure-powershell"></a>Tanúsítvány importálása a Azure PowerShell

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

További információ a [paraméterekről:](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Key Vault, és importált benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- További információ: [Tanúsítvány létrehozásának kezelése a Azure Key Vault](./create-certificate-scenarios.md)
- Példák tanúsítványok [importálására REST API-k használatával](/rest/api/keyvault/importcertificate/importcertificate)
- A biztonsági [Key Vault áttekintése](../general/security-overview.md)