---
title: Ismerkedés az Azure MFA-kiszolgálóval – Azure Active Directory
description: Részletes bevezetés az Azure MFA Server a helyszínen történő használatába
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7bd9913cccbe077a4deed9a7c5bfdc601f3dd5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742340"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication-kiszolgáló – első lépések

<center>

![Első lépések a helyszíni MFA-kiszolgálóval](./media/howto-mfaserver-deploy/server2.png)</center>

Ezen a lapon a kiszolgáló új telepítését és helyszíni Active Directoryval való beállítását ismertetjük. Ha már telepített MFA-kiszolgálót és frissíteni szeretné, tekintse meg [a legújabb Azure Multi-Factor Authentication-kiszolgálóra való frissítéssel kapcsolatos](howto-mfaserver-deploy-upgrade.md) témakört. Ha csak a webszolgáltatás telepítésével kapcsolatban keres információt, tekintse meg [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](howto-mfaserver-deploy-mobileapp.md) című témakört.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

Az Azure Multi-Factor Authentication-kiszolgáló letöltése előtt fontolja meg, milyen terhelési és magas rendelkezésre állási követelményekkel rendelkezik. Ezen információ alapján döntse el, hogyan és hol szeretné üzembe helyezni.

A szükséges memória mennyiségének meghatározásához jó útmutató azoknak a felhasználóknak a száma, akik várhatóan rendszeresen hitelesítést fognak végezni.

| Felhasználók | RAM |
| ----- | --- |
| 1–10 000 | 4 GB |
| 10 001–50 000 | 8 GB |
| 50 001–100 000 | 12 GB |
| 100 000–200 001 | 16 GB |
| 200 001+ | 32 GB |

Szüksége van több kiszolgáló beállítására magas rendelkezésre álláshoz vagy terheléselosztáshoz? Az Azure MFA-kiszolgálóval több módon is beállíthatja ezt a konfigurációt. Az elsőként telepített Azure MFA-kiszolgálót lesz a főkiszolgáló. A további kiszolgálók alárendelt kiszolgálókká válnak, és automatikusan szinkronizálják a felhasználókat és a konfigurációt a főkiszolgálóval. Ekkor konfigurálhat egy elsődleges kiszolgálót, a többit pedig használhatja biztonsági másolathoz, vagy beállíthat terheléselosztást a kiszolgálók között.

Amikor egy fő Azure MFA-kiszolgálónak megszakad az internetes kapcsolata, az alárendelt kiszolgálók továbbra is feldolgozhatják a kétlépéses ellenőrzési kérelmeket. Új felhasználókat azonban nem vehet fel, és a meglévő felhasználók nem frissíthetik a beállításaikat, amíg a főkiszolgáló megint elérhetővé nem válik, vagy egy alárendelt kiszolgálót elő nem léptet.

### <a name="prepare-your-environment"></a>A környezet előkészítése

Győződjön meg róla, hogy az Azure Multi-Factor Authenticationhöz használt kiszolgáló megfelel a következő követelményeknek:

| Az Azure Multi-Factor Authentication-kiszolgáló követelményei | Description |
|:--- |:--- |
| Hardver |<li>200 MB merevlemez-terület</li><li>x32-es vagy x64-es verzió futtatására képes processzor</li><li>Legalább 1 GB RAM</li> |
| Szoftverek |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 (csak [EUME](/lifecycle/faq/extended-security-updates) -vel)</li><li>Windows 10</li><li>Windows 8.1, minden kiadás</li><li>Windows 8, minden kiadás</li><li>Windows 7, minden kiadás (csak [EUME](/lifecycle/faq/extended-security-updates) -vel)</li><li>Microsoft .NET-keretrendszer 4.0</li><li>IIS 7.0 vagy újabb a felhasználói portál vagy a web service SDK telepítésekor</li> |
| Engedélyek | Tartományi rendszergazda vagy vállalati rendszergazdai fiók Active Directoryhoz való regisztráláshoz |

### <a name="azure-mfa-server-components"></a>Az Azure MFA-kiszolgáló összetevői

Az Azure MFA-kiszolgáló három webösszetevőt tartalmaz:

* Web Service SDK – Lehetővé teszi a kommunikációt a többi összetevővel, és az Azure MFA-alkalmazáskiszolgálón van telepítve
* Felhasználói portál – egy olyan IIS-webhely, ahol a felhasználók regisztrálhatnak az Azure Multi-Factor Authenticationre (MFA), és karbantarthatják a fiókjaikat.
* Mobile App Web Service – Lehetővé teszi a Microsoft Authenticatorhoz hasonló mobilalkalmazások használatát a kétlépéses ellenőrzéshez.

Ha az adott kiszolgáló rendelkezik internetkapcsolattal, mindhárom összetevő telepíthető ugyanarra a kiszolgálóra. Ha szétválasztja az összetevőket, a Web Service SDK az Azure MFA-alkalmazáskiszolgálón, a Felhasználói portál és a Mobile App Web Service pedig az internetkapcsolattal rendelkező kiszolgálón lesz telepítve.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Az Azure Multi-Factor Authentication-kiszolgáló tűzfalkövetelményei

Mindegyik MFA-kiszolgálónak képesnek kell lennie kommunikálni a 443-as kimenő porton a következő címekkel:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Ha a kimenő tűzfalak korlátozva vannak a 443-as porton, nyissa meg a következő IP-címtartományokat:

| IP-alhálózat | Hálózati maszk | IP-címtartomány |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

Ha nem használja az eseménymegerősítési szolgáltatást, a felhasználók pedig nem használnak mobilalkalmazásokat az ellenőrzéshez a vállalati hálózaton található eszközökön, csak a következő tartományokra van szüksége:

| IP-alhálózat | Hálózati maszk | IP-címtartomány |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201 – 70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>Az MFA-kiszolgáló letöltése

Az alábbi lépéseket követve töltse le az Azure Multi-Factor Authentication-kiszolgálót az Azure Portalról:

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. A többtényezős hitelesítést (MFA) használó új ügyfeleknek a felhőalapú Azure AD-Multi-Factor Authentication kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat. A következő lépések csak akkor működnek, ha Ön már rendelkezik MFA-kiszolgáló felhasználóval.

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
2. Keresse meg és válassza ki az *Azure Active Directoryt*. Válassza a **biztonsági**  >  **MFA** elemet.
3. A **kezelő MFA-kiszolgáló** területen válassza a **kiszolgáló beállításai** lehetőséget.
4. Válassza a **Letöltés** lehetőséget, és kövesse a letöltési oldalon megjelenő utasításokat a telepítő mentéséhez. 

   ![MFA-kiszolgáló letöltése a Azure Portal](./media/howto-mfaserver-deploy/downloadportal.png)

5. Hagyja megnyitva ezt az oldalt, mivel a telepítő futtatása után még hivatkozni fogunk rá.

## <a name="install-and-configure-the-mfa-server"></a>Az MFA-kiszolgáló telepítése és konfigurálása

Most, hogy letöltötte a kiszolgálót, telepítse és konfigurálja azt. Győződjön meg róla, hogy az a kiszolgáló, amelyikre telepíti, megfelel-e a tervezési szakaszban felsorolt követelményeknek.

1. Kattintson duplán a végrehajtható fájlra.
2. A telepítési mappa kiválasztása képernyőn győződjön meg arról, hogy a mappa helyes, és kattintson a **tovább** gombra.
3. A telepítés befejezése után kattintson a **Befejezés** gombra. Ekkor elindul a konfigurációs varázsló.
4. A konfigurációs varázsló üdvözlőképernyőjén jelölje be **A hitelesítéskonfiguráló varázsló használatának kihagyása** jelölőnégyzetet, és kattintson a **Tovább** gombra. Bezáródik a varázsló, és elindul a kiszolgáló.

   ![Kihagyás a hitelesítési konfiguráció varázsló használatával](./media/howto-mfaserver-deploy/skip2.png)

5. Azon az oldalon, ahonnan letöltötte a kiszolgálót, kattintson az **Aktiváló hitelesítő adatok előállítása** gombra. Másolja ezt az információt az Azure MFA-kiszolgálón a megfelelő mezőkbe, majd kattintson az **Aktiválás** lehetőségre.

> [!NOTE]
> Csak a globális rendszergazdák képesek az aktiválási hitelesítő adatok előállítására a Azure Portalban.

## <a name="send-users-an-email"></a>E-mail küldése a felhasználóknak

