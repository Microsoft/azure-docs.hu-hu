---
title: Általános FabricClient-kivételek elvetve
description: Ismerteti azokat a gyakori kivételeket és hibákat, amelyeket a FabricClient API-k okozhatnak az alkalmazás-és fürtszolgáltatási műveletek végrehajtása során.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258843"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>A FabricClient API-k használata során előforduló gyakori kivételek és hibák
A [FabricClient](/dotnet/api/system.fabric.fabricclient) API-k lehetővé teszik a fürt és az alkalmazás rendszergazdái számára, hogy rendszergazdai feladatokat végezzenek el egy Service Fabric alkalmazáson, szolgáltatáson vagy fürtön. Például az alkalmazások központi telepítése, frissítése és eltávolítása, a fürt állapotának ellenőrzése vagy a szolgáltatás tesztelése. Az alkalmazások fejlesztői és a FabricClient API-kat használhatnak a Service Fabric-fürt és-alkalmazások kezeléséhez szükséges eszközök fejlesztéséhez.

A FabricClient használatával számos különböző típusú műveletet lehet elvégezni.  Az egyes módszerek kivételeket okozhatnak a helytelen bemeneti, futásidejű hibák vagy átmeneti infrastrukturális problémák miatti hibákhoz.  Tekintse meg az API-dokumentációt, amelyből megtudhatja, hogy mely kivételeket dobják egy adott módszer. Vannak azonban kivételek, amelyeket számos különböző [FabricClient](/dotnet/api/system.fabric.fabricclient) API okozhat. A következő táblázat felsorolja azokat a kivételeket, amelyek közösek a FabricClient API-k között.

| Kivétel | Kidobás időpontja |
| --- |:--- |
| [System. Fabric. FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |A [FabricClient](/dotnet/api/system.fabric.fabricclient) objektum lezárt állapotban van. Az Ön által használt [FabricClient](/dotnet/api/system.fabric.fabricclient) -objektum és az új [FabricClient](/dotnet/api/system.fabric.fabricclient) -objektum példányának elvetése. |
| [System. Timeoutexception osztályról](/dotnet/core/api/system.timeoutexception) |A művelet időkorlátja lejárt. A rendszer akkor adja vissza a [OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) , ha a művelet végrehajtása a MaxOperationTimeout-nál több időt vesz igénybe. |
| [System. UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |Sikertelen volt a művelethez való hozzáférés-ellenőrzési művelet. E_ACCESSDENIED visszaadva. |
| [System. Fabric. FabricException](/dotnet/api/system.fabric.fabricexception) |Futásidejű hiba történt a művelet végrehajtása közben. A FabricClient metódusok bármelyike potenciálisan [FabricException](/dotnet/api/system.fabric.fabricexception), a [errorcode](/dotnet/api/system.fabric.fabricexception.errorcode) tulajdonság a kivétel pontos okát jelzi. A hibakódok meghatározása a [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) enumerálásban történik. |
| [System. Fabric. FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |A művelet valamilyen átmeneti hiba miatt nem sikerült. Előfordulhat például, hogy egy művelet meghiúsul, mert a replikák kvóruma átmenetileg nem érhető el. Az átmeneti kivételek az újrapróbálható sikertelen műveleteknek felelnek meg. |

Néhány gyakori [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) -hiba, amely egy [FabricException](/dotnet/api/system.fabric.fabricexception)adható vissza:

| Hiba | Feltétel |
| --- |:--- |
| CommunicationError |Egy kommunikációs hiba miatt a művelet meghiúsult, próbálja megismételni a műveletet. |
| InvalidCredentialType |A hitelesítő adat típusa érvénytelen. |
| InvalidX509FindType |A X509FindType érvénytelen. |
| InvalidX509StoreLocation |A X509 tároló helye érvénytelen. |
| InvalidX509StoreName |A X509-tároló neve érvénytelen. |
| InvalidX509Thumbprint |A X509 tanúsítványának ujjlenyomat-karakterlánca érvénytelen. |
| InvalidProtectionLevel |A védelmi szint érvénytelen. |
| InvalidX509Store |Nem lehet megnyitni a X509-tanúsítványtárolót. |
| InvalidSubjectName |A tulajdonos neve érvénytelen. |
| InvalidAllowedCommonNameList |A köznapi név lista karakterláncának formátuma érvénytelen. Vesszővel tagolt listának kell lennie. |
