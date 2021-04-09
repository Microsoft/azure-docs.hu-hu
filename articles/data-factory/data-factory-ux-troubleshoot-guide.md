---
title: Azure Data Factory UX – problémamegoldás
description: Ismerje meg, hogyan lehet elhárítani Azure Data Factory UX-problémákat.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382837"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>A Azure Data Factory UX problémáinak elhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory UX gyakori hibaelhárítási módszereit vizsgálja.

## <a name="adf-ux-not-loading"></a>Az ADF UX nem töltődik be

> [!NOTE]
> A Azure Data Factory UX hivatalosan támogatja a Microsoft Edge és a Google Chrome böngészőt. Más böngészők használata váratlan vagy nem dokumentált viselkedéshez vezethet.

### <a name="third-party-cookies-blocked"></a>A harmadik féltől származó cookie-k blokkolva

Az ADF UX böngésző-cookie-kat használ a felhasználói munkamenet megőrzéséhez, és lehetővé teszi az interaktív fejlesztési és monitorozási élmények használatát. Lehetséges, hogy a böngésző blokkolja a harmadik féltől származó cookie-kat, mert inkognitóban-munkamenetet használ, vagy ha engedélyezve van az ad blocker. A harmadik féltől származó cookie-k blokkolása problémákat okozhat a portál betöltésekor, például egy üres lapra való átirányítással, https://adf.azure.com/accesstoken.html vagy egy figyelmeztető üzenet beszerzésével, amely azt jelzi, hogy a harmadik féltől származó cookie-k le vannak tiltva. A probléma megoldásához engedélyezze a harmadik féltől származó cookie-k beállításait a böngészőben a következő lépések végrehajtásával:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Az összes cookie engedélyezése

