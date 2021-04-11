---
title: Egyéni szerepkör engedélyei az alkalmazás regisztrálásához – Azure AD | Microsoft Docs
description: Az egyéni rendszergazdai szerepkör engedélyeinek delegálása az alkalmazások regisztrálásának kezeléséhez.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af2c1b912afbcf44cefbfb021c592836dbde5b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466286"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Alkalmazás-regisztrációs engedélyek a Azure Active Directory egyéni szerepköreihez

Ez a cikk a jelenleg elérhető alkalmazás-regisztrációs engedélyeket tartalmazza a Azure Active Directory (Azure AD) egyéni szerepkör-definícióinak esetében.

## <a name="permissions-for-managing-single-tenant-applications"></a>Az egybérlős alkalmazások kezelésére vonatkozó engedélyek

Az egyéni szerepkör engedélyeinek kiválasztásakor lehetősége van arra, hogy hozzáférést biztosítson csak egybérlős alkalmazások kezeléséhez. az egybérlős alkalmazások csak az Azure AD-szervezet azon felhasználói számára érhetők el, ahol az alkalmazás regisztrálva van. az egybérlős alkalmazások úgy vannak definiálva, hogy **támogatott fióktípus** legyenek beállítva a "fiókok ebben a szervezeti címtárban" beállítás. A Graph API az egybérlős alkalmazások esetében a signInAudience tulajdonság értéke "AzureADMyOrg".

Ahhoz, hogy hozzáférést biztosítson csak egybérlős alkalmazások kezeléséhez, használja az alábbi engedélyeket az **Applications. myOrganization** altípussal. Például: Microsoft. Directory/Applications. myOrganization/Basic/Update.

Tekintse meg az [Egyéni szerepkörök áttekintése](custom-overview.md) című témakört, amelyből megtudhatja, hogy mi az általános kifejezés altípusa, az engedély és a tulajdonság beállítása. Az alábbi információk az alkalmazások regisztrálására vonatkoznak.

### <a name="create-and-delete"></a>Létrehozás és törlés

Két engedély áll rendelkezésre az alkalmazás-regisztrációk létrehozásának lehetőségére, amelyek mindegyike különböző viselkedéssel rendelkezik:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>Microsoft. Directory/alkalmazások/createAsOwner

Az engedély kiosztása azt eredményezi, hogy a létrehozó létrehozta a létrehozott alkalmazás regisztrációjának első tulajdonosát, és a létrehozott alkalmazás regisztrálása a létrehozó 250 létrehozott objektumok kvótájának megfelelően fog megjelenni.

#### <a name="microsoftdirectoryapplicationscreate"></a>Microsoft. Directory/alkalmazások/létrehozás

Az engedély kiosztása azt eredményezi, hogy a létrehozó nem lesz hozzáadva a létrehozott alkalmazás regisztrációjának első tulajdonosaként, és a létrehozott alkalmazás regisztrálása nem fog megjelenni a létrehozó 250 létrehozott objektum-kvótájában. Ezt az engedélyt körültekintően kell használni, mivel semmi nem akadályozza meg, hogy az alkalmazás regisztrációkat hozzon létre, amíg meg nem találja a címtár-szintű kvótát. Ha mindkét engedély hozzá van rendelve, ez az engedély elsőbbséget élvez.

Ha mindkét engedély hozzá van rendelve, akkor a/Create engedély elsőbbséget élvez. Bár a/createAsOwner engedély nem adja hozzá automatikusan a létrehozót az első tulajdonosként, a tulajdonosok a Graph API-k vagy a PowerShell-parancsmagok használatakor is megadhatók az alkalmazás regisztrációjának létrehozása során.

A létrehozási engedélyek hozzáférést biztosítanak az **új regisztrációs** parancshoz.

[Ezek az engedélyek hozzáférést biztosítanak az új regisztrációs portál parancshoz](./media/custom-available-permissions/new-custom-role.png)

Az alkalmazások regisztrációjának törlésére két engedély áll rendelkezésre:

#### <a name="microsoftdirectoryapplicationsdelete"></a>Microsoft. Directory/alkalmazások/törlés

Lehetővé teszi az alkalmazások regisztrációjának törlését a altípustól függetlenül; vagyis az egybérlős és a több-bérlős alkalmazások is.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. myOrganization/delete

Lehetővé teszi az alkalmazások regisztrációjának törlését azokra a felhasználókra, amelyek csak a szervezet vagy egy bérlős alkalmazás (myOrganization altípusa) számára érhetők el.

