---
title: Többcsatornás SMB-teljesítmény – Azure Files
description: A többcsatornás SMB teljesítményének megismerése.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374983"
---
# <a name="smb-multichannel-performance"></a>Többcsatornás SMB teljesítménye

A többcsatornás SMB (előzetes verzió) Azure Files lehetővé teszi, hogy egy SMB 3. x ügyfél több hálózati kapcsolatot hozzon létre a prémium fájlmegosztás FileStorage-fiókban való létrehozásához. Az SMB 3,0 protokoll bevezette a többcsatornás SMB szolgáltatást a Windows Server 2012 és a Windows 8 rendszerű ügyfeleken. Emiatt a többcsatornás SMB-t támogató Azure Files SMB 3. x ügyfél kihasználhatja az Azure Premium-fájlmegosztás funkciójának előnyeit. A többcsatornás SMB engedélyezése a Storage-fiókokban nem jár további díjszabással.

## <a name="benefits"></a>Előnyök

Azure Files többcsatornás SMB lehetővé teszi, hogy az ügyfelek több hálózati kapcsolatot használjanak, amelyek nagyobb teljesítményt biztosítanak, miközben csökkentik a tulajdonosi költségeket. A nagyobb teljesítmény a több hálózati adapteren keresztüli sávszélesség-összesítésen keresztül érhető el, és a hálózati adapterek fogadási oldali skálázási (RSS) támogatása lehetővé teszi, hogy az i/o-terhelést több processzor között

- **Megnövekedett átviteli sebesség**: több kapcsolat lehetővé teszi, hogy az adatok egyszerre több útvonalon legyenek átvihetők, és ezáltal jelentős mértékben kihasználják a nagyobb méretű i/o-méretet használó nagyobb fájlméreteket, és magas átviteli sebességet igényelnek egyetlen virtuális gépről vagy kisebb virtuális gépekről. Ilyen számítási feladatok közé tartoznak például a média és a szórakozás a tartalmak létrehozásához, a kódoláshoz, a genomikahez és a pénzügyi szolgáltatások kockázati elemzéséhez.
- **Magasabb IOPS**: a hálózati ADAPTERek RSS-funkciója lehetővé teszi a hatékony terheléselosztást több processzor között több kapcsolattal. Ez segít a virtuálisgép-processzorok nagyobb IOPS és hatékony kihasználtságának megvalósításában. Ez olyan munkaterhelések esetében hasznos, amelyek kis i/o-méretekkel rendelkeznek, például adatbázis-alkalmazásokhoz.
- **Hálózati hibatűrés**: több kapcsolat csökkenti a megszakadási kockázatot, mivel az ügyfelek többé nem támaszkodhatnak az egyes kapcsolatokra.
- **Automatikus konfiguráció**: Ha a többcsatornás SMB engedélyezve van az ügyfeleken és a Storage-fiókokon, lehetővé teszi a meglévő kapcsolatok dinamikus felderítését, és szükség esetén létrehozhat további csatlakozási útvonalakat is.
- **Költségtakarékos**: a számítási feladatok a prémium szintű megosztásokhoz való csatlakozás során nagyobb léptéket érhetnek el egyetlen virtuális gépről vagy egy kis virtuális gépre. Ez csökkentheti a teljes tulajdonlási költségeket azáltal, hogy csökkenti a számítási feladatok futtatásához és kezeléséhez szükséges virtuális gépek számát.

A többcsatornás SMB-vel kapcsolatos további tudnivalókért tekintse meg a [Windows dokumentációját](/azure-stack/hci/manage/manage-smb-multichannel).

