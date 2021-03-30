---
title: 'Oktatóanyag: a PHS beállítása biztonsági másolatként a AD FShoz Azure AD Connectban | Microsoft Docs'
description: Bemutatja, hogyan lehet bekapcsolni a jelszó-kivonatok szinkronizálását biztonsági másolatként és AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9f59906c566d80344891c0796a85b0a4972e68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313093"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Oktatóanyag: a PHS beállítása biztonsági másolatként a AD FShoz Azure AD Connect

A következő oktatóanyag végigvezeti a jelszó-kivonatok szinkronizálásának beállításán a AD FS biztonsági mentése és feladatátvétele során.  Ez a dokumentum azt is bemutatja, hogyan engedélyezhető a jelszó-kivonatolási szinkronizálás elsődleges hitelesítési módszerként, ha AD FS sikertelen vagy elérhetetlenné vált.

>[!NOTE] 
>Bár ezeket a lépéseket általában vészhelyzeti vagy kimaradási helyzetekben hajtják végre, javasoljuk, hogy tesztelje ezeket a lépéseket, és a leállás előtt ellenőrizze az eljárásokat.

>[!NOTE]
>Abban az esetben, ha nem fér hozzá Azure AD Connect kiszolgálóhoz, vagy a kiszolgáló nem rendelkezik hozzáféréssel az internethez, kapcsolatba léphet [Microsoft ügyfélszolgálata](https://support.microsoft.com/en-us/contactus/) az Azure ad-oldal változásainak támogatásához.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag a következő [oktatóanyagra épül: egyetlen ad-erdős környezet összevonása a felhőbe](tutorial-federation.md) , és az oktatóanyag megkezdése előtt szükséges.  Ha még nem végezte el ezt az oktatóanyagot, a jelen dokumentum lépéseinek megkísérlése előtt végezze el ezt.

>[!IMPORTANT]
>A PHS való váltás előtt készítsen biztonsági másolatot a AD FS-környezetről.  Ezt a [AD FS Rapid Restore Tool](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool)használatával teheti meg.

## <a name="enable-phs-in-azure-ad-connect"></a>PHS engedélyezése a Azure AD Connectban
Az első lépés, hogy már van olyan Azure AD Connect környezetünk, amely összevonást használ, bekapcsolja a jelszó-kivonatolási szinkronizálást, és lehetővé teszi a Azure AD Connect számára a kivonatok szinkronizálását.

Tegye a következőket:

1.  Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra.
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  A további feladatok lapon válassza a **szinkronizálási beállítások testreszabása lehetőséget** , majd kattintson a **tovább** gombra.
4.  Adja meg a globális rendszergazda felhasználónevét és jelszavát.  Ez a fiók az előző oktatóanyagban [lett létrehozva](tutorial-federation.md#create-a-global-administrator-in-azure-ad) .
5.  A **címtárak összekapcsolása** képernyőn kattintson a **tovább** gombra.
6.  A **tartomány és szervezeti egység szűrése** képernyőn kattintson a **tovább** gombra.
7.  A **választható szolgáltatások** képernyőn jelölje be a **jelszó-kivonatolás szinkronizálása** jelölőnégyzetet, majd kattintson a **tovább** gombra.
![Kiválasztás](media/tutorial-phs-backup/backup1.png)</br>
8.  A **konfigurálásra kész** képernyőn kattintson a **Konfigurálás** elemre.
9.  A konfiguráció befejeződése után kattintson a **Kilépés** gombra.
10. Ennyi az egész!  Készen áll.  A jelszó-kivonat szinkronizálása most bekövetkezik, és biztonsági másolatként használható, ha AD FS elérhetetlenné válik.

## <a name="switch-to-password-hash-synchronization"></a>Váltás jelszó-kivonatolási szinkronizálásra
Most bemutatjuk, hogyan válthat át a jelszó-kivonat szinkronizálására. Mielőtt elkezdené, gondolja át, milyen feltételek mellett hajtsa végre a kapcsolót. Ne hajtsa végre a kapcsolót ideiglenes okokból, például hálózati leállás, kisebb AD FS probléma vagy a felhasználók egy részhalmazát befolyásoló probléma esetén. Ha úgy dönt, hogy elvégzi a kapcsolót, mert a probléma kijavítása túl sokáig tart, tegye a következőket:

> [!IMPORTANT]
> Ügyeljen arra, hogy a jelszó-kivonatok szinkronizálása az Azure AD-vel eltarthat egy ideig.  Ez azt jelenti, hogy akár 3 órát is igénybe vehet, amíg a szinkronizálások befejeződik, és a jelszó-kivonatok használatával megkezdheti a hitelesítést.

1. Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra.
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza **a felhasználói bejelentkezés módosítása** lehetőséget, majd kattintson a **tovább** gombra.
![Módosítása](media/tutorial-phs-backup/backup2.png)</br>
4.  Adja meg a globális rendszergazda felhasználónevét és jelszavát.  Ez a fiók az előző oktatóanyagban [lett létrehozva](tutorial-federation.md#create-a-global-administrator-in-azure-ad) .
5.  A **felhasználó bejelentkezési** képernyőjén válassza a jelszó- **kivonat szinkronizálása** lehetőséget, és jelölje be a **ne alakítsa át a felhasználói fiókok** számára jelölőnégyzetet.  
6.  Hagyja bejelölve az **egyszeri bejelentkezés alapértelmezett engedélyezése** beállítást, és kattintson a **tovább** gombra.
7.  Az **egyszeri bejelentkezés engedélyezése** képernyőn kattintson a **tovább** gombra.
8.  A **konfigurálásra kész** képernyőn kattintson a **Konfigurálás** elemre.
9.  A konfiguráció befejezése után kattintson a **Kilépés** gombra.
10. A felhasználók mostantól használhatják a jelszavukat, hogy bejelentkezzenek az Azure-ba és az Azure-szolgáltatásokba.

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés az egyik felhasználóval

1. Nyissa meg a következő címet a böngészőben: [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be egy olyan felhasználói fiókkal, amely az új bérlőben lett létrehozva.  A következő formátumban kell bejelentkeznie: ( user@domain.onmicrosoft.com ). Ugyanazt a jelszót használja, amelyet a felhasználó a helyszíni bejelentkezéshez használ.</br>
   ![Képernyőkép, amely egy sikeres üzenetet jelenít meg a bejelentkezés tesztelésekor. ](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Váltson vissza az összevonásra
Most bemutatjuk, hogyan válthat vissza az összevonásra.  Ehhez tegye a következőket:

1.  Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra.
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza **a felhasználói bejelentkezés módosítása** lehetőséget, majd kattintson a **tovább** gombra.
4.  Adja meg a globális rendszergazda felhasználónevét és jelszavát.  Ez az előző oktatóanyagban [itt](tutorial-federation.md#create-a-global-administrator-in-azure-ad) létrehozott fiók.
5.  A **felhasználó bejelentkezési** képernyőjén válassza az **összevonás AD FS** lehetőséget, majd kattintson a **tovább** gombra.  
6. A tartományi rendszergazda hitelesítő adatai lapon adja meg a Contoso\rendszergazda felhasználónevét és jelszavát, majd kattintson a **Tovább gombra.**
7. A AD FS Farm képernyőn kattintson a **tovább** gombra.
8. Az **Azure ad-tartomány** képernyőn válassza ki a tartományt a legördülő listából, és kattintson a **tovább** gombra.
9. A **konfigurálásra kész** képernyőn kattintson a **Konfigurálás** elemre.
10. A konfiguráció befejezése után kattintson a **tovább** gombra.
![Konfigurálás](media/tutorial-phs-backup/backup4.png)</br>
11. Az **összevonási kapcsolat ellenőrzése** képernyőn kattintson az **ellenőrzés** gombra.  Előfordulhat, hogy a sikeres végrehajtáshoz konfigurálnia kell a DNS-rekordokat (adjon hozzá egy és AAAA rekordot).
![Képernyőkép, amely az összevonási kapcsolat ellenőrzése képernyőt és az ellenőrzés gombot mutatja.](media/tutorial-phs-backup/backup5.png)</br>
12. Kattintson a **Kilépés** gombra.

## <a name="reset-the-ad-fs-and-azure-trust"></a>A AD FS és az Azure-beli megbízhatóság alaphelyzetbe állítása
Most vissza kell állítania a AD FS és az Azure közötti megbízhatóságot.

1.  Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra.
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza az **összevonás kezelése** lehetőséget, és kattintson a **tovább** gombra.
4.  Válassza az **Azure ad-megbízhatóság visszaállítása** lehetőséget, és kattintson a **tovább** gombra.
![Alaphelyzetbe állítása](media/tutorial-phs-backup/backup6.png)</br>
5.  A **Kapcsolódás az Azure ad-** hez képernyőn adja meg a globális rendszergazda felhasználónevét és jelszavát.
6.  A **kapcsolódás AD FS** képernyőn adja meg a Contoso\Rendszergazda felhasználónevét és jelszavát, majd kattintson a **Tovább gombra.**
7.  A **tanúsítványok** képernyőn kattintson a **tovább** gombra.

## <a name="test-signing-in-with-a-user"></a>Felhasználóval való bejelentkezés tesztelése

1.  Nyissa meg a következő címet a böngészőben: [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be egy olyan felhasználói fiókkal, amely az új bérlőben lett létrehozva.  A következő formátumban kell bejelentkeznie: ( user@domain.onmicrosoft.com ). Ugyanazt a jelszót használja, amelyet a felhasználó a helyszíni bejelentkezéshez használ.
![Ellenőrzés](media/tutorial-password-hash-sync/verify1.png)

Ezzel sikeresen beállított egy hibrid identitási környezetet, amellyel tesztelheti és megismerheti az Azure által kínált lehetőségeket.

## <a name="next-steps"></a>Következő lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Jelszó-kivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)