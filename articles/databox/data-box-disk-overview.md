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
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: ea9353ed8c1938fa9b33585a0650b4507c671451
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92125030"
---
# <a name="what-is-azure-data-box-disk"></a>Mi az az Azure Data Box Disk-lemez?

A Microsoft Azure Data Box Disk megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi helyszíni adatot az Azure-ba. A biztonságos adatátvitel gyorsaságáról 1-5 SSD-lemez küldésével gondoskodunk. A 8 TB-os titkosított lemezeket egy regionális szolgáltatón keresztül küldik el az adatközpontnak.

A lemezek gyors konfigurálását, összekapcsolását és zárolását a Azure Portal Data Box szolgáltatásán keresztül végezheti el. Az adatokat a lemezekre másolhatja, és elküldheti őket az Azure-ba. Az Azure adatközpontjában adatait a lemezekről automatikusan feltöltjük a felhőbe egy gyors magánhálózati feltöltési hivatkozást használva.

## <a name="use-cases"></a>Használati esetek

A Data Box Disk használatával több terabájtnyi adatot helyezhet át kapcsolat nélkül vagy korlátozott kapcsolattal is. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is.

- **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba. Ilyen lehet például, ha offline szalagokról archivált adatként helyezik át az adatokat az Azure ritka elérésű tárolójába.
- **Növekményes áthelyezés** – ha először tömeges adatáthelyezést végeznek a Data Box Disk használatával (kezdőérték), majd ezt követően a hálózaton keresztül növekményes adatáthelyezések történnek. Ha például a Commvault és a Data Box Disk használatával biztonsági másolatokat helyeznek át az Azure-ba. Az áttelepítés után a növekményes adatok másolása a hálózatról az Azure Storage-ba történik.
- **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül.

### <a name="ingestion-of-data-from-data-box"></a>Adatok betöltése Data Boxról

Az Azure-szolgáltatók és a nem Azure-szolgáltatók a Azure Data Boxtól származó adatok betöltésére képesek. A Azure Data Box adatfeldolgozást biztosító Azure-szolgáltatások a következők:

- **SharePoint Online** – használja a Azure Data Box és a SharePoint áttelepítési eszközt (SPMT) a fájlmegosztás tartalmának a SharePoint Online-ba való áttelepítéséhez. A Data Box használatával eltávolíthatja a WAN-kapcsolat függőségét az adatok átviteléhez. További információ: [a fájlmegosztás tartalmának áttelepítésére szolgáló Azure Data Box Heavy használata a SharePoint Online-ba](data-box-heavy-migrate-spo.md).

- **Azure file Sync** – replikálja a fájlokat a Data Box egy Azure-fájlmegosztást, lehetővé téve az Azure-beli Fájlszolgáltatások központosítását az adatok helyi elérésének fenntartása mellett. További információ: [Deploy Azure file Sync](../storage/files/storage-sync-files-deployment-guide.md).

