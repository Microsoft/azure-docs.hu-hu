---
title: A Microsoft Azure Data Box Disk rendszerkövetelményei | Microsoft Docs
description: Az Azure Data Box Disk szoftveres és hálózati követelményeinek ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 750ed8f65db04199ea284e69693bced65a1dc8d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703133"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Az Azure Data Box Disk rendszerkövetelményei

A cikk a Microsoft Azure Data Box Disk megoldással és a Data Box Diskhez csatlakozó ügyfelekkel kapcsolatos fontos rendszerkövetelményeket írja le. Azt javasoljuk, hogy a Data Box Disk üzembe helyezése előtt alaposan tekintse át az információkat, és az üzembe helyezés és a későbbi üzemeltetés során térjen vissza ezekre.

A rendszerkövetelmények közé tartoznak a lemezekhez csatlakozó ügyfelek támogatott platformjai, a támogatott tárfiókok és a tárolótípusok.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Előfeltételek áttekintése

1. Megrendelte a Data Box Disket az [Oktatóanyag: Az Azure Data Box Disk megrendelése](data-box-disk-deploy-ordered.md) című szakaszban leírtak szerint. Megkapta a lemezeket, és lemezenként egy csatlakozókábelt.
2. Rendelkezik egy ügyfélszámítógéppel, amelyről lemásolhatja az adatokat. Az ügyfélszámítógépen:

    - Egy támogatott operációs rendszernek kell futnia.
    - Telepítve kell lennie a további szükséges szoftvereknek.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Az ügyfelekhez támogatott operációs rendszerek

Itt láthatja azon operációs rendszerek listáját, amelyeken támogatott a lemezek zárolásának feloldása és az adatmásolás a Data Box Diskhez csatlakozó ügyfeleken keresztül.

| **Operációs rendszer** | **Tesztelt verziók** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bites) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>A Windows rendszerű ügyfelekhez kötelező egyéb szoftverek

Windows rendszerű ügyfelek esetén a következőket is telepíteni kell.

| **Szoftver**| **Verzió** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET-keretrendszer |4.5.1 |
| Windows Management Framework |5.1|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>A Linux rendszerű ügyfelekhez kötelező egyéb szoftverek

Linux ügyfelek esetén a Data Box Disk a következő kötelező szoftvereket telepíti:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Támogatott kapcsolat

Az adatokat tartalmazó ügyfélszámítógépnek USB 3.0-s vagy újabb porttal kell rendelkeznie. A lemezek a mellékelt kábellel csatlakoznak ehhez az ügyfélhez.

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Itt láthatja a Data Box Disk támogatott tárolótípusainak listáját.

| **Storage-fiók** | **Megjegyzések** |
| --- | --- |
| Klasszikus | Standard |
| Általános célú  |Standard; a V1 és a V2 is támogatott. A gyakori és a ritka elérésű szintek is támogatottak. |
| Blob Storage-fiók | |

> [!IMPORTANT]
> A hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage-ban Data Box Disk nem támogatott.

## <a name="supported-storage-types-for-upload"></a>Feltöltéshez támogatott tárolótípusok

Itt láthatja a Data Box Diskkel az Azure-ba való feltöltéshez támogatott tárolótípusok listáját.

| **Fájlformátum** | **Megjegyzések** |
| --- | --- |
| Azure-blokkblob | |
| Azure-lapblob  | |
| Azure Files  | |
| Felügyelt lemezek | |

::: zone target="docs"

## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box üzembe helyezése](data-box-disk-deploy-ordered.md)

::: zone-end

