---
title: A fiók Kéttényezős hitelesítésével kapcsolatos gyakori problémák – Azure AD
description: Megoldások a leggyakoribb kétfaktoros ellenőrzési problémákhoz, valamint a munkahelyi vagy iskolai fiókhoz.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0cb5e3463eda5d77bbbdefe223954105fd125c71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98033982"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>A kétfaktoros hitelesítéssel és a munkahelyi vagy iskolai fiókkal kapcsolatos gyakori problémák

Vannak olyan gyakori kétfaktoros ellenőrzési problémák, amelyek gyakrabban fordulnak elő, mint bármelyikünk. Ezt a cikket összeállítottuk a leggyakoribb problémák javításait ismertető cikkben.

A Azure Active Directory (Azure AD) szervezet bekapcsolhatja a kétfaktoros ellenőrzést a fiókjához. Ha a kétfaktoros ellenőrzés be van kapcsolva, a fiókja bejelentkezéséhez a következő adatmennyiségek kombinációja szükséges:

- Felhasználói név
- Az Ön jelszava
- Mobileszköz vagy telefon

A kétfaktoros ellenőrzés sokkal biztonságosabb, mint a jelszó, mivel a kétfaktoros ellenőrzéshez szükség van egy olyan elemre, _amelyhez Ön_ _is tartozik_. Egyetlen hacker sem rendelkezik a fizikai telefonnal.

>[!Important]
>Ha Ön rendszergazda, további információt talál arról, hogyan állíthatja be és kezelheti Azure AD-környezetét az [Azure ad dokumentációjában](../index.yml).

