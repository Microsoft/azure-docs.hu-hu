---
title: Frissítés általános célú v2-tárfiókra
titleSuffix: Azure Storage
description: Frissítés általános célú v2-tárfiókra a Azure Portal, a PowerShell vagy az Azure CLI használatával. Adja meg a blobadatok hozzáférési rétegét.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d6d94efea52d920b8bfe69be2ee07c8b829792d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484098"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Frissítés általános célú v2-tárfiókra

Az általános célú v2-tárfiókok támogatják az Azure Storage legújabb funkcióit, és tartalmazzák az általános célú v1 és Blob Storage-fiókok összes funkcióját. A legtöbb tárolási forgatókönyvhöz az általános célú v2-fiókok használata ajánlott. Az általános célú v2-fiókok biztosítják az Azure Storage legalacsonyabb gigabájtonkénti kapacitásárait, valamint iparági szinten versenyképes tranzakciós árakat. Az általános célú v2-fiókok támogatják az alapértelmezett fiókelérési szinteket a gyors vagy a elérésű, valamint a blobszintű rétegezést a forró, a elérésű és az archív szint között.

Az általános célú v2-tárfiókra való frissítés egyszerű az általános célú v1- vagy Blob Storage-fiókokról. A frissítéshez használhatja a Azure Portal, a PowerShellt vagy az Azure CLI-t. Az általános célú v2-tárfiókra való frissítés nem jár leállással vagy adatvesztéssel. A fiókfrissítés egy egyszerű művelettel Azure Resource Manager, amely módosítja a fiók típusát.

> [!IMPORTANT]
> Az általános célú v1- vagy Blob Storage-fiók általános célú v2-re való frissítése végleges, és nem vonható vissza.

> [!NOTE]
> Bár a Microsoft a legtöbb forgatókönyvhöz általános célú v2-fiókokat javasol, a Microsoft továbbra is támogatja az általános célú v1 fiókokat az új és meglévő ügyfelek számára. Az általános célú v1 tárfiókokat új régiókban hozhatja létre, ha az Azure Storage elérhető az egyes régiókban. A Microsoft jelenleg nem tervezi az általános célú v1-fiókok támogatásának kiprecatezését, és az Azure Storage-funkciók elalasztása előtt legalább egy évre előre értesítést ad. A Microsoft továbbra is biztosít biztonsági frissítéseket az általános célú v1-fiókokhoz, de ehhez a fióktípushoz nem várható új funkciófejlesztés.
>
> A 2020. október 1. utáni új Azure-régiók esetében az általános célú v1 fiókok díjszabása megváltozott, és megegyezik az ilyen régiókban található általános célú v2-fiókok díjszabásának megfelelő értékekkel. A 2020. október 1. előtti Azure-régiókban található általános célú v1-fiókok díjszabása nem változott. Az adott régióban található általános célú v1-fiókok díjszabását az Azure Storage díjszabási oldalán olvashatja el. Válassza ki a régiót, majd a **Díjszabási ajánlatok mellett válassza** az **Egyéb lehetőséget.**

## <a name="upgrade-an-account"></a>Fiók frissítése

