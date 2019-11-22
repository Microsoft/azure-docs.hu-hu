---
title: Fiókok biztonsági mentése és helyreállítása a Microsoft Authenticator alkalmazással – Azure AD
description: Megtudhatja, hogyan készíthet biztonsági másolatot a fiók hitelesítő adatairól, és hogyan állíthatja helyre a biztonsági másolatot a Microsoft Authenticator alkalmazás használatával.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 827213c8d243e9d66c58195e1d9400bed9c3e337
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267005"
---
# <a name="backup-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Fiók hitelesítő adatainak biztonsági mentése és helyreállítása a Microsoft Authenticator alkalmazás használatával

**A következőkre vonatkozik:**

- iOS-eszközök, 5.7.0 és újabb verziók futtatása

- Android-eszközök, 6.6.0 és újabb verziók futtatása

A Microsoft Authenticator alkalmazás biztonsági mentést készít a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. A biztonsági mentést követően az alkalmazással helyreállíthatja az adatokat egy új eszközön, így elkerülhető a zárolás vagy a fiókok ismételt létrehozása.

Az egyes biztonsági mentési tárolóhelyek esetében egy személyes Microsoft-fiók van szükség, az iOS-hez pedig iCloud-fiókra is szükség van. Egyetlen helyen több fiók is tárolható. Rendelkezhet például személyes fiókkal, munkahelyi vagy iskolai fiókkal, valamint személyes, nem Microsoft-fiók Facebook-, Google-és egyéb hasonló szolgáltatásokkal is.

> [!IMPORTANT]
> A rendszer csak a személyes és a harmadik féltől származó fiók hitelesítő adatait tárolja, beleértve a felhasználónevét és az identitásának igazolásához szükséges fiók-ellenőrző kódot. Nem tárolunk semmilyen más, a fiókjához társított információt, például az e-maileket és a fájlokat. A fiókokat semmilyen módon vagy más termékkel vagy szolgáltatással sem társítjuk és nem osztjuk meg. Végül pedig a rendszergazda nem kap semmilyen információt ezekről a fiókokról.

## <a name="back-up-your-account-credentials"></a>A fiók hitelesítő adatainak biztonsági mentése

A hitelesítő adatok biztonsági mentése előtt a következőket kell tennie:

