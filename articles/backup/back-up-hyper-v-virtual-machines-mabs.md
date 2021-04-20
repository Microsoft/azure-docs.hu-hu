---
title: Hyper-V virtuális gépek biztonsági létrehozása MABS-sel
description: Ez a cikk a virtuális gépek biztonsági és helyreállítási eljárásait tartalmazza a Microsoft Azure Backup Server (MABS) használatával.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b4de791269161b477fc07d6539feaa975fdd72ad
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739997"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Hyper-V virtuális gépek biztonsági Azure Backup Server

Ez a cikk azt ismerteti, hogyan lehet hyper-V virtuális gépekről biztonsági Microsoft Azure Backup (MABS) használatával.

## <a name="supported-scenarios"></a>Támogatott esetek

A MABS a következő esetekben képes a Hyper-V gazdakiszolgálón futó virtuális gépek biztonsági útjára:

- **Virtuális gépek helyi vagy közvetlen tárolással** – helyi vagy közvetlenül csatlakoztatott tárhellyel rendelkező önálló Hyper-V-gazdakiszolgálók által futtatott virtuális gépek biztonsági mentése. Például: merevlemez, tárolóhálózati (SAN) eszköz vagy hálózati tárolóeszköz (NAS). A MABS védelmi ügynököt az összes gazdagépre telepíteni kell.

- **Fürtben található virtuális gépek CSV-tárolóval** – megosztott fürtkötetes (CSV) tárolót használó Hyper-V-fürt által futtatott virtuális gépek biztonsági mentése. A MABS védelmi ügynök minden fürtcsomóponton telepítve van.

## <a name="host-versus-guest-backup"></a>Gazdagép és vendég biztonsági mentése

A MABS a Hyper-V virtuális gépek gazdagép- vagy vendégszintű biztonsági mentését is képes készíteni. A gazdagép szintjén a MABS védelmi ügynök telepítve van a Hyper-V-gazdakiszolgálón vagy -fürtön, és megvédi a gazdagépen futó összes virtuális gép és adatfájl védelmét.   A vendég szintjén az ügynök minden virtuális gépre telepítve van, és megvédi az ezen a gépen lévő számítási feladatokat.

Mindkét módszernek vannak előnyei és hátrányai is:

- A gazdagépszintű biztonsági mentések rugalmasak, mivel a vendég gépeken futó operációs rendszer típusától függetlenül működnek, és nem igénylik a MABS védelmi ügynök telepítését az egyes virtuális gépeken. Gazdagépszintű biztonsági mentés üzembe helyezésekor helyreállíthat egy teljes virtuális gépet, vagy fájlokat és mappákat (elemszintű helyreállítás).

- A vendégszintű biztonsági mentés akkor hasznos, ha egy virtuális gépen futó adott számítási feladatokat szeretne védeni. Gazdagépszinten helyreállíthat egy teljes virtuális gépet vagy adott fájlokat, de ez nem biztosít helyreállítást egy adott alkalmazás kontextusában. Ha például adott SharePoint-elemeket kell helyreállítani egy biztonsági másolatból egy virtuális gépről, vendégszintű biztonsági mentést kell készítenie a virtuális gépről. Használjon vendégszintű biztonsági mentést, ha az áttűnő lemezeken tárolt adatokat szeretné védeni. A passthrough lehetővé teszi, hogy a virtuális gép közvetlenül hozzáférjen a tárolóeszközhöz, és nem tárolja a virtuális kötet adatait egy VHD-fájlban.

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

A MABS a következőképpen végzi el a biztonsági mentést a VSS-sel. A leírás lépéseit az átláthatóság érdekében számozással láttuk el.

1. A MABS blokkalapú szinkronizálási motorja kezdeti másolatot ad a védett virtuális gépről, és gondoskodik arról, hogy a virtuális gép másolata teljes és konzisztens legyen.

