---
title: Frissítés általános célú v2-tárfiókra
titleSuffix: Azure Storage
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával frissítsen az általános célú v2 Storage-fiókokra. Hozzáférési szintet határozhat meg a blob-adatértékekhez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 30c73849d07c1495251c17510ea71f29c674c618
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967489"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Frissítés általános célú v2-tárfiókra

Az általános célú v2 Storage-fiókok támogatják az Azure Storage legújabb funkcióit, és az általános célú v1-és blob Storage-fiókok összes funkcióját beépítik. Az általános célú v2-fiókok használata a legtöbb tárolási helyzetben ajánlott. Az általános célú v2-fiókok az Azure Storage-ban a legalacsonyabb/GB-os kapacitást biztosítják, valamint az iparágban versenyképes tranzakciós árakat. Az általános célú v2-fiókok támogatják az alapértelmezett fiók-hozzáférési szinteket a gyakori vagy ritka elérésű és a blob szintű, a gyakori és a ritka elérésű, illetve az archiválási szintek között.

Az általános célú, v1-es vagy blob Storage-fiókokból származó általános célú v2 Storage-fiókra való frissítés egyszerű. A frissítést a Azure Portal, a PowerShell vagy az Azure CLI használatával végezheti el. Az általános célú v2-es Storage-fiókra való frissítés során nincs leállás vagy az adatvesztés kockázata. A fiók frissítése egy egyszerű Azure Resource Manager művelettel történik, amely megváltoztatja a fiók típusát.

> [!IMPORTANT]
> Az általános célú v1-vagy blob Storage-fiókok általános célú v2-re való frissítése végleges, és nem vonható vissza.

> [!NOTE]
> Bár a Microsoft a legtöbb esetben javasolja az általános célú v2-fiókok használatát, a Microsoft továbbra is támogatja az általános célú v1-fiókokat az új és a meglévő ügyfelek számára. Általános célú v1-es Storage-fiókokat hozhat létre új régiókban, amikor az Azure Storage elérhető ezekben a régiókban. A Microsoft jelenleg nem rendelkezik az általános célú v1-fiókok támogatásának elavulttá tételével, és legalább egy éves előzetes értesítést fog biztosítani az Azure Storage funkciójának elavulttá tételéhez. A Microsoft továbbra is biztosítja az általános célú v1-fiókok biztonsági frissítéseit, de ez a fióktípus nem vár új szolgáltatás-fejlesztést.
>
> A 2020. október 1. után online állapotú új Azure-régiók esetében az általános célú v1-fiókok díjszabása megváltozott, és megfelel az általános célú v2-fiókok díjszabásának az adott régiókban. Az Azure-régiók általános célú v1-fiókjainak díjszabása, amelyek 2020 október 1. előtt léteztek, nem változtak. Az általános célú v1-fiókok egy adott régióban való díjszabásáról az Azure Storage díjszabását ismertető oldalon tájékozódhat. Válassza ki a régiót, majd a **díjszabás** mellett válassza az **egyéb** lehetőséget.

## <a name="upgrade-an-account"></a>Fiók frissítése

