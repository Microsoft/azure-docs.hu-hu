---
title: Hogyan lehet letiltani a gyenge jelszavakat az Azure AD - Azure Active Directory
description: Az Azure ad-ben dinamikusan letiltott passwrords a envirionment a gyenge jelszavakat letiltása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7f6dbc869db4a0a444d09a2dc234e171758c706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414859"
---
# <a name="configuring-the-custom-banned-password-list"></a>A letiltott jelszavak egyéni lista konfigurálása

Számos szervezet található, a felhasználók létre közös helyi szavakat, például egy iskolai, focicsapata vagy híres személy, így könnyen kitalálható jelszavakat. A Microsoft egyéni letiltott jelszavak lista lehetővé teszi a szervezetek számára, hogy adja hozzá a karakterláncok kiértékelése és letiltására, mellett a globális le van tiltva jelszólista, ha a felhasználók és rendszergazdák próbál módosítani vagy a jelszó alaphelyzetbe állítása.

## <a name="add-to-the-custom-list"></a>Az egyéni lista hozzáadása

A letiltott jelszavak egyéni lista konfigurálása egy Azure Active Directory Premium P1 vagy P2-licenc szükséges. Részletes információkat az Azure Active Directory licenceléssel kapcsolatos, tekintse meg a [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/). |}

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory**, **hitelesítési módszerek**, majd **jelszavas védelem**.
1. A beállítás **érvényesítése egyéni lista**, az **Igen**.
1. Adja hozzá a karakterláncok a **egyéni le van tiltva a jelszó lista**, soronként egy karakterlánc
   * A letiltott jelszavak egyéni lista legfeljebb 1000 szavakat is tartalmazhat.
   * A letiltott jelszavak egyéni lista egy kis-és nagybetűket.
   * A letiltott jelszavak egyéni lista közös karaktert helyettesítő figyelembe veszi.
      * Példa: "o" és "0" vagy "a" és "\@"
   * A karakterlánc minimális hosszának 4 karakter, a maximális 16 karakternél.
1. Minden karakterláncok hozzáadásakor kattintson **mentése**.

> [!NOTE]
> A alkalmazni egyéni letiltott jelszavak lista frissítések több óráig is tarthat.

![Az Azure Portalon a hitelesítési módszerek egyéni letiltott jelszavak listájának módosítása](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Működés

Minden alkalommal, amikor egy felhasználó vagy rendszergazda visszaállítja, vagy egy Azure AD-jelszó megváltozik, végig a letiltott jelszavak listáit, győződjön meg arról, hogy nem szerepel a listában. Ez az ellenőrzés beállítása és módosítása az Azure AD-vel jelszavakat tartalmazza.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál úgy, hogy az lenne le van tiltva a jelszó alaphelyzetbe állítása, megjelenik a következő hibaüzenetet kapja:

Sajnos a jelszó tartalmaz egy szót, kifejezést vagy mintát, amely lehetővé teszi a jelszó könnyen kitalálható. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>További lépések

[A letiltott jelszavak listáit fogalmi áttekintése](concept-password-ban-bad.md)

[Az Azure AD jelszóvédelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)

[A letiltott jelszavak listáit a helyszíni integráció engedélyezése](howto-password-ban-bad-on-premises.md)