2. A kezdeti másolat készítése és ellenőrzése után a MABS a Hyper-V VSS-íróval rögzíti a biztonsági másolatokat. A VSS-író a MABS-kiszolgálóval szinkronizált lemezblokkok adat konzisztens készletét biztosítja. Ez a megközelítés biztosítja a "teljes biztonsági mentés" előnyét a MABS-kiszolgálóval, ugyanakkor minimalizálja a hálózaton átvitt biztonsági mentési adatokat.

3. A Hyper-V-t futtató kiszolgálón futó MABS védelmi ügynök a meglévő Hyper-V API-k segítségével határozza meg, hogy egy védett virtuális gép támogatja-e a VSS-t is.

   - Ha egy virtuális gép megfelel az online biztonsági mentés követelményeinek, és telepítve van rajta a Hyper-V integrációs szolgáltatások összetevő, akkor a Hyper-V VSS rekurzív módon továbbítja a VSS-kérést a virtuális gép minden VSS-t támogató folyamatához. Ez a művelet anélkül történik meg, hogy a MABS védelmi ügynök telepítve lett volna a virtuális gépen. A Hyper-V VSS-író a rekurzív VSS-kérések révén biztosítja a lemezírási műveletek szinkronizációját, így adatvesztés nélkül rögzíthető a VSS-pillanatkép.

     A Hyper-V integrációs szolgáltatások összetevő meghívja a Hyper-V VSS-írót a virtuális gépekre a Kötet árnyékmásolata szolgáltatásban annak érdekében, hogy a virtuális gépek alkalmazási adatai konzisztensek legyenek.

   - Ha a virtuális gép nem felel meg az online biztonsági mentés követelményeinek, a MABS automatikusan a Hyper-V API-k segítségével szünetelteti a virtuális gépet, mielőtt adatfájlokat rögzít.

4. Miután a virtuális gép kezdeti alapkonfigurációja szinkronizált a MABS-kiszolgálóval, a virtuális gép erőforrásain végrehajtott összes módosítás egy új helyreállítási ponton lesz rögzíti. A helyreállítási pont a virtuális gép adott időpontbeli konzisztens változatát képviseli. Naponta legalább egyszer sor kerülhet egy helyreállítási pont rögzítésére. Új helyreállítási pont létrehozásakor a MABS blokkszintű replikációt használ a Hyper-V VSS-íróval együtt annak meghatározásához, hogy mely blokkok változtak meg a Hyper-V-t futtató kiszolgálón az utolsó helyreállítási pont létrehozása után. A rendszer ezután továbbítja ezeket az adatblokkokat a MABS-kiszolgálóra, és alkalmazza őket a védett adatok replikára.

5. A MABS-kiszolgáló VSS-t használ a helyreállítási adatokat gazdaköteten, hogy több árnyékmásolat is elérhetővé válik. Minden egyes árnyékmásolat külön helyreállítást biztosít. A VSS helyreállítási pontjai a MABS-kiszolgálón vannak tárolva. A Hyper-V-t futtató kiszolgálón készült ideiglenes másolat csak a MABS-szinkronizálás időtartamára lesz tárolva.

>[!NOTE]
>
>A Windows Server 2016-tól kezdődően a Hyper-V virtuális merevlemezek beépített változáskövetéssel, az úgynevezett rugalmas változáskövetéssel (RCT) is szolgálnak. A MABS az RCT-t (a Hyper-V natív változáskövetését) használja, ami csökkenti az időigényes konzisztencia-ellenőrzések szükségét olyan forgatókönyvekben, mint a virtuális gépek összeomlása. Az RCT a VSS pillanatkép-alapú biztonsági mentések által biztosított változáskövetésnél nagyobb rugalmasságot biztosít. A MABS V3 tovább optimalizálja a hálózat és a tárterület-használatot azáltal, hogy a konzisztencia-ellenőrzések során csak a módosított adatokat átvitele biztosítja.

## <a name="backup-prerequisites"></a>A biztonsági mentésre vonatkozó előfeltételek

A Hyper-V virtuális gépek MABS-sel való biztonságimentésének előfeltételei:

