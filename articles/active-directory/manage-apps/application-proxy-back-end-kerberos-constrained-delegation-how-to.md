---
title: Kerberos által korlátozott delegálás – alkalmazásproxy
description: A Kerberos által korlátozott delegálási konfigurációk hibaelhárítása alkalmazásproxy esetében
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: b8562f3bdd82b5b0c2c1340f511f87ad90dfbe3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487950"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Az alkalmazásproxy Kerberos által korlátozott delegálási konfigurációinak hibaelhárítása

Az egyszeri bejelentkezésnek a közzétett alkalmazásokhoz való eléréséhez elérhető módszerek az egyik alkalmazásból a másikba változhatnak. Az egyik lehetőség, hogy a Azure Active Directory (Azure AD) alkalmazásproxy-ajánlat alapértelmezés szerint a Kerberos által korlátozott delegálás (KCD). Beállíthat egy összekötőt a felhasználók számára, hogy korlátozott Kerberos-hitelesítést futtasson a háttérbeli alkalmazásokhoz.

A KCD engedélyezésének eljárása egyszerű. Az egyszeri bejelentkezést támogató különböző összetevők és hitelesítési folyamatok általános megismerése nem igényel további tudnivalókat. Előfordulhat azonban, hogy a KCD SSO nem a várt módon működik. Ezeknek a forgatókönyveknek a megoldásához jó információforrásokra van szüksége.

Ez a cikk egy olyan hivatkozási pontot biztosít, amely segít a leggyakoribb problémák némelyikének hibaelhárításában és önkiszolgálásában. Az összetettebb megvalósítási problémák diagnosztizálását is magában foglalja.

Ez a cikk a következő feltételezéseket biztosítja:

- Az Azure AD Application Proxy üzembe helyezése az [alkalmazásproxy](application-proxy-add-on-premises-application.md) és az általános hozzáférés a nem KCD alkalmazásokhoz a várt módon működik.
- A közzétett célalkalmazás a Internet Information Services (IIS) és a Kerberos Microsoft-implementációján alapul.
- A kiszolgáló és az alkalmazás gazdagépei egyetlen Azure Active Directory tartományban találhatók. A tartományok közötti és erdőbeli forgatókönyvekkel kapcsolatos részletes információkért tekintse meg a [KCD szóló](https://aka.ms/KCDPaper)tanulmányt.
- A tárgy alkalmazás közzé van téve egy Azure-bérlőn, és engedélyezve van az előzetes hitelesítés. A felhasználóknak űrlapalapú hitelesítéssel kell hitelesíteniük magukat az Azure-ban. A részletes ügyfél-hitelesítési forgatókönyvek nem vonatkoznak ebben a cikkben. Előfordulhat, hogy a későbbiekben egy adott időpontban hozzá lehet adni őket.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD Application Proxy számos különböző típusú infrastruktúrába és környezetbe telepíthető. Az architektúrák a szervezettől a szervezetig változhatnak. A KCD kapcsolatos problémák leggyakoribb okai nem a környezetek. Az egyszerű helytelen konfiguráció vagy általános hibák okozzák a legtöbb problémát.

Ezért érdemes meggyőződni arról, hogy az összes előfeltétel teljesült a [KCD SSO és az alkalmazásproxy használatával a](application-proxy-configure-single-sign-on-with-kcd.md) hibaelhárítás megkezdése előtt. Jegyezze fel a Kerberos által korlátozott delegálás konfigurálása a 2012R2-ben című szakaszt. Ez a folyamat egy másik módszert alkalmaz a KCD konfigurálására a Windows korábbi verzióiban. A következő szempontokat is figyelembe kell venni:

- Nem ritka, hogy egy tartományi tag kiszolgálója egy biztonságos csatorna párbeszédpanelt nyit meg egy adott tartományvezérlővel (DC). Ezt követően a kiszolgáló bármikor átléphet egy másik párbeszédpanelre. Így az összekötő-gazdagépek nem korlátozódnak kizárólag adott helyi tartományvezérlővel folytatott kommunikációra.
- A tartományok közötti forgatókönyvek olyan átirányításokra támaszkodnak, amelyek az összekötő gazdagépét olyan tartományvezérlők számára irányítják, amelyek a helyi hálózat peremhálózati határain kívül lehetnek. Ezekben az esetekben ugyanilyen fontos a forgalom továbbítása is a más megfelelő tartományokat képviselő tartományvezérlők felé. Ha nem, a delegálás sikertelen lesz.
- Ha lehetséges, kerülje az összekötő gazdagépek és a tartományvezérlők közötti aktív IP-címek vagy azonosító eszközök elhelyezését. Ezek az eszközök időnként túlságosan zavaróak és akadályozzák az alapvető RPC-forgalmat.

A delegálás tesztelése egyszerű forgatókönyvekben. Minél több változót vezet be, annál többre lehet szükség. Az idő megtakarításához korlátozza a tesztelést egyetlen összekötőre. A probléma megoldása után adjon hozzá további összekötőket.

Bizonyos környezeti tényezők is hozzájárulhatnak a problémához. Ezen tényezők elkerülése érdekében a tesztelés során a lehető legnagyobb mértékben csökkentse az architektúrát. A helytelenül konfigurált belső tűzfal ACL-ek például gyakoriak. Ha lehetséges, küldjön egy összekötőtől érkező összes forgalmat a tartományvezérlők és a háttérbeli alkalmazás felé.

Az összekötők elhelyezkedésének legmegfelelőbb helye a lehető legpontosabb. Egy olyan tűzfal, amely a tesztelés során inline-t használ, felesleges bonyolultságot tesz elérhetővé, és meghosszabbíthatja a vizsgálatokat.

Mi a KCD probléma? Több gyakori jelzés is van arra vonatkozóan, hogy a KCD SSO sikertelen. A probléma első jelei a böngészőben jelennek meg.

![Képernyőkép, amely egy helytelen C D-konfigurációs hiba példáját mutatja, a "helytelen Kerberos által korlátozott delegálás..." hibaüzenettel. Kiemelt.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Példa: az engedélyezés a hiányzó engedélyek miatt nem sikerült](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Mindkét rendszerkép ugyanezt a tünetet mutatja: SSO-hiba. Az alkalmazáshoz való felhasználói hozzáférés megtagadva.

## <a name="troubleshooting"></a>Hibaelhárítás

A hibakeresés módja a probléma és a megfigyelt tünetektől függ. Mielőtt további lépéseket hozna, vizsgálja meg a következő cikkeket. Hasznos hibaelhárítási információkat biztosítanak:

- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)
- [Kerberos-hibák és-tünetek](application-proxy-troubleshoot.md#kerberos-errors)
- [Az egyszeri bejelentkezés használata, ha a helyszíni és a Felhőbeli identitások nem egyeznek](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Ha erre a pontra került, a fő probléma létezik. Az indításhoz válassza szét a folyamatot a következő három szakaszban, amelyeket elháríthat.

### <a name="client-pre-authentication"></a>Ügyfél előhitelesítése

A külső felhasználó egy böngészőben keresztül hitelesíti az Azure-t. Az Azure-ban való előzetes hitelesítés szükséges ahhoz, hogy a KCD SSO működjön. Ha problémák merülnek fel, tesztelje és foglalkozzon ezzel a képességgel. Az előhitelesítési szakasz nem kapcsolódik a KCD vagy a közzétett alkalmazáshoz. Az eltérések az Azure-ban való létezésének ellenőrzésével egyszerűen kioldhatók. Győződjön meg arról is, hogy nincs letiltva vagy blokkolva. A böngészőben megjelenő hiba elég leíró ahhoz, hogy elmagyarázza az okot. Ha bizonytalan, ellenőrizze a Microsoft egyéb hibaelhárítási cikkeit.

### <a name="delegation-service"></a>Delegálási szolgáltatás

Az Azure proxy-összekötő, amely Kerberos-szolgáltatási jegyet kap a felhasználóknak Kerberos-kulcsszolgáltató (KCD).

Az ügyfél és az Azure-beli előtér közötti külső kommunikáció nem befolyásolja a KCD. Ezek a kommunikációk csak akkor működnek, ha a KCD működik. Az Azure proxy szolgáltatás érvényes felhasználói azonosítót biztosít, amely a Kerberos-jegy beszerzésére szolgál. Ezen azonosító nélkül a KCD nem lehetséges, és sikertelen lesz.

Ahogy azt korábban említettük, a böngésző hibaüzenetei néhány jó dolgot jeleznek arról, hogy a dolgok miért sikertelenek. Ügyeljen arra, hogy a válaszban jegyezze fel a tevékenység-azonosítót és az időbélyeget. Ez az információ segít összekapcsolni a viselkedést az Azure proxy eseménynaplójának tényleges eseményeivel.

![Példa: helytelen KCD-konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Az eseménynaplóban megjelenő bejegyzések a 13019 vagy a 12027 eseményként jelennek meg. Az összekötő eseménynaplóinak megkeresése az **alkalmazások és szolgáltatások naplóiban naplózza** a &gt; **Microsoft** &gt; **AadApplicationProxy** - &gt; **összekötő** &gt; **rendszergazdáját**.

![13019-es esemény az alkalmazásproxy eseménynaplójában](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![12027-es esemény az alkalmazásproxy eseménynaplójában](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Használjon **egy** rekordot a belső DNS-ben az alkalmazás címeként, nem **CNAME**-ként.
1. Erősítse meg, hogy az összekötő gazdagép jogosult a kijelölt fiók SPN-re való delegálásra. Erősítse meg, hogy a **bármely hitelesítési protokoll használata** beállítás ki van választva. További információ: [SSO konfigurációs cikk](application-proxy-configure-single-sign-on-with-kcd.md).
1. Ellenőrizze, hogy létezik-e az egyszerű szolgáltatásnév egyetlen példánya az Azure AD-ben. Probléma a `setspn -x` parancssorban bármely tartományi tag gazdagépen.
1. Győződjön meg arról, hogy a tartományi házirend kényszerítve van, amely korlátozza a [kiállított Kerberos-tokenek maximális méretét](/archive/blogs/askds/maxtokensize-and-windows-8-and-windows-server-2012). Ez a szabályzat leállítja az összekötőt abban az esetben, ha túlzottnak találják a tokent.

Egy hálózati nyomkövetés, amely rögzíti az összekötő-gazdagép és a tartományi KDC közötti adatcserét, a következő legjobb lépés a problémával kapcsolatos alacsonyabb szintű részletek beszerzése. További információ: [Deep Dive – problémamegoldás](https://aka.ms/proxytshootpaper).

Ha a jegyek jól láthatók, a naplókban megjelenik egy esemény, amely szerint a hitelesítés sikertelen volt, mert az alkalmazás 401-as értéket adott vissza. Ez az esemény azt jelzi, hogy a célalkalmazás elutasította a jegyét. Lépjen a következő szakaszra.

### <a name="target-application"></a>Célalkalmazás

Az összekötő által biztosított Kerberos-jegy fogyasztója. Ezen a ponton várható, hogy az összekötő Kerberos szolgáltatási jegyet küld a háttérnek. Ez a jegy az első kérelmezési kérelem fejléce.

1. Az alkalmazás a portálon definiált belső URL-címének használatával ellenőrizze, hogy az alkalmazás elérhető-e közvetlenül az összekötő gazdagépének böngészőjében. Ezután sikeresen bejelentkezhet. A részletek az összekötő **– problémamegoldás** oldalon találhatók.
1. Továbbra is az összekötő gazdagépen ellenőrizze, hogy a böngésző és az alkalmazás közötti hitelesítés Kerberos protokollt használ-e. Válasszon a következő lehetőségek közül:
1. Futtasson DevTools (**F12**) az Internet Explorerben, vagy használja a [hegedűst](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) az összekötő-gazdagépről. Nyissa meg az alkalmazást a belső URL-cím használatával. Ellenőrizze az alkalmazás válaszában visszaadott, felkínált WWW-engedélyezési fejléceket, és győződjön meg arról, hogy az egyeztetés vagy a Kerberos van jelen.

   - A böngésző és az alkalmazás közötti válaszban visszaadott következő Kerberos-blob a **YII**-vel kezdődik. Ezek a levelek tájékoztatják, hogy a Kerberos fut. A Microsoft NT LAN Manager (NTLM) azonban mindig a **TlRMTVNTUAAB**-vel kezdődik, amely az NTLM biztonsági támogatási szolgáltatót (NTLMSSP) olvassa be a Base64-ből való dekódoláskor. Ha a blob elején látja a **TlRMTVNTUAAB** , a Kerberos nem érhető el. Ha nem látja a **TlRMTVNTUAAB**, a Kerberos valószínűleg elérhető.

      > [!NOTE]
      > Ha a hegedűst használja, ezzel a módszerrel átmenetileg le kell tiltania a kiterjesztett védelmet az alkalmazás konfigurációjában az IIS-ben.

      ![A böngésző hálózatának ellenőrzési ablaka](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Az ebben az ábrában található blob nem a **TIRMTVNTUAAB**-vel kezdődik. Így ebben a példában a Kerberos elérhető, és a Kerberos-blob nem a **YII**-vel kezdődik.

1. Ideiglenesen távolítsa el az NTLM-t az IIS-hely szolgáltatók listájáról. Az alkalmazást közvetlenül az Internet Explorerből érheti el az összekötő-gazdagépen. Az NTLM már nem szerepel a szolgáltatók listáján. Az alkalmazást csak Kerberos használatával érheti el. Ha a hozzáférés meghiúsul, lehet, hogy probléma van az alkalmazás konfigurációjával. A Kerberos-hitelesítés nem működik.

   - Ha a Kerberos nem érhető el, ellenőrizze az alkalmazás hitelesítési beállításait az IIS-ben. Győződjön meg arról, hogy az **egyeztetés** felül van listázva, az NTLM alatt. Ha nem szeretne **egyeztetni**, **Kerberos vagy egyeztetés** vagy **PKU2U**, folytassa csak akkor, ha a Kerberos működik.

     ![Windows-hitelesítési szolgáltatók](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - A Kerberos és az NTLM helyett ideiglenesen tiltsa le az alkalmazás előhitelesítését a portálon. Próbáljon meg hozzáférni az internetről a külső URL-cím használatával. A rendszer a hitelesítést kéri. Ezt az előző lépésben használt fiókkal is megteheti. Ha nem, akkor probléma van a háttérbeli alkalmazással, nem pedig a KCD.
   - Engedélyezze újra az előhitelesítést a portálon. Hitelesítés az Azure-ban az alkalmazás külső URL-címén keresztüli kapcsolódáshoz való kísérlettel. Ha az egyszeri bejelentkezés sikertelen, a böngészőben egy tiltott hibaüzenet jelenik meg, a naplóban pedig a 13022-es eseményt:

     *A Microsoft HRE Application proxy-összekötő nem tudja hitelesíteni a felhasználót, mert a háttér-kiszolgáló HTTP 401-hibával válaszol a Kerberos-hitelesítésre.*

      ![A HTTTP 401 tiltott hibáját mutatja](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Keresse meg az IIS-alkalmazást. Győződjön meg arról, hogy a konfigurált alkalmazáskészlet és az SPN úgy van konfigurálva, hogy ugyanazt a fiókot használja az Azure AD-ben. Navigáljon az IIS-ben az alábbi ábrán látható módon:

      ![IIS-alkalmazás konfigurációs ablaka](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Az identitás megismerése után ellenőrizze, hogy a fiók konfigurálva van-e a kérdéses SPN-ben. Például: `setspn –q http/spn.wacketywack.com`. Írja be a következő szöveget a parancssorba:

      ![A SetSPN parancssori ablak megjelenítése](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Győződjön meg arról, hogy a portálon az alkalmazás beállításaiban definiált SPN szerepel. Győződjön meg arról, hogy a cél Azure AD-fiókhoz konfigurált egyszerű szolgáltatásnevet az alkalmazás alkalmazáskészlete használja.

      ![SPN-konfiguráció a Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Nyissa meg az IIS-t, és válassza ki a **Configuration Editor** lehetőséget az alkalmazáshoz. Navigáljon a **System. webserver/Security/Authentication/windowsAuthentication**. Győződjön meg arról, hogy a **UseAppPoolCredentials** értéke **true (igaz**).

      ![IIS-konfiguráció alkalmazáskészletének hitelesítő adatai](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Módosítsa az értéket **igaz** értékre. A következő parancs futtatásával távolítsa el az összes gyorsítótárazott Kerberos-jegyet a háttér-kiszolgálóról:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

További információ: [a Kerberos-ügyfél Ticket gyorsítótárának kiürítése az összes munkamenethez](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Ha engedélyezi a kernel üzemmódot, az javítja a Kerberos-műveletek teljesítményét. Ez azonban azt is okozhatja, hogy a kért szolgáltatásnak a számítógépfiók használatával kell visszafejtenie a jegyet. Ezt a fiókot a helyi rendszernek is nevezik. Állítsa **igaz** értékre az KCD megadásához, ha az alkalmazás egy farmon belül több kiszolgálón fut.

- További ellenőrzésként tiltsa le a **kiterjesztett** védelmet is. Bizonyos esetekben a **kiterjesztett** védelem KCD tört ki, amikor az engedélyezve lett az adott konfigurációkban. Ezekben az esetekben az alkalmazás az alapértelmezett webhely almappájába került közzétételre. Ez az alkalmazás csak névtelen hitelesítésre van konfigurálva. Az összes párbeszédpanel szürkén jelenik meg, ami azt sugallja, hogy a gyermekobjektumok nem öröklik az aktív beállításokat. Javasoljuk, hogy tesztelje, de ne felejtse el visszaállítani ezt az **értéket, ha** lehetséges.

  Ez a további vizsgálat a közzétett alkalmazás használatának nyomon követését teszi lehetővé. Elindíthat további összekötőket is, amelyek delegálásra vannak konfigurálva. További információ: részletes technikai [útmutató, az Azure ad Application proxy hibaelhárítása](https://aka.ms/proxytshootpaper).

Ha továbbra sem tudja elérni a folyamatot, a Microsoft támogatási szolgálata segítséget nyújthat. Támogatási jegy létrehozása közvetlenül a portálon belül. A mérnök felveszi Önnel a kapcsolatot.

## <a name="other-scenarios"></a>Egyéb forgatókönyvek

- Az Azure Application proxy egy Kerberos-jegyet kér, mielőtt elküldené a kérelmét egy alkalmazásnak. Egyes harmadik féltől származó alkalmazások nem szeretik ezt a hitelesítési módszert. Ezek az alkalmazások a hagyományosabb tárgyalásokra várnak. Az első kérelem névtelen, amely lehetővé teszi az alkalmazás számára, hogy válaszoljon az általa támogatott hitelesítési típusokra 401-en keresztül. Az ilyen típusú Kerberos-egyeztetés a jelen dokumentumban ismertetett lépések használatával engedélyezhető: a Kerberos által [korlátozott delegálás az egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md).
- A Többugrásos hitelesítés általában olyan esetekben használatos, amikor egy alkalmazás többszintű, háttér-és előtér-szolgáltatással rendelkezik, ahol mindkét esetben hitelesítés szükséges, például SQL Server Reporting Services. További részletek: a [Kerberos által korlátozott delegálás konfigurálása webes igénylési proxy lapokhoz](/troubleshoot/windows-server/identity/configure-kerberos-constrained-delegation).

## <a name="next-steps"></a>Következő lépések

[KCD konfigurálása felügyelt tartományon](../../active-directory-domain-services/deploy-kcd.md).
