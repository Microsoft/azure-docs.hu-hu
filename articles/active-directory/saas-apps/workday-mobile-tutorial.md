---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a munkanap Mobile alkalmazással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a munkanap Mobile alkalmazás között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: ef1ca41f54a15554a04fa3edf608bb13f5fb3398
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182019"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a munkanap Mobile alkalmazással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD), a feltételes hozzáférést és az Intune-t a munkanap Mobile alkalmazással. Ha a Microsofttal integrálja a munkanap Mobile Application alkalmazást, a következőket teheti:

* Bejelentkezés előtt gondoskodjon arról, hogy az eszközök megfeleljenek a szabályzatoknak.
* Adjon hozzá vezérlőket a munkanap Mobile Application szolgáltatáshoz, hogy a felhasználók biztonságosan hozzáférjenek a vállalati adatokhoz. 
* A munkanapokhoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a munkanapokba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépések:

* Munkanapok integrálása az Azure AD-vel.
* Olvassa el [Azure Active Directory egyszeri bejelentkezéses (SSO) integrációt a munkanapokkal](./workday-tutorial.md).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD feltételes hozzáférési szabályzatait és az Intune-t a munkanap Mobile Application szolgáltatással konfigurálja és teszteli.

Az egyszeri bejelentkezés (SSO) engedélyezéséhez az Azure AD-vel konfigurálhatja a munkanap összevont alkalmazását. További információ: [Azure Active Directory egyszeri bejelentkezés (SSO) integrálása munkanapokkal](./workday-tutorial.md).

> [!NOTE] 
> A munkanap nem támogatja az Intune alkalmazás-védelmi szabályzatait. A feltételes hozzáférés használatához a mobileszközök felügyeletét kell használnia.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Annak biztosítása, hogy a felhasználók hozzáférjenek a munkanap Mobile-alkalmazáshoz

A munkanap beállítása a mobil alkalmazásaihoz való hozzáférés engedélyezéséhez. A következő szabályzatokat kell konfigurálnia a munkanap Mobile-hoz:

1. A működési terület jelentéshez tartozó tartományi biztonsági házirendek elérése.
1. Válassza ki a megfelelő biztonsági házirendet:
    * Mobil használat – Android
    * Mobil használat – iPad
    * Mobil használat – iPhone
1. Válassza az **engedélyek szerkesztése** lehetőséget.
1. Jelölje be a **megtekintés vagy a módosítás** jelölőnégyzetet, hogy a biztonsági csoportok hozzáférhessenek a jelentéshez vagy a feladat biztonságossá tételéhez.
1. Jelölje be a **Get vagy a Put** jelölőnégyzetet, hogy a biztonsági csoportok hozzáférjenek az integrációhoz, a jelentéshez vagy a feladat biztonságos műveleteihez.

A függőben lévő biztonsági szabályzatok módosításának aktiválása a **függőben lévő biztonsági szabályzatok aktiválásának** futtatásával.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>A munkaidő-bejelentkezési oldal megnyitása a munkanap mobil böngészőben

Ha feltételes hozzáférést szeretne alkalmazni a munkanap Mobile alkalmazáshoz, meg kell nyitnia az alkalmazást egy külső böngészőben. A **bérlői telepítő szerkesztése – biztonság** területen válassza a **mobil böngésző egyszeri bejelentkezésének engedélyezése natív alkalmazásokhoz** lehetőséget. Ehhez az Intune által jóváhagyott böngészőt kell telepíteni az iOS rendszerű eszközön, illetve az androidos munkahelyi profilban.

![A munkanap mobil böngésző bejelentkezésének képernyőképe.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Feltételes hozzáférési szabályzat beállítása

Ez a szabályzat csak az iOS-vagy Android-eszközökön való bejelentkezést érinti. Ha az összes platformra ki szeretné terjeszteni, válassza ki a kívánt **eszközt**. Ehhez a Szabályzathoz az eszköznek meg kell felelnie a szabályzatnak, és az Intune-on keresztül kell igazolnia. Mivel az Android munkahelyi profilokkal rendelkezik, ez letiltja a felhasználókat abban, hogy bejelentkezzenek munkanapokba, hacsak nem jelentkeznek be a munkahelyi profilon keresztül, és az alkalmazást az Intune vállalati portálon keresztül telepítették. Az iOS-hez egy további lépés is gondoskodik arról, hogy ugyanez a helyzet érvényes legyen.

A munkanap a következő hozzáférési vezérlőket támogatja:
* Többtényezős hitelesítés megkövetelése
* Eszköz megfelelőként való megjelölésének megkövetelése

A munkanap alkalmazás nem támogatja a következőket:
* Jóváhagyott ügyfélalkalmazás megkövetelése
* Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)

