---
title: A Microsoft Azure Data Box Disk áttekintése | Microsoft Docs
description: Az Azure Data Box Disk felhő alapú megoldást mutatja be, amellyel nagy mennyiségű adat helyezhető át az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: ca46ce3355edf2b77400011d023cd6af330a3b58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770432"
---
# <a name="what-is-azure-data-box-disk"></a>Mi az az Azure Data Box Disk-lemez?

A Microsoft Azure Data Box Disk megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi helyszíni adatot az Azure-ba. A biztonságos adatátvitel gyorsaságáról 1-5 SSD-lemez küldésével gondoskodunk. Ezeket a 8 TB-os titkosított lemezeket a rendszer egy regionális szolgáltatón keresztül küldi el az adatközpontba.

A lemezeket gyorsan konfigurálhatja, csatlakoztathatja és feloldhatja a Data Box szolgáltatáson keresztül a Azure Portal. Az adatokat a lemezekre másolhatja, és elküldheti őket az Azure-ba. Az Azure adatközpontjában adatait a lemezekről automatikusan feltöltjük a felhőbe egy gyors magánhálózati feltöltési hivatkozást használva.

## <a name="use-cases"></a>Használati esetek

A Data Box Disk használatával több terabájtnyi adatot helyezhet át kapcsolat nélkül vagy korlátozott kapcsolattal is. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is.

- **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba. Ilyen lehet például, ha offline szalagokról archivált adatként helyezik át az adatokat az Azure ritka elérésű tárolójába.
- **Növekményes áthelyezés** – ha először tömeges adatáthelyezést végeznek a Data Box Disk használatával (kezdőérték), majd ezt követően a hálózaton keresztül növekményes adatáthelyezések történnek. Ha például a Commvault és a Data Box Disk használatával biztonsági másolatokat helyeznek át az Azure-ba. Ezt a migrálást a növekményes adatok hálózaton keresztül az Azure Storage-ba való másolása követi.
- **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül.

### <a name="ingestion-of-data-from-data-box"></a>Adatok behozása a Data Box

Az Azure-szolgáltatók és a nem Azure-szolgáltatók adatokat Azure Data Box. A következő Azure-szolgáltatások biztosítják az adatok Azure Data Box:

- **SharePoint Online** – a Azure Data Box és a SharePoint Migration Tool (SPMT) használatával migrálható a fájlmegosztás tartalma a SharePoint Online-be. A Data Box eltávolítja a WAN-kapcsolat függőségét az adatok átviteléhez. További információkért [lásd: Fájlmegosztási tartalom Azure Data Box Heavy SharePoint Online-be](data-box-heavy-migrate-spo.md)való áttelepítése a SharePoint Online-ban.

- **Azure File Sync** – fájlokat replikál a Data Box egy Azure-fájlmegosztásba, lehetővé téve, hogy központosíthatja a fájlszolgáltatásokat az Azure-ban, miközben helyi hozzáféréssel rendelkezik az adatokhoz. További információ: [Deploy Azure File Sync.](../storage/file-sync/file-sync-deployment-guide.md)

- **HDFS-tárolók** – az adatok a Hadoop-fürt helyszíni Hadoop elosztott fájlrendszer- (HDFS-) tárolóiból az Azure Storage-ba mi áttelepítése Data Box. További információ: Áttelepítés a [hdfs-tárolóból az Azure Storage-ba a Azure Data Box.](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)

- **Azure Backup** – lehetővé teszi a kritikus vállalati adatok nagy méretű biztonsági másolatainak áthelyezését offline mechanizmusokon keresztül egy Azure Recovery Services-tárolóba. További információkért lásd a Azure Backup [áttekintését.](../backup/backup-overview.md)

