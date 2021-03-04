---
title: Mi az az Azure Reservations?
description: Megismerheti az Azure Reservationst és annak díjszabását a virtuális gépek, az SQL Database-adatbázisok, az Azure Cosmos DB és más erőforrások fenntartott példányaira vonatkozó költségek csökkentéséhez.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 02/24/2021
ms.author: banders
ms.openlocfilehash: 7c2cf88eed3d8b727147f57ef9e2b52afd07de59
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039138"
---
# <a name="what-are-azure-reservations"></a>Mi az az Azure Reservations?

Az Azure Reservationszel csökkentheti költségeit, ha több termékre is egy vagy három évre előre kötelezettséget vállal. A kötelezettségvállalással kedvezményt kaphat a felhasznált erőforrásokra. A Reservationszel a használatalapú fizetéshez képest jelentősen, akár 72%-kal csökkentheti az erőforrások költségeit. A Reservations számlázási kedvezményt nyújt, és nincs hatással az erőforrások futtatási állapotára. A foglalás megvásárlása után a rendszer automatikusan alkalmazza a kedvezményt az annak megfelelő erőforrásokra.

A foglalásért fizethet előre vagy havonta. A foglalások előre vagy havonta fizetett összege megegyezik, és ha a havi fizetést választja, nem kell extra díjakat fizetnie. A havi fizetés csak az Azure Reservations esetében érhető el, harmadik féltől származó termékek esetében nem.

Foglalásokat az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) vásárolhat.

## <a name="why-buy-a-reservation"></a>Miért érdemes foglalást vásárolnia?

Ha az erőforrások használata következetes mintát mutat, amely lehetővé teszi a foglalások alkalmazását, akkor a foglalások vásárlásával csökkentheti a költségeit. Ha például egy szolgáltatás több példányát futtatja folyamatosan foglalás nélkül, akkor használatalapú fizetéses díjakat fizet. Ha vásárol egy foglalást, azonnal megkapja a foglalási kedvezményt. Az erőforrásokért ezután nem használatalapú fizetéses díjakat kell fizetnie.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A vásárlást követően a foglalási kedvezmény automatikusan érvényesül a foglalás megvásárlásakor kiválasztott attribútumoknak megfelelő erőforrások használatára. Az attribútumok közé tartozik a termékváltozat, (adott esetben) a régiók és a hatókör. A foglalás hatóköre megadja a foglalási kedvezmények alkalmazásának helyét.

A kedvezmények alkalmazásáról további információt [a fenntartott példányokra vonatkozó kedvezmény alkalmazását](reservation-discount-application.md) ismertető oldalon talál.

