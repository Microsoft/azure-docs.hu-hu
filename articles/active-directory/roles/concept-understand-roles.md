---
title: A Azure Active Directory-szerepkörökkel kapcsolatos fogalmak ismertetése
description: Megtudhatja, hogyan értelmezheti Azure Active Directory beépített és egyéni szerepköröket Azure Active Directory erőforrás-hatókörével.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71291458eec0aec13542d3e0dfaf04a96f391a23
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466116"
---
# <a name="understand-roles-in-azure-active-directory"></a>A Azure Active Directory szerepköreinek megismerése

Körülbelül 60 Azure Active Directory (Azure AD) beépített szerepkör áll rendelkezésre, amelyek a szerepkör-engedélyek rögzített készletével rendelkező szerepkörök. A beépített szerepkörök kiegészítéseként az Azure AD az egyéni szerepköröket is támogatja. Egyéni szerepkörök használatával válassza ki a kívánt szerepkör-engedélyeket. Létrehozhat például egy olyan Azure AD-erőforrások kezelésére, mint például az alkalmazások vagy az egyszerű szolgáltatások.

Ez a cikk ismerteti az Azure AD-szerepköröket és azok használatát.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Hogyan különböznek az Azure AD szerepkörei más Microsoft 365 szerepköröktől

Számos különböző szolgáltatás található Microsoft 365ban, például az Azure AD-ben és az Intune-ban. Ezen szolgáltatások némelyike saját szerepköralapú hozzáférés-vezérlési rendszerrel rendelkezik; kifejezetten

- Azure AD
- Exchange
- Intune
- Security Center
- Megfelelőségi központ
- Microsoft Cloud App Security
- Kereskedelem

Más szolgáltatások, például a csapatok, a SharePoint és a felügyelt asztali számítógépek nem rendelkeznek külön szerepköralapú hozzáférés-vezérlési rendszerrel. Az Azure AD-szerepköröket használják a rendszergazdai hozzáféréshez. Az Azure saját szerepköralapú hozzáférés-vezérlési rendszerrel rendelkezik az Azure-erőforrásokhoz, például a virtuális gépekhez, és ez a rendszer nem ugyanaz, mint az Azure AD-szerepkörök.

