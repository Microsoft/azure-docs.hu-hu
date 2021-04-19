---
title: StorSimple 8000 sorozat áttelepítése Azure File Sync
description: Útmutató storSimple 8100- vagy 8600-as berendezés mikori Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7bde8fe404e0839bf14500bff4fb92ce8cc4ea04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717350"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 és 8600 migrálás Azure File Sync

A StorSimple 8000 sorozatot a 8100-as vagy a 8600-as fizikai, helyszíni berendezés és azok felhőszolgáltatás-összetevői képviselik. Az adatokat bármelyik berendezésről át lehet mikalálni egy Azure File Sync környezetbe. Azure File Sync az alapértelmezett és stratégiai hosszú távú Azure-szolgáltatás, amelybe a StorSimple-berendezések migrálhatóak.

A StorSimple 8000-es [](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) sorozat 2022 decemberéig fog véget érni. Fontos, hogy a lehető leghamarabb megkezdődhet a migrálás megtervezése. Ez a cikk a sikeres áttelepítéshez szükséges háttérismereteket és migrálási lépéseket Azure File Sync.

## <a name="phase-1-prepare-for-migration"></a>1. fázis: Felkészülés a migrálásra

Ez a szakasz a StorSimple-kötetek Azure-fájlmegosztásokbe való migrálásának elején szükséges lépéseket tartalmazza.

### <a name="inventory"></a>Leltár

A migrálás megtervezésekor először azonosítsa az összes migrálni szükséges StorSimple-berendezést és -kötetet. Miután ezt megtette, eldöntheti, melyik migrálási útvonal a legmegfelelőbb az Ön számára.

* A StorSimple fizikai berendezések (8000-es sorozat) ezt a migrálási útmutatót használják.
* A [StorSimple 1200](storage-files-migration-storsimple-1200.md)sorozatú virtuális berendezésekhez használjon egy másik migrálási útmutatót.

### <a name="migration-cost-summary"></a>A migrálás költségeinek összegzése

A StorSimple-kötetek Azure-fájlmegosztásaiba migrálási feladatokon keresztüli migrálás ingyenes StorSimple Data Manager erőforrásban. A migrálás során és után egyéb költségek is felmerülnek:

* **Hálózati forgalom:** A StorSimple-fájlok egy adott Azure-régión belüli tárfiókban vannak. Ha az átkért Azure-fájlmegosztásokat egy ugyanabban az Azure-régióban található tárfiókba osztja ki, a rendszer nem számít fel ki- és visszalépési költségeket. A migrálás részeként áthelyezheti a fájlokat egy másik régióban található tárfiókba. Ebben az esetben a ki- és visszalépési költségek vonatkoznak Önre.
* **Azure-fájlmegosztási tranzakciók:** Amikor fájlokat másol egy Azure-fájlmegosztásba (a migrálás részeként vagy azon kívül), a tranzakciós költségek a fájlok és metaadatok írása során jelentkeznek. Ajánlott eljárásként indítsa el az Azure-fájlmegosztást a tranzakcióoptimalált szinten a migrálás során. A migrálás befejezése után váltson a kívánt szintre. Ezt a következő fázisok a megfelelő ponton mutatják be.
* **Azure-fájlmegosztási szint módosítása:** Azure-fájlmegosztás költségtranzakcióinak módosítása. A legtöbb esetben költséghatékonyabb, ha követi az előző pont tanácsait.
* **Tárolási költségek:** Amikor a migrálás elkezd fájlokat másolni egy Azure-fájlmegosztásba, Azure Files a rendszer felhasználja és kiszámlázja a tárterületet. A migrált biztonsági másolatok [Azure-fájlmegosztási pillanatképek lesznek.](storage-snapshots-files.md) A fájlmegosztási pillanatképek csak a tárkapacitást fogyasztják az eltéréseikhez.
* **StorSimple:** Amíg nincs lehetősége megszüntetni a StorSimple-eszközöket és -tárfiókokat, a StorSimple tárolási, biztonsági mentési és berendezési költségei továbbra is bekövetkeznek.

### <a name="direct-share-access-vs-azure-file-sync"></a>Közvetlen megosztás-hozzáférés és Azure File Sync

Az Azure-fájlmegosztások új lehetőségeket nyitnak meg a fájlszolgáltatások üzembe helyezésének strukturálása terén. Az Azure-fájlmegosztások csupán SMB-megosztások a felhőben, amelyek segítségével a felhasználók közvetlenül az SMB protokollon keresztül férhetnek hozzá a már ismerős Kerberos-hitelesítéssel és a meglévő NTFS-engedélyekkel (fájl- és mappa ACL-ek) natív módon. További információ az [Azure-fájlmegosztások identitásalapú hozzáférésről.](storage-files-active-directory-overview.md)

A közvetlen hozzáférés alternatívája a [Azure File Sync.](./storage-sync-files-planning.md) Azure File Sync a StorSimple azon képességének közvetlen analógiása, hogy a gyakran használt fájlokat a helyszínen gyorsítótárazza.

Azure File Sync microsoftos felhőszolgáltatás, amely két fő összetevőn alapul:

* Fájlszinkronizálás és felhőbeli rétegezés a teljesítmény-hozzáférési gyorsítótár létrehozásához bármely Windows Serveren.
* A fájlmegosztások natív tárolóként érhetők el az Azure-ban, amely több protokollon, például az SMB-protokollon és a fájl REST-en keresztül érhető el.

Az Azure-fájlmegosztások megőrzik a tárolt fájlok( például attribútumok, engedélyek és időbélyegek) fontos fájlhűségi szempontjait. Az Azure-fájlmegosztások esetében már nincs szükség arra, hogy egy alkalmazás vagy szolgáltatás értelmezze a felhőben tárolt fájlokat és mappákat. Ezeket natív módon, ismerős protokollokkal és ügyfelekkel, például a Windows-ügyféllel Fájlkezelő. Az Azure-fájlmegosztások lehetővé teszik, hogy általános célú fájlkiszolgálói adatokat és alkalmazásadatokat tárol a felhőben. Az Azure-fájlmegosztások biztonsági mentése egy beépített funkció, amely tovább javítható a Azure Backup.

Ez a cikk a migrálási lépésekre összpontosít. Ha többet szeretne megtudni az áttelepítés Azure File Sync előtt, tekintse meg a következő cikkeket:

* [Azure File Sync áttekintés](./storage-sync-files-planning.md "Áttekintés")
* [Azure File Sync üzembe helyezési útmutató](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple szolgáltatás adattitkosítási kulcsa

A StorSimple-berendezés első beállításakor az létrehozott egy "szolgáltatásadat-titkosítási kulcsot", és arra kérte, hogy tárolja biztonságosan a kulcsot. Ezzel a kulccsal titkosíthatja a társított Azure-tárfiók összes adatát, ahol a StorSimple-berendezés tárolja a fájlokat.

A "szolgáltatásadat-titkosítási kulcs" szükséges a sikeres migráláshoz. Ideje lekérni ezt a kulcsot a rekordokból, egyet a leltárban minden egyes berendezéshez.

Ha nem találja a kulcsokat a rekordokban, létrehozhat egy új kulcsot a berendezésből. Minden berendezés egyedi titkosítási kulccsal rendelkezik.

#### <a name="change-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs módosítása

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> Amikor a StorSimple-berendezéshez való csatlakozásról dönt, vegye figyelembe a következőket:
>
> * A HTTPS-munkameneten keresztüli csatlakozás a legbiztonságosabb és ajánlott megoldás.
> * Az eszköz soros konzoljához való közvetlen csatlakozás biztonságos, de a soros konzolhoz hálózati kapcsolókon keresztül nem.
> * A HTTP-munkamenet-kapcsolatok választhatók, de *nincsenek titkosítva.* Csak akkor ajánlottak, ha zárt, megbízható hálózaton belül használják őket.

### <a name="storsimple-volume-backups"></a>StorSimple-kötetek biztonsági mentései

A StorSimple különbségi biztonsági mentéseket biztosít a kötet szintjén. Az Azure-fájlmegosztások is képesek erre a képességre, az úgynevezett megosztási pillanatképek.
Az áttelepítési feladatok csak biztonsági másolatokat tudnak áthelyezni, adatokat nem az élő kötetről. Ezért a legutóbbi biztonsági mentésnek mindig szerepel a migrálás során áthelyezve készült biztonsági másolatok listájában.

Döntse el, hogy kell-e áthelyezni a régebbi biztonsági másolatokat a migrálás során.
Ajánlott minél kisebb méretű listát tartani, hogy az áttelepítési feladatok gyorsabban befejeződnek.

Az áttelepíteni szükséges kritikus fontosságú biztonsági mentések azonosításához készítse el a biztonsági mentési szabályzatok ellenőrzőlistát. Ilyenek például a következők:
* A legutóbbi biztonsági mentés. (Megjegyzés: A legutóbbi biztonsági mentésnek mindig a lista részének kell lennie.)
* Havonta egy biztonsági másolat 12 hónapig.
* Évente egy biztonsági másolat három évre. 

Később, az áttelepítési feladatok létrehozásakor a lista segítségével azonosíthatja a StorSimple-kötetek pontosan azokat a biztonsági másolatokat, amelyek át lesznek telepítve a listában található követelmények kielégítéséhez.

> [!CAUTION]
> **50-esnél több** StorSimple-kötet biztonsági mentésének kiválasztása nem támogatott.
> Az áttelepítési feladatok csak biztonsági másolatokat tudnak áthelyezni, adatokat soha nem az élő kötetről. Ezért a legutóbbi biztonsági mentés az élő adatokhoz a legközelebb áll, ezért mindig az áttelepítés során áthelyezni szükséges biztonsági másolatok listájának kell lennie.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Meglévő StorSimple-kötetek leképezása Azure-fájlmegosztásra

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Tárfiókok száma

A migrálás valószínűleg előnyös lehet több tárfiók üzembe helyezésének, amelyek mindegyikében kevesebb Azure-fájlmegosztás található.

Ha a fájlmegosztások rendkívül aktívak (sok felhasználó vagy alkalmazás használja őket), két Azure-fájlmegosztás elérheti a tárfiók teljesítménykorlátját. Ezért az ajánlott eljárás az, ha több tárfiókba mikalkulál, amelyek mindegyikének saját fájlmegosztása van, és általában tárfiókonként kettő vagy három megosztásnál nem több.

Az ajánlott eljárás a tárfiókok üzembe helyezése egy-egy fájlmegosztással. Ha archiválási megosztásokkal rendelkezik, több Azure-fájlmegosztást is készletbe tud venni ugyanabban a tárfiókban.

Ezek a szempontok inkább a közvetlen [felhőalapú](#direct-share-access-vs-azure-file-sync) hozzáférésre (egy Azure-beli virtuális gépen vagy szolgáltatáson keresztül) vonatkoznak, mint a Azure File Sync. Ha kizárólag a megosztások Azure File Sync használni, akkor több is egyetlen Azure-tárfiókba csoportosít. A jövőben előfordulhat, hogy olyan alkalmazást szeretne átemelni a felhőbe, amely közvetlenül hozzáfér egy fájlmegosztáshoz. Ez a forgatókönyv jobb IOPS-t és átviteli sebességet biztosít. Vagy elkezdhet olyan szolgáltatást használni az Azure-ban, amely szintén nagyobb IOPS-t és átviteli sebességet biztosít.

Ha listát készített a megosztásokról, leképezi az egyes megosztásokat arra a tárfiókra, ahol a megosztás található.

> [!IMPORTANT]
> Döntsön egy Azure-régiót, és győződjön meg arról, hogy minden tárfiók és Azure File Sync erőforrás megegyezik a kiválasztott régióval.
> Most ne konfigurálja a tárfiókok hálózati és tűzfalbeállítását. Ezen konfigurációk ezen a ponton való beállítása lehetetlenné tenné a migrálást. A migrálás befejezése után konfigurálja ezeket az Azure Storage-beállításokat.

### <a name="phase-1-summary"></a>1. fázis összegzése

Az 1. fázis végén:

* A StorSimple-eszközök és -kötetek áttekintése jól áttekinthető.
* A Data Manager szolgáltatás készen áll a felhőben található StorSimple-kötetek elérésére, mivel minden StorSimple-eszközhöz lekérte a "szolgáltatásadat-titkosítási kulcsot".
* Van egy terve arra, hogy mely köteteket és biztonsági másolatokat kell migrálni (ha azok a legutóbbiakon túliak).
* Tudja, hogyan lehet leképezni a köteteket a megfelelő számú Azure-fájlmegosztásra és tárfiókra.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>2. fázis: Az Azure Tárolási és migrálási erőforrásainak üzembe helyezése

Ez a szakasz az Azure-ban szükséges különböző erőforrástípusok üzembe helyezésével kapcsolatos szempontokat ismerteti. Néhány a migrálás után fogja tartalmazni az adatokat, néhány pedig csak a migráláshoz szükséges. Ne kezdje el üzembe helyezni az erőforrásokat, amíg el nem véglegesítette az üzembe helyezési tervet. Az üzembe helyezésük után nehéz, néha lehetetlen megváltoztatni az Azure-erőforrások bizonyos aspektusait.

### <a name="deploy-storage-accounts"></a>Tárfiókok üzembe helyezése

Valószínűleg több Azure Storage-fiókot is üzembe kell helyeznie. Mindegyik kisebb számú Azure-fájlmegosztást fog tartani a telepítési tervnek megfelelően, a cikk előző szakaszában befejezettek szerint. A tervezett tárfiókok Azure Portal a következő [hellyel:](../common/storage-account-create.md#create-a-storage-account). Fontolja meg az alábbi alapbeállításokat minden új tárfiókhoz.

> [!IMPORTANT]
> Most ne konfigurálja a tárfiókok hálózati és tűzfalbeállítását. Ezen konfigurációk ezen a ponton való beállítása lehetetlenné tenné a migrálást. A migrálás befejezése után konfigurálja ezeket az Azure Storage-beállításokat.

#### <a name="subscription"></a>Előfizetés

Használhatja ugyanazt az előfizetést, mint amit a StorSimple üzembe helyezéséhez használt, vagy egy másikat. Az egyetlen korlátozás az, hogy az előfizetésnek a StorSimple-előfizetéssel Azure Active Directory bérlőben kell lennie. A migrálás előtt fontolja meg a StorSimple-előfizetés áthelyezését a megfelelő bérlőre. Csak a teljes előfizetést áthelyezheti, az egyes StorSimple-erőforrásokat nem lehet másik bérlőbe vagy előfizetésbe áthelyezni.

#### <a name="resource-group"></a>Erőforráscsoport

Az erőforráscsoportok segítenek az erőforrások és a rendszergazdai felügyeleti engedélyek rendszerezésében. További információ az [Azure-beli erőforráscsoportokról.](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)

#### <a name="storage-account-name"></a>Tárfiók neve

A tárfiók neve egy URL-cím része lesz, és bizonyos karakterkorlátozásokkal rendelkezik. Az elnevezési konvenció szerint a tárfiókok nevének egyedinek kell lennie a világon, csak kisbetűket és számokat kell engedélyeznie, 3–24 karakter hosszúságúnak kell lennie, és nem engedélyez olyan speciális karaktereket, mint a kötőjel vagy az aláhúzásjel. További információ: [Az Azure Storage erőforrás-elnevezési szabályai.](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)

#### <a name="location"></a>Hely

A tárfiók helye vagy Azure-régiója nagyon fontos. Ha a Azure File Sync használja, az összes tárfióknak ugyanabban a régióban kell lennie, mint a Storage Sync Service-erőforrásnak. A megfelelő Azure-régiónak a helyi kiszolgálók és felhasználók közelében vagy központilag kell lennie. Az erőforrás üzembe helyezése után nem módosíthatja annak régióját.

Választhat egy másik régiót is attól a régiótól, ahol a StorSimple-adatok (tárfiók) jelenleg találhatók.

> [!IMPORTANT]
> Ha az aktuális StorSimple-tárfiók helyétől eltérő régiót [választ,](https://azure.microsoft.com/pricing/details/bandwidth) a migrálás során a rendszer díjat fog fizetni a forgalomért. Az adatok elhagyják a StorSimple régiót, és meg fogják adnia az új tárfiók régióját. Ha ugyanabban az Azure-régióban marad, sávszélességgel nem kell fizetni.

#### <a name="performance"></a>Teljesítmény

Választhat prémium szintű tárolót (SSD) az Azure-fájlmegosztások vagy a standard szintű tárolók számára. A Standard szintű [tárolás több réteget tartalmaz a fájlmegosztáshoz.](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share) A standard szintű tárolás a megfelelő választás a StorSimple-ről mirating legtöbb ügyfél számára.

Még mindig nem biztos benne?

* Válassza a Premium Storage lehetőséget, ha [prémium szintű Azure-fájlmegosztás teljesítményére van szüksége.](understanding-billing.md#provisioned-model)
* Válassza a standard szintű tárolót az általános célú fájlkiszolgálói számítási feladatokhoz, beleértve a gyors adatokat és az archív adatokat. Akkor is válassza a standard szintű tárolást, ha a felhőbeli megosztáson az egyetlen számítási feladat Azure File Sync.

#### <a name="account-kind"></a>Fiók altípusa

* Standard szintű tároláshoz válassza a *StorageV2 (általános célú v2) lehetőséget.*
* Prémium fájlmegosztások esetén válassza a *FájlStorage lehetőséget.*

#### <a name="replication"></a>Replikáció

Számos replikációs beállítás érhető el. További információ a különböző replikációs típusokról.

Csak az alábbi két lehetőség közül választhat:

* *Helyileg redundáns tárolás (LRS).*
* *Zónaredundáns tárolás (ZRS),* amely nem minden Azure-régióban érhető el.

> [!NOTE]
> Csak az LRS és a ZRS redundanciatípus kompatibilis a nagy, 100 TiB-kapacitású Azure-fájlmegosztásokkal.

A georedundáns tárolás (GRS) jelenleg minden változatban nem támogatott. A redundancia típusát később átválthatja GRS-re, ha az Azure-ba érkezik támogatás.

#### <a name="enable-100-tib-capacity-file-shares"></a>100 TiB-kapacitású fájlmegosztás engedélyezése

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="A tárfiók létrehozásához Azure Portal speciális lapot bemutató kép.":::
    :::column-end:::
    :::column:::
        Az új **tárfiók** varázsló Speciális szakaszában a Azure Portal engedélyezheti a  nagyméretű fájlmegosztások támogatását ebben a tárfiókban. Ha ez a lehetőség nem érhető el, valószínűleg nem a megfelelő redundanciatípust választotta. Győződjön meg arról, hogy csak az LRS vagy a ZRS lehetőséget választja, hogy ez a lehetőség elérhetővé váljon.
    :::column-end:::
:::row-end:::

A nagyméretű, 100 TiB-kapacitású fájlmegosztások mellett több előnye is van:

* A teljesítmény jelentősen megnő a kisebb, 5 TiB-kapacitású fájlmegosztások (például az IOPS 10-szer nagyobb) méretéhez képest.
* A migrálás jelentősen gyorsabb lesz.
* Biztosíthatja, hogy a fájlmegosztás elegendő kapacitással rendelkezik az összes átemelni kívánt adat tárolásához, beleértve a tárolási kapacitás különbségi biztonsági mentéséhez szükséges adatokat is.
* A jövőbeli növekedést is lefedjük.

### <a name="azure-file-shares"></a>Azure-fájlmegosztások

A tárfiókok létrehozása után  a tárfiók Fájlmegosztás szakaszában üzembe helyezheti a megfelelő számú Azure-fájlmegosztást az 1. fázisból származó migrálási tervnek megfelelően. Fontolja meg a következő alapbeállításokat az Azure-beli új fájlmegosztások esetében.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Képernyőkép Azure Portal új fájlmegosztás felhasználói felületről.":::
    :::column-end:::
    :::column:::
        </br>**Név**</br>Kisbetűk, számok és kötőjelek is támogatottak.</br></br>**Kvóta**</br>A kvóta itt a Windows Server-példányon található SMB-kvótához hasonlítható. Az ajánlott eljárás az, hogy itt ne állítson be kvótát, mert a migrálás és más szolgáltatások sikertelenek lesznek a kvóta elérésekor.</br></br>**Szolgáltatási szintek**</br>Válassza **az új fájlmegosztáshoz** optimalizált tranzakció lehetőséget. A migrálás során számos tranzakció történik. Költséghatékonyabb, ha később a számítási feladatnak leginkább megfelelő szintre módosítja a szintet.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

A migrálási feladatokat kezelő Azure-erőforrás neve **StorSimple Data Manager.** Válassza **az Új erőforrás** lehetőséget, és keresse meg. Ezután kattintson a **Létrehozás** elemre.

Ez az ideiglenes erőforrás a vezényléshez használatos. A migrálás befejezése után megszünteti azt. Ugyanabban az előfizetésben, erőforráscsoportban és régióban kell üzembe helyezni, mint a StorSimple-tárfiók.

### <a name="azure-file-sync"></a>Azure File Sync

A Azure File Sync a leggyakrabban használt fájlok helyszíni gyorsítótárazása is hozzáadható. A StorSimple gyorsítótárazási képességeihez hasonlóan az Azure File Sync felhőbeli rétegezés helyi hozzáférési késést is kínál, valamint nagyobb kontrollt biztosít a Windows Server-példányon elérhető gyorsítótár-kapacitás és a többhelyű szinkronizálás felett. Ha a helyszíni gyorsítótár használata a cél, akkor a helyi hálózaton készítsen elő egy Windows Server rendszerű virtuális gépet (a fizikai kiszolgálók és feladatátvevő fürtök is támogatottak) megfelelő, közvetlenül csatlakoztatott tárolókapacitással.

> [!IMPORTANT]
> Még ne állítsa be Azure File Sync- A megosztás migrálásának Azure File Sync után ajánlott beállítani. A Azure File Sync nem kezdődhet a migrálás 4. fázisa előtt.

### <a name="phase-2-summary"></a>2. fázis összegzése

A 2. fázis végén üzembe fogja helyezni a tárfiókokat és az összes Azure-fájlmegosztást. Emellett egy új erőforrással StorSimple Data Manager is. Az utóbbit a 3. fázisban fogja használni az áttelepítési feladatok konfigurálásakor.

## <a name="phase-3-create-and-run-a-migration-job"></a>3. fázis: Áttelepítési feladat létrehozása és futtatása

Ez a szakasz azt ismerteti, hogyan állíthat be migrálási feladatot, és hogyan lehet gondosan leképezni a StorSimple-kötet könyvtárát a kiválasztott cél Azure-fájlmegosztásba. Első lépésekként keresse meg a StorSimple Data Manager, keresse meg a **feladatdefiníciókat** a menüben, majd válassza **a + Feladatdefiníció lehetőséget.** A megfelelő céltárolótípus az alapértelmezett: **Azure-fájlmegosztás.**

![A StorSimple 8000 sorozat áttelepítési feladattípusa.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Képernyőkép a megnyitott feladatdefiníciókról Azure Portal megnyílik egy új feladatdefiníciók párbeszédpanel, amely a feladat típusát kéri: Másolás fájlmegosztásba vagy blobtárolóba.")

:::row:::
    :::column:::
        ![StorSimple 8000 sorozatú migrálási feladat.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Képernyőkép az áttelepítési feladat új feladat-létrehozási űrlapról.")
    :::column-end:::
    :::column:::
        **Feladatdefiníció neve**</br>Ennek a névnek jeleznie kell az áthelyezés alatt lévő fájlokat. Az Azure-fájlmegosztáshoz hasonló nevet kell adni neki. </br></br>**A feladat futtatás helye**</br>Egy régió kiválasztásakor ugyanazt a régiót kell kiválasztania, mint a StorSimple-tárfiók, vagy ha ez nem érhető el, akkor egy ahhoz közeli régiót. </br></br><h3>Forrás</h3>**Forrás-előfizetés**</br>Válassza ki azt az előfizetést, amelyben a StorSimple-Eszközkezelő tárolja. </br></br>**StorSimple-erőforrás**</br>Válassza ki a StorSimple Eszközkezelő amikor a berendezés regisztrálva van. </br></br>**Szolgáltatásadat-titkosítási kulcs**</br>Tekintse meg [a cikk előző szakaszát,](#storsimple-service-data-encryption-key) ha nem találja a kulcsot a rekordokban. </br></br>**Eszköz**</br>Válassza ki azt a StorSimple-eszközt, amely azt a kötetet tartalmazza, ahová az áttelepítést szeretné. </br></br>**Kötet**</br>Válassza ki a forráskötetet. Később eldöntheti, hogy a teljes kötetet vagy alkönyvtárakat át szeretné-e miolni a cél Azure-fájlmegosztásba.</br></br> **Kötetek biztonsági mentései**</br>A Kötet biztonsági *mentésének kiválasztása* lehetőséget választva kiválaszthatja a feladat részeként áthelyezni kívánt biztonsági másolatokat. A cikk egy hamarosan megjelenő, [dedikált szakasza](#selecting-volume-backups-to-migrate) részletesen ismerteti a folyamatot.</br></br><h3>Cél</h3>Válassza ki az előfizetést, a tárfiókot és az Azure-fájlmegosztást a migrálási feladat céljaként.</br></br><h3>Címtárleképezés</h3>[A cikk egy dedikált szakasza](#directory-mapping)az összes releváns részletet tárgyalja.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Az áttérni kívánt kötetek biztonsági mentésének kiválasztása

A migrálni szükséges biztonsági másolatok kiválasztásakor fontos szempontokat kell figyelembe venni:

- Az áttelepítési feladatok csak biztonsági másolatokat tudnak áthelyezni, élő kötetről származó adatokat nem. Így a legutóbbi biztonsági mentés az élő adatokhoz a legközelebb áll, és mindig az áttelepítés során áthelyezve lévő biztonsági másolatok listájában kell lennie.
- Győződjön meg arról, hogy a legutóbbi biztonsági mentés friss, hogy az élő megosztás eltérése a lehető legkisebb legyen. Az áttelepítési feladat létrehozása előtt érdemes lehet manuálisan aktiválni és elvégezni egy másik kötet biztonsági mentését. Az élő megosztás kis eltérése javítja a migrálási élményt. Ha ez a változás lehet nulla = nincs több módosítás a StorSimple-köteten, miután a legújabb biztonsági mentés készült a listában – akkor az 5. fázis: A felhasználói átvágás jelentősen leegyszerűsödött és felgyorsült.
- A biztonsági másolatokat vissza kell játszani az Azure-fájlmegosztásba a legrégebbitől a **legújabbig.** A régebbi biztonsági másolatok nem rendezhetőek az Azure-fájlmegosztás biztonsági másolatai listájába a migrálási feladat futtatása után. Ezért a feladat létrehozása előtt győződjön  meg arról, hogy a biztonsági másolatok listája befejeződött. 
- A feladatban található biztonsági másolatok listája nem módosítható a feladat létrehozása után, még akkor sem, ha a feladat soha nem futott. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Képernyőkép az új feladat-létrehozási űrlapról, amely azt a részt részletezi, ahol a StorSimple biztonsági másolatai ki vannak választva a migráláshoz." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Az áttelepítési feladathoz a StorSimple-kötet biztonsági mentésének kiválasztásához válassza a *Kötet* biztonsági mentésének kiválasztása lehetőséget a feladat létrehozási űrlapján.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Egy kép, amely azt mutatja, hogy a panel felső része a biztonsági másolatok kiválasztásához felsorolja az összes rendelkezésre álló biztonsági másolatot. A kiválasztott biztonsági másolat szürkén jelenik meg ebben a listában, és a panel alsó részén található második listához lesz hozzáadva. Itt ismét törölhető." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Amikor megnyílik a biztonsági mentést kiválasztó panel, két lista választja el egymástól. Az első listában az összes rendelkezésre álló biztonsági másolat megjelenik. Az eredményhalmazt egy adott időtartományra szűrve bővítheti és szűkítheti. (lásd a következő szakaszt) </br></br>A kiválasztott biztonsági mentés szürkén jelenik meg, és a panel alsó felén egy második listához lesz hozzáadva. A második lista a migráláshoz kiválasztott összes biztonsági másolatot megjeleníti. A hibában kiválasztott biztonsági másolat szintén eltávolítható.
        > [!CAUTION]
        > Ki kell **választania az** összes átemelni kívánt biztonsági másolatot. A későbbiekben nem adhat hozzá régebbi biztonsági másolatokat. A feladat létrehozása után nem módosíthatja a kiválasztott feladatot.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Képernyőkép a biztonsági mentés kijelölési panelje időtartományának kiválasztásáról." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Alapértelmezés szerint a lista úgy van szűrve, hogy az elmúlt hét napban a StorSimple-kötetek biztonsági másolatai is könnyen kiválaszthatóak legyen. A múltban további biztonsági mentések készítéséhez használja a panel tetején található időtartomány-szűrőt. Választhat egy meglévő szűrőt, vagy egyéni időtartományt állíthat be, hogy csak az ebben az időszakban készült biztonsági másolatokat szűrje.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> 50-esnél több StorSimple-kötet biztonsági mentésének kiválasztása nem támogatott. A nagy számú biztonsági mentéssel való feladatok meghiúsulhatnak.

### <a name="directory-mapping"></a>Címtárleképezés

A címtárleképezés nem kötelező az áttelepítési feladathoz. Ha üresen hagyja  a szakaszt, a StorSimple-kötet gyökerében található összes fájl és mappa a cél Azure-fájlmegosztás gyökerében lesz áthelyezve. A legtöbb esetben egy teljes kötet tartalmának Azure-fájlmegosztásban való tárolása nem a legjobb megoldás. Gyakran jobb, ha egy kötet tartalmát több fájlmegosztásra osztja szét az Azure-ban. Ha még nem készített tervet, először tekintse meg a [StorSimple-kötet Azure-fájlmegosztásra való leképezéséről való használatát.](#map-your-existing-storsimple-volumes-to-azure-file-shares)

Előfordulhat, hogy a migrálási terv részeként úgy döntött, hogy a StorSimple-kötetek mappáit több Azure-fájlmegosztás között kell szétosztani. Ebben az esetben ezt a felosztást a következővel valósíthatja meg:

1. Több feladat definiálása egy kötet mappáinak áttelepítése érdekében. Mindegyik ugyanazokkal a StorSimple-kötetforrással, de a célként egy másik Azure-fájlmegosztással fog.
1. Adja meg pontosan, hogy a StorSimple-kötet mely mappáit kell áttelepíteni a megadott fájlmegosztásba a feladat-létrehozási űrlap **Könyvtárleképezés** szakaszának használatával, és az adott leképezési [szemantikát követi.](#semantic-elements)

> [!IMPORTANT]
> Az űrlap elérési útjai és leképezési kifejezései nem érvényesíthetőek az űrlap beküldésekor. Ha a leképezések helytelenül vannak megadva, előfordulhat, hogy egy feladat teljesen meghiúsul, vagy nem kívánt eredményt ad. Ebben az esetben általában az a legjobb, ha törli az Azure-fájlmegosztást, újra létrehozza, majd kijavítja a leképezési utasításokat egy új migrálási feladatban a megosztáshoz. Ha rögzített leképezési utasításokkal futtat egy új feladatot, azzal kijavíthatja a kihagyott mappákat, és behozhatja azokat a meglévő megosztásba. Ezzel a módszersel azonban csak az elérésiút-helytelenül írt mappákat lehet kezelni.

#### <a name="semantic-elements"></a>Szemantikai elemek

A leképezés balról jobbra haladva van kifejezve: [\forrás elérési útja] \> [\cél elérési útja].

|Szemantikai karakter          | Értelmezés  |
|:---------------------------|:---------|
| **\\**                     | Gyökérszintű jelző.       |
| **\>**                     | [Source] és [target-mapping] operátor.     |
|**\|** vagy RETURN (új sor) | Két mappaleképezési utasítás elválasztója. </br>Másik lehetőségként kihagyhatja ezt a karaktert, és az **Enter** billentyűt választva saját sorában is lekérte a következő leképezési kifejezést.        |

### <a name="examples"></a>Példák
Áthelyezi a Felhasználói adatok *mappa tartalmát* a célfájlmegosztás gyökerére:
``` console
\User data > \
```
Áthelyezi a teljes kötet tartalmát egy új elérési útra a célfájlmegosztáson:
``` console
\ > \Apps\HR tracker
```
Áthelyezi a forrásmappa tartalmát egy új elérési útra a célfájlmegosztáson:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Több forráshelyet új könyvtárstruktúrába rendez:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Szemantikai szabályok

* A mappa elérési útjait mindig a gyökérszinthez viszonyítva adja meg.
* Kezdje el az egyes mappaútvonalak gyökérszintű jelzővel" \\ ".
* Ne foglalja bele a meghajtóbetűket.
* Több elérési út megadásakor a forrás- vagy célútvonalak nem fedhetik át egymást:</br>
   Érvénytelen forrásútvonal átfedése – példa:</br>
    *\\folder\1 > \\ mappa*</br>
    *\\folder \\ 1 \\ 2 > \\ folder2*</br>
   Érvénytelen célútvonal átfedése – példa:</br>
   *\\mappa > \\*</br>
   *\\folder2 > \\*</br>
* A rendszer figyelmen kívül hagyja a nem létező forrásmappákat.
* Létrejönnek a célon nem létező mappastruktúrák.
* A Windowshoz hasonlóan a mappanevek is nem a kis- és a nagy- és

> [!NOTE]
> Az áttelepítési feladat nem másolja át a StorSimple-köteten található *\System Volume Information* mappa és *$Recycle.Bin* fájl tartalmát.

### <a name="run-a-migration-job"></a>Áttelepítési feladat futtatása

Az áttelepítési feladatok az *erőforráscsoportban* üzembe helyezett Data Manager erőforrás feladatdefiníciói alatt vannak felsorolva.
A feladatdefiníciók listájából válassza ki a futtatni kívánt feladatot.

A megnyíló feladat panelen az alsó listában láthatja a feladatfutokat. Ez a lista kezdetben üres lesz. A panel tetején található egy Run job (Feladat *futtatása) nevű parancs.* Ez a parancs nem futtatja azonnal a feladatot, hanem megnyitja a **Feladatfuttatás panelt:**

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="Kép a feladatfuttassa panelről megnyitott legördülő vezérlővel, amely az áttelepítendő kiválasztott biztonsági másolatokat jeleníti meg. A legrégebbi biztonsági mentés ki van emelve, először ki kell választani." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        Ebben a kiadásban minden feladatot többször kell futtatni. </br></br>**Az átemelni kívánt biztonsági másolatok listájából a legrégebbi biztonsági másolatból kell indulni.** (a képen kiemelve)</br></br>A feladatot annyiszor futtatja újra, ahányszor kiválasztotta a biztonsági másolatokat, minden alkalommal egy fokozatosan újabb biztonsági mentéssel.
        </br></br>
        > [!CAUTION]
        > Rendkívül fontos, hogy az áttelepítési feladatot úgy futtassa, hogy először a legrégebbi biztonsági másolatot válassza ki, majd újra, minden alkalommal egy fokozatosan újabb biztonsági mentéssel. Mindig manuálisan kell karbantartani a biztonsági másolatok sorrendjét – a legrégebbitől a legújabbig.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>3. fázis összegzése

A 3. fázis végén futtatnia kell legalább egy migrálási feladatát a StorSimple-kötetek és az Azure-fájlmegosztások között. Ugyanazt az áttelepítési feladatot többször is futtatni fogja, a legrégebbitől a legújabbig, áttelepíteni kell a biztonsági mentéseket. Most arra összpontosíthat, hogy be kell Azure File Sync a megosztáshoz (miután befejeződött a megosztás migrálási feladatának befejezése), vagy az információs munkatársak és alkalmazások megosztási hozzáférését az Azure-fájlmegosztáshoz irányíthatja.

## <a name="phase-4-access-your-azure-file-shares"></a>4. fázis: Az Azure-fájlmegosztások elérése

Az Azure-fájlmegosztások elérésének két fő stratégiája van:

* **Azure File Sync:** [Azure File Sync](#deploy-azure-file-sync) üzembe helyezése egy helyszíni Windows Server-példányon. Azure File Sync helyi gyorsítótár összes előnye a StorSimple-höz hasonló.
* **Közvetlen megosztás-hozzáférés:** [Közvetlen megosztás-hozzáférés üzembe helyezése.](#deploy-direct-share-access) Akkor használja ezt a stratégiát, ha egy adott Azure-fájlmegosztás hozzáférési forgatókönyve nem használja ki a helyi gyorsítótárazást, vagy ha már nem tud helyszíni Windows Server-példányt üzembe tartani. Itt a felhasználók és az alkalmazások továbbra is az SMB-protokollon keresztül érik el az SMB-megosztásokat. Ezek a megosztások már nem helyszíni kiszolgálón, hanem közvetlenül a felhőben vannak.

Már el kellett döntenünk, hogy melyik lehetőség a legmegfelelőbb az Ön számára az [útmutató 1.](#phase-1-prepare-for-migration) fázisában.

A szakasz további részében az üzembe helyezési utasításokra összpontosítunk.

### <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése

Itt az ideje, hogy üzembe helyezd a Azure File Sync.

1. Hozza létre Azure File Sync felhőerőforrást.
1. Telepítse a Azure File Sync ügynököt a helyszíni kiszolgálón.
1. Regisztrálja a kiszolgálót a felhőerőforrásban.

Még ne hozzon létre szinkronizálási csoportokat. Az Azure-fájlmegosztással való szinkronizálás beállítása csak akkor történhet meg, ha az Azure-fájlmegosztásba való migrálási feladatok befejeződtek. Ha a migrálás Azure File Sync előtt kezdte el használni a Azure File Sync, az feleslegesen megnehezítheti a migrálást, mivel nem lehetett könnyen tudatni vele, hogy mikor kellett elindítani az átállást.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>A felhőalapú Azure File Sync üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Ha módosítani szeretné azt az Azure-régiót, ahol az adatok a migrálás befejezése után találhatók, a Storage Sync Service-t ugyanabban a régióban kell üzembe helyeznie, mint a migrálás céltárfiókját.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Helyszíni Windows Server-példány üzembe helyezése

* Hozza létre a Windows Server 2019-et (legalább 2012R2) virtuális gépként vagy fizikai kiszolgálóként. A Windows Server feladatátvevő fürt is támogatott. Ne használja újra a StorSimple 8100-as vagy 8600-as portot elvezető kiszolgálót.
* Közvetlenül csatlakoztatott tároló kiépítése vagy hozzáadása. A hálózati tárolók nem támogatottak.

Ajánlott az új Windows Server-példány számára a StorSimple 8100-as vagy 8600-as berendezés helyi gyorsítótárazható kapacitásával megegyező vagy nagyobb tárterületet adni. A Windows Server-példányt ugyanúgy fogja használni, mint a StorSimple-berendezést. Ha a tárterülete megegyezik a berendezés tárolókapacitásának, a gyorsítótárazásnak hasonlónak kell lennie, ha nem ugyanaz. A Windows Server-példányhoz hozzáadhat tárhelyet, vagy eltávolíthatja onnan. Ezzel a képességgel skálázható a helyi kötet mérete és a gyorsítótárazható helyi tárterület mennyisége.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>A Windows Server-példány előkészítése fájlszinkronizáláshoz

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>A Azure File Sync konfigurálása a Windows Server-példányon

A folyamathoz a regisztrált helyszíni Windows Server-példánynak készen kell állnia, és csatlakozva kell lennie az internethez.

> [!IMPORTANT]
> A folytatás előtt be kell fejeződnie a fájlok és mappák Azure-fájlmegosztásba történő StorSimple-migrálásának. Győződjön meg arról, hogy a fájlmegosztáson nem történt további módosítás.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Mindenképpen kapcsolja be a felhőbeli rétegezést. A felhőbeli rétegezés az a Azure File Sync, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárkapacitással rendelkezik, mint a felhőben tároltak, de a teljes névtér elérhető. A helyileg érdekes adatokat a rendszer helyileg is gyorsítótárazza a gyors, helyi hozzáférési teljesítmény érdekében. A felhőbeli rétegezés ezen a lépésen való bekapcsolásának egy másik oka az, hogy ebben a szakaszban nem szeretnénk szinkronizálni a fájltartalmat. Jelenleg csak a névtérnek szabad továbblépni.

### <a name="deploy-direct-share-access"></a>Közvetlen megosztási hozzáférés üzembe helyezése

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ez a videó egy útmutató és bemutató arról, hogyan teheti biztonságossá az Azure-fájlmegosztásokat közvetlenül az információkkal dolgozó munkatársak és alkalmazások számára öt egyszerű lépésben.</br>
        A videó egyes témakörök dedikált dokumentációira hivatkozik:

* [Az identitások áttekintése](storage-files-active-directory-overview.md)
* [Tárfiók tartományhoz való csatlakozása](storage-files-identity-auth-active-directory-enable.md)
* [Az Azure-fájlmegosztások hálózatának áttekintése](storage-files-networking-overview.md)
* [Nyilvános és privát végpontok konfigurálása](storage-files-networking-endpoints.md)
* [A konfiguráció S2S VPN](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN konfigurálása](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN konfigurálása](storage-files-configure-p2s-vpn-linux.md)
* [DNS-továbbítás konfigurálása](storage-files-networking-dns.md)
* [Az DFS-N konfigurálása](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>4. fázis összegzése

Ebben a fázisban több áttelepítési feladat létrehozása és futtatása is StorSimple Data Manager. Ezek a feladatok áttelepítik a fájlokat és mappákat az Azure-fájlmegosztásba. Emellett üzembe is Azure File Sync vagy előkészítette a hálózati és tárfiókokat a közvetlen megosztás-hozzáféréshez.

## <a name="phase-5-user-cut-over"></a>5. fázis: Felhasználó-átvágás

Ez a fázis a migrálás burkolt folyamatának a körére szól:

* Tervezze meg az állásidőt.
* A StorSimple oldalon a 3. fázisban a migrálási feladatok futtatása közben végrehajtott felhasználói és alkalmazásai változásainak kezelése.
* A felhasználók feladatátvétele az új Windows Server-példányra Azure File Sync azure-fájlmegosztásokkal közvetlen megosztási hozzáféréssel.

### <a name="plan-your-downtime"></a>Az állásidő megterve

Ehhez a migrálási megközelítéshez némi állásidőre van szükség a felhasználók és az alkalmazások számára. A cél az állásidő minimálisra emelése. A következő szempontok segíthetnek:

* Tartsa rendelkezésre a StorSimple-köteteket a migrálási feladatok futtatása közben.
* Ha befejezte egy megosztás adatáttelepítési feladatának futtatását, ideje eltávolítani a felhasználói hozzáférést (legalább írási hozzáférést) a StorSimple-kötetről vagy -megosztásról. A végső RoboCopy be fogja kapni az Azure-fájlmegosztást. Ezután átveheti a felhasználókat. A RoboCopy futtatásának helyétől függ, hogy a Azure File Sync vagy a közvetlen megosztási hozzáférést választotta. A RoboCopy következő szakasza ezt a témát fedi le.
* Miután befejezte a RoboCopy-catchet, készen áll arra, hogy közvetlenül az Azure-fájlmegosztás vagy egy Windows Server-példányon található SMB-megosztás segítségével tegye elérhetővé az új helyet a Azure File Sync. A DFS-N üzembe helyezése gyakran segít a gyors és hatékony átvágásban. Így a meglévő megosztási címek konzisztensek maradnak, és egy új helyre fognak visszapontosodni, amely az áttelepített fájlokat és mappákat tartalmazza.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Annak megállapítása, hogy a névtér mikor szinkronizált teljesen a kiszolgálóval

Ha a Azure File Sync azure-fájlmegosztáshoz használja, fontos, hogy a helyi RoboCopy elkezdését  megelőzően megállapítsa, hogy a teljes névtér befejezte-e a letöltést a kiszolgálóra. A névtér letöltéséhez szükséges idő az Azure-fájlmegosztásban lévő elemek számától függ. Két módszerrel lehet meghatározni, hogy a névtér teljesen megérkezett-e a kiszolgálóra.

#### <a name="azure-portal"></a>Azure Portal

A névtér Azure Portal láthatja, ha a névtér teljesen megérkezett.

* Jelentkezzen be a Azure Portal, és a szinkronizálási csoporthoz. Ellenőrizze a szinkronizálási csoport és a kiszolgálóvégpont szinkronizálási állapotát.
* Az érdekes irány a letöltés. Ha a kiszolgálóvégpont újonnan ki van építve, kezdeti szinkronizálást fog mutatni, ami azt jelzi, hogy a névtér továbbra is leáll.
Miután a kezdeti szinkronizáláson már nem történt **változás,** a névtér teljesen ki lesz töltve a kiszolgálón. Most folytathatja a helyi RoboCopyval.

#### <a name="windows-server-event-viewer"></a>Windows Server Eseménynapló

A Windows Server-példány Eseménynapló is használhatja annak nevére, hogy a névtér mikor érkezett meg teljesen.

1. Nyissa meg **a Eseménynapló,** majd nyissa meg az **Alkalmazások és szolgáltatások szolgáltatásokat.**
1. Nyissa meg a **Microsoft\FileSync\Agent\Telemetry fájlt.**
1. Keresse meg a **legutóbbi 9102-es** eseményt, amely egy befejezett szinkronizálási munkamenetnek felel meg.
1. Válassza **a Részletek** lehetőséget, és ellenőrizze, hogy olyan eseményt keres-e, ahol a **SyncDirection** értéke **Download (Letöltés).**
1. Abban az időpontban, amikor a névtér letöltve lett a kiszolgálóra, egyetlen esemény lesz, a **FullGhostedSync** és a **HResult**  =  **0 értékkel.**
1. Ha kihagyja ezt az eseményt, más **9102-es** eseményeket is keresnie kell a **SyncDirection** Download és  =   **a**  =  **Scenario "RegularSync" forgatókönyv használatával.** Az egyik ilyen esemény megkeresése azt is jelzi, hogy a névtér letöltése és szinkronizálása befejeződött a normál szinkronizálási munkamenetek között, függetlenül attól, hogy van-e szinkronizálható vagy nem szinkronizálható esemény.

### <a name="a-final-robocopy"></a>A végső RoboCopy

Ezen a ponton különbségek vannak a helyszíni Windows Server-példány és a StorSimple 8100 vagy 8600 berendezés között.

1. Be kell látnia a felhasználók vagy alkalmazások által a StorSimple oldalán a migrálás során végrehajtott módosításokat.
1. A StorSimple Azure File Sync használata esetén: A StorSimple-berendezés ki van töltve a Windows Server-példánysal szemben, és csak egy névtérrel rendelkezik, amely jelenleg nem tárol helyileg tárolt fájltartalmat. A végső RoboCopy segítségével gyorsan elindíthatja a helyi Azure File Sync-gyorsítótárat azáltal, hogy a helyileg gyorsítótárazott fájlok tartalmát a rendelkezésre álló helyére húzza, és elfér a Azure File Sync kiszolgálón.
1. Előfordulhat, hogy egyes fájlokat az áttelepítési feladat érvénytelen karakterek miatt maradt hátra. Ha igen, másolja őket a Azure File Sync Windows Server-példányra. Később módosíthatja őket, hogy szinkronizálva legyenek. Ha nem használ Azure File Sync megosztáshoz, jobb, ha a StorSimple-köteten érvénytelen karaktereket tartalmaznak. Ezután futtassa a RoboCopyt közvetlenül az Azure-fájlmegosztáson.

> [!WARNING]
> A Windows Server 2019-ben a Robocopy jelenleg olyan problémát tapasztal, amely miatt a célkiszolgáló Azure File Sync által rétegzett fájljainak hatókörét a rendszer a forrásból újra fel fogja tölteni, majd újra feltölti az Azure-ba a robocopy /COPY függvényének használata során. Rendkívül fontos, hogy a Robocopyt a 2019-től más Windows Serveren használja. Az előnyben részesített lehetőség a Windows Server 2016. Ez a megjegyzés akkor lesz frissítve, ha a probléma a következőn keresztül Windows Update.

> [!WARNING]
> Nem *szabad elindítani a* RoboCopyt, mielőtt a kiszolgáló teljesen letölti egy Azure-fájlmegosztás névterét. További információ: Annak meghatározása, hogy a névtér mikor lett teljesen [letöltve a kiszolgálóra.](#determine-when-your-namespace-has-fully-synced-to-your-server)

 Csak olyan fájlokat szeretne másolni, amelyek az áttelepítési feladat utolsó futtatása után módosultak, és olyan fájlokat, amelyek korábban még nem helyezték át ezeket a feladatokat. A probléma megoldásához meg kell oldania, hogy a migrálás befejezése után miért nem voltak később áthelyezve a kiszolgálón. További információ: Azure File Sync [hibaelhárítása.](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

A RoboCopy számos paraméterrel rendelkezik. Az alábbi példa egy befejezett parancsot és a paraméterek kiválasztásának okait mutatja be.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Háttér:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi a RoboCopy többszálas futtatását. Az alapértelmezett érték 8, a maximális érték pedig 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      A NAPLÓFÁJLba UNICODE-ként írja ki az állapotot (felülírja a meglévő naplót).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Kimenetek a konzolablakban. Egy naplófájl kimenetével együtt használva.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Kihagyja az előrehaladás naplózását, hogy a napló olvasható maradjon.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Ugyanabban a módban futtatja a RoboCopyt, mint a biztonsági mentési alkalmazás. Lehetővé teszi a RoboCopy számára olyan fájlok áthelyezését, amelyekhez az aktuális felhasználó nem rendelkezik engedélyekkel.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /FOG
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy csak a forrás (StorSimple-berendezés) és a cél (Windows Server-címtár) közötti változásokat vegye figyelembe.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Bizonyos tükrözött forgatókönyvekben megőrzi a hűséget.</br>Példa: Két Robocopy futtatása között egy fájl ACL-változást és attribútumfrissítést tapasztal, például rejtettként van *megjelölve.* /IT nélkül a Robocopy kihagyhatja az ACL-változást, így nem továbbíthatja a célhelyre.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      A fájlmásolás részletessége (alapértelmezés szerint /COPY:DAT), másolásjelzők: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ACL, O=Owner information, U=aUditing information.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      MÁSOLJA AZ ÖSSZES fájl információit (egyenértékű a /COPY:DATSOU értékekkel).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      A könyvtárak másolásának fidelitása (alapértelmezés szerint /DCOPY:DA), másolásjelölők: D=Data, A=Attributes, T=Timestamps.
   :::column-end:::
:::row-end:::

A RoboCopy-parancs forrás- és célhelyének konfigurálásakor ellenőrizze, hogy a forrás és a cél struktúrája egyezik-e. Ha az áttelepítési feladat könyvtárleképezési funkcióját használta, a gyökérkönyvtár szerkezete különbözhet a StorSimple-kötet struktúrájától. Ebben az esetben előfordulhat, hogy több RoboCopy-feladatra lesz szüksége, mindegyik alkönyvtárhoz egyet. Ha nem biztos abban, hogy a parancs a várt módon fog-e teljesíteni, használhatja az */L* paramétert, amely a parancsot szimulálja anélkül, hogy ténylegesen módosításokat hajt végre.

Ez a RoboCopy-parancs a /REL-t használja, így nem fogja áthelyezni az azonos fájlokat (például rétegzett fájlokat). Ha azonban a forrás és a cél elérési útja helytelen, a /ABBAN az esetben is kiüríti a Könyvtárstruktúrákat a Windows Server-példányon vagy az Azure-fájlmegosztáson, amelyek nem találhatóak meg a StorSimple-forrásútvonalon. Pontosan meg kell egyezniük a RoboCopy-feladat céllal, hogy a migrált tartalmat a migrálás során végrehajtott legújabb módosításokkal frissítve legyen.

Tekintse át a RoboCopy naplófájlját, és nézze meg, hogy nem maradtak-e fájlok. Ha problémák vannak, javítsa ki őket, majd futtassa újra a RoboCopy-parancsot. A fontos fájlok vagy mappák megoldatlan problémáinak megoldása előtt ne megszüntetje a StorSimple-erőforrásokat.

Ha nem használja a Azure File Sync a szóban forgó Azure-fájlmegosztás gyorsítótárazára, hanem a közvetlen megosztás-hozzáférést választotta:

1. [Csatlakoztassa az Azure-fájlmegosztást](storage-how-to-use-files-windows.md#mount-the-azure-file-share) hálózati meghajtóként egy helyi Windows-géphez.
1. Hajtsa végre a RoboCopyt a StorSimple és a csatlakoztatott Azure-fájlmegosztás között. Ha a fájlok nem másolnak, javítsa ki a nevüket a StorSimple oldalon az érvénytelen karakterek eltávolításához. Ezután próbálja újra a RoboCopyt. A korábban felsorolt RoboCopy-parancs többször is futtatható anélkül, hogy feleslegesen idézné elő a StorSimple-t.

### <a name="user-cut-over"></a>Felhasználó-átvágás

Ha a Azure File Sync használja, valószínűleg létre kell hoznia az SMB-megosztásokat az Azure File Sync-kompatibilis Windows Server-példányon, amelyek megegyeznek a StorSimple-köteten található megosztásokkal. Ezt a lépést előre betöltheti, és korábban is meg is használhatja, hogy itt ne veszítsen időt. Azonban meg kell győződnie arról, hogy a pont előtt senki sem férhet hozzá a Windows Server-példány módosításaihoz.

Ha DFS-N üzemelő példánytal rendelkezik, a DFN-Namespaces az új kiszolgálómappába mutathat. Ha nincs DFS-N üzemelő példánya, és a 8100-as vagy 8600-as berendezést helyileg egy Windows Server-példányon tárolta, akkor a kiszolgálót kiveheti a tartományból. Ezután csatlakozzon tartományhoz az új Azure File Sync Windows Server-példányhoz. A folyamat során a kiszolgálónak ugyanazt a kiszolgálónevet és megosztási nevet adja meg, mint a régi kiszolgálónak, hogy az átvágás átlátható maradjon a felhasználók, a csoportházirend és a parancsfájlok számára.

További információ a [DFS-N-ről.](/windows-server/storage/dfs-namespaces/dfs-overview)

## <a name="deprovision"></a>Megszüntetés

Amikor megszüntet egy erőforrást, elveszíti a hozzáférését az erőforrás és az adatok konfigurációja között. Megszüntetés nem vonható vissza. Csak akkor folytassa, ha már megerősítette a következőt:

* A migrálás befejeződött.
* Nincsenek függőségek a megszüntetni kívánt StorSimple-fájlokon, -mappákon vagy kötetek biztonsági másolatán.

Mielőtt hozzákezd, ajánlott megfigyelni az új Azure File Sync éles környezetben való üzembe helyezését. Ez az idő lehetőséget ad az esetlegesen felmerülő problémák megoldására. Miután legalább néhány napja megfigyelte a Azure File Sync üzembe helyezését, megkezdheti az erőforrások megszüntetését ebben a sorrendben:

1. Megszüntetni a StorSimple Data Manager erőforrást a Azure Portal. Ezzel az összes DTS-feladat törlődik. A másolási naplókat nem fogja tudni egyszerűen lekérni. Ha fontosak a rekordok számára, a megszüntetés előtt olvassa be őket.
1. Győződjön meg arról, hogy a StorSimple fizikai berendezések migrálva vannak, majd azok regisztrációját meg kell újulni. Ha nem biztos abban, hogy migrálták őket, ne folytassa. Ha megszünteti ezeket az erőforrásokat, amíg még szükség van rá, nem tudja helyreállítani az adatokat vagy azok konfigurációját.<br>Szükség esetén először megszüntetheti a StorSimple-kötet erőforrását, amely megtisztítja a berendezésen az adatokat. Ez több napot is igénybe **vehet,** és nem állítja ki kriminalisztikailag a berendezésen az adatokat. Ha ez fontos Önnek, kezelje a lemez nullázását az erőforrás-megszüntetéstől és a szabályzatok szerint.
1. Ha már nem maradt több regisztrált eszköz a StorSimple-Eszközkezelő, akkor eltávolíthatja az adott Eszközkezelő erőforrást.
1. Ideje törölni a StorSimple-tárfiókot az Azure-ban. A folytatás előtt állítsa le és ellenőrizze, hogy a migrálás befejeződött-e, és hogy ezektől az adatoktól semmi és senki sem függ.
1. Válassza le a StorSimple fizikai berendezést az adatközpontból.
1. Ha Ön a StorSimple-berendezés a tulajdonában van, akkor ingyenesen újrahasznosítható a számítógépen. Ha az eszköz bérletben van, tájékoztassa a bérlőt, és szükség szerint adja vissza az eszközt.

A migrálás befejeződött.

> [!NOTE]
> Továbbra is kérdései vannak vagy problémákba ütközött?</br>
> Azért vagyunk itt, hogy segítsünk AzureFilesMigration@microsoft.com a(nak)

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a [Azure File Sync: aka.ms/AFS.](./storage-sync-files-planning.md)
* A felhőbeli rétegezés [szabályzatának rugalmassága.](storage-sync-cloud-tiering-overview.md)
* [Engedélyezze Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) azure-fájlmegosztáson a pillanatképek ütemezéséhez és a biztonsági másolatok megőrzési ütemezésének meghatározásához.
* Ha azt látja a Azure Portal, hogy egyes fájlok véglegesen [](storage-sync-files-troubleshoot.md) nem szinkronizálódnak, tekintse át a hibaelhárítási útmutatót a problémák megoldásához.
