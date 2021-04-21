---
title: Rövid Azure-útmutató – Titkos kulcs beállítása és lekérése a Key Vaultból az Azure Portal használatával | Microsoft Docs
description: Rövid útmutató, amely bemutatja a titkos kulcsok beállítását és lekérését az Azure Key Vaultból az Azure Portal használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 0c1a3362c4f1dfd3396ccd4704933d06d2611414
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814117"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból az Azure Portal használatával

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben a rövid útmutatóban egy kulcstartót hoz létre, majd eltárol benne egy titkos kulcsot. 

További információ: 
- [Key Vault áttekintés](../general/overview.md)
- [Titkos kulcsok áttekintése.](about-secrets.md)

## <a name="prerequisites"></a>Előfeltételek

A Azure Key Vault szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) kezdés előtt hozzon létre egy ingyenes fiókot.

A titkos kulcsokhoz való hozzáférés az Azure Key Vault. Ebben a rövid útmutatóban hozzon létre egy kulcstartót a [Azure Portal,](../general/quick-create-portal.md) [az Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell.](../general/quick-create-powershell.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kulcs a tárolóhoz való hozzáadásához kövesse az alábbi lépéseket:

1. Lépjen az új kulcstartóhoz a Azure Portal
1. A Key Vault lapon válassza a Titkos **kulcsok lehetőséget.**
1. Kattintson a **Létrehozás/Importálás** gombra.
1. A **Titkos kód létrehozása** képernyőn válassza az alábbi értékeket:
    - **Feltöltési beállítások**: Manuális.
    - **Név:** Adja meg a titkos fájl nevét. A titkos névnek egyedinek kell lennie a Key Vault. A névnek 1–127 karakteres sztringnek kell lennie, amely egy betűvel kezdődik, és csak 0–9, a-z, A-Z és -. További információ az elnevezésről: [Key Vault, azonosítók és verziószámozás](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Érték:** Adjon meg egy értéket a titkos számára. Key Vault API-k sztringként fogadják el és ják vissza a titkos értékeket. 
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson a **Létrehozás** lehetőségre.

Miután megérkezett az üzenet arról, hogy a titkos kulcs sikeresen létrejött, kattintson rá a listában. 

További információ a titkos kulcsok attribútumairól: [Információk Azure Key Vault titkos kulcsokról](./about-secrets.md)

## <a name="retrieve-a-secret-from-key-vault"></a>Titkos adat lekérése a Key Vault

Ha a jelenlegi verzióra kattint, láthatja az előző lépésben megadott értéket.

![Titkos kulcs tulajdonságai](../media/quick-create-portal/current-version-hidden.png)

Ha a jobb oldali panelen a "Titkos érték megjelenítése" gombra kattint, láthatja a rejtett értéket. 

![Titkos érték jelent meg](../media/quick-create-portal/current-version-shown.png)

A korábban létrehozott titkos adatokat [az Azure CLI]()vagy a [Azure PowerShell]() is lekérheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

> [!NOTE]
> Fontos megjegyezni, hogy egy titkos kulcs, kulcs, tanúsítvány vagy kulcstartó törlése után az egy 7–90 naptári napig konfigurálható ideig helyreállítható marad. Ha nincs megadva konfiguráció, az alapértelmezett helyreállítási időszak 90 nap lesz. Ez elegendő időt biztosít a felhasználóknak a titkos adatok véletlen törlésének jelzésére és a válaszadásra. A kulcstartók és kulcstartó-objektumok törlésével és helyreállításával kapcsolatos további információkért lásd a helyreállítható törlés Azure Key Vault [áttekintését.](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault és tárolt benne egy titkos adatokat. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Read [Secure access to a Key Vault](../general/security-features.md)
- Lásd: Use Key Vault with App Service Web App (A [Key Vault használata App Service webalkalmazással)](../general/tutorial-net-create-vault-azure-web-app.md)
- Lásd: Use Key Vault with application deployed to VM (Az [Key Vault használata virtuális gépen üzembe helyezett alkalmazással)](../general/tutorial-net-virtual-machine.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)