Ez a tartalom segíthet a munkahelyi vagy iskolai fiókjában, amely a szervezet által megadott fiók (például dritan@contoso.com ). Ha problémái vannak a kétfaktoros ellenőrzéssel egy személyes Microsoft-fiókon, amely egy saját maga által beállított fiók (például danielle@outlook.com ), tekintse [meg a kétfaktoros ellenőrzés bekapcsolását a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nekem nincs mobileszközom

Ez történik. A mobileszköz otthon maradt, és most már nem használhatja a telefont annak ellenőrzéséhez, hogy ki vagy. Lehet, hogy korábban már hozzáadott egy alternatív módszert a fiókjába való bejelentkezéshez, például az irodai telefonján keresztül. Ha igen, ezt az alternatív módszert használhatja most. Ha soha nem adott meg alternatív ellenőrzési módszert, segítségért forduljon a szervezet ügyfélszolgálatához.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Bejelentkezés munkahelyi vagy iskolai fiókba más ellenőrzési módszer használatával

1. Jelentkezzen be a fiókjába, de a **kétfaktoros ellenőrző** lapon válassza a **bejelentkezés másik módon** hivatkozását.

    ![Bejelentkezés ellenőrzési módszerének módosítása](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Ha nem látja a **Bejelentkezés más módon** hivatkozást, az azt jelenti, hogy nem állított be semmilyen más ellenőrzési módszert. Kérje meg a rendszergazdát, hogy jelentkezzen be a fiókjába.

2. Válassza ki az alternatív ellenőrzési módszert, és folytassa a kétfaktoros ellenőrzési folyamattal.

## <a name="i-cant-turn-two-factor-verification-off"></a>Nem kapcsolhatom ki a kétfaktoros ellenőrzést

- Ha kétfaktoros ellenőrzést használ egy Microsoft-szolgáltatáshoz tartozó személyes fiókkal, például a alain@outlook.com funkciót, be [-és kikapcsolhatja](https://account.live.com/proofs/Manage).

- Ha kétfaktoros ellenőrzést használ munkahelyi vagy iskolai fiókjával, az valószínűleg azt jelenti, hogy a szervezet úgy döntött, hogy ezt a hozzáadott biztonsági funkciót kell használnia. Nincs mód arra, hogy egyénileg kikapcsolja.

Ha nem tudja kikapcsolni a kétfaktoros ellenőrzést, akkor a szervezeti szinten alkalmazott biztonsági beállítások miatt is előfordulhat. További információ a biztonsági alapbeállításokról: [Mi a biztonsági alapértékek?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>Az eszköz elvesztése vagy ellopása megszakadt

Ha elvesztette vagy használta a mobileszköz ellopását, a következő műveletek bármelyikét elvégezheti:

- Jelentkezzen be egy másik módszer használatával.
- Kérje meg szervezete ügyfélszolgálatát, hogy törölje a beállításait.

Nyomatékosan javasoljuk, hogy a céges ügyfélszolgálat tudja, hogy a telefon elvesztése vagy ellopása megszakadt-e. Az ügyfélszolgálat megteheti a megfelelő frissítéseket a fiókjában. A beállítások törlése után a rendszer felszólítja, hogy regisztráljon a [kétfaktoros ellenőrzésre](multi-factor-authentication-end-user-first-time.md) a következő bejelentkezés alkalmával.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Nem fogadom el a mobileszközön eljuttatott ellenőrző kódot

Az ellenőrző kód nem érkezik gyakori probléma. A probléma általában a mobileszköz és a hozzá tartozó beállításokhoz kapcsolódik. Íme néhány művelet, amelyet kipróbálhat.

Próbálja ki! | Útmutató információk
--------- | ------------
A Microsoft hitelesítő alkalmazás vagy az ellenőrző kódok használata | A bejelentkezéskor a "elérte a határértéket az ellenőrző hívások esetében" vagy "a szöveges ellenőrző kódok korlátozása" hibaüzenetet kapta. <br/><br/>A Microsoft egy rövid időn belül korlátozza az ugyanazon felhasználó által végrehajtott ismétlődő hitelesítési kísérleteket. Ez a korlátozás nem vonatkozik a Microsoft Authenticatorra vagy az ellenőrző kódra. Ha elérte ezeket a korlátokat, használhatja a hitelesítő alkalmazást, az ellenőrző kódot, vagy próbáljon meg néhány perc múlva újra bejelentkezni. <br/><br/> "Sajnáljuk, nem sikerül ellenőrizni a fiókját" hibaüzenet jelenik meg a bejelentkezés során. <br/><br/> A Microsoft a sikertelen hang-vagy SMS-hitelesítési kísérletek miatt korlátozhatja vagy letilthatja a hang-vagy SMS-hitelesítési kísérleteket, amelyeket ugyanaz a felhasználó, a telefonszám vagy a szervezet hajt végre. Ha ezt a hibát tapasztalja, kipróbálhat egy másik módszert, például a hitelesítő alkalmazást vagy az ellenőrző kódot, vagy elérheti a rendszergazda segítségét.
Mobil eszköz újraindítása | Előfordulhat, hogy az eszköz csak a frissítésre van szüksége. Az eszköz újraindításakor az összes háttérbeli folyamat és szolgáltatás véget ér. Az újraindítás az eszköz alapvető összetevőit is leállítja. Az eszköz újraindításakor bármely szolgáltatás vagy összetevő frissül.
Ellenőrizze, hogy helyesek-e a biztonsági adatok | Győződjön meg arról, hogy a biztonsági ellenőrzési módszer adatai pontosak, különösen a telefonszámok. Ha helytelen telefonszámot helyez üzembe, az összes riasztás nem megfelelő számú. Szerencsére a felhasználó nem fog tudni bármit megtenni a riasztásokkal, de nem segít bejelentkezni a fiókjába. Az adatok helyességének biztosításához tekintse meg a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikket.
Ellenőrizze, hogy be vannak-e kapcsolva az értesítések | Győződjön meg arról, hogy a mobileszköz értesítések bekapcsolva. Győződjön meg arról, hogy a következő értesítési módok engedélyezettek: <br/><br/> &bull; Telefonhívások <br/> &bull; A hitelesítési alkalmazás <br/> &bull; Szöveges üzenetküldési alkalmazás <br/><br/> Győződjön meg arról, hogy ezek a módok olyan riasztást hoznak létre, amely _látható_ az eszközön.
Győződjön meg arról, hogy rendelkezik egy eszköz-és internetkapcsolattal | Győződjön meg arról, hogy a telefonhívások és a szöveges üzenetek bekerülnek a mobileszközön. Ismerőse van, és SMS-üzenetet küld Önnek, hogy mindkét fél megkapja. Ha nem kapja meg a hívást vagy a szöveget, először ellenőrizze, hogy a mobileszköz be van-e kapcsolva. Ha az eszköz be van kapcsolva, de még nem fogadja el a hívást vagy a szöveget, valószínűleg probléma van a hálózattal. A szolgáltatóval kell kommunikálnia. Ha gyakran vannak jelekkel kapcsolatos problémák, javasoljuk, hogy telepítse és használja a [Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md) a mobileszközön. A hitelesítő alkalmazás véletlenszerű biztonsági kódokat tud létrehozni a bejelentkezéshez, és nem igényel semmilyen cellahivatkozást vagy internetkapcsolatot.
Kikapcsolás ne zavarja | Győződjön meg arról, hogy nincs bekapcsolva a mobileszköz **ne zavarja** a funkcióját. Ha ez a funkció be van kapcsolva, az értesítések nem kaphatnak riasztást a mobileszközön. A szolgáltatás kikapcsolásával kapcsolatos útmutatásért tekintse meg a mobileszköz kézikönyvét.
Telefonszámok feloldásának feloldása | A Egyesült Államok a Microsoft hanghívásai a következő számokból származnak: + 1 (866) 539 4191, + 1 (855) 330 8653 és + 1 (877) 668 6536.
Akkumulátorral kapcsolatos beállítások keresése | Ha úgy állítja be az akkumulátor-optimalizálást, hogy kevesebb gyakran használt alkalmazás maradjon aktív a háttérben, az értesítési rendszer valószínűleg érintett. Próbálja meg kikapcsolni az akkumulátor-optimalizálást mind a hitelesítési alkalmazás, mind az üzenetkezelési alkalmazás esetében. Ezután próbáljon újra bejelentkezni a fiókjába.
Külső gyártótól származó biztonsági alkalmazások letiltása | Néhány telefonos biztonsági alkalmazás blokkolja a szöveges üzeneteket és a telefonhívásokat a bosszantó ismeretlen hívóktól. Előfordulhat, hogy a biztonsági alkalmazások nem kapják meg az ellenőrző kódot. Próbálja meg letiltani a harmadik féltől származó biztonsági alkalmazásokat a telefonján, majd kérje meg, hogy küldjön egy másik ellenőrző kódot.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Nem kapok Rákérdezés a második ellenőrzési információra

A felhasználónevével és jelszavával jelentkezhet be a munkahelyi vagy iskolai fiókjába. Ezután meg kell adnia a további biztonsági ellenőrzési információkat. Ha a rendszer nem kéri, lehet, hogy még nem állította be az eszközt. A mobileszköz beállítását úgy kell beállítani, hogy az adott további biztonsági ellenőrzési módszerrel működjön.

Lehet, hogy még nem állította be az eszközt. A mobileszköz beállítását úgy kell beállítani, hogy működjön az adott további biztonsági ellenőrzési módszerrel. Ahhoz, hogy a mobileszköz az ellenőrzési módszernek megfelelően elérhető legyen, tekintse meg a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)című témakört. Ha tudja, hogy még nem állította be az eszközt vagy a fiókját, kövesse a [saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikket.

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Új telefonszámmal rendelkezem, és szeretném felvenni

Ha van új telefonszáma, frissítenie kell a biztonsági ellenőrzési módszer részleteit. Ez lehetővé teszi, hogy az ellenőrzési kérések a megfelelő helyre lépjenek. Az ellenőrzési módszer frissítéséhez kövesse a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) című cikk a **telefonszám hozzáadása vagy módosítása** című szakaszának lépéseit.

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Új mobileszközön vagyok, és szeretném felvenni

Ha van új mobileszköz, be kell állítania, hogy működjön a kétfaktoros ellenőrzéssel. Ez egy több lépésből álló megoldás:

1. Állítsa be az eszközt úgy, hogy az a fiók [beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című szakasz lépéseit követve működjön együtt a fiókjával.

1. Frissítse a fiókját és az eszköz adatait a **további biztonsági ellenőrzés** oldalon. A frissítés elvégzéséhez törölje a régi eszközt, és adja hozzá az újat. További információt a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben talál.

Nem kötelező lépések:

- Telepítse a Microsoft Authenticator alkalmazást a mobileszközön a [Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit követve.

- Kapcsolja be a kétfaktoros ellenőrzést a megbízható eszközökhöz a kétfaktoros [ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) című cikk a kéttényezős ellenőrzési **kérések megbízható eszközön** című részében ismertetett lépéseket követve.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Problémák léptek fel a mobil eszközön való bejelentkezés közben

Előfordulhat, hogy a mobileszköz-alapú ellenőrzési módszer (például szöveges üzenetküldés) használata még nehezebbé válik, miközben nemzetközi helyen dolgozik. Azt is megteheti, hogy a mobileszköz barangoló díjat von maga után. Ebben az esetben javasoljuk, hogy a Microsoft Authenticator alkalmazást használja a Wi-Fi gyors elérésére szolgáló lehetőséggel. A Microsoft Authenticator alkalmazás mobileszközön való beállításával kapcsolatos további információkért tekintse meg a [Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikket.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nem tudom beolvasni az alkalmazás jelszavait

Az alkalmazás jelszavai lecserélik a normál jelszót a régebbi asztali alkalmazások esetében, amelyek nem támogatják a kétfaktoros ellenőrzést. Először ellenőrizze, hogy helyesen írta-e be a jelszót. Ha ez nem oldja meg, próbálja meg létrehozni az alkalmazás új alkalmazás-jelszavát. Ehhez kövesse az alkalmazás **jelszavának létrehozása és törlése a** [kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) című cikk saját alkalmazások portálján című szakaszának lépéseit.

## <a name="i-cant-turn-off-two-factor-verification"></a>Nem tudom kikapcsolni a kétfaktoros ellenőrzést

Ha a kétfaktoros ellenőrzést használja munkahelyi vagy iskolai fiókjával (például alain@contoso.com ), az valószínűleg azt jelenti, hogy a szervezet úgy döntött, hogy ezt a hozzáadott biztonsági funkciót kell használnia. Mivel a szervezet úgy döntött, hogy ezt a funkciót kell használnia, nincs mód arra, hogy önállóan kikapcsolja. Ha azonban a kéttényezős ellenőrzést személyes fiókkal használja, például a szolgáltatás be-és kikapcsolását alain@outlook.com is lehetővé teszi. A személyes fiókok kétfaktoros ellenőrzésének vezérlésével kapcsolatos útmutatásért lásd: [kétfaktoros ellenőrzés bekapcsolása vagy kikapcsolása a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Ha nem tudja kikapcsolni a kétfaktoros ellenőrzést, akkor a szervezeti szinten alkalmazott biztonsági beállítások miatt is előfordulhat. További információ a biztonsági alapbeállításokról: [Mi a biztonsági alapértékek?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nem találok választ a problémára

Ha kipróbálta ezeket a lépéseket, de továbbra is problémákat tapasztal, forduljon a szervezet ügyfélszolgálatához segítségért.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md)

- [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)
