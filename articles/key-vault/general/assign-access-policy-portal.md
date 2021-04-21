---
title: Hozzáférés-Azure Key Vault szabályzat hozzárendelése (Portál)
description: Hogyan használható a Azure Portal egy Key Vault hozzáférési szabályzat hozzárendelése egy rendszerbiztonsági taghoz vagy alkalmazásidentitáshoz.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 443b269e7155fc206ee50e7907a7acded2c22f53
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751489"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Hozzáférés-Key Vault hozzárendelése a Azure Portal

A Key Vault házirend határozza meg, hogy egy adott rendszerbiztonsági tag, azaz felhasználó, alkalmazás vagy felhasználói [](../secrets/index.yml)csoport [](../keys/index.yml)különböző műveleteket hajthat-e végre Key Vault titkos kulcsokon, kulcsokon és [tanúsítványokon.](../certificates/index.yml) A hozzáférési szabályzatokat a következő Azure Portal (ez a cikk), az [Azure CLI](assign-access-policy-cli.md)vagy a [Azure PowerShell.](assign-access-policy-powershell.md)

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

További információ a csoportok létrehozásáról a Azure Active Directory a Azure Portal: [Alapszintű](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) csoport létrehozása és tagok hozzáadása

## <a name="assign-an-access-policy"></a>Hozzáférési szabályzat hozzárendelése

1.  A [Azure Portal](https://portal.azure.com)lépjen a Key Vault erőforráshoz. 

1.  A **Beállítások alatt** válassza a Hozzáférési **szabályzatok** lehetőséget, majd a **Hozzáférési szabályzat hozzáadása lehetőséget:**

    ![Válassza a Hozzáférési szabályzatok, majd a Szerepkör-hozzárendelés hozzáadása lehetőséget.](../media/authentication/assign-policy-portal-01.png)

1.  Válassza ki a kívánt engedélyeket a Tanúsítványengedélyek, **a Kulcsengedélyek** és a **Titkos kulcs engedély alatt.** Olyan sablont is kiválaszthat, amely gyakori engedélykombinációkat tartalmaz:

    ![Hozzáférési szabályzat engedélyeinek megadása](../media/authentication/assign-policy-portal-02.png)

1. A **Rendszerbiztonsági tag kiválasztása alatt** válassza a Nincs **kiválasztva** hivatkozást az Egyszerű **kiválasztási** panel megnyitásához. Írja be a felhasználó, alkalmazás vagy szolgáltatásnév nevét a keresőmezőbe, válassza ki a megfelelő eredményt, majd válassza a **Kijelölés lehetőséget.**

    ![A hozzáférési szabályzat rendszerbiztonsági tagjának kiválasztása](../media/authentication/assign-policy-portal-03.png)

    Ha felügyelt identitást használ az alkalmazáshoz, keresse meg és válassza ki magának az alkalmazásnak a nevét. (A felügyelt identitásokkal és szolgáltatásnévvel kapcsolatos további információkért lásd: Key Vault hitelesítés – alkalmazásidentitás [és szolgáltatásnév .)](authentication.md#app-identity-and-security-principals)
 
1.  A Hozzáférési szabályzat **hozzáadása panelre visszatérve** válassza a **Hozzáadás lehetőséget** a hozzáférési szabályzat mentéshez.

    ![A hozzáférési szabályzat hozzáadása a hozzárendelt rendszerbiztonsági taggal](../media/authentication/assign-policy-portal-04.png)

1. A Hozzáférési **szabályzatok lapra visszatérve** ellenőrizze, hogy a hozzáférési szabályzat szerepel-e az Aktuális hozzáférési **szabályzatok** listában, majd válassza a **Mentés lehetőséget.** A hozzáférési szabályzatokat a rendszer nem alkalmazza, amíg nem menti őket.

    ![A hozzáférési szabályzat módosításainak mentése](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault biztonság: Identitás- és hozzáférés-kezelés](security-overview.md#identity-management)
- [Kulcstartó biztonságossá való tere.](security-overview.md)
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)