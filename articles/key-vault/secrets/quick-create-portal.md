---
title: Rövid Azure-útmutató – Titkos kulcs beállítása és lekérése a Key Vaultból az Azure Portal használatával | Microsoft Docs
description: Rövid útmutató, amely bemutatja a titkos kulcsok beállítását és lekérését az Azure Key Vaultból az Azure Portal használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 904c00c26171854a32234d7b6ba5cac81ac43e5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936668"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból az Azure Portal használatával

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben a rövid útmutatóban egy kulcstartót hoz létre, majd eltárol benne egy titkos kulcsot. 

További információ a szolgáltatásról: 
- [Key Vault áttekintése](../general/overview.md)
- A [titkok áttekintése](about-secrets.md).

## <a name="prerequisites"></a>Előfeltételek

Azure Key Vault eléréséhez Azure-előfizetésre lesz szüksége. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

A titkokhoz való minden hozzáférés a Azure Key Vaulton keresztül zajlik. Ebben a rövid útmutatóban egy kulcstartót hoz létre [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)vagy [Azure PowerShell](../general/quick-create-powershell.md)használatával.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

A titkos kód a tárolóhoz való hozzáadásához kövesse az alábbi lépéseket:

1. Navigáljon az új kulcstartóhoz a Azure Portal
1. A Key Vault beállítások oldalain válassza a **titkok** elemet.
1. Kattintson a **Létrehozás/Importálás** gombra.
1. A **Titkos kód létrehozása** képernyőn válassza az alábbi értékeket:
    - **Feltöltési beállítások**: Manuális.
    - **Név**: írja be a titok nevét. A titkos névnek egyedinek kell lennie egy Key Vault belül. A névnek 1-127 karakterből álló karakterláncnak kell lennie, betűvel kell kezdődnie, és csak 0-9, a-z, A-Z és-. További információ a névadásról: [Key Vault objektumok, azonosítók és verziószámozás](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Érték**: adja meg a titok értékét. Key Vault API-k elfogadják és visszaadják a titkos értékeket karakterláncként. 
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson a **Létrehozás** lehetőségre.

Miután megérkezett az üzenet arról, hogy a titkos kulcs sikeresen létrejött, kattintson rá a listában. 

További információ a titkok attribútumairól: [Tudnivalók a Azure Key Vault titkokról](./about-secrets.md)

## <a name="retrieve-a-secret-from-key-vault"></a>Titkos kód beolvasása Key Vault

Ha a jelenlegi verzióra kattint, láthatja az előző lépésben megadott értéket.

![Titkos kulcs tulajdonságai](../media/quick-create-portal/current-version-hidden.png)

A jobb oldali ablaktáblán látható "titkos érték megjelenítése" gombra kattintva megtekintheti a rejtett értéket. 

![A titkos érték megjelent](../media/quick-create-portal/current-version-shown.png)

Az [Azure CLI]()-t vagy [Azure PowerShell]() is használhatja a korábban létrehozott titkos kód lekéréséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

> [!NOTE]
> Fontos megjegyezni, hogy a titkos kulcs, a kulcs, a tanúsítvány vagy a kulcstartó törlése után a rendszer a 7 – 90 naptári nap konfigurálható időtartamára helyreállítható marad. Ha nincs megadva konfiguráció, az alapértelmezett helyreállítási időszak 90 napra lesz állítva. Ez elegendő időt biztosít a felhasználóknak a véletlen titkos törlésre és a válaszadásra. A Key vaultok és a Key Vault-objektumok törlésével és helyreállításával kapcsolatos további információkért lásd: [Azure Key Vault-törlés – áttekintés](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és egy titkos kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- [Key Vault biztonságos hozzáférésének](../general/secure-your-key-vault.md) olvasása
- Lásd: [Key Vault használata app Service Web App használatával](../general/tutorial-net-create-vault-azure-web-app.md)
- Lásd: [Key Vault használata a virtuális géphez üzembe helyezett alkalmazással](../general/tutorial-net-virtual-machine.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)