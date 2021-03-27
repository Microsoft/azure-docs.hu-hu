---
title: Microsoft Azure Data Box rendszerkövetelményei | Microsoft Docs
description: Ismerje meg a Azure Data Box fontos rendszerkövetelményeit, valamint a Data Boxhoz csatlakozó ügyfeleket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: 7f999dbf7a4e0262e36181a98560931d32d3296b
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612500"
---
# <a name="azure-data-box-system-requirements"></a>Rendszerkövetelmények Azure Data Box

Ez a cikk a Microsoft Azure Data Box és a Data Boxhoz kapcsolódó ügyfelek fontos rendszerkövetelményeit ismerteti. Javasoljuk, hogy körültekintően tekintse át az információkat, mielőtt üzembe helyezi a Data Box, majd a telepítés és a művelet során szükség esetén tekintse át a következőt:.

A rendszerkövetelmények a következők:

* A **szoftverre vonatkozó követelmények:** A Data Boxhoz csatlakozó gazdagépek a helyi webes felhasználói felület támogatott operációs rendszereit, a fájlátviteli protokollokat, a Storage-fiókokat, a tárolási típusokat és a böngészőket ismertetik.
* **Hálózati követelmények:** A Data Box a Data Box legjobb működéséhez szükséges hálózati kapcsolatokra és portokra vonatkozó követelményeket ismerteti.


## <a name="software-requirements"></a>Szoftverkövetelmények

A szoftverre vonatkozó követelmények közé tartoznak a támogatott operációs rendszerek, a fájlátviteli protokollok, a Storage-fiókok, a tárolási típusok és a helyi webes felhasználói felület böngészők.

### <a name="supported-operating-systems-for-clients"></a>Az ügyfelekhez támogatott operációs rendszerek

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Az ügyfelek által támogatott fájlátviteli protokollok

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Az Data Box-megosztásokhoz való kapcsolódás nem támogatott az exportálási rendeléseken a REST használatával.

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Támogatott webböngészők

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Hálózati követelmények

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Javasoljuk, hogy legalább 1 10-GbE-alapú kapcsolatban legyen. Ha egy 10 GbE-kapcsolat nem érhető el, a másolási sebességre egy 1 GbE adathivatkozás használható.

### <a name="port-requirements"></a>Portra vonatkozó követelmények

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon, hogy engedélyezze az SMB-vagy NFS-forgalmat. Ebben *a táblázatban a (* *bejövő*) arra utal, hogy a bejövő ügyfél milyen irányba kér hozzáférést az eszközhöz. A *kimenet* (vagy *kimenő*) arra az irányra vonatkozik, amelyben a Data Box eszköz külsőleg küld adatokat, a telepítésen kívül. Előfordulhat például, hogy az adatforgalom az interneten van.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Következő lépések

* [A Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
