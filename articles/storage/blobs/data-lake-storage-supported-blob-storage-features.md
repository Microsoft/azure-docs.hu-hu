---
title: A blob Storage funkciói elérhetők a Azure Data Lake Storage Gen2ban | Microsoft Docs
description: Ismerje meg, hogy mely blob Storage-funkciókat használhatja a Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 769163dd09ec29c2c206d0273e71de27732fb9db
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516278"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>A blob Storage funkciói a Azure Data Lake Storage Gen2ban érhetők el

A [diagnosztikai naplózást](../common/storage-analytics-logging.md), a [hozzáférési szinteket](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési házirendeket](storage-lifecycle-management-concepts.md) blob Storage funkciók mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a funkciókhoz hozzáférjenek.

Ez a táblázat a blob Storage azon funkcióit sorolja fel, amelyeket a Azure Data Lake Storage Gen2 használhat. A táblázatokban megjelenő elemek idővel változnak, ahogy a támogatás továbbra is bővül. Ha többet szeretne megtudni a szolgáltatás előzetes verziójának állapotával kapcsolatos problémákról, tekintse meg az [ismert problémákról](data-lake-storage-known-issues.md) szóló cikket.

## <a name="supported-blob-storage-features"></a>Támogatott Blob Storage-funkciók

A következő táblázat azt mutatja be, hogy az egyes Blobok tárolási funkciói hogyan támogatottak a Data Lake Storage Gen2. Van egy oszlop a standard és a [prémium szintű teljesítményhez](premium-tier-for-data-lake-storage.md) . 

|Blob Storage funkció |Standard |Prémium |Kapcsolódó cikkek |
|---------------|-------------------|---|
|Gyakori elérésű tárolási szint|Általánosan elérhető|Nem támogatott|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Ritka elérésű tárolási szint|Általánosan elérhető|Nem támogatott|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Események|Általánosan elérhető|Általánosan elérhető|[Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)|
|Metrikák (klasszikus)|Általánosan elérhető|Általánosan elérhető|[Azure Storage Analytics-metrikák (klasszikus)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrikák az Azure Monitorban|Általánosan elérhető|Előnézet|[Azure Storage-metrikák az Azure Monitorban](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB Storage PowerShell-parancsok|Általánosan elérhető|Általánosan elérhető|[Rövid útmutató: Blobok feltöltése, letöltése és listázása a PowerShell-lel](storage-quickstart-blobs-powershell.md)|
|BLOB Storage – Azure CLI-parancsok|Általánosan elérhető|Általánosan elérhető|[Gyors útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](storage-quickstart-blobs-cli.md)|
|BLOB Storage API-k|Általánosan elérhető|Általánosan elérhető|[Rövid útmutató: Az Azure Blob Storage .NET-hez készült ügyféloldali kódtára (v12)](storage-quickstart-blobs-dotnet.md)<br>[Gyors útmutató: Blobok kezelése a Java V12 SDK-val](storage-quickstart-blobs-java.md)<br>[Gyors útmutató: Blobok kezelése a Python V12 SDK-val](storage-quickstart-blobs-python.md)<br>[Gyors útmutató: Blobok kezelése a JavaScript V12 SDK-val Node.js](storage-quickstart-blobs-nodejs.md)|
|Diagnosztikai naplók|Általánosan elérhető|Előnézet |[Azure Storage Analytics-naplózás](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Archív hozzáférési szint|Általánosan elérhető|Nem támogatott|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Életciklus-kezelési szabályzatok (rétegek)|Általánosan elérhető|Még nem támogatott|[Az Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)|
|Életciklus-kezelési szabályzatok (blob törlése)|Általánosan elérhető|Általánosan elérhető|[Az Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)|
|Bejelentkezés Azure Monitor|Előnézet |Előnézet|[Az Azure Storage figyelése](./monitor-blob-storage.md)|
|Pillanatképek|Előnézet<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Előnézet<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[BLOB-Pillanatképek](snapshots-overview.md)|
|Statikus webhelyek|Előnézet<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Előnézet<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Statikus webhely üzemeltetés az Azure Storage-ban](storage-blob-static-website.md)|
|Megváltoztathatatlan tároló|Előnézet<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Előnézet<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Az üzlet szempontjából létfontosságú blobadatok tárolása nem módosítható tárolással](storage-blob-immutable-storage.md)|
|Tároló – Soft delete|Előnézet|Előnézet|[Tárolók törlése (előzetes verzió)](soft-delete-container-overview.md)|
|Azure Storage-leltár|Előnézet|Előnézet|[Az Azure Storage-leltár használata a blob-adatkezeléshez (előzetes verzió)](blob-inventory.md)|
|Egyéni tartományok|Előnézet<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|Előnézet<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[Egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md)|
|Blobos törlés|Még nem támogatott|Még nem támogatott|[Blobok helyreállítható törlése](./soft-delete-blob-overview.md)|
|Blobfuse|Általánosan elérhető|Általánosan elérhető|[BLOB Storage csatlakoztatása fájlrendszerként a blobfuse-mel](storage-how-to-mount-container-linux.md)|
|Névtelen nyilvános hozzáférés |Általánosan elérhető|Általánosan elérhető| Lásd: [Névtelen nyilvános olvasási hozzáférés beállítása tárolók és Blobok számára](anonymous-read-access-configure.md).|
|Ügyfél által felügyelt fiók feladatátvétele|Még nem támogatott|Még nem támogatott|[Vész-helyreállítási és-fiók feladatátvétele](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Ügyfél által biztosított kulcsok|Még nem támogatott|Még nem támogatott|[Titkosítási kulcs megadása a blob Storage-kérelemben](encryption-customer-provided-keys.md)|
|Titkosítási hatókörök|Még nem támogatott|Még nem támogatott|[Titkosítási hatókörök létrehozása és kezelése (előzetes verzió)](encryption-scope-manage.md)|
|Csatorna módosítása|Még nem támogatott|Még nem támogatott|[A hírcsatornák támogatásának módosítása az Azure Blob Storage-ban](storage-blob-change-feed.md)|
|Objektumreplikáció|Még nem támogatott|Még nem támogatott|[Objektumok replikálásának konfigurálása a blokkos blobokhoz](object-replication-configure.md)|
|BLOB verziószámozása|Még nem támogatott|Még nem támogatott|[BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)|

<div id="preview-form"><sup>1</sup> Ha a pillanatképeket, a nem módosítható tárolókat vagy a statikus webhelyeket Data Lake Storage Gen2 használatával szeretné használni, regisztrálnia kell az előzetes verzióban az <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>űrlap</a>kitöltésével.  </div>
<div id="preview-form-2"><sup>2</sup> Az Egyéni tartománynév csak a blob szolgáltatás vagy a statikus webhely végpontja számára képezhető le. A Data Lake tárolási végpontja nem </a> támogatott.  </div>

## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)
- [A Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)