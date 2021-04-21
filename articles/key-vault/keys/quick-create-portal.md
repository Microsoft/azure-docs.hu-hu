---
title: Azure rövid útmutató – Kulcs beállítása és lekérése a Key Vault használatával Azure Portal | Microsoft Docs
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és Azure Key Vault kulcsot a Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 75ea1c0e92e4d22e73178685472eb1953565a616
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750337"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Rövid útmutató: Kulcs beállítása és lekérése a Azure Key Vault a Azure Portal

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben a rövid útmutatóban létrehoz egy kulcstartót, majd annak használatával tárol egy kulcsot. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](../general/overview.md) szakaszban talál.

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

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vault

Ha kulcsokat szeretne hozzáadni a tárolóhoz, csak néhány további lépést kell tennie. Ebben az esetben hozzáadunk egy kulcsot, amelyet egy alkalmazás használni tud. A kulcs neve **ExampleKey.**

1. A Tulajdonságok Key Vault oldalán válassza a Kulcsok **lehetőséget.**
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A Kulcs **létrehozása képernyőn** válassza a következő értékeket:
    - **Beállítások:** Generálás.
    - **Név:** ExampleKey.
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson a **Létrehozás** lehetőségre.

Miután megjelenik az üzenet arról, hogy a kulcs sikeresen létrejött, rákattinthat rá a listában. Ezután megjelenik néhány tulajdonság. Ha a jelenlegi verzióra kattint, láthatja az előző lépésben megadott értéket.

![Fő tulajdonságok](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy kulcsot. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Key Vault](../general/overview.md)
- Lásd [Azure Key Vault fejlesztői útmutatót](../general/developers-guide.md)
- Tekintse át [a Key Vault biztonsági áttekintését](../general/security-overview.md)