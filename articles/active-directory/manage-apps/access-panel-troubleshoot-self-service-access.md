---
title: Önkiszolgáló alkalmazás-hozzáférés probléma |} A Microsoft Docs
description: Az önkiszolgáló alkalmazás-hozzáférés kapcsolatos problémák elhárítása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784278"
---
# <a name="problem-using-self-service-application-access"></a>A probléma önkiszolgáló alkalmazás-hozzáférés

Az önkiszolgáló alkalmazás-hozzáférés kiválóan alkalmas, hogy a felhasználók saját felderíteni az alkalmazások, igény szerint jóváhagyása ezeknek az alkalmazásoknak hozzáférést az üzleti csoport lehetővé teszik. Engedélyezheti, hogy az üzleti csoportok a hozzáférési paneljükön jelszó egyszeri bejelentkezést az alkalmazások jobb a felhasználókhoz rendelt hitelesítő adatok kezelésére.

Mielőtt a felhasználók saját felderítheti alkalmazásokat a hozzáférési paneljükön, engedélyeznie kell a **önkiszolgáló alkalmazás-hozzáférés** bármely olyan alkalmazások, amelyek szeretné engedélyezni a felhasználók számára helyi felderítését, és kérjen hozzáférést.

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák

-   Győződjön meg arról, hogy az önkiszolgáló alkalmazás-hozzáférés megfelelően van konfigurálva. Tekintse meg az "önkiszolgáló alkalmazás-hozzáférés konfigurálása".

-   Ellenőrizze, hogy a felhasználó vagy csoport engedélyezve van az önkiszolgáló alkalmazás-hozzáférés kéréséhez.

-   Győződjön meg arról, hogy a felhasználó a megfelelő hely az önkiszolgáló alkalmazás-hozzáférési felkeresett. felhasználók kaphatnak a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) , és kattintson a **+ Hozzáadás** gombra kattintva keresse meg az alkalmazások, amelyhez engedélyezte az önkiszolgáló hozzáférés.

-   Ha nemrégiben konfigurálták az önkiszolgáló alkalmazás-hozzáférést, próbáljon meg bejelentkezni be és ki újra a felhasználó hozzáférési Panel be néhány perc múlva megtekintheti, ha az önkiszolgáló hozzáférés módosításainak jelentek.

## <a name="how-to-configure-self-service-application-access"></a>Az önkiszolgáló alkalmazás-hozzáférés konfigurálása

Ahhoz, hogy az önkiszolgáló alkalmazás-hozzáférést egy alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a [ **az Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüből.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki az önkiszolgáló engedélyezni kívánt alkalmazást a listából való hozzáférést.

7. Ha az alkalmazás betöltött, kattintson a **önkiszolgáló** az alkalmazás bal oldali navigációs menüjében.

8. Ehhez az alkalmazáshoz önkiszolgáló alkalmazás-hozzáférés engedélyezéséhez kapcsolja be a **engedélyezése a felhasználók számára az alkalmazáshoz való hozzáférés kérése?** kapcsolót **igen.**

9. Ezután, amelyekhez a felhasználók, akik kérése az alkalmazáshoz való hozzáférést kell adni a csoport kijelöléséhez kattintson a választó a felirat melletti **melyik csoporthoz lesz hozzáadva a hozzárendelt felhasználók?** , és válasszon ki egy csoportot.

10. **Nem kötelező:** Ha szeretne egy üzleti jóváhagyás szükséges felhasználók férhessenek hozzá, állítsa be a **az alkalmazáshoz való hozzáférés engedélyezéséhez jóváhagyás szükséges?** kapcsolót **Igen**.

11. **Nem kötelező: Csak az a jelszavas egyszeri bejelentkezést használó alkalmazások számára** szeretné ezeket üzleti jóváhagyók kapnak az alkalmazáshoz jóváhagyott felhasználók jelszavainak, ha a **jóváhagyók beállíthatják a felhasználói jelszavakat ehhez a alkalmazás?**  kapcsolót **Igen**.

12. **Nem kötelező:** A munkahelyi jóváhagyónak, akik jogosultak a hagyja jóvá az alkalmazáshoz való hozzáférés megadásához kattintson a címke melletti a választó **kik hagyhatják jóvá az alkalmazáshoz való hozzáférést?** legfeljebb 10 egyes munkahelyi jóváhagyónak kiválasztásához.

    >[!NOTE]
    > Csoportok nem támogatottak.
    >
    >

13. **Nem kötelező:** **Alkalmazások, amelyeknél a szerepkörök**, ha szeretné az önkiszolgáló felhasználók hozzárendelése egy szerepkörhöz, kattintson a Tovább gombra a választó a **melyik szerepkörhöz legyenek hozzárendelve a felhasználók ebben az alkalmazásban?** , válassza ki a szerepkört, amelyhez Ezeknek a felhasználóknak hozzá kell rendelni.

14. Kattintson a **mentése** gombra a Befejezés gombra a panel tetején.

Miután elvégezte az önkiszolgáló alkalmazás-konfigurációs, felhasználók kaphatnak a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) , és kattintson a **+ Hozzáadás** , amelyhez engedélyezte az önkiszolgáló alkalmazások gomb a hozzáférés. Munkahelyi jóváhagyónak is megjelenik egy értesítés a saját [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/). Engedélyezheti egy e-mail értesíti őket, amikor a felhasználó által kért a jóváhagyást igénylő alkalmazásokhoz való hozzáférés. 

Ezek a jóváhagyások támogatja egyetlen jóváhagyási munkafolyamatokat csak, ami azt jelenti, hogy több jóváhagyóval ad meg, ha egyetlen jóváhagyók bármelyike jóváhagyhat a hozzáférni az alkalmazáshoz.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha a probléma megoldásához nem ezeket a hibaelhárítási lépéseket 

Nyisson meg egy támogatási jegyet a következő adatokat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail-címe)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeret során hiba történik.

-   Fiddler-nyomkövetés

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../users-groups-roles/groups-self-service-management.md)