- **HDFS** -tárolók – az adatok áttelepíthetők egy helyszíni Hadoop ELOSZTOTT fájlrendszer (HDFS) Hadoop-fürtről az Azure Storage-ba a Data Box használatával. További információ: [áttelepítés helyszíni HDFS áruházból az Azure Storage-ba a Azure Data Box használatával](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Azure Backup** – lehetővé teszi a kritikus vállalati adatokat használó nagyméretű biztonsági másolatok áthelyezését az Azure Recovery Services-tárolón keresztül offline mechanizmusokkal. További információ: [Azure Backup Overview (áttekintés](../backup/backup-overview.md)).

A Data Box-adatait számos nem Azure-szolgáltatóval is használhatja. Ilyenek például a következők:

- **[CommVault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** – lehetővé teszi, hogy nagy mennyiségű adatmennyiséget telepítsen át Microsoft Azure a Azure Data Box használatával.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** – lehetővé teszi nagy mennyiségű adatok biztonsági mentését és replikálását a Hyper-V-gépről a Data boxba.

Az Data Box-nal integrálható, nem Azure-beli szolgáltatók listáját itt tekintheti meg: [Azure Data Box Partners](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>A munkafolyamat

A munkafolyamat általában az alábbi lépésekből áll:

1. **Rendelés** – hozzon létre egy rendelést a Azure Portalban, adja meg a szállítási adatokat, valamint a cél Azure Storage-fiókot az adatokhoz. Ha a lemezek rendelkezésre állnak, az Azure titkosítja, előkészíti és kiszállítja a lemezeket egy szállítási nyomkövetési azonosítóval.

2. **Kézbesítés** – Ha a lemezek megérkeztek, csomagolja ki és csatlakoztassa őket ahhoz a számítógéphez, amelyen a másolandó adatok vannak. Oldja fel a lemezek zárolását.

3. **Adatok másolása** – A lemezre áthúzással másolhatja át az adatokat.

4. **Visszaküldés** – Előkészítés után küldje vissza a lemezeket az Azure-adatközpontba.

5. **Feltöltés** – A lemezekről az adatok automatikusan lesznek átmásolva az Azure-ba. A National Institute of Standards and Technology (NIST) útmutatóit követve a lemezeket biztonságosan töröljük.

A folyamat során e-mailben értesítjük az összes állapotadatok változásáról. A folyamatot részletesebben is megismerheti az [A Data Box Disks üzembe helyezése az Azure Portalon](data-box-disk-quickstart-portal.md) című témakörben.

## <a name="benefits"></a>Előnyök

A Data Box Disk nagy mennyiségű adat Azure-ba való áthelyezésére szolgál a hálózat használata nélkül. A megoldás a következő előnyökkel jár:

- **Gyorsaság** – A Data Box Disk USB 3.0-ás kapcsolatot használ, amellyel akár 35 TB méretű adat is áthelyezhető az Azure-ba kevesebb mint egy hét alatt.

- **Egyszerű használat** – A Data Box megoldás egyszerűen használható.

  - A lemezek USB-kapcsolatot használnak, amelyhez szinte egyáltalán nem szükséges beállítás.
  - A lemezek kis méretűek, így egyszerűen kezelhetők.
  - A lemezeknek nincs szüksége külső áramellátásra.
  - A lemezek használhatóak adatközpont-kiszolgálóval, asztali számítógéppel vagy laptoppal is.
  - A megoldás teljes körű nyomon követést tesz lehetővé a Azure Portal használatával.

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
| Tömeg                                                  | < 2 lb dobozonként. Legfeljebb 5 lemez a dobozban                |
| Dimenziók                                              | Lemez – 2,5 inch SSD |
| Kábelek                                                  | 1 USB 3.1 kábel lemezenként|
| Tárolási kapacitás megrendelésenként                              | 40 TB (használható ~ 35 TB)|
| Lemezek tárkapacitása                                   | 8 TB (használható ~ 7 TB)|
| Adatinterfész                                          | USB   |
| Biztonság                                                | Előre titkosítva BitLocker és biztonságos frissítés használatával <br> Hozzáférési kulcs által védett lemezek <br> Mindig titkosított adatok  |
| Adatátviteli sebesség                                      | maximum 430 MBps a fájlmérettől függően      |
|Kezelés                                               | Azure Portal |

## <a name="region-availability"></a>Régiónkénti elérhetőség

A régió rendelkezésre állásával kapcsolatos információkért tekintse meg a [régiók által elérhető Azure-termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Disk a Azure Government felhőben is üzembe helyezhető. További információ: [Mi az Azure Government?](../azure-government/documentation-government-welcome.md)

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lépjen a [díjszabás oldalra](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Következő lépések

- A [Data Box Disk rendszerkövetelményeinek](data-box-disk-system-requirements.md) áttekintése.
- A [Data Box Disk korlátainak](data-box-disk-limits.md) megismerése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.