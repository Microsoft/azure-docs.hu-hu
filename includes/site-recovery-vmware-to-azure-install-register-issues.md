---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5f794420eebb052538de0903727951de39f44a48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001557"
---
### <a name="installation-failures"></a>Telepítési hibák
| **Példa hibaüzenet** | **Javasolt művelet** |
|--------------------------|------------------------|
|HIBA: a fiókok betöltése nem sikerült. Hiba: System.IO.IOException: A CS-kiszolgáló telepítésekor és regisztrálásakor nem sikerült beolvasni az átviteli kapcsolatból származó adatokat.| Ellenőrizze, hogy a TLS 1.0 engedélyezve van-e számítógépen. |

### <a name="registration-failures"></a>Regisztrációs hibák
A regisztrációs hibákat a **%ProgramData%\ASRLogs** mappa naplóinak áttekintésével lehet kikeresni.

| **Példa hibaüzenet** | **Javasolt művelet** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Üzenet: ACS50008: Az SAML-jogkivonat érvénytelen.<br>Nyomkövetési azonosító: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Korrelációs azonosító: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Időbélyegző: **2016-12-12 14:50:08Z<br>** | Győződjön meg arról, hogy a rendszeróra által mutatott idő legfeljebb 15 perccel tér el a helyi időtől. A regisztráció befejezéséhez futtassa ismét a telepítőt.|
|**09:35:27** :DRRegistrationException kivétel történt a kiválasztott tanúsítvány vészhelyreállítási tárolóinak lekérése során: : Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: Az SAML-jogkivonat érvénytelen.<br>Nyomkövetési azonosító: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Korrelációs azonosító: abe9deb8-3e64-464d-8375-36db9816427a<br>Időbélyegző: **2016-05-19 01:35:39Z**<br> | Győződjön meg arról, hogy a rendszeróra által mutatott idő legfeljebb 15 perccel tér el a helyi időtől. A regisztráció befejezéséhez futtassa ismét a telepítőt.|
|06:28:45:Nem sikerült a tanúsítvány előállítása<br>06:28:45:A telepítést nem lehet folytatni. A Site Recovery hitelesítéséhez szükséges tanúsítvány nem hozható létre. Telepítés ismételt futtatása | Győződjön meg arról, hogy helyi rendszergazdaként futtatja a telepítőt. |