|Előfeltétel|Részletek|
|------------|-------|
|MABS-előfeltételek|– Ha elemszintű helyreállítást szeretne végrehajtani a virtuális gépeken (fájlok, mappák, kötetek helyreállítása), akkor telepítenie kell a Hyper-V szerepkört a MABS-kiszolgálóra.  Ha csak a virtuális gépet szeretné helyreállítani, és nem az elemszintűt, akkor nincs szükség a szerepkörre.<br />– Egyetlen MABS-kiszolgálón legfeljebb 800, 100 GB-os virtuális gépet védhet, és több, nagyobb fürtöt támogató MABS-kiszolgálót engedélyezhet.<br />– A MABS kizárja a lapfájlt a növekményes biztonsági mentésből a virtuális gép biztonsági mentési teljesítményének javítása érdekében.<br />– A MABS képes biztonságimentetni egy Hyper-V-kiszolgálóról vagy -fürtről a MABS-kiszolgálóval azonos tartományban, illetve egy gyermek- vagy megbízható tartományban. Ha munkacsoportban vagy nem megbízható tartományban található Hyper-V-ről szeretne biztonságimentet, be kell állítania a hitelesítést. Egyetlen Hyper-V-kiszolgáló esetén használhatja az NTLM-et vagy a tanúsítványhitelesítést. Fürtök esetén csak tanúsítványhitelesítést használhat.<br />–   A gazdaszintű biztonsági mentésnek a virtuális gép adatainak csatlakoztatott lemezre történő biztonsági mentéséhez való használata nem támogatott. Ebben a forgatókönyvben azt javasoljuk, hogy gazdagépszintű biztonsági mentést használjon a VHD-fájlok és a vendégszintű biztonsági mentések biztonsági mentéséhez a gazdagépen nem látható egyéb adatok biztonsági mentéséhez.<br />   – A deduplikált köteten tárolt virtuális gépekről biztonságimentéseket is lehet.|
|A Hyper-V virtuális gépek előfeltételei|– A virtuális gépen futó integrációs összetevők verziójának meg kell egyeznünk a Hyper-V-gazdagép verziójával. <br />–   Az egyes virtuális gépek biztonsági mentéséhez szabad területre lesz szükség azon a köteten, amelyen a virtuálismerevlemez-fájlok találhatók, hogy a Hyper-V a biztonsági mentés során elég hellyel rendelkezzen a különbséglemezek (AVHD) számára. A területnek legalább akkorának kell lennie, mint a **kezdeti lemezméret\*forgalom sebessége\*biztonsági mentési időablak** számítás eredménye. Ha egy fürtön több biztonsági mentést futtat, akkor az egyes virtuális gépek különbséglemezeinek elegendő tárolókapacitással kell rendelkezniük, hogy a fenti számítás szerint elférjenek rajtuk az egyes virtuális gépek.<br />– A Windows Server 2012 R2-t futtató Hyper-V gazdakiszolgálókon található virtuális gépek biztonsági másolataihoz meg kell adni egy SCSI-vezérlőt, még akkor is, ha nem csatlakozik semmihez. (A Windows Server 2012 R2 biztonsági mentésben a Hyper-V-gazdagép csatlakoztat egy új virtuális merevlemezt a virtuális gépen, majd később leválasztja azt. Ezt csak az SCSI-vezérlő támogatja, ezért a virtuális gép online biztonsági mentéséhez szükséges.  E beállítás nélkül az 10103-as eseményazonosító lesz kibocsátva, amikor megpróbálja biztonságimentésbe őket a virtuális gépről.)|
|A Linux előfeltételei|– A Linux rendszerű virtuális gépekről a MABS használatával is biztonságimenthet. Csak a fájlkonzisztens pillanatképek támogatottak.|
|Virtuális gépek biztonsági mentése CSV-tárolóval|–   A megosztott fürtkötetes tároláshoz telepítse a Kötet árnyékmásolata szolgáltatás (VSS) hardverszolgáltatót a Hyper-V-kiszolgálóra. A VSS hardverszolgáltatóról a tárolóhálózat (SAN) gyártójánál érdeklődhet.<br />– Ha egyetlen csomópont váratlanul leáll egy CSV-fürtben, a MABS konzisztencia-ellenőrzést végez az adott csomóponton futó virtuális gépeken.<br />–   Ha újra kell indítani a kérdéses fürt egyik olyan Hyper-V-kiszolgálóját, amelyen engedélyezve van a BitLocker meghajtótitkosítás, konzisztencia-ellenőrzést kell futtatni a Hyper-V rendszerű virtuális gépeken.|
|Virtuális gépek biztonsági mentése SMB-tárolóval|– Kapcsolja be az automatikus csatlakoztatást a Hyper-V-t futtató kiszolgálón a virtuális gépek védelmének engedélyezéséhez.<br />   –   Tiltsa le a TCP Chimney tehermentesítést.<br />–   Ügyeljen arra, hogy minden Hyper-V machine$-fiók teljes körű engedélyekkel rendelkezzen a megfelelő távoli SMB-fájlmegosztásokon.<br />– Győződjön meg arról, hogy a virtuális gép összetevőinek elérési útja a másik helyre való helyreállítás során 260 karakternél rövidebb legyen. Ha nem, a helyreállítás sikeres lehet, de a Hyper-V nem fogja tudni csatlakoztatni a virtuális gépet.<br />– Az alábbi forgatókönyvek nem támogatottak:<br />     Központi telepítések, amelyekben a virtuális gép egyes összetevői helyi köteten, más összetevők pedig távoli köteten vannak; egy IPv4- vagy IPv6-cím a tárolóhely fájlkiszolgálójának, és egy virtuális gép helyreállítása távoli SMB-megosztásokat használó számítógépre.<br />– Minden SMB-kiszolgálón engedélyeznie kell a Fájlkiszolgáló VSS-ügynökszolgáltatást – Adja hozzá a Szerepkörök és szolgáltatások hozzáadása Kiszolgálói szerepkörök kiválasztása Fájl- és tárolási szolgáltatások fájlszolgáltatás fájlszolgáltatása Fájlkiszolgáló  >    >    >    >    >  **VSS-ügynökszolgáltatás.**|