1. Keresse fel a **Chrome://Settings/Cookies** a böngészőben.
1. Válassza **a minden cookie engedélyezése** lehetőséget. 

    ![Az összes cookie engedélyezése a Chrome-ban](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Csak a cookie-k használatának engedélyezése az ADF UX számára
Ha nem kívánja engedélyezni az összes cookie-t, igény szerint egyszerűen engedélyezheti az ADF UX használatát:
1. Látogasson el a **Chrome://Settings/Cookies**.
1. Válassza a **Hozzáadás** lehetőséget a **helyek területen a cookie-k mindig használható** lehetőséggel 

    ![Az ADF UX hozzáadása az engedélyezett helyekhez a Chrome-ban](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Adja hozzá a **ADF.Azure.com** -webhelyet, és kattintson a **minden cookie** lehetőségre, majd a Mentés elemre. 

    ![Az összes cookie engedélyezése az ADF UX-helyről](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Keresse fel a **Edge://Settings/Content/Cookies** a böngészőben.
1. Győződjön meg arról, hogy engedélyezi a **helyek számára a cookie-k adatmentését és olvasását** , valamint hogy a **külső cookie-k blokkolása** beállítás le van tiltva 

    ![Az összes cookie engedélyezése az Edge-ben](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Csak a cookie-k használatának engedélyezése az ADF UX számára

Ha nem kívánja engedélyezni az összes cookie-t, igény szerint egyszerűen engedélyezheti az ADF UX használatát:

1. Látogasson el a **Edge://Settings/Content/Cookies**.
1. Az **Engedélyezés** szakaszban válassza a **Hozzáadás** elemet, és adja hozzá a **ADF.Azure.com** helyet. 

    ![Az ADF UX hozzáadása az engedélyezett helyekhez az Edge-ben](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.

## <a name="connection-failed-on-adf-ux"></a>Nem sikerült kapcsolódni az ADF UX-hez

Előfordulhat, hogy a "kapcsolódás sikertelen" hibát észlelt az ADF UX-ben az alábbi képernyőképhez hasonlóan, miután a **Kapcsolódás**, az **előnézet** stb. elemre kattintott.

![Sikertelen volt a hozzáférés](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

Ebben az esetben először próbálkozhat ugyanazzal a művelettel a böngészőben a InPrivate-böngészési módban is.

Ha továbbra sem működik, a böngészőben nyomja meg az F12 billentyűt a **fejlesztői eszközök** megnyitásához. Nyissa meg a **hálózat** lapot, ellenőrizze a **gyorsítótár letiltását**, próbálja megismételni a sikertelen műveletet, és keresse meg a sikertelen kérelmet (piros színnel).

![Sikertelen kérelem](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Ezután keresse meg az **állomásnév** (ebben az esetben a **dpnortheurope.SVC.DataFactory.Azure.com**) nevet a sikertelen kérelem **URL-címében** .

Írja be a **gazdagép nevét** közvetlenül a böngésző címsorában. Ha a böngészőben a 404 jelenik meg, ez általában azt jelenti, hogy az ügyfél oldalán ok van, és a probléma az ADF szolgáltatás oldalán található. A támogatási jegyet az ADF UX-hibaüzenetből származó **tevékenység-azonosítóval** együtt küldheti.

![Az erőforrás nem található](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Ha nem, vagy a böngészőben hasonló hibaüzenet jelenik meg, ez általában azt jelenti, hogy van egy ügyféloldali probléma. Kövesse a hibaelhárítási lépéseket.

![Ügyféloldali hiba](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Nyisson meg egy **parancssort** , és írja be az **nslookup dpnortheurope.SVC.DataFactory.Azure.com** parancsot. A normál válasznak az alábbihoz hasonlóan kell kinéznie:

![1. parancs válasza](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Ha normál DNS-választ lát, további információért forduljon a helyi informatikai támogatási szolgálathoz, és ellenőrizze a tűzfal beállításait, hogy az állomásnév HTTPS-kapcsolata le van-e tiltva vagy sem. Ha a probléma nem oldható fel, az ADF UX hibaüzenetből származó **tevékenység-azonosítóval** rendelkező támogatási jegyet.

Ha ezt látja, akkor ez általában azt jelenti, hogy a DNS-kiszolgáló a DNS-név feloldásakor hiba történik. Általában az INTERNETSZOLGÁLTATÓ (internetszolgáltató) vagy a DNS (például a Google DNS 8.8.8.8) módosítása lehetséges megkerülő megoldás lehet a kipróbáláshoz. Ha a probléma továbbra is fennáll, akkor az **nslookup DataFactory.Azure.com** és az **nslookup Azure.com** további kipróbálása után megtekintheti, hogy a DNS-feloldás milyen szinten meghiúsult, és az összes információt elküldheti a helyi informatikai TÁMOGATÁSnak vagy az internetszolgáltatónak a hibaelhárításhoz. Ha úgy vélik, hogy a probléma továbbra is a Microsoft oldalán található, a támogatási jegyet az ADF UX-hibaüzenetből származó **tevékenység-azonosítóval** kell benyújtani.

![2. parancs válasza](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Társított szolgáltatás típusának módosítása az adatkészletekben

A fájlformátum-adatkészletek használhatók az összes fájl alapú összekötővel, például konfigurálhat egy parketta-adatkészletet az Azure blobon vagy a Azure Data Lake Storage Gen2on. Vegye figyelembe, hogy mindegyik összekötő a tevékenységhez kapcsolódó adattárolók különböző készletét támogatja, és a különböző alkalmazás-modellel rendelkezik. 

Az ADF-szerzői felhasználói felületen, ha fájlformátum-adatkészletet használ egy tevékenységben – beleértve a másolási, a keresési, a GetMetadata, a törlési tevékenységeket – és az adatkészletben, amelyet a jelenlegitől eltérő típusú társított szolgáltatásra kíván mutatni (például váltás a fájlrendszerről a ADLS Gen2ra), akkor a következő figyelmeztető üzenet jelenik meg. Annak érdekében, hogy tiszta kapcsoló legyen, az Ön beleegyezik, a folyamatokat és tevékenységeket, amelyek hivatkoznak erre az adathalmazra, az új típust is használni fogja, és az új típussal nem kompatibilis meglévő adattár-beállítások törlődnek, mivel az már nem lesz érvényes.

Ha többet szeretne megtudni az egyes összekötők által támogatott adattár-beállításokról, a részletes tulajdonságok listájának megjelenítéséhez nyissa meg a megfelelő összekötő cikk-> másolási tevékenység tulajdonságait. Tekintse meg az [Amazon S3](connector-amazon-simple-storage-service.md), az [Azure Blob](connector-azure-blob-storage.md), a [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), az [Azure file Storage](connector-azure-file-storage.md), a [fájlrendszer](connector-file-system.md), az [FTP](connector-ftp.md), a [Google Cloud Storage](connector-google-cloud-storage.md), a [HDFS](connector-hdfs.md), a [http](connector-http.md)és az [SFTP](connector-sftp.md)témakört.

![Figyelmeztető üzenet](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

* [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-videók](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)