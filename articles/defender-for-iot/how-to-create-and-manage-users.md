---
title: Felhasználók létrehozása és kezelése
description: Érzékelők és a helyszíni felügyeleti konzol felhasználóinak létrehozása és kezelése. A felhasználók hozzárendelhetők a rendszergazda, a biztonsági elemző vagy a csak olvasási jogosultsággal rendelkező felhasználó szerepköréhez.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/03/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: dff379c99fa7383c7f7844cf8d195a345e88a335
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466269"
---
# <a name="about-defender-for-iot-console-users"></a>A IoT-konzol felhasználói

Ez a cikk bemutatja, hogyan hozhatók létre és kezelhetők az érzékelők és a helyszíni felügyeleti konzolok felhasználói. A felhasználói szerepkörök közé tartozik a rendszergazda, a biztonsági elemző vagy a csak olvasási jogosultsággal rendelkező felhasználó. Az egyes szerepkörök az érzékelő vagy a helyszíni felügyeleti konzol eszközeihez tartozó engedélyek tartományához vannak társítva. A szerepkörök úgy vannak kialakítva, hogy megkönnyítsék a IoT készült Azure Defender részletes és biztonságos elérését.

A funkciók nyomon követhetik a felhasználói tevékenységeket, és engedélyezhetik Active Directory bejelentkezést is.

Alapértelmezés szerint a rendszer minden érzékelőt és helyszíni felügyeleti konzolt telepít egy *cyberx és egy támogató* felhasználóval. Ezek a felhasználók speciális eszközöket biztosítanak a hibaelhárításhoz és a beállításhoz. A rendszergazda felhasználóknak be kell jelentkezniük a felhasználói hitelesítő adatokkal, létre kell hozniuk egy rendszergazda felhasználót, majd további felhasználókat kell létrehoznia a biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók számára.

## <a name="role-based-permissions"></a>Szerepköralapú engedélyek
A következő felhasználói szerepkörök érhetők el:

- **Csak olvasás**: a csak olvasási jogosultsággal rendelkező felhasználók olyan feladatokat hajtanak végre, mint például a riasztások és az eszközök megtekintése az eszköz térképén. Ezek a felhasználók hozzáférhetnek a **navigáció** alatt megjelenő beállításokhoz.

- **Biztonsági elemző**: a biztonsági elemzőknek csak olvasási jogosultsággal rendelkező felhasználói engedélyei vannak. Emellett műveleteket végezhetnek az eszközökön, visszaigazolhatja a riasztásokat, és használhatnak vizsgálati eszközöket. Ezek a felhasználók hozzáférhetnek a **Navigálás** és **elemzés** alatt megjelenő beállításokhoz.

- **Rendszergazda**: a rendszergazdák hozzáférhetnek az összes eszközhöz, beleértve a rendszerkonfigurációk definiálását, a felhasználók létrehozását és felügyeletét, valamint egyebeket. Ezek a felhasználók hozzáférhetnek a **Navigálás**, **elemzés** és **felügyelet** alatt megjelenő beállításokhoz.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Szerepköralapú engedélyek a helyszíni felügyeleti konzol eszközeihez

Ez a szakasz a rendszergazdák, a biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók számára elérhető engedélyeket ismerteti a helyszíni felügyeleti konzolon.  

| Engedély | Csak olvasás | Biztonsági elemző | Rendszergazda |
|--|--|--|--|
| A vállalati Térkép megtekintése és szűrése | ✓ | ✓ | ✓ |
| Hely létrehozása |  |  | ✓ |
| Hely kezelése (zónák hozzáadása és szerkesztése) |  |  | ✓ |
| Az eszközök leltárának megtekintése és szűrése | ✓ | ✓ | ✓ |
| Riasztások megtekintése és kezelése: nyugtázás, megismerés és PIN-kód | ✓ | ✓ | ✓ |
| Jelentések készítése |  | ✓ | ✓ |
| Kockázatértékelési jelentések megtekintése |  | ✓ | ✓ |
| Riasztás kizárásának beállítása |  | ✓ | ✓ |
| Hozzáférési csoportok megtekintése vagy meghatározása |  |  | ✓ |
| Rendszerbeállítások kezelése |  |  | ✓ |
| Felhasználók kezelése |  |  | ✓ |
| Riasztási adatküldés a partnereknek |  |  | ✓ |
| Tanúsítványok kezelése |  |  | ✓ |
| Munkamenet időkorlátja, ha a felhasználók nem aktívak | 30 perc | 30 perc  | 30 perc  |