## <a name="back-up-virtual-machines"></a>Virtuális gépek biztonsági mentése

1. Állítsa be a [MABS-kiszolgálót és](backup-azure-microsoft-azure-backup.md) [a tárolót.](backup-mabs-add-storage.md) A tárhely beállításakor alkalmazza a tárolási kapacitásra vonatkozó irányelveket.
   - Virtuális gép átlagos mérete – 100 GB
   - Virtuális gépek száma MABS-kiszolgálónként – 800
   - A 800 virtuális gép teljes mérete – 80 TB
   - Biztonságimásolat-tároláshoz szükséges terület – 80 TB

2. Állítsa be a MABS védelmi ügynököt a Hyper-V-kiszolgálón vagy a Hyper-V-fürtcsomópontokon.

3. A MABS felügyeleti konzolon válassza a **Védelem** Védelmi csoport létrehozása lehetőséget az Új védelmi csoport  >   létrehozása **varázsló megnyitásához.**

4. A **Csoporttagok kiválasztása** lapon válassza ki a védendő virtuális gépeket az ezeket magukban foglaló Hyper-V-gazdakiszolgálóról. Javasoljuk, hogy minden olyan virtuális gépet, amelyre azonos védelmi szabályzat vonatkozik, egy védelmi csoportba helyezzen. A tárterület hatékony felhasználása érdekében engedélyezze az együttes elhelyezést. Az együttes elhelyezés lehetővé teszi a különböző védelmi csoportokból származó adatok ugyanazon lemezen vagy szalagon történő tárolását, így több adatforrásnak egyetlen replika- és helyreállításipont-kötete keletkezik.

5. Az **Adatvédelmi módszer kiválasztása** oldalon adja meg a védelmi csoport nevét. Válassza ki a **Rövid távú védelmet szeretnék a következő használatával: Lemez** elemet, és válassza az **Online védelmet szeretnék** beállítást, ha az Azure Backup szolgáltatás segítségével szeretné az Azure-ba menteni az adatok biztonsági másolatát.

