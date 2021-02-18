---
title: Engedélyezett hitelesítésszolgáltatók az Azure-beli előtérben standard/prémium (előzetes verzió)
description: Ez a cikk felsorolja az összes, a saját tanúsítvány létrehozásakor engedélyezett hitelesítésszolgáltatót.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100240"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Engedélyezett hitelesítésszolgáltatók az Azure-beli előtérben standard/prémium (előzetes verzió)

Ha a HTTPS szolgáltatást a saját tanúsítványával engedélyezi egy Azure bejárati ajtó standard/prémium egyéni tartományához. A TLS/SSL-tanúsítvány létrehozásához engedélyezett hitelesítésszolgáltató (CA) szükséges. Ellenkező esetben, ha nem engedélyezett HITELESÍTÉSSZOLGÁLTATÓT vagy önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet.

A saját tanúsítvány létrehozásakor a következő hitelesítésszolgáltatók engedélyezettek:

- AddTrust külső HITELESÍTÉSSZOLGÁLTATÓI gyökér
- AlphaSSL legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ
- AME infra CA 01
- AME infra CA 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Autopilot legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja
- Baltimore CyberTrust-gyökér
- 3. osztály – nyilvános elsődleges hitelesítésszolgáltató
- COMODO RSA hitelesítésszolgáltató
- COMODO RSA-tartomány érvényesítése biztonságos kiszolgáló HITELESÍTÉSSZOLGÁLTATÓja
- D-TRUST root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ
- DigiCert globális root G2
- DigiCert, magas megbízhatóságú HITELESÍTÉSSZOLGÁLTATÓ – 3
- DigiCert magas megbízhatóságú, EV legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja
- DigiCert SHA2 kiterjesztett ellenőrzési kiszolgáló HITELESÍTÉSSZOLGÁLTATÓja
- DigiCert SHA2, magas megbízhatóságú kiszolgáló HITELESÍTÉSSZOLGÁLTATÓja
- DigiCert SHA2 biztonságos kiszolgálói HITELESÍTÉSSZOLGÁLTATÓ
- DST legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ X3
- D-Trust root Class 3 CA 2 2009
- Titkosítás mindenhol DV TLS-CA
- Legfelső szintű hitelesítésszolgáltató megbízza
- Legfelső szintű hitelesítésszolgáltató megbízza – G2
- Entrust.net hitelesítésszolgáltató (2048)
- GeoTrust globális HITELESÍTÉSSZOLGÁLTATÓ
- GeoTrust elsődleges hitelesítésszolgáltató
- GeoTrust elsődleges hitelesítésszolgáltató – G2
- GeoTrust RSA CA 2018
- GlobalSign
- GlobalSign kiterjesztett ellenőrzési CA-SHA256-G2
- GlobalSign-szervezet ellenőrzési CA-G2
- GlobalSign legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ
- Go Daddy – legfelső szintű hitelesítésszolgáltató – G2
- Go Daddy – biztonságos hitelesítésszolgáltató – G2
- Let 's encrypting Authority X3
- MS e-szigno legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ 2009
- QuoVadis gyökérszintű CA2 G3
- RapidSSL RSA CA 2018
- Biztonsági kommunikáció RootCA1
- Biztonsági kommunikáció RootCA2
- Biztonsági kommunikáció RootCA3
- Symantec Class 3 EV SSL-CA – G3
- Symantec 3. osztály – biztonságos kiszolgálói HITELESÍTÉSSZOLGÁLTATÓ – G4
- Symantec Enterprise Mobile root a Microsoft számára
- Thawte elsődleges legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ
- Thawte elsődleges legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ – G2
- Thawte elsődleges legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ – G3
- Thawte RSA CA 2018
- Thawte időbélyegzővel rendelkező HITELESÍTÉSSZOLGÁLTATÓ
- TrustAsia TLS RSA CA
- VeriSign Class 3 kiterjesztett érvényesítési SSL CA
- VeriSign Class 3 kiterjesztett érvényesítési SSL SGC HITELESÍTÉSSZOLGÁLTATÓ
- VeriSign Class 3 nyilvános elsődleges hitelesítésszolgáltató – G5
- VeriSign International Server CA – 3. osztály
- VeriSign időbélyegzési szolgáltatás gyökere
- VeriSign univerzális legfelső szintű hitelesítésszolgáltató
