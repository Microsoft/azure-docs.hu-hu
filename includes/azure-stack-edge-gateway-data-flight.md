---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466843"
---
A repülésben lévő adattároláshoz:

- A standard Transport Layer Security (TLS) 1,2 az eszköz és az Azure közötti adattovábbításra szolgál. Nincs tartalék a TLS 1,1-es és korábbi verzióihoz. Az ügynök kommunikációja le lesz tiltva, ha a TLS 1,2 nem támogatott. A portál és az SDK felügyeletéhez a TLS 1,2 is szükséges.
- Amikor az ügyfelek egy böngésző helyi webes FELÜLETén keresztül érik el az eszközt, a standard TLS 1,2 alapértelmezett biztonságos protokollként van használatban.

  - Az ajánlott eljárás az, hogy a böngészőt a TLS 1,2 használatára konfigurálja.
  - Az eszköz csak a TLS 1,2-et támogatja, és nem támogatja a régebbi verziójú TLS 1,1 és TLS 1,0.
- Javasoljuk, hogy az SMB 3,0 titkosítással használja az adatok védelme érdekében az adatkiszolgálókról történő másoláskor.
