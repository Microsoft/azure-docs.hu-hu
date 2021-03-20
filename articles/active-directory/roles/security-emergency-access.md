---
title: A vészhelyzeti hozzáférési rendszergazdai fiókok kezelése – Azure AD
description: Ez a cikk azt ismerteti, hogyan lehet vészhelyzeti hozzáférési fiókok használatával megakadályozni, hogy véletlenül kizárja a Azure Active Directory (Azure AD) szervezetét.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6a1e4b3b44004ec6d03c293bbd10617b3d3af69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740822"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben

Fontos, hogy megakadályozza, hogy véletlenül kizárja a Azure Active Directory (Azure AD) szervezetét, mert nem tud bejelentkezni vagy aktiválni egy másik felhasználói fiókot rendszergazdaként. A szervezete két vagy több *vészhelyzeti hozzáférésű fiókjának* létrehozásával csökkentheti a rendszergazdai hozzáférés véletlen hiányának következményeit.

A sürgősségi hozzáférési fiókok magas jogosultsággal rendelkeznek, és nem adott személyekhez vannak rendelve. A vészhelyzeti hozzáférési fiókok csak az olyan vészhelyzeti esetekre valók, amikor a normál rendszergazdai fiókok nem használhatók. Javasoljuk, hogy a vészhelyzeti fiókokra vonatkozó korlátozásokat csak akkor használja, ha feltétlenül szükséges.

Ez a cikk a vészhelyzeti hozzáférési fiókok Azure AD-ben való kezeléséhez nyújt útmutatást.

## <a name="why-use-an-emergency-access-account"></a>Miért érdemes vészhelyzeti hozzáférési fiókot használni?

Előfordulhat, hogy egy szervezetnek a következő helyzetekben vészhelyzeti hozzáférési fiókot kell használnia:

- A felhasználói fiókok összevonása folyamatban van, és az összevonás jelenleg nem érhető el a cella-hálózati szünet vagy az identitás-szolgáltató kimaradása miatt. Ha például az identitás-szolgáltató gazdagépe leállt a környezetben, előfordulhat, hogy a felhasználók nem tudnak bejelentkezni, amikor az Azure AD átirányítja az identitás-szolgáltatóhoz.
- A rendszergazdák regisztrálva vannak az Azure AD Multi-Factor Authenticationon keresztül, és az összes önálló eszköze nem érhető el, vagy a szolgáltatás nem érhető el. Előfordulhat, hogy a felhasználók nem tudják befejezni Multi-Factor Authentication egy szerepkör aktiválásához. A cella hálózati meghibásodása például megakadályozza, hogy a telefonhívásokat vagy szöveges üzeneteket fogadjon, az eszközre regisztrált két hitelesítési mechanizmust.
- A legutóbbi globális rendszergazdai hozzáféréssel rendelkező személy elhagyta a szervezetet. Az Azure AD megakadályozza az utolsó globális rendszergazdai fiók törlését, de nem akadályozza meg a fiók törlését vagy letiltását a helyszínen. Előfordulhat, hogy a szervezet nem tudja helyreállítani a fiókot.
- Előre nem látható körülmények, például a természeti katasztrófák vészhelyzete, amely során a mobiltelefon vagy más hálózat nem érhető el. 

## <a name="create-emergency-access-accounts"></a>Vészhelyzeti hozzáférési fiókok létrehozása

Hozzon létre két vagy több vészhelyzeti hozzáférési fiókot. Ezeknek a fiókoknak kizárólag felhőalapú fiókoknak kell lenniük, amelyek a \* . onmicrosoft.com tartományt használják, és amelyek nem összevont vagy nem szinkronizált helyszíni környezetből.

A fiókok konfigurálásakor a következő követelményeknek kell teljesülniük:

