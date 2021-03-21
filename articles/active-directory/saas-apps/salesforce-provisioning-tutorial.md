---
title: 'Oktatóanyag: a Salesforce konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg a Salesforce és az Azure AD végrehajtásához szükséges lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Salesforce-be.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 2a4d3f3f9465b8813cdf6ee26760d819d73a08c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94353104"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Salesforce konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a Salesforce és az Azure AD végrehajtásához szükséges lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Salesforce.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure Active Directory-bérlő
* Salesforce.com-bérlő

> [!IMPORTANT]
> Ha Salesforce.com próbaverziós fiókot használ, akkor nem fogja tudni beállítani az automatikus felhasználó-kiépítés beállítását. A próbaverziós fiókokhoz nincs engedélyezve a szükséges API-hozzáférés, amíg meg nem vásárolják őket. Ezt a korlátozást egy ingyenes [fejlesztői fiók](https://developer.salesforce.com/signup) használatával érheti el az oktatóanyag elvégzéséhez.

Ha Salesforce sandbox-környezetet használ, tekintse meg a [Salesforce homokozó integrációs oktatóanyagát](./salesforce-sandbox-tutorial.md).

## <a name="assigning-users-to-salesforce"></a>Felhasználók kiosztása a Salesforce

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra vagy csoportokra van szükség az Azure AD-ben a Salesforce alkalmazás eléréséhez. A döntés elvégzése után ezeket a felhasználókat hozzárendelheti a Salesforce-alkalmazáshoz a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) című részben leírtak szerint.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Fontos Tippek a felhasználók Salesforce való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Salesforce a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha a felhasználó Salesforce rendel hozzá, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során

    > [!NOTE]
    > Ez az alkalmazás a Salesforce származó profilokat importálja a létesítési folyamat részeként, amelyet az ügyfél kiválaszthat a felhasználók Azure AD-ben való hozzárendeléséhez. Vegye figyelembe, hogy a Salesforce importált profilok szerepkörökként jelennek meg az Azure AD-ben.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a [Salesforce felhasználói fiók létesítési API-V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure ad-ben a felhasználók és csoportok hozzárendelése alapján a Salesforce-ben a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!Tip]
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a Salesforce számára, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus üzembe helyezésének konfigurálása

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok Salesforce való kiépítés.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

