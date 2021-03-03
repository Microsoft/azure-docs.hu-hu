---
title: Microsoft Azure Data Box Disk GYIK | Microsoft Docs in data
description: Gyakori kérdéseket és válaszokat tartalmaz a Azure Data Box Disk, egy felhőalapú megoldásra, amely lehetővé teszi nagy mennyiségű adatok átvitelét az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: 7212fc4113c1de0a7aee4c6c02e8fa65f9828680
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724829"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: gyakori kérdések

A Microsoft Azure Data Box Disk felhőalapú megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi adatot az Azure-ba. A GYIK a Data Box Disk-lemezek Azure Portalon történő használatával kapcsolatban esetlegesen felmerülő kérdésekre ad választ. 

A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Tudnivalók a szolgáltatásról
- Konfigurálás és csatlakoztatás 
- Állapot nyomon követése
- Adatok áttelepítése 
- Adatok ellenőrzése és feltöltése 


## <a name="about-the-service"></a>Tudnivalók a szolgáltatásról

### <a name="q-what-is-azure-data-box-service"></a>K. Mi az az Azure Data Box szolgáltatás? 
A.  Az Azure Data Box szolgáltatást offline adatbetöltéshez hoztuk létre. A szolgáltatás termékek egész tárházát kezeli, amelyek mindegyike különböző kapacitású tárolókra történő adatátvitelhez lett igazítva. 

### <a name="q-what-are-azure-data-box-disks"></a>K. Mik azok az Azure Data Box-lemezek?
A. Az Azure Data Box-lemezek több terabájtnyi adat gyors, költséghatékony és biztonságos adatátvitelét teszik lehetővé az Azure-ba, illetve az Azure-ból. A Microsoft elküld Önnek 1–5 lemezt, legfeljebb 35 TB-os tárkapacitással. Az Azure Portalon a Data Box szolgáltatás segítségével könnyedén konfigurálhatja, csatlakoztathatja és feloldhatja ezeket a lemezeket.  

A lemezek a Microsoft BitLocker meghajtótitkosítással vannak titkosítva, amelynek titkosítási kulcsait az Azure Portalon kezelheti. Ezután másolja az adatait az ügyfél kiszolgálóiról. Az adatközpontban a Microsoft egy gyors és privát hálózati feltöltési kapcsolaton keresztül a lemezről a felhőbe telepíti az adatait, majd feltölti őket az Azure-ba.

### <a name="q-when-should-i-use-data-box-disks"></a>K. Mikor érdemes Data Box Disk-lemezeket használnom?
A. Ha 40 TB-nyi (vagy kevesebb) adata van, amelyet szeretne átvinni az Azure-ba, akkor hasznos lehet a Data Box Disk-lemezek használata.

