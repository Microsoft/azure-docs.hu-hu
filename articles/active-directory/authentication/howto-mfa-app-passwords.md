---
title: Az Azure AD Multi-Factor Authentication-Azure Active Directory alkalmazás jelszavának konfigurálása
description: Ismerje meg, hogyan konfigurálhatja és használhatja az alkalmazások jelszavait az Azure AD-ban található örökölt alkalmazásokhoz Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743105"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Azure AD többtényezős hitelesítésének engedélyezése és használata alkalmazásjelszavakat használó régi alkalmazásokkal

Néhány régebbi, nem böngészőbeli alkalmazás, például az Office 2010 vagy a korábbi és az Apple Mail az iOS 11 előtt nem érti a hitelesítési folyamat szüneteltetését vagy megszakítását. Ha egy felhasználó engedélyezve van az Azure AD Multi-Factor Authentication, és megkísérli használni a régebbi, nem böngészőbeli alkalmazások egyikét, nem tudnak sikeresen hitelesíteni. Ha ezeket az alkalmazásokat biztonságos módon szeretné használni az Azure AD Multi-Factor Authentication a felhasználói fiókokhoz, az alkalmazás jelszavait is használhatja. Ezek az alkalmazások jelszavai lecserélték a hagyományos jelszót, hogy az alkalmazások megkerüljék a többtényezős hitelesítést, és megfelelően működjenek.

A modern hitelesítés a Microsoft Office 2013-ügyfelek és újabb rendszerek esetében támogatott. Az Office 2013-ügyfelek, beleértve az Outlookot, támogatják a modern hitelesítési protokollokat, és lehetővé tehetik a kétlépéses ellenőrzéssel való munkát. Az ügyfél engedélyezése után az alkalmazás jelszavai nem szükségesek az ügyfélhez.

Ez a cikk bemutatja, hogyan engedélyezheti és használhatja az alkalmazás jelszavait olyan örökölt alkalmazásokhoz, amelyek nem támogatják a többtényezős hitelesítési kéréseket.

>[!NOTE]
> Az alkalmazás jelszavai nem működnek a feltételes hozzáférésen alapuló multi-Factor Authentication házirendek és a modern hitelesítés használatával.

## <a name="overview-and-considerations"></a>Áttekintés és szempontok

Ha a felhasználói fiók engedélyezve van az Azure AD Multi-Factor Authenticationhoz, a rendszer a rendszeres bejelentkezési kérést egy további ellenőrzés megszakítja. Egyes régebbi alkalmazások nem értik ezt a megszakítást a bejelentkezési folyamat során, így a hitelesítés meghiúsul. A felhasználói fiókok biztonságának fenntartása és az Azure AD Multi-Factor Authentication engedélyezése után az alkalmazás jelszavai a felhasználó normál felhasználóneve és jelszava helyett használhatók. Ha a bejelentkezés során használt alkalmazás jelszava nincs további ellenőrzési kérés, a hitelesítés sikeres lesz.

Az alkalmazás jelszavai automatikusan létrejönnek, és a felhasználó nem adja meg. Ez az automatikusan generált jelszó megnehezíti, hogy egy támadó kitalálja, hogy ez biztonságosabb. A felhasználóknak nem kell nyomon követniük a jelszavakat, vagy minden alkalommal be kell őket írni, amikor az alkalmazás jelszavait csak egyszer kell beírni.

Az alkalmazás jelszavainak használatakor a következő szempontokat kell figyelembe venni:

