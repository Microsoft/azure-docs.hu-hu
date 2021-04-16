---
title: Felügyeleti egységek hozzáadása és eltávolítása – Azure Active Directory | Microsoft Docs
description: A felügyeleti egységek használatával korlátozhatja a szerepkör-engedélyek hatókörét a Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fad9356d3379e76aa259d67711d18f14a4e266f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505275"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Felügyeleti egységek kezelése a Azure Active Directory

A Azure Active Directory (Azure AD) részletesebb felügyeleti vezérlése érdekében a felhasználókat hozzárendelheti egy Azure AD-szerepkörhöz egy vagy több felügyeleti egységre korlátozott hatókörrel.

## <a name="get-started"></a>Bevezetés

1. Ha lekérdezéseket futtat a következő utasításokból a [Graph Explorerben,](https://aka.ms/ge)tegye a következőket:

    a. A Azure Portal az Azure AD-hez. 
    
    b. Az alkalmazások listájában válassza **a** Graph-vizsgáló.
    
    c. Az Engedélyek **panelen** válassza a Rendszergazdai hozzájárulás **megadása a Graph-vizsgáló.**

    ![A "Rendszergazdai jóváhagyás megadása a Graph-vizsgáló" hivatkozást bemutató képernyőkép.](./media/admin-units-manage/select-graph-explorer.png)


1. Használja [az Azure AD PowerShellt.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="add-an-administrative-unit"></a>Felügyeleti egység hozzáadása

A felügyeleti egységeket a következő parancsokkal használhatja: Azure Portal PowerShell.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal az Azure AD-hez. Ezután a bal oldali panelen válassza a Felügyeleti **egységek lehetőséget.**

    ![Az Azure AD Felügyeleti egységek hivatkozásának képernyőképe.](./media/admin-units-manage/nav-to-admin-units.png)

1. Válassza a **hozzáadás** gombot a panel felső részén,  majd a Név mezőben adja meg a felügyeleti egység nevét. Ha szükséges, adja meg a felügyeleti egység leírását.

    ![Képernyőkép a Hozzáadás gombról és a Felügyeleti egység nevének beírását mutató Név mezőről.](./media/admin-units-manage/add-new-admin-unit.png)

1. Válassza a kék **Hozzáadás gombot** a felügyeleti egység véglegesítéül.

### <a name="use-powershell"></a>A PowerShell használata

Az alábbi parancsok futtatása előtt telepítse az [Azure AD PowerShellt:](https://www.powershellgallery.com/packages/AzureAD/)

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Az idézőjelek között megadott értékeket szükség szerint módosíthatja.

### <a name="use-microsoft-graph"></a>A Microsoft Graph

Kérés

```http
POST /administrativeUnits
```

Törzs

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Felügyeleti egység eltávolítása

Az Azure AD-ban eltávolíthat egy felügyeleti egységet, amely már nem szükséges felügyeleti szerepkörök hatóköregységeként.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal válassza az **Azure AD** lehetőséget, majd válassza a **Felügyeleti egységek lehetőséget.** 
1. Válassza ki a törölni kívánt felügyeleti egységet, majd válassza a **Törlés lehetőséget.** 
1. A felügyeleti egység törlésének megerősítéséhez válassza az **Igen lehetőséget.** A felügyeleti egység törlődik.

![Képernyőkép a felügyeleti egység Törlés gombjáról és a megerősítési ablakról.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>A PowerShell használata

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

Az idézőjelek között megadott értékeket szükség szerint módosíthatja az adott környezetben.

### <a name="use-the-graph-api"></a>A Graph API-t használják

Kérés

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Törzs

```http
{}
```

## <a name="next-steps"></a>Következő lépések

* [Felhasználók kezelése felügyeleti egységben](admin-units-add-manage-users.md)
* [Csoportok kezelése egy felügyeleti egységben](admin-units-add-manage-groups.md)
