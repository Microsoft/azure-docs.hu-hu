---
title: Hyper-V rendszerű virtuális gépek biztonsági mentése a MABS-mel
description: Ez a cikk a virtuális gépek Microsoft Azure Backup Server (MABS) használatával történő biztonsági mentésének és helyreállításának eljárásait tartalmazza.
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: a020559229771fff1ecc8fb512a5b2af70240cdd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509506"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Hyper-V rendszerű virtuális gépek biztonsági mentése Azure Backup Server

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni a Hyper-V rendszerű virtuális gépekről Microsoft Azure Backup kiszolgáló (MABS) használatával.

## <a name="supported-scenarios"></a>Támogatott esetek

A MABS a Hyper-V-gazdagépeken futó virtuális gépek biztonsági mentését a következő esetekben végezheti el:

- **Virtuális gépek helyi vagy közvetlen tárolással** – helyi vagy közvetlenül csatlakoztatott tárhellyel rendelkező önálló Hyper-V-gazdakiszolgálók által futtatott virtuális gépek biztonsági mentése. Például: merevlemez, Tárolóhálózati (SAN) eszköz vagy hálózati csatlakoztatott tároló (NAS) eszköz. A MABS védelmi ügynököt minden gazdagépre telepíteni kell.

- **Fürtben található virtuális gépek CSV-tárolóval** – megosztott fürtkötetes (CSV) tárolót használó Hyper-V-fürt által futtatott virtuális gépek biztonsági mentése. A MABS védelmi ügynök minden fürtcsomóponton telepítve van.

## <a name="host-versus-guest-backup"></a>Gazdagép és vendég biztonsági mentése

A MABS a Hyper-V virtuális gépek gazdagépét vagy vendég szintű biztonsági mentését is elvégezheti. A gazdagép szintjén a MABS védelmi ügynök telepítve van a Hyper-V-gazdagépen vagy-fürtön, és megvédi a gazdagépen futó összes virtuális gépet és adatfájlt.   A vendég szinten az ügynököt minden virtuális gépre telepíti, és megvédi a gépen lévő munkaterhelést.

Mindkét módszernek vannak előnyei és hátrányai is:

- A gazdagép szintű biztonsági mentések rugalmasak, mert a vendég gépen futó operációs rendszer típusától függetlenül működnek, és nem szükséges a MABS védelmi ügynök telepítése minden egyes virtuális gépen. Ha a gazdagép szintű biztonsági mentést telepíti, a teljes virtuális gépet, illetve a fájlokat és mappákat (elemszintű helyreállítás) is helyreállíthatja.

- A vendég szintű biztonsági mentés akkor lehet hasznos, ha egy virtuális gépen futó adott munkaterheléseket kíván védelemmel ellátni. A gazdagép szintjén helyreállíthatja a teljes virtuális gépet vagy az adott fájlokat, de nem biztosít a helyreállítást egy adott alkalmazás kontextusában. Ha például adott SharePoint-elemeket szeretne helyreállítani egy biztonsági másolatból álló virtuális gépről, akkor a virtuális gép vendég szintű biztonsági másolatát kell használnia. Ha az áteresztő lemezeken tárolt adatvédelmet szeretné biztosítani, használja a vendég szintű biztonsági mentést. A továbbító lehetővé teszi, hogy a virtuális gép közvetlenül hozzáférhessen a tárolóeszközhöz, és nem tárolja a virtuális köteteket a VHD-fájlokban.

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

A MABS a következőképpen végzi a biztonsági mentést a VSS-vel. A leírás lépéseit az átláthatóság érdekében számozással láttuk el.

1. A MABS blokk-alapú szinkronizációs motor a védett virtuális gép kezdeti másolatát készíti el, és biztosítja, hogy a virtuális gép másolata elkészült és konzisztens legyen.

2. A kezdeti másolat létrejötte és ellenőrzése után a MABS a Hyper-V VSS-író használatával rögzíti a biztonsági mentéseket. A VSS-író adathalmazt biztosít a MABS-kiszolgálóval szinkronizált lemezes blokkokhoz. Ez a megközelítés biztosítja a teljes biztonsági mentés előnyeit a MABS-kiszolgálóval, miközben minimalizálja a hálózaton keresztül továbbított biztonsági mentési adatmennyiséget.