Ha általános célú v1- vagy Blob Storage-fiókot általános célú v2-fiókra frissít, használja a Azure Portal, a PowerShellt vagy az Azure CLI-t.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a tárfiókot.
3. A Beállítások **szakaszban** kattintson a Konfiguráció **elemre.**
4. A **Fiók típusa** területen kattintson a **Frissítés** elemre.
5. A **Frissítés megerősítése** területen írja be a fiók nevét.
6. Kattintson **a panel** alján található Frissítés gombra.

    ![Fiók fajtászámának frissítése](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha egy általános célú v1-fiókot általános célú v2-fiókra frissít a PowerShell használatával, először frissítse a PowerShellt az Az.Storage modul legújabb **verziójának használatára.** A PowerShell telepítésével kapcsolatos információkért lásd [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikket.

Ezután hívja meg a következő parancsot a fiók frissítéshez, és helyettesítse be az erőforráscsoport nevét, a tárfiók nevét és a kívánt fiók-hozzáférési szintet.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha egy általános célú v1-fiókot az Azure CLI használatával általános célú v2-fiókra frissít, először telepítse az Azure CLI legújabb verzióját. A CLI telepítésével kapcsolatban lásd [az Azure CLI 2.0-s verziójának telepítését](/cli/azure/install-azure-cli) ismertető szakaszt.

Ezután hívja meg a következő parancsot a fiók frissítéshez, és helyettesítse be az erőforráscsoport nevét, a tárfiók nevét és a kívánt fiók-hozzáférési szintet.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Hozzáférési szint megadása a blobadatokhoz

Az általános célú v2-fiókok az összes Azure Storage-szolgáltatást és adatobjektumot támogatják, de a hozzáférési szintek csak a Blob Storage-ban lévő blokkblobok számára érhetők el. Amikor általános célú v2-tárfiókra frissít, megadhat egy alapértelmezett hozzáférési szintet a fiókhoz , amely azt jelzi, hogy a blobadatok alapértelmezett rétege úgy lesz feltöltve, mintha az egyes blob-hozzáférési szint paramétere nincs megadva.

A blob-hozzáférési szintek lehetővé teszik, hogy a várt használati minták alapján a leghatékonyabb tárolót válassza. A blokkblobok a forró, a hűtési és az archív tárolási szinten is tárolhatók. A hozzáférési szintekkel kapcsolatos további információkért lásd: [Azure Blob Storage: Hot (Gyors elérésű, cool) és archive storage tiers (Azure Blob Storage: A](../blobs/storage-blob-storage-tiers.md)gyors és a gyors elérésű, valamint az archív tárolási szintek).

Alapértelmezés szerint a rendszer létrehoz egy új tárfiókot a gyorselérési szinten, és egy általános célú v1 tárfiók frissíthető a fiókszintre. Ha a frissítéskor nincs megadva fiókelérési szint, a rendszer alapértelmezés szerint a gyorselérési szintre frissíti. Ha azt vizsgálja, hogy melyik hozzáférési szintet kell használnia a frissítéshez, fontolja meg az aktuális adathasználati forgatókönyvet. Az általános célú v2-fiókra való áttelepítésnek két tipikus felhasználói forgatókönyve van:

* Rendelkezik egy meglévő általános célú v1-tárfiókkal, és szeretné kiértékelni egy általános célú v2-tárfiókra való frissítést, amely a blobadatokhoz megfelelő tárolási hozzáférési szinttel rendelkezik.
* Úgy döntött, hogy általános célú v2-tárfiókot használ, vagy már rendelkezik ilyen fiókkal, és szeretné kiértékelni, hogy a blobadatokhoz érdemes-e a tároláshoz a forró vagy a elérésű hozzáférési szintet használni.

Mindkét esetben az első prioritás az általános célú v2-tárfiókban tárolt adatok tárolási, hozzáférési és üzemeltetési költségeinek becslése, és ezek összehasonlítása az aktuális költségekkel.

## <a name="pricing-and-billing"></a>Árak és számlázás

A v1 tárfiókok általános célú v2-fiókra való frissítése ingyenes. A frissítési folyamat során megadhatja a kívánt fiókszintet. Ha a frissítéskor nincs fiókszint megadva, a frissített fiók alapértelmezett fiókrétege `Hot` lesz. A tárelérési szint frissítés utáni módosítása azonban a számla változását eredményezheti, ezért javasoljuk, hogy a frissítés során adja meg az új fiókszintet.

Az összes tárfiók az egyes blobok szintjén alapuló árképzési modellt alkalmaz a blobtároláshoz. Tárfiókok használatakor az alábbi számlázási szempontok érvényesülnek:

* **Tárolási költségek:** A tárolt adatok mennyisége mellett az adatok tárolási költsége a tárolási hozzáférési szinttől függően változik. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.

* **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A elérésű és archív tárolási szint adataiért gigabájtonkénti adatelérési díjat kell fizetnie az olvasásokért.

* **Tranzakciós költségek**: Minden szint esetében tranzakciónkénti díjat kell fizetni, ez emelkedik, ha a szint ritkábban használt adatokat tárol.

* **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.

* **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.

* **A tárelérési** szint módosítása: Ha a fiók tárelérési rétegét a elérésükről a elérésükről a leggyakoribbakra módosítja, akkor a díj megegyezik a tárfiókban lévő összes adat beolvasásának költségével. Ha azonban a fiók hozzáférési rétegét a elérésükről a elérésükbe írja, akkor az összes adat a elérésű szintre kerül (csak GPv2-fiókok esetében).

> [!NOTE]
> A tárfiókok árképzési modelljével kapcsolatos további információért lásd [az Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Az aktuális használati minták költségeinek becslése

A blobadatok egy adott szinten lévő általános célú v2-tárfiókban való tárolásának és elérésének költségeinek megbecsléséhez értékelje ki a meglévő használati mintát vagy a várható használati mintát. Általában a következőket érdemes figyelembe venni:

* A Blob Storage-használat gigabájtban, beleértve a következőket:
  * Mennyi adatot tárol a tárfiókjában?
  * Havi lebontásban hogyan változik az adatmennyiség? Az új adatok folyamatosan átveszik a korábbi adatok helyét?

* A Blob Storage-adatok elsődleges hozzáférési mintája, beleértve a következőket:
  * Mennyi adatot olvas be és ír a tárfiókba?
  * Hány olvasási és írási művelet történik a tárfiókban az adatokon?

Az igényeinek leginkább megfelelő hozzáférési szint meghatározásához hasznos lehet meghatározni a blobadat-kapacitást és az adatok felhasznált módjaját. Ez a legjobban a fiók monitorozási metrikai alapján használhatja.

### <a name="monitoring-existing-storage-accounts"></a>A meglévő tárfiókok figyelése

A meglévő tárfiókok monitorozásához és az ezzel kapcsolatos adatgyűjtéshez nyújt segítséget az Azure Storage Analytics, amellyel naplózhatja a tárfiókokat, és megnézheti a fiókokra vonatkozó mérőszámokat. A Storage Analytics olyan mérőszámokat tárol, amelyek a tárolási szolgáltatáshoz érkező kérések összesített tranzakcióstatisztikáját és kapacitási adatait tartalmazzák mind a GPv1 és a GPv2, mind a Blob Storage fióktípus esetében. Ezeket az adatokat a jól ismert táblák tárolják az adott tárfiókban.

További információért lásd [a Storage Analytics mérőszámainak áttekintését](../blobs/monitor-blob-storage.md) és [a Storage Analytics mérőszámainak táblasémáját](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> A Blob Storage-tárfiókok a tábla szolgáltatásvégpontját csak az adott fiók mérőszámadatainak tárolásához és eléréséhez jelenítik meg.

A Blob Storage tárolófelhasználásának figyeléséhez engedélyeznie kell a kapacitási mérőszámot.
Ha ez a mérőszám engedélyezve van, a rendszer naponta rögzíti a tárfiók blobszolgáltatásának kapacitásadatait, és létrehoz egy táblabejegyzést az adott tárfiók *$MetricsCapacityBlob* táblájában.

A Blob Storage adathozzáférési mintáinak figyeléséhez engedélyeznie kell az óránkénti tranzakciók mérőszámát az API szintjén. Ha az óránkénti tranzakciók mérőszáma engedélyezve van, a rendszer óránként összesíti az API-tranzakciókat, és táblabejegyzést hoz létre az adott tárfiók *$MetricsHourPrimaryTransactionsBlob* táblájában. A *$MetricsHourSecondaryTransactionsBlob* tábla a másodlagos végpontra rögzíti a tranzakciókat RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> Ha rendelkezik egy általános célú tárfiókkal, amelyben lapblobokat és virtuálisgép-lemezeket, illetve üzenetsorokat, fájlokat vagy táblákat tárol a blokkblobok és a hozzáfűző blobok adatai mellett, akkor ez a becslési folyamat nem alkalmazható. A kapacitási adatok nem tesznek különbséget a blokkblobok és más adattípusok között, és nem adnak meg kapacitási adatokat ez utóbbiakhoz. Ilyen adattípusok használatakor a legutóbb számlázott mennyiségi tételek feltárásához alternatív módszert kell alkalmazni.

Azt javasoljuk, hogy az adatfelhasználás és -hozzáférés megfelelő mintájának előállításához olyan megőrzési időszakot válasszon a mérőszámhoz, amely megfelel az Ön használati szokásainak, és extrapolálja az adatokat. Az egyik lehetőség az, hogy hét napig őrzi meg a mérőszámadatokat, és minden héten összegyűjti az adatokat a hónap végén elvégzendő elemzéshez. A másik lehetőség az, hogy az utolsó 30 nap mérőszámadatait őrzi meg, és a 30 napos időszak végén hajtja végre az adatok összegyűjtését és elemzését.

A metrikaadatok engedélyezésével, gyűjtésével és megtekintésével kapcsolatos részletekért lásd: [Storage Analytics-metrikák.](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> Az elemzési adatok tárolása, elérése és letöltése ugyanúgy díjhoz kötött, mint a normál felhasználói adatok használata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Költségbecslés a használati mérőszámok alapján

#### <a name="capacity-costs"></a>Kapacitásköltségek

A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„data”* sorkulcsú utolsó bejegyzése mutatja a felhasználói adatok által igénybe vett tárolókapacitást. A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„analytics”* sorkulcsú utolsó bejegyzése mutatja az elemzési naplók által igénybe vett tárolókapacitást.

A felhasználói adatok és az elemzési naplók (ha engedélyezve vannak) által igénybe vett teljes kapacitás alapján lehet megbecsülni a tárfiókban lévő adatok tárolási költségét. Ugyanezzel a módszerrel becsülhetők meg a tárolási költségek a GPv1-tárfiókokban.

#### <a name="transaction-costs"></a>Tranzakciós költségek

A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalBillableRequests”* összege mutatja az adott API tranzakcióinak teljes számát. *Például* az adott időszakba eső *„GetBlob”* tranzakciók száma a *„user;GetBlob”* sorkulcsú bejegyzések összes számlázható kérelmének összege alapján számítható ki.

A Blob Storage-tárfiókok tranzakciós költségeinek kiszámításához a tranzakciókat három csoportra kell felosztania, mivel ezekhez különböző árak tartoznak.

* Írási tranzakciók, például *„PutBlob”*, *„PutBlock”*, *„PutBlockList”*, *„AppendBlock”*, *„ListBlobs”*, *„ListContainers”*, *„CreateContainer”*, *„SnapshotBlob”* és *„CopyBlob”*.
* Törlési tranzakciók, például *„DeleteBlob”* és *„DeleteContainer”*.
* Minden egyéb tranzakció.

A GPv1-tárfiókok tranzakciós költségeinek becsléséhez összesítenie kell az összes tranzakciót, függetlenül a művelettől és az API-tól.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Az adatok hozzáférésének és a georeplikációs adatok átvitelének költségei

Bár a tároló elemzése nem jelzi a tárfiókból olvasott és a tárfiókba írt adatok mennyiségét, a tranzakciók mérőszámának táblája alapján lehetőség van megközelítőleges becslésre. A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalIngress”* összege mutatja az adott API bejövő adatainak teljes mennyiségét bájtban. Hasonlóképpen a *„TotalEgress”* összege a kimenő adatok teljes mennyiségét mutatja bájtban.

A Blob Storage-tárfiókok adat-hozzáférési költségeinek kiszámításához a tranzakciókat két csoportra kell felosztania.

* A tárfiókból lekért adatok mennyisége a *„TotalEgress”* összegéből becsülhető meg, elsődlegesen a *„GetBlob”* és a *„CopyBlob”* művelet alapján.

* A tárfiókba írt lekért adatok mennyisége a *„TotalIngress”* összegéből becsülhető meg, elsődlegesen a *„PutBlob”*, a *„PutBlock”*, a *„CopyBlob”* és az *„AppendBlock”* művelet alapján.

A Blob Storage-tárfiókok georeplikációs adatátviteli költségei szintén az írt adatok mennyiségének becslése alapján számítható ki GRS- vagy RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> A gyakran és a gyakran használt adatok tárolási hozzáférési rétegének használatával kapcsolatos költségek kiszámítására vonatkozó részletesebb példáért olvassa el a "What are Hot and Cool access tiers and how should I determine which one to use?" (Mi a gyakori és a gyakori elérésű hozzáférési szint? című gyakori kérdéseket, és hogyan kell meghatároznom, hogy melyiket *használjam?)* az [Azure Storage díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Következő lépések

* [Tárfiókok áttekintése](storage-account-overview.md)
* [Tárfiók létrehozása](storage-account-create.md)
* [Azure Storage-fiók áthelyezése másik régióba](storage-account-move.md)
* [Törölt tárfiók helyreállítása](storage-account-recover.md)
