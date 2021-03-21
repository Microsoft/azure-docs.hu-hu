---
title: Törölt Microsoft 365 csoport visszaállítása – Azure AD | Microsoft Docs
description: Törölt csoport visszaállítása, visszaállítható csoportok megtekintése és csoport végleges törlése Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1eacba47e68310dd45976bcd9ddff052fa193436
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090137"
---
# <a name="restore-a-deleted-microsoft-365-group-in-azure-active-directory"></a>Törölt Microsoft 365 csoport visszaállítása Azure Active Directory

Ha töröl egy Microsoft 365 csoportot a Azure Active Directoryban (Azure AD), a törölt csoport megmarad, de a törlés időpontjától számított 30 napig nem látható. Ennek célja az, hogy a csoport és tartalma szükség esetén visszaállítható legyen. Ez a funkció kizárólag az Azure AD-ben Microsoft 365 csoportokra korlátozódik. Nem használható biztonsági csoportok és terjesztési csoportok esetén. Vegye figyelembe, hogy a 30 napos csoportos visszaállítási időszak nem szabható testre.

> [!NOTE]
> Ne használja a `Remove-MsolGroup` parancsot, mert azzal véglegesen törli a csoportot. Mindig `Remove-AzureADMSGroup` Microsoft 365 csoport törlésére használható.

A csoportok visszaállításához szükséges engedély az alábbiak bármelyike lehet:

Szerepkör | Engedélyek
--------- | ---------
Globális rendszergazda, csoport rendszergazdája, partner szint-támogatás és Intune-rendszergazda | Visszaállíthatja az összes törölt Microsoft 365 csoportot
Felhasználói rendszergazda és partner Tier1-támogatás | Visszaállíthatja az összes törölt Microsoft 365 csoportot, kivéve azokat a csoportokat, amelyek a globális rendszergazdai szerepkörhöz vannak rendelve
User | Visszaállíthatja az összes törölt Microsoft 365 csoportot.

## <a name="view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore"></a>A visszaállításhoz elérhető törölt Microsoft 365 csoportok megtekintése és kezelése

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal.

2. Válassza a **csoportok** lehetőséget, majd válassza a **törölt csoportok** lehetőséget a visszaállítani kívánt törölt csoportok megtekintéséhez.

    ![a visszaállításhoz elérhető csoportok megtekintése](./media/groups-restore-deleted/deleted-groups3.png)

3. A **törölt csoportok** panelen a következőket teheti:

   - Állítsa vissza a törölt csoportot és annak tartalmát a **Restore Group (visszaállítási csoport**) lehetőség kiválasztásával.
   - Véglegesen távolítsa el a törölt csoportot a **Törlés végleges** lehetőség kiválasztásával. A csoportok végleges eltávolításához rendszergazdának kell lennie.

## <a name="view-the-deleted-microsoft-365-groups-that-are-available-to-restore-using-powershell"></a>A PowerShell használatával történő visszaállításhoz elérhető törölt Microsoft 365 csoportok megtekintése

Az alábbi parancsmagokkal tekintheti meg a törölt csoportokat annak ellenőrzéséhez, hogy a kívánt csoport vagy csoportok végleges törlése nem történt-e még meg. A parancsmagok az [Azure AD PowerShell-modul](https://www.powershellgallery.com/packages/AzureAD/) részét képezik. A modullal kapcsolatban az [Azure Active Directory PowerShell 2-es verzióját](/powershell/azure/active-directory/install-adv2) ismertető cikkben tekinthet meg további információt.

1.  Futtassa a következő parancsmagot az Azure AD-szervezetben lévő összes törölt Microsoft 365 csoport megjelenítéséhez, amelyek továbbra is elérhetők a visszaállításhoz.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Ha ismeri egy adott csoport objektumazonosítóját (amelyet egyébként az 1. lépésben található parancsmag kimenetében láthat), az alábbi parancsmag futtatásával ellenőrizheti, hogy az adott törölt csoport végleges törlése megtörtént-e már.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-microsoft-365-group-using-powershell"></a>Törölt Microsoft 365 csoport visszaállítása a PowerShell használatával

Miután ellenőrizte, hogy a csoport visszaállítható-e, az alábbi lépések valamelyikét végrehajtva állíthatja vissza a törölt csoportot. Ha a csoport dokumentumokat, SP-webhelyeket vagy más állandó objektumokat tartalmaz, a csoport és a tartalmainak teljes visszaállítása akár 24 órát is igénybe vehet.

1. A csoport és a tartalmainak visszaállításához futtassa az alábbi parancsmagot.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. A törölt csoport az alábbi parancsmag futtatásával távolítható el véglegesen.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Hogyan ellenőrizhető a visszaállítás sikeressége?

Annak ellenőrzéséhez, hogy sikeresen visszaállította-e a Microsoft 365 csoportot, futtassa a `Get-AzureADGroup –ObjectId <objectId>` parancsmagot a csoport adatainak megjelenítéséhez. A visszaállítási kérés befejeződését követően:

- A csoport megjelenik az Exchange bal oldali navigációs sávján.
- A csoportra vonatkozó terv megjelenik a Planner alkalmazásban.
- Minden SharePoint-webhely és annak tartalma elérhető lesz
- A csoport a Microsoft 365 csoportokat támogató Exchange-végpontokból és egyéb Microsoft365-munkaterhelésből is elérhető.

## <a name="next-steps"></a>Következő lépések

E cikkekben további információk találhatók az Azure Active Directory-csoportokkal kapcsolatban.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
