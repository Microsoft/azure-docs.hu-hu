---
title: Office 365 külső megosztási és B2B-együttműködés – Azure AD
description: A O365 és a Azure Active Directory B2B együttműködés segítségével ismerteti az erőforrások megosztását külső partnerekkel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb407735cff5a0c43d3a584ff37a0eabc178381
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367444"
---
# <a name="office-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Office 365 külső megosztási és Azure Active Directory (Azure AD) B2B-együttműködés

Az Azure AD B2B együttműködés és az Office 365 külső megosztás (OneDrive, SharePoint Online, egyesített csoportok stb.) esetében a külső felhasználók hitelesítése az Azure AD B2B használatával történik.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Miben különbözik az Azure AD B2B a külső megosztástól a SharePoint Online-ban?

A OneDrive/SharePoint Online külön meghívó-kezelővel rendelkezik. A külső megosztás támogatása a OneDrive-ben és a SharePoint Online-ban megkezdődött, mielőtt az Azure AD kifejlesztette a támogatását. Idővel a OneDrive/SharePoint Online külső megosztása számos szolgáltatást és több millió felhasználót halmozott fel, akik a termék beépített megosztási mintáját használják. A OneDrive/SharePoint Online külső megosztás működése és az Azure AD B2B-együttműködés működése között azonban van néhány finom különbség. További információt a OneDrive/SharePoint Online külső megosztásról a [külső megosztás áttekintésében](https://docs.microsoft.com/sharepoint/external-sharing-overview)talál. A folyamat általában az alábbi módokon tér el az Azure AD B2B-től:

- A OneDrive/SharePoint Online hozzáadja a felhasználókat a címtárhoz, miután a felhasználók beváltották a meghívókat. Tehát a beváltást megelőzően nem jelenik meg a felhasználó az Azure AD-portálon. Ha egy másik hely meghívja a felhasználót egy időben, új meghívás jön létre. Az Azure AD B2B-együttműködés használatakor azonban a rendszer azonnal felveszi a felhasználókat a meghívásra, hogy azok mindenhol megjelenjenek.

- A OneDrive-ben és a SharePoint Online-ban beváltási élmény különbözik az Azure AD B2B együttműködésben tapasztalt élménytől. Miután egy felhasználó beváltotta a meghívót, a tapasztalatok megegyeznek.

- Az Azure AD B2B együttműködés meghívott felhasználóit a OneDrive/SharePoint Online megosztás párbeszédpanelekről lehet kiválasztani. A OneDrive/SharePoint Online meghívott felhasználók az Azure AD-ben is megjelennek a meghívóik beváltása után.

- A licencelési követelmények eltérnek. Minden fizetős Azure AD-licenc esetében akár 5 vendég felhasználó is hozzáférhet a fizetős Azure AD-funkciókhoz. A licenceléssel kapcsolatos további tudnivalókért tekintse meg az [Azure ad B2B licencelés](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) és [a "mi a külső felhasználó?" című részt a SharePoint Online külső megosztásának áttekintésében](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

A OneDrive/SharePoint Online-ban az Azure AD B2B együttműködéssel való külső megosztás kezeléséhez állítsa be a OneDrive/SharePoint Online külső megosztási beállítást úgy, hogy **csak a szervezet címtárában már meglévő külső felhasználók számára engedélyezze a megosztást**. A felhasználók a külsőleg megosztott webhelyekre léphetnek, és a rendszergazda által hozzáadott külső közreműködők közül választhatnak. A rendszergazda a B2B Collaboration Meghívási API-kon keresztül veheti fel a külső közreműködőket.


![A OneDrive/SharePoint Online külső megosztási beállítása](media/o365-external-user/odsp-sharing-setting.png)

A külső megosztás engedélyezése után lehetőség van arra, hogy a felhasználók a SharePoint Online-ban (a Spongya) a meglévő vendég felhasználókat is megkeressék.

Ezt a funkciót a bérlői és a webhelycsoport szintjén a "ShowPeoplePickerSuggestionsForGuestUsers" beállítás használatával engedélyezheti. A szolgáltatást a set-SPOTenant és a set-SPOSite parancsmagok segítségével állíthatja be, amelyek lehetővé teszik a tagok számára a címtárban lévő összes vendég felhasználó keresését. A bérlői hatókör változásai nincsenek hatással a már kiépített Spongya-webhelyekre.

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B csoportmunka-felhasználó hozzáadása egy szerepkörhöz](add-guest-to-role.md)
* [B2B együttműködési meghívások delegálása](delegate-invitations.md)
* [Dinamikus csoportok és B2B-együttműködés](use-dynamic-groups.md)
* [Azure Active Directory B2B-együttműködés hibaelhárítása](troubleshoot.md)
