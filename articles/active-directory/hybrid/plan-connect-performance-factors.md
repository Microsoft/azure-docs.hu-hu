---
title: Az Azure AD Connect teljesítményét befolyásoló tényezők
description: Ez a dokumentum ismerteti, hogyan befolyásolják a különböző tényezők a Azure AD Connect kiépítési motort. Ezek a tényezők segítenek a szervezeteknek a Azure AD Connect központi telepítés megtervezésében, hogy azok megfeleljenek a szinkronizálási követelményeiknek.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90087009"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Az Azure AD Connect teljesítményét befolyásoló tényezők

Azure AD Connect a Active Directory szinkronizálja az Azure AD-vel. Ez a kiszolgáló a felhasználói identitások felhőbe való áthelyezésének kritikus eleme. Egy Azure AD Connect teljesítményét befolyásoló elsődleges tényezők a következők:

| **Tervezési tényező**| **Definíció** |
|:-|-|
| Topológia| A végpontok és összetevők eloszlásának Azure AD Connect kell kezelnie a hálózaton. |
| Méretezés| A Azure AD Connect által kezelendő objektumok, például a felhasználók, csoportok és szervezeti egységek száma. |
| Hardver| A hardver (fizikai vagy virtuális) az egyes hardver-összetevők Azure AD Connect és függő teljesítményére, beleértve a CPU-t, a memóriát, a hálózatot és a merevlemez-konfigurációt. |
| Konfiguráció| Hogyan dolgozza fel a Azure AD Connect a címtárakat és az információkat. |
| Betöltés| Az objektum változásainak gyakorisága. A terhelések egy óra, nap vagy hét során változhatnak. Az összetevőtől függően előfordulhat, hogy meg kell terveznie a maximális terhelést vagy az átlagos terhelést. |

A dokumentum célja a Azure AD Connect kiépítési motor teljesítményének befolyásolását befolyásoló tényezők leírása. A nagy vagy összetett szervezetek (több mint 100 000 objektumot kiépítő szervezetek) a javaslatok segítségével optimalizálják Azure AD Connect megvalósítását, ha az itt ismertetett teljesítménnyel kapcsolatos problémákat tapasztalnak. A Azure AD Connect egyéb összetevői, például [Azure ad Connect Health](how-to-connect-health-agent-install.md) és Agents nem szerepelnek itt.

> [!IMPORTANT]
> A Microsoft nem támogatja a dokumentált műveleteken kívüli Azure AD Connect módosítását vagy működtetését. Ezen műveletek bármelyike inkonzisztens vagy nem támogatott állapotba Azure AD Connect szinkronizálást eredményezhet. Ennek eredményeképpen a Microsoft nem tud technikai támogatást biztosítani az ilyen üzemelő példányokhoz.

## <a name="azure-ad-connect-component-factors"></a>Összetevő-tényezők Azure AD Connect

A következő ábra a kiépítési motor magas szintű architektúráját mutatja egyetlen erdőhöz való csatlakozáshoz, bár több erdő is támogatott. Ez az architektúra azt mutatja be, hogy a különböző összetevők hogyan hatnak egymással.

