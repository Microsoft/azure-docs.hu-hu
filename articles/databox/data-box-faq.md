---
title: Microsoft Azure Data Box – gyakori kérdések | Microsoft Docs
description: A gyakori kérdéseket és válaszokat tartalmaz a Azure Data Boxre, egy felhőalapú megoldásra, amely lehetővé teszi nagy mennyiségű adatok átvitelét az Azure-ba.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/25/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: a692aeba312b6fcad580eac901f4b7bc65f059fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730575"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: gyakori kérdések

A Microsoft Azure Data Box hibrid megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi adatot az Azure-ba egy átviteli eszköz segítségével. A gyakori kérdések a Data Box Azure Portalon történő használatával kapcsolatban esetlegesen felmerülő kérdésekre adnak választ.

A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Tudnivalók a szolgáltatásról
- Eszköz rendelése
- Konfigurálás és csatlakoztatás 
- Állapot nyomon követése
- Adatok másolása 
- Eszköz kiszállítása
- Adatok ellenőrzése és feltöltése 
- Felügyeleti lánc támogatása

## <a name="about-the-service"></a>Tudnivalók a szolgáltatásról

### <a name="q-what-is-azure-data-box-service"></a>K. Mi az az Azure Data Box szolgáltatás? 
A.  Az Azure Data Box szolgáltatást offline adatbetöltéshez hoztuk létre. A szolgáltatás különböző tárolókapacitású termékek egész tárházát kezeli, amelyek mindegyike adatátvitelhez lett igazítva. 

### <a name="q-what-is-azure-data-box"></a>K. Mi az az Azure Data Box?
A. A Azure Data Box az Azure-ba való gyors, költséges és biztonságos adatátvitelt tesz lehetővé. A Data Box eszköz az Azure Portalon keresztül rendelhető meg. A Microsoft a 80 – TB felhasználható kapacitású tárolóeszközt a regionális szolgáltatón keresztül üzemelteti. 

Miután az eszköz megérkezett, gyorsan beállítható a helyi webes felhasználói felülettel. Másolja az adatait a kiszolgálókról az eszközre vagy az eszközről a kiszolgálókra, és az eszközt küldje vissza az Azure-nak. Importálási sorrend esetén az Azure-adatközpontban automatikusan feltölti az adatait az eszközről az Azure-ba. A teljes folyamatot végigkövetheti a Data Box szolgáltatásban, az Azure Portalon.