Ha a munkanapokat felügyelt eszközként szeretné beállítani, hajtsa végre a következő lépéseket:

![Képernyőfelvétel csak a felügyelt eszközökről és a Felhőbeli alkalmazásokról vagy műveletekről.](./media/workday-tutorial/managed-devices-only.png)

1. Válassza a **Kezdőlap**  >  **Microsoft Intune**  >  **feltételes hozzáférés – szabályzatok** lehetőséget. Ezután válassza a **csak felügyelt eszközök** lehetőséget. 

1. **Csak felügyelt eszközök** esetén a **név** területen válassza a **csak felügyelt eszközök** lehetőséget, majd válassza a **felhőalapú alkalmazások vagy műveletek** lehetőséget.

1. **Felhőalapú alkalmazásokban vagy műveletekben**:

    a. Váltson át a **felhőalapú alkalmazásokra** **vonatkozó szabályzat kiválasztásához** .

    b. A **Belefoglalás** területen válassza az **alkalmazások kiválasztása** lehetőséget.

    c. A **kiválasztás** listából válassza a **munkanap** lehetőséget.

    d. Válassza a **Kész** lehetőséget.

1. Kapcsolja be az engedélyezési **szabályzatot** **a** következőre:.

1. Kattintson a **Mentés** gombra.

A hozzáférés **biztosítása** érdekében hajtsa végre a következő lépéseket:

![Képernyőfelvétel csak a felügyelt eszközökről, és a megadása.](./media/workday-tutorial/managed-devices-only-2.png)

1. Válassza a **Kezdőlap**  >  **Microsoft Intune**  >  **feltételes hozzáférés – szabályzatok** lehetőséget. Ezután válassza a **csak felügyelt eszközök** lehetőséget. 

1. A **csak felügyelt eszközök** **területen válassza a** **csak felügyelt eszközök** elemet. A **Hozzáférés-vezérlés** alatt válassza ki az **Engedélyezés** elemet.

1. A **Grant**:

    a. Válassza ki azokat a vezérlőket, amelyek számára **engedélyezi a hozzáférést**.

    b. Válassza ki az **Eszköz megfelelőként való megjelölésének megkövetelése** elemet.

    c. Jelölje be **a kiválasztott vezérlők egyikének megkövetelése** jelölőnégyzetet.

    d. Válassza a **Kiválasztás** lehetőséget

1. Kapcsolja be az engedélyezési **szabályzatot** **a** következőre:.

1. Kattintson a **Mentés** gombra.

## <a name="set-up-device-compliance-policy"></a>Eszköz megfelelőségi szabályzatának beállítása

Annak biztosítása érdekében, hogy az iOS-eszközök csak a mobileszköz-kezelés által kezelt munkanapokon keresztül legyenek bejelentkezni, le kell tiltania az App Store-alkalmazást a **com. munkanap. workdayapp** hozzáadásával a korlátozott alkalmazások listájához. Ez biztosítja, hogy csak a vállalati portálon keresztül telepített munkanapokhoz tartozó eszközök férhessenek hozzá a munkanapokhoz. A böngésző esetében az eszközök csak akkor férhetnek hozzá a munkanapokhoz, ha az eszközt az Intune felügyeli, és felügyelt böngészőt használ.

