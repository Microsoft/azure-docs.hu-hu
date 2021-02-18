---
title: Együttműködés másokkal – LUIS
titleSuffix: Azure Cognitive Services
description: Az alkalmazás tulajdonosa hozzáadhat közreműködőket a szerzői erőforráshoz. Ezek a közreműködők módosíthatják a modellt, betanítják és közzétehetik az alkalmazást.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 5ca13784fe2f9a6a5b448bc838bf508f01b0a9fe
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095192"
---
# <a name="add-contributors-to-your-app"></a>Közreműködők hozzáadása az alkalmazáshoz

Az alkalmazások tulajdonosai hozzáadhatnak közreműködőket az alkalmazásokhoz. Ezek a közreműködők módosíthatják a modellt, betanítják és közzétehetik az alkalmazást. Miután [áttelepítette](luis-migration-authoring.md) a fiókját, a _közreműködők_ a Azure Portalban kezelhetők a szerzői erőforráshoz a **hozzáférés-vezérlés (iam)** lapon. Adjon hozzá egy felhasználót a közreműködő e-mail-címével és a _közreműködő_ szerepkörrel.

## <a name="add-contributor-to-azure-authoring-resource"></a>Közreműködő hozzáadása az Azure authoring resourcehez

Áttelepítette, ha a LUIS authoring Experience egy authoring-erőforráshoz van kötve a LUIS **-portál Manage-> Azure-erőforrások** lapján.

1. A Azure Portal keresse meg a Language Understanding (LUIS) authoring erőforrást. A típusa a következő: `LUIS.Authoring` .
1. Az erőforrás **Access Control (iam)** lapon válassza a **+ Hozzáadás** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása** elemet.

    ![A Azure Portalban vegyen fel szerepkör-hozzárendelést az erőforrás-létrehozáshoz.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a közreműködő **szerepkört** . A **hozzáférés kiosztása** beállításnál válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** lehetőséget. A **Select (kiválasztás** ) lehetőségnél adja meg a felhasználó e-mail-címét. Ha a felhasználó neve több mint 1 e-mail-cím, akkor győződjön meg arról, hogy az _elsődleges_ e-mail-fiók.

    ![Felhasználó e-mail-címének hozzáadása az Azure AD közreműködői szerepköréhez](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Ha a felhasználó e-mail-címe megtalálható, válassza ki a fiókot, és válassza a **Mentés** lehetőséget.

    Ha problémája van a szerepkör-hozzárendeléssel, tekintse át az [Azure-szerepkörök hozzárendelése](../../role-based-access-control/role-assignments-portal.md) és az [Azure hozzáférés-vezérlés hibaelhárítása című témakört](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="view-the-app-as-a-contributor"></a>Az alkalmazás megtekintése közreműködőként

Miután közreműködőként bővült, [Jelentkezzen be a Luis portálra](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Több e-mailekkel rendelkező felhasználók

Ha a LUIS-alkalmazáshoz közreműködőket ad hozzá, pontosan megadja az e-mail-címet. Míg Azure Active Directory (Azure AD) lehetővé teszi, hogy egyetlen felhasználó több e-mail fiókkal is felhasználható, a LUIS megköveteli a felhasználótól, hogy jelentkezzen be a közreműködő hozzáadásakor megadott e-mail-címmel.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Erőforrások Azure Active Directory

Ha [Azure Active Directoryt](../../active-directory/index.yml) (Azure ad) használ a szervezetben, Language UNDERSTANDING (Luis) engedélyre van szüksége a felhasználók hozzáférésére vonatkozó információkhoz, amikor a Luis-t szeretnék használni. A LUIS által igényelt erőforrások minimálisak.

A részletes leírást akkor tekintheti meg, ha olyan fiókkal próbál regisztrálni, amely rendszergazdai jogosultságokkal rendelkezik, vagy nem igényel rendszergazdai jogosultságot, például a rendszergazdai beleegyezett:

* Lehetővé teszi, hogy bejelentkezzen az alkalmazásba a szervezeti fiókjával, és hagyja, hogy az alkalmazás beolvassa a profilját. Azt is lehetővé teszi, hogy az alkalmazás beolvassa az alapszintű vállalati adatokat. Ez LUIS engedélyt ad az alapszintű profilokra vonatkozó adatok olvasására, például felhasználói azonosító, e-mail, név
* Lehetővé teszi az alkalmazás számára, hogy megtekintse és frissítse az adatait, még akkor is, ha jelenleg nem használja az alkalmazást. A felhasználó hozzáférési jogkivonatának frissítéséhez engedély szükséges.


### <a name="azure-active-directory-tenant-user"></a>Bérlői felhasználó Azure Active Directory

A LUIS standard Azure Active Directory (Azure AD) engedélyezési folyamatot használ.

A bérlői rendszergazdának közvetlenül kell működnie azzal a felhasználóval, akinek hozzáférést kell biztosítania a LUIS használatához az Azure AD-ben.

* Először a felhasználó bejelentkezik a LUIS-be, és a rendszergazdai jóváhagyást igénylő előugró párbeszédpanelt látja. A folytatás előtt a felhasználó kapcsolatba lép a bérlői rendszergazdával.
* Másodszor, a bérlői rendszergazda bejelentkezik a LUIS-ba, és megtekint egy beleegyező folyamat előugró párbeszédpanelt. Ez az a párbeszédpanel, amely számára a rendszergazdának engedélyt kell adnia a felhasználó számára. Ha a rendszergazda elfogadja az engedélyt, a felhasználó továbbra is elvégezheti a LUIS-t. Ha a bérlői rendszergazda nem tud bejelentkezni a LUIS-be, a [rendszergazda hozzáférhet a](https://account.activedirectory.windowsazure.com/r#/applications) luishoz. Ezen az oldalon szűrheti a listát a nevet tartalmazó elemekre `LUIS` .

Ha a bérlői rendszergazda csak bizonyos felhasználók számára szeretne LUIS-t használni, néhány lehetséges megoldás létezik:
* Adja meg a "rendszergazdai beleegyezett" (beleegyezett az Azure AD összes felhasználójára), de az "igen" értékre állítsa a "felhasználói hozzárendelés szükséges" értéket a vállalati alkalmazás tulajdonságai területen, végül pedig csak a kívánt felhasználókat rendeli hozzá az alkalmazáshoz. Ezzel a módszerrel a rendszergazda továbbra is "rendszergazdai beleegyezett" az alkalmazáshoz, de megadhatja azokat a felhasználókat, akik hozzáférhetnek hozzá.
* Egy második megoldás, amely az [Azure ad Identity and Access Management API-t használja Microsoft Graphban](/graph/azuread-identity-access-management-concept-overview) az egyes felhasználók beleegyezett.

További információ az Azure Active Directory-felhasználókról és-engedélyekről:
* [Alkalmazás korlátozása](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) felhasználói csoportra

## <a name="next-steps"></a>Következő lépések

* Megtudhatja [, hogyan használhatja a verzióit](luis-how-to-manage-versions.md) az alkalmazás életciklusának szabályozására.
* Ismerje meg az adott erőforrással kapcsolatos fogalmakat, például a [szerzői erőforrást](luis-how-to-azure-subscription.md#authoring-key) és a [közreműködőket](luis-how-to-azure-subscription.md#contributions-from-other-authors) .
* Útmutató szerzői és futtatókörnyezeti erőforrások [létrehozásához](luis-how-to-azure-subscription.md)
* Migrálás az új [szerzői erőforrásba](luis-migration-authoring.md)