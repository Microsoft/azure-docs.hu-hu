---
title: Az Azure cache Redis újdonságai
description: Az Azure cache legújabb frissítései a Redis-hez
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91492450"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Az Azure cache Redis újdonságai

## <a name="azure-tls-certificate-change"></a>Azure TLS-tanúsítvány módosítása

A Microsoft frissíti az Azure-szolgáltatásokat, hogy a TLS-kiszolgálói tanúsítványokat egy másik hitelesítésszolgáltatótól (CA) használják. Ez a változás a 2020-es és a 2020-es (becsült) október 26. és 9. közötti fázisban van. Az Azure ezt a változást teszi, mert [az aktuális hitelesítésszolgáltatói tanúsítványok nem felelnek meg a CA/Browser fórum](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)alapkonfigurációjának követelményeinek. A probléma a 2020. július 1-jén jelent meg, és a világszerte több népszerű nyilvános kulcsú infrastruktúra (PKI) szolgáltatóra vonatkozik. Az Azure-szolgáltatások által használt legtöbb TLS-tanúsítvány a *Baltimore CyberTrust legfelső szintű* PKI-től származik. A Redis Service-hez készült Azure cache továbbra is a Baltimore CyberTrust gyökerére lesz láncolva. A TLS-kiszolgálói tanúsítványokat azonban új közbenső hitelesítésszolgáltatók (nemzetközi árumegállapodások vagy-EK) adják ki, amely a 2020. október 12-én kezdődik.

> [!NOTE]
> Ez a változás a nyilvános [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/geographies/)lévő szolgáltatásokra korlátozódik. Kizárja a szuverén (pl. Kína) vagy a kormányzati felhők körét.
>
>

### <a name="does-this-change-affect-me"></a>Befolyásolja a változás a változást?

Várható, hogy a változás nem érinti a legtöbb Azure cache-t a Redis-ügyfelek számára. Előfordulhat, hogy az alkalmazása érintett, ha explicit módon megadja az elfogadható tanúsítványok listáját, amely a "tanúsítvány-rögzítés" néven ismert. Ha a Baltimore CyberTrust-gyökér helyett közbenső vagy levél típusú tanúsítványra van rögzítve, **azonnali műveleteket kell végrehajtania** a tanúsítvány konfigurációjának módosításához.

Az alábbi táblázat a behengerelt tanúsítványokról tartalmaz információkat. Az alkalmazás által használt tanúsítványtól függően előfordulhat, hogy frissítenie kell a Redis-példányhoz tartozó Azure cache-hez való kapcsolódás elvesztésének megakadályozásához.

| HITELESÍTÉSSZOLGÁLTATÓ típusa | Aktuális | Küldés utáni (12. október 2020) | Művelet |
| ----- | ----- | ----- | ----- |
| Gyökér | Ujjlenyomat: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Lejárat: hétfő, május 12., 2025, 4:59:00 PM<br><br> Tulajdonos neve:<br> CN = Baltimore CyberTrust gyökér<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Nem változik | Nincsenek |
| Intermedierek | Ujjlenyomatai megfelelnek<br> CN = Microsoft IT TLS CA 1<br> Ujjlenyomat: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Ujjlenyomat: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Ujjlenyomat: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Ujjlenyomat: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Lejárat: péntek, május 20., 2024 5:52:38<br><br> Tulajdonos neve:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = USA<br> | Ujjlenyomatai megfelelnek<br> CN = Microsoft RSA TLS CA 01<br> Ujjlenyomat: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> Ujjlenyomat: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Lejárat: kedd, október 8, 2024 12:00:00 AM;<br><br> Tulajdonos neve:<br> O = Microsoft Corporation<br> C = USA<br> | Kötelező |

### <a name="what-actions-should-i-take"></a>Milyen műveleteket kell elvégeznie?

Ha az alkalmazás az operációs rendszer tanúsítványtárolóját használja, vagy a Baltimore gyökerét többek között PIN-kód fölé állítja, nincs szükség beavatkozásra. Ha azonban az alkalmazása bármilyen közbenső vagy levél TLS-tanúsítványt rögzít, javasoljuk, hogy a következő gyökereket adja meg:

| Tanúsítvány | Ujjlenyomat |
| ----- | ----- |
| [Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA legfelső szintű hitelesítésszolgáltató 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert globális root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> A köztes és a levélbeli tanúsítványokat is gyakran kell módosítani. Azt javasoljuk, hogy ne vegyen függőséget. Ehelyett rögzítse az alkalmazást egy főtanúsítványhoz, mert ritkábban görgeti.
>
>

A köztes tanúsítványok rögzítésének folytatásához adja hozzá a következőt a rögzített köztes tanúsítványok listájához, amely néhány továbbiat tartalmaz a jövőbeli változások minimalizálásához:

| A HITELESÍTÉSSZOLGÁLTATÓ köznapi neve | Ujjlenyomat |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS kiállító CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS kiállító CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS kiállító CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS kiállító HITELESÍTÉSSZOLGÁLTATÓ 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Ha az alkalmazás egy kódban érvényesíti a tanúsítványt, módosítania kell azt az újonnan rögzített tanúsítványok tulajdonságainak (például kiállítóinak, ujjlenyomatának) felismeréséhez. Ennek a további ellenőrzésnek az összes rögzített tanúsítványra ki kell terjednie, hogy a jövőben még nagyobb legyen.

## <a name="next-steps"></a>Következő lépések

Ha további kérdése van, vegye fel velünk a kapcsolatot a [támogatási szolgálattal](https://azure.microsoft.com/support/options/).  