### <a name="q-what-is-the-price-of-data-box-disks"></a>K. Mennyibe kerülnek a Data Box Disk-lemezek?
A. Data Box lemezek árával kapcsolatos információkért lépjen a [díjszabás oldalra](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>K. Hogyan kaphatok Data Box Disk-lemezeket? 
A.  Azure Data Box lemezek beszerzéséhez jelentkezzen be Azure Portal és hozzon létre egy Data Box-sorrendet a lemezekhez. Adja meg a kapcsolattartási és értesítési adatait. A megrendelés elküldése után a rendelkezésre állás függvényében 10 napon belül szállítjuk Önnek a lemezeket.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>K. Egyszerre legfeljebb mennyi adatot vihetek át a Data Box Disks-lemezekkel?
A. Öt lemez esetén 8 TB-os kapacitással (7 TB felhasználható kapacitással) a maximálisan felhasználható kapacitás 35 TB. Így 35 TB-nyi adat vihető át egyetlen példányban. Több adat átviteléhez több lemez megrendelése szükséges.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>K. Honnan tudhatom meg, hogy a Data Box Disk-lemezek elérhetők-e a régiómban? 
A.  Ha szeretné megtekinteni, hogy jelenleg milyen Data Box lemezek érhetők el, lépjen a [régió rendelkezésre állása területre](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>K. Mely régiókban tárolhatok adatokat a Data Box Disk-lemezekkel?
A. A Data Box Disk az Egyesült Államok, Kanada, Ausztrália, Nyugat-Európa és Észak-Európa, Korea és Japán régiókban is támogatott. Csak a nyilvános Azure-felhőrégiók támogatottak. Az Azure Government és más független felhők nem támogatottak.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>K. Hogyan importálhatók a saját helyükre tartozó forrásadatok egy adott országban vagy régióban egy másik országban található Azure-régióba?
A. Data Box Disk támogatja az adatfeldolgozást kizárólag a rendeltetési országon vagy régióban belül, és nem keresztez nemzetközi határokat. Az egyetlen kivétel az Európai Unió (EU) megrendeléseire vonatkozik, ahol a Data Box-lemezek bármelyik EU-országba/régióba oda tudnak szállítani.

Ha például az Egyesült államokbeli USA-beli Azure-beli Storage-fiókba kívánja helyezni az adatait, az alábbi módon érheti el:

#### <a name="option-1"></a>1. módszer: 

Az [Azure import/export szolgáltatásból](../import-export/storage-import-export-service.md) származó adatokkal rendelkező [támogatott lemez](../import-export/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks) szállítása a kanadai forrás helyéről az Azure West US datacenterba.

#### <a name="option-2"></a>2. lehetőség:

1. Megrendelési Data Box Disk Kanadában egy Storage-fiók kiválasztásával Kanada középső régiójában. Az SSD-lemezeket az Azure-adatközpontból, Közép-Kanadában, a rendelés létrehozása során megadott szállítási címtől (Kanadában) szállítjuk.

2. Miután a rendszer átmásolta a helyszíni kiszolgáló adatait a lemezekre, a Microsoft által biztosított visszatérési címkék használatával visszaküldheti azokat a kanadai Azure-adatközpontba. A Data Box Disk (ok) ban található, a rendelés létrehozásakor kiválasztott Kanada Azure-régiójában lévő, a cél Storage-fiókba való feltöltésre kerül.

3. Ezután a AzCopy hasonló eszközzel másolhatók az adattárak az USA nyugati régiójában lévő Storage-fiókba. Ez a lépés a Data Box Disk számlázásban nem szereplő [szabványos tárolási](https://azure.microsoft.com/pricing/details/storage/) és [sávszélesség-díjakat](https://azure.microsoft.com/pricing/details/bandwidth/) tartalmazza.

#### <a name="q-does-data-box-disk-store-any-customer-data-outside-of-the-service-region"></a>K. A Data Box Disk a szolgáltatási régión kívül tárolja az ügyféladatokat?

A. Nem. Data Box Disk a szolgáltatási régión kívülről nem tárol ügyféladatokat. Az ügyfél teljes mértékben birtokolja az adatait, és a rendelés létrehozása során kiválasztott Storage-fiók alapján mentheti az adott helyre.  

A vásárlói adatok mellett Data Box Disk adatokat is tartalmaz, amelyek metaadatokat és figyelési naplókat tartalmaznak. Az összes régióban (kivéve Dél-és Délkelet-Ázsiában) a rendszer az adatvesztés elleni védelem [érdekében egy,](../best-practices-availability-paired-regions.md) a Geo-redundáns tárolási fiókon keresztül tárolja és replikálja Data Box Disk az adattárolást.  

A Dél-és Délkelet-Ázsiában található [adattárolási követelmények](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) miatt Data Box Disk az adat tárolása egy zóna redundáns tárolási (ZRS) fiókban történik, hogy az egyetlen régióban legyen tárolva. Délkelet-Ázsiában az összes Data Box Disk-szolgáltatás a Szingapúrban és Dél-Brazíliában található meg, az adattárolók Brazíliában vannak tárolva. 

Ha a Dél-Brazíliában és Délkelet-Ázsiában található szolgáltatások meghibásodása van, az ügyfelek új rendeléseket hozhatnak létre egy másik régióból. Az új megrendelések a létrehozott régióból lesznek kézbesítve, és az ügyfelek felelősek a Data Box Disk és oda történő szállításért.



### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>K. Hogyan állíthatom helyre az adatokat, ha egy teljes régió meghibásodik?

A. Szélsőséges körülmények között, amikor egy régió elvesztése jelentős katasztrófa miatt megszakad, a Microsoft regionális feladatátvételt kezdeményezhet. Ebben az esetben nincs szükség beavatkozásra a részen. A megrendelés a feladatátvételi régión keresztül lesz teljesítve, ha ugyanazon az országon vagy kereskedelmi határon belül van. Egyes Azure-régiók azonban nem rendelkeznek ugyanahhoz a földrajzi vagy kereskedelmi határhoz tartozó párosított régióval. Ha bármelyik régióban van katasztrófa, létre kell hoznia a Data Box sorrendet egy másik elérhető régióból, és át kell másolnia az Azure-ba az új régióban. További információ: [Üzletmenet-folytonosság és vészhelyreállítás (BCDR): Az Azure párosított régiói](../best-practices-availability-paired-regions.md).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>K. Kihez fordulhatok, ha probléma merülne fel a Data Box Disk-lemezekkel kapcsolatban?
A. Ha Data Box lemezzel kapcsolatos problémákat tapasztal, forduljon a [Microsoft ügyfélszolgálatahoz](./data-box-disk-contact-microsoft-support.md).

## <a name="order-device"></a>Eszköz rendelése

### <a name="q-how-do-i-get-data-box-disk"></a>K. Hogyan lekérni Data Box Disk? 
A.  Azure Data Box Disk beszerzéséhez jelentkezzen be a Azure Portalba, és hozzon létre egy Data Box Disk rendelést. Adja meg a kapcsolattartási és értesítési adatait. Ha megrendelést helyezett a rendelkezésre állás alapján, a rendszer 10 napon belül elküldi a Data Box Disk. További információ [a Data Box megrendeléséről](data-box-disk-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>K. Nem sikerült Data Box Disk sorrendet létrehozni a Azure Portalban. Miért?
A. Ha nem hoz létre Data Box Disk sorrendet, az előfizetési típussal vagy a hozzáféréssel kapcsolatos probléma merült fel.

Ellenőrizze az előfizetését. A Data Box Disk csak Nagyvállalati Szerződés (EA) és a Cloud Solution Provider (CSP) előfizetési ajánlatok esetében érhető el. Ha nem rendelkezik ezekkel az előfizetési típusokkal, lépjen kapcsolatba Microsoft ügyfélszolgálata az előfizetés frissítéséhez.

Ha egy támogatott típusú előfizetéssel rendelkezik, ellenőrizze a hozzáférési szintjét. Megrendelés létrehozására csak az előfizetések közreműködői vagy tulajdonosai jogosultak.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>K. Mennyi időt vesz igénybe a rendelés létrehozása az Azure-ba feltöltött adatok alapján?

A. A megrendelés feldolgozásának minden fázisában a következő becsült bevezetési idő alapján érdemes elvárnia, mire számíthat.  

Ezek az átvezető időpontok a *becslések*. Az adatközpont, az egyidejű megrendelések és az egyéb környezeti feltételek betöltése hatással van a megrendelés feldolgozásának minden szakaszának időpontjára.

**Data Box Disk rendelés becsült átvezetési ideje:**

1. Megrendelés Data Box Disk: néhány perc a portálról
2. Lemez kiosztása és előkészítése: legfeljebb 5 munkanap, a leltár rendelkezésre állása és a feldolgozandó függőben lévő megrendelések száma alapján
3. Szállítás: 2–3 munkanap
4. Adatmásolás az ügyfél webhelyén: az adatok természetétől, méretétől és a fájlok számától függ.
5. Visszaküldés: 2–3 munkanap
6. Az adatközpontban történő feldolgozás és az Azure-ba való feltöltés: az adatok feltöltése az adatközpontban kezdődik, amint a működés közbeni feldolgozás befejeződött, és a lemez csatlakoztatva van. A feltöltési idő az adatok természetétől, a mérettől és a fájlok számától függ.

## <a name="configure-and-connect"></a>Konfigurálás és csatlakoztatás
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>K. Megadhatom a megrendelésben a Data Box Disk-lemezek számát?
A.  Nem. Az adatmérettől és a lemezek rendelkezésre állásának függvényében 8 TB-os lemezeket (legfeljebb öt lemezt) érhet el.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>K. Hogyan oldhatom fel a Data Box Disk-lemezeket? 
A.  Nyissa meg az Azure Portalon a Data Box Disk-megrendelését, majd az **Eszköz részletei** lehetőséget. Másol le a jelszót. Töltse le az Azure Portalról az operációs rendszerének megfelelő Data Box Disk lemezzárolás-feloldó eszközt. Futtassa azon a számítógépen, amelyről az adatokat a lemezekre szeretné másolni. Adja meg a jelszót a lemezek feloldásához. Ugyanaz a jelszó oldja fel az összes lemezt. 

A részletes útmutatót a [Lemez feloldása Windows-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) vagy a [Lemez feloldása Linux-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) című szakaszban talál.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>K. Használhatok linuxos gazdaszámítógépet a Data Box Disk-lemezek csatlakoztatásához és az adatok másolásához?
A.  Igen. Linux- és a Windows-ügyfelek is használhatók a Data Box Disk-lemezek csatlakoztatásához és az adatok másolásához. További információkért tekintse meg a gazdaszámítógépre vonatkozó [Támogatott operációs rendszerek](data-box-disk-system-requirements.md) listáját.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>K. Már feladták a lemezeket, de szeretném lemondani a megrendelést. Miért nincs Mégse gomb?
A.  A megrendelést csak a lemezek megrendelése után, de még a szállítás megkezdése előtt mondhatja le. Ha feladtuk a lemezeket, már nem mondhatja le a megrendelést. A lemezeket azonban ingyenesen visszaállíthatja. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>K. Csatlakoztathatok adatátvitel céljából több Data Box Disk-lemezt is ugyanahhoz a gazdaszámítógéphez?
A. Igen. Ugyanahhoz a gazdaszámítógéphez egyszerre több Data Box Disk-lemez is csatlakoztatható, és párhuzamosan több másolási feladat is futtatható.

## <a name="track-status"></a>Állapot nyomon követése

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>K. Hogyan tudom nyomon követni a lemezeket a megrendelésem feladásától kezdve egészen azok visszaküldéséig? 
A.  A Data Box Disk-lemez megrendelésének állapotát az Azure Portalon követheti nyomon. A megrendelés létrehozásakor a rendszer egy értesítési e-mail-cím megadását is kéri. Ha megadta az egyiket, akkor e-mailben értesítjük a rendelés összes állapotának változásáról. További információ az [Értesítési e-mailek beállítása](data-box-portal-ui-admin.md#edit-notification-details) témában.

### <a name="q-how-do-i-return-the-disks"></a>K. Hogyan küldhetem vissza a lemezeket? 
A.  A Microsoft a Data Box Disk-lemezek csomagjában egy levélcímkét is elküld Önnek. Ragassza ezt a címkét egy szállítódobozra, majd adja le a lezárt dobozt a szállítmányozójánál. Ha a címke megsérült vagy elveszett, nyissa meg az **Áttekintés > Levélcímke letöltése** menüpontot, és töltsön le egy új levélcímkét.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Felvehetem magam is a Data Box Disk rendelést? Visszaadhatom a lemezeket egy kiválasztott szolgáltatón keresztül?
A. Igen. A Microsoft csak US Gov régióban biztosít önfelügyelt szállítást is. A Data Box Disk rendelés elhelyezésekor kiválaszthatja az önfelügyelt szállítási lehetőséget is. A Data Box Disk sorrend kiválasztásához hajtsa végre a következő lépéseket:
    
1. A rendelés elhelyezése után a rendszer feldolgozza a sorrendet, és előkészíti a lemezeket. E-mailben értesítést küldünk arról, hogy a megrendelés készen áll a felvételre. 
2. Ha a rendelés készen áll a beléptetésre, lépjen a megrendelésre a Azure Portal, és navigáljon az **Áttekintés** panelre. 
3. Ekkor megjelenik egy értesítés, amely tartalmazza a kódot a Azure Portalban. Küldjön e-mailt a [Azure Data Box operatív csapatnak](mailto:adbops@microsoft.com) , és adja meg nekik a kódot. A csapat megadja a felvételi dátum és idő helyét és ütemtervét. Az e-mailes értesítés kézhezvételét követően 5 munkanapon belül meg kell hívnia a csapatot.

Miután az Adatmásolás és az érvényesítés befejeződött, hajtsa végre a következő lépéseket a lemez visszaküldéséhez:

1. Miután az adatok érvényesítése befejeződött, válassza le a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és a csatlakoztatott kábeleket egy buborék-körbefuttatással, és helyezze azokat a szállítás mezőbe. Ha a tartozékok hiányoznak, a szolgáltató költségeket számíthat fel.

    - Használja fel újra az első szállításkor kapott csomagolást. Ajánlott megfelelően rögzített buborékfóliát használni a lemezek becsomagolásához.
    - Gondoskodjon róla, hogy a lemez szorosan illeszkedjen a dobozba.
3. A megrendelés **Áttekintés** paneljén Azure Portal. Egy kóddal rendelkező értesítést kell látnia.
4. Használja ezt a kódot, és küldje el e-mailben a [Azure Data Box Operations csapatnak](mailto:adbops@microsoft.com) , és adja meg nekik a kódot. A felhasználók a lemezek ledobásának helyét és időpontját is megadják.


## <a name="migrate-data"></a>Adatok áttelepítése

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>K. Legfeljebb mekkora méretű adat esetén használhatók a Data Box Disk-lemezek?  
A.  A Data Box Disk megoldással legfeljebb 5, összesen 35 TB-os felhasználható kapacitással rendelkező lemez használható. Maguk a lemezek 8 TB-osak (amelyből 7 használható fel).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>K. Mekkora a Data Box Disk-lemezeken támogatott legnagyobb blokkblob és lapblob mérete? 
A.  A legnagyobb méreteket az Azure Storage tárhelykorlátai határozzák meg. A legnagyobb blokkblob mérete körülbelül 4,768 TiB, a legnagyobb lapblob mérete pedig 8 TiB. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](../storage/blobs/scalability-targets.md)ismertető témakört.

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>K. Milyen adatátviteli sebességgel rendelkeznek a Data Box Disk-lemezek?
A. A tesztjeink során USB 3.0-ás kapcsolat esetén a lemezeknél 430 MB/s-os adatátviteli sebességet is mértünk. A tényleges szám a használt fájl méretének függvényében eltérő lehet. Kisebb fájlok esetén gyengébb teljesítmény figyelhető meg.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>K. Honnan tudhatom, hogy az átvitel során az adataim biztonságban vannak? 
A.  A Data Box Disk-lemezek 128 bites BitLocker AES-titkosítással vannak titkosítva, és a titkosítás jelszava csak az Azure Portalon érhető el. A jelszó lekéréséhez jelentkezzen be az Azure Portalra a fiókja hitelesítő adataival. Ezt a jelszót a Data Box lemezzárolás-feloldó eszköz használata során adhatja meg.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>K. Hogyan tudom az adataimat a Data Box Disk-lemezekre másolni? 
A.  Az adatlemezekre másoláshoz használjon SMB-másolási eszközt, például, `Robocopy` `Diskboss` vagy akár a Windows fájlkezelő húzását.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>K. Vannak tippek az adatok másolásának felgyorsítására?
A.  A másolási folyamat felgyorsításához:

- Használjon több másolási adatfolyamot. Például a `Robocopy` esetében használja a többszálú kapcsolót. A ténylegesen használt parancsokkal kapcsolatban további információkat a következő oktatóanyagban talál: [Oktatóanyag: Adatok másolása az Azure Data Box Disk-lemezre, és az adatok ellenőrzése](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Használjon több munkamenetet.
- Például a hálózati megosztásból történő másolás helyett (ahol a hálózat sebessége korlátokat szabhat) gondoskodjon róla, hogy az adatok azon a helyi számítógépen legyenek, amelyre a lemezeket csatlakoztatja.
- Győződjön meg arról, hogy az USB 3,0-es vagy újabb verzióját használja a másolási folyamat során. Töltse le és használja az [ `USBView` eszközt](/windows-hardware/drivers/debugger/usbview) a számítógéphez csatlakoztatott USB-vezérlők és USB-eszközök azonosításához.
- Mérje meg az adatok másolására használt számítógép teljesítményét. Töltse le és használja az [ `Bluestop` `FIO` eszközt](https://ci.appveyor.com/project/axboe/fio) a kiszolgálói hardver teljesítményének összehasonlításához. Válassza ki a legújabb x86-vagy x64-buildet, válassza a **összetevők fület** , és töltse le az MSI-t.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>K. Hogyan lehet felgyorsítani az adatok másolását, ha a forrásadatok között sok kisméretű fájl található (KB-os vagy néhány MB-os méretűek)?
A.  A másolási folyamat felgyorsításához:

- Hozzon létre egy helyi VHDx-et egy gyors tárolón, vagy hozzon létre egy üres VHD-t a HDD-n vagy az SSD-n (lassabb).
- Csatlakoztassa egy virtuális géphez.
- Másolja a fájlokat a virtuális gép lemezére.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>K. Használhatok több tárfiókot is a Data Box Disk-lemezekkel?
A.  Nem. A Data Box Disk-lemezekkel jelenleg csak egyetlen általános célú vagy hagyományos tárfiók használata támogatott. A gyakori és a ritka elérésű blob is támogatott. Jelenleg csak az USA-beli, Nyugat-európai és észak-európai Azure-beli Storage-fiókok támogatottak.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>K. Milyen eszközkészlet érhető el az adataim Data Box lemezzel?
A. A Data Box Disk elérhető eszközkészlet három eszközt tartalmaz:
 - **Data Box Disk-feloldási eszköz**: ezzel az eszközzel oldhatja fel a Microsoft által szállított titkosított lemezeket. Ha a lemezeket az eszközzel oldja fel, meg kell adnia a Azure Portal Data Box Disk sorrendjében elérhető hozzáférési kulcsot. 
 - **Data Box Disk érvényesítési eszköz**: ezzel az eszközzel ellenőrizheti a méretet, a formátumot és a blob nevét az Azure elnevezési konvenciók szerint. Emellett ellenőrzőösszegeket is generál a másolt adatelemekhez, amelyeket azután az Azure-ba feltöltött adatellenőrzéshez használ a rendszer.
 - **Data Box Disk megosztott másolási eszköz**: akkor használja ezt az eszközt, ha több lemezt használ, és olyan nagy adatkészlettel rendelkezik, amelyet az összes lemezre el kell osztani és át kell másolni. Ez az eszköz jelenleg Windows rendszeren érhető el. Ez az eszköz felügyelt lemezek esetén nem támogatott. Ez az eszköz érvényesíti az Adatmásolást, így az eszköz használatakor kihagyhatja az ellenőrzési lépést.

Az eszközkészlet a Windows és a Linux rendszerhez egyaránt elérhető. Az eszközkészletet innen töltheti le:
- [Data Box Disk eszközkészlet letöltése Windowsra](https://aka.ms/databoxdisktoolswin) 
- [Data Box Disk eszközkészlet letöltése Linuxra](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>K. Használhatom a Data Box Disk az adatok Azure Filesba való átvitelére, majd az adatok Azure File Sync használatával történő felhasználására? 
A. A Azure Files Data Box Disk, de nem fognak jól működni Azure File Sync. A metaadatokat a rendszer nem őrzi meg, ha a fájlra vonatkozó adatokat Azure File Sync használja.


## <a name="verify-and-upload"></a>Ellenőrzés és feltöltés

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>K. Milyen gyorsan férhetnek hozzá az Azure-beli adataim a lemezek visszaszállítása után? 
A.  Amint az Adatmásolás megrendelési állapota befejeződöttként jelenik meg, azonnal hozzáférhet az adataihoz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>K. A feltöltést követően hol lesznek az adataim az Azure-ban?
A.  Ha a lemezen a *BlockBlob* és a *PageBlob* mappákba másolta az adatait, akkor a *BlockBlob* és a *PageBlob* mappákban található almappáknak létrejön az Azure-tárfiókban egy-egy tároló. Ha közvetlenül a *BlockBlob* és a *PageBlob* mappákba másolta a fájlokat, akkor a fájlok az Azure Storage-fiók alatt *$root* alapértelmezett tárolóban találhatók. Amikor az *AzureFile* mappában található mappába másol egy mappát, létrejön egy fájlmegosztás.

### <a name="q-i-just-noticed-that-i-didnt-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>K. Észrevettem, hogy nem követtem a saját tárolók Azure elnevezési követelményeit. Így is fel lehet tölteni az adataimat az Azure-ba?
A. A rendszer a tároló neveiben lévő nagybetűket automatikusan kisbetűvé alakítja. Ha a nevek más módokon nem megfelelőek – például speciális karaktereket vagy más nyelveket tartalmaznak – a feltöltés sikertelen lesz. További információt az [Azure elnevezési konvencióit](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) ismertető szakaszban talál.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>K. Hogyan tudom ellenőrizni a több Data Box Disk-lemezre feltöltött adataimat?
A.  Az adatok másolásának befejezése után futtassa az `DataBoxDiskValidation.cmd` fájlt a *DataBoxDiskImport* mappából, hogy létrehozzon az érvényesítéshez egy ellenőrzőösszeget. Ha több lemeze is van, akkor minden egyes lemezen meg kell nyitnia a parancssori ablakot, és le kell futtatnia ezt a parancsot. Vegye figyelembe, hogy ez a művelet az adatok méretétől függően akár hosszú ideig (órákig) is eltarthat.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>K. Mi történik az adataimmal, miután visszaküldtem a lemezeket?
A.  Ha befejeződött az adatok Azure-ba történő másolása, a rendszer a lemezen található adatokat a NIST SP 800-88 Revision 1 útmutatásainak megfelelően biztonságosan törli.  

### <a name="q-how-is-my-data-protected-during-transit"></a>K. Milyen védelem alatt állnak az adataim a szállítás közben? 
A.  A Data Box lemezek titkosítása AES-128 Microsoft BitLocker-titkosítással történik, és egyetlen hitelesítő kulcs szükséges az összes lemez zárolásának feloldásához és az adateléréshez.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>K. Le kell futtatnom ismét az ellenőrzőösszeg-érvényesítést, ha további adatokat másolok a Data Box Disk-lemezekre?
A. Igen. Ha úgy dönt, hogy érvényesíti az adatait (ezt mi is javasoljuk), akkor ha további adatokat másol a lemezre, újra le kell futtatnia az érvényesítést.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>K. Az összes lemezt felhasználtam az adataim átviteléhez, és szükségem van további lemezek megrendelésére. Fel lehet adni gyorsan a megrendelést?
A. Lehetősége van klónozni az előző megrendelését. A klónozással létrehozhat egy, az előzővel azonos megrendelést, amelynek szerkesztheti a részleteit, anélkül, hogy újra meg kellene adnia a címet vagy az értesítési adatokat.

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>K. Átmásoltam az adatfájlokat a ManagedDisk mappába. Nem látok felügyelt lemezeket a felügyelt lemezekhez megadott erőforráscsoporthoz. Az adataim az Azure-ba lettek feltöltve? Hol találom meg?
A. Igen. Az adatai az Azure-ba lettek feltöltve, de ha nem lát felügyelt lemezt a megadott erőforráscsoporthoz, akkor valószínű, hogy az adatai érvénytelenek. Ha az oldal Blobok, a Blobok, a Azure Files vagy a felügyelt lemezek nem érvényesek, a következő mappákba kerülnek:
 - Az oldal Blobok egy blokk blob-tárolóba kerülnek, amely a *databoxdisk-érvénytelen-PB-* vel kezdődik.
 - A Azure Files egy blokk BLOB-tárolót nyit meg, amely a következővel kezdődik: *databoxdisk-érvénytelen-AF-*.
 - A Managed Disks egy blokk blob-tárolóba kerül, amely a *databoxdisk-érvénytelen-MD-*-vel kezdődik.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Data Box Disk rendszerkövetelményeit](data-box-disk-system-requirements.md).
- A [Data Box Disk korlátainak](data-box-disk-limits.md) megismerése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.