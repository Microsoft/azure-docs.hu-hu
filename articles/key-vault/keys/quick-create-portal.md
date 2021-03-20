---
title: Azure rövid útmutató – Key Vault kulcsának beállítása és lekérése a Azure Portal használatával | Microsoft Docs
description: Gyors útmutató, amely bemutatja, hogyan állíthatja be és kérhet le egy kulcsot a Azure Key Vault használatával a Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 419fe72d400177ce9fa3d9811d7dfa06e74c0810
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935053"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Gyors útmutató: kulcs beállítása és lekérése Azure Key Vault használatával a Azure Portal

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben a rövid útmutatóban egy kulcstartót hoz létre, amelyet aztán a kulcs tárolására használhat. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](../general/overview.md) szakaszban talál.

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

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vaulthoz

A kulcs a tárolóhoz való hozzáadásához mindössze néhány további lépést kell elvégeznie. Ebben az esetben olyan kulcsot adunk hozzá, amelyet egy alkalmazás használhat. A kulcs neve **ExampleKey**.

1. A Key Vault tulajdonságok lapon válassza a **kulcsok** lehetőséget.
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A **kulcs létrehozása** képernyőn válassza a következő értékeket:
    - **Beállítások**: generált.
    - **Név**: ExampleKey.
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson a **Létrehozás** lehetőségre.

Miután megkapta az üzenetet, hogy a kulcs sikeresen létrejött, kattintson rá a listában. Ezután megjelenik néhány tulajdonság. Ha a jelenlegi verzióra kattint, láthatja az előző lépésben megadott értéket.

![Fő tulajdonságok](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és egy kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)