3. A Hyper-V-t futtató kiszolgálókon futó MABS védelmi ügynök a meglévő Hyper-V API-k használatával határozza meg, hogy a védett virtuális gép is támogatja-e a VSS-t.

   - Ha egy virtuális gép megfelel az online biztonsági mentés követelményeinek, és telepítve van rajta a Hyper-V integrációs szolgáltatások összetevő, akkor a Hyper-V VSS rekurzív módon továbbítja a VSS-kérést a virtuális gép minden VSS-t támogató folyamatához. Ez a művelet a MABS védelmi ügynöknek a virtuális gépre való telepítése nélkül történik. A Hyper-V VSS-író a rekurzív VSS-kérések révén biztosítja a lemezírási műveletek szinkronizációját, így adatvesztés nélkül rögzíthető a VSS-pillanatkép.

     A Hyper-V integrációs szolgáltatások összetevő meghívja a Hyper-V VSS-írót a virtuális gépekre a Kötet árnyékmásolata szolgáltatásban annak érdekében, hogy a virtuális gépek alkalmazási adatai konzisztensek legyenek.

   - Ha a virtuális gép nem felel meg az online biztonsági mentés követelményeinek, a MABS automatikusan a Hyper-V API-k használatával szünetelteti a virtuális gépet az adatfájlok rögzítése előtt.

4. Miután a virtuális gép kezdeti példánya szinkronizálva lett a MABS-kiszolgálóval, a rendszer a virtuális gép erőforrásain végrehajtott összes módosítást egy új helyreállítási pontba rögzíti. A helyreállítási pont a virtuális gép adott időpontbeli konzisztens változatát képviseli. Naponta legalább egyszer sor kerülhet egy helyreállítási pont rögzítésére. Új helyreállítási pont létrehozásakor a MABS a Hyper-V VSS-író mellett blokkolja a blokk szintű replikálást annak meghatározásához, hogy a legutóbbi helyreállítási pont létrehozása után a Hyper-V-t futtató kiszolgálón milyen blokkok lettek módosítva. Ezeket az adatblokkokat a rendszer átviszi a MABS-kiszolgálóra, és alkalmazza őket a védett adatreplikára.

5. A MABS-kiszolgáló a helyreállítási adattároló köteteken található VSS-t használja, hogy több árnyékmásolat is elérhető legyen. Minden egyes árnyékmásolat külön helyreállítást biztosít. A VSS helyreállítási pontjait a MABS-kiszolgálón tárolja a rendszer. A Hyper-V-t futtató kiszolgálón végrehajtott ideiglenes másolat csak a MABS-szinkronizálás időtartamára van tárolva.

>[!NOTE]
>
>A Windows Server 2016-es verziótól kezdődően a Hyper-V virtuális merevlemezek beépített változások követését jelentik a rugalmas változások követése (RCT) néven. A MABS a RCT (a Hyper-V natív változások nyomon követését) használja, ami csökkenti az időigényes konzisztencia-ellenőrzés szükségességét olyan helyzetekben, mint például a virtuális gépek összeomlása. Az RCT a VSS pillanatkép-alapú biztonsági mentések által biztosított változáskövetésnél nagyobb rugalmasságot biztosít. A MABS v3 a konzisztencia-ellenőrzések során csak a módosult adatmennyiségek átvitelével optimalizálja a hálózat és a tárterület felhasználását.

## <a name="backup-prerequisites"></a>A biztonsági mentésre vonatkozó előfeltételek

A Hyper-V virtuális gépek MABS történő biztonsági mentésének előfeltételei a következők:

