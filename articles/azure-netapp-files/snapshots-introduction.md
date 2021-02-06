---
title: A Azure NetApp Files-Pillanatképek működése | Microsoft Docs
description: Bemutatja, hogyan működik Azure NetApp Files a pillanatképek, beleértve a pillanatképek létrehozásának módszereit, a pillanatképek visszaállításának módszereit, a pillanatképek használatát a régiók közötti replikációs beállításokban.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 526ef0af08833954aef4136716930cec0df40eea
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625247"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Az Azure NetApp Files pillanatképeinek működése

Ez a cikk ismerteti, hogyan működnek a Azure NetApp Files-Pillanatképek. Azure NetApp Files a pillanatkép-technológia stabilitást, méretezhetőséget és gyorsabb helyreállítást biztosít, és nem befolyásolja a teljesítményt. Azure NetApp Files Snapshot Technology biztosítja az adatvédelmi megoldások alapját, beleértve az Egyfájlos visszaállításokat, a kötetek visszatárolását és a klónozást, valamint a régiók közötti replikálást. 

A mennyiségi Pillanatképek használatával kapcsolatos lépésekért lásd: [Pillanatképek kezelése Azure NetApp Files használatával](azure-netapp-files-manage-snapshots.md). A régiók közötti replikáció pillanatkép-kezelésével kapcsolatos megfontolásokat a [régiók közötti replikáció használatára vonatkozó követelmények és megfontolások](cross-region-replication-requirements-considerations.md)című témakörben talál.

## <a name="what-volume-snapshots-are"></a>A kötetek pillanatképei  