![A diagram bemutatja, hogyan hatnak a csatlakoztatott címtárak és Azure AD Connect kiépítési motor, beleértve az összekötő területét és a metaverse-összetevőket egy SQL Database. ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

A kiépítési motor csatlakozik az egyes Active Directory erdőkhöz és az Azure AD-hez. Az adatok az egyes könyvtárakból való olvasásának folyamatát importálásnak nevezzük. Az Exportálás a címtárak a kiépítési motorból való frissítését jelenti. A szinkronizálás kiértékeli az objektumok a kiépítési motoron belüli folyamatának szabályait. A további részletekért tekintse meg [Azure ad Connect Sync: az architektúra megismerése](./concept-azure-ad-connect-sync-architecture.md)című témakört.

Azure AD Connect a következő átmeneti területeket, szabályokat és folyamatokat használja, hogy lehetővé tegye a szinkronizálást Active Directoryról az Azure AD-re:

* **Összekötő területe (CS)** – az egyes csatlakoztatott könyvtárakból (CD-ről) származó objektumok, amelyek a tényleges könyvtárak, a kiépítési motor által feldolgozható állapotban vannak. Az Azure AD saját CS-t és minden olyan erdőt tartalmaz, amelyhez csatlakozik.
* **Metaverse (MV)** – a szinkronizálni kívánt objektumok a szinkronizálási szabályok alapján jönnek létre. Az objektumoknak léteznie kell a MV-ban, mielőtt objektumokat és attribútumokat tölthetnek fel a többi csatlakoztatott címtárba. Csak egy MV van.
* **Szinkronizálási szabályok** – eldöntik, hogy mely objektumok lesznek létrehozva (tervezett) vagy csatlakoztatva (összekapcsolva) az MV-beli objektumokhoz. A szinkronizálási szabályok azt is eldöntik, hogy mely attribútumok lesznek átmásolva vagy átalakítva a címtárakba.
* **Futtatási profilok** – az objektumok és az attribútumok másolásának folyamati lépéseit az átmeneti területek és a csatlakoztatott könyvtárak közötti szinkronizálási szabályoknak megfelelően csomagolja.

Különböző futtatási profilok találhatók a kiépítési motor teljesítményének optimalizálása érdekében. A legtöbb szervezet az alapértelmezett ütemterveket és a futtatási profilokat használja a normál működéshez, de előfordulhat, hogy néhány szervezetnek [módosítania kell az ütemtervet](./how-to-connect-sync-feature-scheduler.md) , vagy más futtatási profilokat kell elindítania, hogy az ne legyen gyakori helyzetekben. A következő futtatási profilok érhetők el:

### <a name="initial-sync-profile"></a>Kezdeti szinkronizálási profil

A kezdeti szinkronizálási profil az a folyamat, amelynek során a rendszer első alkalommal beolvassa a csatlakoztatott könyvtárakat, például egy Active Directory erdőt. Ezután elemzést végez a Sync Engine-adatbázis összes bejegyzéséről. A kezdeti ciklus új objektumokat hoz létre az Azure AD-ben, és hosszabb időt vesz igénybe, ha a Active Directory erdők nagyok. A kezdeti szinkronizálás a következő lépéseket tartalmazza:

1. Teljes importálás minden összekötőn
2. Teljes szinkronizálás minden összekötőn
3. Exportálás az összes összekötőn

### <a name="delta-sync-profile"></a>Különbözeti szinkronizálási profil

A szinkronizálási folyamat optimalizálásához a futtatási profil csak a legutóbbi szinkronizálási folyamat óta dolgozza fel a csatlakoztatott címtárakban található objektumok módosításait (a létrehozási, törlési és frissítési műveleteit). Alapértelmezés szerint a különbözeti szinkronizálási profil 30 percenként fut. A szervezeteknek törekedniük kell arra, hogy a 30 percnél továbbra is megtartsa a szükséges időt, hogy az Azure AD naprakész legyen. Azure AD Connect állapotának figyeléséhez az állapotfigyelő [ügynök](how-to-connect-health-sync.md) használatával tekintheti meg a folyamattal kapcsolatos problémákat. A különbözeti szinkronizálási profil a következő lépéseket tartalmazza:

1. Különbözeti Importálás az összes összekötőn
2. Különbözeti szinkronizálás az összes összekötőn
3. Exportálás az összes összekötőn

Egy tipikus vállalati szervezet Delta-szinkronizálási forgatókönyve a következő:

- az objektumok ~ 1%-a törölve
- az objektumok ~ 1%-a létrehozva
- az objektumok 5%-a módosult

A változási arány attól függően változhat, hogy a szervezet milyen gyakran frissíti a felhasználókat a Active Directoryban. Például a magasabb fokú változás a szezonális bérlet és a munkahelyi erő csökkentése esetén fordulhat elő.

### <a name="full-sync-profile"></a>Teljes szinkronizálási profil

Ha a következő konfigurációs változások bármelyikét végrehajtotta, teljes szinkronizálási ciklus szükséges:



- Megnövelte a csatlakoztatott könyvtárakból importálandó objektumok vagy attribútumok hatókörét. Ha például tartományt vagy szervezeti egységet ad hozzá az importálási hatókörhöz.
- Módosítások történtek a szinkronizálási szabályokban. Ha például létrehoz egy új szabályt, amely feltölti a felhasználó címét az Azure AD-ben Active Directory extension_attribute3. Ehhez a frissítéshez a kiépítési motornak újra meg kell vizsgálnia az összes meglévő felhasználót, hogy frissítse a címüket, hogy alkalmazza a módosítást.

A teljes szinkronizálási ciklus a következő műveleteket tartalmazza:

1. Teljes importálás minden összekötőn
2. Teljes/különbözeti szinkronizálás minden összekötőn
3. Exportálás az összes összekötőn

> [!NOTE]
> Alapos tervezésre van szükség, ha a Active Directory vagy az Azure AD számos objektumához tömeges frissítést végez. A tömeges frissítés miatt a különbözeti szinkronizálási folyamat hosszabb időt vesz igénybe az importálás során, mert sok objektum módosult. A hosszú importálások akkor is megtörténhetnek, ha a tömeges frissítés nem befolyásolja a szinkronizálási folyamatot. Például, ha az Azure AD-ben sok felhasználóhoz rendel hozzá licenceket, a hosszú importálási ciklust eredményez az Azure AD-ből, de a Active Directory nem fog semmilyen attribútumot módosítani.

### <a name="synchronization"></a>Szinkronizálás

A szinkronizálási folyamat futtatókörnyezete a következő teljesítménnyel rendelkezik:

* A szinkronizálás egyszálas, ami azt jelenti, hogy a kiépítési motor nem hajtja végre a csatlakoztatott könyvtárak, objektumok és attribútumok futtatási profiljainak párhuzamos feldolgozását.
* Az importálási idő lineárisan nő a szinkronizált objektumok számával. Ha például az 10 000-es objektumok importálása 10 percet vesz igénybe, akkor a 20 000-objektumok körülbelül 20 percet vesznek igénybe ugyanazon a kiszolgálón.
* Az Exportálás szintén lineáris.
* A szinkronizálás exponenciálisan növekedni fog a más objektumokra hivatkozó objektumok száma alapján. A csoporttagságok és a beágyazott csoportok a fő teljesítményre gyakorolt hatással rendelkeznek, mivel tagjaik felhasználói objektumokra vagy más csoportokra hivatkoznak. Ezeket a hivatkozásokat meg kell találni, és hivatkozni kell rá az MV-beli tényleges objektumokra a szinkronizálási ciklus befejezéséhez.

### <a name="filtering"></a>Szűrés

Az importálni kívánt Active Directory-topológia mérete az a szám, amely befolyásolhatja a teljesítményt és a kiépítési motor belső összetevőinek teljes idejét.

A [szűrést](./how-to-connect-sync-configure-filtering.md) az objektumok szinkronizált értékre való csökkentésére kell használni. Ez megakadályozza a szükségtelen objektumok feldolgozását és exportálását az Azure AD-be. A preferencia sorrendben a következő szűrési módszerek érhetők el:



- **Tartományalapú szűrés** – ezzel a beállítással kiválaszthatja az Azure ad-vel szinkronizálandó, meghatározott tartományokat. Ha a Azure AD Connect Sync telepítése után módosítja a helyszíni infrastruktúrát, akkor a szinkronizálási motor konfigurációjától kell hozzáadnia és eltávolítania a tartományokat.
- **Szervezeti egység (OU) szűrés** – az Azure ad-be való kiépítéshez a szervezeti egységek használatával célozza meg Active Directory tartományok meghatározott objektumait. A szervezeti egység szerinti szűrés a második ajánlott szűrési mechanizmus, mivel egyszerű LDAP-hatóköri lekérdezésekkel importálja az objektumok kisebb részhalmazát Active Directory.
- **Attribútumok szűrése objektum** alapján – az objektumok attribútum értékeit használja annak eldöntéséhez, hogy Active Directory adott objektuma az Azure ad-ban van-e kiépítve. Az attribútumok szűrése kiválóan használható a szűrők finomhangolásához, ha a tartomány és a szervezeti egység szűrése nem felel meg az adott szűrési követelményeknek. Az attribútumok szűrése nem csökkenti az importálási időt, de csökkentheti a szinkronizálást és az exportálási időpontokat.
- **Csoport alapú szűrés** – csoporttagság használatával döntheti el, hogy az objektumokat az Azure ad-ben kell-e kiépíteni. A csoport alapú szűrés csak tesztelési helyzetekben használható, éles környezetben nem ajánlott, mert a csoporttagság ellenőrzéséhez szükséges extra terhelés a szinkronizálási ciklusban.

A Active Directory CS számos állandó [leválasztó objektuma](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) több szinkronizálási időt okozhat, mivel a kiépítési motornak újra kell értékelnie az egyes leválasztó objektumokat a szinkronizálási ciklusban lehetséges kapcsolathoz. A probléma megoldása érdekében vegye figyelembe a következő javaslatok egyikét:



- Helyezze el a leválasztó objektumait tartomány vagy szervezeti egység szerinti szűrés használatával az importálás hatókörén kívül.
- Projekt/csatlakoztassa az objektumokat az MV-hoz, és állítsa a [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) attribútumot igaz értékre, hogy megakadályozza ezeknek az objektumoknak az üzembe helyezését az Azure ad cs-ban.

> [!NOTE]
> A felhasználók megkaphatják a zavaros vagy az alkalmazás engedélyeivel kapcsolatos problémákat, ha túl sok objektum szűrve van. Egy hibrid Exchange Online-implementációban például a helyszíni postaládákkal rendelkező felhasználók a globális címlistában több felhasználót láthatnak, mint a postaládákkal rendelkező felhasználók az Exchange Online-ban. Más esetekben előfordulhat, hogy a felhasználók egy felhőalapú alkalmazásban szeretnének hozzáférést adni egy másik felhasználónak, amely nem része a szűrt objektumok hatókörének.

### <a name="attribute-flows"></a>Attribútumok folyamatai

Az attribútumok folyamata az objektumok attribútumainak az egyik csatlakoztatott címtárból egy másik csatlakoztatott könyvtárba történő másolásának vagy átalakításának folyamata. Ezek a szinkronizálási szabályok részeként vannak meghatározva. Ha például egy felhasználó telefonszámát módosítja a Active Directory, a rendszer frissíti az Azure AD telefonszámát. A szervezetek [módosíthatják az attribútum folyamatait](./how-to-connect-sync-change-the-configuration.md) a különböző követelményekhez. Azt javasoljuk, hogy a módosítás előtt másolja a meglévő attribútumok folyamatait.

Az egyszerű átirányítások, például az attribútumérték egy másik attribútumra való továbbítása nem befolyásolja a lényeges teljesítményt. Átirányítási példa egy mobil számra Active Directory az Azure AD-ben az Office Phone-számra.

Az attribútumok értékének átalakítása hatással lehet a szinkronizálási folyamat teljesítményére. Az attribútumok átalakítása magában foglalja az attribútumok módosításait, újraformázását, összefűzését vagy kivonását.

A szervezetek letilthatják bizonyos attribútumok áramlását az Azure AD-be, de nem befolyásolják a kiépítési motor teljesítményét.

> [!NOTE]
> Ne törölje a nem kívánt attribútumok folyamatait a szinkronizálási szabályokban. Javasoljuk, hogy inkább tiltsa le őket, mert a törölt szabályok újból létrejönnek a Azure AD Connect frissítése során.

## <a name="azure-ad-connect-dependency-factors"></a>Függőségi tényezők Azure AD Connect

A Azure AD Connect teljesítménye függ az általa importált és a-ba exportált csatlakoztatott könyvtárak teljesítményével. Például az importálni kívánt Active Directory mérete vagy az Azure AD szolgáltatás hálózati késése. A kiépítési motor által használt SQL-adatbázis hatással van a szinkronizálási ciklus általános teljesítményére is.

### <a name="active-directory-factors"></a>Active Directory tényezők

Ahogy azt korábban említettük, az importálandó objektumok száma jelentősen befolyásolja a teljesítményt. A [hardverre és előfeltételekre vonatkozó Azure ad Connect](how-to-connect-install-prerequisites.md) körvonalazza az adott hardveres csomagokat az üzemelő példány mérete alapján. Azure AD Connect csak a [Azure ad Connect topológiák](plan-connect-topologies.md)által meghatározott topológiákat támogatja. Nincsenek teljesítmény-optimalizálási és javaslatok a nem támogatott topológiák esetében.

Győződjön meg arról, hogy a Azure AD Connect-kiszolgáló megfelel a hardverre vonatkozó követelményeknek az importálni kívánt Active Directory-méret alapján. A Azure AD Connect-kiszolgáló és a Active Directory-tartományvezérlők közötti rossz vagy lassú hálózati kapcsolat lelassíthatja az importálást.

### <a name="azure-ad-factors"></a>Azure AD-tényezők

Az Azure AD szabályozást használ a Cloud Service-szolgáltatásoknak a szolgáltatásmegtagadási (DoS) támadások elleni védelemhez. Az Azure AD jelenleg legfeljebb 5 percenként 7 000 írási korlátot (84 000/óra) tartalmaz. Például a következő műveletek szabályozható:



- Azure AD Connect Exportálás az Azure AD-be.
- A PowerShell-parancsfájlok vagy-alkalmazások közvetlenül a háttérben frissítik az Azure AD-t, például a dinamikus csoporttagságok esetében is.
- A felhasználók saját identitási rekordokat frissítenek, például az MFA vagy a SSPR regisztrációját (önkiszolgáló jelszó-visszaállítás).
- Műveletek a grafikus felhasználói felületen belül.

Tervezze meg az üzembe helyezési és karbantartási feladatokat, és győződjön meg arról, hogy a Azure AD Connect szinkronizálási ciklusa nincs hatással a szabályozás korlátaira. Ha például egy nagy felvételi hullámtal rendelkezik, ahol több ezer felhasználói identitást hoz létre, akkor a dinamikus csoporttagságok, a licencelési hozzárendelések és az önkiszolgáló jelszó-visszaállítási regisztrációk frissítése is okozhat. Ezeket az írásokat érdemes több órán át vagy néhány napig elosztani.

### <a name="sql-database-factors"></a>SQL Database-tényezők

A forrás Active Directory topológiájának mérete befolyásolja az SQL Database teljesítményét. Kövesse az SQL Server-adatbázis [hardveres követelményeit](how-to-connect-install-prerequisites.md) , és vegye figyelembe a következő javaslatokat:



- A több mint 100 000 felhasználóval rendelkező szervezetek csökkenthetik a hálózati késéseket az SQL Database és a kiépítési motor ugyanazon a kiszolgálón való elhelyezésével.
- A szinkronizálási folyamat nagy bemeneti és kimeneti (I/O) követelményei miatt a kiépítési motor SQL Database-hez szükséges SSD-k használata az optimális eredmények érdekében, ha nem lehetséges, érdemes lehet RAID 0 vagy RAID 1 konfigurációt használni.
- Ne végezzen teljes szinkronizálási megelőző jelleggel; szükségtelen adatváltozást és lassabb válaszidőt okoz.

## <a name="conclusion"></a>Összegzés

A Azure AD Connect megvalósításának teljesítményének optimalizálása érdekében vegye figyelembe a következő ajánlásokat:



- A Azure AD Connect-kiszolgáló megvalósítási méretén alapuló [ajánlott hardverkonfiguráció](how-to-connect-install-prerequisites.md) használata.
- Ha nagy léptékű központi telepítések során Azure AD Connect frissíteni, érdemes a [swing Migration metódust](./how-to-upgrade-previous-version.md#swing-migration)használni, hogy a lehető legkevesebb állásidőt és a legjobb megbízhatóságot biztosítsa. 
- A legjobb írási teljesítmény érdekében használja az SSD-t az SQL Database-hez.
- Szűrje a Active Directory hatókört úgy, hogy csak olyan objektumokat tartalmazzon, amelyeket az Azure AD-ben kell kiépíteni, tartomány, szervezeti egység vagy attribútum szűrés használatával.
- Ha módosítania kell az attribútum alapértelmezett folyamatának szabályait, először másolja a szabályt, majd módosítsa a másolást, és tiltsa le az eredeti szabályt. Ne felejtse el újrafuttatni a teljes szinkronizálást.
- Tervezze meg a megfelelő időt a kezdeti teljes szinkronizálási futtatási profilhoz.
- Törekedni kell arra, hogy 30 percen belül elvégezze a különbözeti szinkronizálási ciklust. Ha a különbözeti szinkronizálási profil nem fejeződött be 30 percen belül, módosítsa az alapértelmezett szinkronizálási gyakoriságot úgy, hogy az tartalmazza a teljes különbözeti szinkronizálási ciklust.
- A [Azure ad Connect szinkronizálási állapotának](how-to-connect-health-agent-install.md) figyelése az Azure ad-ben.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).