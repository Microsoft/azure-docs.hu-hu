---
title: Delegálás és szerepkörök a jogosultságok kezelésében – Azure AD
description: Megtudhatja, hogyan delegálhatja a rendszergazdák és a projektmenedzserek hozzáférési irányítását, hogy azok képesek legyenek a hozzáférés kezelésére.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204d83b96e3cbe26759d678126d8826d0b2e492e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577848"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegálás és szerepkörök az Azure AD-jogosultságok kezelésében

Alapértelmezés szerint a globális rendszergazdák és a felhasználói rendszergazdák az Azure AD-jogosultságok felügyeletének minden aspektusát létrehozzák és kezelhetik. Előfordulhat azonban, hogy a szerepkörök felhasználói nem ismerik az összes olyan helyzetet, ahol hozzáférési csomagok szükségesek. Jellemzően a megfelelő részlegeken, csapatokon vagy projekteken belül a felhasználók, akik tudják, hogy milyen erőforrásokkal és milyen hosszú ideig dolgoznak együtt a szolgáltatással. A nem rendszergazdák számára nem korlátozott engedélyek megadása helyett a lehető legkevesebb engedélyt adhat a felhasználóknak a feladataik elvégzéséhez, illetve az ütköző vagy nem megfelelő hozzáférési jogosultságok létrehozásának elkerüléséhez.

Ez a videó áttekintést nyújt arról, hogyan delegálhatja a hozzáférés-szabályozást a rendszergazdától olyan felhasználók számára, akik nem rendszergazdák.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Példa delegálásra

Ha meg szeretné tudni, hogyan delegálhatja a hozzáférési szabályozást a jogosultságok kezelésében, ez segít egy példa kialakításában. Tegyük fel, hogy a szervezete a következő rendszergazdai és felettesekkel rendelkezik.