Ez a funkció nagyobb teljesítménybeli előnyöket biztosít a több szálon futó alkalmazások számára, de általában nem segíti az egyszálas alkalmazásokat. További részletekért tekintse meg a [teljesítmény-összehasonlítás](#performance-comparison) szakaszt.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Konfiguráció

A többcsatornás SMB csak akkor működik, ha a szolgáltatás engedélyezve van az ügyféloldali (az ügyfélen) és a szolgáltatás-oldali (Azure Storage-fiók).

A Windows-ügyfeleken a többcsatornás SMB alapértelmezés szerint engedélyezve van. A konfigurációt a következő PowerShell-parancs futtatásával ellenőrizheti: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Az Azure Storage-fiókjában engedélyeznie kell a többcsatornás SMB-t. Lásd: [többcsatornás SMB engedélyezése (előzetes verzió)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Többcsatornás SMB letiltása
A legtöbb forgatókönyvben, különösen a többszálas számítási feladatokban az ügyfeleknek jobb teljesítményt kell látniuk a többcsatornás SMB-vel. Bizonyos speciális forgatókönyvek, például az egyszálas munkaterhelések vagy a tesztelési célból előfordulhat, hogy le szeretné tiltani a többcsatornás SMB-t. További részletekért tekintse meg a [teljesítmény-összehasonlítást](#performance-comparison) .

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Ellenőrizze, hogy a többcsatornás SMB megfelelően van-e konfigurálva

1. Hozzon létre egy prémium szintű fájlmegosztást, vagy használjon egy meglévőt.
1. Győződjön meg arról, hogy az ügyfél támogatja a többcsatornás SMB használatát (egy vagy több hálózati adapternél engedélyezve van a fogadó oldali skálázás). További részletekért tekintse meg a [Windows dokumentációját](/azure-stack/hci/manage/manage-smb-multichannel) .
1. Fájlmegosztás csatlakoztatása az ügyfélhez.
1. A terhelést az alkalmazással hozhatja meg.
    Az olyan másolási eszköz, mint például a Robocopy/MT, vagy bármely olyan teljesítményű eszköz, mint például a Diskspd az olvasási/írási fájlok esetében, terhelést hozhat.
1. Nyissa meg a PowerShellt rendszergazdaként, és használja a következő parancsot: `Get-SmbMultichannelConnection |fl`
1. A **MaxChannels** és a **CurrentChannels** tulajdonságainak megkeresése

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Képernyőkép a Get-SMBMultichannelConnection eredményeiről." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Teljesítmény-összehasonlítás

Az olvasási/írási munkaterhelés két kategóriája van – egyszálas és többszálú. A legtöbb számítási feladat több fájlt használ, de lehetnek olyan konkrét használati esetek, amikor a munkaterhelés egyetlen fájllal működik egy megosztásban. Ez a szakasz a különböző használati esetekre és a teljesítményre gyakorolt hatására vonatkozik. Általánosságban elmondható, hogy a legtöbb számítási feladat több szálból áll, és több fájlon terjeszti a munkaterhelést, így a többcsatornás SMB-vel kapcsolatos jelentős teljesítménybeli Újdonságok figyelhetők.

- Több **szálon futó/több fájl**: a számítási feladatok mintája alapján jelentős teljesítmény-javulást kell látni az iOS és a több csatornán keresztül történő írási és olvasási műveletek során. A teljesítmény nyeresége a IOPS, az átviteli sebesség és a késés tekintetében a 2 – 4 – 4x közötti távolságtól függetlenül változhat. Ehhez a kategóriához engedélyezni kell a többcsatornás SMB-t a legjobb teljesítmény érdekében.
- **Többszálas/egyetlen fájl**: ebben a kategóriában a legtöbb felhasználási esethez a munkaterhelések lehetővé teszik a többcsatornás SMB használatát, különösen akkor, ha a számítási feladat átlagos i/o-mérettel rendelkezik > ~ 16k. Néhány példa arra, hogy a többcsatornás SMB kihasználja az egyetlen nagyméretű fájl biztonsági mentését vagy helyreállítását. Kivételt jelent, ha a többcsatornás SMB letiltására van szükség, ha a számítási feladat kisméretű IOs-nagy. Ebben az esetben előfordulhat, hogy egy kisebb, 10%-os teljesítménybeli csökkenést tapasztal. A használati esettől függően érdemes lehet több fájlon kiterjeszteni a terhelést, vagy letiltani a funkciót. A részletekért tekintse meg a [konfiguráció](#configuration) szakaszt.
- **Egyszálas/több fájl vagy egyetlen fájl**: a legtöbb egyszálas számítási feladathoz a párhuzamosság hiánya miatt minimális teljesítménybeli előnyök tartoznak, általában a teljesítmény csökkenése 10%, ha engedélyezve van a többcsatornás SMB. Ebben az esetben ideális a többcsatornás SMB letiltására, egyetlen kivételsel. Ha az egyszálas munkaterhelés több fájl között is terjesztheti a terhelést, és egy átlagosan nagyobb i/o-méretet használ (> ~ 16k), akkor a többcsatornás SMB-nek kisebb teljesítménybeli előnyökkel kell rendelkeznie.

### <a name="performance-test-configuration"></a>Teljesítményteszt konfigurálása

A cikkben szereplő diagramok esetében a következő konfigurációt használták: egyetlen standard D32s v3 virtuális gép egyetlen RSS-kompatibilis hálózati adapterrel, négy csatornával. A Load a diskspd.exe használatával lett létrehozva, és több szálat is ellátott a 10-es i/o-mélységgel, a véletlenszerű IOs-t pedig különböző IO-méretekkel

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="A teljesítmény-tesztelési konfigurációt bemutató képernyőkép." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Megtalálhatjuk – többszálú/több fájl többcsatornás SMB-vel

A Load a különböző IO-méretekkel rendelkező 10 fájlra lett létrehozva. A vertikális Felskálázási teszt eredményei jelentős fejlesztéseket mutattak a IOPS és az átviteli sebesség tesztelési eredményeiben a többcsatornás SMB engedélyezve. A következő diagramok az eredményeket ábrázolják:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Teljesítmény diagramja" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Adatátviteli teljesítmény diagramja" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Egyetlen hálózati adapteren, olvasáskor a 2 – 3. teljesítménybeli növekedés megfigyelhető, és írás esetén a IOPS és az átviteli sebesség tekintetében a 3x-4x-es nyereség.
- A többcsatornás SMB lehetővé tette a IOPS és az átviteli sebesség elérését, hogy egyetlen hálózati adapterrel és a négy csatornás korláttal is elérjék a virtuális gépek
- Mivel a kimenő forgalom (vagy a tárterületre való beolvasás) nem mért, az olvasási sebesség túllépte a virtuális gép 16 000 Mbps-os korlátját (2 GiB/s). A teszt elért >2,7 GiB/s. A bejövő forgalom (vagy a tárolásba való írás) továbbra is a virtuális gépek korlátaira vonatkozik.
- A terhelés több fájlra is kiterjeszthető, ami jelentős mértékű tökéletesítéseket tesz lehetővé.

A tesztelés során használt példa a következő: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Többszálas/Egyfájlos munkaterhelések többcsatornás SMB-vel

A terhelés egyetlen 128 GiB-fájlhoz lett létrehozva. Ha engedélyezve van a többcsatornás SMB, a többszálas/egyetlen fájlokkal történő vertikális Felskálázási teszt a legtöbb esetben a fejlesztést mutatja. A következő diagramok az eredményeket ábrázolják:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="A IOPS teljesítményének ábrája." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Egy fájl átviteli teljesítményének diagramja" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Egy nagyobb átlagos IO-mérettel (> ~ 16k) rendelkező hálózati adapteren az olvasási és írási műveletek terén jelentős változások történtek.
- Kisebb i/o-méret esetén a teljesítmény kis mértékben befolyásolta a ~ 10%-ot, ha a többcsatornás SMB engedélyezve volt. Ez enyhíthető a több fájl terhelésének elosztásával vagy a szolgáltatás letiltásával.
- A teljesítmény továbbra is az [egyetlen fájl korlátaihoz](storage-files-scale-targets.md#file-scale-targets)kötődik.

## <a name="optimizing-performance"></a>Teljesítmény optimalizálása

A következő tippek segíthetnek a teljesítmény optimalizálásában:

- Győződjön meg arról, hogy a Storage-fiók és az ügyfél ugyanabban az Azure-régióban található, hogy csökkentse a hálózati késést.
- Több szálon futó alkalmazások használata és az elosztott terhelés több fájl között.
- A többcsatornás SMB teljesítménybeli előnyei a terhelést elosztó fájlok számával növekednek.
- A prémium szintű megosztási teljesítményt a kiosztott megosztási méret (IOPS/kimenő/bejövő forgalom) és az egyetlen fájl korlátai kötik. Részletekért lásd a [prémium szintű fájlmegosztás kiépítés ismertetése](understanding-billing.md#provisioned-model)című témakört.
- Egyetlen virtuálisgép-ügyfél maximális teljesítménye továbbra is a virtuális gépek korlátaihoz kötődik. Például a [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) legfeljebb 16 000 MB/s (vagy 2GBps) sávszélességet tud támogatni, a virtuális gépről érkező kimenő forgalom (a tárolóba való írás), a bejövő forgalom (a tárolóból való olvasás) nem. A fájlmegosztás teljesítményét a számítógép hálózati korlátai, a processzorok, a belső tárterület rendelkezésre álló hálózati sávszélesség, az IO-méretek, a párhuzamosságok és egyéb tényezők határozzák meg.
- A kezdeti teszt általában bemelegítő, elveti az eredményeket, és megismétli a tesztet.
- Ha a teljesítményt egyetlen ügyfél korlátozza, és a munkaterhelés még nem éri el a kiosztott megosztási korlátokat, a nagyobb teljesítmény a több ügyfél terhelésének elosztásával is megvalósítható.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>A IOPS, az átviteli sebesség és az i/o-méretek közötti kapcsolat

**Teljesítmény = IO-méret * IOPS**

A magasabb i/o-méretek nagyobb átviteli sebességet eredményeznek, és nagyobb késéssel fognak rendelkezni, ami alacsonyabb számú nettó IOPS eredményez. A kisebb i/o-méretek magasabb IOPS fognak vezetni, de alacsonyabb nettó átviteli sebességet és késést eredményeznek.

## <a name="next-steps"></a>Következő lépések

- [Többcsatornás SMB engedélyezése FileStorage-fiókban (előzetes verzió)](storage-files-enable-smb-multichannel.md)
- A többcsatornás SMB-vel kapcsolatos további információkért tekintse meg a [Windows dokumentációját](/azure-stack/hci/manage/manage-smb-multichannel) .