6. A **Célmegtartási Short-Term** megadása beállításban adja meg, hogy mennyi ideig szeretné megőrizni a  >  lemezadatokat. A **Szinkronizálás gyakorisága beállításban** adja meg, hogy milyen gyakran fusson az adatok növekményes biztonsági mentése. A növekményes biztonsági mentések időközeinek megadása helyett azt is megteheti, hogy engedélyezi a **Csak helyreállítási pont létrehozása előtt** beállítást. Ha ez a beállítás engedélyezve van, a MABS expressz teljes biztonsági mentést futtat minden ütemezett helyreállítási pont előtt.

    > [!NOTE]
    >
    >Ha alkalmazások munkaterhelését védi, akkor a helyreállítási pontok a Szinkronizálás gyakorisága beállítás szerint jönnek létre, feltéve, hogy az alkalmazás támogatja a növekményes biztonsági mentéseket. Ha nem, akkor a MABS expressz teljes biztonsági mentést futtat növekményes biztonsági mentés helyett, és az expressz biztonsági mentés ütemezésének megfelelően hozza létre a helyreállítási pontokat.<br></br>A biztonsági mentési folyamat nem készít biztonsági másolatot a virtuális gépekhez társított ellenőrzőpontokról.

7. A **Lemezfoglalás áttekintése lapon** tekintse át a védelmi csoporthoz lefoglalt tárolókészlet lemezterületét.

   **A Teljes adatméret** a biztonságimentni kívánt adatok mérete, a **MABS-on** kiépítni kívánt lemezterület pedig a MABS által a védelmi csoport számára javasolt terület. A MABS a beállítások alapján választja ki az ideális biztonsági mentési kötetet. Lehetőség van azonban arra, hogy a **Disk allocation details** (Lemezfoglalás részletei) panelen módosítsa a biztonsági mentési kötetek választási lehetőségeit. A munkaterhelésekhez válassza ki a legördülő menüből az előnyben részesített tárhelyet. A módosítások megváltoztatják az **Összes tárhely** és a **Szabad tárterület** értékeit az **Elérhető lemezterület** ablaktáblán. Az underprovisioned space (Ki nem épített terület) az a tárterület, amit a MABS javasol a kötethez való hozzáadásához, hogy a jövőben zökkenőmentesen folytatja a biztonsági mentéseket.

8. A **Replika-létrehozási módszer kiválasztása** oldalon adhatja meg, hogy a rendszer hogyan végezze el a védelmi csoport adatainak kezdeti replikálását. Ha az Automatikus replikálás a hálózaton keresztül lehetőséget **választja,** javasoljuk, hogy csúcsidőn kívüli időpontra válasszon. Nagy mennyiségű adat vagy az optimálisnál kisebb hálózati feltételek esetén érdemes lehet a Manuális választást választani, amelyhez cserélhető adathordozó használatával offline replikálni kell az adatokat.

9. A **Konzisztencia-ellenőrzési beállítások** oldalon válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Beállíthatja hogy a rendszer ütemezés szerint futtasson ellenőrzést, vagy csak akkor, amikor a replikaadatok inkonzisztenssé válnak. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, bármikor lefuttathat egy manuális ellenőrzést, ha a jobb gombbal a védelmi csoportra kattint, és a **Konzisztencia-ellenőrzés végrehajtása** elemet választja.

    A védelmi csoport létrehozását követően megtörténik az adatok kezdeti replikálása a kiválasztott módszernek megfelelően. A kezdeti replikálás után a védelmi csoport beállításai szerint lezajlanak az egyes biztonsági mentések. Ha helyre kell állítania a biztonságimentett adatokat, vegye figyelembe a következőket:

## <a name="back-up-replica-virtual-machines"></a>Replika virtuális gépek biztonsági mentése

Ha a MABS Windows Server 2012 R2 vagy annál nagyobb operációs rendszeren fut, akkor a replika virtuális gépekről biztonsági másolatot is lehet kapni. Ez több szempontból is hasznos:

**Csökkenti a biztonsági mentéseknek a futó munkaterhelésekre gyakorolt hatását** – A virtuális gép biztonsági mentése némi többletterhelést okoz, mivel egy pillanatkép jön létre. A biztonsági mentési folyamat egy másodlagos távoli helyre való kiszervezése után a biztonsági mentési művelet már nem érinti a futó számítási feladatot. Ez csak olyan telepítések esetén alkalmazható, ahol a biztonsági másolat tárolása egy távoli helyen történik. Például biztonsági másolatokat készíthet naponta, és a gyors helyreállítás érdekében helyileg tárolhatja az adatokat, de a replika virtuális gépekről havonta vagy negyedévente is készíthet biztonsági másolatokat, amelyeket a hosszú távú megőrzés érdekében egy távoli helyen tárolhat.

**Kíméli a sávszélességet** – Egy tipikus fiókirodai/központi irodai telepítés esetén megfelelő nagyságrendű kiosztott sávszélességre van szükség a biztonsági másolatok telephelyek közötti átviteléhez. Ha az adatok biztonsági mentési stratégiája mellett kialakít egy replikációs és feladatátvételi stratégiát, csökkentheti a hálózaton keresztül továbbított redundáns adatok mennyiségét. Ha az elsődleges helyett a replika virtuális gép adatairól biztonsági másolatot küld, azzal megtakarítja a biztonsági másolatok hálózaton keresztüli küldésével kapcsolatos többletterhelést.

**Lehetővé teszi a szolgáltatói biztonsági mentést** – Replika telephelyként üzemeltetett adatközpontot is használhat, és nincs szükség másodlagos adatközpontra. Ebben az esetben a szolgáltatói SLA megköveteli a replika virtuális gépek konzisztens biztonsági mentését.

A feladatátvétel indításáig a replika virtuális gép ki van kapcsolva, és a VSS nem garantálja a replika virtuális gép alkalmazáskonzisztens biztonsági mentését. Így a replika virtuális gép biztonsági mentése csak összeomlás-konzisztens lesz. Ha az összeomlás-konzisztens mentést nem lehet garantálni a biztonsági mentés sikertelen lesz, ami számos esetben előfordulhat:

- A replika virtuális gép működése nem kifogástalan, állapota kritikus.

- A replika virtuális gép újraszinkronizálást végez (az Újraszinkronizálás folyamatban vagy az Újraszinkronizálás szükséges állapotban van).

- A virtuális gépre vonatkozóan az elsődleges és másodlagos hely közötti kezdeti replikálás folyamatban vagy függőben van.

- A rendszer alkalmazza a .hrl-naplókat a replika virtuális gépre, vagy egy korábbi műveletet, amely a .hrl-naplókat alkalmazza a virtuális lemezen, vagy megszakadt vagy megszakadt.

- A replika virtuális gép áttelepítése vagy feladatátvétele folyamatban van.

## <a name="recover-backed-up-virtual-machines"></a>Biztonsági másolatba mentett virtuális gépek helyreállítása

Amikor biztonsági másolatba mentett virtuális gépeket állít helyre, a Helyreállítási varázsló segítségével választhatja ki a kívánt virtuális gépet és az adott helyreállítási pontot. A Helyreállítási varázsló megnyitásához és a virtuális gép helyreállításához tegye a következőt:

1. A MABS felügyeleti konzolon írja be a virtuális gép nevét, vagy bontsa ki a védett elemek listáját, lépjen a Minden védett **HyperV-adat** elemhez, és válassza ki a helyreállítani kívánt virtuális gépet.

2. Az elérhető **helyreállítási pontok a helyreállítási** pontokhoz panelen a naptárban válassza ki a kívánt dátumot. Az **Elérési út** ablaktáblán válassza ki a Helyreállítási varázslóban használni kívánt helyreállítási pontot.

3. A Helyreállítási **varázsló megnyitásához** a Műveletek **menüben válassza** a Helyreállítás lehetőséget.

    A kiválasztott virtuális gép és helyreállítási pont megjelenik a **Helyreállítási beállítások áttekintése** képernyőn. Kattintson a **Tovább** gombra.

