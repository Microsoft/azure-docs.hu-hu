---
title: Az Azure-Application Gateway kölcsönös hitelesítésének hibáinak megoldása
description: Megtudhatja, hogyan lehet elhárítani a Application Gateway kölcsönös hitelesítését
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231431"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Kölcsönös hitelesítési hibák elhárítása Application Gateway (előzetes verzió)

Ismerje meg, hogyan oldhatja meg a kölcsönös hitelesítéssel kapcsolatos problémákat Application Gateway használatakor. 

## <a name="overview"></a>Áttekintés 

Egy Application Gateway kölcsönös hitelesítésének konfigurálását követően számos hiba jelenhet meg a kölcsönös hitelesítés használatakor. A hibák gyakori okai a következők:

* Tanúsítvány vagy tanúsítványlánc feltöltése legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nélkül
* Több legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal rendelkező tanúsítványlánc feltöltése
* Olyan tanúsítványláncot töltött fel, amely csak HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nélküli levél-tanúsítványt foglalt le 
* Érvényesítési hibák a kiállító megkülönböztető neve miatt  

Áttekintjük a különböző forgatókönyveket, amelyekkel Ön is futhat, és elháríthatja ezeket a forgatókönyveket. Ezután a hibakódok és az egyes hibakódok valószínű okainak magyarázatával találkozhat a kölcsönös hitelesítéssel. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Forgatókönyvek hibaelhárítása – konfigurációs problémák
Előfordulhat, hogy a kölcsönös hitelesítés konfigurálására tett kísérlet során érdemes megnéznie a helyzetet. Bemutatjuk, hogyan lehet elhárítani a leggyakoribb buktatókat. 

### <a name="self-signed-certificate"></a>Önaláírt tanúsítvány

#### <a name="problem"></a>Probléma 

A feltöltött ügyféltanúsítvány egy önaláírt tanúsítvány, amely a ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate hibakódot eredményezi.

#### <a name="solution"></a>Megoldás 

Ellenőrizze, hogy a használt önaláírt tanúsítvány rendelkezik-e a *BasicConstraintsOid* = "2.5.29.19" bővítménnyel, amely azt jelzi, hogy a tulajdonos hitelesítésszolgáltatóként működhet. Ezzel biztosíthatja, hogy a használt tanúsítvány HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány legyen. Az önaláírt Ügyféltanúsítványok létrehozásával kapcsolatos további információkért tekintse meg a [megbízható Ügyféltanúsítványok](./mutual-authentication-certificate-management.md)című témakört.

## <a name="scenario-troubleshooting---connectivity-problems"></a>Forgatókönyv hibaelhárítása – kapcsolódási problémák

Lehetséges, hogy a kölcsönös hitelesítést bármilyen probléma nélkül konfigurálta, de problémákba ütközhet, amikor kéréseket küld a Application Gateway. A következő szakasz néhány gyakori problémáját és megoldását tárgyaljuk. A *sslClientVerify* tulajdonságot a Application Gateway hozzáférési naplóiban találja. 

### <a name="sslclientverify-is-none"></a>A SslClientVerify nincs

#### <a name="problem"></a>Probléma 

A *sslClientVerify* tulajdonság "None" értékként jelenik meg a hozzáférési naplókban. 

#### <a name="solution"></a>Megoldás 

Ez akkor jelenik meg, ha az ügyfél nem küld ügyféltanúsítványt, amikor kérelmet küld a Application Gateway. Ez akkor fordulhat elő Application Gateway, ha a kérést küldő ügyfél nem megfelelően van konfigurálva az Ügyféltanúsítványok használatára. Az alábbi OpenSSL-paranccsal ellenőrizheti, hogy Application Gateway ügyfél-hitelesítés beállítása a várt módon működik-e:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

A `-cert` jelző a levél tanúsítványa, a `-key` jelző pedig az ügyfél titkos kulcsának fájlja. 

Az OpenSSL parancs használatával kapcsolatos további információkért `s_client` tekintse meg a [manuális lapot](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html).

### <a name="sslclientverify-is-failed"></a>A SslClientVerify sikertelen

#### <a name="problem"></a>Probléma

A *sslClientVerify* tulajdonság "sikertelen" értékként jelenik meg a hozzáférési naplókban. 

#### <a name="solution"></a>Megoldás

