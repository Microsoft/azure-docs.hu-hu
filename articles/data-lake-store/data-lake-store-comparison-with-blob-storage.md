---
title: Az Azure Storage-Blobból az Azure Data Lake Storage Gen1 összehasonlítása |} A Microsoft Docs
description: Az Azure Storage-Blobból az Azure Data Lake Storage Gen1 összehasonlítása
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 478c261bb909cbc931a7dbbaa9cb6c61152970e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878970"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Az Azure Data Lake Storage Gen1 és az Azure Blob Storage összehasonlítása

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

Ebben a cikkben a táblázat néhány fontos szempontjai big data-feldolgozáshoz mentén az Azure Data Lake Storage Gen1 és az Azure Blob Storage közötti különbségeket foglalja össze. Az Azure Blob Storage egy általános célú, skálázható objektumtárolását, amely számos különböző adattárolási forgatókönyvekhez készült. Az Azure Data Lake Storage Gen1 egy nagy kapacitású adattár, amely a big data-elemzési számítási feladatokhoz van optimalizálva.

|  | 1. generációs Azure Data Lake Storage | Azure Blob Storage |
| --- | --- | --- |
| Cél |A big data-elemzési célokra tárolás |Általános célú objektumtároló a számos különböző storage-forgatókönyvek, beleértve a big data-elemzés |
| Használati példák |Kötegelt, interaktív, analitikai és gép tanulási streamelési például naplófájlok, IoT-adatokat, kattintson a adatfolyamok, nagy méretű adatkészletek |Bármilyen típusú szöveget vagy bináris adatot, például az alkalmazás biztonsági célból, biztonsági mentési adatokat, a streamelési és az általános célú adatok médiatárolónkba. Emellett teljes körű támogatást adatelemzési feladatokhoz; kötegelt, interaktív, analitikai és gép tanulási streamelési például naplófájlok, IoT-adatokat, kattintson a adatfolyamok, nagy méretű adatkészletek |
| Fő fogalmak |A Data Lake Storage Gen1 fiók mappával, viszont tartalmazó fájlként tárolt adatok |Storage-fiók rendelkezik tárolókkal, melynek cserébe megvan a blobok formájában adatok |
| struktúra |Hierarchikus fájlrendszer |Objektumtároló strukturálatlan névtér esetében |
| API |HTTPS-kapcsolaton keresztül a REST API |REST API HTTP/HTTPS-kapcsolaton keresztül |
| Kiszolgálóoldali API |[WebHDFS-kompatibilis REST API-val](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Az Azure Blob Storage REST API-val](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop-fájl rendszer ügyfél |Igen |Igen |
| Adatműveletek – hitelesítés |Alapján [az Azure Active Directory-identitásokkal](../active-directory/develop/authentication-scenarios.md) |A közös titkos kulcsot – alapján [hozzáférési kulcsainak](../storage/common/storage-account-manage.md#access-keys) és [megosztott hozzáférési aláírást kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Adatműveletek – hitelesítési protokoll |OAuth 2.0. Hívások tartalmaznia kell egy érvényes JWT (JSON Web Token) Azure Active Directory által kiadott |Üzenet kivonat-alapú hitelesítési kód (HMAC). Hívások a Base64-kódolású SHA-256 kivonatoló tartalmaznia kell a HTTP-kérelem egy része felett. |
| Adatműveletek – engedélyezés |A POSIX hozzáférés-vezérlési listák (ACL).  Az Azure Active Directory-identitásokkal alapuló hozzáférés-vezérlési listák a fájl- és szintjén állítható. |A fiókszintű engedélyezése – használja [Tárelérési kulcsok](../storage/common/storage-account-manage.md#access-keys)<br>A fiók, tároló vagy a blob engedélyezési - használja [megosztott hozzáférési aláírást kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Adatműveletek – naplózás |Érhető el. Lásd: [Itt](data-lake-store-diagnostic-logs.md) információt. |Elérhető |
| Az inaktív adatok titkosítása |<ul><li>Transzparens, kiszolgálóoldali</li> <ul><li>A szolgáltatás által kezelt kulcsok</li><li>Az ügyfél által felügyelt kulcsok az Azure-Kulcstartóban</li></ul></ul> |<ul><li>Transzparens, kiszolgálóoldali</li> <ul><li>A szolgáltatás által kezelt kulcsok</li><li>Az ügyfél által felügyelt kulcsok az Azure KeyVault (előzetes verzió)</li></ul><li>Ügyféloldali titkosítás</li></ul> |
| Felügyeleti műveletek (például fiók létrehozása) |[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) fiókok kezelése az Azure által biztosított (RBAC) |[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) fiókok kezelése az Azure által biztosított (RBAC) |
| Fejlesztői SDK-k |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Elemzési számítási feladat teljesítményére |Optimalizált teljesítménygyűjtési párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebességű és iops-t. |Optimalizált teljesítménygyűjtési párhuzamos elemzési számítási feladatokhoz. |
| Blobméretének korlátjai |A fiókok méretének, a fájlok méretét vagy a fájlok száma korlátlan |Dokumentált konkrét korlátozások [Itt](../storage/common/storage-scalability-targets.md). Nagyobb fiók korlátozza kapcsolatba lép a [Azure-támogatás](https://azure.microsoft.com/support/faq/) |
| Georedundancia |Helyileg redundáns (az adatok egy Azure-régióban. több példány) |Helyileg redundáns (LRS), a zónaredundáns (ZRS), globálisan georedundáns (GRS), az írásvédett globálisan redundáns (RA-GRS) zónáját. Lásd: [Itt](../storage/common/storage-redundancy.md) további információ |
| Szolgáltatás állapota |Általánosan elérhető |Általánosan elérhető |
| Régiónkénti rendelkezésre állás |Lásd: [Itt](https://azure.microsoft.com/regions/#services) |Az összes Azure-régióban érhető el |
| Ár |Lásd: [díjszabása](https://azure.microsoft.com/pricing/details/data-lake-store/) |Lásd: [díjszabása](https://azure.microsoft.com/pricing/details/storage/) |