|Előfeltétel|Részletek|
|------------|-------|
|A MABS előfeltételei|– Ha elemszintű helyreállítást szeretne végrehajtani a virtuális gépeken (fájlok, mappák, kötetek helyreállítása), akkor telepítenie kell a Hyper-V szerepkört a MABS-kiszolgálóra.  Ha csak a virtuális gépet kívánja helyreállítani, és nem elemszintű, akkor a szerepkör nem szükséges.<br />– Akár 800 virtuális gépet is véd 100 GB-onként egy MABS-kiszolgálón, és engedélyezheti a nagyobb fürtöket támogató több MABS-kiszolgálót.<br />– A MABS kizárja a lapozófájlt a növekményes biztonsági mentésből a virtuális gép biztonsági mentési teljesítményének növelése érdekében.<br />– A MABS képes biztonsági másolatot készíteni egy Hyper-V-kiszolgálóról vagy-fürtről, amely ugyanabban a tartományban van, mint a MABS-kiszolgáló, vagy egy gyermek vagy egy megbízható tartomány. Ha a Hyper-V biztonsági mentését munkacsoportban vagy nem megbízható tartományban szeretné végrehajtani, akkor be kell állítania a hitelesítést. Egyetlen Hyper-V kiszolgáló esetén NTLM-vagy tanúsítványalapú hitelesítést használhat. Fürt esetén csak tanúsítványalapú hitelesítést használhat.<br />–   A gazdaszintű biztonsági mentésnek a virtuális gép adatainak csatlakoztatott lemezre történő biztonsági mentéséhez való használata nem támogatott. Ebben az esetben azt javasoljuk, hogy a gazdagép szintű biztonsági mentéssel készítsen biztonsági másolatot a VHD-fájlokról, és a vendég szintű biztonsági mentéssel készítsen biztonsági másolatot a gazdagépen nem látható egyéb adatfájlokról.<br />   – Elvégezheti a deduplikált köteteken tárolt virtuális gépek biztonsági mentését.|
|A Hyper-V virtuális gépek előfeltételei|– A virtuális gépen futó integrációs összetevők verziójának meg kell egyeznie a Hyper-V-gazdagép verziójával. <br />–   Az egyes virtuális gépek biztonsági mentéséhez szabad területre lesz szükség azon a köteten, amelyen a virtuálismerevlemez-fájlok találhatók, hogy a Hyper-V a biztonsági mentés során elég hellyel rendelkezzen a különbséglemezek (AVHD) számára. A területnek legalább akkorának kell lennie, mint a **kezdeti lemezméret\*forgalom sebessége\*biztonsági mentési időablak** számítás eredménye. Ha egy fürtön több biztonsági mentést futtat, akkor az egyes virtuális gépek különbséglemezeinek elegendő tárolókapacitással kell rendelkezniük, hogy a fenti számítás szerint elférjenek rajtuk az egyes virtuális gépek.<br />-A Windows Server 2012 R2 rendszerű Hyper-V-gazdagépeken található virtuális gépek biztonsági mentéséhez a virtuális gépnek rendelkeznie kell egy SCSI-vezérlővel, még akkor is, ha az nem csatlakozik semmihöz. (A Windows Server 2012 R2 online biztonsági mentésében a Hyper-V-gazdagép egy új virtuális merevlemezt csatlakoztat a virtuális géphez, majd később leválasztja azt. Ezt csak az SCSI-vezérlő tudja támogatni, ezért szükséges a virtuális gép online biztonsági mentéséhez.  A beállítás nélkül a 10103-es AZONOSÍTÓJÚ esemény lesz kiadva, amikor megkísérli a virtuális gép biztonsági mentését.)|
|A Linux előfeltételei|– A Linux rendszerű virtuális gépek biztonsági mentését a MABS használatával végezheti el. Csak a fájlkonzisztens pillanatképek támogatottak.|
|Virtuális gépek biztonsági mentése CSV-tárolóval|–   A megosztott fürtkötetes tároláshoz telepítse a Kötet árnyékmásolata szolgáltatás (VSS) hardverszolgáltatót a Hyper-V-kiszolgálóra. A VSS hardverszolgáltatóról a tárolóhálózat (SAN) gyártójánál érdeklődhet.<br />– Ha egy csomópont váratlanul leáll egy CSV-fürtben, a MABS konzisztencia-ellenőrzést hajt végre az adott csomóponton futó virtuális gépeken.<br />–   Ha újra kell indítani a kérdéses fürt egyik olyan Hyper-V-kiszolgálóját, amelyen engedélyezve van a BitLocker meghajtótitkosítás, konzisztencia-ellenőrzést kell futtatni a Hyper-V rendszerű virtuális gépeken.|
|Virtuális gépek biztonsági mentése SMB-tárolóval|– Kapcsolja be az automatikus csatlakoztatást a Hyper-V-t futtató kiszolgálón a virtuális gép védelmének engedélyezéséhez.<br />   –   Tiltsa le a TCP Chimney tehermentesítést.<br />–   Ügyeljen arra, hogy minden Hyper-V machine$-fiók teljes körű engedélyekkel rendelkezzen a megfelelő távoli SMB-fájlmegosztásokon.<br />– Győződjön meg arról, hogy az összes virtuálisgép-összetevő fájljának elérési útja a másik helyre történő helyreállítás során kevesebb, mint 260 karakter. Ha nem, a helyreállítás sikeres lehet, de a Hyper-V nem fogja tudni csatlakoztatni a virtuális gépet.<br />– A következő forgatókönyvek nem támogatottak:<br />     Olyan központi telepítések, ahol a virtuális gép egyes összetevői helyi köteteken találhatók, és egyes összetevők távoli köteteken találhatók; egy IPv4-vagy IPv6-cím a tárolási hely fájlkiszolgáló számára, valamint egy virtuális gép helyreállítása olyan számítógépre, amely távoli SMB-megosztásokat használ.<br />– Engedélyeznie kell a Fájlkiszolgálói VSS-ügynök szolgáltatást minden SMB-kiszolgálón – vegye fel azt a **szerepkörök és szolgáltatások hozzáadása**  >  **elemnél válassza a kiszolgálói szerepkörök**  >  **fájl-és tárolási szolgáltatások** Fájlszolgáltatások fájl-szolgáltatás fájlkiszolgáló  >    >    >  **VSS-ügynök szolgáltatást**.|