A hozzáférési naplók számos lehetséges hibát okozhatnak. Az alábbi lista a hiba gyakori okait sorolja fel:
* **Nem sikerült beolvasni a kiállítói tanúsítványt:** Nem található az ügyféltanúsítvány kiállítói tanúsítványa. Ez általában azt jelenti, hogy a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának lánca nem fejeződött be a Application Gateway. Ellenőrizze, hogy befejeződött-e a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc feltöltése a Application Gateway.  
* **Nem sikerült beolvasni a helyi kiállítói tanúsítványt:** A kiállítói tanúsítvány lekéréséhez hasonlóan nem található az ügyféltanúsítvány kiállítói tanúsítványa. Ez általában azt jelenti, hogy a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának lánca nem fejeződött be a Application Gateway. Ellenőrizze, hogy befejeződött-e a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc feltöltése a Application Gateway.
* **Nem sikerült ellenőrizni az első tanúsítványt:** Nem sikerült ellenőrizni az ügyféltanúsítványt. Ez a hiba kifejezetten akkor fordul elő, ha az ügyfél csak a levél tanúsítványát mutatja be, amelynek a kiállítója nem megbízható. Ellenőrizze, hogy befejeződött-e a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc feltöltése a Application Gateway.
* **Nem sikerült ellenőrizni az ügyféltanúsítvány kiállítóját:** Ez a hiba akkor fordul elő, ha a konfigurációs *VerifyClientCertIssuerDN* értéke TRUE (igaz). Ez általában akkor fordul elő, ha az ügyféltanúsítvány kiállítói megkülönböztető neve nem egyezik meg az ügyfél által feltöltött megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványláncből kinyert *ClientCertificateIssuerDN* . A *ClientCertificateIssuerDN* Application Gateway kibontásával kapcsolatos további információkért tekintse meg [Application Gateway Kinyeri a kiállító megkülönböztető](./mutual-authentication-overview.md#verify-client-certificate-dn)nevét. Ajánlott eljárásként győződjön meg róla, hogy fájlon keresztül tölt fel egy tanúsítványláncot Application Gatewayra. 

Ha további információt szeretne arról, hogyan bontsa ki a teljes megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványláncot a Application Gatewayba való feltöltéshez, olvassa el a [megbízható ügyfelek hitelesítésszolgáltatói tanúsítványainak kinyerése](./mutual-authentication-certificate-management.md)című témakört.

## <a name="error-code-troubleshooting"></a>Hibakód hibaelhárítása
Ha a következő hibakódok valamelyikét látja, néhány ajánlott megoldásunk segít feloldani az esetlegesen felmerülő problémát. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Hibakód: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Ok

Nincs hiányzó tanúsítvány. A feltöltött tanúsítvány lehet üres fájl, amely nem tartalmaz tanúsítványokat. 

#### <a name="solution"></a>Megoldás

Ellenőrizze, hogy a feltöltött tanúsítványfájl nem rendelkezik-e hiányzó adattal. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Hibakód: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Ok

A tanúsítványlánc rendelkezik egy titkos kulccsal. A tanúsítványlánc nem lehet titkos kulcs. 

#### <a name="solution"></a>Megoldás

Ellenőrizze a feltöltött tanúsítványláncot, és távolítsa el a lánc részét képező titkos kulcsot. Töltse fel újra a láncot a titkos kulcs nélkül. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Hibakód: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Ok

Ennek a hibakódnak két lehetséges oka van.
1. Az elemzés nem sikerült, mert a lánc nem a megfelelő formátumban jelenik meg. Application Gateway azt várja, hogy a tanúsítványlánc PEM formátumú legyen, és az egyes tanúsítvány-adatok tagolása is várható. 
2. Az elemző nem talált semmit az elemzéshez. A feltöltött fájl valószínűleg csak elhatárolókkal rendelkezhet, de nem tartalmaz tanúsítványokat. 

#### <a name="solution"></a>Megoldás

A hiba okának függvényében két lehetséges megoldás létezik. 
* Ellenőrizze, hogy a feltöltött tanúsítványlánc megfelelő formátumú-e (PEM), és hogy a tanúsítványhoz tartozó adatmennyiség megfelelően tagolva lett-e. 
* Győződjön meg arról, hogy a feltöltött tanúsítványfájl az elhatárolók mellett a tanúsítványra vonatkozó adatok mellett is szerepelt. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Hibakód: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Ok

A feltöltött tanúsítvány csak a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nélküli levél tanúsítványát tárolja. HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokkal rendelkező tanúsítványlánc feltöltése és a levél tanúsítványa elfogadható, mivel a levél tanúsítványa csak figyelmen kívül hagyható, de a tanúsítványnak rendelkeznie kell HITELESÍTÉSSZOLGÁLTATÓval. 

#### <a name="solution"></a>Megoldás 

Ellenőrizze, hogy a feltöltött tanúsítványlánc nem csupán a levél tanúsítványát használta-e. A *BasicConstraintsOid* = "2.5.29.19" kiterjesztésnek jelen kell lennie, és jeleznie kell, hogy a tárgy működhet hitelesítésszolgáltatóként.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Hibakód: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Ok

A tanúsítványlánc több legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt tartalmaz *, vagy* nulla legfelső szintű hitelesítésszolgáltatói tanúsítványokat tartalmaz. 

#### <a name="solution"></a>Megoldás

A feltöltött tanúsítványoknak pontosan egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt kell tartalmazniuk (és azonban sok köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra van szükség).