4. A **Helyreállítási típus kiválasztása képernyőn** válassza ki, hová szeretné visszaállítani az adatokat, majd válassza a Tovább **lehetőséget.**

    - **Helyreállítás az eredeti példányra:** Ha az eredeti példányra végzi a helyreállítást, a rendszer törli az eredeti VHD-t és az összes kapcsolódó ellenőrzőpontot. A MABS a Hyper-V VSS-író használatával visszaállítja a VHD- és egyéb konfigurációs fájlokat az eredeti helyre. A helyreállítási folyamat végén a virtuális gépek továbbra is magas rendelkezésre állásúak lesznek.
        A helyreállításhoz szükség van az erőforráscsoportra. Ha az nem érhető el, akkor egy másik helyre végezze el a helyreállítást, és állítsa a virtuális gépet magas rendelkezésre állásúra.

    - **Helyreállítás** virtuális gépként bármely gazdagépre: A MABS támogatja az alternatív helyre való helyreállítást (ALR), amely a védett Hyper-V virtuális gépek zökkenőmentes helyreállítását biztosítja egy másik Hyper-V-gazdagépre, a processzorarchitektúrától függetlenül. A fürtcsomópontra helyreállított Hyper-V virtuális gépek nem lesznek magas rendelkezésre állásban. Ha ezt a beállítást választja, a Helyreállítási varázsló megjelenít egy további, a cél és a cél elérési útjának azonosítására szolgáló képernyőt.
    
        >[!NOTE]
        >Ha az eredeti gazdagépet választja, a működés ugyanaz, mint a **Helyreállítás az eredeti példányra.** Az eredeti VHD és az összes kapcsolódó ellenőrzőpont törlődik.

    - Másolás hálózati **mappába:** A MABS támogatja az elemszintű helyreállítást (ILR), amely lehetővé teszi fájlok, mappák, kötetek és virtuális merevlemezek (VHD-k) elemszintű helyreállítását a Hyper-V virtuális gépek gazdagépszintű biztonsági másolatából egy hálózati megosztásra vagy egy MABS által védett kiszolgáló kötetére. Az elemszintű helyreállítás végrehajtásához a MABS védelmi ügynököt nem kell telepíteni a vendégen belül. Ha ezt a beállítást választja, a Helyreállítási varázsló megjelenít egy további, a cél és a cél elérési útjának azonosítására szolgáló képernyőt.

5. A **Helyreállítási beállítások megadása mezőben adja** meg a helyreállítási beállításokat, majd válassza a Tovább **lehetőséget:**

    - Ha egy virtuális gépet alacsony sávszélességgel helyreállít, válassza a **Módosítás** lehetőséget a Hálózati sávszélesség használatának **szabályozása engedélyezéséhez.** A sávszélesség-szabályozási beállítás bekapcsolása után megadhatja, hogy mekkora sávszélességet, illetve mikor kíván elérhetővé tenni.
    - Ha konfigurálta a hálózatot, jelölje be a **San-alapú** helyreállítás engedélyezése hardveres pillanatképekkel jelölőnégyzetet.
    - Ha azt szeretné, hogy a rendszer e-mail-értesítést küldjön a helyreállítási folyamat befejezése után, jelölje be az **E-mail küldése a helyreállítás befejezéséről** lehetőséget, majd adja meg a kívánt e-mail-címeket.

6. Az Összefoglalás képernyőn ellenőrizze az adatok helyességét. Ha a részletek helytelenek, vagy módosítani szeretné a beállításokat, válassza a Vissza **lehetőséget.** Ha elégedett a beállításokkal, válassza a **Helyreállítás lehetőséget** a helyreállítási folyamat elkezdéséhez.

7. A helyreállítási feladatra vonatkozó információk a **Helyreállítás állapota** képernyőn jelennek meg.

## <a name="next-steps"></a>Következő lépések

[Adatok helyreállítása az Azure Backup Serverről](./backup-azure-alternate-dpm-server.md)
