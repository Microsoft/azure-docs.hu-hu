---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900963"
---
Ez a cikk a kiadott Azure Active Directory Connect kiépítési ügynök verzióit és funkcióit sorolja fel. Az Azure AD csapata rendszeresen frissíti a kiépítési ügynököt új funkciókkal és funkciókkal. A kiépítési ügynök automatikusan frissül, amikor megjelent egy új verzió. 

A Microsoft közvetlen támogatást biztosít az ügynök legújabb verziójához és egy korábbi verzióhoz.

## <a name="113540"></a>1.1.354.0

2021. január 20., letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
- A GMSA-élmény fejlesztése, beleértve az előre egyéni létrehozott GMSA-fiók támogatását
- [PowerShell-parancsmagok](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) támogatása a GMSA telepítéséhez
- [Parancssori felület támogatása](../articles/active-directory/cloud-sync/how-to-install-pshell.md) az ügynök telepítéséhez (csendes telepítés)
- További diagnosztika az ügynök forrásának karanténba helyezésével kapcsolatban
- Hibajavítások, amelyek közé tartozik a szervezeti egységbeli hatókörű szűrők memóriahasználat csökkentése, a PHS futtatása csak a hatókörben lévő felhasználók számára, a szervezeti egységben lévő beágyazott objektumok kezelése szervezeti egység hatókörének használatakor stb. 


### <a name="fixed-issues"></a>Megoldott problémák
-    Karanténba helyezés megakadályozása a hatókörön kívüli csoport
-   Ha a hatóköri szűrők konfigurálva vannak – a PHS-feladatok most már csak a hatókörben lévő felhasználók esetében működnek
-   Az ügynök néha lefagy a frissítés során
-   A beágyazott szervezeti egységekben lévő objektumok kezdeti szinkronizálása OU-hatókör használatával
-   A Repair-AADCloudSyncToolsAccount robusztusvé tétele
-   A szervezeti egységhez tartozó szűrők nagy mértékű memóriahasználat csökkentése
-   A rendszergazdai szerepkör-ellenőrzés sikertelen, ha a szerepkör tagjai biztonsági csoportot tartalmaznak
-   A GMSA mappa engedélyeinek javítása, amely megakadályozza az ügynök tanúsítványának megújítását







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Kiadás állapota

November 23., 2020: kiadva a letöltéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* A [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts) támogatása
* A növekményes és a különbözeti szinkronizálási ciklus során a 1500-nál kevesebb taggal rendelkező csoportok támogatása. Ez a csoport-hatókörű szűrő használatakor alkalmazható
* Nagyméretű csoportok támogatása a tagok méretével akár 15 000 fordulat
* A szinkronizálás kezdeti fejlesztése
* Részletes részletes naplózás
* A [AADCloudSyncTools PowerShell-modul](../articles/active-directory/cloud-sync/reference-powershell.md) bemutatása
* Rögzített korlátozások az ügynök nem angol nyelvű kiszolgálóra való telepítésének engedélyezéséhez
* Csak a hatókörben lévő objektumok PHS-szűrésének támogatása (eredetileg a jelszó-kivonatok szinkronizálása minden objektum esetében)
* A memóriavesztés hibájának kijavítása az ügynökben
* Továbbfejlesztett kiépítési naplók
* Az LDAP- [kapcsolat időtúllépésének](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) konfigurálásának támogatása 
* Az [átirányítási](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) beállítások konfigurálásának támogatása 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Kiadás állapota

2019. december 4., letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* A [Azure ad Connect Cloud Sync](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) támogatását is támogatja a felhasználók szinkronizálásához, a helyszíni Active Directory az Azure ad-vel való kapcsolattartáshoz és az adatok csoportosításához


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Kiadás állapota

2019. szeptember 9.: kiadva az automatikus frissítéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* További nyomkövetési és naplózási beállítások konfigurálása a kiépítési ügynökkel kapcsolatos problémák felderítéséhez
* Csak azokat az Azure AD-attribútumokat lehet beolvasni, amelyek a leképezésben vannak konfigurálva a szinkronizálás teljesítményének növeléséhez

### <a name="fixed-issues"></a>Megoldott problémák

* Kijavítva egy olyan hibát, amelyben az ügynök nem válaszoló állapotba került, ha probléma merült fel az Azure AD-beli kapcsolatok meghibásodása esetén
* Kijavított egy hibát, amely problémát okozott, amikor a bináris adatok beolvasása Azure Active Directory
* Kijavítva egy olyan hibát, amelyben az ügynök nem tudta megújítani a bizalmi kapcsolatot a Cloud Hybrid Identity Service használatával

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Kiadás állapota

2019. január 23., letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Kitatarozta a kiépítési ügynököt és az összekötő architektúráját a jobb teljesítmény, stabilitás és megbízhatóság érdekében 
* A kiépítési ügynök konfigurációjának egyszerűsítése a felhasználói felületen alapuló telepítővarázsló használatával 
* Az automatikus ügynök frissítéseinek támogatása