- A segélyhívó fiókok nem társíthatók a szervezet egyetlen felhasználójának sem. Győződjön meg arról, hogy a fiókok nincsenek csatlakoztatva az alkalmazottak által biztosított mobil telefonokhoz, az egyes alkalmazottakkal vagy más, az alkalmazottakra vonatkozó hitelesítő adatokkal rendelkező hardveres jogkivonatokhoz. Ez az elővigyázatosság olyan példányokra vonatkozik, amelyekben az egyes alkalmazottak nem érhetők el, ha a hitelesítő adatok szükségesek. Fontos annak biztosítása, hogy a regisztrált eszközök egy ismert, biztonságos helyen legyenek tárolva, amely több módon kommunikál az Azure AD-vel.
- A vészhelyzeti hozzáférési fiókhoz használt hitelesítési mechanizmusnak eltérőnek kell lennie a többi rendszergazdai fiók által használttól, beleértve az egyéb vészhelyzeti hozzáférési fiókokat is.  Ha például a normál rendszergazdai bejelentkezés a helyszíni MFA-n keresztül történik, akkor az Azure AD MFA egy másik mechanizmus lenne.  Ha azonban az Azure AD MFA a rendszergazdai fiókok hitelesítésének elsődleges része, akkor érdemes lehet más megközelítést használni, például ha feltételes hozzáférést használ egy harmadik féltől származó MFA-szolgáltatóhoz egyéni vezérlők segítségével.
- A használat hiánya miatt az eszköznek vagy a hitelesítő adatnak nem szabad lejárnia, illetve nem lehet az automatikus karbantartás hatóköre.  
- A vészhelyzeti hozzáférési fiókok esetében a globális rendszergazdai szerepkör-hozzárendelést véglegesen el kell végezni. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Legalább egy fiók kizárása a telefon alapú multi-Factor Authentication szolgáltatásból

Ha csökkenteni szeretné a sérült jelszó miatti támadás kockázatát, akkor az Azure AD azt javasolja, hogy minden egyes felhasználóhoz többtényezős hitelesítést igényeljen. Ebbe a csoportba tartoznak a rendszergazdák és az összes többi (például pénzügyi tisztviselők), akiknek a feltört fiók jelentős hatással lenne.

A segélyhívó fiókok közül legalább egy nem rendelkezhet ugyanazzal a többtényezős hitelesítési mechanizmussal, mint a többi nem vészhelyzeti fiók. Ez magában foglalja a külső felek többtényezős hitelesítési megoldásait is. Ha feltételes hozzáférési szabályzattal szeretné megkövetelni a [többtényezős hitelesítés használatát az](../authentication/howto-mfa-userstates.md) Azure ad-hez és más csatlakoztatott szoftverekhez (SaaS) tartozó összes rendszergazda számára, ki kell zárnia a vészhelyzeti hozzáférési fiókokat ebből a követelményből, és egy másik mechanizmust kell konfigurálnia. Emellett győződjön meg arról, hogy a fiókok nem rendelkeznek felhasználónkénti multi-Factor Authentication-házirenddel.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Válasszon ki legalább egy fiókot a feltételes hozzáférési házirendből

Vészhelyzet esetén nem szeretné, hogy a házirend ne tiltsa le a probléma megoldásához szükséges hozzáférést. Legalább egy vészhelyzeti hozzáférési fiókot ki kell zárni az összes feltételes hozzáférési szabályzatból.

## <a name="federation-guidance"></a>Összevonási útmutató

Egyes szervezetek az AD tartományi szolgáltatásokat és az ADFS-t, vagy hasonló identitás-szolgáltatót használnak az Azure AD-hez való összevonása. Nincsenek [rendszergazdai jogosultságokkal rendelkező helyszíni fiókok](../fundamentals/protect-m365-from-on-premises-attacks.md). Az Azure AD-n kívüli rendszergazdai jogosultsággal rendelkező fiókok felügyeletének vagy beszerzésének elsajátítása szükségtelen kockázatot jelent a rendszer (ek) meghibásodása vagy sérülése esetén.

## <a name="store-account-credentials-safely"></a>Fiók hitelesítő adatainak biztonságos tárolása

A szervezeteknek biztosítaniuk kell, hogy a vészhelyzeti hozzáférési fiókok hitelesítő adatai biztonságban legyenek, és csak olyan személyeknek legyenek ismertek, akik jogosultak a használatra. Néhány ügyfél intelligens kártyát használ, mások pedig jelszavakat használnak. A vészhelyzeti hozzáférési fiókhoz tartozó jelszó általában két vagy három részre van elkülönítve, külön papírra írva, és biztonságos, tűzálló széfekben, amelyek biztonságos, külön helyen vannak tárolva.

Ha jelszavakat használ, győződjön meg arról, hogy a fiókok olyan erős jelszavakkal rendelkeznek, amelyek nem járnak le a jelszóban. Ideális esetben a jelszónak legalább 16 karakter hosszúnak és véletlenszerűen generáltnak kell lennie.

