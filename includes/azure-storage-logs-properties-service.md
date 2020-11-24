---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562521"
---
| Tulajdonság | Leírás |
|:--- |:---|
|**accountName** | A Storage-fiók neve. Például: `mystorageaccount`.  |
|**requestUrl** | A kért URL-cím. |
|**userAgentHeader** | A **felhasználói ügynök fejlécének** értéke idézőjelek között. Például: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | A **hivatkozó** fejléc értéke. Például: `http://contoso.com/about.html`.|
|**ügyfélkérelem** | A kérelem **x-MS-Client-Request-ID** fejlécének értéke. Például: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**ETAG** | A visszaadott objektum ETag azonosítója az idézőjelek között. Például: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | A kért művelet végrehajtásához szükséges ezredmásodpercben kifejezett teljes idő. Ez az érték nem tartalmazza a hálózati késést (a bejövő kérelem beolvasásának idejét és a válasz küldését a kérelmezőnek). Például: `22`. |
|**serviceType** | A kérelemhez társított szolgáltatás. Például: `blob` ,, `table` `files` , vagy `queue` . |
|**operationCount** | A kérelemben érintett összes naplózott művelet száma. Ez a szám a indexével kezdődik `0` . Egyes kérelmeknél több műveletre van szükség. A legtöbb kérelem csak egy műveletet hajt végre. Például: `1`. |
|**requestHeaderSize** | A kérelem fejlécének mérete bájtban kifejezve. Például: `578`. <br>Ha egy kérelem sikertelen, ez az érték üres is lehet. |
|**requestBodySize** | A tárolási szolgáltatás által beolvasott kérési csomagok mérete bájtban kifejezve. <br> Például: `0`. <br>Ha egy kérelem sikertelen, ez az érték üres is lehet.  |
|**responseHeaderSize** | A válasz fejlécének mérete bájtban kifejezve. Például: `216`. <br>Ha egy kérelem sikertelen, ez az érték üres is lehet.  |
|**responseBodySize** | A tárolási szolgáltatás által írt válasz csomagok mérete bájtban megadva. Ha egy kérelem sikertelen, ez az érték üres is lehet. Például: `216`.  |
|**requestMd5** | A kérelemben a **Content-MD5** fejléc vagy az **x-MS-Content-MD5** fejléc értéke. Az ebben a mezőben megadott MD5 kivonatoló érték a kérelemben szereplő tartalmat jelöli. Például: `788815fd0198be0d275ad329cafd1830`. <br>Ez a mező üres lehet.  |
|**serverMd5** | A tárolási szolgáltatás által kiszámított MD5-kivonat értéke. Például: `3228b3cf1069a5489b298446321f8521`. <br>Ez a mező üres lehet.  |
|**lastModifiedTime** | A visszaadott objektum utolsó módosításának időpontja (LMT).  Például: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Ez a mező üres olyan műveletekhez, amelyek több objektumot adhatnak vissza. |
|**conditionsUsed** | A feltételt képviselő kulcs-érték párok pontosvesszővel tagolt listája. A feltételek a következők lehetnek: <li> If-Modified-Since <li> Ha – nem módosítva – óta <li> If-Match <li> If-None-Match  <br> Például: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | A tárolási szolgáltatásnak továbbított kérelem Content-Length fejlécének értéke. Ha a kérelem sikeres volt, ez az érték egyenlő a requestBodySize. Ha egy kérelem sikertelen, az érték nem lehet egyenlő a requestBodySize, vagy üres is lehet. |
|**tlsVersion** | A kérelem összekapcsolásakor használt TLS-verzió. Például: `TLS 1.2`. |
|**smbTreeConnectID** | A kiszolgáló-üzenetblokk (SMB) **treeConnectId** a fa csatlakozási idején lett létrehozva. Például: `0x3` |
|**smbPersistentHandleID** | Állandó leíró azonosító egy olyan SMB2-LÉTREHOZÁSi kérelemből, amely túléli a hálózat újrakapcsolását.  Az [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 hivatkozik **SMB2_FILEID. Állandó**. Például: `0x6003f` |
|**smbVolatileHandleID** | A SMB2-LÉTREHOZÁSi kérelem által a hálózat újrakapcsolásakor újrahasznosított ideiglenes leíró azonosítója.  Az [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 hivatkozik **SMB2_FILEID. Változékony**. Például: `0xFFFFFFFF00000065` |
|**smbMessageID** | A kapcsolatok relatív **MessageID**. Például: `0x3b165` |
|**smbCreditsConsumed** | A kérelem által felhasznált bejövő vagy kimenő forgalom 64 kilobájt egységben. Például: `0x3` |
|**smbCommandDetail** | További információ erről a kérésről az általános típus helyett. Például: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | A fájlhoz vagy könyvtárhoz társított **FileId** .  Nagyjából hasonló az NTFS-FileId. Például: `0x9223442405598953` |
|**smbSessionID** | A munkamenet-beállítási időpontban létesített SMB2 **munkamenet** -azonosító. Például: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Érték a **SMB2_HEADER. parancsban**. Jelenleg ez egy 0 és 18 közötti szám. Például: `0x6` |
|**smbCommandMinor** | A **SmbCommandMajor** alosztálya, ahol szükséges. Például: `DirectoryCloseAndDelete` |