2. Ha már konfigurálta a Salesforce az egyszeri bejelentkezéshez, keresse meg a Salesforce-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **Salesforce** az alkalmazás-gyűjteményben. Válassza a Salesforce lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a Salesforce példányát, majd válassza a **kiépítés** lapot.

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőfelvétel: az Salesforce-létesítési oldal, a kiépítési mód pedig az automatikus és más beállítható értékekre van beállítva.](./media/salesforce-provisioning-tutorial/provisioning.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a következő konfigurációs beállításokat:

    a. A **rendszergazda felhasználóneve** szövegmezőbe írja be a Salesforce-fiók nevét, amely a Salesforce.com hozzárendelt **rendszergazda** profillal rendelkezik.

    b. A **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.

6. A Salesforce biztonsági jogkivonat beszerzéséhez nyisson meg egy új lapot, és jelentkezzen be ugyanahhoz a Salesforce-rendszergazdai fiókba. A lap jobb felső sarkában kattintson a nevére, majd a **Beállítások** elemre.

    ![Képernyőfelvétel: a Beállítások hivatkozás kiválasztva.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Automatikus felhasználó-kiépítés engedélyezése")

7. A bal oldali navigációs panelen kattintson a **személyes adatok** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **biztonsági jogkivonat alaphelyzetbe állítása** elemre.
  
    ![Képernyőfelvétel: a személyes adatokból kiválasztott biztonsági jogkivonat alaphelyzetbe állítása.](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Automatikus felhasználó-kiépítés engedélyezése")

8. A **biztonsági jogkivonat alaphelyzetbe állítása** lapon kattintson a **biztonsági jogkivonat alaphelyzetbe állítása** gombra.

    ![Képernyőfelvétel a REST biztonsági jogkivonat oldalon, a magyarázó szöveggel és a biztonsági jogkivonat alaphelyzetbe állítására szolgáló lehetőséggel](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Automatikus felhasználó-kiépítés engedélyezése")

9. Keresse meg a rendszergazdai fiókhoz társított e-mailek mappáját. Keressen egy e-mailt az új biztonsági jogkivonatot tartalmazó Salesforce.com.

10. Másolja ki a tokent, nyissa meg az Azure AD-ablakot, és illessze be a **titkos jogkivonat** mezőbe.

11. A **bérlői URL-címet** meg kell adni, ha a Salesforce példánya a Salesforce Government felhőben van. Ellenkező esetben nem kötelező. Adja meg a bérlői URL-címet a "https:// \<your-instance\> . my.Salesforce.com" formátum használatával, \<your-instance\> és cserélje le az Salesforce-példány nevét.

12. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Salesforce-alkalmazáshoz.

13. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az alábbi jelölőnégyzetet.

14. Kattintson a **Mentés gombra.**  

15. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Salesforce lehetőséget.**

16. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az Salesforce-be szinkronizált felhasználói attribútumokat. Vegye figyelembe, hogy az **egyező** tulajdonságokként kiválasztott attribútumok a Salesforce felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

17. Az Azure AD-kiépítési szolgáltatás Salesforce való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a beállítások **szakaszban:**

18. Kattintson a **Mentés gombra.**

> [!NOTE]
> A felhasználók a Salesforce alkalmazásban való kiépítés után a rendszergazdának konfigurálnia kell a nyelvspecifikus beállításait. A nyelvi konfigurációval kapcsolatos további információkért tekintse meg [ezt](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) a cikket.

Ezzel elindítja a felhasználók és csoportok szakaszban Salesforce rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a Salesforce alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="common-issues"></a>Gyakori problémák
* Ha olyan problémák merülnek fel, amelyek engedélyezik a Salesforce való hozzáférést, ügyeljen a következőkre:
    * A használt hitelesítő adatoknak rendszergazdai hozzáférése van a Salesforce.
    * Az Ön által használt Salesforce verziója támogatja a webes hozzáférést (például fejlesztői, vállalati, homokozó és korlátlan számú Salesforce).
    * A webes API-hozzáférés engedélyezve van a felhasználó számára.
* Az Azure AD-kiépítési szolgáltatás támogatja a felhasználó számára a kiépítési nyelvet, a területi beállításokat és az időzónát. Ezek az attribútumok az alapértelmezett attribútum-hozzárendelésekben találhatók, de nem rendelkeznek alapértelmezett forrásoldali attribútummal. Győződjön meg arról, hogy az alapértelmezett forrásoldali attribútumot választotta, és hogy a forrás attribútum a SalesForce által várt formátumban van. Például az localeSidKey for English (Egyesült Államok) en_US. Tekintse át az [itt](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) megadott útmutatót a megfelelő localeSidKey-formátum meghatározásához. A languageLocaleKey formátumait [itt](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)találja. A formátum helyességének biztosítása mellett előfordulhat, hogy az [itt](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)leírtak szerint a nyelvnek engedélyezve kell lennie a felhasználók számára. 
* **SalesforceLicenseLimitExceeded:** A felhasználó nem hozható létre a célalkalmazás alkalmazásban, mert nincsenek elérhető licencek ehhez a felhasználóhoz. További licenceket is megadhat a célalkalmazás számára, vagy áttekintheti a felhasználói hozzárendelések és attribútumok leképezésének konfigurációját, hogy a megfelelő felhasználók hozzá legyenek rendelve a megfelelő attribútumokhoz.
* **SalesforceDuplicateUserName:** A felhasználót nem lehet kiépíteni, mert egy másik Salesforce.com-bérlőben duplikált "username" Salesforce.com rendelkezik.A Salesforce.com-ben a "username" attribútum értékének egyedinek kell lennie az összes Salesforce.com-bérlőn.Alapértelmezés szerint a felhasználó userPrincipalName Azure Active Directory a "username" lesz a Salesforce.com.Itt két lehetősége van.Az egyik lehetőség, hogy megkeresse és átnevezi a felhasználót a "username" névvel a másik Salesforce.com-bérlőben, ha a másik bérlőt is felügyeli.A másik lehetőség az Azure Active Directory felhasználó hozzáférésének eltávolítása arra a Salesforce.com-bérlőre, amelyhez a címtár integrálva van. A következő szinkronizálási kísérlet során újra próbálkozunk a művelettel. 
* **SalesforceRequiredFieldMissing:** A Salesforce használatához bizonyos attribútumoknak jelen kell lennie a felhasználó számára a felhasználó sikeres létrehozásához vagy frissítéséhez. Ez a felhasználó nem rendelkezik a szükséges attribútumok egyikével. Győződjön meg arról, hogy az attribútumok, például az e-mailek és az aliasok fel vannak töltve az összes olyan felhasználóra, akit a Salesforce kíván kiépíteni. Azokat a felhasználókat, akik nem rendelkeznek ezekkel az attribútumokkal, [attribútum-alapú hatóköri szűrőket](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)használnak fel. 
* A Salesforce való kiépítés alapértelmezett attribútumának leképezése magában foglalja az SingleAppRoleAssignments kifejezést, amely az Azure AD-ben a Salesforce-ben történő appRoleAssignments leképezésére használható. Győződjön meg arról, hogy a felhasználók nem rendelkeznek több alkalmazás-szerepkör-hozzárendeléssel az Azure AD-ben, mivel az attribútumok leképezése csak az egyik szerepkör kiosztását támogatja. 
* A Salesforce megköveteli, hogy az e-mail-frissítéseket manuálisan kell jóváhagyni a módosítás előtt. Ennek eredményeképpen előfordulhat, hogy a kiépítési naplók több bejegyzést is láthatnak a felhasználó e-mail-címének frissítéséhez (az e-mail módosításának jóváhagyása után).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](./salesforce-tutorial.md)