A bevezetés megkönnyítése érdekében engedélyezze, hogy az MFA-kiszolgáló kommunikálhasson a felhasználóival. Az MFA-kiszolgáló tájékoztató e-mailt küld a felhasználóknak arról, hogy regisztrálta őket a kétlépéses ellenőrzési szolgáltatásban.

Az elküldött e-mail tartalmát az határozza meg, hogyan konfigurálja a felhasználókat a kétlépéses ellenőrzéshez. Ha például tud telefonszámokat importálni a vállalat címtárából, az e-mailnek tartalmaznia kell az alapértelmezett telefonszámokat, így a felhasználók tudni fogják, hogy mire számítsanak. Ha nem importál telefonszámokat, vagy ha a felhasználói a mobilalkalmazást fogják használni, olyan e-mailt küldjön a felhasználóknak, amely a fiók regisztrációjának befejezésére kéri őket. Az e-mail tartalmazzon egy hivatkozást az Azure Multi-Factor Authentication felhasználói portáljára.

Az e-mail tartalma a felhasználóhoz beállított ellenőrzési módszertől is függ (telefonhívás, SMS vagy mobilalkalmazás). Ha például a felhasználónak PIN-kódot kell használnia a hitelesítéskor, az e-mail tartalmazza a kezdeti PIN-kódot. A felhasználóknak általában az első ellenőrzéskor módosítaniuk kell a PIN-kódot.

### <a name="configure-email-and-email-templates"></a>E-mailek és e-mail-sablonok konfigurálása

Kattintson a bal oldalon található e-mail ikonra az e-mailek küldésére vonatkozó beállítások megadásához. Ezen az oldalon adhatja meg a levelezési kiszolgáló SMTP-adatait, és innen küldhet e-maileket az **E-mailek küldése a felhasználóknak** jelölőnégyzet bejelölésével.

![MFA-kiszolgáló – E-mail-konfiguráció](./media/howto-mfaserver-deploy/email1.png)

Az E-mail tartalma lapon áttekintheti a választható e-mail-sablonokat. Válassza a leginkább megfelelő sablont attól függően, hogyan konfigurálta a felhasználókat a kétlépéses ellenőrzés végrehajtására.

