---
title: Helyszíni Azure AD jelszavas védelem – gyakori kérdések
description: Tekintse át az Azure AD jelszavas védelmével kapcsolatos gyakori kérdéseket helyszíni Active Directory Domain Services környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625128"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD jelszavas védelem a helyszínen – gyakori kérdések

Ez a szakasz az Azure AD jelszavas védelmével kapcsolatos gyakran feltett kérdésekre ad választ.

## <a name="general-questions"></a>Általános kérdések

**K: milyen útmutatást kell adni a felhasználóknak a biztonságos jelszó kiválasztásához?**

A jelen témakörben a Microsoft aktuális útmutatója a következő hivatkozásra kattintva érhető el:

[Microsoft-jelszó – útmutató](https://www.microsoft.com/research/publication/password-guidance)

**K: a helyszíni Azure AD jelszavas védelem támogatott a nem nyilvános felhőkben?**

A helyszíni Azure AD jelszavas védelmet a nyilvános felhőben és az Arlington-felhőben is támogatja. A rendelkezésre álláshoz nem jelentettek be dátumot más felhőkben.

Az Azure AD-portál lehetővé teszi, hogy a nem támogatott felhőkben még a helyszíni "jelszavas védelem a Windows Server Active Directory konfigurációjában" beállítást is módosítsa. ezeket a módosításokat megőrzi a rendszer, de más esetben soha nem lép érvénybe. A helyszíni proxy ügynökök vagy erdők regisztrálása nem támogatott a nem támogatott felhőkben, és az ilyen regisztrációs kísérletek mindig sikertelenek lesznek.

**K: Hogyan alkalmazhatom az Azure AD jelszavas védelem előnyeit a helyszíni felhasználók egy részhalmazára?**

Nem támogatott. A üzembe helyezés és az engedélyezés után az Azure AD jelszavas védelme nem tesz különbséget – az összes felhasználó egyenlő biztonsági előnyöket kap.

**K: mi a különbség a jelszó módosítása és a jelszó beállítása (vagy alaphelyzetbe állítás) között?**

A jelszó módosítása akkor történik meg, amikor egy felhasználó új jelszót választ, miután igazolta, hogy ismeri a régi jelszót. A jelszó módosítása például az, hogy mi történik, amikor egy felhasználó bejelentkezik a Windowsba, és a rendszer kéri, hogy válasszon ki egy új jelszót.

A jelszó beállítása (más néven jelszó-visszaállítás) az, amikor egy rendszergazda új jelszóval helyettesíti egy fiók jelszavát, például a Active Directory felhasználók és számítógépek felügyeleti eszköz használatával. Ehhez a művelethez magas szintű jogosultság szükséges (általában tartományi rendszergazda), és a műveletet végző személy általában nem ismeri a régi jelszót. Az ügyfélszolgálati forgatókönyvek gyakran jelszavas készleteket végeznek, például olyan felhasználók számára, akik elfelejtették a jelszavukat. A jelszó-megadási eseményeket is látni fogja, ha új felhasználói fiókot hoz létre első alkalommal egy jelszóval.

A jelszó-ellenőrzési házirend ugyanúgy viselkedik, függetlenül attól, hogy megtörtént-e a jelszó módosítása vagy beállítása. Az Azure AD Password Protection DC Agent szolgáltatás különböző eseményeket naplóz, hogy megtudja, van-e jelszó-módosítási vagy-beállítási művelet.  Lásd: [Az Azure ad jelszavas védelem figyelése és naplózása](./howto-password-ban-bad-on-premises-monitor.md).

**K: az Azure AD jelszavas védelme a telepítés után érvényesíti a meglévő jelszavakat?**

Nem – az Azure AD jelszavas védelme csak jelszó-módosítási vagy-beállítási művelet esetén képes a jelszavas házirendeket a titkosítatlan jelszavakon érvényesíteni. Miután a Active Directory fogadta el a jelszót, a rendszer csak a jelszó hitelesítési protokoll-specifikus kivonatait őrzi meg. A tiszta szöveges jelszó soha nem marad meg, ezért az Azure AD jelszavas védelme nem tudja érvényesíteni a meglévő jelszavakat.

Az Azure AD jelszavas védelem kezdeti üzembe helyezése után az összes felhasználó és fiók végül egy Azure AD jelszavas védelem-érvényesített jelszót használ, mivel a meglévő jelszavaik általában az idő múlásával lejárnak. Ha szükséges, ezt a folyamatot a felhasználói fiókok jelszavainak egyszeri manuális lejáratával gyorsíthatja fel.

A "jelszó soha nem jár le" beállítással konfigurált fiókok soha nem lesznek kényszerítve a jelszavuk módosítására, kivéve, ha a manuális lejáratra sor kerül.

**K: miért történik a duplikált jelszó-elutasítási események naplózása, amikor gyenge jelszót próbál beállítani a Active Directory felhasználók és számítógépek kezelése beépülő modullal?**

A Active Directory felhasználók és számítógépek kezelése beépülő modul először megpróbálja beállítani az új jelszót a Kerberos protokoll használatával. Ha hiba történik, a beépülő modul egy második kísérletet tesz a jelszó megadására egy örökölt (SAM RPC) protokoll használatával (a használt protokollok nem fontosak). Ha az új jelszót az Azure AD jelszavas védelme gyengenak tekinti, ez a beépülő modul viselkedése két, a jelszó-visszaállítási elutasítási esemény naplózása után következik be.

**K: miért történik az Azure AD jelszavas védelem jelszavas védelmének ellenőrzése egy üres felhasználónévvel?**

A Active Directory támogatja a jelszavak tesztelését, így ellenőrizheti, hogy a tartomány aktuális jelszó-összetettségi követelményeit adja-e át, például a [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API használatával. Ha a rendszer ily módon érvényesíti a jelszót, a tesztelés a jelszó-szűrő-DLL-alapú termékek (például az Azure AD jelszavas védelem) érvényesítését is magában foglalja, de a jelszó-szűrési dll-hez átadott felhasználónevek üresek lesznek. Ebben az esetben az Azure AD jelszavas védelme a jelenleg érvényben lévő jelszóházirend használatával továbbra is érvényesíti a jelszót, és egy eseménynapló-üzenetet ad ki az eredmény rögzítéséhez, azonban az Eseménynapló-üzenetben üres lesz a Felhasználónév mező.

**K: támogatott az Azure AD jelszavas védelem az egyéb jelszó-szűrő alapú termékekkel való párhuzamos telepítésére?**

Igen. A több regisztrált jelszó-szűrési dll-fájl támogatása egy alapvető Windows-szolgáltatás, amely nem jellemző az Azure AD jelszavas védelmére. A jelszó fogadása előtt minden regisztrált jelszó-szűrő dll-fájlnak meg kell egyeznie.

**K: Hogyan telepíthetem és konfigurálom az Azure AD jelszavas védelmet a Active Directory környezetben az Azure használata nélkül?**

Nem támogatott. Az Azure AD jelszavas védelme egy olyan Azure-szolgáltatás, amely támogatja a helyszíni Active Directory környezetbe való kiterjesztését.

**K: Hogyan változtathatom meg a szabályzat tartalmát a Active Directory szinten?**

Nem támogatott. A szabályzat csak az Azure AD-portál használatával felügyelhető. Lásd még az előző kérdést is.

**K: Miért szükséges a DFSR a SYSVOL-replikációhoz?**

A fájlreplikációs szolgáltatás (a DFSR megelőző technológia) számos ismert problémát tartalmaz, és a Windows Server újabb verzióiban nem támogatott. Active Directory. Az Azure AD jelszavas védelem zéró tesztelését a fájlreplikációs szolgáltatás által konfigurált tartományokon hajtja végre.

További információt a következő cikkekben talál:

[A SYSVOL-replikáció DFSR való áttelepítésének esete](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[A Befejezés közel van a fájlreplikációs szolgáltatáshoz](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Ha a tartomány még nem használja a DFSR-t, akkor az Azure AD jelszavas védelem telepítése előtt át kell telepítenie a DFSR használatára. További információt a következő hivatkozásra kattintva talál:

[SYSVOL-replikáció áttelepítési útmutatója: FRS – Elosztott fájlrendszer replikációs szolgáltatása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Az Azure AD jelszavas védelem TARTOMÁNYVEZÉRLŐi ügynöke jelenleg a SYSVOL-replikációhoz használt FRS-t használó tartományok tartományvezérlőjén települ, de a szoftver nem fog megfelelően működni ebben a környezetben. További negatív mellékhatások például az egyes fájlok replikálásának meghiúsulása, a SYSVOL visszaállítási eljárásai pedig sikeresek, de az összes fájl replikálásának csendes sikertelensége esetén sikertelenek lesznek. A tartományt a lehető leghamarabb telepítse át a DFSR használatára, mind a DFSR rejlő előnyeit, mind pedig az Azure AD jelszavas védelem üzembe helyezésének feloldását is. A szoftver jövőbeli verziói automatikusan le lesznek tiltva, ha olyan tartományban fut, amely továbbra is FÁJLREPLIKÁCIÓS szolgáltatást használ.

**K: mekkora lemezterületre van szükség a szolgáltatás a tartományi SYSVOL megosztáson?**

A pontos lemezterület-használat változhat, mivel ez olyan tényezőktől függ, mint a tiltott tokenek száma és hossza a Microsoft globálisan tiltott listájában, valamint a bérlői egyéni listán, valamint a titkosítási terhelést is. Ennek a listának a tartalma valószínűleg növekedni fog a jövőben. Ennek szem előtt tartásával ésszerű elvárás, hogy a szolgáltatásnak legalább öt (5) megabájt lemezterülettel kell rendelkeznie a tartomány SYSVOL-megosztásán.

**K: Miért szükséges újraindítás a DC-ügynök szoftverének telepítéséhez vagy frissítéséhez?**

Ezt a követelményt az alapvető Windows-viselkedés okozza.

**K: van mód arra, hogy a tartományvezérlő-ügynököt egy adott proxykiszolgáló használatára konfigurálja?**

Nem. Mivel a proxykiszolgáló állapota nem megfelelő, nem fontos, hogy a rendszer mely konkrét proxykiszolgálót használja.

**K: rendben van az Azure AD-beli jelszavas védelem proxy szolgáltatásának üzembe helyezése más szolgáltatásokkal, például Azure AD Connectokkal?**

Igen. Az Azure AD jelszavas védelmi proxy szolgáltatás és Azure AD Connect soha nem ütköznek közvetlenül egymással.

Sajnos inkompatibilitás található az Azure AD jelszavas védelmi proxy szoftverrel és a [Azure Active Directory Application proxy](../manage-apps/application-proxy.md) szoftver által telepített szolgáltatás által telepített Microsoft Azure ad összekapcsoló ügynök-frissítési szolgáltatás verziójával. Ez a kompatibilitási megoldás azt eredményezheti, hogy az ügynök-frissítési szolgáltatás nem tud kapcsolódni az Azure-hoz a szoftverfrissítésekért. Az Azure AD jelszavas védelmi proxy és a Azure Active Directory Application Proxy ugyanarra a gépre való telepítése nem ajánlott.

**K: milyen sorrendben kell telepíteni és regisztrálni a DC-ügynököket és-proxykat?**

A proxy Agent telepítésének, a DC-ügynök telepítésének, az erdő regisztrálásának és a proxy regisztrációnak a rendezése támogatott.

**K: Aggódom a tartományvezérlők a szolgáltatás üzembe helyezésével kapcsolatos teljesítménybeli találatok esetén?**

Az Azure AD jelszavas védelem-tartományvezérlő ügynöke nem befolyásolja jelentős mértékben a tartományvezérlő teljesítményét egy meglévő kifogástalan állapotú Active Directory üzemelő példányban.

A legtöbb Active Directory központi telepítéshez tartozó jelszó-módosítási művelet a teljes munkaterhelés kis hányada az adott tartományvezérlőn. Tegyük fel például, hogy egy Active Directory tartomány 10000 felhasználói fiókkal és egy 30 napos MaxPasswordAge-házirenddel van beállítva. Ez a tartomány átlagosan 10 000/30 = ~ 333 jelszó-módosítási műveletet lát el naponta, ami egy kis számú művelet akár egyetlen tartományvezérlő esetében is. Tekintse át a lehetséges legrosszabb esetet: tegyük fel, hogy a ~ 333 jelszó módosításait egyetlen TARTOMÁNYVEZÉRLŐn hajtották végre egy órán belül. Ez a forgatókönyv például akkor fordulhat elő, ha sok alkalmazott dolgozik hétfőn reggel. Még ebben az esetben is a ~ 333/60 perc = hat jelszó módosítása percenként, ami nem jelentős terhelés.

Ha azonban az aktuális tartományvezérlők már csak teljesítmény-korlátozott szinten futnak (például maxed a CPU-ra, lemezterületre, lemezes I/O-re stb.), ajánlott további tartományvezérlőket hozzáadni vagy kibővíteni a rendelkezésre álló lemezterületet a szolgáltatás telepítése előtt. Lásd még a fenti, SYSVOL lemezterület-használatról szóló kérdést.

**K: szeretném tesztelni az Azure AD jelszavas védelmet a saját tartományában található néhány tartományvezérlőn. Lehetséges a felhasználói jelszó módosításának kényszerítése az adott tartományvezérlők használatára?**

Nem. A Windows ügyfél operációs rendszere határozza meg, hogy melyik tartományvezérlőt használja a rendszer, amikor a felhasználó megváltoztatja a jelszavát. A tartományvezérlő kiválasztására olyan tényezők alapján van kiválasztva, mint például a Active Directory hely és az alhálózati hozzárendelések, a környezet-specifikus hálózati konfiguráció stb. Az Azure AD jelszavas védelme nem szabályozza ezeket a tényezőket, és nem befolyásolhatja, hogy melyik tartományvezérlő van kiválasztva a felhasználó jelszavának módosításához.

A cél részleges elérésének egyik módja az Azure AD jelszavas védelem üzembe helyezése az adott Active Directory-hely összes tartományvezérlőjén. Ez a módszer ésszerű lefedettséget biztosít a helyhez hozzárendelt Windows-ügyfelek számára, ezért az ügyfeleknek bejelentkezett felhasználókra és a jelszavuk módosítására is érvényes lesz.

**K: Ha az Azure AD jelszavas védelem DC Agent szolgáltatást csak az elsődleges tartományvezérlőn (PDC) telepíti, a rendszer a tartomány összes többi tartományvezérlőjén is védeni fogja?**

Nem. Ha egy felhasználó jelszava megváltozik egy adott nem PDC-alapú tartományvezérlőn, a rendszer soha nem továbbítja a tiszta szöveges jelszót az elsődleges tartományvezérlőnek (ez az ötlet egy gyakori helytelen érzékelés). Miután elfogadták az új jelszót egy adott TARTOMÁNYVEZÉRLŐn, a tartományvezérlő ezt a jelszót használja az adott jelszó különböző hitelesítési protokoll-specifikus kivonatának létrehozásához, majd megőrzi ezeket a kivonatokat a címtárban. A tiszta szöveges jelszó nem marad meg. A frissített kivonatok ezután replikálódnak az elsődleges tartományvezérlőre. Bizonyos esetekben előfordulhat, hogy a felhasználói jelszavakat közvetlenül az elsődleges tartományvezérlőn változtatják meg, a különböző tényezőktől, például a hálózati topológiától és a Active Directory hely kialakítástól függően. (Lásd az előző kérdést.)

Összefoglalva, az Azure AD jelszavas védelem DC Agent szolgáltatásának az elsődleges tartományvezérlőn való üzembe helyezéséhez szükség van a szolgáltatás 100%-os biztonsági lefedettségének elérésére a tartományon belül. A szolgáltatás csak az elsődleges tartományvezérlőn való telepítése nem biztosít Azure AD jelszavas védelmet a tartomány bármely más tartományvezérlőjén.

**K: az egyéni intelligens zárolás miért nem működik még azután is, hogy az ügynököket a helyszíni Active Directory környezetbe telepítették?**

Az egyéni intelligens zárolás csak az Azure AD-ben támogatott. Az Azure AD-portál egyéni intelligens zárolási beállításainak módosításai nincsenek hatással a helyszíni Active Directory környezetre, még a telepített ügynökökkel is.

**K: System Center Operations Manager felügyeleti csomag elérhető az Azure AD jelszavas védelméhez?**

Nem.

**K: az Azure AD továbbra is elutasítja a gyenge jelszavakat, bár úgy konfiguráltam, hogy a házirend naplózási módban legyen?**

A naplózási mód csak a helyszíni Active Directory környezetekben támogatott. Az Azure AD implicit módon mindig "kényszerítve" módban van, amikor kiértékeli a jelszavakat.

**K: a felhasználók a hagyományos Windows-hibaüzenetet látják, ha az Azure AD jelszavas védelme visszautasítja a jelszót. Lehet testreszabni ezt a hibaüzenetet, hogy a felhasználók tudják, mi történt valójában?**

Nem. A felhasználók által a tartományvezérlő által a jelszó elutasításakor megjelenő hibaüzenetet az ügyfélszámítógép nem a tartományvezérlő vezérli. Ez a viselkedés megtörténik, hogy a rendszer elutasítja-e a jelszót az alapértelmezett Active Directory jelszóházirend vagy egy jelszó-szűrő alapú megoldás, például az Azure AD jelszavas védelme.

## <a name="password-testing-procedures"></a>Jelszó-tesztelési eljárások

Előfordulhat, hogy a szoftver megfelelő működésének ellenőrzése és a [jelszó-értékelési algoritmus](concept-password-ban-bad.md#how-are-passwords-evaluated)jobb megismerése érdekében érdemes elvégeznie a különböző jelszavak alapvető tesztelését. Ez a szakasz egy olyan metódust vázol fel, amely megismételhető eredmények előállítására szolgál.

Miért szükséges az ilyen lépések követése? Több tényezőt is megnehezítik a helyszíni Active Directory környezetben a jelszavak ellenőrzött, ismételhető tesztelésének elvégzéséhez:

* A rendszer az Azure-ban konfigurálja és megőrzi a jelszóházirend beállításait, a helyszíni tartományvezérlő ügynöke pedig rendszeresen szinkronizálja a házirend másolatait egy lekérdezési mechanizmus használatával. A lekérdezési ciklusban rejlő késés zavart eredményezhet. Ha például az Azure-ban konfigurálja a szabályzatot, de elfelejti, hogy szinkronizálja a DC-ügynökkel, akkor előfordulhat, hogy a tesztek nem eredményezik a várt eredményeket. A lekérdezési időköz jelenleg óránként egyszer hardcoded, de a házirend módosításainak közötti várakozási idő nem ideális az interaktív tesztelési forgatókönyvekhez.
* Ha egy új jelszóházirend le lett szinkronizálva egy tartományvezérlőre, több késés lép fel, miközben más tartományvezérlőkre replikálja a rendszer. Ezek a késések váratlan eredményekhez vezethetnek, ha olyan tartományvezérlőn próbál meg jelszót váltani, amely még nem kapta meg a szabályzat legújabb verzióját.
* A jelszó-változtatások felhasználói felületen keresztüli tesztelése megnehezíti az eredmények megbízhatóságát. Például egyszerűen helytelenül írhatja be a jelszót egy felhasználói felületre, különösen azért, mert a legtöbb jelszó felhasználói felülete elrejti a felhasználói bevitelt (például a Windows CTRL-ALT-DELETE-> jelszó felhasználói felületének módosítása).
* Nem lehet szigorúan szabályozni, hogy melyik tartományvezérlőt használja a rendszer a jelszó-változások tartományhoz csatlakozó ügyfelekről történő tesztelésekor. A Windows ügyfél operációs rendszere olyan tényezők alapján kiválaszt egy tartományvezérlőt, mint például a Active Directory hely és az alhálózati hozzárendelések, a környezet-specifikus hálózati konfiguráció stb.

Ezen problémák elkerülése érdekében az alábbi lépések a jelszó alaphelyzetbe állításakor a tartományvezérlőre való bejelentkezés során végrehajtott parancssori tesztelésen alapulnak.

> [!WARNING]
> Ezeket az eljárásokat csak tesztkörnyezetben kell használni, mivel az összes bejövő jelszó módosítása és alaphelyzetbe állítása érvényesítés nélkül, a tartományvezérlő ügynök szolgáltatás leállítása után, valamint a tartományvezérlővé való beléptetés során felmerülő megnövekedett kockázat elkerülésére is vonatkozik.

A következő lépések azt feltételezik, hogy legalább egy tartományvezérlőre telepítette a DC ügynököt, legalább egy proxyt telepített, és a proxyt és az erdőt is regisztrálta.

1. Jelentkezzen be a tartományvezérlőre tartományi rendszergazdai hitelesítő adatokkal (vagy más olyan hitelesítő adatokkal, amelyek megfelelő jogosultsággal rendelkeznek a tesztelési felhasználói fiókok létrehozásához és a jelszavak visszaállításához), amelyen telepítve van a DC Agent szoftver, és a rendszer újraindította a szolgáltatást.
1. Nyissa meg a Eseménynaplót, és navigáljon a [DC Agent rendszergazdai eseménynaplóba](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log).
1. Nyisson meg egy rendszergazda jogú parancssort.
1. Tesztelési fiók létrehozása a jelszó-teszteléshez

   A felhasználói fiókok számos módon hozhatók létre, de az ismétlődő tesztelési ciklusok során könnyen elérhetővé teheti a parancssori kapcsolót:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   Az alábbi vitafórumhoz tegyük fel, hogy létrehozott egy "ContosoUser" nevű teszt fiókot, például:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Nyisson meg egy webböngészőt (Előfordulhat, hogy a tartományvezérlő helyett külön eszközt kell használnia), jelentkezzen be a [Azure Portalba](https://portal.azure.com), és keresse meg Azure Active Directory > biztonsági > hitelesítési módszereit > jelszavas védelem.
1. Módosítsa az Azure AD jelszavas védelmi szabályzatot a végrehajtani kívánt teszteléshez szükséges módon.  Dönthet például úgy, hogy a kényszerített vagy a naplózási módot konfigurálja, vagy dönthet úgy, hogy módosítja a tiltott kifejezések listáját az egyéni tiltott jelszavak listájában.
1. Szinkronizálja az új szabályzatot a DC Agent szolgáltatás leállításával és újraindításával.

   Ez a lépés többféleképpen is elvégezhető. Az egyik módszer a Service Management felügyeleti konzol használata. ehhez kattintson a jobb gombbal az Azure AD Password Protection DC Agent szolgáltatásra, és válassza az újraindítás lehetőséget. Egy másik módszer is elvégezhető a parancssori ablakban, például:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. A Eseménynapló ellenőrizze, hogy az új szabályzat le lett-e töltve.

   Minden alkalommal, amikor a DC Agent szolgáltatás leáll és elindult, az 2 30006 eseményt kell megjelennie. Az első 30006 esemény a SYSVOL-megosztás lemezén gyorsítótárazott szabályzatot tükrözi. A második 30006-as eseménynek (ha van) frissített bérlői házirendje van, és ha igen, az Azure-ból letöltött szabályzatot fogja tartalmazni. A bérlői házirend dátumának értéke jelenleg a szabályzatnak az Azure-ból letöltött becsült időbélyeget jeleníti meg.
   
   Ha a második 30006 esemény nem jelenik meg, a folytatás előtt hárítsa el a problémát.
   
   Az 30006-es esemény az alábbi példához hasonlóan fog kinézni:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   A kényszerített és a vizsgálati mód közötti váltás például a AuditOnly jelzőt fogja eredményezni (a fenti szabályzat a AuditOnly = 0 értékkel kényszerített módban van); az egyéni tiltott jelszavak listájának módosításai nem jelennek meg közvetlenül a 30006-as eseményen (és biztonsági okokból nem történik meg sehol máshol). A szabályzat az Azure-ból való letöltése sikeres volt, miután egy ilyen módosítás a módosított egyéni tiltott jelszavak listáját is tartalmazza.

1. Futtasson egy tesztet egy új jelszó visszaállítására tett kísérlettel a felhasználói fiók teszteléséhez.

   Ez a lépés a parancssori ablakból is elvégezhető, például:

   ```text
   net.exe user ContosoUser <password>
   ```

   A parancs futtatása után további információkat kaphat a parancs eredményéről az eseménynaplóban megtekintve. A jelszó-ellenőrzés eredményének eseményei a [DC-ügynök rendszergazdai eseménynaplójának](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) témakörében vannak dokumentálva. ezekkel az eseményekkel ellenőrizheti a teszt eredményét, valamint az net.exe parancsok interaktív kimenetét is.

   Próbálja ki például a következőt: kísérlet egy olyan jelszó megadására, amelyet a Microsoft globális listája tiltott (vegye figyelembe, hogy a lista [nem dokumentált](concept-password-ban-bad.md#global-banned-password-list) , de egy ismert tiltott kifejezéssel is tesztelhető). Ez a példa azt feltételezi, hogy a házirendet kényszerített módban konfigurálta, és az egyéni tiltott jelszavak listájához nulla kifejezést adott hozzá.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   A dokumentációban, mivel a teszt a jelszó-visszaállítási művelet volt, a ContosoUser-felhasználó 10017-as és 30005-es eseményt kell látnia.

   Az 10017-es eseménynek a következő példához hasonlóan kell kinéznie:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Az 30005-es eseménynek a következő példához hasonlóan kell kinéznie:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   Ez jó volt – próbálkozzon egy másik példával! Ezúttal megpróbálunk beállítani egy jelszót, amelyet az egyéni tiltott lista tiltott, miközben a házirend naplózási módban van. Ez a példa feltételezi, hogy végrehajtotta a következő lépéseket: konfigurálta a házirendet naplózási módban, hozzáadta a "lachrymose" kifejezést az egyéni tiltott jelszavak listájához, és szinkronizálta az eredő új házirendet a tartományvezérlőhöz a DC Agent szolgáltatás a fent ismertetett módon történő kerékpározásával.

   Ok, állítsa be a betiltott jelszó variációját:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   Ne feledje, ez az idő sikeres volt, mert a házirend naplózási módban van. A ContosoUser-felhasználó 10025-es és 30007-es eseményt kell látnia.

   Az 10025-es eseménynek a következő példához hasonlóan kell kinéznie:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Az 30007-es eseménynek a következő példához hasonlóan kell kinéznie:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Folytassa a különböző választott jelszavak tesztelését, és ellenőrizze az eredményeket az Eseménynaplóban az előző lépésekben ismertetett eljárások alapján. Ha módosítania kell a szabályzatot a Azure Portalban, ne felejtse el szinkronizálni az új szabályzatot a DC-ügynökkel a korábban leírtak szerint.

Olyan eljárásokat ismertetünk, amelyek lehetővé teszik az Azure AD jelszavas védelem jelszavának érvényesítési viselkedésének vezérelt tesztelését. Ha a parancssorból közvetlenül egy tartományvezérlőre állítja vissza a felhasználói jelszavakat, akkor az ilyen tesztelési művelet szokatlannak tűnhet, de a korábban leírtak szerint megismételhető eredmények hozhatók létre. Ahogy a különböző jelszavakat teszteli, tartsa szem előtt a [jelszó-értékelési algoritmust](concept-password-ban-bad.md#how-are-passwords-evaluated) , mivel ez segíthet a nem várt eredmények magyarázatában.

> [!WARNING]
> Ha az összes tesztelés befejeződött, ne felejtse el törölni a tesztelési célra létrehozott felhasználói fiókokat.

## <a name="additional-content"></a>További tartalom

Az alábbi hivatkozások nem részei az Azure AD jelszavas védelem alapszintű dokumentációjának, de hasznos információforrásnak számítanak a szolgáltatással kapcsolatos további információk.

[Az Azure AD jelszavas védelme már általánosan elérhető!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-mailes adathalászat elleni védelem útmutatója – 15. rész: a Microsoft Azure AD jelszavas védelmi szolgáltatás implementálása (helyszíni is)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Az Azure AD jelszavas védelme és az intelligens zárolás mostantól nyilvános előzetes verzióban érhető el.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified-támogatás érhető el

Ha többet szeretne megtudni az Azure AD jelszavas védelméről, és üzembe helyezi azt a környezetében, kihasználhatja a Microsoft proaktív szolgáltatásának előnyeit a Premier vagy Unified támogatási szerződéssel rendelkező ügyfelek számára. A szolgáltatás neve Azure Active Directory: jelszavas védelem. További információért forduljon a technikai fiók kezelőjéhez.

## <a name="next-steps"></a>Következő lépések

Ha van olyan helyszíni Azure AD-beli jelszavas védelem kérdése, amely itt nem válaszol, küldje el az alábbi visszajelzési elemeket – Köszönjük!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)