* Felhasználónként legfeljebb 40 alkalmazás jelszava lehet.
* A jelszavakat gyorsítótárazó és a helyszíni helyzetekben használatos alkalmazások sikertelenek lehetnek, mert az alkalmazás jelszava nem ismert a munkahelyi vagy iskolai fiókon kívül. Példa erre a forgatókönyvre a helyszíni Exchange-e-mailek, de az archivált levelek a felhőben vannak. Ebben az esetben ugyanez a jelszó nem működik.
* Miután az Azure AD Multi-Factor Authentication engedélyezve van egy felhasználói fiókon, az alkalmazás jelszavai a legtöbb nem böngészővel rendelkező ügyfélprogrammal használhatók, mint az Outlook és a Microsoft Skype vállalati verzió. A rendszergazdai műveletek azonban nem hajthatók végre az alkalmazások jelszavainak használatával a nem böngészőalapú alkalmazásokon, például a Windows PowerShellen keresztül. A műveletek akkor sem hajthatók végre, ha a felhasználó rendelkezik rendszergazdai fiókkal.
    * A PowerShell-parancsfájlok futtatásához hozzon létre egy erős jelszót tartalmazó szolgáltatásfiókot, és ne engedélyezze a fiókot kétlépéses ellenőrzéshez.
* Ha azt gyanítja, hogy egy felhasználói fiók sérült, és visszavonja/alaphelyzetbe állítja a fiók jelszavát, akkor az alkalmazás jelszavait is frissíteni kell. Az alkalmazás jelszavai nem vonhatók automatikusan vissza a felhasználói fiók jelszavának visszavonása/alaphelyzetbe állítása során. A felhasználónak törölnie kell a meglévő alkalmazás jelszavát, és újakat kell létrehoznia.
   * További információ: [alkalmazások jelszavainak létrehozása és törlése a további biztonsági ellenőrzés lapról](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

>[!WARNING]
> Az alkalmazások jelszavai nem működnek olyan hibrid környezetekben, ahol az ügyfelek a helyszíni és a Felhőbeli automatikus észlelési végpontokkal kommunikálnak. A helyi hitelesítéshez tartományi jelszavak szükségesek. A felhővel való hitelesítéshez az alkalmazás jelszavai szükségesek.

### <a name="app-password-names"></a>Alkalmazás jelszavának nevei

Az alkalmazás jelszavának nevének tükröznie kell azt az eszközt, amelyen a használatban van. Ha olyan laptopja van, amely nem böngésző alkalmazásokkal, például az Outlook, a Word és az Excel alkalmazással rendelkezik, hozzon létre egy **laptop** nevű alkalmazást az alkalmazások számára. Hozzon létre egy **Desktop** nevű másik alkalmazás-jelszót ugyanahhoz az asztali számítógépen futó alkalmazásokhoz.

Azt javasoljuk, hogy eszközönként hozzon létre egy alkalmazási jelszót, és ne egy alkalmazás jelszavaként.

## <a name="federated-or-single-sign-on-app-passwords"></a>Összevont vagy egyszeri bejelentkezési alkalmazás jelszavai

Az Azure AD támogatja az összevonást vagy az egyszeri bejelentkezést (SSO) helyszíni Active Directory Domain Services (AD DS). Ha a szervezete az Azure AD-vel összevont és Azure AD-Multi-Factor Authentication használ, a következő alkalmazás-jelszóra vonatkozó szempontokat kell figyelembe venni:

>[!NOTE]
> A következő pontok csak az összevont (SSO) ügyfelekre érvényesek.

* Az alkalmazások jelszavait az Azure AD ellenőrzi, ezért az összevonás mellőzése. Az összevonás csak az alkalmazás jelszavának beállításakor használatos.
* Az identitás-szolgáltató (identitásszolgáltató) nem kapcsolódik az összevont (SSO) felhasználókhoz, a passzív folyamattól eltérően. Az alkalmazás jelszavait a munkahelyi vagy iskolai fiók tárolja. Ha a felhasználó elhagyja a vállalatot, a felhasználó adatai valós **időben, a** felhasználók adatait a munkahelyi vagy iskolai fiókba áramlanak. A fiók letiltása/törlése akár három órát is igénybe vehet, ami késleltetheti az alkalmazás jelszavának letiltását/törlését az Azure AD-ben.
* A helyszíni ügyfél Access Control beállításait az alkalmazás jelszavai szolgáltatása nem veszi figyelembe.
* Nem érhető el helyszíni hitelesítési naplózási vagy naplózási képesség az alkalmazás jelszavai szolgáltatásával.

Egyes speciális architektúrák a többtényezős hitelesítéshez szükséges hitelesítő adatok kombinációját igénylik az ügyfelekkel. Ezek a hitelesítő adatok a munkahelyi vagy iskolai fiókhoz tartozó felhasználónevet és jelszót, valamint az alkalmazás jelszavait is tartalmazhatják. A követelmények attól függnek, hogyan történik a hitelesítés. A helyszíni infrastruktúrát, a munkahelyi vagy iskolai fiókhoz tartozó felhasználónevet és jelszót hitelesítő ügyfelek esetében kötelező megadni. Az Azure AD-t hitelesítő ügyfelek esetében szükség van egy alkalmazás jelszavára.

Tegyük fel például, hogy a következő architektúrával rendelkezik:

* Active Directory helyszíni példányát az Azure AD-vel összevontuk.
* Az Exchange Online-t használja.
* A Skype vállalati verzió a helyszínen használható.
* Az Azure AD Multi-Factor Authentication.

Ebben az esetben a következő hitelesítő adatokat használja:

* A Skype vállalati verzióba való bejelentkezéshez használja a munkahelyi vagy iskolai fiókja felhasználónevét és jelszavát.
* Ha a címjegyzéket az Exchange Online-hoz kapcsolódó Outlook-ügyfélről szeretné elérni, használja az alkalmazás jelszavát.

## <a name="allow-users-to-create-app-passwords"></a>Alkalmazások jelszavainak létrehozásának engedélyezése a felhasználók számára

Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazás-jelszavakat. Az alkalmazás jelszava funkciót engedélyezni kell, mielőtt a felhasználók használni tudják őket. Ha lehetővé szeretné tenni a felhasználóknak az alkalmazás jelszavának létrehozását, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg és válassza ki a **Azure Active Directory**, majd a **felhasználók** lehetőséget.
3. A *felhasználók* ablak tetején lévő navigációs sávon válassza a **multi-Factor Authentication** lehetőséget.
4. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai** elemet.
5. A **szolgáltatás beállításai** lapon jelölje be a **felhasználók számára az alkalmazás jelszavának engedélyezése lehetőséget a nem böngésző alkalmazásokba való bejelentkezéshez** .

    ![Képernyőkép a Azure Portalról, amely a többtényezős hitelesítés szolgáltatás beállításait mutatja be az alkalmazás jelszavainak felhasználójának engedélyezéséhez](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> Ha letiltja a felhasználók számára az alkalmazás jelszavának létrehozását, a meglévő alkalmazás jelszavai továbbra is működni tudnak. A felhasználók azonban nem kezelhetik vagy törölhetik a meglévő alkalmazások jelszavát, ha letiltja ezt a lehetőséget.
>
> Ha letiltja az alkalmazás jelszavának létrehozását, azt is javasoljuk, hogy [hozzon létre egy feltételes hozzáférési szabályzatot, amely letiltja a régi hitelesítés használatát](../conditional-access/block-legacy-authentication.md). Ez a megközelítés megakadályozza, hogy a meglévő alkalmazások jelszavai működőképesek legyenek, és kényszerítse a modern hitelesítési módszerek használatát.

## <a name="create-an-app-password"></a>Alkalmazás jelszavának létrehozása

Amikor a felhasználók befejezik az Azure AD-Multi-Factor Authentication kezdeti regisztrációját, a regisztrációs folyamat végén lehetősége van az alkalmazás jelszavának létrehozására.

A felhasználók a regisztráció után is létrehozhatnak alkalmazás-jelszavakat. További információk és részletes lépések a felhasználók számára: Mik az [alkalmazások jelszavai az Azure ad-ben multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan engedélyezhető a felhasználók számára az Azure AD-Multi-Factor Authentication gyors regisztrálása: a [kombinált biztonsági információk regisztrációjának áttekintése](concept-registration-mfa-sspr-combined.md).
