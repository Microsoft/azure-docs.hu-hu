---
title: Helyszíni Azure AD jelszavas védelem engedélyezése
description: Ismerje meg, hogyan engedélyezhető az Azure AD jelszavas védelem helyszíni Active Directory Domain Services-környezethez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741779"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Helyszíni Azure Active Directory jelszavas védelem engedélyezése

A felhasználók gyakran olyan gyakori helyi szavakat (például iskolát, sport csapatot vagy híres személyt) használó jelszavakat hoznak létre. Ezek a jelszavak könnyen kiolvashatók és gyengék a szótáron alapuló támadásokkal szemben. A szervezet erős jelszavainak kényszerítéséhez Azure Active Directory (Azure AD) jelszavas védelme globális és egyéni tiltott jelszavakat tartalmaz. A jelszó-módosítási kérelem meghiúsul, ha egyezés szerepel a tiltott jelszavak listájában.

A helyszíni Active Directory Domain Services (AD DS) környezetének védelme érdekében telepítheti és konfigurálhatja az Azure AD jelszavas védelmet a helyszíni TARTOMÁNYVEZÉRLŐvel való együttműködéshez. Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD jelszavas védelmet a helyszíni környezetben.

Az Azure AD jelszavas védelem helyszíni környezetben való működésével kapcsolatos további információkért lásd: [Az Azure ad jelszavas védelem betartatása a Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD jelszavas védelmet a helyszíni környezetben. A cikk elvégzése előtt [telepítse és regisztrálja az Azure ad jelszavas védelmi proxy szolgáltatást és a DC-ügynököket](howto-password-ban-bad-on-premises-deploy.md) a helyszíni AD DS környezetben.

## <a name="enable-on-premises-password-protection"></a>Helyszíni jelszavas védelem engedélyezése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg **Azure Active Directory**  >  **biztonsági**  >  **hitelesítési módszerek**  >  **jelszavas védelmét**.
1. Állítsa be a **jelszavas védelem engedélyezésének lehetőségét a Windows Server Active Directory** az *Igen* értékre.

    Ha ez a beállítás a *nem* értékre van állítva, az összes telepített Azure ad-beli jelszavas védelmi tartományvezérlő-ügynök nyugalmi módba kerül, ahol az összes jelszó elfogadva van. Nem végeznek érvényesítési tevékenységet, és nem jönnek létre naplózási események.

1. Azt javasoljuk, hogy először állítsa be a **módot** a *naplózásra*. A funkció és a szervezet felhasználóira gyakorolt hatása után a mód *kényszerített* **állapotra** vált. További információkért lásd a következő szakaszt a [működésének módjáról](#modes-of-operation).
1. Ha elkészült, válassza a **Mentés** lehetőséget.

    [![Helyszíni jelszavas védelem engedélyezése a Azure Portal hitelesítési módszereiben](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Működési módok

Ha engedélyezi a helyszíni Azure AD jelszavas védelmet, használhatja a *naplózási* módot vagy a *kényszerített* módot. Azt javasoljuk, hogy a kezdeti üzembe helyezés és a tesztelés mindig vizsgálati módban induljon el. Az Eseménynapló bejegyzéseinek figyelésével meg kell figyelni, hogy a meglévő működési folyamatok megzavarják-e a *kényszerített* mód engedélyezése után.

### <a name="audit-mode"></a>Vizsgálati mód

A *vizsgálati* mód célja, hogy a szoftvert "mi if" módban futtassa. Minden egyes Azure AD Password Protection DC Agent szolgáltatás a jelenleg aktív szabályzatnak megfelelően kiértékel egy bejövő jelszót.

Ha az aktuális házirend naplózási módban van konfigurálva, a "rossz" jelszavak az Eseménynapló-üzeneteket eredményezik, de a rendszer feldolgozza és frissíti azokat. Ez az egyetlen különbség a naplózás és a kikényszerítés mód között. Minden más művelet ugyanazt a műveletet futtatja.

### <a name="enforced-mode"></a>Kényszerített mód

A *kényszerített* üzemmód végső konfigurációként szolgál. A naplózási módban hasonlóan az egyes Azure AD Password Protection DC Agent szolgáltatás a jelenleg aktív szabályzatnak megfelelően kiértékeli a bejövő jelszavakat. Ha a kényszerített mód engedélyezve van, akkor a rendszer elutasítja a szabályzatnak megfelelően biztonságosnak ítélt jelszót.

Ha az Azure AD jelszavas védelmi tartományvezérlő ügynöke kényszerített módban visszautasítja a jelszót, a végfelhasználó hasonló hibát lát el, például megtekintheti, hogy a jelszó elutasítása a hagyományos helyszíni jelszó-összetettségi kényszerítéssel történt-e. Előfordulhat például, hogy egy felhasználó a következő hagyományos hibaüzenetet jeleníti meg a Windows-bejelentkezés vagy a jelszó módosítása képernyőn:

*"Nem sikerült frissíteni a jelszót. Az új jelszóhoz megadott érték nem felel meg a tartomány hosszúsági, összetettségi vagy előzményi követelményeinek. "*

Ez az üzenet csak egy példát mutat be több lehetséges eredményre. Az adott hibaüzenet attól függően változhat, hogy milyen szoftvert vagy forgatókönyvet próbál meg használni egy nem biztonságos jelszó megadására.

Előfordulhat, hogy az érintett végfelhasználóknak az informatikai személyzettel kell dolgozniuk az új követelmények megismeréséhez és a biztonságos jelszavak kiválasztásához.

> [!NOTE]
> Az Azure AD jelszavas védelme nem szabályozza az ügyfélszámítógép által a gyenge jelszavak elutasításakor megjelenő hibaüzenetet.

## <a name="next-steps"></a>Következő lépések

Ha testre szeretné szabni a tiltott jelszavak listáját a szervezet számára, tekintse meg [Az Azure ad jelszavas védelem egyéni tiltott jelszavak listájának konfigurálása](tutorial-configure-custom-password-protection.md)című témakört.

A helyszíni események figyelését lásd: helyszíni [Azure ad jelszavas védelem figyelése](howto-password-ban-bad-on-premises-monitor.md).
