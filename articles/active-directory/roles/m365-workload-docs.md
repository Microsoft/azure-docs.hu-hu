---
title: Rendszergazdai szerepkör docs Microsoft 365 szolgáltatások között – Azure AD | Microsoft Docs
description: Tartalom és API-referenciák keresése a Microsoft 365-szolgáltatások felügyeleti szerepköreihez Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f56f8ac42f0db3d2cd27453cd023a2e869b0cde0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466082"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>A Azure Active Directory Microsoft 365 szolgáltatásainak szerepkörei

A Microsoft 365ban található összes termék felügyelhető a Azure Active Directory (Azure AD) felügyeleti szerepköreivel. Egyes termékek a termékre vonatkozó további szerepköröket is biztosítanak. Az egyes termékek által támogatott szerepkörökkel kapcsolatos információkért tekintse meg az alábbi táblázatot. A szerepkör-biztonsági tervezéssel kapcsolatos útmutatásért lásd: az [Azure ad-ben az emelt szintű hozzáférés biztonságossá tétele a hibrid és a Felhőbeli](security-planning.md)környezetekben.

## <a name="where-to-find-content"></a>Hol található a tartalom?

Microsoft 365 szolgáltatás | Szerepkör tartalma | API-tartalom
---------------------- | ------------------ | -----------------
Rendszergazdai szerepkörök az Office 365-ben és Microsoft 365 üzleti tervekben | [Rendszergazdai szerepkörök Microsoft 365](/office365/admin/add-users/about-admin-roles) | Nem elérhető
Azure Active Directory (Azure AD) és Azure AD Identity Protection| [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list)
Exchange Online| [Exchange szerepköralapú hozzáférés-vezérlés](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell Exchange-hez](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Szerepkör-hozzárendelések beolvasása](/powershell/module/exchange/role-based-access-control/get-rolegroup)
SharePoint Online | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md)<br>További [információ a SharePoint-rendszergazdai szerepkörről Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list)
Csapatok/Skype vállalati verzió | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list)
Biztonsági & megfelelőségi központ (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365-rendszergazdai szerepkörök](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Szerepkör-hozzárendelések beolvasása](/powershell/module/exchange/role-based-access-control/get-rolegroup)
Biztonságos pontszám | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list)
Compliance Manager | [A megfelelőségi kezelő szerepkörei](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Nem elérhető
Azure Information Protection | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list)
Microsoft Cloud App Security | [Szerepköralapú hozzáférés-vezérlés](/cloud-app-security/manage-admins) | [API-leírások](/cloud-app-security/api-tokens) 
Azure Komplex veszélyforrások elleni védelem | [Az Azure ATP szerepkörcsoportjai](/azure-advanced-threat-protection/atp-role-groups) | Nem elérhető
Windows Defender Komplex veszélyforrások elleni védelem | [Windows Defender ATP szerepköralapú hozzáférés-vezérlés](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Nem elérhető
Privileged Identity Management | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list)
Intune | [Intune szerepköralapú hozzáférés-vezérlés](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Felügyelt asztal | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list)

## <a name="next-steps"></a>Következő lépések

* [Azure AD-rendszergazdai szerepkörök kiosztása vagy eltávolítása](manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepköreinek referenciája](permissions-reference.md)