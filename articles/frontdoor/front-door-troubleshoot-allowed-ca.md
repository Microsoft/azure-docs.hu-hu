---
title: Hitelesítésszolgáltatók engedélyezett az Azure bejárati ajtajának Service egyéni HTTPS engedélyezése |} A Microsoft Docs
description: Egyéni tartomány HTTPS engedélyezéséhez a saját tanúsítványt használ, ha egy engedélyezett hitelesítésszolgáltató (CA) létrehozásához kell használnia.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 5cf94079dcd68887d9725ffbe9124f9b6c897dd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736156"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Hitelesítésszolgáltatók engedélyezett az Azure bejárati ajtajának Service egyéni HTTPS engedélyezése

Azure bejárati ajtajának Service egyéni tartomány amikor Ön [HTTPS szolgáltatás engedélyezése a saját tanúsítvánnyal](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), engedélyezett hitelesítésszolgáltató (CA) kell használnia az SSL-tanúsítvány létrehozásához. Ellenkező esetben nem engedélyezett hitelesítésszolgáltató vagy egy önaláírt tanúsítványt használ, ha a kérés rendszer elutasítja.

A következő CAs engedélyezettek a saját tanúsítvány létrehozásakor:

- AddTrust külső hitelesítésszolgáltató legfelső szintű
- AlphaSSL Root CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- Hozzáférési pont legfelső szintű hitelesítésszolgáltató
- Hozzáférési pont legfelső szintű hitelesítésszolgáltató 2013-hoz
- Hozzáférési pont legfelső szintű hitelesítésszolgáltató 2014
- APCA-DM3P
- Az autopilot legfelső szintű hitelesítésszolgáltató
- Baltimore CyberTrust legfelsőbb szintű
- Nyilvános elsődleges hitelesítésszolgáltató osztály 3
- Hitelesítésszolgáltató COMODO RSA
- COMODO RSA tartomány érvényesítési biztonságos kiszolgáló hitelesítésszolgáltató
- D – megbízható legfelső szintű osztály 3 hitelesítésszolgáltató 2 2009
- A Felhőszolgáltatások DigiCert hitelesítésszolgáltató-1
- A DigiCert globális legfelső szintű hitelesítésszolgáltató
- A DigiCert nagy frissítési garanciát biztosító hitelesítésszolgáltató – 3
- A DigiCert nagy garancia, Bővített legfelső szintű hitelesítésszolgáltató
- A DigiCert SHA2 bővített ellenőrzés kiszolgáló hitelesítésszolgáltató
- A DigiCert SHA2 magas szintű megbízhatóság kiszolgáló hitelesítésszolgáltató
- A DigiCert SHA2 biztonságos kiszolgáló hitelesítésszolgáltató
- Nyári Időszámítás legfelső szintű hitelesítésszolgáltató X3
- D – megbízható legfelső szintű osztály 3 hitelesítésszolgáltató 2 2009
- Sokfelé kell tárolnia a titkosítási DV TLS hitelesítésszolgáltató
- Entrust-legfelső szintű hitelesítésszolgáltató
- Entrust-legfelső szintű hitelesítésszolgáltató - G2 csomag
- Entrust.net Certification Authority (2048)
- GeoTrust globális hitelesítésszolgáltató
- GeoTrust elsődleges hitelesítésszolgáltató
- GeoTrust elsődleges hitelesítésszolgáltató - G2 csomag
- Geotrust RSA CA 2018
- GlobalSign
- Bővített ellenőrzés CA - SHA256 - G2 GlobalSign
- GlobalSign szervezet érvényesítési CA - G2 csomag
- GlobalSign Root CA
- Go Daddy legfelső szintű hitelesítésszolgáltató - G2 csomag
- Go Daddy biztonságos hitelesítésszolgáltató - G2 csomag
- RapidSSL RSA CA 2018
- Legfelső szintű Hivatal
- A Symantec osztály 3 Bővített SSL CA - G3 csomag
- A Symantec osztály 3 biztonságos kiszolgáló CA - G4
- A Microsoft a Symantec Enterprise Mobile Root
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató - G2 csomag
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató - G3 csomag
- Thawte RSA CA 2018
- Thawte Timestamping CA
- TrustAsia TLS RSA CA
- SSL-Hitelesítésszolgáltatói VeriSign osztály 3 bővített ellenőrzés
- A VeriSign osztály 3 bővített ellenőrzés SSL kiszolgáló által Kezdeményezett hitelesítésszolgáltató
- 3. a VeriSign osztály nyilvános elsődleges hitelesítésszolgáltató - G5
- A VeriSign nemzetközi kiszolgáló CA - 3 osztályban
- VeriSign Time Stamping Service Root
- A VeriSign univerzális legfelső szintű hitelesítésszolgáltató