- Személyes [Microsoft-fiók](https://account.microsoft.com/account) , amely helyreállítási fiókként működik.

- **Csak iOS esetén** a tényleges tárolási helynek [iCloud-fiókkal](https://www.icloud.com/) kell rendelkeznie.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>A Felhőbeli biztonsági mentés bekapcsolása iOS-eszközökön

- Az iOS-eszközön válassza a **Beállítások**, majd a **biztonsági mentés**lehetőséget, majd kapcsolja be az **iCloud biztonsági mentést**.

    A fiók hitelesítő adatai biztonsági mentést kapnak az iCloud-fiókjába.

    ![iOS-beállítások képernyő, amely az iCloud biztonsági mentési beállításainak helyét jeleníti meg](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>A Felhőbeli biztonsági mentés bekapcsolása Android-eszközökön

- Az Android-eszközén válassza a **Beállítások**, majd a **biztonsági**mentés lehetőséget, majd kapcsolja be a **Felhőbeli biztonsági mentést**.

    A fiók hitelesítő adatait a rendszer biztonsági másolatot készít a Felhőbeli fiókjához.

    ![Android-beállítások képernyő, amely a biztonsági mentési beállítások helyét jeleníti meg](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>A fiók hitelesítő adatainak helyreállítása az új eszközön

A fiók hitelesítő adatait a Felhőbeli fiókjából állíthatja helyre, de először győződjön meg arról, hogy a helyreállított fiók nem létezik a Microsoft Authenticator alkalmazásban. Ha például helyreállítja a személyes Microsoft-fiók, meg kell győződnie arról, hogy a hitelesítő alkalmazásban már be van állítva a személyes Microsoft-fiók. Ez az ellenőrzés fontos, így biztos lehet benne, hogy tévedésből nem írunk felül vagy törölünk egy meglévő fiókot.

### <a name="to-recover-your-information"></a>Az adatok helyreállítása

1. Nyissa meg a mobileszközön a Microsoft Authenticator alkalmazást, majd a képernyő alján kattintson a **helyreállítás indítása** elemre.

    ![Microsoft Authenticator alkalmazás, amely megmutatja, hová kattintson a helyreállítás megkezdése elemre.](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Jelentkezzen be a helyreállítási fiókjába ugyanazzal a személyes Microsoft-fiók használatával, amelyet a biztonsági mentési folyamat során használt.

    A fiók hitelesítő adatait az új eszközre állítja vissza.

A helyreállítás befejezése után észreveheti, hogy a Microsoft Authenticator alkalmazásban a személyes Microsoft-fiók ellenőrző kódok különböznek a régi és az új telefonok között. A kódok különböznek, mert minden eszköz saját egyedi hitelesítő adatokkal rendelkezik, de mindkettő érvényes és működik, miközben a társított telefon használatával jelentkezik be.

## <a name="recover-additional-accounts-requiring-more-verification"></a>További ellenőrzéseket igénylő további fiókok helyreállítása

Ha leküldéses értesítéseket használ személyes vagy munkahelyi vagy iskolai fiókjaival, akkor a képernyőn megjelenő riasztást kap, amely szerint további ellenőrzéseket kell megadnia az adatok helyreállítása előtt. Mivel a leküldéses értesítések olyan hitelesítő adatokat igényelnek, amely az adott eszközhöz van kötve, és soha nem küldi el a hálózaton keresztül, a hitelesítő adatoknak az eszközön való létrehozása előtt bizonyítania kell az identitását.

Személyes Microsoft-fiókok esetén a jelszó megadásával, valamint egy másodlagos e-mail-cím vagy telefonszám használatával igazolhatja a személyazonosságát. Munkahelyi vagy iskolai fiókok esetében a fiók szolgáltatója által megadott QR-kódot kell beolvasnia.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>A személyes fiókok további ellenőrzésének biztosítása

1. A Microsoft Authenticator alkalmazás **fiókok** képernyőjén válassza ki a helyreállítani kívánt fiók melletti legördülő nyilat.

    ![Microsoft Authenticator alkalmazás, amely az elérhető fiókokat jeleníti meg a hozzájuk tartozó legördülő nyilakkal](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. A **helyreállításhoz**válassza a bejelentkezés lehetőséget, írja be a jelszót, majd erősítse meg az e-mail-címet vagy a telefonszámot további ellenőrzésként.

    ![Microsoft Authenticator alkalmazás, amely lehetővé teszi a bejelentkezési adatok megadását](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>További ellenőrzés biztosítása munkahelyi vagy iskolai fiókokhoz

1. A Microsoft Authenticator alkalmazás **fiókok** képernyőjén válassza ki a helyreállítani kívánt fiók melletti legördülő nyilat.

    ![Microsoft Authenticator alkalmazás, amely az elérhető fiókokat jeleníti meg a hozzájuk tartozó legördülő nyilakkal](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Jelölje be a **QR-kód bevizsgálása a helyreállításhoz**lehetőséget, majd vizsgálja meg a QR-kódot.

    ![Microsoft Authenticator alkalmazás, amely lehetővé teszi a QR-kód vizsgálatát](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >A QR-kódokról és annak beszerzéséről további információért lásd: Ismerkedés [a Microsoft Authenticator alkalmazással](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) vagy [biztonsági információk beállítása a hitelesítő alkalmazás használatára](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), attól függően, hogy a rendszergazda bekapcsolta-e a biztonsági adatokat.
    >
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Biztonsági mentési és helyreállítási problémák elhárítása

Előfordulhat, hogy a biztonsági mentésnek néhány oka van:

- **Operációs rendszerek módosítása.** A biztonsági mentést az iCloud for iOS és az Android rendszerhez készült Microsoft felhőalapú tárolási szolgáltatója tárolja. Ez azt jelenti, hogy a biztonsági mentés nem érhető el, ha az Android és az iOS rendszerű eszközök között vált. Ha elvégzi a kapcsolót, manuálisan kell újból létrehoznia a fiókokat a Microsoft Authenticator alkalmazásban.

- **Hálózati problémák.** Ha hálózattal kapcsolatos problémákat tapasztal, ellenőrizze, hogy csatlakozik-e a hálózathoz, és hogy megfelelően bejelentkezett-e a fiókjába.

- **Fiókkal kapcsolatos problémák.** Ha fiókkal kapcsolatos problémákat tapasztal, győződjön meg arról, hogy megfelelően bejelentkezett a fiókjába. Az iOS esetében ez azt jelenti, hogy az Apple-vel azonos AppleID-fiókkal kell bejelentkeznie az iCloudba.

- **Véletlen törlés.** Lehetséges, hogy törölte a biztonsági mentési fiókot az előző eszközről, vagy a felhőalapú Storage-fiókját kezeli. Ebben az esetben manuálisan kell újból létrehoznia a fiókját az alkalmazáson belül.

- **Meglévő Microsoft Authenticator fiókok.** Ha már beállított fiókokat a Microsoft Authenticator alkalmazásban, az alkalmazás nem fogja tudni helyreállítani a biztonsági másolatban szereplő fiókokat. A helyreállítás megakadályozása révén biztosíthatja, hogy a fiók adatai ne legyenek felülírva az elavult információkkal. Ebben az esetben a biztonsági mentés helyreállítása előtt el kell távolítania a meglévő fiók adatait a hitelesítő alkalmazásban beállított meglévő fiókokról.

- **A biztonsági mentés elavult.** Ha a biztonsági mentési információk elavultak, előfordulhat, hogy az adatok frissítését újra be kell jelentkeznie a Microsoft helyreállítási fiókjába. A helyreállítási fiók a személyes Microsoft-fiók, amelyet kezdetben használt a biztonsági másolat tárolására. Ha bejelentkezésre van szükség, egy piros pont jelenik meg a menüben vagy a műveleti sávon. Miután kiválasztotta a piros pontot, a rendszer felszólítja az adatok frissítésére.

## <a name="next-steps"></a>Következő lépések

Most, hogy biztonsági mentést készített, és visszaállította a fiókja hitelesítő adatait az új eszközre, továbbra is használhatja a Microsoft Authenticator alkalmazást a személyazonosságának ellenőrzéséhez. További információ: bejelentkezés a [fiókba a Microsoft Authenticator alkalmazás használatával](user-help-sign-in.md).

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Mi a Microsoft Authenticator alkalmazás?](user-help-auth-app-overview.md)

- [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)

- [Többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/)
