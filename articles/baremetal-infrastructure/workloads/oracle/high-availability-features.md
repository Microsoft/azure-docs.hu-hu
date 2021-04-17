---
title: Magas rendelkezésre állású funkciók a Azure-beli Oracle BareMetalhoz
description: Ismerje meg az BareMetal oracle-adatbázisokhoz elérhető funkcióit.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 75032cc6351504a8400be43d05091d2b47484229
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590430"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Magas rendelkezésre állású funkciók a Azure-beli Oracle BareMetalhoz

Ebben a cikkben az Oracle főbb magas rendelkezésre állási és vészhelyreállítási funkcióival fogunk átesni.

Az Oracle számos funkciót kínál, hogy rugalmas platformot építsen ki az Oracle-adatbázisok futtatásához. Bár egyetlen funkció sem biztosít lefedettséget az összes hibatípushoz, a technológiák réteges kombinálása magas rendelkezésre álló rendszert hoz létre. Nem minden funkció szükséges a rendelkezésre állás fenntartásához. A stratégiák kombinálásával azonban a legjobb védelmet biztosít a különféle hibákkal kapcsolatban. 

## <a name="flashback-database"></a>Flashback Database

A [Flashback Database](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) szolgáltatás a Oracle Database Enterprise Edition. Visszatekeri az adatbázist egy adott időpontra. Ez a funkció [abban különbözik a Recovery Manager (RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) időponthoz időben való helyreállításától, hogy az aktuális időponttól távolodik el, és nem a visszaállítás utáni szélerősséges helyreállítás. Ez sokkal gyorsabb befejezési időket ad vissza.
 
Ezt a funkciót az Oracle Data Guard mellett [is használhatja.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590) A Flashback Database lehetővé teszi, hogy az adatbázis-rendszergazda visszaállítsa a hibás adatbázist egy Data Guard-konfigurációba teljes RMAN-visszaállítás és -helyreállítás nélkül. Ezzel a funkcióval sokkal gyorsabban visszaállíthatja a vészhelyreállítási képességet (valamint az Active Data Guard kihelyett jelentéskészítési és biztonsági mentési előnyeit).
 
Ezt a funkciót használhatja a készenléti adatbázis késleltetett újrahasználata helyett. A készenléti adatbázis vissza flash-ként visszajelenhet egy problémás pontra.
 
A Oracle Database a visszacsatolási naplókat a gyors helyreállítási területen (FRA) tartja. Ezek a naplók elkülönülnek az újrado-naplóktól, és több helyet igényelnek az FRA-ban. Alapértelmezés szerint a rendszer 24 órányi visszacsatolási naplót tart meg, de ez a beállítás az igényeinek megfelelően megváltoztatható.

## <a name="oracle-real-application-clusters"></a>Oracle Real Application Clusters

[Az Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) lehetővé teszi, hogy több összekapcsolt kiszolgáló egyetlen adatbázis-szolgáltatásként jelenjen meg a végfelhasználók és az alkalmazások számára. Ez a funkció számos meghibásodási pontot eltávolít, és oracle-adatbázisok felismert magas rendelkezésre állású aktív/aktív megoldása.

Ahogy az Oracle magas rendelkezésre állását áttekintő és ajánlott eljárásokat ismertető témakörének alábbi ábra is [mutatja,](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)egyetlen RAC-adatbázis jelenik meg az alkalmazásréteg számára. Az alkalmazások a SCAN listenerhez csatlakoznak, amely egy adott adatbázispéldányhoz irányítja a forgalmat. Az RAC több példány hozzáférését szabályozza, hogy fenntartsa az adatkonzisztenciát a különböző számítási csomópontokon.

![Az Oracle RAC architektúráját áttekintő diagram.](media/oracle-high-availability/oracle-real-application-clusters.png)

Ha egy példány meghibásodik, a szolgáltatás az összes többi példányon folytatódik. A megoldásban üzembe helyezett adatbázisok az n+1 RAC-konfigurációban lesznek, ahol n a szolgáltatás támogatásához szükséges minimális feldolgozási teljesítmény.

Oracle Database szolgáltatások lehetővé teszik a csomópontok közötti feladatátvételt, ha egy példány transzparens módon meghibásodik. Az ilyen hibák lehetnek tervezettek vagy nem tervezettek. Az alkalmazással való munka (gyors alkalmazásértesítési események), amikor egy példány elérhetetlenné válik, a szolgáltatás egy megmaradt csomópontra lesz áthelyezve. A szolgáltatás a szolgáltatás konfigurációjában megadott csomópontra kerül előnyben részesítettként vagy elérhetőként.

A szolgáltatások egy Oracle Database szolgáltatás csak a szerepkörétől függően indul el. Ez a funkció Data Guard feladatátvételkor használatos. A Data Guard használatával üzembe helyezett összes mintára szükség van egy adatbázis-szolgáltatás Data Guard-szerepkörhöz való csatolásához.

Létrehozható például két szolgáltatás, a MY DB APP és a \_ \_ MY DB \_ \_ AS. A MY DB APP szolgáltatás csak akkor lesz elindítva, ha az adatbázis-példány a \_ \_ PRIMARY Data Guard szerepkörrel van elindítva. A MY \_ DB AS csak akkor lesz elindítva, ha a Data Guard szerepkör FIZIKAI \_ \_ KÉSZENLÉTBEN van. Ez a konfiguráció lehetővé teszi, hogy az alkalmazások az APP szolgáltatásra, valamint a jelentéskészítésre is mutasson, amelyet ki lehet tölteni az Aktív készenlétbe, és az \_ \_ AS szolgáltatásra lehet rámutatni.

## <a name="oracle-data-guard"></a>Oracle Data Guard

A Data Guardgal az adatbázisok azonos másolatát külön fizikai hardveren tarthatja fenn. Ideális esetben a hardvert földrajzilag el kell különíteni egymástól. A Data Guard nem korlátozza a távolságot, bár a távolság hatással van a védelmi módokra. A nagyobb távolság késést okoz a helyek között, ami miatt előfordulhat, hogy bizonyos beállítások (például a szinkron replikáció) már nem lesznek használhatók.

A Data Guard a tárolószintű replikációval szemben kínál előnyöket:

- Mivel a replikáció adatbázis- tisztában van vele, a rendszer csak a releváns forgalmat replikálja.
- Bizonyos számítási feladatok magas bemeneti/kimeneti értékeket hozhatnak létre az ideiglenes táblaterek esetében, amelyek készenléti állapotban nem szükségesek, ezért nincsenek replikálva.
- A replikált blokkok ellenőrzése a készenléti adatbázisban történik, így biztosítva, hogy az elsődleges adatbázison bevezetett fizikai sérüléseket ne replikálja a rendszer a készenléti adatbázisba.
- Megakadályozza a logikai blokkon belüli és az elveszett írási sérüléseket. Emellett kiküszöböli annak a kockázatát, hogy a tár-rendszergazdák által vétett hibák a készenléti állapotba replikálnak.
Az ismétlés előre meghatározott ideig késleltethető, így a felhasználói hibák nem lesznek azonnal replikálva a készenléti állapotba.

## <a name="azure-netapp-files-snapshots"></a>Azure NetApp Files pillanatképek

Az BareMetalban használt NetApp Files tárolási megoldással pillanatképeket készíthet a kötetekről. A pillanatképekkel gyorsan visszaállíthatja a fájlrendszert egy adott időpontra. A pillanatkép-készítési technológiák lehetővé teszik a helyreállítási időre vonatkozó célkitűzés (RTO) olyan idejét, amely csak az adatbázis biztonsági másolatának visszaállításához szükséges idő töredékét teszi ki.

Az Oracle-adatbázisok pillanatkép-készítési funkciója az Azure NetApp SnapCenteren keresztül érhető el. A SnapCenter lehetővé teszi a kötetpillanatkép létrehozásának és visszaállításának ütemezését és automatizálását.

## <a name="recovery-manager"></a>Recovery Manager

Recovery Manager (RMAN) a fizikai adatbázisok biztonsági mentésének előnyben részesített segédprogramja. Az RMAN az adatbázisvezérlő fájllal (vagy egy központi helyreállítási katalógussal) kommunikál az adatbázis különböző alapvető összetevőinek védelme érdekében, beleértve a következőket:

- Adatbázis-adatfájlok
- Archivált újrado-naplók
- Adatbázis-vezérlőfájlok
- Adatbázis-inicializálási fájlok (spfile)

Az RMAN lehetővé teszi az adatbázisok biztonsági mentését a meleg vagy a hideg időről. Ezekkel a biztonsági másolatokkal készenléti adatbázisokat hozhat létre, vagy adatbázisokat duplikálhat környezetek klónozásához. Az RMAN egy visszaállítás-érvényesítési funkcióval is rendelkezik. Ez a függvény beolvas egy biztonságimásolat-készletet, és meghatározza, hogy használható-e az adatbázis adott időpontra való helyreállítására.

Mivel az RMAN egy Oracle által biztosított segédprogram, képes beolvasni az adatbázisfájlok belső struktúráját. Ez lehetővé teszi fizikai és logikai sérülési ellenőrzések futtatását a biztonsági mentési és visszaállítási műveletek során. Az adatbázis adatfájlokat is helyreállíthat, és egyes adatfájlokat és tablespace-eket visszaállíthat egy adott időpontra. Ezek az RMAN által nyújtott előnyök a tárolási pillanatképekkel való használathoz. Az RMAN biztonsági mentései utolsó védelmi vonalként használhatók a teljes adatvesztéssel szemben, ha nem használhat pillanatképeket.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Oracle bareMetal-infrastruktúrán futó védelmének és teljesítményének optimalizálására vonatkozó lehetőségeket és javaslatokat:

> [!div class="nextstepaction"]
> [Oracle BareMetal Infrastructure-kiszolgálók beállításai](options-considerations-high-availability.md)