A saját adatait Data Box azure-beli szolgáltatókkal is használhatja. Ilyenek például a következők:

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** – lehetővé teszi nagy mennyiségű adat áttelepítését a Microsoft Azure a Azure Data Box.
- **[Veeam –](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** lehetővé teszi nagy mennyiségű adat biztonsági mentését és replikálását a Hyper-V gépről a Data Box.

A partnerrel integrálható egyéb, nem Azure-beli szolgáltatók listájáért tekintse meg a Data Box partnerekkel [Azure Data Box listáját.](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf)

## <a name="the-workflow"></a>A munkafolyamat

A munkafolyamat általában az alábbi lépésekből áll:

1. **Megrendelés** – Hozzon létre egy rendelést a Azure Portal, adja meg a szállítási adatokat és az adatok cél Azure Storage-fiókját. Ha a lemezek rendelkezésre állnak, az Azure titkosítja, előkészíti és kiszállítja a lemezeket egy szállítási nyomkövetési azonosítóval.

2. **Kézbesítés** – Ha a lemezek megérkeztek, csomagolja ki és csatlakoztassa őket ahhoz a számítógéphez, amelyen a másolandó adatok vannak. Oldja fel a lemezek zárolását.

3. **Adatok másolása** – A lemezre áthúzással másolhatja át az adatokat.

4. **Visszaküldés** – Előkészítés után küldje vissza a lemezeket az Azure-adatközpontba.

5. **Feltöltés** – A lemezekről az adatok automatikusan lesznek átmásolva az Azure-ba. A National Institute of Standards and Technology (NIST) útmutatóit követve a lemezeket biztonságosan töröljük.

A folyamat során e-mailben kap értesítést az állapotváltozásról. A folyamatot részletesebben is megismerheti az [A Data Box Disks üzembe helyezése az Azure Portalon](data-box-disk-quickstart-portal.md) című témakörben.

## <a name="benefits"></a>Előnyök

A Data Box Disk nagy mennyiségű adat Azure-ba való áthelyezésére szolgál a hálózat használata nélkül. A megoldás a következő előnyökkel jár:

- **Gyorsaság** – A Data Box Disk USB 3.0-ás kapcsolatot használ, amellyel akár 35 TB méretű adat is áthelyezhető az Azure-ba kevesebb mint egy hét alatt.

- **Egyszerű használat** – A Data Box megoldás egyszerűen használható.

  - A lemezek USB-kapcsolatot használnak, amelyhez szinte egyáltalán nem szükséges beállítás.
  - A lemezek kis méretűek, így egyszerűen kezelhetők.
  - A lemezeknek nincs szüksége külső áramellátásra.
  - A lemezek használhatóak adatközpont-kiszolgálóval, asztali számítógéppel vagy laptoppal is.
  - A megoldás teljes követést biztosít a Azure Portal.

- **Biztonságos** – A Data Box Disk beépített biztonsági védelemmel rendelkezik a lemezekhez, az adatokhoz és a szolgáltatáshoz.
  - A lemezek védve vannak a módosítások ellen, és biztonságos feltöltési funkcióval rendelkeznek.
  - A lemezeken lévő adatokat a teljes folyamat alatt AES 128 bites titkosítás védi.
  - A lemezeket csak az Azure Portalon elérhető kulcs használatával lehet feloldani.
  - A szolgáltatást az Azure biztonsági funkciói védik.
  - Miután az adatok fel lettek töltve az Azure-ba, a lemezeket az NIST 800-88r1 szabvány előírásainak megfelelően teljesen töröljük.  

További információt az [Azure Data Box biztonsága és adatvédelme](data-box-disk-security.md) című témakörben talál.

## <a name="features-and-specifications"></a>Funkciók és specifikációk

| Specifikációk                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Tömeg                                                  | < 2 lb dobozonként. Legfeljebb 5 lemez a dobozba                |
| Dimenziók                                              | Lemez – 2,5 inch SSD |
| Kábelek                                                  | 1 USB 3.1 kábel lemezenként|
| Tárolási kapacitás megrendelésenként                              | 40 TB (használható ~ 35 TB)|
| Lemezek tárkapacitása                                   | 8 TB (használható ~ 7 TB)|
| Adatinterfész                                          | USB   |
| Biztonság                                                | Előre titkosítva BitLocker és biztonságos frissítés használatával <br> Hozzáférési kulcs által védett lemezek <br> Mindig titkosított adatok  |
| Adatátviteli sebesség                                      | maximum 430 MBps a fájlmérettől függően      |
|Kezelés                                               | Azure Portal |

## <a name="region-availability"></a>Régiónkénti elérhetőség

A régiók rendelkezésre állására vonatkozó információkért a régiónként elérhető [Azure-termékek oldalon található.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Data Box Disk is üzembe helyezhetők az Azure Government Cloudban. További információ: [Mi az a Azure Government?](../azure-government/documentation-government-welcome.md).

## <a name="pricing"></a>Díjszabás

A díjszabásról további információt a Díjszabás [oldalon található.](https://azure.microsoft.com/pricing/details/databox/disk/)

## <a name="next-steps"></a>Következő lépések

- A [Data Box Disk rendszerkövetelményeinek](data-box-disk-system-requirements.md) áttekintése.
- A [Data Box Disk korlátainak](data-box-disk-limits.md) megismerése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.