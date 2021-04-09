---
title: Önkiszolgáló jelszó-visszaállítás Windows-eszközökön – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti Azure Active Directory önkiszolgáló jelszó-visszaállítást a Windows bejelentkezési képernyőjén.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491385"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Azure Active Directory önkiszolgáló jelszó-visszaállítás engedélyezése a Windows bejelentkezési képernyőjén

Az önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára a Azure Active Directory (Azure AD) számára a jelszavuk módosítását vagy alaphelyzetbe állítását, rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül. A felhasználók általában egy böngészőt nyitnak meg egy másik eszközön a [SSPR-portál](https://aka.ms/sspr)eléréséhez. A Windows 7, 8, 8,1 és 10 rendszerű számítógépeken a felhasználói élmény javítása érdekében engedélyezheti a felhasználók számára, hogy a Windows bejelentkezési képernyőjén állítsa alaphelyzetbe a jelszavukat.

![Példa a Windows 7 és a 10 bejelentkezési képernyőre a SSPR hivatkozás látható](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> Ez az oktatóanyag azt ismerteti, hogyan engedélyezhető a SSPR a Windows-eszközökön a vállalatban.
>
> Ha az informatikai csapat nem engedélyezte a SSPR használatát a Windows-eszközről, vagy ha a bejelentkezés során problémákba lépett, további segítségért forduljon az ügyfélszolgálathoz.

## <a name="general-limitations"></a>Általános korlátozások

A következő korlátozások vonatkoznak a SSPR használatára a Windows bejelentkezési képernyőjén:

- A jelszó alaphelyzetbe állítása jelenleg nem támogatott Távoli asztal vagy Hyper-V bővített munkamenetekben.
- Néhány külső hitelesítő adat szolgáltatója ismert, hogy problémákat okozhat a szolgáltatással kapcsolatban.
- Ha a [EnableLUA beállításkulcs](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) módosításával tiltja le az UAC-t, a probléma problémákat okozhat.
- Ez a funkció nem működik a 802.1 x hálózati hitelesítéssel rendelkező hálózatok esetében, és a "azonnali végrehajtás a felhasználó bejelentkezése előtt" beállítást. A 802.1 x hálózati hitelesítéssel telepített hálózatok esetében ajánlott a számítógép-hitelesítés használata a funkció engedélyezéséhez.
- A hibrid Azure AD-hez csatlakoztatott számítógépeknek az új jelszó használatához és a gyorsítótárazott hitelesítő adatok frissítéséhez hálózati kapcsolattal kell rendelkezniük a tartományvezérlőhöz. Ez azt jelenti, hogy az eszközöknek a szervezet belső hálózatán vagy egy helyszíni tartományvezérlőhöz való hálózati hozzáféréssel rendelkező VPN-en kell lenniük.
- Ha rendszerképet használ, a Sysprep futtatása előtt győződjön meg arról, hogy a webes gyorsítótár törlődik a beépített rendszergazda számára a profilmásolási lépés végrehajtása előtt. A lépéssel kapcsolatos további információkért tekintse meg az [Egyéni alapértelmezett felhasználói profil használata esetén](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)a terméktámogatási cikkben.
- A következő beállításokat ismerheti meg a Windows 10-es eszközökön a jelszavak használatának és alaphelyzetbe állításának megakadályozása érdekében:
    - Ha a Windows 10 rendszerben a Ctrl + Alt + Del billentyűkombináció szükséges, a **jelszó alaphelyzetbe állítása** nem fog működni.
    - Ha a zárolási képernyő értesítései ki vannak kapcsolva, a **jelszó alaphelyzetbe állítása** nem fog működni.
    - Az *HideFastUserSwitching* értéke engedélyezve vagy 1
    - Az *DontDisplayLastUserName* értéke engedélyezve vagy 1
    - Az *NoLockScreen* értéke engedélyezve vagy 1
    - A *EnableLostMode* be van állítva az eszközön
    - A Explorer.exe helyére egyéni rendszerhéj van lecserélve
- A következő adott három beállítás kombinációja miatt a funkció nem működik.
    - Interaktív bejelentkezés: nem szükséges a CTRL + ALT + DEL = letiltva
    - *DisableLockScreenAppNotifications* = 1 vagy engedélyezve
    - A Windows SKU nem otthoni vagy Professional kiadás

> [!NOTE]
> Ezek a korlátozások a Windows Hello for Business PIN-kódjának alaphelyzetbe állítására is érvényesek az eszköz zárolási képernyőjéről.
>

## <a name="windows-10-password-reset"></a>Windows 10 jelszó alaphelyzetbe állítása

Ha Windows 10-es eszközt szeretne konfigurálni a SSPR a bejelentkezési képernyőn, tekintse át a következő előfeltételeket és konfigurációs lépéseket.

### <a name="windows-10-prerequisites"></a>Windows 10 előfeltételek

- A rendszergazdának [engedélyeznie kell az Azure ad önkiszolgáló jelszó-visszaállítást a Azure Portal](tutorial-enable-sspr.md).
- A felhasználónak regisztrálnia kell a SSPR a funkció használata előtt [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Nem egyedi, ha a Windows bejelentkezési képernyőjén SSPR használ, minden felhasználónak meg kell adnia a hitelesítési kapcsolattartási adatokat, mielőtt alaphelyzetbe állíthatja a jelszavát.
- Hálózati proxyra vonatkozó követelmények:
    - A 443- `passwordreset.microsoftonline.com` es port a és a `ajax.aspnetcdn.com`
    - A Windows 10-es eszközök csak a gépi szintű proxyk konfigurációját támogatják.
- Futtassa legalább a Windows 10 2018-es verzióját (v1803), és az eszközöknek a következőknek kell lenniük:
    - Azure AD-hez csatlakoztatva
    - csatlakozik a Hibrid Azure AD-hez

### <a name="enable-for-windows-10-using-intune"></a>Windows 10 engedélyezése az Intune használatával

A konfigurációs módosítás üzembe helyezésével engedélyezheti a SSPR a bejelentkezési képernyőről az Intune használatával a legrugalmasabb módszert. Az Intune lehetővé teszi, hogy a konfigurációs módosítást az Ön által meghatározott gépek adott csoportján telepítse. Ehhez a metódushoz regisztrálni kell az Intune-ba az eszközt.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Eszközkonfigurációs szabályzat létrehozása az Intune-ban

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **Intune** lehetőséget.
1. Hozzon létre egy új eszköz-konfigurációs profilt az **eszköz konfigurációs**  >  **profiljaiban**, majd válassza a **+ profil létrehozása** lehetőséget.
   - A **platform** esetében válassza a *Windows 10 és újabb*
   - A **Profil típusa** beállításnál válassza az *Egyéni* lehetőséget.
1. Válassza a **Létrehozás** lehetőséget, majd adjon meg egy értelmes nevet a profilhoz, például *: Windows 10 bejelentkezési képernyő SSPR*

    Szükség esetén adjon meg egy értelmes leírást a profilról, majd kattintson a **tovább** gombra.
1. A *konfigurációs beállítások* területen válassza a **Hozzáadás** lehetőséget, és adja meg a következő OMA-URI beállítást a jelszó alaphelyzetbe állításához.
      - Adjon meg egy értelmes nevet a beállítás elmagyarázása érdekében, például *adja hozzá a SSPR hivatkozást*.
      - Opcionálisan megadhatja a beállítás kifejező leírását.
      - Állítsa az **OMA-URI** beállítást `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` értékre
      - Állítsa az **Adattípus** beállítást **Egész szám** értékre
      - Állítsa az **Érték** beállítást **1** értékre

    Válassza a **Hozzáadás**, majd a **tovább** lehetőséget.
1. A szabályzat meghatározott felhasználókhoz, eszközökhöz vagy csoportokhoz rendelhető hozzá. Rendelje hozzá a profilt igény szerint a környezetéhez, ideális esetben az eszközök tesztelési csoportjához, majd válassza a **tovább** lehetőséget.

    További információ: [felhasználói és eszköz profilok társítása Microsoft Intuneban](/mem/intune/configuration/device-profile-assign).

1. Szükség szerint konfigurálja az alkalmazhatósági szabályokat a környezet számára, például *Ha a Windows 10 Enterprise operációsrendszer-kiadás*, majd válassza a **tovább** lehetőséget.
1. Tekintse át a profilt, majd válassza a **Létrehozás** lehetőséget.

### <a name="enable-for-windows-10-using-the-registry"></a>Windows 10 engedélyezése a beállításjegyzék használatával

A következő lépésekkel engedélyezheti a SSPR a bejelentkezési képernyőn egy beállításkulcs használatával:

1. Jelentkezzen be a Windows rendszerű számítógépre rendszergazdai hitelesítő adatok használatával.
1. Nyomja le a **Windows**  +  **R** billentyűt a *Futtatás* párbeszédpanel megnyitásához, majd futtassa a **Regedit parancsot** rendszergazdaként
1. Adja meg a következő beállításkulcsot:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 jelszó-visszaállítás hibaelhárítása

Ha problémája merül fel a Windows bejelentkezési képernyőjén a SSPR használatával, az Azure AD-napló tartalmazza az IP-címmel és a *ClientType* kapcsolatos információkat, amelyekben a jelszó alaphelyzetbe állítása történt, ahogy az alábbi példában látható:

![Windows 7 jelszó alaphelyzetbe állítása az Azure AD naplójában](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Ha a felhasználók egy Windows 10-es eszköz bejelentkezési képernyőjéről állítanak alaphelyzetbe a jelszavukat, egy alacsony jogosultságú ideiglenes fiók `defaultuser1` jön létre. Ezzel a fiókkal a jelszó-visszaállítási folyamat biztonságos marad.

Maga a fiók véletlenszerűen generált jelszót tartalmaz, nem jelenik meg az eszköz bejelentkezéséhez, és automatikusan törlődik, miután a felhasználó alaphelyzetbe állítja a jelszavát. Több `defaultuser` profil is létezik, de nyugodtan figyelmen kívül hagyható.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 és 8,1 jelszó alaphelyzetbe állítása

Windows 7, 8 vagy 8,1 rendszerű eszköz SSPR való konfigurálásához a bejelentkezési képernyőn tekintse át a következő előfeltételeket és konfigurációs lépéseket.

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 és 8,1 előfeltételek

- A rendszergazdának [engedélyeznie kell az Azure ad önkiszolgáló jelszó-visszaállítást a Azure Portal](tutorial-enable-sspr.md).
- A felhasználónak regisztrálnia kell a SSPR a funkció használata előtt [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Nem egyedi, ha a Windows bejelentkezési képernyőjén SSPR használ, minden felhasználónak meg kell adnia a hitelesítési kapcsolattartási adatokat, mielőtt alaphelyzetbe állíthatja a jelszavát.
- Hálózati proxyra vonatkozó követelmények:
    - 443-es port `passwordreset.microsoftonline.com`
- Javított Windows 7 vagy Windows 8,1 operációs rendszer.
- TLS 1,2 engedélyezve a [Transport Layer Security (TLS) beállításjegyzék-beállításokban](/windows-server/security/tls/tls-registry-settings#tls-12)található útmutatás használatával.
- Ha a gépen több külső hitelesítő adat-szolgáltató van engedélyezve, a felhasználók több felhasználói profilt látnak a bejelentkezési képernyőn.

> [!WARNING]
> A TLS 1,2-et engedélyezni kell, nem csak automatikus egyeztetésre van beállítva.

### <a name="install"></a>Telepítés

A Windows 7, 8 és 8,1 esetében egy kis összetevőt kell telepíteni a gépre, hogy engedélyezze a SSPR a bejelentkezési képernyőn. A SSPR-összetevő telepítéséhez hajtsa végre a következő lépéseket:

1. Töltse le a megfelelő telepítőt az engedélyezni kívánt Windows-verzióhoz.

    A szoftver telepítőjének a Microsoft letöltőközpontban érhető el [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Jelentkezzen be arra a gépre, amelyre telepíteni szeretné, majd futtassa a telepítőt.
1. A telepítés után az újraindítás kifejezetten ajánlott.
1. Az újraindítás után a bejelentkezési képernyőn válasszon ki egy felhasználót, és válassza az "Elfelejtett jelszó?" lehetőséget. a jelszó-visszaállítási munkafolyamat elindításához.
1. A jelszó alaphelyzetbe állításához a képernyőn megjelenő lépéseket követve fejezze be a munkafolyamatot.

![Például a Windows 7 a "Elfelejtett jelszó?" gombra kattintott SSPR folyamat](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Csendes telepítés

Az SSPR összetevő a következő parancsok használata nélkül telepíthető vagy távolítható el:

- A beavatkozás nélküli telepítéshez használja az "msiexec/i SsprWindowsLogon.PROD.msi/Qn" parancsot.
- Csendes eltávolításhoz használja az "msiexec/x SsprWindowsLogon.PROD.msi/Qn" parancsot.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 és 8,1 jelszó-visszaállítás hibaelhárítása

Ha problémája merül fel a Windows bejelentkezési képernyőjén a SSPR használatával, az események a gépen és az Azure AD-ben is naplózva lesznek. Az Azure AD-események között szerepelnek az IP-címről és a jelszó alaphelyzetbe állítására szolgáló ClientType, ahogy az a következő példában látható:

![Windows 7 jelszó alaphelyzetbe állítása az Azure AD naplójában](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Ha további naplózásra van szükség, a számítógépen található beállításkulcs módosítható a részletes naplózás engedélyezéséhez. Csak a következő beállításkulcs-értékkel engedélyezheti a részletes naplózást hibaelhárítási célokra:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- A részletes naplózás engedélyezéséhez hozzon létre egy `REG_DWORD: "EnableLogging"` értéket, és állítsa 1 értékre.
- A részletes naplózás letiltásához módosítsa a `REG_DWORD: "EnableLogging"` értéket 0-ra.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

A Windows-eszközökhöz konfigurált SSPR, milyen módosításokat végez a felhasználó? Honnan fogják tudni, hogy a bejelentkezési képernyőn új jelszót kérhetnek? Az alábbi képernyőképek azt mutatják be, hogy a felhasználó hogyan állíthatja alaphelyzetbe a jelszavukat a SSPR használatával:

![Példa a Windows 7 és a 10 bejelentkezési képernyőre a SSPR hivatkozás látható](./media/howto-sspr-windows/windows-reset-password.png)

Amikor a felhasználók megpróbálnak bejelentkezni, megjelenik egy **új jelszó** kérése vagy **elfelejtett jelszó** hivatkozás, amely megnyitja az önkiszolgáló jelszó-visszaállítási élményt a bejelentkezési képernyőn. Ezzel a funkcióval a felhasználók visszaállíthatják a jelszavukat anélkül, hogy egy másik eszközt kellene használniuk egy webböngésző eléréséhez.

A szolgáltatás használatával kapcsolatos további információkért tekintse meg a [munkahelyi vagy iskolai jelszavának alaphelyzetbe állítása](../user-help/active-directory-passwords-update-your-own-password.md) című témakört.

## <a name="next-steps"></a>Következő lépések

A felhasználói regisztrációs élmény egyszerűbbé tétele érdekében [előre feltöltheti a felhasználói hitelesítési kapcsolattartási adatokat a SSPR](howto-sspr-authenticationdata.md).
