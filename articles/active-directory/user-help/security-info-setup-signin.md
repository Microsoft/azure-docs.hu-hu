---
title: Biztonsági információk beállítása bejelentkezési kérésből – Azure AD
description: A munkahelyi vagy iskolai fiók biztonsági adatainak beállítása, miután a rendszer rákérdez a szervezet bejelentkezési oldalára.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/07/2020
ms.author: curtand
ms.openlocfilehash: 016e0de26bf3a9513940be161cfecf6d1c2b9d02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88798499"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Biztonsági adatok beállítása bejelentkezési kérésből

Ha a munkahelyi vagy iskolai fiókjába való bejelentkezés után azonnal kéri a biztonsági adatok beállítását, kövesse az alábbi lépéseket.

Ez a kérdés csak akkor jelenik meg, ha nem állította be a szervezete által megkövetelt biztonsági adatokat. Ha korábban már beállította a biztonsági adatokat, de módosítani szeretné a módosításokat, kövesse a különböző metódus-alapú útmutató cikkeiben ismertetett lépéseket. További információ: [a biztonsági adatok hozzáadásának vagy frissítésének áttekintése](./security-info-setup-auth-app.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Biztonsági ellenőrzés és jelszó-visszaállítási hitelesítés

A biztonsági információ módszereit a kéttényezős biztonsági ellenőrzéshez és a jelszó-visszaállításhoz is használja a rendszer. Azonban nem minden metódus használható egyszerre mindkettőhöz.

| Metódus | Alkalmazási cél |
| ------ | -------- |
| Hitelesítő alkalmazás | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| SMS-ek | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Telefonbeszélgetések | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Biztonsági kulcs | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| E-mail fiók | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |
| Biztonsági kérdések | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |

## <a name="sign-in-to-your-work-or-school-account"></a>Jelentkezzen be a munkahelyi vagy iskolai fiókjába

Miután bejelentkezett a munkahelyi vagy iskolai fiókjába, megjelenik egy üzenet, amely arra kéri, hogy adjon meg további információt, mielőtt hozzáfér a fiókjához.

![További információk kérése](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Biztonsági adatok beállítása a varázsló használatával

A következő lépésekkel állíthatja be a munkahelyi vagy iskolai fiókjához tartozó biztonsági adatokat a parancssorból.

>[!Important]
>Ez csak egy példa a folyamatra. A szervezet követelményeitől függően előfordulhat, hogy a rendszergazda különböző ellenőrzési módszereket állított be, amelyeket be kell állítania a folyamat során. Ebben a példában két módszert igényelünk, a Microsoft Authenticator alkalmazást és egy mobiltelefon-számot a hitelesítési hívásokhoz vagy szöveges üzenetekhez.

1. Miután kiválasztotta a **következőt** a parancssorból, megjelenik a **fiók biztonságos létrehozása varázsló** , amely az első olyan metódust mutatja be, amelyet a rendszergazdának és a szervezetnek be kell állítania. Ebben a példában ez a Microsoft Authenticator alkalmazás.

   > [!Note]
   > Ha a Microsoft Authenticator alkalmazástól eltérő hitelesítő alkalmazást szeretne használni, válassza a **másik hitelesítő alkalmazás használata** hivatkozást.
   >
   > Ha a szervezete lehetővé teszi, hogy egy másik módszert válasszon a hitelesítő alkalmazás mellett, akkor válassza a **másik módszer csatolása** lehetőséget.

    ![Tartsa a fiókja biztonságban varázslót, amely az Auth alkalmazás letöltési oldalát jeleníti meg](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Válassza a **Letöltés most** lehetőséget, hogy letöltse és telepítse a Microsoft Authenticator alkalmazást a mobileszközön, majd válassza a **tovább** lehetőséget. További információ az alkalmazás letöltéséről és telepítéséről: [a Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md).

    ![Tartsa meg a fiókja védelmét, és jelenítse meg a fiókját a hitelesítő beállítása oldalon](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Maradjon a **fiók beállítása** oldalon, miközben beállítja a Microsoft Authenticator alkalmazást a mobileszközön.

4. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be az értesítések engedélyezését (ha a rendszer kéri), válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók** lehetőséget.

    >[!Note]
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

5. Térjen vissza a **fiók beállítása** lapra a számítógépen, majd válassza a **tovább** lehetőséget.

    Megjelenik a **QR-kód vizsgálata** lap.

    ![A QR-kód vizsgálata a hitelesítő alkalmazás használatával](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Az 5. lépésben a munkahelyi vagy iskolai fiók létrehozása után a megadott kód beolvasásával megjelentek a Microsoft Authenticator alkalmazás QR-kód olvasója, amely megjelent a mobileszközön.

    A hitelesítő alkalmazásnak sikeresen hozzá kell adnia a munkahelyi vagy iskolai fiókját anélkül, hogy további adatokat kellene megadnia. Ha azonban a QR-kód olvasója nem tudja beolvasni a kódot, akkor a **nem tudja beolvasni a QR-rendszerképet** , és manuálisan adja meg a kódot és az URL-címet a Microsoft Authenticator alkalmazásban. A kódok manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

7. Kattintson a **Tovább gombra** a **QR-kód vizsgálata** lapon a számítógépen.

    A rendszer értesítést küld a mobileszközön lévő Microsoft Authenticator alkalmazásnak, hogy tesztelje a fiókját.

    ![A fiók tesztelése a hitelesítő alkalmazással](media/security-info/securityinfo-prompt-test-app.png)

8. Hagyja jóvá az értesítést a Microsoft Authenticator alkalmazásban, majd kattintson a **tovább** gombra.

    ![Sikeres értesítés, az alkalmazás és a fiók csatlakoztatása](media/security-info/securityinfo-prompt-auth-app-success.png)

    A biztonsági adatokat a rendszer úgy frissíti, hogy a Microsoft Authenticator alkalmazás alapértelmezés szerint a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során ellenőrizze az identitást.

9. A **telefon** beállítása lapon válassza ki, hogy szöveges üzenetet vagy telefonhívást szeretne kapni, majd válassza a **tovább** lehetőséget. Ebben a példában szöveges üzeneteket használunk, ezért egy telefonszámot kell használnia egy olyan eszközhöz, amely képes szöveges üzeneteket fogadni.

    ![A telefonszám beállításának megkezdése szöveges üzenetküldéshez](media/security-info/securityinfo-prompt-text-msg.png)

    A rendszer szöveges üzenetet küld a telefonszámára. Ha telefonhívást szeretne kapni, a folyamat ugyanaz. Egy szöveges üzenet helyett egy utasításokat tartalmazó telefonhívást fog kapni.

10. Adja meg a mobileszközön küldött szöveges üzenet által megadott kódot, majd kattintson a **tovább** gombra.

    ![A fiók tesztelése a szöveges üzenettel](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Tekintse át a sikeres értesítéseket, majd kattintson a **kész** gombra.

    ![Sikeres értesítés](media/security-info/securityinfo-prompt-call-answered-success.png)

    A biztonsági adatokat a rendszer úgy frissíti, hogy a szöveges üzenetküldés biztonsági mentési módszerként való használatával igazolja az identitását a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során.

12. A **siker** lapon ellenőrizheti, hogy sikeresen beállította-e a Microsoft Authenticator alkalmazást és a telefont (SMS vagy telefonhívás) a biztonsági adatokhoz, majd válassza a **kész** lehetőséget.

    ![A varázsló sikeresen befejezve oldal](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Ha a szervezete megköveteli az alkalmazások jelszavainak használatát, a varázsló további szakasza is megjelenik, ahol beállíthatja. Ha egy harmadik, az **alkalmazás jelszavai** nevű szakaszt lát, ki kell töltenie azt, hogy el tudja végezni a varázslót. Az alkalmazás jelszavának hozzáadásával kapcsolatos lépésekért tekintse meg a jelen cikk az [alkalmazás jelszavainak kezelése](#manage-your-app-passwords) című szakaszát.

### <a name="manage-your-app-passwords"></a>Az alkalmazás jelszavának kezelése

Bizonyos alkalmazások, például az Outlook 2010, nem támogatják a kétlépéses ellenőrzést. Ez a támogatás hiánya azt jelenti, hogy ha kétlépéses ellenőrzést használ a szervezetében, az alkalmazás nem fog működni. A probléma megkerüléséhez létrehozhat egy automatikusan létrehozott jelszót, amelyet az egyes nem böngésző alkalmazásokhoz használhat, a normál jelszótól eltérő módon.

>[!Note]
>Ha nem látja ezt a lehetőséget a varázslóban, az azt jelenti, hogy a rendszergazda nem állította be. Ha ez nincs beállítva, de tudnia kell, hogy az alkalmazás jelszavait kell használnia, kövesse az [alkalmazás jelszavainak beállítása a biztonsági adatokból lapon](security-info-app-passwords.md)található lépéseket.

Az alkalmazások jelszavainak használatakor fontos megjegyezni:

- Az alkalmazás jelszavai automatikusan létrejönnek, és csak egyszer kerülnek be az alkalmazásba.

- Felhasználónként legfeljebb 40 jelszó adható meg. Ha a korlát után megpróbál létrehozni egyet, a rendszer arra kéri, hogy töröljön egy meglévő jelszót, mielőtt az újat hozna létre.

- Egy eszközön egyetlen alkalmazás jelszava használható, nem pedig alkalmazásként. Hozzon létre például egyetlen jelszót a laptopján lévő összes alkalmazáshoz, majd egy másik jelszót az összes alkalmazáshoz az asztalon.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Alkalmazás jelszavainak hozzáadása a bejelentkezési varázslóban

1. Miután befejezte a varázsló előző szakaszait, kattintson a **Tovább gombra** , és fejezze be az **alkalmazás jelszavának** szakaszát.

2. Írja be a jelszót igénylő alkalmazás nevét, például `Outlook 2010` , majd kattintson a **tovább** gombra.

    ![Adja hozzá az alkalmazás jelszava nevet a varázslóban.](media/security-info/app-password-app-password.png)

3. Másolja a jelszót az **alkalmazás jelszava** képernyőről, és illessze be az alkalmazás **jelszó** területére (ebben a példában az Outlook 2010).

    ![Alkalmazás jelszava lap, a másoláshoz használt jelszóval](media/security-info/app-password-copy-password.png)

4. Miután átmásolta a jelszót, és beillesztette az alkalmazásba, térjen vissza ehhez a varázslóhoz, és győződjön meg arról, hogy a bejelentkezési módszer összes adata pontos, majd válassza a **kész** lehetőséget.

    ![Alkalmazás jelszava lap, a befejezési megjegyzéssel](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Következő lépések

- A biztonsági adatok alapértelmezett módszereinek módosításához, törléséhez vagy frissítéséhez lásd:

    - [Hitelesítő alkalmazás biztonsági adatainak beállítása](security-info-setup-auth-app.md).

    - [A szöveges üzenetküldés biztonsági adatainak beállítása](security-info-setup-text-msg.md).

    - [Állítsa be a biztonsági adatokat a telefonhívások használatára](security-info-setup-phone-number.md).

    - [Állítsa be az e-mailek használatára vonatkozó biztonsági adatokat](security-info-setup-email.md).

    - [Biztonsági információk beállítása az előre definiált biztonsági kérdések használatához](security-info-setup-questions.md).

- További információ a bejelentkezés módjáról a megadott módszer használatával: [Bejelentkezés](user-help-sign-in.md).

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com/) , vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.