![Delegálás a rendszergazdától a feletteseknek](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Az informatikai rendszergazdaként a Hana a marketingben, a pénzügyben és az üzleti szempontból kritikus fontosságú tartalomban lévő, a vállalat tulajdonában lévő, illetve a vállalati erőforrásokért felelős személyeknek szól.

A jogosultságok kezelésével delegálhatja a hozzáférés-vezérlést ezen nem rendszergazdák számára, mivel azok, akik tudják, hogy mely felhasználóknak van szükségük a hozzáférésre, mennyi ideig és milyen erőforrásokhoz. A nem rendszergazdák számára való delegálás biztosítja, hogy a megfelelő személyek a részlegük hozzáférését kezelik.

Itt az egyik módszer, hogy a Hana átruházhatja a hozzáférés-szabályozást a marketing, a Pénzügy és a jogi részleg számára.

1. A Hana létrehoz egy új Azure AD-beli biztonsági csoportot, és a csoport tagjaiként adja ki a fanézetet, a jelölést és a Joe-t.

1. A Hana hozzáadja ezt a csoportot a katalógus-létrehozói szerepkörhöz.

    A famodul, a Mark és a Joe mostantól katalógusokat hozhat létre a részlegek számára, erőforrásokat adhat hozzá a részlegekhez, és további delegálást végezhet a katalógusban. Nem látják egymás katalógusait.

1. A fanézet létrehoz egy **marketing** -katalógust, amely egy erőforrás-tároló.

1. Az adatforráshoz a marketing részleg tulajdonában lévő erőforrásokat adja hozzá a katalógushoz.

1. A famodul a katalógus tulajdonosai számára más személyeket is hozzáadhat a katalógushoz, ami segít megosztani a katalógus-felügyeleti feladatokat.

1. A falemez tovább delegálhatja a marketing-katalógusban található hozzáférési csomagok létrehozását és kezelését a marketing részleg projektmenedzserei számára. Ezt úgy teheti meg, hogy hozzárendeli azokat a hozzáférési csomag kezelőjének szerepkörhöz. A hozzáférési csomag kezelője létrehozhat és kezelhet hozzáférési csomagokat. 

Az alábbi ábrán a marketing, a Pénzügy és a jogi részleg erőforrásaival kapcsolatos katalógusok láthatók. Ezen katalógusok használatával a projektmenedzserek hozzáférési csomagokat hozhatnak létre a csapatokhoz vagy projektekhez.

![Jogosultsági felügyeleti delegált példa](./media/entitlement-management-delegate/elm-delegate.png)

A delegálás után a marketing részleg a következő táblázathoz hasonló szerepkörökkel rendelkezhet.

| User | Feladat szerepköre | Azure AD-szerepkör | Jogosultsági felügyeleti szerepkör |
| --- | --- | --- | --- |
| Hana | Rendszergazda | Globális rendszergazda vagy felhasználói rendszergazda |  |
| Elemér | Marketing Manager | User | Katalógus létrehozója és katalógusának tulajdonosa |
| Bob | Marketing-érdeklődő | User | Katalógus tulajdonosa |
| Jessica | Marketing projektmenedzser | User | A Package Manager elérése |

## <a name="entitlement-management-roles"></a>Jogosultsági felügyeleti szerepkörök

A jogosultságok kezeléséhez a következő szerepkörök tartoznak, amelyek a jogosultságok felügyeletére vonatkoznak.

| Jogosultsági felügyeleti szerepkör | Description |
| --- | --- |
| Katalógus létrehozója | Katalógusok létrehozása és kezelése. Általában az a rendszergazda, aki nem globális rendszergazda, vagy erőforrás-tulajdonos az erőforrások gyűjteménye számára. A katalógust létrehozó személy automatikusan a katalógus első katalógusának tulajdonosa lesz, és további katalógus-tulajdonosokat adhat hozzá. A katalógus-létrehozó nem tudja kezelni vagy megtekinteni azokat a katalógusokat, amelyeket nem a saját, és nem adhatnak hozzá olyan erőforrásokat, amelyek nem a katalógusba tartoznak. Ha a katalógus létrehozójának egy másik katalógust kell kezelnie, vagy nem a saját erőforrásokat kell hozzáadnia, kérheti, hogy a katalógus vagy az erőforrás közös tulajdonosa legyen. |
| Katalógus tulajdonosa | Meglévő katalógusok szerkesztése és kezelése. Általában az informatikai rendszergazda vagy az erőforrás tulajdonosa, vagy a felhasználó, aki a katalógus tulajdonosát választotta. |
| A Package Manager elérése | A katalógusban lévő összes meglévő hozzáférési csomag szerkesztése és kezelése. |
| Hozzáférési csomag hozzárendelés-kezelője | Az összes létező hozzáférési csomag hozzárendelésének szerkesztése és kezelése. |

Továbbá a kiválasztott jóváhagyó és egy hozzáférési csomag kérelmezője rendelkezik jogosultságokkal, de nem szerepkör.

| Jobb | Description |
| --- | --- |
| Jóváhagyó | A szabályzat engedélyezi a csomagok elérésére irányuló kérések jóváhagyását vagy elutasítását, bár a hozzáférési csomag definíciói nem módosíthatók. |
| Requestor (Kérelmező) | Egy hozzáférési csomag szabályzata által jogosult a hozzáférési csomag igénylésére. |

A következő táblázat felsorolja azokat a feladatokat, amelyeket a jogosultsági felügyeleti szerepkörök elvégezhetnek.

| Feladat | Rendszergazda | Katalógus létrehozója | Katalógus tulajdonosa | A Package Manager elérése | Hozzáférési csomag hozzárendelés-kezelője |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Delegálás katalógus-létrehozóra](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Csatlakoztatott szervezet hozzáadása](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Új katalógus létrehozása](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Erőforrás hozzáadása katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Katalógus tulajdonosának hozzáadása](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Katalógus szerkesztése](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Katalógus törlése](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Delegálás egy Access Package Managerbe](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Hozzáférési csomag kezelőjének eltávolítása](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Új hozzáférési csomag létrehozása katalógusban](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Erőforrás-szerepkörök módosítása egy hozzáférési csomagban](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Szabályzatok létrehozása és szerkesztése](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Felhasználó közvetlen kiosztása egy hozzáférési csomaghoz](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Felhasználó közvetlen eltávolítása egy hozzáférési csomagból](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Hozzáférési csomaghoz hozzárendelt hozzárendelés megtekintése](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Hozzáférési csomag kéréseinek megtekintése](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Kérelem kézbesítési hibáinak megtekintése](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Kérelem újrafeldolgozása](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Függőben lévő kérelem megszakítása](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Hozzáférési csomag elrejtése](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési csomag törlése](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Erőforrások katalógusba való felvételéhez szükséges szerepkörök

A globális rendszergazdák hozzáadhatnak vagy eltávolíthatnak bármely csoportot (felhőalapú biztonsági csoportokat vagy felhőalapú Microsoft 365-csoportokat), alkalmazást vagy SharePoint Online-webhelyet egy katalógusban. A felhasználó rendszergazdája hozzáadhat vagy eltávolíthat bármely csoportot vagy alkalmazást egy katalógusban, kivéve a címtárbeli szerepkörhöz hozzárendelhető csoportokat. Vegye figyelembe, hogy a felhasználói rendszergazdák kezelhetik a címtárbeli szerepkörhöz hozzárendelhető csoportokat tartalmazó katalógus hozzáférési csomagjait.  A szerepkörhöz hozzárendelhető csoportokkal kapcsolatos további információk [: szerepkörhöz hozzárendelhető csoport létrehozása Azure Active Directoryban](../roles/groups-create-eligible.md).

Olyan felhasználók számára, akik nem globális rendszergazda vagy felhasználói rendszergazda, csoportok, alkalmazások vagy SharePoint Online-helyek katalógusba való felvételéhez a *felhasználónak rendelkeznie kell a szükséges* Azure ad-címtárbeli szerepkörrel és a katalógus tulajdonosi jogosultsági kezelési szerepkörével. A következő táblázat felsorolja azokat a szerepkör-kombinációkat, amelyek szükségesek az erőforrások katalógushoz való hozzáadásához. Az erőforrások katalógusból való eltávolításához ugyanazokat a szerepköröket kell megadnia.

| Azure AD-címtárbeli szerepkör | Jogosultsági felügyeleti szerepkör | Hozzáadhat biztonsági csoportot | Hozzáadhat Microsoft 365 csoportot | Hozzáadhat alkalmazást | Hozzáadhat SharePoint Online-webhelyet |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globális rendszergazda](../roles/permissions-reference.md) | n.a. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Felhasználói rendszergazda](../roles/permissions-reference.md) | n.a. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-rendszergazda](../roles/permissions-reference.md) | Katalógus tulajdonosa | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-rendszergazda](../roles/permissions-reference.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [Teams szolgáltatás rendszergazdája](../roles/permissions-reference.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [SharePoint-rendszergazda](../roles/permissions-reference.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Alkalmazás-rendszergazda](../roles/permissions-reference.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| [Cloud Application Administrator](../roles/permissions-reference.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| User | Katalógus tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha az alkalmazás tulajdonosa |  |

Egy feladat legkevésbé Kiemelt szerepkörének meghatározásához [a rendszergazdai szerepkörök a Azure Active Directoryban](../roles/delegate-by-task.md#entitlement-management)is hivatkozhatnak a rendszergazda szerepkörre.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési szabályozás delegálása a katalógus-létrehozók számára](entitlement-management-delegate-catalog.md)
- [Erőforrások katalógusának létrehozása és kezelése](entitlement-management-catalog-create.md)