## <a name="back-up-virtual-machines"></a>Virtuális gépek biztonsági mentése

1. Állítsa be a [MABS-kiszolgálót](backup-azure-microsoft-azure-backup.md) és [a tárolót](backup-mabs-add-storage.md). A tárhely beállításakor alkalmazza a tárolási kapacitásra vonatkozó irányelveket.
   - Virtuális gép átlagos mérete – 100 GB
   - Virtuális gépek száma MABS-kiszolgálónként – 800
   - A 800 virtuális gép teljes mérete – 80 TB
   - Biztonságimásolat-tároláshoz szükséges terület – 80 TB

2. Állítsa be a MABS védelmi ügynököt a Hyper-V-kiszolgálón vagy a Hyper-V-fürt csomópontjain. Ha vendég szintű biztonsági mentést végez, az ügynököt azon virtuális gépekre kell telepítenie, amelyekről biztonsági másolatot szeretne készíteni a vendég szintjén.

3. Az   >  **új védelmi csoport létrehozása** varázsló megnyitásához a MABS felügyeleti konzolján kattintson a védelem védelmi **csoport létrehozása** elemre.

4. A **Csoporttagok kiválasztása** lapon válassza ki a védendő virtuális gépeket az ezeket magukban foglaló Hyper-V-gazdakiszolgálóról. Javasoljuk, hogy minden olyan virtuális gépet, amelyre azonos védelmi szabályzat vonatkozik, egy védelmi csoportba helyezzen. A tárterület hatékony felhasználása érdekében engedélyezze az együttes elhelyezést. Az együttes elhelyezés lehetővé teszi a különböző védelmi csoportokból származó adatok ugyanazon lemezen vagy szalagon történő tárolását, így több adatforrásnak egyetlen replika- és helyreállításipont-kötete keletkezik.