![Képernyőkép az iOS-eszközök megfelelőségi házirendjéről.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Intune-alkalmazás konfigurációs házirendjeinek beállítása

| Eset | Kulcs érték párok |
|----------------------------------------------------------------------------------------   |-----------|
| A bérlői és a webes címek mezőinek automatikus feltöltése:<br>● Munkanapokat az Android for Work-profilok engedélyezésekor.<br>● Munkanapokat iPaden és iPhone-on.     | A bérlő konfigurálásához használja ezeket az értékeket: <br>● Konfigurációs kulcs = `UserGroupCode`<br>● Érték típusa = karakterlánc <br>● Konfigurációs érték = a bérlő neve. Például: `gms`<br>A következő értékek használatával konfigurálhatja a webcímet:<br>● Konfigurációs kulcs = `AppServiceHost`<br>● Érték típusa = karakterlánc<br>● Konfigurációs érték = a bérlő alap URL-címe. Például: `https://www.myworkday.com`                                |   |
| A következő műveletek letiltása a munkanapokon iPaden és iPhone-on:<br>● Kivágás, másolás és beillesztés<br>● Nyomtatás                       | A funkció letiltásához állítsa be a kulcs értékét (Boolean) a `False` következőre:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| A képernyőképek letiltása az Androidon futó munkanapokon. |A funkció letiltásához állítsa a (logikai) értéket a `False` `AllowScreenshots` kulcsra.|
| Tiltsa le a javasolt frissítéseket a felhasználók számára.|A funkció letiltásához állítsa a (logikai) értéket a `False` `AllowSuggestedUpdates` kulcsra.|
|Szabja testre az App Store-beli URL-címét, hogy a felhasználók az Ön által választott App Store-ban irányítsák a mobil felhasználókat.|Az alkalmazás-áruház URL-címének módosításához használja ezeket az értékeket:<br>● Konfigurációs kulcs = `AppUpdateURL`<br>● Érték típusa = karakterlánc<br> ● Konfigurációs érték = alkalmazás-áruház URL-címe|
|       |


## <a name="ios-configuration-policies"></a>iOS-t futtató eszközökhöz készült konfigurációs szabályzatok

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be.
1. Keressen rá az **Intune** kifejezésre, vagy válassza ki a widgetet a listából.
1. Válassza az **ügyfélalkalmazások**  >  **alkalmazások**  >  **alkalmazás-konfigurációs szabályzatok** lehetőséget. Ezután válassza a **+**  >  **felügyelt eszközök** hozzáadása elemet.
1. Adjon meg egy nevet.
1. A **platform** területen válassza az **iOS/iPadOS** elemet.
1. A **társított alkalmazás** alatt válassza ki a munkaterületet a hozzáadott iOS-alkalmazáshoz.
1. Válassza a **konfigurációs beállítások** lehetőséget. A **konfigurációs beállítások formátuma** területen válassza az **XML-adat megadása** lehetőséget.
1. Íme egy példa XML-fájl. Adja meg az alkalmazni kívánt konfigurációkat. Cserélje le `STRING_VALUE` a szöveget a használni kívánt karakterláncra. Cserélje le `<true /> or <false />` a `<true />` -t vagy a-t  `<false />` . Ha nem ad hozzá egy konfigurációt, ez a példa úgy működik, mint a (z) értékre `True` .

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Válassza a **Hozzáadás** lehetőséget.
1. Frissítse az oldalt, és válassza ki az újonnan létrehozott házirendet.
1. Válassza a **hozzárendelések** lehetőséget, majd válassza ki, hogy ki szeretné alkalmazni az alkalmazást.
1. Kattintson a **Mentés** gombra.

## <a name="android-configuration-policies"></a>Konfigurációs szabályzatok Android-alapú eszközökhöz

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be.
2. Keressen rá az **Intune** kifejezésre, vagy válassza ki a widgetet a listából.
3. Válassza az **ügyfélalkalmazások**  >  **alkalmazások**  >  **alkalmazás-konfigurációs szabályzatok** lehetőséget. Ezután válassza a **+**  >  **felügyelt eszközök** hozzáadása elemet.
5. Adjon meg egy nevet. 
6. A **platform** területen válassza az **Android** lehetőséget.
7. A **társított alkalmazás** alatt válassza ki az Android-alkalmazáshoz hozzáadott munkanapokat.
8. Válassza a **konfigurációs beállítások** lehetőséget. A **konfigurációs beállítások formátuma** területen válassza a **JSON-adat megadása** lehetőséget.