## <a name="monitor-sign-in-and-audit-logs"></a>Bejelentkezések és naplók figyelése

A szervezeteknek figyelniük kell a bejelentkezési és a naplózási tevékenységet a vészhelyzeti fiókokból, és el kell indítaniuk az értesítéseket a többi rendszergazdának. A break Glass-fiókok tevékenységének figyelése esetén ellenőrizheti, hogy ezek a fiókok csak teszteléshez vagy tényleges vészhelyzetekhez használatosak. Az Azure Log Analytics segítségével figyelheti a bejelentkezési naplókat, és elindíthatja az e-mailek és SMS-riasztásokat a rendszergazdáknak, amikor az üveg-fiókok bejelentkeznek.

### <a name="prerequisites"></a>Előfeltételek

1. [Azure ad bejelentkezési naplók küldése](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Azure monitorra.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>A break Glass-fiókok objektumazonosítók beszerzése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a felhasználói rendszergazdai szerepkörhöz.
1. Válassza ki **Azure Active Directory**  >  **felhasználókat**.
1. Keressen rá a break-Glass fiókra, és válassza ki a felhasználó nevét.
1. Másolja ki és mentse az Object ID attribútumot, hogy később is használhassa.
1. Ismételje meg az előző lépéseket a második break-Glass-fióknál.

### <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely a megfigyelő közreműködő szerepkörhöz van rendelve Azure monitorban.
1. Válassza a **minden szolgáltatás** lehetőséget, írja be a "log Analytics" kifejezést a Keresés mezőbe, majd válassza ki **log Analytics munkaterületeket**.
1. Jelöljön ki egy munkaterületet.
1. A munkaterületen válassza a **riasztások**  >  **új riasztási szabály** lehetőséget.
    1. Az **erőforrás** területen ellenőrizze, hogy az előfizetés az, amelyhez a riasztási szabályt hozzá kívánja rendelni.
    1. A **feltétel** területen válassza a **Hozzáadás** lehetőséget.
    1. Válassza az **egyéni naplók keresése** lehetőséget a **jel neve** alatt.
    1. A **keresési lekérdezés** területen adja meg a következő lekérdezést, szúrja be a két break Glass-fiók objektum-azonosítóit.
        > [!NOTE]
        > Minden további felvenni kívánt break Glass-fiókhoz adjon hozzá egy másik "vagy UserId = =" ObjectGuid a lekérdezéshez.

        ![A break Glass-fiókok objektumazonosítók hozzáadása egy riasztási szabályhoz](./media/security-emergency-access/query-image1.png)

    1. A **riasztási logika** területen adja meg a következőket:

        - A következő alapján: az eredmények száma
        - Operátor: nagyobb, mint
        - Küszöbérték: 0

    1. A **kiértékelve** alapján területen válassza ki azt az **időtartamot (percben)** , ameddig a lekérdezés futtatásának idejét szeretné használni, valamint a **gyakoriságot (percben)** , hogy milyen gyakran szeretné futtatni a lekérdezést. A gyakoriság értéke nem lehet kisebb, mint az időszak.

        ![riasztási logika](./media/security-emergency-access/alert-image2.png)

    1. Válassza a **Kész** lehetőséget. Most már megtekintheti a riasztás becsült havi költségét.
1. Válassza ki a riasztás által bejelentendő felhasználók műveleti csoportját. Ha létre szeretne hozni egyet, tekintse meg [a műveleti csoport létrehozása](#create-an-action-group)című témakört.
1. Ha testre szeretné szabni a műveleti csoport tagjainak küldött e-mailes értesítést, a műveletek **testreszabása** területen válassza a műveletek lehetőséget.
1. A **riasztás részletei** területen adja meg a riasztási szabály nevét, és adjon hozzá egy opcionális leírást.
1. Az esemény **súlyossági szintjének** beállítása. Javasoljuk, hogy a kritikus értékre **(0.)** állítsa be.
1. A **szabály engedélyezése a létrehozáskor** területen hagyja a beállítást **Igen** értékre állítva.
1. Ha egy ideig ki szeretné kapcsolni a riasztásokat, jelölje be a **riasztások mellőzése** jelölőnégyzetet, és adja meg a várakozási időtartamot a riasztás újbóli kikapcsolása előtt, majd válassza a **Mentés** lehetőséget.
1. Kattintson a **Riasztási szabály létrehozása** lehetőségre.

### <a name="create-an-action-group"></a>Műveleti csoport létrehozása

1. Válassza **a műveleti csoport létrehozása** lehetőséget.

    ![műveleti csoport létrehozása értesítési műveletekhez](./media/security-emergency-access/action-group-image3.png)

1. Adja meg a műveleti csoport nevét és egy rövid nevet.
1. Ellenőrizze az előfizetést és az erőforráscsoportot.
1. A művelet típusa területen válassza az **e-mail/SMS/leküldés/hang** lehetőséget.
1. Adja meg a művelet nevét, például: **globális rendszergazda értesítése**.
1. Válassza ki a **műveletet** **e-mail/SMS/leküldés/hangként**.
1. Válassza a **részletek szerkesztése** lehetőséget a konfigurálni kívánt értesítési módszerek kiválasztásához, majd adja meg a szükséges kapcsolattartási adatokat, majd a részletek mentéséhez kattintson **az OK gombra** .
1. Adja meg az aktiválni kívánt további műveleteket.
1. Válassza az **OK** lehetőséget.

## <a name="validate-accounts-regularly"></a>Fiókok rendszeres ellenőrzése

Ha a személyzet tagjait vészhelyzeti hozzáférési fiókok használatára tanítja, és ellenőrzi a vészhelyzeti hozzáférési fiókokat, legalább a következő lépéseket kell elvégeznie rendszeres időközönként:

- Győződjön meg arról, hogy a biztonsági figyelő személyzete tisztában van azzal, hogy a fiók-ellenőrzési tevékenység folyamatban van.
- Győződjön meg arról, hogy a válságkezelési folyamat ezen fiókok használatára van dokumentálva és naprakész.
- Győződjön meg arról, hogy a folyamat során a rendszergazdák és a biztonsági tisztviselők, akiknek szükségük lehet ezen lépések végrehajtására.
- Frissítse a fiók hitelesítő adatait – különösen a jelszavakat – a vészhelyzeti hozzáférési fiókok esetében, majd ellenőrizze, hogy a segélyhívó fiókok be tudnak-e jelentkezni, és rendszergazdai feladatokat hajtson végre.
- Győződjön meg arról, hogy a felhasználók nem regisztrálták Multi-Factor Authentication vagy az önkiszolgáló jelszó-visszaállítást (SSPR) bármely egyéni felhasználó eszközére vagy személyes adataira. 
- Ha a fiókok regisztrálva vannak Multi-Factor Authentication eszközre, akkor a bejelentkezés vagy a szerepkör aktiválása során győződjön meg arról, hogy az eszköz elérhető az összes olyan rendszergazda számára, aki vészhelyzetben szüksége lehet rá. Ellenőrizze azt is, hogy az eszköz legalább két olyan hálózati útvonalon keresztül tud-e kommunikálni, amelyek nem rendelkeznek közös meghibásodási móddal. Az eszköz például képes kommunikálni az internettel a létesítmény vezeték nélküli hálózatán és a cella szolgáltatói hálózatán keresztül is.

Ezeket a lépéseket rendszeres időközönként és a legfontosabb változásokhoz kell végrehajtani:

- Legalább 90 naponta
- Ha a közelmúltban módosult az informatikai személyzet, például a feladatok változása, a távozás vagy egy új bérlet
- Ha a szervezet Azure AD-előfizetései megváltoztak

## <a name="next-steps"></a>Következő lépések

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](security-planning.md)
- [Felhasználók hozzáadása az Azure ad használatával](../fundamentals/add-users-azure-active-directory.md) és [az új felhasználó társítása a globális rendszergazdai szerepkörhöz](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Regisztráljon a prémium szintű Azure ADra](../fundamentals/active-directory-get-started-premium.md), ha még nem regisztrált már
- [Egy felhasználó kétlépéses ellenőrzésének megkövetelése](../authentication/howto-mfa-userstates.md)
- A [Microsoft 365 globális rendszergazdáinak további védelmét is konfigurálhatja](/office365/enterprise/protect-your-global-administrator-accounts), ha Microsoft 365
- A [globális rendszergazdák hozzáférési felülvizsgálatának elindítása](../privileged-identity-management/pim-how-to-start-security-review.md) és [a meglévő globális rendszergazdák átváltása a rendszergazda szerepköreire](permissions-reference.md)