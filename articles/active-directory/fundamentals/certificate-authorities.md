---
title: Hitelesítésszolgáltatók Azure Active Directory
description: Az Azure-ban használt megbízható tanúsítványok listája
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96859131"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Azure Active Directory által használt hitelesítésszolgáltatók

> [!IMPORTANT]
> Az ezen a lapon található információk csak azokra az entitásokra vonatkoznak, amelyek explicit módon határozzák meg az elfogadható hitelesítésszolgáltatók (CA-k) listáját. Ezt a gyakorlatot, amelyet tanúsítvány-rögzítésnek neveznek, el kell kerülni, hacsak nincs más lehetőség.

Az Azure Active Directory (Azure AD) Identity Services TLS/SSL protokollon keresztüli elérésére irányuló entitások az alább felsorolt hitelesítésszolgáltatók tanúsítványainak használatával jelennek meg. Ha az entitás megbízhatónak minősíti ezeket a hitelesítésszolgáltatókat, akkor a tanúsítványok segítségével ellenőrizheti az Identity Services identitását és törvényességét, valamint biztonságos kapcsolatokat hozhat létre.

A hitelesítésszolgáltatók besorolhatók a legfelső szintű Hitelesítésszolgáltatókba és a köztes Hitelesítésszolgáltatókba. A legfelső szintű hitelesítésszolgáltatók általában egy vagy több társított közbenső hitelesítésszolgáltatóval rendelkeznek. Ez a cikk felsorolja az Azure AD Identity Services által használt legfelső szintű hitelesítésszolgáltatókat és az ezekhez a gyökerekhez társított közbenső hitelesítésszolgáltatókat. Minden egyes HITELESÍTÉSSZOLGÁLTATÓ esetében egységes erőforrás-azonosítók (URI-k) szerepelnek a társított szolgáltatói információ-hozzáférés (AIA) és a tanúsítvány-visszavonási lista terjesztési pont (CDP) fájljainak letöltéséhez. Ha szükséges, egy URI-t is biztosítunk az online tanúsítványállapot-protokoll (OCSP) végpontjának.

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Az Azure nyilvános és az USA-beli kormányzati felhőkben használt hitelesítésszolgáltatók

A különböző szolgáltatások különböző gyökér-vagy köztes hitelesítésszolgáltatókat alkalmazhatnak. Ezért előfordulhat, hogy az alább felsorolt összes bejegyzés megadása kötelező.

### <a name="digicert-global-root-g2"></a>DigiCert globális root G2


| Legfelső szintű hitelesítésszolgáltató| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - |- |-|-|-|-|
| DigiCert globális root G2| 033af1e6a711a 9a0bb2864b11d09fae5| Augusztus 1-től 2013 <br>Január 15., 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>Társított közbenső hitelesítésszolgáltatók

| Kiállító és közbenső HITELESÍTÉSSZOLGÁLTATÓ| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - | - | - | - | - | 
| Microsoft Azure TLS kiállító CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| Július 29., 2020<br>2024. június 27.| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS kiállító CA 02| 0c6ae97cced59983 8690a00a9ea53214| Július 29., 2020<br>2024. június 27.| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS kiállító CA 05| 0d7bede97d8209967a 52631b8bdd18bd| Július 29., 2020<br>2024. június 27.| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS kiállító HITELESÍTÉSSZOLGÁLTATÓ 06| 02e79171fb8021e93fe 2d983834c50c0| Július 29., 2020<br>2024. június 27.| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust-gyökér

| Legfelső szintű hitelesítésszolgáltató| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - | - | - | - | - | 
| Baltimore CyberTrust-gyökér| 020000b9| 2000. május 12.<br>2025. május 12.| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Társított közbenső hitelesítésszolgáltatók

| Kiállító és közbenső HITELESÍTÉSSZOLGÁLTATÓ| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 2020. július 21.<br>2024. október 8.| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 2020. július 21.<br>2024. május 20.| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ

| Legfelső szintű hitelesítésszolgáltató| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - | - | - | - | - | 
| DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ| 083be056904246 b1a1756ac95991c74a| November 9., 2006<br>November 9., 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Társított közbenső hitelesítésszolgáltatók

| Kiállító és közbenső HITELESÍTÉSSZOLGÁLTATÓ| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - | - | - | - | - |
| DigiCert SHA2 biztonságos kiszolgálói HITELESÍTÉSSZOLGÁLTATÓ| 01fda3eb6eca75c 888438b724bcfbc91| Március 8., 2013. március 8., 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 biztonságos kiszolgálói HITELESÍTÉSSZOLGÁLTATÓ |02742eaa17ca8e21 c717bb1ffcfd0ca0 |Szeptember 22., 2020<br>Szeptember 22., 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Az Azure China 21Vianet-felhőben használt hitelesítésszolgáltatók

### <a name="digicert-global-root-ca"></a>DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ


| Legfelső szintű hitelesítésszolgáltató| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - | - | - | - | - |
| DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ| 083be056904246b 1a1756ac95991c74a| November 9., 2006<br>November 9., 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>Társított közbenső HITELESÍTÉSSZOLGÁLTATÓ

| Kiállító és közbenső HITELESÍTÉSSZOLGÁLTATÓ| Sorozatszám| Kiállítás dátuma lejárati dátuma| SHA1 ujjlenyomat| URI |
| - | - | - | - | - | - |
| DigiCert alapszintű RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 2020. március 4.<br>2030. március 4.| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Következő lépések
[Tudnivalók a Microsoft 365 titkosítási láncokról](/microsoft-365/compliance/encryption-office-365-certificate-chains)