![MFA-kiszolgálói E-mail sablonok a konzolon](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Felhasználók importálása az Active Directoryból

A kiszolgáló telepítése után a felhasználók hozzáadása következik. Létrehozhatja őket manuálisan, importálhatja az Active Directory-felhasználókat, vagy beállíthatja az Active Directoryval történő automatikus szinkronizálást.

### <a name="manual-import-from-active-directory"></a>Manuális importálás az Active Directoryból

1. Az Azure MFA-kiszolgálón a bal oldalon válassza a **Felhasználók** elemet.
2. Alul válassza az **Importálás Active Directoryból** lehetőséget.
3. Most kereshet egyes felhasználókat vagy kereshet az AD címtárban felhasználókat tartalmazó szervezeti egységeket. Ebben az esetben meg kell adni a felhasználók szervezeti egységét.
4. Jelölje ki az összes felhasználót a jobb oldalon, és kattintson az **Importálás** gombra. Megjelenik egy előugró ablak, amely a művelet sikerességéről értesít. Zárja be az importálási ablakot.

   ![MFA-kiszolgáló felhasználójának importálása Active Directory](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Automatikus szinkronizálás az Active Directoryval

1. Az Azure MFA-kiszolgálón válassza a **Címtár-integráció** elemet a bal oldalon.
2. Lépjen a **Szinkronizálás** lapra.
3. Válassza az alul lévő **Hozzáadás** lehetőséget
4. A megjelenő **Szinkronizálási elem hozzáadása** mezőben válassza az ehhez a szinkronizálási feladathoz tartozó Tartomány, Szervezeti egység **vagy** biztonsági csoport, Beállítások, Módszer alapértelmezett beállításai vagy Nyelv alapértelmezett beállításai lehetőséget, és kattintson a **Hozzáadás** gombra.
5. Jelölje be a **Active Directory-szinkronizálás engedélyezése** jelölőnégyzetet, majd válassza ki a **Szinkronizálási időköz** értékét egy perc és 24 óra között.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>A felhasználói adatok kezelése az Azure Multi-Factor Authentication-kiszolgálón

Ha a helyszíni Multi-Factor Authentication-(MFA-) kiszolgálót használja, a rendszer a helyi kiszolgálókon tárolja a felhasználó által tárolt adatfájlokat. A felhőben nincsenek állandó felhasználói adatok. Amikor a felhasználó kétlépéses ellenőrzést végez, az MFA-kiszolgáló elküldi az adatokat az Azure MFA-felhőszolgáltatásnak az ellenőrzés végrehajtásához. Amikor a rendszer ezeket a hitelesítési kéréseket elküldi a felhőszolgáltatásnak, a kérések és a naplók a következő mezőket tartalmazzák, hogy azok elérhetők legyenek az ügyfél hitelesítési/használati jelentéseiben. Egyes mezők megadása nem kötelező, így engedélyezhetők vagy letilthatók a Multi-Factor Authentication-kiszolgálón. Az MFA-kiszolgáló és az MFA-felhőszolgáltatás közötti kommunikáció SSL-/TLS-titkosítást használ a 443-as kimenő porton. Ezek a mezők a következők:

* Egyedi azonosító – felhasználónév vagy belső MFA-kiszolgálói azonosító
* Utónév és vezetéknév (nem kötelező)
* E-mail-cím (nem kötelező)
* Telefonszám – hanghívások vagy SMS-hitelesítés esetén
* Eszköztoken – mobilalkalmazásos hitelesítés esetén
* Hitelesítési módszer
* Hitelesítés eredménye
* MFA-kiszolgáló neve
* MFA-kiszolgáló IP-címe
* Ügyfél IP-címe – ha elérhető

A fenti mezőkön kívül a rendszer az ellenőrzési eredményeket (sikeres/elutasítva) és az elutasítások okait is tárolja a hitelesítési adatokkal együtt. Ezek az adatok a hitelesítési/használati jelentésekben érhetők el.

> [!IMPORTANT]
> 2019 márciusában a telefonhívási lehetőségek nem lesznek elérhetők az MFA-kiszolgáló felhasználói számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

## <a name="back-up-and-restore-azure-mfa-server"></a>Az Azure MFA-kiszolgáló biztonsági mentése és visszaállítása

A biztonsági mentés megfelelőségének ellenőrzése minden rendszeren fontos lépés.

Az Azure MFA-kiszolgáló biztonsági mentéséhez győződjön meg arról, hogy rendelkezik másolati példánnyal a **C:\Program Files\Multi-Factor Authentication Server\Data** mappáról, beleértve a **PhoneFactor.pfdata** fájlt is. 

Ha visszaállításra lenne szükség, a következő lépésekkel hajthatja végre:

1. Telepítse újra az Azure MFA-kiszolgálót egy új kiszolgálón.
2. Aktiválja az új Azure MFA-kiszolgálót.
3. Állítsa le a **MultiFactorAuth** szolgáltatást.
4. Írja felül a **PhoneFactor.pfdata** fájlt a biztonsági másolati példánnyal.
5. Indítsa el a **MultiFactorAuth** szolgáltatást.

Az új kiszolgáló ezzel használatra kész az eredeti biztonsági másolatban található konfigurációval és felhasználói adatokkal.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>A TLS/SSL-protokollok és titkosítócsomagok kezelése

Miután az MFA Server 8.x vagy újabb változatára frissített, érdemes eltávolítani minden régebbi vagy gyengébb titkosítócsomagot, hacsak vállalata nem követeli meg azok használatát. A tennivalókat az [AD FS által használt SSL/TLS-protokollok és titkosítócsomagok kezelését](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) ismertető rész tartalmazza.

## <a name="next-steps"></a>Következő lépések

- A [felhasználói portál](howto-mfaserver-deploy-userportal.md) telepítése és konfigurálása a felhasználói önkiszolgáló funkciókhoz.
- Az Azure MFA-kiszolgáló telepítése és konfigurálása [Active Directory összevonási szolgáltatásokkal](multi-factor-authentication-get-started-adfs.md), [RADIUS-hitelesítéssel](howto-mfaserver-dir-radius.md) vagy [LDAP-hitelesítéssel](howto-mfaserver-dir-ldap.md).
- [Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md) telepítése és konfigurálása.
- [Telepítse az Azure multi-Factor Authentication-kiszolgáló Mobile App Web Service](howto-mfaserver-deploy-mobileapp.md)-t.
- [Speciális forgatókönyvek az Azure multi-Factor Authentication és a harmadik féltől származó virtuális magánhálózatok esetében](howto-mfaserver-nps-vpn.md).