![Ezek az engedélyek hozzáférést biztosítanak az alkalmazás regisztrációjának törlése parancshoz](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> A létrehozási engedélyeket tartalmazó szerepkör hozzárendelésekor a szerepkör-hozzárendelést a címtár hatókörében kell elvégezni. Az erőforrás-hatókörben hozzárendelt létrehozási engedély nem teszi lehetővé az alkalmazások regisztrációjának létrehozását.

### <a name="read"></a>Olvasás

A szervezet minden felhasználója alapértelmezés szerint képes beolvasni az alkalmazás regisztrációs adatait. A vendég felhasználói és az alkalmazás-szolgáltatási rendszerbiztonsági tag azonban nem. Ha egy szerepkört egy vendég felhasználóhoz vagy alkalmazáshoz szeretne hozzárendelni, a megfelelő olvasási engedélyeket is meg kell adnia.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/alkalmazások/allProperties/olvasás

Az egybérlős és több-bérlős alkalmazások összes olyan tulajdonságának olvasása, amely nem olvasható olyan helyzetekben, mint a hitelesítő adatok.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myOrganization/allProperties/READ

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/allProperties/READ, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/alkalmazások/tulajdonosok/olvasás

Lehetővé teszi az egybérlős és a több-bérlős alkalmazások tulajdonosi tulajdonságainak olvasását. Hozzáférést biztosít az összes mezőhöz az alkalmazás-regisztrációs tulajdonosok lapon:

![Ez az engedély hozzáférést biztosít az alkalmazás-regisztrációs tulajdonosok laphoz](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. Directory/alkalmazások/standard/olvasás

Hozzáférést biztosít a szabványos alkalmazás-regisztrációs tulajdonságok olvasásához. Ez az alkalmazás regisztrációs oldalain található tulajdonságokat is magában foglalja.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myOrganization/standard/READ

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/standard/READ, de csak egybérlős alkalmazások esetében.

### <a name="update"></a>Frissítés

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/alkalmazások/allProperties/Update

Lehetőség az egybérlős és a több-bérlős alkalmazások összes tulajdonságának frissítésére.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. myOrganization/allProperties/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/allProperties/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>Microsoft. Directory/alkalmazások/célközönség/frissítés

Az egybérlős és a több-bérlős alkalmazások támogatott fiók típusának (signInAudience) tulajdonságának frissítése.

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációja által támogatott fiók Type tulajdonságához a hitelesítés lapon](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. myOrganization/hallgatóság/frissítés

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/hallgatóság/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>Microsoft. Directory/alkalmazások/hitelesítés/frissítés

A válasz URL-cím, a kijelentkezési URL-cím, az implicit folyamat és a közzétevői tartomány tulajdonságainak frissítése egyetlen bérlős és több-bérlős alkalmazásokban. Hozzáférést biztosít az alkalmazás regisztrációs hitelesítése oldalon található összes mezőhöz a támogatott fióktípus kivételével:

![Hozzáférést biztosít az alkalmazás regisztrációs hitelesítéséhez, de a fiókok típusai nem támogatottak](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. myOrganization/Authentication/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/Authentication/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/alkalmazások/alapszintű/frissítés

A név, embléma, Kezdőlap URL-cím, szolgáltatási URL-cím és adatvédelmi nyilatkozat URL-tulajdonságainak frissítése az egybérlős és a több-bérlős alkalmazásokban. Hozzáférést biztosít az alkalmazás regisztrációjának védjegyezése lapon lévő összes mezőhöz:

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációjának védjegyezési lapjához](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. myOrganization/Basic/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/Basic/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés

Az egybérlős és a több-bérlős alkalmazásokhoz tartozó tanúsítványok és az ügyfél-titkok tulajdonságainak frissítése. Hozzáférést biztosít az alkalmazás regisztrációs tanúsítványainak & Secrets oldalon található összes mezőhöz:

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációs tanúsítványainak & Secrets oldalához](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. myOrganization/hitelesítő adatok/frissítés

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory/alkalmazások/tulajdonosok/frissítés

Az egybérlős és a több-bérlős tulajdonos tulajdonságának frissítése. Hozzáférést biztosít az összes mezőhöz az alkalmazás-regisztrációs tulajdonosok lapon:

![Ez az engedély hozzáférést biztosít az alkalmazás-regisztrációs tulajdonosok laphoz](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. myOrganization/owners/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/owners/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/alkalmazások/engedélyek/frissítés

Lehetőség van a delegált engedélyek, alkalmazás-engedélyek, engedélyezett ügyfélalkalmazások, szükséges engedélyek frissítésére, valamint az egybérlős és a több-bérlős alkalmazások engedélyezési tulajdonságainak megadására. A nem teszi lehetővé a jóváhagyás végrehajtását. Hozzáférést biztosít az alkalmazás regisztrációs API-engedélyeihez tartozó összes mezőhöz, és elérhetővé teszi az API-lapokat:

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációs API-engedélyeinek oldalához](./media/custom-available-permissions/app-registration-api-permissions.png)

![Ezek az engedélyek hozzáférést biztosítanak az alkalmazás regisztrálásához API-oldalként](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. myOrganization/engedélyek/frissítés

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/permissions/Update, de csak az egybérlős alkalmazások esetében.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Következő lépések

- Egyéni szerepkörök létrehozása [a Azure Portal, az Azure ad PowerShell és a Graph API](custom-create.md) használatával
- [Szerepkör-hozzárendelések felsorolása](view-assignments.md)
