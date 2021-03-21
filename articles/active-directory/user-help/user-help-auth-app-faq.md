---
title: Kérdések & Microsoft Authenticator alkalmazással kapcsolatos válaszok – Azure AD
description: Gyakori kérdések és válaszok (GYIK) a Microsoft-hitelesítési alkalmazásról és a kétfaktoros ellenőrzésről.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 8ddc02075d2cdeba4a4fa83a2475ec20c3f18d6e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418079"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Gyakran ismételt kérdések (GYIK) a Microsoft Authenticator alkalmazásról

Ez a cikk a Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdésekre ad választ. Ha nem talál választ a kérdésére, lépjen a [Microsoft Authenticator alkalmazás fórumára](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

A Microsoft Authenticator alkalmazás felváltotta a Azure Authenticator alkalmazást, és az Azure AD-Multi-Factor Authentication használata esetén az ajánlott alkalmazás. Az Microsoft Authenticator alkalmazás [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) és [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)rendszerhez érhető el.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="registering-a-device"></a>Eszköz regisztrálása

**K**: regisztrálja az eszközt úgy, hogy a vállalat vagy szolgáltatás számára hozzáférést biztosítson az eszközhöz?

**A**: az eszköz regisztrálása lehetővé teszi, hogy az eszköz hozzáférjen a szervezet szolgáltatásaihoz, és nem teszi lehetővé a szervezet számára az eszköz hozzáférését.

### <a name="error-adding-account"></a>Hiba a fiók hozzáadásakor

**K**: ha megpróbálom felvenni a fiókomat, hibaüzenet jelenik meg, amely azt jelzi, hogy a hozzáadni kívánt fiók jelenleg nem érvényes. A probléma megoldásához forduljon a rendszergazdához (egyediség ellenőrzése). " Mit tegyek?

Válasz **: forduljon** a rendszergazdához, és tudassa velük, hogy nem tudja hozzáadni a fiókját a hitelesítő számára az egyediség-ellenőrzési probléma miatt. Meg kell adnia a bejelentkezési felhasználónevét, hogy a rendszergazda meg tudja keresni a szervezetében.

### <a name="legacy-apns-support-deprecated"></a>Örökölt APNs-támogatás elavult

**K**: mivel az Apple push Notification szolgáltatás örökölt bináris felülete elavult a 2020-es verzióban, hogyan használhatom Microsoft Authenticator/Phone faktor használatát a bejelentkezéshez?

**A**: az Apple bejelentette, hogy az iOS-eszközökhöz (például a telefonos faktor által használt) bináris felületét használó leküldéses értesítések [elavultak](https://developer.apple.com/news/?id=11042019a) . A leküldéses értesítések fogadásának folytatásához javasoljuk, hogy a felhasználók a hitelesítő alkalmazást az alkalmazás legújabb verziójára frissítse. Addig is megkerülheti, ha a hitelesítő alkalmazásban manuálisan ellenőrzi az értesítéseket.

### <a name="app-lock-feature"></a>Az alkalmazás zárolási funkciója

**K**: Mi az az App Lock, és hogyan használhatom, hogy segítsenek biztonságosabbá tenni?

**A**: az alkalmazás-zárolással biztonságosabbá teheti az egyszeri ellenőrző kódokat, az alkalmazás adatait és az alkalmazás beállításait. Ha az alkalmazás zárolása engedélyezve van, az eszköz PIN-kódjának vagy a biometrikus adatoknak a hitelesítő minden megnyitásakor meg kell adnia a hitelesítést. Az alkalmazás-zárolás azt is lehetővé teszi, hogy Ön az egyetlen, aki jóváhagyhatja az értesítéseket a PIN-kód vagy a biometrikus adatoknak a bejelentkezési értesítések jóváhagyásakor történő megadásával. Az alkalmazás-zárolás be-és kikapcsolása a hitelesítő beállítások oldalon végezhető el. Alapértelmezés szerint az alkalmazás zárolása be van kapcsolva az eszközön található PIN-kód vagy biometrika beállításakor.<br><br>Sajnos nincs garancia arra, hogy az alkalmazás zárolása leállítja valakinek a hitelesítő számára való hozzáférését. Ennek az az oka, hogy az eszköz regisztrációja a hitelesítő kívül más helyekre is megtörténhet, például az Android-Fiókbeállítások vagy a céges portál alkalmazásban.

### <a name="windows-mobile-retired"></a>Windows Mobile kivont

**K**: Windows Mobile-eszközem van, és a windows Mobile Microsoft Authenticator elavult. Folytatható a hitelesítés az alkalmazás használatával?

**A**: a Windows Mobile Microsoft Authenticatort használó összes hitelesítés 2020. július 15-én megszűnik. Azt javasoljuk, hogy használjon egy alternatív hitelesítési módszert a fiókok zárolásának elkerülése érdekében.<br>A vállalati felhasználók alternatív lehetőségei a következők:<br><ul><li>Az [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) vagy [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)rendszerhez készült Microsoft Authenticator beállítása.</li><li>Az [SMS beállítása](multi-factor-authentication-setup-phone-number.md) az ellenőrző kódok fogadására.</li><li>A telefonszám beállítása a telefonhívások fogadására az [identitásuk ellenőrzéséhez](multi-factor-authentication-setup-office-phone.md).</li></ul><br>A személyes Microsoft-fiók felhasználók alternatív beállításai a következők:<br><ul><li>Az [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) vagy [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)rendszerhez készült Microsoft Authenticator beállítása.</li><li>Alternatív bejelentkezési módszer (SMS vagy e-mail) beállítása a biztonsági adatok [Microsoft-fiók biztonsága lapról](https://account.microsoft.com/security/)történő frissítésével.</li></ul>

### <a name="android-screenshots"></a>Androidos képernyőképek

**K**: képernyőképeket készíthetek az egyszeri jelszavas (OTP) kódokról az Android-hitelesítő szolgáltatásban?

**A**: a hitelesítő Android kiadási 6.2003.1704 kezdődően alapértelmezés szerint minden egyszeri jelszavas kód rejtve marad a hitelesítő képernyőképének megadásával. Ha szeretné megtekinteni az OTP-kódokat a képernyőképeken, vagy más alkalmazások számára is lehetővé teszi a hitelesítő képernyő rögzítését, akkor a következőt teheti:. Csak kapcsolja be a **Screen Capture** beállítást a hitelesítő eszközben, és indítsa újra az alkalmazást.

### <a name="delete-stored-data"></a>Tárolt adattárolók törlése

**K**: milyen adatkezelési tárolót használ a hitelesítő az Ön nevében, és hogyan törölhetem?

**A**: a hitelesítő alkalmazás háromféle típusú információt gyűjt:

- A fiók hozzáadásakor megadott fiókadatok. Ezeket az adatvesztést a fiók eltávolításával távolíthatja el.
- A csak az alkalmazásban maradó diagnosztikai naplófájlok, amíg el nem **küld visszajelzést** az alkalmazás felső menüjében, hogy naplókat küldjön a Microsoftnak. Ezek a naplók személyes, például e-mail-címeket, kiszolgálói címeket vagy IP-címeket is tartalmazhatnak. Tartalmazhatnak olyan eszközöket is, mint az eszköz neve és az operációs rendszer verziója. A gyűjtött személyes adatok az alkalmazással kapcsolatos problémák elhárításához szükséges információkra korlátozódnak. Ezeket a naplófájlokat bármikor megtekintheti az alkalmazásban az összegyűjtött adatok megtekintéséhez. A naplófájlok elküldésekor a hitelesítési alkalmazás-mérnökök csak az ügyfél által jelentett problémák megoldására használják azokat.
- Nem személyes azonosításra alkalmas használati adatok, például "a fiók hozzáadása a folyamathoz/sikeres Hozzáadás fiókhoz" vagy "értesítés jóváhagyva". Ezek az adatai a mérnöki döntések szerves részét képezik. A használat segít megállapítani, hogy hol javíthatjuk az alkalmazásokat az Ön számára fontos módokon. Ezt az adatgyűjtési értesítést akkor láthatja, amikor első alkalommal használja az alkalmazást. Arról tájékoztatja, hogy ki lehet kapcsolni az alkalmazás **Beállítások**   lapján. Ezt a beállítást bármikor be-és kikapcsolhatja.

### <a name="codes-in-the-app"></a>Az alkalmazásban szereplő kódok

**K**: milyen kódok szerepelnek az alkalmazásban?

**A**: a hitelesítő megnyitásakor mozaikként fogja látni a hozzáadott fiókokat. A munkahelyi vagy iskolai fiókjai és a személyes Microsoft-fiókjai hat vagy nyolc számjegyből állnak a fiók teljes képernyős nézetében (a fiók csempére koppintva). Más fiókok esetében az alkalmazás **fiókok** lapján egy hat vagy nyolc számjegyből álló szám jelenik meg.<br>Ezeket a kódokat egyszer használatos jelszóként fogja használni annak ellenőrzéséhez, hogy Ön kinek szól. Miután bejelentkezett felhasználónevével és jelszavával, írja be a fiókhoz társított ellenőrző kódot. Például, ha a Katy-fiókjába jelentkezik be, akkor koppintson a fiók csempére, majd használja az 895823-es ellenőrző kódot. Az Outlook-fiók esetében ugyanazokat a lépéseket kell követnie.<br>Koppintson a contoso-fiók csempére.<br>![A hitelesítő alkalmazásban lévő fiókok csempéi](media/user-help-auth-app-faq/katy-signin.png)<br>A contoso-fiók csempére koppintva az ellenőrző kód teljes képernyőn látható.<br>![Ellenőrző kód a hitelesítő fiókjának csempéje](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Időzítő visszaszámlálása

**K**: miért tartja a kód melletti számot a számlálás során?

**A**: az aktív ellenőrző kód mellett egy 30 másodperces időzítőt is láthat. Ez az időzítő úgy van, hogy soha ne jelentkezzen be kétszer ugyanazzal a kóddal. A jelszótól eltérően nem szeretnénk megjegyezni ezt a számot. Az a gondolat, hogy csak valaki fér hozzá a telefonjára, ismeri a kódot.

### <a name="grayed-account-tile"></a>Szürke fiók csempe

**K**: Miért szürke a fiókom csempe?

**A**: néhány szervezet megköveteli, hogy a hitelesítő működjön az egyszeri bejelentkezéssel, és hogy megvédje a szervezeti erőforrásokat. Ebben az esetben a fiók nem a kétlépéses ellenőrzéshez használatos, és szürke vagy inaktívként jelenik meg. Ezt a típusú fiókot gyakran "Broker"-fióknak nevezzük.

### <a name="device-registration"></a>Eszközregisztráció

**K**: Mi az az eszköz regisztrálása?

**A**: a szervezete megkövetelheti, hogy regisztrálja az eszközt a biztonságos erőforrásokhoz, például fájlokhoz és alkalmazásokhoz való hozzáférés nyomon követéséhez. Az is előfordulhat, hogy a feltételes hozzáférés bekapcsolásával csökkentheti az adott erőforrásokhoz való nemkívánatos hozzáférés kockázatát. Megszüntetheti az eszköz regisztrációját a **beállításokban**, de elveszítheti az e-mailek elérését az Outlookban, a OneDrive lévő fájlokat, és elveszti a telefonos bejelentkezés lehetőségét.

### <a name="verification-codes-when-connected"></a>Ellenőrző kódok a csatlakozáskor

**K**: csatlakoztatni kell az internetet vagy a hálózatot az ellenőrző kódok beszerzéséhez és használatához?

**A**: a kódok nem igénylik az interneten vagy az adatkapcsolathoz való csatlakozást, így nincs szükség telefonos szolgáltatásra a bejelentkezéshez. Emellett az is előfordulhat, hogy az alkalmazás a Bezárás után azonnal leáll, ezért nem fogja lecsapolni az akkumulátort.

### <a name="no-notifications-when-app-is-closed"></a>Nincs értesítés az alkalmazás bezárásakor

**K**: Miért csak az alkalmazás megnyitásakor kapok értesítéseket? Az alkalmazás bezárásakor nem kapok értesítést.

**A**: Ha értesítést kap, de nem riasztást, még a Ringer-on is, ellenőrizze az alkalmazás beállításait. Győződjön meg arról, hogy az alkalmazás be van kapcsolva a hang használatára, vagy az értesítések vibrálása. Ha egyáltalán nem kap értesítést, ellenőrizze a következő feltételeket:<ul><li>A telefonja nem zavarja vagy nem halk módot? Ezek az üzemmódok megakadályozhatják, hogy az alkalmazások értesítéseket küldjenek.</li><li>Más alkalmazásokból is kaphat értesítéseket? Ha nem, akkor probléma lehet a telefon hálózati kapcsolataival vagy az Android vagy az Apple értesítési csatornával. Megpróbálkozhat a hálózati kapcsolatainak a telefonos beállításokkal való feloldásával. Előfordulhat, hogy a szolgáltatóval kell kommunikálnia az Android-vagy az Apple Notifications-csatornával kapcsolatos segítségért.</li><li>Kaphat-e értesítéseket az alkalmazás egyes fiókjaihoz, de másokat nem? Ha igen, távolítsa el a problémás fiókot az alkalmazásból, adja hozzá újból az értesítéseket, és ellenőrizze, hogy a probléma kijavítva van-e.</li></ul>Ha kipróbálta ezeket a lépéseket, és továbbra is problémába ütközik, javasoljuk, hogy küldje el a naplófájlokat a diagnosztika számára. Nyissa meg az alkalmazást, lépjen az alkalmazás legfelső szintű menüjébe, majd válassza a **visszajelzés küldése** lehetőséget. Ezután nyissa meg a [Microsoft Authenticator alkalmazás fórumát](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) , és tájékoztassa a Microsoftot a megjelenő problémáról és a szükséges lépésekről.

### <a name="switch-to-push-notifications"></a>Váltás leküldéses értesítésekre

**K**: az alkalmazásban ellenőrző kódokat használok, de Hogyan válthatok le a leküldéses értesítésekre?

**A**: beállíthat értesítéseket munkahelyi vagy iskolai fiókjához (ha a rendszergazda engedélyezi) vagy a személyes Microsoft-fiók. Az értesítések nem működnek a harmadik féltől származó fiókok, például a Google vagy a Facebook esetében.<br>Ha a személyes fiókját át szeretné váltani az értesítésekre, újra regisztrálnia kell az eszközt a fiókkal. Lépjen a **fiók hozzáadása** elemre, válassza a **személyes Microsoft-fiók** lehetőséget, majd jelentkezzen be a felhasználónevével és jelszavával.<br>Munkahelyi vagy iskolai fiókjához a szervezet eldönti, hogy engedélyezi-e az egykattintásos értesítéseket.

### <a name="notifications-for-other-accounts"></a>Más fiókok értesítései

**K**: az értesítések nem Microsoft-fiókokhoz működnek?

**A**: nem, az értesítések csak a Microsoft-fiókokkal és a Azure Active Directory fiókokkal működnek. Ha a munkahelyi vagy iskolai Azure AD-fiókokat használ, ki tudják kapcsolni ezt a funkciót.

### <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

**K**: új eszköz van, vagy visszaállítottam az eszközt egy biztonsági másolatból. Hogyan újra beállítani a saját fiókjaikat a hitelesítő szolgáltatásban?

**A**: ha bekapcsolta a **Felhőbeli biztonsági mentést** a régi eszközén, a régi biztonsági mentés használatával helyreállíthatja a fiókja hitelesítő adatait az új iOS-vagy Android-eszközön. További információért lásd a [fiók hitelesítő adatainak biztonsági mentése és helyreállítása a hitelesítő használatával](user-help-auth-app-backup-recovery.md) című cikket.

### <a name="lost-device"></a>Elveszett eszköz

**K**: elvesztettem az eszközt, vagy Áthelyeztem egy új eszközre. Hogyan ügyeljen arra, hogy az értesítések ne folytassanak a régi eszközön?

Válasz **: a** hitelesítő hozzáadása az új eszközhöz nem távolítja el automatikusan az alkalmazást a régi eszközről. Még nem elég a régi eszközről törölni az alkalmazást. Törölnie kell az alkalmazást a régi eszközről, és tájékoztatnia kell a Microsoftot vagy a céget a régi eszköz regisztrációjának törléséről.<ul><li>**Az alkalmazás eltávolítása az eszközről személyes Microsoft-fiók használatával.** Lépjen a [fiók biztonsága](https://account.microsoft.com/security)oldal kétlépéses ellenőrzés területére   , és válassza ki a régi eszköz ellenőrzésének kikapcsolását.</li><li>**Alkalmazás eltávolítása az eszközről munkahelyi vagy iskolai Microsoft-fiók használatával.** A régi eszköz ellenőrzésének kikapcsolásához lépjen a [MyApps-oldal](https://myapps.microsoft.com/) vagy a szervezet egyéni portáljának kétlépéses ellenőrzési területére.</li></ul>

### <a name="remove-account-from-app"></a>Fiók eltávolítása az alkalmazásból

**K**: Hogyan eltávolít egy fiókot az alkalmazásból?

**A**: koppintson arra a fiókra, amelyet el szeretne távolítani az alkalmazásból, hogy megtekintse a fiók teljes képernyőjét. A fiók **eltávolítása** elemre koppintva távolítsa el a fiókot az alkalmazásból.<br>Ha van olyan eszköz, amely regisztrálva van a szervezetében, szükség lehet egy további lépésre a fiók eltávolításához. Ezeken az eszközökön a hitelesítő automatikusan eszköz-rendszergazdaként lesz regisztrálva. Ha teljesen el szeretné távolítani az alkalmazást, először törölnie kell az alkalmazás regisztrációját az alkalmazás beállításaiban.

### <a name="too-many-permissions"></a>Túl sok engedély

**K**: Miért kéri az alkalmazás a sok engedélyt?

**A**: itt látható a megadható engedélyek teljes listája, valamint az alkalmazás által használt alkalmazások. A megjelenő konkrét engedélyek a telefon típusától függenek.<ul><li>**Hely**. Előfordulhat, hogy a szervezete szeretné tudni a tartózkodási helyét, mielőtt engedélyezi bizonyos erőforrások elérését. Az alkalmazás csak abban az esetben fogja kérni ezt az engedélyt, ha a szervezete rendelkezik egy helyet igénylő házirenddel.</li><li>**Használjon biometrikus hardvert.** Néhány munkahelyi és iskolai fiókhoz szükség van egy további PIN-kódra, amikor igazolja az identitását. Az alkalmazáshoz a PIN-kód megadása helyett a biometrikus vagy az Arcfelismerés használatára van szükség.</li><li>**Kamera.** A QR-kódok beolvasására használatos munkahelyi, iskolai vagy nem Microsoft-fiók hozzáadásakor.</li><li>**Névjegyek és telefonszámok.** Az alkalmazásnak ezt az engedélyt kell adnia a munkahelyi vagy iskolai Microsoft-fiókok keresésére a telefonján, és fel kell vennie azokat az alkalmazásba.</li><li>**SMS.** Annak ellenőrzése, hogy a telefonszáma megegyezik-e a rekordban szereplő számmal, amikor első alkalommal jelentkezik be a személyes Microsoft-fiók. Szöveges üzenetet küldünk arra a telefonra, amelyen az 6-8 számjegyű ellenőrző kódot tartalmazó alkalmazást telepítette. Nem kell megkeresnie ezt a kódot, és nem kell megadnia, mert a hitelesítő automatikusan megkeresi a szöveges üzenetben.</li><li>**Más alkalmazások rajzolása.** Az Ön személyazonosságát igazoló értesítés a többi futó alkalmazáson is megjelenik.</li><li>**Adatok fogadása az internetről.** Ez az engedély szükséges az értesítések küldéséhez.</li><li>**A telefon alvó állapotból való letiltásának megakadályozása.** Ha regisztrálja az eszközt a munkahelyén, a szervezete megváltoztathatja a szabályzatot a telefonon.</li><li>**A vibráció vezérlése.** Megadhatja, hogy szeretne-e vibrációt kapni, amikor értesítést kap a személyazonosságának ellenőrzéséhez.</li><li>**Használjon ujjlenyomat-hardvert.** Néhány munkahelyi és iskolai fiókhoz szükség van egy további PIN-kódra, amikor igazolja az identitását. A folyamat megkönnyítése érdekében lehetővé tesszük az ujjlenyomat használatát a PIN-kód megadása helyett.</li><li> **Hálózati kapcsolatok megtekintése.** Microsoft-fiók hozzáadásakor az alkalmazás hálózati/internetkapcsolatot igényel.</li><li>**A tároló tartalmának beolvasása**. Ezt az engedélyt csak akkor használja a rendszer, ha technikai problémát tesz elérhetővé az alkalmazás beállításain keresztül. A rendszer a tárolóból származó néhány információt gyűjt a probléma diagnosztizálásához.</li><li>**Teljes hálózati hozzáférés.** Ez az engedély szükséges ahhoz, hogy értesítéseket küldjön a személyazonosságának ellenőrzéséhez.</li><li>**Futtatás indításkor.** Ha újraindítja a telefont, ez az engedély biztosítja, hogy továbbra is megkapja az értesítéseket a személyazonosságának ellenőrzéséhez.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Kérelmek jóváhagyása feloldás nélkül

**K**: miért teszi lehetővé a hitelesítő a kérések jóváhagyását az eszköz zárolásának feloldása nélkül?

**A**: nem kell feloldania az eszköz zárolását az ellenőrzési kérések jóváhagyása érdekében, mert mindössze annyit kell bizonyítania, hogy a telefonja Önnel rendelkezik. A kétlépéses ellenőrzéshez két dolgot kell bizonyítani – egy dolgot, és egy dolgot. Az a dolog, amit tud, a jelszava. A telefonja (a hitelesítő beállítása és a többtényezős hitelesítési igazolásként való regisztrálása). Ezért a telefon és a kérelem jóváhagyása megfelel a második hitelesítési tényező feltételeinek.

### <a name="activity-notifications"></a>Tevékenységek értesítései

**K**: Miért kapok értesítést a fiókommal kapcsolatos tevékenységről?

**A**: a rendszer azonnal elküldi a tevékenységgel kapcsolatos értesítéseket a hitelesítő számára, ha a személyes Microsoft-fiókjainak változása történik, és így továbbra is biztonságosabbá teszi a biztonságot. Ezeket az értesítéseket korábban csak e-mailben és SMS-ben küldtünk el. További információ ezekről a tevékenységi értesítésekről: [Mi történik, ha szokatlan bejelentkezés van a fiókjába](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in). Ha meg szeretné változtatni, hogy hol kapja meg az értesítéseket, jelentkezzen be, ahol kapcsolatba léphet a fiókja [nem kritikus fiókkal kapcsolatos riasztások](https://account.live.com/SecurityNotifications/Update) oldalával.

### <a name="one-time-passcodes"></a>Egyszeri jelszó

**K**: a saját egyszeri jelszava nem működik. Mit tegyek?

**A**: Ellenőrizze, hogy az eszközön megadott dátum és idő megfelelő-e, és hogy az automatikusan szinkronizálva van-e. Ha a dátum és az idő helytelen, vagy nincs szinkronban, a kód nem fog működni.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**K**: a Windows 10 Mobile operációs rendszer a 2019 decemberében elavult. A Windows Mobile operációs rendszerek Microsoft Authenticator is elavulttá válik?

**A**: az összes Windows Mobile operációs rendszer hitelesítő adatai a 2020. február 28. után nem lesznek támogatottak. A felhasználók nem jogosultak arra, hogy a fenti dátum után új frissítéseket kapjanak az alkalmazásnak. Február 28. után a 2020-es Microsoft-szolgáltatások, amelyek jelenleg támogatják a Microsoft Authenticator összes Windows Mobile operációs rendszeren történő hitelesítését, megkezdik a támogatásuk kivonását. A Microsoft-szolgáltatásokban való hitelesítéshez határozottan javasoljuk, hogy minden felhasználó számára a jelen dátum előtt váltson át egy másik hitelesítési mechanizmusra.

### <a name="default-mail-app"></a>Alapértelmezett levelezési alkalmazás

**K**: a munkahelyi vagy iskolai fiókba való bejelentkezéskor az iOS-hez készült alapértelmezett e-mail alkalmazás használatával a hitelesítő kéri a biztonsági ellenőrzési információk megadását. Miután megtörtént az információ megadása és a posta alkalmazásba való visszatérés, hibaüzenetet kapok. Mit tehetek?

**A**: ez valószínűleg azért fordul elő, mert a bejelentkezés és a levelező alkalmazás két különböző alkalmazásban fordul elő, így a kezdeti háttér-bejelentkezési folyamat leáll, és sikertelen lesz. Ha szeretné kijavítani ezt a megoldást, javasoljuk, hogy a képernyő jobb alsó sarkában lévő **Safari** ikonra kattintva jelentkezzen be a levelező alkalmazásba. A Safariba való áthelyezéssel a teljes bejelentkezési folyamat egyetlen alkalmazásban történik, amely lehetővé teszi, hogy sikeresen bejelentkezzen az alkalmazásba.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**K**: Miért kapok problémát az Apple Watch a watchOS 7 rendszeren?

**A**: probléma van az értesítések jóváhagyásával a watchOS 7 rendszeren, és az Apple-szel dolgozunk, hogy megszerezze ezt a hibát. Addig is a Microsoft Authenticator watchOS alkalmazást igénylő értesítéseket a telefonon jóvá kell hagyni.

### <a name="apple-watch-doesnt-show-accounts"></a>Az Apple Watch nem jeleníti meg a fiókokat

**K**: Miért nem jelenik meg az összes fiókom, amikor megnyitom a hitelesítő szolgáltatást az Apple watchban?

**A**: a hitelesítő csak a Microsoft személyes vagy iskolai vagy munkahelyi fiókjait támogatja leküldéses értesítésekkel az Apple Watch Companion alkalmazásban. Más fiókok, például a Google vagy a Facebook esetében az ellenőrző kódok megtekintéséhez meg kell nyitnia a hitelesítő alkalmazást a telefonján.

### <a name="apple-watch-notifications"></a>Apple Watch-értesítések

**K**: Miért nem tudom jóváhagyni vagy megtagadni az Apple Watch-értesítéseket?

**A**: először is győződjön meg arról, hogy frissítette a hitelesítő verzió 6.0.0 vagy újabb verzióját az iPhone-on. Ezután nyissa meg az Apple Watch Microsoft Authenticator Companion alkalmazást, és keresse meg azokat a fiókokat, amelyeken **be van állítva a set up** gomb. Fejezze be a telepítési folyamatot a fiókok értesítéseinek jóváhagyásához.

### <a name="apple-watch-communication-error"></a>Apple Watch kommunikációs hiba

**K**: kommunikációs hiba történt az Apple Watch és a telefon között. Mire használhatom a hibakeresést?

**A**: Ez a hiba akkor fordul elő, ha a figyelési képernyő alvó állapotba kerül, mielőtt befejeződik a telefonos kommunikáció.<br><b>Ha a hiba a telepítés során történik:</b><br>Próbálja meg újra futtatni a telepítőt, és győződjön meg róla, hogy ébren tartja az órát, amíg a folyamat el nem fejeződik. Egy időben nyissa meg az alkalmazást a telefonján, és válaszoljon a megjelenő üzenetekre.<br>Ha a telefonja és a figyelése még mindig nem kommunikál, a következő műveleteket hajthatja meg:<ol><li>Kényszerítse ki a Microsoft Authenticator telefonos alkalmazást, majd nyissa meg újra az iPhone-on.</li><li>Kényszerítse ki a Companion alkalmazást az Apple Watch-on.<ol><li> A Microsoft Authenticator Companion alkalmazás megnyitása az óráján</li><li>Tartsa lenyomva az oldalsó gombot, amíg meg nem jelenik a **Leállítás** képernyő.</li><li>Szabadítsa fel az oldalsó gombot, és tartsa lenyomva a digitális koronát az aktív alkalmazás bezárásának kényszerítéséhez.</li></ol></li><li>Kapcsolja ki a Bluetooth-t és a Wi-Fit mind a telefonján, mind az óráján, majd kapcsolja be újra.</li><li>Indítsa újra az iPhone-t és az óráját.</li></ol><b>Ha a hiba akkor jelentkezik, amikor jóváhagy egy értesítést:</b><br>Amikor legközelebb megpróbálkozik az Apple Watch-értesítés jóváhagyásával, a képernyőn ébren maradhat, amíg a kérés be nem fejeződik, és a hang azt jelzi, hogy sikeres volt.

### <a name="apple-watch-companion-app-not-syncing"></a>Az Apple Watch Companion-alkalmazás nem szinkronizál

**K**: Miért nem a Microsoft Authenticator Companion-alkalmazás az Apple Watch-szinkronizálásra vagy a megtekintésre?

**A**: Ha az alkalmazás nem jelenik meg a saját óráján, próbálja meg a következő műveleteket: <ol><li>Győződjön meg arról, hogy az óra watchOS 4,0-es vagy újabb verzióját futtatja.</li><li>A Watch szinkronizálása újra.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Az Apple Watch Companion-alkalmazás összeomlott

**K**: az Apple Watch Companion-alkalmazás összeomlott. Elküldhetem az összeomlási naplókat, hogy meg lehessen vizsgálni?

**A**: először is meg kell győződnie arról, hogy Ön úgy döntött, hogy megosztja az elemzést velünk. Ha Ön Testflighthoz-felhasználó, már regisztrálva van. Ellenkező esetben lépjen a **beállítások > adatvédelem > elemzés** lehetőségre, és válassza az **iPhone megosztása & az elemzés** és a **megosztás az alkalmazások fejlesztői** lehetőségeivel lehetőséget.<br>A regisztrációt követően megpróbálhatja reprodukálni az összeomlást, hogy a rendszer automatikusan elküldje az összeomlási naplókat a vizsgálathoz. Ha azonban nem tudja reprodukálni az összeomlást, manuálisan is átmásolhatja a naplófájlokat, és elküldheti azokat nekünk.<ol><li>Nyissa meg a Watch alkalmazást a telefonján, lépjen a **beállítások > általános** elemre, majd kattintson az **elemzés megtekintése** elemre.</li><li>Keresse meg a megfelelő összeomlást a **settings > Privacy > analytics > Analytics-adatok** területen, majd manuálisan másolja a teljes szöveget.</li><li>Nyissa meg a hitelesítő eszközt a telefonján, és illessze be a vágólapra a bemásolt szöveget a  **probléma elhárítása** című szakaszban leírtak szerint **?** a  **visszajelzés küldése** lapon. </li></ol>

## <a name="autofill-with-authenticator"></a>Automatikus kitöltés a hitelesítő használatával

**K**: mi a hitelesítő automatikus kitöltés?

**A**: a hitelesítő alkalmazás mostantól biztonságosan tárolja és kitölti a jelszavakat a telefonon felkeresett alkalmazásokon és webhelyeken. Az automatikus kitöltés eszközzel szinkronizálhatja és kihasználhatja az iOS-és Android-eszközökön található jelszavait. Miután beállította a hitelesítő alkalmazást automatikus kitöltési szolgáltatóként a telefonján, a jelszó mentésével megadhatja a jelszavát, amikor beírja őket egy webhelyre vagy egy alkalmazás bejelentkezési oldalára. A jelszavak a [személyes Microsoft-fiók](https://account.microsoft.com/account) részeként lesznek mentve, és akkor is elérhetők, amikor bejelentkezik a Microsoft Edge-be a személyes Microsoft-fiók.

**K**: milyen adatokat tud hitelesíteni a hitelesítő?

**A**: a hitelesítő kihasználhatja a felhasználóneveket és a jelszavakat a telefonon felkeresett webhelyeken és alkalmazásokban.

**K**: Hogyan bekapcsolja a jelszó automatikus kitöltését a hitelesítő eszközön a telefonon?

**A**: kövesse az alábbi lépéseket:

1. Nyissa meg a hitelesítő alkalmazást.
1. A hitelesítő **jelszavak** lapján válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, és jelentkezzen be a [Microsoft-fiók](https://account.microsoft.com/account)használatával. Ez a funkció jelenleg csak a Microsoft-fiókokat támogatja, és még nem támogatja a munkahelyi vagy iskolai fiókokat.

**K**: hogyan a hitelesítő az alapértelmezett automatikus kitöltés szolgáltatót a telefonon?

**A**: kövesse az alábbi lépéseket:

1. Nyissa meg a hitelesítő alkalmazást.
1. Az alkalmazás **jelszavak** lapján válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, és jelentkezzen be a [Microsoft-fiók](https://account.microsoft.com/account)használatával.
1. Tegye a következők egyikét:

   - Az iOS-es **Beállítások** részen válassza ki az automatikus kitöltés beállítás **bekapcsolása** beállítást az automatikus kitöltés beállításai szakaszban, hogy megtudja, hogyan állíthatja be a hitelesítő alapértelmezett automatikus kitöltés-szolgáltatóként.
   - Az Androidban a **Beállítások** területen válassza az automatikus kitöltés- **szolgáltatóként** lehetőséget az automatikus kitöltés beállításai szakaszban.

**K**: mi történik, ha az **Automatikus kitöltés** nem érhető el a beállítások között?

**A**: Ha az automatikus kitöltés nem érhető el a hitelesítő számára, annak oka az lehet, hogy az automatikus kitöltés még nem engedélyezett a szervezet vagy a fiók típusa számára. Ezt a funkciót olyan eszközön használhatja, amelyen nincs hozzáadva a munkahelyi vagy iskolai fiókja. Ha többet szeretne megtudni arról, hogyan engedélyezhető az automatikus kitöltés a szervezet számára, olvassa el [az automatikus kitöltés a rendszergazdák](#autofill-for-it-admins)számára című témakört.

**K**: hogyan a jelszavak szinkronizálásának leállítása?

**A**: a hitelesítő alkalmazásban a jelszavak szinkronizálásának leállításához nyissa meg a **Beállítások** automatikus kiegészítési  >  **Beállítások**  >  **szinkronizálása fiókot**. A következő képernyőn kiválaszthatja a **szinkronizálás leállítása és az összes automatikus** kitöltési érték eltávolítása lehetőséget. Ezzel eltávolítja a jelszavakat és egyéb automatikus kitöltési adatok az eszközről. Az automatikus kitöltés nem befolyásolja a többtényezős hitelesítést.

**K**: Hogyan védi a jelszavakat a hitelesítő alkalmazás?

**A**: a hitelesítő alkalmazás már magas szintű biztonságot nyújt a többtényezős hitelesítéshez és a fiókok kezeléséhez, és ugyanez a magas biztonsági sáv is ki van bővítve a jelszavak kezelésére.

- A **hitelesítő alkalmazásnak erős hitelesítésre van szüksége**: a hitelesítésbe való bejelentkezéshez második tényező szükséges. Ez azt jelenti, hogy a hitelesítő alkalmazás jelszavai akkor is védve vannak, ha valaki rendelkezik a Microsoft-fiók jelszavával.
- Az adatok automatikus kitöltése a **biometria és a PIN-kód használatával történik**: az alkalmazás vagy hely jelszavának automatikus kitöltéséhez a hitelesítő megköveteli a biometrikus vagy az eszköz PIN-kódját. Ez segít az extra biztonság hozzáadásában, így még akkor is, ha valaki más fér hozzá az eszközhöz, nem tudja kitölteni vagy megtekinteni a jelszavát, mert nem tudják megadni a biometrikus vagy az eszköz PIN-kódját. Emellett a felhasználók nem tudják megnyitni a jelszavak lapot, hacsak nem biztosítanak biometrikus vagy PIN-kódot, még akkor sem, ha kikapcsolják az alkalmazás beállításaiban az alkalmazások zárolását.
- **Titkosított jelszavak az eszközön: az** eszközön lévő jelszavak titkosítva vannak, és a titkosítási/visszafejtési kulcsok soha nem tárolódnak, és szükség esetén mindig létrejönnek. A jelszavak csak akkor lettek visszafejtve, ha a felhasználó az automatikus kitöltés vagy a felhasználó szeretné megtekinteni a jelszót, és mindkettő biometrikus vagy PIN-kódot igényel.
- **Felhő-és hálózati biztonság**: a felhőben lévő jelszavak csak akkor lesznek titkosítva és visszafejtve, amikor elérik az eszközt. A jelszavakat egy SSL-védelemmel ellátott HTTPS-kapcsolaton keresztül szinkronizálja a rendszer, ami segít megakadályozni, hogy a támadók lehallgatják a bizalmas adatokat a szinkronizálás során. Azt is javasoljuk, hogy ellenőrizze, hogy a hálózaton keresztül szinkronizált adatok a kriptográfiai kivonatos függvények (pontosabban a kivonatoló alapú üzenet-hitelesítési kód) használatával legyenek szinkronizálva.

## <a name="autofill-for-it-admins"></a>Automatikus kitöltés a rendszergazdák számára

**K**: az alkalmazottak vagy a tanulók a jelszó automatikus kitöltését használják a hitelesítő alkalmazásban?

Válasz **: igen**, a [személyes Microsoft-fiókjainak](https://go.microsoft.com/fwlink/?linkid=2144423) automatikus kitöltése mostantól a legtöbb vállalati felhasználó számára is működik, még akkor is, ha egy munkahelyi vagy iskolai fiókot ad hozzá a hitelesítő alkalmazáshoz. Az űrlap kitöltésével engedélyezheti vagy megtagadhatja az automatikus kitöltés használatát a szervezet számára, és [elküldheti azt a hitelesítő csapatnak](https://aka.ms/ConfigureAutofillInAuthenticator). Az automatikus kitöltés jelenleg nem érhető el munkahelyi vagy iskolai fiókokhoz.

**K**: a felhasználók munkahelyi vagy iskolai fiókjának jelszava automatikusan szinkronizálva lesz?

**A**: nem. A jelszó automatikus kitöltése nem fogja szinkronizálni a felhasználók munkahelyi vagy iskolai fiókjának jelszavát. Ha a felhasználók egy webhelyet vagy alkalmazást látogatnak meg, a hitelesítő felajánlja a webhely vagy alkalmazás jelszavának mentését, és a rendszer csak akkor menti a jelszót, ha a felhasználó kiválasztja.
  
**K**: a saját szervezetem egyes felhasználói engedélyezési csak az automatikus kitöltés?

**A**: nem. A vállalatok jelenleg csak az összes felhasználó számára engedélyezhetik a jelszavak automatikus kitöltését.

**K**: mit tegyek, ha az alkalmazottak vagy a tanulók több munkahelyi vagy iskolai fiókkal rendelkeznek? Az alkalmazottak például több vállalattól vagy iskolától származó fiókkal rendelkeznek a Microsoft Authenticatorban.

**A**: a hitelesítő alkalmazásban hozzáadott összes vállalatot vagy iskolát engedélyezni kell – az alkalmazás tulajdonosának a hitelesítő szolgáltatásban történő automatikus kitöltéséhez. A korlátozás alól kivételt képez, ha az alkalmazott vagy a tanuló a munkahelyi vagy iskolai fiókját hozzáadja a Microsoft felhőalapú multi-Factor Authentication szolgáltatáshoz [külső vagy harmadik féltől származó fiókkal](user-help-auth-app-add-non-ms-account.md).

## <a name="next-steps"></a>Következő lépések

- Ha nem sikerül beszereznie a személyes Microsoft-fiók ellenőrzési kódját, tekintse meg az **ellenőrző kód problémáinak elhárítása** című szakaszt a [Microsoft-fiók biztonsági adatok & ellenőrző kódok](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) című cikkben.

- Ha további információra van szüksége a kétlépéses ellenőrzésről, tekintse [meg a fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című témakört.

- Ha további információra van szüksége a biztonsági adatokról, tekintse meg a [biztonsági adatok (előzetes verzió) áttekintése](./security-info-setup-signin.md) című témakört.

- Ha nem válaszolt erre a kérdésre, szeretnénk meghallgatni Önt. Lépjen a [Microsoft Authenticator alkalmazás fórumára](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) , ahol felteheti a kérdéseit, és segítséget kérhet a Közösségtől, vagy hozzászólást hagyhat ezen az oldalon.