5. Az **Adatvédelmi módszer kiválasztása** oldalon adja meg a védelmi csoport nevét. Válassza ki a **Rövid távú védelmet szeretnék a következő használatával: Lemez** elemet, és válassza az **Online védelmet szeretnék** beállítást, ha az Azure Backup szolgáltatás segítségével szeretné az Azure-ba menteni az adatok biztonsági másolatát.

6. A **Short-Term célok**  >  **megőrzési tartományának** megadása területen válassza ki, hogy mennyi ideig kívánja megőrizni a lemezes adattárolást. A **szinkronizálás gyakorisága** mezőben adja meg, hogy milyen gyakran fusson a növekményes biztonsági másolatok. A növekményes biztonsági mentések időközeinek megadása helyett azt is megteheti, hogy engedélyezi a **Csak helyreállítási pont létrehozása előtt** beállítást. Ha ez a beállítás engedélyezve van, a MABS expressz teljes biztonsági mentést futtat az egyes ütemezett helyreállítási pontok előtt.

    > [!NOTE]
    >
    >Ha alkalmazások munkaterhelését védi, akkor a helyreállítási pontok a Szinkronizálás gyakorisága beállítás szerint jönnek létre, feltéve, hogy az alkalmazás támogatja a növekményes biztonsági mentéseket. Ha nem, akkor a MABS expressz teljes biztonsági mentést futtat növekményes biztonsági mentés helyett, és a helyreállítási pontokat az expressz biztonsági mentési ütemtervnek megfelelően hozza létre.

7. A **lemez lefoglalásának áttekintése** lapon tekintse át a védelmi csoport számára lefoglalt tárterület lemezterületét.

   A **teljes adatméret** a biztonsági mentéshez használni kívánt adatmennyiség, a **MABS** pedig a lemezterület, amelyet a MABS a védelmi csoport számára ajánl. A MABS a beállítások alapján kiválasztja az ideális biztonsági mentési kötetet. Lehetőség van azonban arra, hogy a **Disk allocation details** (Lemezfoglalás részletei) panelen módosítsa a biztonsági mentési kötetek választási lehetőségeit. A munkaterhelésekhez válassza ki a legördülő menüből az előnyben részesített tárhelyet. A módosítások megváltoztatják az **Összes tárhely** és a **Szabad tárterület** értékeit az **Elérhető lemezterület** ablaktáblán. A kiépített terület az a tárolási MABS, amely a kötethez való hozzáadást javasolja, hogy zökkenőmentesen folytassa a biztonsági mentéseket a jövőben.

8. A **Replika-létrehozási módszer kiválasztása** oldalon adhatja meg, hogy a rendszer hogyan végezze el a védelmi csoport adatainak kezdeti replikálását. Ha úgy dönt, hogy **automatikusan replikálja a hálózaton keresztül**, javasoljuk, hogy válasszon ki egy off-Peak időpontot. Nagy mennyiségű vagy az optimális hálózati feltételeknél kisebb méretű adatforgalom esetén érdemes **manuálisan** kiválasztani a lehetőséget, amelyhez a cserélhető adathordozón keresztül az adatreplikálás szükséges.

9. A **Konzisztencia-ellenőrzési beállítások** oldalon válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Beállíthatja hogy a rendszer ütemezés szerint futtasson ellenőrzést, vagy csak akkor, amikor a replikaadatok inkonzisztenssé válnak. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, bármikor lefuttathat egy manuális ellenőrzést, ha a jobb gombbal a védelmi csoportra kattint, és a **Konzisztencia-ellenőrzés végrehajtása** elemet választja.

    A védelmi csoport létrehozását követően megtörténik az adatok kezdeti replikálása a kiválasztott módszernek megfelelően. A kezdeti replikálás után a védelmi csoport beállításai szerint lezajlanak az egyes biztonsági mentések. Ha helyre kell állítania az adatok biztonsági másolatát, vegye figyelembe a következőket:

## <a name="back-up-replica-virtual-machines"></a>Replika virtuális gépek biztonsági mentése

Ha a MABS Windows Server 2012 R2 vagy újabb rendszeren fut, akkor biztonsági mentést készíthet a replika virtuális gépekről. Ez több szempontból is hasznos:

**Csökkenti a biztonsági mentéseknek a futó munkaterhelésekre gyakorolt hatását** – A virtuális gép biztonsági mentése némi többletterhelést okoz, mivel egy pillanatkép jön létre. A biztonsági mentési folyamat másodlagos távoli helyre történő kiszervezésével a biztonsági mentési művelet már nem érinti a futó munkaterhelést. Ez csak olyan telepítések esetén alkalmazható, ahol a biztonsági másolat tárolása egy távoli helyen történik. Például biztonsági másolatokat készíthet naponta, és a gyors helyreállítás érdekében helyileg tárolhatja az adatokat, de a replika virtuális gépekről havonta vagy negyedévente is készíthet biztonsági másolatokat, amelyeket a hosszú távú megőrzés érdekében egy távoli helyen tárolhat.

**Kíméli a sávszélességet** – Egy tipikus fiókirodai/központi irodai telepítés esetén megfelelő nagyságrendű kiosztott sávszélességre van szükség a biztonsági másolatok telephelyek közötti átviteléhez. Ha az adatok biztonsági mentési stratégiája mellett kialakít egy replikációs és feladatátvételi stratégiát, csökkentheti a hálózaton keresztül továbbított redundáns adatok mennyiségét. Ha nem az elsődleges, hanem a replika virtuális gép adatokról készít biztonsági mentést, a biztonsági másolatban szereplő adatokat a hálózaton keresztül küldi el.

**Lehetővé teszi a szolgáltatói biztonsági mentést** – Replika telephelyként üzemeltetett adatközpontot is használhat, és nincs szükség másodlagos adatközpontra. Ebben az esetben a szolgáltatói SLA-nak konzisztens biztonsági másolatot kell készítenie a replika virtuális gépekről.

A feladatátvétel indításáig a replika virtuális gép ki van kapcsolva, és a VSS nem garantálja a replika virtuális gép alkalmazáskonzisztens biztonsági mentését. Így a replika virtuális gépek biztonsági mentése csak összeomlás-konzisztens lesz. Ha az összeomlás-konzisztens mentést nem lehet garantálni a biztonsági mentés sikertelen lesz, ami számos esetben előfordulhat:

- A replika virtuális gép működése nem kifogástalan, állapota kritikus.

- A replika virtuális gép újraszinkronizálást végez (az Újraszinkronizálás folyamatban vagy az Újraszinkronizálás szükséges állapotban van).

- A virtuális gépre vonatkozóan az elsődleges és másodlagos hely közötti kezdeti replikálás folyamatban vagy függőben van.

- . HRL naplók vannak alkalmazva a replika virtuális gépre, vagy a. HRL naplók virtuális gépen történő alkalmazására irányuló korábbi művelet meghiúsult, vagy megszakították vagy megszakították.

- A replika virtuális gép áttelepítése vagy feladatátvétele folyamatban van.

## <a name="recover-backed-up-virtual-machines"></a>Biztonsági másolatba mentett virtuális gépek helyreállítása

Amikor biztonsági másolatba mentett virtuális gépeket állít helyre, a Helyreállítási varázsló segítségével választhatja ki a kívánt virtuális gépet és az adott helyreállítási pontot. A Helyreállítási varázsló megnyitásához és a virtuális gép helyreállításához tegye a következőt:

1. A MABS felügyeleti konzolon írja be a virtuális gép nevét, vagy bontsa ki a védett elemek listáját, és válassza ki a helyreállítani kívánt virtuális gépet.

2. A **helyreállítási pontok** ablaktábla naptár területén válassza ki az összes dátumot, hogy megjelenjenek a rendelkezésre álló helyreállítási pontok. Az **Elérési út** ablaktáblán válassza ki a Helyreállítási varázslóban használni kívánt helyreállítási pontot.

3. A **műveletek** menüben válassza a **helyreállítás** lehetőséget a helyreállítási varázsló megnyitásához.

    A kiválasztott virtuális gép és helyreállítási pont megjelenik a **Helyreállítási beállítások áttekintése** képernyőn. Kattintson a **Tovább** gombra.

