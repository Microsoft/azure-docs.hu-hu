---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86218062"
---
| Port nem.| Be vagy ki | Port hatóköre| Kötelező|   Jegyzetek |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Ki|WAN |No|A rendszer a kimenő portot használja az internet-hozzáféréshez a frissítések lekéréséhez. <br>A kimenő webes proxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS)|Ki|WAN|Yes|A kimenő port a felhőben tárolt adatok elérésére szolgál.<br>A kimenő webes proxy a felhasználó által konfigurálható.|
| UDP 123 (NTP)|Ki|WAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha Internet-alapú NTP-kiszolgálót használ.  |   
| UDP 53 (DNS)|Ki|WAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha Internet alapú DNS-kiszolgálót használ.<br>Javasoljuk, hogy használjon helyi DNS-kiszolgálót. |
| TCP 5985 (WinRM)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Ez a port a távoli PowerShell-lel HTTP-n keresztül történő csatlakozáshoz szükséges.  |
| UDP 67 (DHCP)|Ki|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha helyi DHCP-kiszolgálót használ.  |
| TCP 80 (HTTP)|Ki/be|LAN|Yes|Ez a port a helyi kezelőfelület helyi felhasználói felületének bejövő portja az eszközön. <br>A helyi felhasználói felület HTTP-n keresztüli elérése automatikusan a HTTPS-re lesz átirányítva.  |
| TCP 443 (HTTPS)|Ki/be|LAN|Yes|Ez a port a helyi kezelőfelület helyi felhasználói felületének bejövő portja az eszközön. |
| TCP 445 (SMB)|In|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|In|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az NFS-en keresztül csatlakozik. |
