---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179756"
---
A repülésben lévő adattároláshoz:

- A standard TLS 1,2 az eszköz és az Azure közötti adattovábbításra szolgál. Nincs tartalék a TLS 1,1-es és korábbi verzióihoz. Az ügynök kommunikációja le lesz tiltva, ha a TLS 1,2 nem támogatott. A portál és az SDK felügyeletéhez a TLS 1,2 is szükséges.
- Amikor az ügyfelek egy böngésző helyi webes FELÜLETén keresztül érik el az eszközt, a standard TLS 1,2 alapértelmezett biztonságos protokollként van használatban.

    - Az ajánlott eljárás az, hogy a böngészőt a TLS 1,2 használatára konfigurálja.
    - Ha a böngésző nem támogatja a TLS 1,2-et, használhatja a TLS 1,1 vagy a TLS 1,0.
- Javasoljuk, hogy az SMB 3,0 titkosítással használja az adatok védelme érdekében az adatkiszolgálókról történő másoláskor.
