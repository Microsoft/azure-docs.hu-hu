---
title: Rövid útmutató – Azure Key Vault létrehozása a Azure Portal
description: Gyors útmutató, amely bemutatja, hogyan hozhat létre Azure Key Vault a Azure Portal használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 729b6ccf1092679a71aa35f49be7d3199d4f4918
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936277"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>Gyors útmutató: kulcstartó létrehozása a Azure Portal használatával

A Azure Key Vault egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a kulcsok, [titkos](../secrets/index.yml) [kódok](../keys/index.yml)és [tanúsítványok](../certificates/index.yml)számára. További információ a Key Vaultről: [About Azure Key Vault](overview.md); a Key vaultban tárolt adatokkal kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](about-keys-secrets-certificates.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ebben a rövid útmutatóban egy kulcstartót hoz létre a [Azure Portal](https://portal.azure.com). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása** lehetőséget.
2. A keresőmezőbe írja be a **Key Vault** kifejezést.
3. Az eredmények listájában válassza a **Key Vault** lehetőséget.
4. A Key Vault szakaszban kattintson a **Létrehozás** gombra.
5. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:
    - **Név**: Egy egyedi nevet kell megadnia. Ebben a rövid útmutatóban a **contoso-vault2-** t használjuk. 
    - **Előfizetés**: Válassza ki az előfizetést.
    - Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet.
    - A **Hely** legördülő menüből válassza ki a helyet.
    - A többi beállítást hagyja az alapértelmezett értéken.
6. A fenti adatok megadása után válassza a **Létrehozás** elemet.

Jegyezze fel az alábbi két tulajdonságot:

* **Tároló neve**: A példában ez a **Contoso-Vault2**. Ezt a nevet fogja majd más lépésekben is használni.
* **Tár URI-ja**: A példában ez a https://contoso-vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Jelenleg csak az Azure-fiókja jogosult arra, hogy műveleteket végezzen ezen az új kulcstartón.

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/quick-create-portal/vault-properties.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault a Azure Portal használatával. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](overview.md)
- Tekintse át a [Azure Key Vault biztonsági áttekintést](security-overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](developers-guide.md)