Ha külön szerepkör-alapú hozzáférés-vezérlési rendszert választunk. Ez azt jelenti, hogy van egy másik adattár, ahol a szerepkör-definíciók és a szerepkör-hozzárendelések tárolódnak. Hasonlóképpen van egy másik házirend-döntési pont, ahol a hozzáférés-ellenőrzések történnek. További információ: [Microsoft 365-szolgáltatások szerepkörei az Azure ad-ben](m365-workload-docs.md) és a [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Bizonyos Azure AD-szerepkörök miért más szolgáltatásokhoz tartoznak

Microsoft 365 számos szerepköralapú hozzáférés-vezérlési rendszerrel rendelkezik, amelyek egymástól függetlenül, a saját szolgáltatási portálján vannak kifejlesztve. Annak érdekében, hogy az Azure AD-portálról Microsoft 365 az identitást, az egyes szolgáltatásokra jellemző beépített szerepköröket is hozzáadta, amelyek mindegyike rendszergazdai hozzáférést biztosít egy Microsoft 365 szolgáltatáshoz. Ilyen például az Exchange-rendszergazdai szerepkör az Azure AD-ben. Ez a szerepkör egyenértékű a [szervezeti felügyeleti szerepkör-csoporttal](/exchange/organization-management-exchange-2013-help) az Exchange szerepköralapú hozzáférés-vezérlési rendszerében, és képes kezelni az Exchange összes aspektusát. Hasonlóképpen hozzáadjuk az Intune rendszergazdai szerepkört, a Teams Administratort, a SharePoint-rendszergazdát és így tovább. A szolgáltatás-specifikus szerepkörök az Azure AD beépített szerepköreinek egyik kategóriája a következő szakaszban.

## <a name="categories-of-azure-ad-roles"></a>Azure AD-szerepkörök kategóriái

Az Azure AD beépített szerepkörei eltérnek a használatuk helyétől, amelyek a következő három széles kategóriába tartoznak.

- **Azure ad-specifikus szerepkörök**: ezek a szerepkörök csak az Azure ad-ben lévő erőforrások kezeléséhez biztosítanak engedélyeket. Például a felhasználó rendszergazdája, az alkalmazás rendszergazdája, a csoportok rendszergazdája minden engedélyt biztosít az Azure AD-ben élő erőforrások kezeléséhez.
- **Szolgáltatás-specifikus szerepkörök**: a főbb Microsoft 365 szolgáltatások (nem Azure ad) esetében olyan szolgáltatás-specifikus szerepköröket állítottunk össze, amelyek engedélyeket biztosítanak a szolgáltatásban lévő összes szolgáltatás kezeléséhez.  Például az Exchange-rendszergazda, az Intune-rendszergazda, a SharePoint-rendszergazda és a Teams rendszergazdai szerepkörök a megfelelő szolgáltatásokkal kezelhetik a szolgáltatásokat. Az Exchange-rendszergazda kezelheti a postaládákat, az Intune-rendszergazda felügyelheti az eszközöket, a SharePoint-rendszergazdák kezelhetik a webhelycsoportok kezelését, a csapatok rendszergazdái kezelhetik a hívási tulajdonságokat és így tovább.
- Szolgáltatások **közötti szerepkörök**: vannak olyan szerepkörök, amelyek a szolgáltatásokra terjednek ki. Két globális szerepkörünk van – globális rendszergazda és globális olvasó. Az összes Microsoft 365 szolgáltatás tiszteletben tartja ezt a két szerepkört. Emellett vannak olyan biztonsággal kapcsolatos szerepkörök is, mint például a biztonsági rendszergazda és a biztonsági olvasó, amely a Microsoft 365on belüli több biztonsági szolgáltatáshoz biztosít hozzáférést. Ha például az Azure AD-ben biztonsági rendszergazdai szerepköröket használ, felügyelheti Microsoft 365 Security Center, a Microsoft Defender komplex veszélyforrások elleni védelmet és a Microsoft Cloud App Securityt. Hasonlóképpen, a megfelelőségi rendszergazda szerepkörben felügyelheti a megfelelőséggel kapcsolatos beállításokat Microsoft 365 megfelelőségi központban, az Exchange-ben stb.

![Az Azure AD beépített szerepköreinek három kategóriája](./media/concept-understand-roles/role-overlap-diagram.png)

A következő táblázat segítséget nyújt a szerepkör-kategóriák megismeréséhez. A kategóriák neve tetszőlegesen elnevezhető, és nem jelenti azt, hogy a [dokumentált szerepkörre vonatkozó engedélyeken](permissions-reference.md)kívül más képességeket sem kíván.

Kategória | Szerepkör
---- | ----
Azure AD-specifikus szerepkörök | alkalmazás-rendszergazda<br>Alkalmazásfejlesztő<br>Hitelesítés rendszergazdája<br>B2C IEF kulcskészlet rendszergazdája<br>B2C IEF házirend rendszergazdája<br>Felhőalkalmazás-rendszergazda<br>Felhőalapú eszköz rendszergazdája<br>Feltételes hozzáférésű rendszergazda<br>Eszközrendszergazdák<br>Directory-olvasók<br>Címtár-szinkronizálási fiókok<br>Címtár-írók<br>Külső azonosító – felhasználói folyamat rendszergazdája<br>Külső azonosító felhasználói flow-attribútumának rendszergazdája<br>Külső identitás-szolgáltató rendszergazdája<br>Csoportok rendszergazdája<br>Vendég meghívója<br>Segélyszolgálat rendszergazdája<br>Hibrid identitás-rendszergazda<br>Licenc rendszergazdája<br>Partneri Tier1-támogatás<br>Partneri szint-támogatás<br>Jelszó-rendszergazda<br>Kiemelt jogosultságú hitelesítés rendszergazdája<br>Kiemelt szerepkörű rendszergazda<br>Jelentések olvasója<br>Felhasználói fiók rendszergazdája
Szolgáltatások közötti szerepkörök | Globális rendszergazda<br>Megfelelőségi rendszergazda<br>Megfelelőségi adatkezelő<br>Globális olvasó<br>Biztonsági rendszergazda<br>Biztonsági operátor<br>Biztonsági olvasó<br>Szolgáltatás-támogatási rendszergazda
Szolgáltatás-specifikus szerepkörök | Azure DevOps-rendszergazda<br>Azure Information Protection rendszergazda<br>Számlázási adminisztrátor<br>CRM szolgáltatás rendszergazdája<br>Ügyfél-kulcstároló hozzáférési jóváhagyója<br>Asztali elemzési rendszergazda<br>Exchange szolgáltatás rendszergazdája<br>Az Áttekintés rendszergazdája<br>Üzleti vezető<br>Intune szolgáltatás rendszergazdája<br>Kaizala-rendszergazda<br>Lync szolgáltatás rendszergazdája<br>A Message Center adatvédelmi olvasója<br>Üzenetközpont-olvasó<br>Modern kereskedelmi felhasználó<br>Hálózati rendszergazda<br>Office-alkalmazások rendszergazdája<br>Power BI-szolgáltatásadminisztrátor<br>Energiagazdálkodási platform rendszergazdája<br>Nyomtató rendszergazdája<br>Nyomtató technikusa<br>Keresés a Rendszergazdában<br>Keresési szerkesztő<br>SharePoint szolgáltatás rendszergazdája<br>Csapatok kommunikációs rendszergazdája<br>Csapat kommunikációs támogató mérnök<br>A Teams Communications támogatási szakértője<br>Csapatok eszközeinek rendszergazdája<br>Teams szolgáltatás rendszergazdája

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD szerepköralapú hozzáférés-vezérlésének áttekintése](custom-overview.md)
- Szerepkör-hozzárendelések létrehozása [a Azure Portal, az Azure ad PowerShell és a Graph API](custom-create.md) használatával
- [Szerepkör-hozzárendelések felsorolása](view-assignments.md)