#### <a name="assign-users-to-access-groups"></a>Felhasználók társítása a csoportok eléréséhez

A rendszergazdák növelhetik a felhasználói hozzáférés-vezérlést a Defenderben a IoT, ha a felhasználókat egy adott *hozzáférési csoporthoz* rendelik. A hozzáférési csoportok olyan zónákhoz, helyekhez, régiókhoz és üzleti egységekhez vannak rendelve, amelyekben az érzékelő található. A felhasználók hozzáférési csoportokhoz való hozzárendelésével a rendszergazdák konkrétan vezérelhetik, hogy a felhasználók hogyan kezelhetik és elemezzék az eszközök észlelését. 

Ezzel a módszerrel olyan nagyméretű szervezeteket is használhat, amelyekben a felhasználói engedélyek összetettek vagy globális szervezeti biztonsági házirend alapján meghatározhatók. További információ: a [globális hozzáférés-vezérlés meghatározása](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Szerepköralapú engedélyek az érzékelő eszközökhöz

Ez a szakasz az érzékelő-rendszergazdák, a biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók számára elérhető engedélyeket ismerteti.  

| Engedély | Csak olvasás | Biztonsági elemző | Rendszergazda |
|--|--|--|--|
| Az irányítópult megtekintése | ✓ | ✓ | ✓ |
| Vezérlő Térkép nagyítási nézetei |  |  | ✓ |
| Riasztások megtekintése | ✓ | ✓ | ✓ |
| Riasztások kezelése: nyugtázás, megismerés és PIN-kód |  | ✓ | ✓ |
| Idősor eseményeinek megtekintése |  | ✓ | ✓ |
| Eszközök, ismert vizsgálati eszközök, programozási eszközök engedélyezése |  | ✓ | ✓ |
| Vizsgálati adatbázis megtekintése | ✓ | ✓ | ✓ |
| Rendszerbeállítások kezelése |  |  | ✓ |
| Felhasználók kezelése |  |  | ✓ |
| DNS-kiszolgálók névkeresési kereséshez |  |  | ✓ |
| Riasztási adatküldés a partnereknek |  | ✓ | ✓ |
| Riasztási megjegyzések létrehozása |  | ✓ | ✓ |
| Programozási változások előzményeinek megtekintése | ✓ | ✓ | ✓ |
| Testreszabott riasztási szabályok létrehozása |  | ✓ | ✓ |
| Egyszerre több értesítés kezelése |  | ✓ | ✓ |
| Tanúsítványok kezelése |  |  | ✓ |
| Munkamenet időkorlátja, ha a felhasználók nem aktívak | 30 perc | 30 perc | 30 perc |

## <a name="define-users"></a>Felhasználók definiálása

Ez a szakasz a felhasználók definiálásának módját ismerteti. A Cyberx, a támogatás és a rendszergazda felhasználók hozzáadhatnak, eltávolíthatnak és frissíthetnek más felhasználói definíciókat.

Felhasználó definiálása:

1. Az érzékelő vagy a helyszíni felügyeleti konzol bal oldali ablaktábláján válassza a **felhasználók** lehetőséget.
1. A **felhasználók** ablakban válassza a **felhasználó létrehozása** lehetőséget.
1. A **felhasználó létrehozása** panelen adja meg a következő paramétereket:

   - **Felhasználónév**: adjon meg egy felhasználónevet.
   - **E-mail**: adja meg a felhasználó e-mail-címét.
   - **Keresztnév: adja** meg a felhasználó utónevét.
   - **Vezetéknév**: adja meg a felhasználó vezetéknevét.
   - **Szerepkör**: adja meg a felhasználó szerepkörét. Lásd: [szerepköralapú engedélyek](#role-based-permissions).
   - **Hozzáférési csoport**: Ha felhasználót hoz létre a helyszíni felügyeleti konzolhoz, adja meg a felhasználó hozzáférési csoportját. Lásd: [globális hozzáférés-vezérlés meghatározása](how-to-define-global-user-access-control.md).
   - **Password (jelszó**): válassza ki a felhasználó típusát a következő módon:
     - **Helyi felhasználó**: adjon meg egy jelszót az érzékelő vagy egy helyszíni felügyeleti konzol felhasználója számára. A jelszónak legalább hat karakterből kell állnia, és tartalmaznia kell betűket és számokat.
     - **Active Directory felhasználó**: engedélyezheti, hogy a felhasználók Active Directory hitelesítő adatok használatával bejelentkezzenek az érzékelőbe vagy a felügyeleti konzolba. A definiált Active Directory csoportok adott jogosultsági szintekhez társíthatók. Konfigurálhat például egy adott Active Directory csoportot, és a csoport összes felhasználóját hozzárendelheti a csak olvasható felhasználói típushoz.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Kezelheti a felhasználókat.":::

## <a name="user-session-timeout"></a>Felhasználói munkamenet időkorlátja

Ha a felhasználók nem aktívak a billentyűzeten vagy az egéren egy adott időpontra, akkor kijelentkezett a munkamenetből, és újra be kell jelentkezniük.

Ha a felhasználók 30 percen belül nem működtek együtt a konzol egerével vagy a billentyűzettel, a munkamenet kijelentkezése kényszerítve lesz.

Ez a funkció alapértelmezés szerint engedélyezve van, és a frissítés után is letiltható. Emellett a munkamenetek számlálási ideje is frissíthető. A munkamenet-idő másodpercben van meghatározva. A definíciók az érzékelő és a helyszíni felügyeleti konzolon alkalmazhatók.

A munkamenet időkorlátja üzenet jelenik meg a konzolon, ha az inaktivitás időtúllépése sikeres volt.

### <a name="control-inactivity-sign-out"></a>Inaktivitás vezérlése – kijelentkezés

A rendszergazda felhasználók engedélyezheti és letilthatja a inaktivitásos kijelentkezést, és módosíthatja az inaktivitási küszöbértékeket.

A parancs elérése:

1. Jelentkezzen be a CLI-be az érzékelő vagy a helyszíni felügyeleti konzolhoz a Defender használatával a IoT rendszergazdai hitelesítő adataival.

1. Írja be a következő szöveget: `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

A funkció letiltásához váltson át a következőre: `infinity_session_expiration = true` `infinity_session_expiration = false` .

A kijelentkezési számlálási időszakok frissítéséhez módosítsa az `= <number>` értéket a szükséges időpontra.

## <a name="track-user-activity"></a>Felhasználói tevékenység nyomon követése 

Az esemény idővonalán nyomon követheti a felhasználói tevékenységeket az egyes érzékelőkön. Az idősor megjeleníti az eseményt vagy az érintett eszközt, valamint azt az időpontot és dátumot, ameddig a felhasználó elvégezte a tevékenységet.

Felhasználói tevékenység megtekintése:

1. Jelentkezzen be az érzékelőbe.
1. Az esemény idővonalán engedélyezze a **felhasználói műveletek** lehetőséget. 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="A felhasználó tevékenységének megtekintése.":::

## <a name="integrate-with-active-directory-servers"></a>Integrálás Active Directory-kiszolgálókkal 

Konfigurálja az érzékelőt vagy a helyszíni felügyeleti konzolt a Active Directory való együttműködéshez. Ez lehetővé teszi Active Directory felhasználók számára, hogy a Active Directory hitelesítő adataik használatával hozzáférhessenek a Defender for IoT-konzolokhoz.

Az LDAP-alapú hitelesítés két típusa támogatott:

- **Teljes hitelesítés**: a rendszer beolvassa a felhasználói adatokat az LDAP-kiszolgálóról. Ilyenek például az utónév, a vezetéknév, az e-mail és a felhasználói engedélyek.

- **Megbízható felhasználó**: a rendszer csak a felhasználói jelszót kéri le. A beolvasott egyéb felhasználói adatok az érzékelőben definiált felhasználókon alapulnak.

### <a name="active-directory-and-defender-for-iot-permissions"></a>A IoT engedélyeinek Active Directory és Defender

Az itt definiált Active Directory-csoportokat társíthatja konkrét jogosultsági szintekhez. Beállíthat például egy adott Active Directory csoportot, és csak olvasási engedélyeket rendelhet a csoportban lévő összes felhasználóhoz.

Active Directory konfigurálása:

1. A bal oldali panelen válassza a **Rendszerbeállítások** elemet.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Megtekintheti Active Directory rendszerbeállításait.":::

2. A **Rendszerbeállítások** panelen válassza a **Active Directory** lehetőséget.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Szerkessze Active Directory konfigurációját.":::

3. Az **Active Directory konfigurációjának szerkesztése** párbeszédpanelen válassza a **Active Directory integráció engedélyezve**  >  **Mentés** lehetőséget. A **Active Directory konfigurációjának szerkesztése** párbeszédpanel kibontja, és megadhatja a Active Directory konfigurálásához szükséges paramétereket.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Adja meg a Active Directory konfigurálásához szükséges paramétereket.":::

    > [!NOTE]
    > - Pontosan úgy kell megadnia az LDAP-paramétereket, ahogy a Active Directoryban megjelennek.
    > - Az összes Active Directory paraméternél csak kisbetűset használjon. A kisbetűset akkor is használja, ha a Active Directory konfigurációi nagybetűket használnak.
    > - Ugyanahhoz a tartományhoz nem állítható be az LDAP és az LDAP. Ugyanakkor a különböző tartományokhoz is használhat egyszerre egy időben.

4. Állítsa be a Active Directory kiszolgáló paramétereit az alábbiak szerint:

   | Kiszolgáló paraméter | Leírás |
   |--|--|
   | Tartományvezérlő teljes tartományneve | Állítsa a teljes tartománynevet (FQDN) pontosan úgy, ahogy az az LDAP-kiszolgálón megjelenik. Adja meg például a következőt: `host1.subdomain.domain.com`. |
   | Tartományvezérlő portja | Adja meg azt a portot, amelyen az LDAP konfigurálva van. |
   | Elsődleges tartomány | Állítsa be a tartománynevet (például: `subdomain.domain.com` ) és a kapcsolódási típust az LDAP-konfigurációnak megfelelően. |
   | Active Directory csoportok | Adja meg az LDAP-kiszolgálón Active Directory konfigurációjában definiált csoportnevet. |
   | Megbízható tartományok | Megbízható tartomány hozzáadásához adja hozzá a tartománynevet és a megbízható tartomány kapcsolódási típusát. <br />A megbízható tartományokat csak a felhasználók által definiált felhasználók számára állíthatja be. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>ActiveDirectory-csoportok a helyszíni felügyeleti konzolhoz

Ha Active Directory-csoportokat hoz létre a helyszíni felügyeleti konzol felhasználói számára, minden Active Directory csoporthoz létre kell hoznia egy hozzáférési csoporthoz tartozó szabályt. A helyszíni felügyeleti konzol Active Directory a hitelesítő adatok nem fognak működni, ha nem létezik hozzáférési csoport szabály a Active Directory felhasználói csoport számára. Lásd: [globális hozzáférés-vezérlés meghatározása](how-to-define-global-user-access-control.md).

1. Kattintson a **Mentés** gombra.

2. Megbízható kiszolgáló hozzáadásához válassza a **kiszolgáló hozzáadása** lehetőséget, és konfigurálja a másik kiszolgálót.

## <a name="resetting-passwords"></a>Jelszavak alaphelyzetbe állítása

### <a name="cyberx-or-support-user"></a>CyberX vagy támogató felhasználó

Csak a **CyberX** és a **támogatási** felhasználó férhet hozzá a **Jelszó-helyreállítási** szolgáltatáshoz. Ha a **CyberX** vagy a **támogatási** felhasználó elfelejtette a jelszavát, akkor a jelszó- **helyreállítási** lehetőséggel visszaállíthatja a jelszót a IoT bejelentkezési oldalára.

CyberX vagy támogató felhasználó jelszavának alaphelyzetbe állítása:

1. A IoT-beli Defender-bejelentkezési képernyőn válassza a  **jelszó-helyreállítás** lehetőséget. Megnyílik a **Jelszó-helyreállítási** képernyő.

1. Válassza a **CyberX** vagy a **támogatás** lehetőséget, és másolja ki az egyedi azonosítót.

1. Navigáljon a Azure Portal, és válassza a **helyek és érzékelők** lehetőséget.  

1. Válassza ki az **előfizetés-szűrő** ikont :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  a felső eszköztáron, és válassza ki azt az előfizetést, amelyhez az érzékelő csatlakozik.

1. Jelölje be a helyszíni **felügyeleti konzol jelszavának helyreállítása** lapot.

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="A helyreállítási fájl letöltéséhez kattintson a helyszíni felügyelet helyreállítása gombra.":::

1. Adja meg a **Jelszó-helyreállítási** képernyőn kapott egyedi azonosítót, és kattintson a **helyreállítás** elemre. A `password_recovery.zip` Fájl letöltése megtörténik.

    > [!NOTE]
    > Ne változtassa meg a jelszó-helyreállítási fájlt. Ez egy aláírt fájl, és nem fog működni, ha Ön nem módosítja.

1. A **Jelszó-helyreállítási** képernyőn válassza a **feltöltés** lehetőséget. Ekkor megnyílik **a jelszó feltöltése helyreállítási fájl** ablak.

1. Válassza a **Tallózás** lehetőséget a fájl megkereséséhez `password_recovery.zip` , vagy húzza a fájlt az `password_recovery.zip` ablakra.

    > [!NOTE]
    > Hibaüzenet jelenhet meg, amely azt jelzi, hogy a fájl érvénytelen. A hibaüzenet kijavításához győződjön meg arról, hogy a megfelelő előfizetést választotta a letöltés előtt, `password_recovery.zip` majd töltse le újra.  

1. Ezután a **tovább** gombra, a felhasználó és a rendszer által létrehozott jelszó jelenik meg a felügyeleti konzolon.

### <a name="administrator-security-analyst-and-read-only-user"></a>Rendszergazda, biztonsági elemző és csak olvasási jogosultsággal rendelkező felhasználó

Az írásvédett és a biztonsági elemzők nem tudják visszaállítani a saját jelszavukat, és a jelszavuk alaphelyzetbe állításához fel kell venniük a kapcsolatot a rendszergazdával, a támogatással vagy a CyberX szerepkörrel rendelkező felhasználóval. A rendszergazdának kapcsolatba kell lépnie a **CyberX** vagy a **támogatási** felhasználóval a jelszavuk alaphelyzetbe állításához.

A felhasználó jelszavának alaphelyzetbe állítása az érzékelőn:

1. A rendszergazdának, a támogatásnak vagy a CyberX szerepkör-felhasználónak be kell jelentkeznie az érzékelőbe.

1. Válassza a **felhasználók** lehetőséget a bal oldali panelen.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Válassza ki a felhasználó lehetőséget a bal oldali ablaktáblán.":::

1. Keresse meg a felhasználót, és válassza a **Szerkesztés** lehetőséget a **műveletek** legördülő menüből.

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Válassza a szerkesztés lehetőséget a műveletek legördülő menüből.":::

1. Adja meg az új jelszót az **új jelszóban, és** **erősítse meg az új jelszó** mezőket.

1. Válassza a **Frissítés** lehetőséget.

A felhasználó jelszavának alaphelyzetbe állítása a helyszíni felügyeleti konzolon:

1. A rendszergazdának, a támogatásnak vagy a CyberX szerepkör-felhasználónak be kell jelentkeznie az érzékelőbe.

1. Válassza a **felhasználók** lehetőséget a bal oldali panelen.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="A bal oldali panelen válassza a felhasználó lehetőséget.":::

1. Keresse meg a felhasználót, és kattintson a Szerkesztés ikonra :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: .

1. Adja meg az új jelszót az **új jelszóban, és** **erősítse meg az új jelszó** mezőket.

1. Válassza a **Frissítés** lehetőséget.

## <a name="see-also"></a>Lásd még

[Az érzékelő](how-to-activate-and-set-up-your-sensor.md) 
 aktiválása és beállítása [A helyszíni felügyeleti konzol](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 aktiválása és beállítása [Érzékelő tevékenység nyomon követése](how-to-track-sensor-activity.md)
