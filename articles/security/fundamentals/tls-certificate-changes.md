---
title: Az Azure TLS-tanúsítvány változásai
description: Az Azure TLS-tanúsítvány változásai
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 04b68899b796088bd4b89509a413c7a1fe5369bb
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475352"
---
# <a name="azure-tls-certificate-changes"></a>Az Azure TLS-tanúsítvány változásai  

A Microsoft frissíti az Azure-szolgáltatásokat, hogy TLS-tanúsítványokat használjanak egy másik legfelső szintű hitelesítésszolgáltatóktól (CA). Ez a változás azért történik, mert az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok nem felelnek meg a CA/Browser fórum alapkövetelményeinek, és 2021. február 15-én visszavonjuk.

## <a name="when-will-this-change-happen"></a>Mikor fog történni ez a változás?

A meglévő Azure-végpontok a 2020-as augusztus 13. óta lépcsőzetes módon lettek átalakulóban. Minden újonnan létrehozott Azure TLS/SSL-végpont frissített tanúsítványokat tartalmaz az új legfelső szintű hitelesítésszolgáltatókhoz.

Szolgáltatás-specifikus részletek:

- A [Azure Active Directory](../../active-directory/index.yml) (Azure ad) szolgáltatások a 2020. július 7-én kezdték el ezt az áttérést.
- Az [Azure IoT hub](https://azure.microsoft.com/services/iot-hub) és a [DPS](../../iot-dps/index.yml) továbbra is a Baltimore CYBERTRUST legfelső szintű hitelesítésszolgáltatóján marad, de a köztes hitelesítésszolgáltatók is megváltoznak. [További részletekért kattintson ide](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- Az [Azure Storage](../../storage/index.yml) továbbra is a Baltimore CyberTrust legfelső szintű hitelesítésszolgáltatóján marad, de a köztes hitelesítésszolgáltatók is megváltoznak. [További részletekért kattintson ide](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- A [Redis-hez készült Azure cache](../../azure-cache-for-redis/index.yml) a Baltimore CyberTrust legfelső szintű hitelesítésszolgáltatón marad, de a köztes hitelesítésszolgáltatók is megváltoznak. [További részletekért kattintson ide](../../azure-cache-for-redis/cache-whats-new.md).
- Az Azure Instance Metadata Service továbbra is a Baltimore CyberTrust legfelső szintű HITELESÍTÉSSZOLGÁLTATÓján marad, de a köztes hitelesítésszolgáltatók is megváltoznak. [További részletekért kattintson ide](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html).

> [!IMPORTANT]
> Előfordulhat, hogy az ügyfeleknek frissíteniük kell az alkalmazás (oka) t a módosítás után, hogy meggátolják a kapcsolódási hibákat az Azure-szolgáltatásokhoz való kapcsolódási kísérlet során.

## <a name="what-is-changing"></a>Mi változik?

Manapság az Azure-szolgáltatások által használt TLS-tanúsítványok többsége a következő legfelső szintű HITELESÍTÉSSZOLGÁLTATÓhoz kapcsolódik:

| A HITELESÍTÉSSZOLGÁLTATÓ köznapi neve | Ujjlenyomat (SHA1) |
|--|--|
| [Baltimore CyberTrust-gyökér](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Az Azure-szolgáltatások által használt TLS-tanúsítványok a következő legfelső szintű hitelesítésszolgáltatók egyikére lesznek felfűzve:

| A HITELESÍTÉSSZOLGÁLTATÓ köznapi neve | Ujjlenyomat (SHA1) |
|--|--|
| [DigiCert globális root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust-gyökér](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA legfelső szintű hitelesítésszolgáltató 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC legfelső szintű hitelesítésszolgáltató 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Mikor lehet kivonni a régi közbenső ujjlenyomatot?

Az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat a rendszer a 2021. február 15-ig *nem* vonja vissza. Ezen időpont után eltávolíthatja a régi ujjlenyomatai megfelelnek a kódból.

Ha ez a dátum változik, a rendszer értesítést küld az új visszavonási dátumról.

## <a name="will-this-change-affect-me"></a>Hatással lesz a változás? 

Várható, hogy **a legtöbb Azure-ügyfél nem** lesz hatással.  Előfordulhat azonban, hogy az alkalmazása érintett, ha explicit módon megadja az elfogadható hitelesítésszolgáltatók listáját. Ezt a gyakorlatot tanúsítvány-rögzítésnek nevezzük.

Az alábbi módokon derítheti fel, hogy az alkalmazás érintett-e:

- Keresse meg az [itt](https://www.microsoft.com/pki/mscorp/cps/default.htm)található Microsoft IT TLS-hitelesítésszolgáltatók ujjlenyomatát, köznapi nevét és egyéb tanúsítvány-tulajdonságait. Ha egyezés van, akkor az alkalmazás hatással lesz rá. A probléma megoldásához frissítse a forráskódot az új hitelesítésszolgáltatók közé. Ajánlott eljárásként győződjön meg arról, hogy a CAs-t rövid felmondással lehet felvenni vagy szerkeszteni. Az iparági szabályozások megkövetelik, hogy a CA-tanúsítványokat hét napon belül le lehessen cserélni, ezért a rögzítésre szoruló ügyfeleknek gyors reagálásra van szükségük.

- Ha olyan alkalmazással rendelkezik, amely integrálható az Azure API-kkal vagy más Azure-szolgáltatásokkal, és nem biztos benne, hogy tanúsítvány-rögzítést használ, ellenőrizze az alkalmazás gyártójával.

- Az Azure-szolgáltatásokkal kommunikáló különböző operációs rendszerek és nyelvi futtatókörnyezetek további lépéseket igényelhetnek a tanúsítványlánc megfelelő felépítéséhez az új gyökerekkel:
    - **Linux**: számos disztribúcióhoz szükséges a CAs hozzáadása a/etc/SSL/certs.-hez A konkrét utasításokért tekintse meg a terjesztés dokumentációját.
    - **Java**: Győződjön meg arról, hogy a Java-kulcs tárolója tartalmazza a fent felsorolt CAs-t.
    - **Leválasztott környezetekben futó Windows** esetén: a leválasztott környezetekben futó rendszereknek hozzá kell adni a megbízható legfelső szintű hitelesítésszolgáltatók tárolójához hozzáadott új gyökereket, valamint a köztes hitelesítésszolgáltatók tárolójához hozzáadott köztes adatkészleteket.
    - **Android**: keresse meg az eszköz és az Android-verzió dokumentációját.
    - **Egyéb hardvereszközök, különösen a IoT**: forduljon az eszköz gyártójához.

- Ha olyan környezettel rendelkezik, amelyben a tűzfalszabályok úgy vannak beállítva, hogy engedélyezze a kimenő hívásokat a visszavont tanúsítványok listájának (CRL) letöltésére és/vagy az online tanúsítványállapot-protokoll (OCSP) ellenőrzési helyeire. A következő CRL-eket és az OCSP URL-címeket is engedélyeznie kell:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d – megbízhatóság&#46;net
    - http://root-c3-ca2-2009&#46; OCSP&#46;d-Trust&#46;net
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com
    - http://www&#46; Microsoft&#46;com/pkiops

## <a name="next-steps"></a>Következő lépések

Ha további kérdése van, vegye fel velünk a kapcsolatot a [támogatási szolgálattal](https://azure.microsoft.com/support/options/).
