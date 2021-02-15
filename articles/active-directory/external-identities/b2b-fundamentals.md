---
title: Azure Active Directory B2B – ajánlott eljárások és javaslatok
description: Ismerkedjen meg az ajánlott eljárásokat és javaslatokat a vállalatok közötti (B2B) vendég felhasználói hozzáférés Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94fd488ceb7ddb3724dd576c97c9070481e95147
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365633"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B – ajánlott eljárások
Ez a cikk ajánlásokat és ajánlott eljárásokat tartalmaz a vállalatok közötti (B2B) együttműködéshez Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Október 2021-től kezdve** a Microsoft nem támogatja a meghívások beváltását a nem felügyelt ("vírus" vagy "igény szerinti") Azure ad-fiókok és a bérlők számára a B2B együttműködési forgatókönyvek létrehozásához. Ekkor az egyszer használatos egyszeri jelszó funkció bekapcsol minden meglévő bérlőre, és alapértelmezés szerint engedélyezve lesz az új bérlők számára. Engedélyezjük az e-mailek egyszeri jelszavas szolgáltatását, mivel zökkenőmentes tartalék hitelesítési módszert biztosít a vendég felhasználói számára. Azonban lehetősége van letiltani ezt a funkciót, ha úgy dönt, hogy nem használja. Részletekért lásd az [e-mailek egyszeri jelszavas hitelesítését](one-time-passcode.md) ismertető témakört.


## <a name="b2b-recommendations"></a>B2B-javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Az összevonása való optimális bejelentkezési élmény érdekében | Amikor csak lehetséges, a összevonása közvetlenül az Identity Providers használatával engedélyezheti a meghívott felhasználóknak, hogy Microsoft-fiókok (MSAs) vagy Azure AD-fiókok létrehozása nélkül jelentkezzenek be a megosztott alkalmazásokba és az erőforrásokra. A [Google összevonási szolgáltatással](google-federation.md) engedélyezheti a B2B vendég felhasználói számára a Google-fiókkal való bejelentkezést. Vagy használhatja a [közvetlen összevonás (előzetes verzió) szolgáltatást](direct-federation.md) úgy, hogy közvetlen összevonást állítson be bármely olyan szervezettel, amelynek az identitás-szolgáltatója (identitásszolgáltató) támogatja az SAML 2,0 vagy WS-Fed protokollt. |
| Az e-mailes egyszeri jelszó funkció használata olyan B2B-vendégekhez, akik nem tudnak más módon hitelesíteni magukat | Az [egyszeri e-mail-jelszó](one-time-passcode.md) funkció hitelesíti a B2B vendég felhasználóit, ha nem hitelesíthetők más módon, például az Azure ad-vel, a Microsoft-fiók (MSA) vagy a Google Federation szolgáltatással. Ha a vendég felhasználó bevált egy meghívót, vagy egy megosztott erőforráshoz fér hozzá, ideiglenes kódot kérhet, amelyet a rendszer elküld az e-mail-címére. Ezután a kód beírásával folytathatja a bejelentkezést. |
| Vállalati arculat megjelenítése a bejelentkezési oldalon | Testreszabhatja a bejelentkezési oldalát, így az jobban használható a B2B vendég felhasználói számára. Lásd: [vállalati arculat hozzáadása a bejelentkezéshez és a hozzáférési panel oldalaihoz](../fundamentals/customize-branding.md). |
| Adatvédelmi nyilatkozat hozzáadása a B2B vendég felhasználói beváltási felületéhez | Felveheti a szervezete adatvédelmi nyilatkozatának URL-címét az első alkalommal meghívó beváltási folyamatba, hogy a meghívott felhasználónak el kell fogadnia az adatvédelmi feltételeit a folytatáshoz. További információ [: a szervezet adatvédelmi adatainak hozzáadása Azure Active Directoryban](../fundamentals/active-directory-properties-area.md). |
| A tömeges meghívó (előzetes verzió) szolgáltatással egyszerre több B2B vendég-felhasználót hívhat meg | Egyszerre több vendég felhasználót is meghívhat a szervezete számára a Azure Portalban található tömeges Meghívási Előnézet funkció használatával. Ez a funkció lehetővé teszi egy CSV-fájl feltöltését a B2B vendég felhasználók létrehozására és a meghívások tömeges küldésére. Lásd: [oktatóanyag a B2B-felhasználók tömeges meghívásához](tutorial-bulk-invite.md). |
| Multi-Factor Authenticationra vonatkozó feltételes hozzáférési szabályzatok betartatása (MFA) | Javasoljuk, hogy az MFA-szabályzatokat a partner B2B-felhasználókkal megosztani kívánt alkalmazásokra érvényesítse. Így az MFA-t következetesen érvényesítjük a bérlő alkalmazásaiban, függetlenül attól, hogy a partnerszervezet használ-e MFA-t. Tekintse [meg a vállalatközi csoportmunka-felhasználók feltételes hozzáférését](conditional-access.md)ismertető témakört. |
| Ha az eszközökön alapuló feltételes hozzáférési szabályzatokat kényszeríti, a kizárási listával engedélyezheti a hozzáférést a B2B-felhasználók számára | Ha az eszközön alapuló feltételes hozzáférési szabályzatok engedélyezve vannak a munkahelyen, a B2B vendég felhasználói eszközei le lesznek tiltva, mert nem a szervezet felügyeli. Létrehozhat olyan kizárási listát, amely adott partner felhasználókat tartalmaz, hogy kizárják őket az eszközön alapuló feltételes hozzáférési szabályzatból. Tekintse [meg a vállalatközi csoportmunka-felhasználók feltételes hozzáférését](conditional-access.md)ismertető témakört. |
| Bérlő-specifikus URL-cím használata, ha közvetlen hivatkozásokat biztosít a B2B vendég felhasználóinak | A meghívót tartalmazó e-mailek alternatívájaként az alkalmazásra vagy a portálra mutató közvetlen hivatkozást is megadhat. A közvetlen hivatkozásnak bérlőre jellemzőnek kell lennie, vagyis tartalmaznia kell egy bérlői azonosítót vagy egy ellenőrzött tartományt, hogy a vendég hitelesíthető legyen a bérlőben, ahol a megosztott alkalmazás található. Tekintse [meg a vendég felhasználó beváltási élményét](redemption-experience.md). |
| Alkalmazás fejlesztésekor a UserType használatával határozza meg a vendég felhasználói élményt  | Ha alkalmazást fejleszt, és különböző élményeket szeretne biztosítani a bérlői felhasználók és a vendég felhasználók számára, használja a UserType tulajdonságot. A UserType jogcím jelenleg nem szerepel a jogkivonatban. Az alkalmazásoknak a Microsoft Graph API-t kell használniuk ahhoz, hogy lekérdezzek a felhasználó címtárát, hogy megkapják a UserType. |
| A UserType tulajdonság *csak* akkor módosítható, ha a felhasználó kapcsolata a szervezet változásával | Habár lehetséges, hogy a PowerShell használatával a tag UserType tulajdonságát a vendégnek (és fordítva) is át lehet alakítani, ezt a tulajdonságot csak akkor kell módosítania, ha a felhasználó a szervezethez való viszonya megváltozik. Tekintse meg [a B2B vendég felhasználó tulajdonságait](user-properties.md).|

## <a name="next-steps"></a>Következő lépések

[B2B-megosztás kezelése](delegate-invitations.md)