Az Azure NetApp Files Pillanatképek egy adott időponthoz tartozó fájlrendszerbeli (Volume) rendszerképek. Ideális online biztonsági mentést is biztosít. Egy pillanatkép használatával [létrehozhat egy új kötetet](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [visszaállíthat egy fájlt](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client), vagy [visszaállíthat egy kötetet](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). Azure NetApp Files köteteken tárolt adott alkalmazásadatok esetében további lépésekre lehet szükség az alkalmazások konzisztenciájának biztosítása érdekében.  

Az alacsony terhelésű Pillanatképek a Azure NetApp Files részét képező kötet-virtualizációs technológia egyedi funkciói révén lehetségesek. Az adatbázishoz hasonlóan ez a réteg mutatókat használ a lemezen lévő tényleges adatblokkokhoz. Az adatbázistól eltérően azonban nem írhatók át meglévő blokkok; a frissített adatot egy új blokkba írja, és megváltoztatja a mutatót. Egy Azure NetApp Files pillanatkép egyszerűen manipulálja a blokkoló mutatókat, létrehoz egy "fagyasztva", csak olvasható nézetet a kötetről, amely lehetővé teszi, hogy az alkalmazások speciális programozás nélkül férhessenek hozzá a fájlok és a címtár-hierarchiák régebbi verzióihoz. A tényleges adatblokkok nem másolódnak át. Így a pillanatképek hatékonyak a létrehozásához szükséges idő alatt; Ezek a kötetek méretétől függetlenül szinte azonnal megtalálhatók. A pillanatképek is hatékonyak a tárolóhelyen. Ők maguk nem használnak szóközt, és csak a pillanatképek és az aktív kötetek közötti különbözeti blokkok maradnak meg. 

Az alábbi ábrák a fogalmakat szemléltetik:  

![A pillanatképek legfontosabb fogalmait bemutató diagramok](../media/azure-netapp-files/snapshot-concepts.png)

A diagramokon pillanatképet készítünk az 1a. ábrán. Az 1b. ábrán a módosított adat *új blokkba* kerül, és a mutató frissül. A pillanatkép mutatója azonban továbbra is a *korábban írt blokkra* mutat, így élő és az adatok előzményeit is megtekintheti. Egy másik pillanatkép az 1c. ábrán látható. Most már elérheti az adatok három generációját (az élő adatok, a Snapshot 2 és az 1. pillanatkép, az életkor szerinti sorrendben), anélkül, hogy el kellene végeznie a három teljes másolathoz szükséges lemezterületet. 

A pillanatkép csak a mennyiségi metaadatok (*inode tábla*) másolatát veszi át. Mindössze néhány másodpercet vesz igénybe, függetlenül a kötet méretétől, a felhasznált kapacitástól, illetve a kötet tevékenységének szintjétől. Ezért a 100-TiB-kötet pillanatképének elkészítésekor a rendszer a 100-GiB kötet pillanatképének elkészítésekor megegyező (nulla) időt vesz igénybe. A pillanatkép létrehozása után az adatfájlok módosításai a normál módon megjelennek a fájlok aktív verziójában.

Eközben a pillanatképből kimutatott adatblokkok stabilak és nem változtathatók meg. A Azure NetApp Files kötetek "írás közbeni átirányítása" jellegéből adódóan a pillanatkép nem tartalmaz teljesítménybeli terhelést, és önmagában nem használ szóközt. Az idő múlásával akár 255 pillanatképet is tárolhat, amelyek mindegyike csak olvashatóként és online verzióként érhető el, így kevesebb kapacitást használ, mint a megváltoztatott blokkok száma az egyes Pillanatképek között. A módosított blokkokat az aktív kötet tárolja. A pillanatképekre rámutató blokkok (csak olvashatóként) a köteten a megőrzéshez szükségesek, hogy csak akkor legyenek felhasználva, ha az összes mutatót (az aktív kötetben és a pillanatképekben) törölve lettek. Ezért a kötetek kihasználtsága a pillanatképekben tárolt új adatblokkok vagy (módosított) adatblokkok idővel növekedni fog.

 Az alábbi ábra a kötetek pillanatképeit és a felhasznált terület időbeli alakulását mutatja be: 

![A kötet pillanatképeit és a felhasznált terület időbeli alakulását ábrázoló diagram](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Mivel a kötet-pillanatkép csak a legutóbbi pillanatkép óta megjelenő blokk-változásokat rögzíti, a következő alapvető előnyöket biztosítja:

* A pillanatképek ***tárolási hatékonyságot*** mutatnak.   
    A pillanatképek minimális tárterületet használnak, mert nem másolja a teljes kötet adatblokkait. Két, egymás után készített pillanatkép eltér a kettő közötti időintervallumban hozzáadott vagy módosított blokkokkal. Ez a blokk növekményes viselkedése korlátozza a tárterülethez kapcsolódó kapacitás-felhasználást. Számos alternatív pillanatkép-implementáció az aktív fájlrendszerrel megegyező tárolási köteteket használja fel, és növeli a tárolási kapacitásra vonatkozó követelményeket. Az alkalmazások napi *letiltási szintjétől* függően Azure NetApp Files a pillanatképek több vagy kevesebb kapacitást fognak használni, de csak a módosított adatmennyiséget. Az átlagos napi pillanatkép-felhasználás a felhasznált kötetek kapacitása legfeljebb 1-5%-a, a kötetek esetében pedig akár 20-30%-kal, például SAP HANA adatbázis-köteteknél. Ügyeljen arra, hogy a [kötet-és a pillanatkép-használatot](azure-netapp-files-metrics.md#volumes) a pillanatképek kapacitásának felhasználására figyelje a létrehozott és karbantartott Pillanatképek számával.   

* A pillanatképek ***gyorsak a létrehozásához, replikálásához, visszaállításához vagy klónozásához***.   
    Csak néhány másodpercet vesz igénybe a pillanatképek létrehozása, replikálása, visszaállítása vagy klónozása, a kötet méretétől és a tevékenységek szintjétől függetlenül. [Igény szerint](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)mennyiségi pillanatképet is létrehozhat. A [Pillanatkép-szabályzatok](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) segítségével megadhatja, mikor Azure NetApp Files automatikusan hozzon létre egy pillanatképet, és hány pillanatképet kell megőrizni egy köteten.  Az alkalmazás konzisztenciája úgy érhető el, ha a pillanatképeket az alkalmazás réteggel összehangolja, például a SAP HANA [AzAcSnap eszközének](azacsnap-introduction.md) használatával.

* A pillanatképek nem befolyásolják a kötet ***teljesítményét***.   
    A kiállított technológia "írásra való átirányításának" jellegéből adódóan az Azure NetApp Files Pillanatképek tárolása vagy megőrzése nem befolyásolja a teljesítményt, még a nagy adattevékenységek esetében is. A pillanatképek törlése a legtöbb esetben nem befolyásolja a teljesítményt. 

* A pillanatképek ***skálázhatóságot*** biztosítanak, mert gyakran hozhatók létre, és számos megtartható.   
    Azure NetApp Files kötetek akár 255 pillanatképet is támogatnak. Az alacsony hatású, gyakran létrehozott Pillanatképek nagy mennyiségű tárolásának lehetősége növeli annak valószínűségét, hogy a kívánt verzió sikeresen helyreállítható.

* A pillanatképek a ***felhasználói láthatóságot** _ és _ *_fájl-helyreállító_* * fájlokat biztosítják.   
Azure NetApp Files Snapshot Technology nagy teljesítményének, méretezhetőségének és stabilitásának köszönhetően ideális online biztonsági mentést biztosít a felhasználó által vezérelt helyreállításhoz. A pillanatképek a fájl-, könyvtár-vagy kötet-visszaállítási célokra elérhetővé tehetők a felhasználók számára. A további megoldások lehetővé teszik a biztonsági másolatok másolását a kapcsolat nélküli tárolóba, vagy a [régiók közötti replikálást](cross-region-replication-introduction.md) a megőrzési vagy vész-helyreállítási célokra.

## <a name="ways-to-create-snapshots"></a>Pillanatképek létrehozásának módjai   

A pillanatképek létrehozásához és karbantartásához több módszert is használhat:

* Manuálisan (igény szerint), a következő használatával:   
    * A [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), a [REST API](/rest/api/netapp/snapshots), az [Azure CLI](/cli/azure/netappfiles/snapshot)vagy a [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot) -eszközök
    * Parancsfájlok (lásd a [példákat](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Automatizált, a használatával:
    * Pillanatkép-szabályzatok, [Azure Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [REST API](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy)vagy [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) eszközök használatával
    * Alkalmazás-konzisztens pillanatkép-eszközök, például [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>A kötetek és Pillanatképek replikálásának módja a DR  

Azure NetApp Files támogatja a [régiók közötti replikációt](cross-region-replication-introduction.md) a vész-helyreállítási (Dr) célokra. Azure NetApp Files régiók közötti replikáció SnapMirror technológiát használ. A rendszer csak a megváltozott blokkokat tömörített, hatékony formátumban továbbítja a hálózaton. Ha a kötetek között a régiók közötti replikációt kezdeményezik, a teljes kötet tartalma (azaz a tényleges tárolt adatblokkok) csak egyszer lesznek átadva. Ezt a műveletet alapkonfigurációnak *nevezzük.* A kezdeti átvitel után a rendszer csak a megváltozott blokkokat (a pillanatképekben rögzítettek szerint) helyezi át. Az eredmény a forrás kötet aszinkron 1:1-replikája, beleértve az összes pillanatképet. Ez a viselkedés teljes és növekményes – örökre replikációs mechanizmust követ. Ez a technológia minimálisra csökkenti a régiók közötti replikáláshoz szükséges adatok mennyiségét, így az adatátviteli költségek megtakarítását. Emellett lerövidíti a replikálási időt is. Egy kisebb helyreállítási időkorlátot (RPO) érhet el, mivel a rendszer több pillanatképet is létrehozhat, és gyakrabban, korlátozott adatátvitelsel. Továbbra is igénybe veszi a gazdagép-alapú replikációs mechanizmusokat, elkerülve a virtuális gépek és a szoftverlicenc-költségeket.

A következő ábra a régiók közötti replikációs helyzetekben a pillanatképek forgalmát mutatja be: 

![A régiók közötti replikációs helyzetekben a pillanatképek forgalmát bemutató diagram](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Az adatok pillanatképből való visszaállításának módjai  

A Azure NetApp Files Snapshot Technology nagy mértékben javítja a biztonsági mentések gyakoriságát és megbízhatóságát. Ez minimális teljesítménybeli terhelést jelent, és biztonságosan hozható létre aktív köteten. Azure NetApp Files Pillanatképek lehetővé teszik a közel-azonnali, biztonságos és opcionálisan felhasználó által felügyelt visszaállítást. Ez a szakasz azt ismerteti, hogyan lehet az adatokhoz hozzáférni vagy visszaállítani Azure NetApp Files pillanatképeket.

### <a name="restoring-files-or-directories-from-snapshots"></a>Fájlok vagy könyvtárak visszaállítása pillanatképből 

Ha a [Pillanatkép elérési útjának láthatósága](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) nem értékre van állítva `hidden` , a felhasználók közvetlenül hozzáférhetnek a pillanatképekhez a véletlen törlés, a sérülés vagy az adatok módosításának helyreállításához. A fájlok és könyvtárak biztonsága megmarad a pillanatképben, és a pillanatképek csak olvashatók a terv alapján. Így a helyreállítás biztonságos és egyszerű. 

A következő ábrán egy pillanatképhez tartozó fájl-vagy könyvtár-hozzáférés látható: 

![A pillanatképhez fájl-vagy címtár-hozzáférést bemutató diagram](../media/azure-netapp-files/snapshot-file-directory-access.png)

A diagramon az 1. pillanatkép csak a különbözeti blokkokat használja az aktív kötet és a pillanatkép-létrehozás pillanata között. Ha azonban a pillanatképet a kötet pillanatképének elérési útján keresztül éri el, az adatok úgy *jelennek* meg, mintha a pillanatkép létrehozásakor a teljes kötet kapacitása megmaradjon. A pillanatkép-mappák elérésével a felhasználók saját maguk állíthatják vissza az adatokból a fájlokat és könyvtárakat a választható pillanatképből.

Hasonlóképpen, a cél régiók közötti replikációs köteteken található Pillanatképek csak olvashatók a DR régióban tárolt adathelyreállításhoz.  

A következő ábra a pillanatképek elérését mutatja a régiók közötti replikációs helyzetekben: 

![A régiók közötti replikáció pillanatkép-hozzáférését bemutató diagram](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Lásd: [fájl visszaállítása pillanatképből](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) az egyes fájlok vagy könyvtárak pillanatképből való visszaállításával kapcsolatban.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Pillanatkép visszaállítása (klónozása) új kötetre

Azure NetApp Files pillanatképeket egy különálló, független kötetre is visszaállíthatja. Ez a művelet majdnem azonnali, a kötet méretétől és a felhasznált kapacitástól függetlenül. Az újonnan létrehozott kötet szinte azonnal elérhető a hozzáféréshez, míg a tényleges kötet és a pillanatkép adatblokkok át lesznek másolva. A kötet méretétől és a kapacitástól függően ez a folyamat jelentős időt vehet igénybe, amíg a fölérendelt kötet és a pillanatkép nem törölhető. A kötet azonban már a kezdeti létrehozás után is elérhető, míg a másolási folyamat folyamatban van a háttérben. Ez a funkció lehetővé teszi a gyors mennyiségi létrehozást az adathelyreállításhoz vagy a kötetek klónozásához a tesztelés és a fejlesztés érdekében. Az adatmásolási folyamat jellegéből adódóan a tárolási kapacitás készletének használata a visszaállítás befejeződése után megduplázódik, az új kötet pedig az eredeti pillanatkép teljes aktív kapacitását mutatja. A folyamat befejezése után a kötet független lesz, és az eredeti kötethez való társítása megtörténik, a forrás-és a pillanatképek pedig az új kötettől függetlenül kezelhetők vagy eltávolíthatók.

Az alábbi ábrán egy pillanatkép (klónozás) visszaállításával létrehozott új kötet látható:   

![Egy pillanatkép visszaállításával létrehozott új kötetet bemutató diagram](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Ugyanazokat a műveleteket hajthatja végre a replikált pillanatképeken a vész-helyreállítási (DR) köteten. Minden pillanatkép új kötetre állítható vissza, még akkor is, ha a régiók közötti replikáció aktív vagy folyamatban van. Ez a funkció lehetővé teszi a tesztelési és fejlesztési környezetek nem zavaró létrehozását egy DR régióban, a használatba vett adatmennyiséget, míg a replikált köteteket csak DR célra lehet használni. Ez a használati eset lehetővé teszi, hogy a tesztelés és a fejlesztés el legyen különítve a termeléstől, ami kiküszöböli a lehetséges hatást az éles környezetekre  

A következő ábra a kötet-helyreállítást (klónozást) mutatja be a DR cél kötetének pillanatképének használatával, miközben a régiók közötti replikáció zajlik:  

![A kötetek visszaállítását a DR célként megadott kötet pillanatképét ábrázoló diagram](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Lásd: [Pillanatkép visszaállítása új kötetre](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) a kötet-visszaállítási műveletekkel kapcsolatban.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Pillanatkép visszaállítása (visszaállítás folyamatban)

Bizonyos esetekben, mivel az új kötet tárolási kapacitást fog használni, előfordulhat, hogy a pillanatképből új kötetet nem szükséges vagy nem lehet létrehozni. Ha gyorsan helyre szeretné állítani az adatsérülést (például adatbázis-sérüléseket vagy ransomware támadásokat), akkor megfelelőbb lehet a köteten belüli pillanatkép visszaállítása. Ezt a műveletet a Azure NetApp Files pillanatkép-visszavonási funkciója segítségével végezheti el. Ez a funkció lehetővé teszi, hogy gyorsan visszaállítson egy kötetet arra az állapotba, amely egy adott pillanatkép készítésekor megtörtént. A legtöbb esetben a kötetek visszafordítása sokkal gyorsabb, mint az egyes fájlok egy pillanatképből az aktív fájlrendszerre való visszaállítása, különösen nagy méretű, több TiB-os köteteken. 

A kötet-Pillanatképek visszaállítása majdnem azonnali, és csak néhány másodpercig tart, még a legnagyobb kötetek esetében is. Az aktív kötet metaadatainak (*inode-tábla*) helyére a pillanatkép-metaadatokat a pillanatkép-létrehozás időpontjában kell cserélni, így a kötet visszaállítható az adott időpontra. Nem kell átmásolni az adatblokkokat a visszaállított állapotra a hatályba léptetéshez. Ezért sokkal hatékonyabb, mint a pillanatképek visszaállítása egy új kötetre. 

Az alábbi ábrán egy korábbi pillanatképre visszaállított kötet látható:  

![Egy korábbi pillanatképre visszaállító kötetet bemutató diagram](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> A rendszer elveszi a kiválasztott pillanatkép elkészítése után elkészített aktív fájlrendszerbeli adatok és Pillanatképek. A pillanatkép-visszavonási művelet lecseréli a célként megadott köteten lévő összes olyan adatmennyiséget, amely a kijelölt pillanatképben lévő adatokkal rendelkezik. A pillanatképek kiválasztásakor figyelmet kell fordítani a pillanatkép tartalmára és a létrehozási dátumra. A pillanatkép-visszaállítási művelet nem vonható vissza.  

Tekintse meg a kötet visszavonása a [Pillanatkép használatával](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) című témakört a funkció használatáról.

## <a name="how-snapshots-are-deleted"></a>A pillanatképek törlése 

A pillanatképek tárolási kapacitást használnak. Így általában nem maradnak meg határozatlan ideig. Az adatvédelem, az adatmegőrzés és a helyreállítás érdekében a különböző időpontokban létrehozott Pillanatképek általában online állapotban vannak, a RPO, a RTO és az adatmegőrzési SLA-követelményektől függően. A régebbi pillanatképeket azonban gyakran nem kell megőrizni a Storage szolgáltatásban, és előfordulhat, hogy törölni kell a tárterület felszabadításához. Bármely pillanatkép törölhető (nem feltétlenül a létrehozás sorrendjében) egy rendszergazdától bármikor. 

> [!IMPORTANT]
> A pillanatkép törlési művelete nem vonható vissza. Meg kell őriznie a kötet offline példányait az adatvédelem és a megőrzés céljából. 

Pillanatkép törlésekor a rendszer a pillanatképből származó összes mutatót eltávolítja a meglévő adatblokkokra. Ha egy adatblokknak nincs több mutatója (az aktív kötet vagy a köteten lévő egyéb Pillanatképek), a rendszer visszaküldi az adatblokkot a kötet szabad területére a későbbi használat érdekében. Ezért a pillanatképek eltávolítása általában nagyobb kapacitást szabadít fel egy köteten, mint az aktív kötet adatainak törlése, mert az adatblokkok gyakran a korábban létrehozott pillanatképekben vannak rögzítve. 

Az alábbi ábrán látható, hogy milyen hatással van a pillanatkép 3 törlésének a kötetről való törlése:  

![A pillanatkép törlésének tárolási felhasználási hatását bemutató diagram](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Ügyeljen arra, hogy [figyelje a kötetek és a pillanatképek felhasználását](azure-netapp-files-metrics.md#volumes) , és Ismerje meg, hogyan hatnak az alkalmazás, az aktív kötet és a pillanatkép-felhasználás. 

A pillanatképek törlésével kapcsolatos tudnivalókat lásd: [Pillanatképek törlése](azure-netapp-files-manage-snapshots.md#delete-snapshots) . A folyamat automatizálásával kapcsolatban lásd: [Pillanatkép-házirendek kezelése](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) .

## <a name="next-steps"></a>Következő lépések

* [Pillanatképek kezelése az Azure NetApp Filesszal](azure-netapp-files-manage-snapshots.md)
* [Kötet-és pillanatkép-metrikák figyelése](azure-netapp-files-metrics.md#volumes)
* [Pillanatkép-szabályzatokkal kapcsolatos problémák elhárítása](troubleshoot-snapshot-policies.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files Pillanatképek 101 videó](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files pillanatkép áttekintése](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