### <a name="q-when-should-i-use-data-box"></a>K. Mikor érdemes a Data Boxot használni?
A. Ha 40-500 TB-nyi adatról szeretne átvinni az Azure-ba vagy az-ból, akkor a Data Box használata hasznos. Az 40 TB-nál < adatméretekhez használja a Data Box Disk és az adatméretet > 500 TB-ot, regisztráljon a [Data Box Heavyra](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>K. Mennyibe kerül a Data Box?
A. A Data Box 10 napig egy névleges díj fejében érhető el. Amikor az Azure Portalon egy rendelés létrehozásakor kiválaszt egy termékmodellt, megjelenik az eszköz ára. A standard szintű szállítási díjak és díjak az Azure Storage-ban is érvényesek. Az exportálási megrendelések hasonló díjszabási modellnek minősülnek, mint az importálási rendelésekhez, bár további kimenő költségek is érvényesek. 

További információért látogasson el a [Azure Data Box díjszabásra](https://azure.microsoft.com/pricing/details/storage/databox/) és a kimenő forgalomra vonatkozó [díjakra](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>K. Egyszerre legfeljebb mennyi adatot vihetek át a Data Box segítségével?
A. A Data Box összes kapacitása 100 TB, használható kapacitása pedig 80 TB. Data Box használatával akár 80 TB-nyi adat is továbbítható. Több adat átviteléhez több eszköz megrendelése szükséges.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>K. Honnan tudhatom meg, hogy a Data Box elérhető-e a régiómban? 
A.  További információ arról, hogy mely országokban/régiókban érhető el a Data Box, lépjen a [régió rendelkezésre állása](data-box-overview.md#region-availability)lehetőségre.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>K. Mely régiókban tárolhatok adatokat a Data Box segítségével?
A. A Data Box az USA, Nyugat-Európa, Észak-Európa, Franciaország, Egyesült Királyság, Japán, Ausztrália és Kanada területén található összes régióban támogatott. További információért lépjen a [régió rendelkezésre állása](data-box-overview.md#region-availability)elemre.

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>K. Hogyan importálhatók a forrásadatok egy adott országban egy másik országban vagy régióban lévő Azure-régióba, vagy exportálható az adatok az egyik országban található Azure-régióból egy másik országba/régióba?

Data Box támogatja az adatfeldolgozást vagy a kilépést kizárólag a rendeltetési országon/régión belül, és nem keresztez nemzetközi határokat. Az egyetlen kivétel az Európai Unió (EU) megrendeléseire vonatkozik, ahol az adatmezők bármely EU-országba/régióból elhelyezhetők.

Például abban az esetben, ha az importálási forgatókönyvben volt olyan adatforrás, amelyet Kanadában szeretne áthelyezni egy Azure West US Storage-fiókba, akkor a következő módon érheti el:

1. Megrendelési Data Box Kanadában a Storage-fiók kiválasztásával Kanadában. Az eszköz a kanadai Azure-adatközpontból a rendelés létrehozása során megadott szállítási lakcímbe (Kanadában) van elküldve.

2. Miután megtörtént a helyszíni Adatmásolás a Data Boxre, a rendszer visszaküldi az eszközt a kanadai Azure-adatközpontba. A Data Boxban található, majd a rendelés létrehozásakor kiválasztott Kanada Azure-régiójában lévő, a cél Storage-fiókba való feltöltésre kerül.

3. Ezután a AzCopy hasonló eszközzel másolhatók az adattárak az USA nyugati régiójában lévő Storage-fiókba. Ez a lépés a Data Box számlázásban nem szereplő [szabványos tárolási](https://azure.microsoft.com/pricing/details/storage/) és [sávszélesség-díjakat](https://azure.microsoft.com/pricing/details/bandwidth/) tartalmazza.

#### <a name="q-does-data-box-store-any-customer-data-outside-of-the-service-region"></a>K. A Data Box a szolgáltatási régión kívül tárolja az ügyféladatokat?

A. Nem. Data Box a szolgáltatási régión kívülről nem tárol ügyféladatokat. Az ügyfél teljes mértékben birtokolja az adatait, és a rendelés létrehozása során kiválasztott Storage-fiók alapján mentheti az adott helyre.  

A vásárlói adatok mellett Data Box adatokat is tartalmaz, amelyek az eszközhöz kapcsolódó biztonsági összetevőket, az eszköz és a szolgáltatás figyelési naplóit, valamint a szolgáltatással kapcsolatos metaadatokat tartalmaznak. Az adatvesztés elleni védelem érdekében az összes régióban (kivéve a Dél-és Délkelet-ázsiai régiókat) a rendszer egy geo-redundáns Storage-fiókkal tárolja és replikálja a párosított régióba Data Boxi az adatvesztést.  

A Dél-és Délkelet-Ázsiában található [adattárolási követelmények](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) miatt Data Box az adat tárolása egy zóna redundáns tárolási (ZRS) fiókban történik, hogy az egyetlen régióban legyen tárolva. Délkelet-Ázsiában az összes Data Box-szolgáltatás a Szingapúrban és Dél-Brazíliában található meg, az adattárolók Brazíliában vannak tárolva. 

Ha a Dél-Brazíliában és Délkelet-Ázsiában található szolgáltatások meghibásodása van, az ügyfelek új rendeléseket hozhatnak létre egy másik régióból. Az új megrendelések a létrehozott régióból lesznek kézbesítve, és az ügyfelek felelősek a Data Box eszköz és az oda való szállításért.

### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>K. Hogyan állíthatom helyre az adatokat, ha egy teljes régió meghibásodik?

A. Szélsőséges körülmények között, amikor egy régió elvesztése jelentős katasztrófa miatt megszakad, a Microsoft regionális feladatátvételt kezdeményezhet. Ebben az esetben nincs szükség beavatkozásra a részen. A megrendelés a feladatátvételi régión keresztül lesz teljesítve, ha ugyanazon az országon vagy kereskedelmi határon belül van. Egyes Azure-régiók azonban nem rendelkeznek ugyanahhoz a földrajzi vagy kereskedelmi határhoz tartozó párosított régióval. Ha bármelyik régióban van katasztrófa, létre kell hoznia a Data Box sorrendet egy másik elérhető régióból, és át kell másolnia az Azure-ba az új régióban. További információ: [Üzletmenet-folytonosság és vészhelyreállítás (BCDR): Az Azure párosított régiói](../best-practices-availability-paired-regions.md).

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>K. Kihez vegyem fel a kapcsolatot, ha a Data Boxkel kapcsolatos bármilyen problémára ráakadok?
A. Ha Data Boxtel kapcsolatos problémákba ütközik, [forduljon a Microsoft ügyfélszolgálatahoz](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>K. Elveszítettem a Data Box. Elveszett az eszköz díja?
A. Igen. Az elveszett vagy sérült eszközökért díjat számítunk fel. Ez a díj a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/storage/databox/) és a [termék használati feltételeiben](https://www.microsoft.com/licensing/product-licensing/products)szerepel.


## <a name="order-device"></a>Eszköz rendelése

### <a name="q-how-do-i-get-data-box"></a>K. Hogyan szerezhetem be a Data Boxot? 
A.  Az Azure Data Box beszerzéséhez jelentkezzen be az Azure Portalon, majd hozzon létre egy Data Box-megrendelést. Adja meg a kapcsolattartási és értesítési adatait. A megrendelés elküldése után a rendelkezésre állás függvényében 10 napon belül szállítjuk Önnek a Data Boxot. További információ [a Data Box megrendeléséről](data-box-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>K. Nem sikerült Data Box sorrendet létrehozni a Azure Portalban. Miért?
A. Ha nem hoz létre Data Box sorrendet, az előfizetési típussal vagy a hozzáféréssel kapcsolatos probléma merült fel.

Ellenőrizze az előfizetését. A Data Box csak Nagyvállalati Szerződés (EA) és Felhőszolgáltató (CSP) típusú előfizetés esetén érhető el. Ha nem rendelkezik ezekkel az előfizetési típusokkal, lépjen kapcsolatba Microsoft ügyfélszolgálata az előfizetés frissítéséhez.

Ha egy támogatott típusú előfizetéssel rendelkezik, ellenőrizze a hozzáférési szintjét. Megrendelés létrehozására csak az előfizetések közreműködői vagy tulajdonosai jogosultak.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>K. Mennyi időt vesz igénybe a rendelés létrehozása az Azure-ba feltöltött adatok alapján?

A. A megrendelés feldolgozásának minden fázisában a következő becsült bevezetési idő alapján érdemes elvárnia, mire számíthat.  

Ezek az átvezető időpontok a *becslések*. Az adatközpont, az egyidejű megrendelések és az egyéb környezeti feltételek betöltése hatással van a megrendelés feldolgozásának minden szakaszának időpontjára.

**Data Box rendelés becsült átvezetési ideje:**

1. Megrendelés Data Box: néhány perc a portálról
2. Eszközök kiosztása és előkészítése: 1-2 munkanap, a leltár rendelkezésre állása és a függőben lévő egyéb megrendelések teljesítése alapján
3. Szállítás: 2–3 munkanap
4. Adatmásolás az ügyfél webhelyén: az adatok természetétől, méretétől és a fájlok számától függ.
5. Visszaküldés: 2–3 munkanap
6. Eszköz feldolgozása az adatközpontban: 1-2 munkanap, a feldolgozásra váró egyéb megrendelések függvényében
7. Adatok feltöltése az Azure-ba: a feldolgozás befejezését követően kezdődik, és az eszköz csatlakoztatva van. A feltöltési idő az adatok természetétől, a mérettől és a fájlok számától függ.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>K. Rendeltem néhány Data Box-eszközt. Nem tudok további rendeléseket létrehozni. Miért?
A. Az előfizetések másodpercenként legfeljebb öt aktív rendelést engedélyezünk (a kiválasztott ország és régió kombinációja). Ha további eszközökre van szüksége, forduljon a Microsoft támogatási szolgálatához az előfizetésére vonatkozó korlátozás bővítéséhez.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>K. Amikor megpróbálok létrehozni egy rendelést, arról kapok értesítést, hogy a Data Box szolgáltatás nem érhető el. Mit jelent ez?
A. A Data Box szolgáltatás nem érhető el a kiválasztott ország és régió kombinációjára. A kombináció módosításával valószínűleg elérhetővé válik a Data Box szolgáltatás. Azon régiók listáját, ahol a szolgáltatás elérhető, a [Data Box regionális elérhetőségét](data-box-overview.md#region-availability) ismertető témakörben találja.

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>K. Néhány napja adtam fel a Data Box-megrendelésem. Mikor fogom megkapni a Data Boxot?
A. A megrendelések beérkezésekor ellenőrizzük, hogy van-e a rendelésnek megfelelő elérhető eszköz. Ha van, akkor 10 napon belül kiszállítjuk. Elképzelhető, hogy bizonyos időszakokban a kereslet megnövekszik. Ebben az esetben rendelését sorba állítjuk, a rendelés állapotát pedig az Azure Portalon követheti nyomon. Ha a rendelés 90 napon belül sem teljesül, akkor a rendszer automatikusan visszavonja azt.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>K. Feltöltöttem a Data Box az adataival, és egy másikat kell megrendelnie. Fel lehet adni gyorsan a megrendelést?
A. Lehetősége van klónozni az előző megrendelését. A klónozással létrehozhat egy, az előzővel azonos megrendelést, amelynek szerkesztheti a részleteit, anélkül, hogy újra meg kellene adnia a címet vagy az értesítési adatokat. A klónozás csak importálási rendelésekhez engedélyezett.

## <a name="configure-and-connect"></a>Konfigurálás és csatlakoztatás

### <a name="q-how-do-i-unlock-the-data-box"></a>K. Hogyan oldhatom fel a Data Box zárolását? 
A.  Nyissa meg az Azure Portalon a Data Box-megrendelését, majd az **Eszköz részleteit**. Másolja ki a zárolásfeloldási jelszót. Jelentkezzen be ezzel a jelszóval a Data Box helyi webes felhasználói felületére. További információt [az Azure Data Box kicsomagolását, illetve a kábelek és az eszköz csatlakoztatását](data-box-deploy-set-up.md) ismertető oktatóanyagban talál.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>K. Használhatok linuxos gazdaszámítógépet a Data Box csatlakoztatásához és az adatok másolásához?
A.  Igen. A Data Box-eszközökkel SMB- és NFS-ügyfelekhez is csatlakozhat. További információkért tekintse meg a gazdaszámítógépre vonatkozó [Támogatott operációs rendszerek](data-box-system-requirements.md) listáját.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>K. Már feladták a Data Boxom, de szeretném lemondani a megrendelést. Miért nincs Mégse gomb?
A.  A rendelést csak annak feladását követően, de még a feldolgozását megelőzően vonhatja vissza. Ha a Data Box-megrendelés feldolgozása már megtörtént, nem mondhatja le a rendelést. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>K. Csatlakoztathatok egy Data Boxot egyszerre több gazdaszámítógéphez adatátvitel céljából?
A. Igen. Egy Data Boxhoz egyszerre több gazdaszámítógép is csatlakoztatható, és párhuzamosan több másolási feladat is futtatható. További információt [az adatok az Azure Data Boxra történő másolását](data-box-deploy-copy-data.md) ismertető oktatóanyagban talál.

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>K. Csatlakozhatok a Data Box 10 GbE felületéhez is az adatok átviteléhez?
A. Igen. A GbE interfészek is csatlakoztathatók a Data Box az Adatmásolás közben. Az adatok másolásával kapcsolatos további információkért nyissa meg az [oktatóanyag: adatok másolása Azure Data Boxre](data-box-deploy-copy-data.md)című témakört.

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>K. Az elülső kezelőpanelen világít a rendszerhibajelző LED. Mit tegyek?
A. Egy Data Box elején két LED lámpa található a főkapcsoló gomb alatt. Az alsó – a rendszerhiba jelzője, amely jelzi, hogy a rendszer kifogástalan állapotú-e.

A rendszerhibák jelző LED-je piros színnel jelezheti az alábbi problémák egyikét:
- Ventilátor meghibásodása
- A CPU hőmérséklete magas
- Az alaplap hőmérséklete magas
- Kettős beágyazott memóriamodul (DIMM) hiba a kód (ECC) csatlakoztatásakor

Hajtsa végre a következő lépéseket:
1. Győződjön meg arról, hogy a ventilátor működik-e.
2. Helyezze át az eszközt egy nagyobb légáram feletti helyre.

Ha a rendszerhiba-jelző jelzőfény továbbra is fennáll, [forduljon a Microsoft ügyfélszolgálatahoz](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>K. Nem férek hozzá a Data Box zárolásfeloldási jelszavához az Azure Portalon. Miért?
A. Ha nem tudja elérni a zárolási jelszót a Azure Portalban, ellenőrizze az előfizetése és a Storage-fiókja engedélyeit. Győződjön meg arról, hogy közreműködői vagy tulajdonosi jogosultságokkal rendelkezik az erőforráscsoport szintjén. A hozzáférési hitelesítő adatok megtekintéséhez legalább Data Box operátori szerepkörre van szükség.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>K. Támogatott-e a Data Box a port csatorna konfigurációja? Az MPIO ismertetése
A. Nem támogatjuk a portok csatornájának konfigurációját, a többutas i/o (MPIO) konfigurációt vagy a vLAN-konfigurációt Data Boxon.

## <a name="track-status"></a>Állapot nyomon követése

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>K. Hogyan tudom nyomon követni a Data Boxot a megrendelésem feladásától kezdve egészen az eszköz visszaküldéséig? 
A.  A Data Box-megrendelésének állapotát az Azure Portalon követheti nyomon. A megrendelés létrehozásakor a rendszer felszólítja, hogy adjon meg egy értesítő e-mailt. Ha megadja az egyiket, a rendszer e-mailben értesíti a rendelés összes állapotának változásáról. További információ az [Értesítési e-mailek beállítása](data-box-portal-ui-admin.md#edit-notification-details) témában.

### <a name="q-how-do-i-return-the-device"></a>K. Hogyan küldhetem vissza az eszközt? 
A.  A Microsoft megjelenít egy fuvarlevélcímkét az E-ink-megjelenítőn. Ha a szállítási címke nem jelenik meg az E-Ink megjelenítésnél, ugorjon az **áttekintés > a szállítási címke letöltése** elemre. Töltse le és nyomtassa ki a címkét, szúrja be a címkét a tiszta műanyag címkébe az eszközön, és dobja ki az eszközt a szállítási szolgáltató helyén. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>K. Kaptam egy e-mail-értesítést, hogy az eszközöm elért az Azure-adatközpontba. Hogyan deríthetem ki, hogy az adatok felöltése folyamatban van-e?
A. Nyissa meg az Azure Portalon a Data Box-megrendelését, majd az **Áttekintést**. Ha az adatok feltöltése már megkezdődött, a jobb oldali panelen látható a másolás folyamatának előrehaladása. 

## <a name="migrate-data"></a>Adatok áttelepítése

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>K. Legfeljebb mekkora mennyiségű adat esetén használható a Data Box?  
A.  A Data Box hasznos tárolókapacitása 80 TB. Egyetlen Data Box-eszközön 40 TB és 80 TB közti mennyiségű adatot tárolhat. Akár 500 TB-ig nagyobb adatméretek esetén több Data Box eszközt is rendelhet. Az 500 TB-ot meghaladó adatmennyiségek esetén regisztráljon a Data Box Heavy szolgáltatásba.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>K. Mekkora a Data Box-eszközökön támogatott legnagyobb blokkblob és lapblob mérete? 
A.  A legnagyobb méreteket az Azure Storage tárhelykorlátai határozzák meg. A legnagyobb blokkblob mérete körülbelül 4,768 TiB, a legnagyobb lapblob mérete pedig 8 TiB. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](../storage/blobs/scalability-targets.md)ismertető témakört.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>K. Honnan tudhatom, hogy az átvitel során az adataim biztonságban vannak? 
A. Több biztonsági funkció is gondoskodik a Data Box biztonságáról az átvitel során. Ezek közé tartozik az illetéktelen hozzáférést megakadályozó burkolat, az illetéktelen hardver- vagy szoftverhozzáférést észlelő megoldások, vagy az eszköz zárolásának feloldására szolgáló jelszó. További információt [az Azure Data Box biztonsági és adatvédelmi szolgáltatásait](data-box-security.md) ismertető cikkben talál.

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>K. Hogyan másolhatom az adataimat a Data Boxra? 
A.  Ha SMB-ügyfelet használ, használhat egy SMB-másolási eszközt, például a `Robocopy` , `Diskboss` vagy akár a Windows fájlkezelő húzását az eszközre másoláshoz. 

Ha NFS-ügyfelet használ, akkor az [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) vagy [Ultracopier](https://ultracopier.first-world.info/) alkalmazást használhatja. 

További információt [az adatok az Azure Data Boxra történő másolását](data-box-deploy-copy-data.md) ismertető oktatóanyagban talál.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>K. Vannak tippek az adatok másolásának felgyorsítására?
A.  A másolási folyamat felgyorsításához:

- Használjon több másolási adatfolyamot. Például a `Robocopy` esetében használja a többszálú kapcsolót. A konkrét parancsokkal kapcsolatban további információt [az adatok az Azure Data Boxra másolását és ellenőrzését](data-box-deploy-copy-data.md) ismertető oktatóanyagban talál.
- Használjon több munkamenetet.
- A hálózati megosztáson való másolás helyett (ahol a hálózati sebesség korlátozhatja a másolási sebességet), az adatok helyileg is tárolhatók azon a számítógépen, amelyhez a Data Box csatlakoztatva van.
- Mérje meg az adatok másolására használt számítógép teljesítményét. Töltse le és használja az [ `Bluestop` `FIO` eszközt](https://ci.appveyor.com/project/axboe/fio) a kiszolgálói hardver teljesítményének összehasonlításához. Válassza ki a legújabb x86-vagy x64-buildet, válassza a **összetevők fület** , és töltse le az MSI-t.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>K. Használhatok több tárfiókot is a Data Boxhoz?
A.  Igen. A Data Boxhoz legfeljebb 10 tárfiók használata támogatott. Ezek lehetnek általános célú, klasszikus vagy Blob Storage-fiókok. A gyakori és a ritka elérésű blob is támogatott.


## <a name="ship-device"></a>Eszköz kiszállítása

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>K. Az eszköz kézbesítése megszakadt, de úgy tűnik, hogy az eszköz sérült. Mit tegyek?
A. Ha az eszköz megsérült, vagy ha bebizonyosodik az illetéktelen módosítást, ne használja az eszközt. [Vegye fel a kapcsolatot Microsoft ügyfélszolgálata](data-box-disk-contact-microsoft-support.md) és az eszközt a lehető leghamarabb küldje vissza. Létrehozhat egy új Data Box-megrendelést is egy csereeszköz igényléséhez. Ebben az esetben nem számítunk fel díjat a helyettesítő eszközért.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>K. Felvehetem magam is a Data Box rendelést? Visszaadhatom a Data Box a választott szolgáltatón keresztül?
A. Igen. A Microsoft a saját üzemeltetésű szállítást is biztosítja. A Data Box rendelés elhelyezésekor kiválaszthatja az önfelügyelt szállítási lehetőséget is. További információ: [önfelügyelt szállítás Data Box számára](data-box-portal-customer-managed-shipping.md).

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>K. Az Data Box-eszközök országa/régiója a szállítás során is határokon átnyúlik?
A. Az összes Data Box eszköz a rendeltetési országon/régión belül, és nem keresztez nemzetközi határokat. Az egyetlen kivétel az Európai Unió (EU) megrendeléseire vonatkozik, ahol az eszközök bármelyik EU-országba/régióba oda tudnak szállítani. Ez a Data Box és a Data Box Heavy eszközre is vonatkozik.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>K. Megrendeltem egy Data Box az USA keleti régiójában, de kaptam egy olyan eszközt, amelyet az USA nyugati régiójában szállítottak el. Hová kell visszaadni az eszközt?
A. A lehető legrövidebb időn belül megpróbáljuk beolvasni a Data Box eszközt. Rangsoroljuk a szállítást egy, a Storage-fiók helyéhez legközelebb lévő adatközpontból, de az eszköz minden olyan Azure-adatközpontból elérhető, amely rendelkezik rendelkezésre álló leltárral. A Data Box vissza kell adni ugyanarra a helyre, ahol a szállítási címkén látható módon szállították.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>K. Az E-ink-képernyőn nem jelenik meg a csomagvisszaküldési címke. Mit tegyek?
A. Ha az E-Ink kijelző nem jeleníti meg a visszáru-szállítólevél címkéjét, hajtsa végre a következő lépéseket:
- Távolítsa el a régi szállítási címkét és minden egyéb, az előző szállításból származó címkét.
- Nyissa meg az Azure Portalon a megrendelését. Nyissa meg az **áttekintést** , és **töltse le a szállítási címkéket**. További információ a [fuvarlevélcímke letöltéséről](data-box-portal-admin.md#download-shipping-label).
- Nyomtassa ki a címkét, és helyezze az eszközre rögzített átlátszó műanyag tasakba. 
- Ügyeljen arra, hogy a fuvarlevélcímke jól látható legyen. 

### <a name="q-how-is-my-data-protected-during-transit"></a>K. Milyen védelem alatt állnak az adataim a szállítás közben? 
A.  Az átvitel során a Data Box következő funkciói védik az adatokat.
 - A Data Box-lemezek 256 bites AES-titkosítással vannak titkosítva. 
 - Az eszköz zárolva van, és egy zárolásfeloldási jelszót igényel a belépéshez és az adatok eléréséhez.
További információ [a Data Box biztonsági funkcióiról](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>K. Befejeztem az importálási sorrendet, és leállítottam az eszközt. Továbbra is Hozzáadhatok további adatmennyiséget a Data Box?
A. Igen. Bekapcsolhatja az eszközt, és további adatokat adhat hozzá. A másolás végeztével újra futtatnia kell a **szállításra való előkészítés** műveletet.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>K. Megkaptam az eszközt, és nem indul el a rendszerindítás? Hogyan az eszköz visszaszállítása?
A. Ha az eszköz nem indul el, lépjen a sorrendbe a Azure Portal. Töltsön le egy szállítási címkét, és csatolja az eszközhöz. További információ a [fuvarlevélcímke letöltéséről](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Ellenőrzés és feltöltés

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>K. Milyen gyorsan férhetnek hozzá az Azure-beli adataim a Data Box visszaszállítása után? 
A.  Ha az **Adatmásolási** sorrend állapota **befejezettként** jelenik meg, azonnal hozzáférhet az adataihoz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>K. A feltöltést követően hol lesznek az adataim az Azure-ban?
A.  Amikor az adatait a Data Boxba másolja, attól függően, hogy az adatblokk blob-vagy lapozófájl-vagy Azure-fájlok, az Azure Storage-fiókban az alábbi elérési utak egyikére lesz feltöltve az adatai:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Másik lehetőségként megnyithatja az Azure Storage-fiókját a Azure Portalban, és innen navigálhat.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>K. Most vettem észre, hogy nem követtem az Azure elnevezési követelményeit a tárolóim esetében. Így is fel lehet tölteni az adataimat az Azure-ba?
A.  Ha a tárolók nevei nagybetűvel rendelkeznek, a rendszer automatikusan kisbetűvé alakítja ezeket a neveket. Ha a nevek valami más szempontból nem megfelelőek (például speciális karaktereket tartalmaznak, más nyelvűek stb.) akkor a feltöltés meghiúsul. A megosztások, tárolók és fájlok elnevezésével kapcsolatos további útmutatásért keresse fel a következőt:
- [Megosztások elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokkblobok és lapblobok konvenciói](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>K. Hogyan tudom ellenőrizni a Data Boxra feltöltött adataimat?
A.  Az adatmásolást követően a **szállításra való előkészítés** művelet futtatásakor a rendszer ellenőrzi az adatokat. Az ellenőrzési folyamat során létrehoz egy listát az adatfájlokról és azok ellenőrzőösszegeiről. Letöltheti a fájlok listáját, és ellenőrizheti a listát a forrásadatok fájljaiban. További információ [a szállításra való előkészítésről](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>K. Mi történik az adataim után a Data Box visszaküldése után?
A.  Ha befejeződött az adatok Azure-ba történő másolása, a rendszer a Data Box-lemezeken található adatokat a NIST SP 800-88 Revision 1 útmutatásainak megfelelően biztonságosan törli. További információ [az adatok a Data Boxról történő törléséről](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Naplózási jelentés

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Hogyan támogatja az Azure Data Box szolgáltatás az ügyfelek felügyeletilánc-eljárásait?
A.  Az Azure Data Box szolgáltatás natív módon biztosít jelentéseket, amelyek felhasználhatók a felügyeleti láncok dokumentációjához. A naplózási és másolási naplók az Azure-beli tárfiókban érhetők el, a [rendelési előzmények letöltése](data-box-portal-admin.md#download-order-history) pedig az Azure Portalon lehetséges a teljesítést követően.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Milyen típusú jelentések érhetők el a felügyeleti láncokhoz?
A.  A következő típusú jelentések érhetők el a felügyeleti láncokhoz:

- Közlekedési logisztika a UPS-ből.
- Bekapcsolási naplók és a felhasználói megosztások hozzáférési naplói.
- AJ-vagy manifest-fájl 64-bites ciklikus redundancia-ellenőrzővel (CRC-64) vagy ellenőrzőösszeggel minden, a Data Box sikeresen betöltött fájlhoz.
- Jelentések azon fájlokról, amelyeknek sikertelen volt a feltöltése az Azure-tárfiókba.
- A Data Box (az NIST 800 88R1 szabvány szerinti) megtisztítása az adatok Azure-tárfiókba való átmásolását követően.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Elérhetők-e a szállítói nyomkövetési naplók (UPS)? 
A.  A futárcégek nyomkövetési naplóit a Data Box rendelési előzményei tartalmazzák. Ez a jelentés azt követően érhető el, miután az eszköz visszatért az Azure-adatközpontba, és a lemezeken lévő adatok törölve lettek. Azonnali igény esetén közvetlenül a szolgáltató webhelyére is megtekintheti a megrendelés követési számát, és lekérheti a nyomkövetési adatokat.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Elszállíthatom magam a Data Boxot az Azure-adatközpontba? 
A.  Nem. Ha a Microsoft által felügyelt szállítást választotta, az adatátvitelt nem végezheti el. Az Azure-adatközpont jelenleg nem fogadja el az ügyfelektől vagy a UPS-től eltérő szállítótól érkező Data Box kézbesítését.

Ha az önfelügyelt szállítást választotta, akkor a Data Box az Azure-adatközpontból is kiválaszthatja vagy letilthatja.


## <a name="next-steps"></a>Következő lépések

- A [Data Box rendszerkövetelményeinek](data-box-system-requirements.md) áttekintése.
- A [Data Box korlátjainak](data-box-limits.md) értelmezése.
- Az [Azure Data Box](data-box-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.