A foglalási hatókörök működésére vonatkozó további információkat a [foglalási hatókörökkel](prepare-buy-reservation.md#scope-reservations) foglalkozó témakör ismerteti.

## <a name="determine-what-to-purchase"></a>Kiválaszthatja, hogy mit szeretne megvásárolni 

Az Azure Databricksen kívül minden foglalási díj óraalapú. A foglalásokat az alapján érdemes megvásárolni, hogy általában milyen használat jellemzi a rendszerét. A használati adatok elemzésével vagy a foglalási javaslatokkal határozhatja meg, hogy milyen foglalást vásároljon. A javaslatok a következő helyeken érhetők el:

- Azure Advisor (csak virtuális gépek)
- Foglalásvásárlási folyamat az Azure Portalon
- Cost Management Power BI-alkalmazás
- API-k 

További információkért lásd:  [A megvásárolni kívánt foglalás kiválasztása](determine-reservation-purchase.md) 

## <a name="buying-a-reservation"></a>Foglalás vásárlása 

Foglalásokat az Azure Portalon, API-kkal, a PowerShellben és a parancssori felületen vásárolhat. 

Nyissa meg az [Azure Portalt](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) a vásárláshoz.

További információért lásd:  [Foglalás vásárlása](prepare-buy-reservation.md).

## <a name="how-is-a-reservation-billed"></a>Hogy történik a foglalás számlázása? 

A foglalás számlázása az előfizetésnél rögzített fizetési móddal történik. Ha elérhető, a foglalás költsége az Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesz levonva. Ha az Azure-előrefizetés egyenlege nem fedezi a foglalás költségét, akkor a többletköltségek ki lesznek Önnek számlázva. Ha használatalapú díjas, egyéni csomagban rendelkezik előfizetéssel, akkor a rendszer az előre fizetendő vásárlással azonnal megterheli a fiókjához tartozó hitelkártyát. A havi kifizetések a számlán jelennek még, és a rendszer havonta terheli meg a hitelkártyáját. Számlás fizetés esetén a díjak a következő számlán jelennek meg. 

## <a name="who-can-manage-a-reservation-by-default"></a>Alapértelmezés szerint kik kezelhetik a foglalásokat?

Alapértelmezés szerint a következő felhasználók tekinthetik meg és kezelhetik a foglalásokat:

- A foglalást vásárló személyt és a foglalás vásárlásához használt számlázási előfizetés fiókadminisztrátorát a rendszer hozzáadja a foglalási rendeléshez.
- Nagyvállalati Szerződéssel és Microsoft Ügyfélszerződéssel rendelkező számlázási rendszergazdák.

A foglalások kezelésének engedélyezéséhez más személyek számára lásd: [Foglalások kezelése az Azure-erőforrásoknál](manage-reserved-vm-instance.md).

## <a name="get-reservation-details-and-utilization-after-purchase"></a>Foglalásrészletek és kihasználtság lekérése a vásárlás után

Ha van engedélye a foglalás megtekintésére, az Azure Portalon tekintheti meg és használhatja. Az adatokat API-kkal is lekérheti. 

A foglalások Azure Portalon való megtekintéséről további információért lásd:  [Foglalások megtekintése az Azure Portalon](view-reservations.md) 

## <a name="manage-reservations-after-purchase"></a>Foglalások kezelése vásárlás után 

Egy Azure-foglalás megvásárlása után frissítheti a hatókört, hogy a foglalást egy másik előfizetésre alkalmazza, módosítsa, hogy ki kezelheti a foglalást, kisebb részekre ossza a foglalást, vagy módosítsa a példányméret rugalmasságát. 

További információk:  [Foglalások kezelése Azure-erőforrásokhoz](manage-reserved-vm-instance.md) 

## <a name="flexibility-with-azure-reservations"></a>Rugalmasság az Azure Reservations segítségével

Az Azure Reservations biztosítja a rugalmasságot a változó igények kielégítéséhez. A foglalásokat le tudja cserélni azonos típusú foglalásokra. Kérheti egy foglalás díjának visszatérítését is 50 000 USD összegig az elmúlt 12 hónapra vonatkozóan, ha már nincs rá szüksége. A visszatérítések felső korlátja a Microsofttal kötött szerződése hatálya alá tartozó összes foglalásra vonatkozik.

További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>A foglalás által fedezett költségek

- **Fenntartott virtuálisgép-példány** – A foglalás csak a virtuális gép és a felhőszolgáltatások számítási költségeit fedezi. Nem fedezi azonban a további szoftverek, a Windows, a hálózatkezelés és a tárolás költségeit.
- **Fenntartott kapacitás az Azure Storage-ban** – A foglalás a standard Blob Storage- vagy Azure Data Lake Gen2-tárfiókok tárolási kapacitását fedezi. A foglalás nem fedezi a sávszélességgel és a tranzakciókkal járó díjakat.
- **Fenntartott Azure Cosmos DB-kapacitás** – A foglalás az erőforrások számára kiosztott átviteli sebességet fedezi. Nem fedezi azonban a tárolás és a hálózatkezelés költségeit.
- **Adatáramlások Azure Data Factory** – a foglalás magában foglalja az integrációs modul költségét a megvásárolt számítási típushoz és a magok számához.
- **Fenntartott SQL Database virtuális mag** – A felügyelt SQL-példányt és az SQL Database rugalmas készletet/önálló adatbázist is lefedi. A foglalás csak a számítási költségeket foglalja magában. Az SQL-licenc számlázása külön történik.
- **Azure Synapse Analytics** – A foglalás a cDWU-használatot fedezi. Nem fedezi az Azure Synapse Analytics használatával kapcsolatos tárolási és hálózatkezelési költségeket.
- **Azure Databricks** – A foglalás csak a DBU-használatot fedezi. Az egyéb – például számítási, tárolási és hálózati – díjakat külön számítjuk fel.
- **App Service-bélyeg díja** – A foglalás a bélyeghasználatot fedezi. Nem érvényes azonban a feldolgozókra, így a bélyeghez társított egyéb erőforrásokért külön kell fizetnie.
- **Azure Database for MySQL** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a MySQL-adatbáziskiszolgálóhoz kapcsolódó szoftverek, hálózatkezelés és tárolás költségeit.
- **Azure Database for PostgreSQL** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a PostgreSQL-adatbáziskiszolgálóhoz kapcsolódó szoftverek, hálózatkezelés és tárolás költségeit.
- **Azure Database for MariaDB** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a MariaDB-adatbáziskiszolgálóhoz kapcsolódó szoftverek, hálózatkezelés és tárolás költségeit.
- **Azure Data Explorer** – A foglalás magában foglalja az árrés díját. A foglalás nem vonatkozik a fürtökhöz kapcsolódó számítási feladatok, hálózatkezelés és tárolás költségeire.
- **Azure Cache for Redis** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a Redis Cache-példányokhoz kapcsolódó hálózatkezelés és tárolás költségeit.
- **Azure Dedicated Host** – A dedikált gazdagép csak a számítási költségeket foglalja magában.
- **Azure Disk Storage-foglalások** – A foglalás csak a P30-as vagy annál nagyobb méretű, prémium szintű SSD-k esetében érvényes. Az egyéb lemeztípusokat és a P30-asnál kisebb méretű lemezeket nem fedi le.

Szoftvercsomagok:

- **SUSE Linux** – A foglalás a szoftvercsomag költségeit foglalja magában. A kedvezmények csak a SUSE-mérőszámokra vonatkoznak, a virtuálisgép-használatra nem.
- **Red Hat-csomagok** – A foglalás a szoftvercsomag költségeit foglalja magában. A kedvezmények csak a RedHat-mérőszámokra vonatkoznak, a virtuálisgép-használatra nem.
- **Azure VMware Solution by CloudSimple** – A foglalás a VMware CloudSimple-csomópontokat foglalja magában. További szoftverköltségek is felmerülhetnek.
- **Azure Red Hat OpenShift** – A foglalás az OpenShift-költségeket foglalja magában, az Azure-infrastruktúra költségeit nem.

A Windows rendszerű virtuális gépek és az SQL Database esetében a foglalási kedvezmény nem vonatkozik a szoftverek költségeire. A licencelési költségeket az [Azure Hybrid Benefittel](https://azure.microsoft.com/pricing/hybrid-benefit/) fedezheti.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:
    - [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
    - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)
    - [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)

- További információk a szolgáltatáscsomagok foglalásaival kapcsolatban:
    - [Azure Reserved VM Instancesszel rendelkező virtuális gépek](../../virtual-machines/prepay-reserved-vm-instances.md)
    - [Fenntartott Azure Cosmos DB-kapacitással rendelkező Azure Cosmos DB-erőforrások](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrások](../../azure-sql/database/reserved-capacity-overview.md)
    - [Fenntartott Azure Cache for Redis-kapacitással rendelkező Azure Cache for Redis-erőforrások](../../azure-cache-for-redis/cache-reserved-pricing.md) További információk a szoftvercsomagok foglalásaival kapcsolatban:
    - [Azure Reservations-beli Red Hat-szoftvercsomagok](../../virtual-machines/linux/prepay-suse-software-charges.md)
    - [Azure Reservations-beli SUSE-szoftvercsomagok](../../virtual-machines/linux/prepay-suse-software-charges.md)