Általános célú v1-vagy blob Storage-fiók egy általános célú v2-fiókra történő frissítéséhez használja a Azure Portal, a PowerShell vagy az Azure CLI-t.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a tárfiókot.
3. A **Beállítások** szakaszban kattintson a **konfiguráció** elemre.
4. A **Fiók típusa** területen kattintson a **Frissítés** elemre.
5. A **Frissítés megerősítése** területen írja be a fiók nevét.
6. Kattintson a panel alján található **frissítés** gombra.

    ![Fiók frissítése](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha egy általános célú v1-fiókot szeretne egy általános célú v2-fiókra frissíteni a PowerShell használatával, először frissítse a PowerShellt, hogy az az **. Storage** modul legújabb verzióját használja. A PowerShell telepítésével kapcsolatos információkért lásd [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikket.

Ezután hívja meg a következő parancsot a fiók frissítéséhez, az erőforráscsoport nevének, a Storage-fiók nevének és a kívánt fiók hozzáférési szintjének a behelyettesítéséhez.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha egy általános célú v1-fiókot az Azure CLI használatával szeretne általános célú v2-fiókra frissíteni, először telepítse az Azure CLI legújabb verzióját. A CLI telepítésével kapcsolatban lásd [az Azure CLI 2.0-s verziójának telepítését](/cli/azure/install-azure-cli) ismertető szakaszt.

Ezután hívja meg a következő parancsot a fiók frissítéséhez, az erőforráscsoport nevének, a Storage-fiók nevének és a kívánt fiók hozzáférési szintjének a behelyettesítéséhez.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Hozzáférési szintek megadása a blob-adatértékekhez

Az általános célú v2-fiókok támogatják az összes Azure Storage-szolgáltatást és-adatobjektumot, a hozzáférési szintek azonban csak a blob Storage-ban lévő Blobok blokkolására használhatók. Egy általános célú v2-es Storage-fiókra való frissítéskor megadhat egy alapértelmezett fiók-hozzáférési szintet, amely az alapértelmezett szint, a blob-adatok feltöltése, ha nincs megadva az egyéni blob-hozzáférési réteg paraméter.

A blob hozzáférési szintjei lehetővé teszik a leginkább költséghatékony tárterület kiválasztását a várt használati minták alapján. A blokkos Blobok a gyakori, ritka vagy archív szinteken tárolhatók. A hozzáférési szintekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

Alapértelmezés szerint a rendszer létrehoz egy új Storage-fiókot a gyors elérési szinten, és egy általános célú v1-es Storage-fiókot is frissít a gyakori vagy a ritkán használt fiók szintjére. Ha nincs megadva fiók-hozzáférési szint a Verziófrissítéskor, a rendszer alapértelmezés szerint a frissítésre frissíti. Ha vizsgálja meg, hogy melyik hozzáférési szintet szeretné használni a frissítéshez, vegye figyelembe az aktuális adatfelhasználási forgatókönyvet. Az általános célú v2-fiókokba való áttelepítéshez két tipikus felhasználói forgatókönyv van:

* Rendelkezik egy meglévő általános célú v1 Storage-fiókkal, és szeretné kiértékelni az általános célú v2 Storage-fiókra való frissítést, a blob-adatelérési réteg megfelelő tárolási hozzáférési szintjével.
* Úgy döntött, hogy egy általános célú v2-es Storage-fiókot használ, vagy már rendelkezik ilyennel, és szeretné kiértékelni, hogy érdemes-e a gyakori vagy ritka elérésű tárolási hozzáférési szintet használni a blob-adatokhoz.

Mindkét esetben az első prioritás az általános célú v2 Storage-fiókban tárolt adatok tárolási, elérési és üzemeltetési költségeinek becslése, valamint az aktuális költségekkel való összehasonlítás.

## <a name="pricing-and-billing"></a>Árak és számlázás

Egy v1-es Storage-fiók egy általános célú v2-fiókra való frissítése ingyenes. A frissítési folyamat során megadhatja a kívánt fiók szintjét. Ha nincs megadva a fiók szintje a Verziófrissítéskor, a frissített fiók alapértelmezett fiókjának szintje a következő lesz: `Hot` . Ha azonban a frissítés után módosítja a tárolási hozzáférési szintet, akkor a számla módosítása is előfordulhat, hogy a frissítés során javasolt az új fiók rétegének megadására.

Az összes tárfiók az egyes blobok szintjén alapuló árképzési modellt alkalmaz a blobtároláshoz. Tárfiókok használatakor az alábbi számlázási szempontok érvényesülnek:

* **Tárolási költségek**: a tárolt adatok mennyisége mellett az adattárolás költsége a tárolási hozzáférési szintjétől függően változhat. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.

* **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A lassú elérésű és az archív tároló hozzáférési rétegében lévő adatok esetében a GB-nál több adatelérési díjat számítunk fel a beolvasáshoz.

* **Tranzakciós költségek**: Minden szint esetében tranzakciónkénti díjat kell fizetni, ez emelkedik, ha a szint ritkábban használt adatokat tárol.

* **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.

* **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.

* **A tárolási hozzáférési szint módosítása**: a fiók tárolási hozzáférési rétegének a lassúról a gyors elérésű értékre való módosítása a Storage-fiókban meglévő összes információ olvasásával egyenlő. Azonban a fiókhoz való hozzáférési szint gyors és lassú elérésű értékre való módosítása az összes adatoknak a ritka rétegbe való írásával egyenlő (csak GPv2-fiókok esetében).

> [!NOTE]
> A tárfiókok árképzési modelljével kapcsolatos további információért lásd [az Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>A jelenlegi használati minták költségeinek becslése

Egy adott réteg általános célú v2-es Storage-fiókjában lévő blob-adatok tárolási és elérési költségeinek megbecsléséhez értékelje ki a meglévő használati mintát, vagy közelítse meg a várt használati mintát. Általában a következőket érdemes figyelembe venni:

* A blob Storage-felhasználás (GB), beleértve a következőket:
  * Mennyi adatot tárol a tárfiókjában?
  * Havi lebontásban hogyan változik az adatmennyiség? Az új adatok folyamatosan átveszik a korábbi adatok helyét?

* A blob Storage-adatai elsődleges hozzáférési mintája, beleértve a következőket:
  * Mennyibe kerül az adatok olvasása és írása a Storage-fiókba?
  * Hány olvasási művelet és írási művelet történik a Storage-fiókban lévő adatokon?

Annak érdekében, hogy az igényeinek megfelelő hozzáférési szintet döntse el, hasznos lehet a blob-adatkapacitás meghatározása, valamint az adathasználatuk módja. Ez a fiók figyelési metrikáinak megtekintésével végezhető el legjobban.

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

A metrikák adatainak engedélyezésével, gyűjtésével és megtekintésével kapcsolatos részletekért lásd a [Storage Analytics mérőszámait](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Az elemzési adatok tárolása, elérése és letöltése ugyanúgy díjhoz kötött, mint a normál felhasználói adatok használata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Költségbecslés a használati mérőszámok alapján

#### <a name="capacity-costs"></a>Kapacitási költségek

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
> A gyakori és ritka elérésű tárolási hozzáférési szint használatának költségeinek kiszámításához tekintse meg a *"mi a gyors és a lassú elérési szint, és Hogyan határozható meg, hogy melyiket érdemes használni?"* című részt. az [Azure Storage díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Következő lépések

* [Tárfiókok áttekintése](storage-account-overview.md)
* [Tárfiók létrehozása](storage-account-create.md)
* [Azure Storage-fiók áthelyezése másik régióba](storage-account-move.md)
* [Törölt tárfiók helyreállítása](storage-account-recover.md)
