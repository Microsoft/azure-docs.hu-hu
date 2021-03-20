---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89505902"
---
| Port nem.| Be vagy ki | Port hatóköre| Kötelező| Jegyzetek |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|In|LAN|Yes|Ez a port a blob Storage REST API-k HTTP-n keresztüli Data Box való kapcsolódására szolgál. Ha nem csatlakozik a REST API-khoz, a rendszer automatikusan átirányítja a helyi webes felhasználói felületre a 8443-es kapcsolaton keresztül. |
| TCP 443 (HTTPS)|In|LAN|Yes|Ez a port a blob Storage REST API-k HTTPS-kapcsolaton keresztüli Data Box való kapcsolódására szolgál. Ha nem csatlakozik a REST API-khoz, a rendszer automatikusan átirányítja a helyi webes felhasználói felületre a 8443-es kapcsolaton keresztül. |
| TCP 8443 (HTTPS-Alt)|In|LAN|Yes|Ez egy másik HTTPS-port, amelyet a rendszer a helyi webes felhasználói felülethez való csatlakozáskor használ az eszközök kezeléséhez. |
| TCP 445 (SMB)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az NFS-en keresztül csatlakozik. |
| TCP 111 (NFS)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Ez a port a rpcbind/port hozzárendeléshez használatos, és csak akkor szükséges, ha az NFS-en keresztül csatlakozik.  |