4. A **helyreállítási típus kiválasztása** képernyőn válassza ki, hogy hová szeretné visszaállítani az adatkészletet, majd válassza a **tovább** lehetőséget.

    - **Visszaállítás az eredeti példányra**: Ha az eredeti példányt állítja helyre, a rendszer törli az eredeti virtuális merevlemezt és az összes kapcsolódó ellenőrzőpontot. A MABS a Hyper-V VSS-író használatával helyreállítja a VHD-t és az egyéb konfigurációs fájlokat az eredeti helyükre. A helyreállítási folyamat végén a virtuális gépek továbbra is magas rendelkezésre állásúak lesznek.
        A helyreállításhoz szükség van az erőforráscsoportra. Ha az nem érhető el, akkor egy másik helyre végezze el a helyreállítást, és állítsa a virtuális gépet magas rendelkezésre állásúra.

    - **Helyreállítás virtuális gépnek bármely gazdagépre**: a MABS támogatja a másik helyre történő helyreállítást (ALR), amely a védett Hyper-v virtuális gépek zökkenőmentes helyreállítását teszi lehetővé egy másik Hyper-v-gazdagépen, a processzor-architektúrától függetlenül. A fürtcsomóponton helyreállított Hyper-V virtuális gépek nem lesznek magasan elérhetők. Ha ezt a beállítást választja, a Helyreállítási varázsló megjelenít egy további, a cél és a cél elérési útjának azonosítására szolgáló képernyőt.
    
        >[!NOTE]
        >Ha az eredeti gazdagépet választja, a viselkedés ugyanaz, mint az **eredeti példány helyreállítása**. A rendszer törli az eredeti virtuális merevlemezt és az összes kapcsolódó ellenőrzőpontot.

    - **Másolás hálózati mappába**: a MABS támogatja az elemszintű helyreállítást (ILR), amely lehetővé teszi a fájlok, mappák, kötetek és virtuális merevlemezek (VHD-k) elemszintű helyreállítását a Hyper-V virtuális gépek gazdagép szintű biztonsági másolatából egy hálózati megosztásra vagy egy MABS által védett kiszolgáló kötetére. Az elemszintű helyreállítás végrehajtásához a MABS védelmi ügynököt nem kell a vendégen belül telepíteni. Ha ezt a beállítást választja, a Helyreállítási varázsló megjelenít egy további, a cél és a cél elérési útjának azonosítására szolgáló képernyőt.

5. A **helyreállítási beállítások megadása** területen adja meg a helyreállítási beállításokat, majd válassza a **Next (tovább**) gombot:

    - Ha alacsony sávszélességen keresztül helyreállít egy virtuális gépet, válassza a **módosítás** lehetőséget a **hálózati sávszélesség-használat szabályozásának** engedélyezéséhez. A sávszélesség-szabályozási beállítás bekapcsolása után megadhatja, hogy mekkora sávszélességet, illetve mikor kíván elérhetővé tenni.
    - Ha konfigurálta a hálózatot, válassza a **hardveres Pillanatképek használatával történő San-alapú helyreállítás engedélyezése** lehetőséget.
    - Ha azt szeretné, hogy a rendszer e-mail-értesítést küldjön a helyreállítási folyamat befejezése után, jelölje be az **E-mail küldése a helyreállítás befejezéséről** lehetőséget, majd adja meg a kívánt e-mail-címeket.

6. Az Összefoglalás képernyőn ellenőrizze az adatok helyességét. Ha a részletek nem megfelelőek, vagy ha módosítani kívánja a módosításokat, válassza a **vissza** lehetőséget. Ha elégedett a beállításokkal **, válassza a helyreállítás lehetőséget** a helyreállítási folyamat elindításához.

7. A helyreállítási feladatra vonatkozó információk a **Helyreállítás állapota** képernyőn jelennek meg.

## <a name="next-steps"></a>Következő lépések

[Adatok helyreállítása az Azure Backup Serverről](./backup-azure-alternate-dpm-server.md)
