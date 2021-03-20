---
title: Microsoft Azure Data Box Heavy rendszerkövetelményei | Microsoft Docs
description: Tudnivalók a Azure Data Box Heavy szoftveres és hálózati követelményeiről
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84707752"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Rendszerkövetelmények Azure Data Box Heavy

Ez a cikk a Azure Data Box Heavy eszközre és az eszközhöz csatlakozó ügyfelekre vonatkozó fontos rendszerkövetelményeket ismerteti. Javasoljuk, hogy körültekintően tekintse át az információkat a Data Box Heavy üzembe helyezése előtt, majd az üzembe helyezés és az azt követő művelet során szükség szerint küldje vissza.

A rendszerkövetelmények a következők:

* A **Data Box Heavyhoz csatlakozó gazdagépek szoftveres követelményei** – ismerteti a támogatott platformokat, a helyi webes felhasználói felület, az SMB-ügyfelek és a Data boxhoz csatlakozni képes gazdagépek további követelményeit.
* **A Data Box Heavy hálózati követelményei** – információt nyújt a Data Box Heavy eszköz optimális működéséhez szükséges hálózati követelményekről.

## <a name="software-requirements"></a>Szoftverkövetelmények

A szoftverre vonatkozó követelmények tartalmazzák a támogatott operációs rendszerekkel, a helyi webes felhasználói felület és az SMB-ügyfelek által támogatott böngészőkkel kapcsolatos információkat.

### <a name="supported-operating-systems-for-clients"></a>Az ügyfelekhez támogatott operációs rendszerek

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Támogatott fájlrendszerek Linux rendszerű ügyfelek számára

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Támogatott webböngészők

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Hálózati követelmények

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. A leggyorsabb másolási sebességhez a 2 40-GbE kapcsolatok párhuzamosan használhatók (egy csomóponton belül). Ha nem érhető el a 40-GbE, javasoljuk, hogy legalább 2 10-GbE kapcsolattal rendelkezzen (egy-egy csomóponton).

### <a name="port-requirements"></a>Portra vonatkozó követelmények

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon, hogy engedélyezze az SMB-vagy NFS-forgalmat. A (z)  vagy a *bejövő* tábla a bejövő ügyfél által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimenő* *állapot arra utal* , hogy a Data Box Heavy-eszköz hogyan küldi el az adatokat külsőleg, az üzembe helyezésen kívül: például az internet felé.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Következő lépések

* [A Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
