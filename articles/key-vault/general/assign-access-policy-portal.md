---
title: Azure Key Vault hozzáférési szabályzat (portál) társítása
description: Key Vault hozzáférési szabályzatok egy egyszerű szolgáltatáshoz vagy alkalmazás-identitáshoz való hozzárendelésének módja a Azure Portal használatával.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 910b8dae10036cc2e396be13495fd28363dc971d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934560"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Key Vault hozzáférési szabályzat társítása a Azure Portal használatával

A Key Vault hozzáférési szabályzat meghatározza, hogy egy adott szolgáltatásnév, azaz egy alkalmazás vagy felhasználói csoport különböző műveleteket hajthat végre Key Vault [titkokon](../secrets/index.yml), [kulcsokon](../keys/index.yml)és [tanúsítványokon](../certificates/index.yml). Hozzáférési szabályzatokat a Azure Portal (ez a cikk), az [Azure CLI](assign-access-policy-cli.md)vagy a [Azure PowerShell](assign-access-policy-powershell.md)használatával rendelhet hozzá.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

További információ a Azure Active Directory csoportok létrehozásáról a Azure Portal használatával: [alapszintű csoport létrehozása és Tagok hozzáadása](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Hozzáférési szabályzat hozzárendelése

1.  A [Azure Portal](https://portal.azure.com)navigáljon a Key Vault erőforráshoz. 

1.  A **Beállítások** területen válassza a **hozzáférési szabályzatok**, majd a **hozzáférési házirend hozzáadása** elemet:

    ![Válassza a hozzáférési szabályzatok lehetőséget, majd a szerepkör-hozzárendelés hozzáadása elemet.](../media/authentication/assign-policy-portal-01.png)

1.  Válassza ki a kívánt engedélyeket a **tanúsítvány engedélyei**, a **kulcs engedélyei** és a **titkos engedélyek** területen. Kiválaszthat egy olyan sablont is, amely közös engedélyezési kombinációkat tartalmaz:

    ![Hozzáférési házirend engedélyeinek megadása](../media/authentication/assign-policy-portal-02.png)

1. A **rendszerbiztonsági tag kiválasztása** területen válassza a **nincs kiválasztott** hivatkozást a **résztvevő** kijelölése ablaktábla megnyitásához. Adja meg az alkalmazás vagy az egyszerű szolgáltatásnév nevét a keresés mezőben, válassza ki a megfelelő eredményt, majd válassza a **kiválasztás** lehetőséget.

    ![Az egyszerű szolgáltatásnév kiválasztása a hozzáférési házirendhez](../media/authentication/assign-policy-portal-03.png)

    Ha felügyelt identitást használ az alkalmazáshoz, keresse meg és válassza ki az alkalmazás nevét. (A felügyelt identitással és egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [Key Vault Authentication-app Identity and szolgáltatásnév](authentication.md#app-identity-and-security-principals).)
 
1.  Vissza a hozzáférési házirend **hozzáadása** panelen kattintson a **Hozzáadás** elemre a hozzáférési házirend mentéséhez.

    ![Hozzáférési szabályzat hozzáadása a hozzárendelt egyszerű szolgáltatáshoz](../media/authentication/assign-policy-portal-04.png)

1. A **hozzáférési házirendek** lapon ellenőrizze, hogy a hozzáférési házirend szerepel-e a **jelenlegi hozzáférési házirendek** területen, majd válassza a **Mentés** lehetőséget. A hozzáférési házirendeket a rendszer nem alkalmazza, amíg meg nem menti őket.

    ![A hozzáférési szabályzat módosításainak mentése](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>További lépések

- [Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](security-overview.md#identity-management)
- [A kulcstartó védelme](secure-your-key-vault.md).
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)