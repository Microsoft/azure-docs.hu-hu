---
title: Oktatóanyag – Az X.509 nyilvános kulcsú tanúsítványok Azure IoT Hub| Microsoft Docs
description: Oktatóanyag – Az X.509 nyilvános kulcsú tanúsítványok Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378346"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Oktatóanyag: Az X.509 nyilvános kulcsú tanúsítványok ismertetése

Az X.509-tanúsítványok felhasználók, számítógépek, szolgáltatások vagy eszközök digitális dokumentumai. A tanúsítványokat egy hitelesítésszolgáltató (CA), alárendelt CA vagy regisztrációs szervezet bocsátotta ki, és tartalmazzák a tanúsítvány tulajdonosának nyilvános kulcsát. Nem tartalmazzák a tulajdonos titkos kulcsát, amelyet biztonságosan kell tárolni. A nyilvános kulcsú tanúsítványokat az [RFC 5280 dokumentálja.](https://tools.ietf.org/html/rfc5280) Digitális aláírással vannak aláírva, és általában a következő információkat tartalmazzák:

* A tanúsítvány tulajdonosának adatai
* A tulajdonos titkos kulcsának megfelelő nyilvános kulcs
* A kiállító hitelesítésszolgáltatóval kapcsolatos információk
* A támogatott titkosítási és/vagy digitális aláírási algoritmusok
* A tanúsítvány visszavonási és érvényességi állapotának megállapításához szükséges információk

## <a name="certificate-fields"></a>Tanúsítványmezők

Az idő alatt három tanúsítványverzió volt elérhető. Mindegyik verzió mezőket ad hozzá az előzőhöz. A 3.verzió aktuális, és a 3. verzió mezői mellett az 1. és a 2. verzió mezőit is tartalmazza. Az 1. verzió a következő mezőket definiálta:

* **Verzió:** A tanúsítvány verziószámát azonosító érték (1, 2 vagy 3)
* **Sorozatszám:** A CA által kiadott összes tanúsítvány egyedi száma
* **CA aláírási algoritmus:** Annak az algoritmusnak a neve, amely alapján a CA aláírja a tanúsítvány tartalmát
* **Kiállító neve:** A tanúsítvány kiállító hitelesítésszolgáltatójának megkülönböztető neve (DN).
* **Érvényességi időtartam:** Az az időtartam, amely alatt a tanúsítvány érvényesnek minősül
* **Tulajdonos neve:** A tanúsítvány által képviselt entitás neve
* **Tulajdonos nyilvános kulcsának adatai:** A tanúsítvány tulajdonosának tulajdonában lévő nyilvános kulcs

A 2. verzió a következő mezőket adott hozzá, amelyek a tanúsítványkiállítóval kapcsolatos információkat tartalmaznak. Ezeket a mezőket azonban ritkán használják.

* **Kiállító egyedi azonosítója:** A kiállító hitelesítésszolgáltató egyedi azonosítója a hitelesítésszolgáltató által meghatározott módon
* **Tulajdonos egyedi azonosítója:** A kiállító hitelesítésszolgáltató által meghatározott tanúsítvány tulajdonosának egyedi azonosítója

A 3-as verziójú tanúsítványok a következő bővítményeket adtak hozzá:

* **Hitelesítésszolgáltatói** kulcs azonosítója: Ez két érték egyike lehet:
  * A tanúsítványt kibocsátó hitelesítésszolgáltatói tanúsítvány tulajdonosának és sorozatszámának száma
  * A tanúsítványt kibocsátó hitelesítésszolgáltató nyilvános kulcsának kivonata
* **Tulajdonoskulcs azonosítója:** Az aktuális tanúsítvány nyilvános kulcsának kivonata
* **Kulcshasználat** Meghatározza a szolgáltatást, amelyhez tanúsítvány használható. Ez a következő értékek közül egy vagy több lehet:
  * **Digitális aláírás**
  * **Nem megtagadás**
  * **Kulcstitkosítás**
  * **Adattűnés**
  * **Kulcsszerződés**
  * **Kulcs tanúsítványának előjele**
  * **CRL-aláírás**
  * **Csak titkosítás**
  * **Csak titkosítás**
* **Titkos kulcs használati időtartama:** A kulcspár titkos kulcsrészének érvényességi időtartama
* **Tanúsítvány-házirendek:** A tanúsítvány tulajdonosának érvényesítéséhez használt házirendek
* **Szabályzatleképezések:** Leképezi egy szervezet szabályzatát egy másik szervezet szabályzataira
* **Tulajdonos alternatív neve:** A tulajdonos alternatív neveinek listája
* **Kiállító alternatív neve:** A kiállító hitelesítésszolgáltató alternatív neveinek listája
* **Tulajdonos Dir attribútuma:** X.500- vagy LDAP-címtár attribútumai
* **Alapszintű korlátozások:** Lehetővé teszi a tanúsítvány számára annak jelölését, hogy egy hitelesítésszolgáltatónak, illetve felhasználónak, számítógépnek, eszköznek vagy szolgáltatásnak van-e kibocsátva. Ez a bővítmény elérésiút-hosszkorlátozást is tartalmaz, amely korlátozza a létezhet alárendelt HITELESÍTÉS-k számát.
* **Névkorlátozások:** Azt jelöli, hogy mely névterek engedélyezettek a hitelesítésszolgáltató által kiállított tanúsítványokban
* **Házirendkorlátozások:** A CA-k közötti szabályzatleképezések tiltása érdekében használható
* **Kibővített kulcshasználat:** Azt jelzi, hogy a tanúsítvány nyilvános kulcsa hogyan használható a kulcshasználati bővítményben meghatározott **célokon** túl
* **CRL terjesztési pontok:** Egy vagy több OLYAN URL-t tartalmaz, ahol a visszavont tanúsítványok alaplistát (CRL) közzétették
* **AnyPolicy akadályozása:** Meggátolja  az összes kiállítási házirend OID (2.5.29.32.0) használatát az alárendelt CA-tanúsítványokban
* **Legfrissebb CRL:** Egy vagy több olyan URL-t tartalmaz, ahol a kiállító hitelesítésszolgáltató különbözeti CRL-fájlja közzé van téve
* **Hitelesítésszolgáltatói információkhoz** való hozzáférés: Egy vagy több olyan URL-cím, ahol a kiállító hitelesítésszolgáltatói tanúsítvány közzé van téve
* **Tulajdonosi információkhoz való** hozzáférés: A tanúsítvány tulajdonosának további részleteinek lekérését tartalmazza

## <a name="certificate-formats"></a>Tanúsítványformátumok

A tanúsítványok többféle formátumban menthetők. Azure IoT Hub hitelesítés általában PEM- és PFX-formátumokat használ.

### <a name="binary-certificate"></a>Bináris tanúsítvány

Ez egy der ASN.1 kódolást használó nyers bináris tanúsítványt tartalmaz.

### <a name="ascii-pem-format"></a>ASCII PEM formátum

A PEM-tanúsítvány (.pem kiterjesztés) tartalmaz egy base64 kódolású tanúsítványt, amely -----BEGIN TANÚSÍTVÁNYsal kezdődik----- és -----END CERTIFICATE végződésű-----. A PEM formátum nagyon gyakori, és a IoT Hub szükséges bizonyos tanúsítványok feltöltésekor.

### <a name="ascii-pem-key"></a>ASCII- (PEM-) kulcs

Egy base64 kódolású DER kulcsot tartalmaz, amely valószínűleg további metaadatokat tartalmaz a jelszóvédelemhez használt algoritmusról.

### <a name="pkcs7-certificate"></a>PKCS #7 tanúsítvány

Aláírt vagy titkosított adatok szállításához tervezett formátum. Ezt az [RFC 2315 határozza meg.](https://tools.ietf.org/html/rfc2315) A teljes tanúsítványláncot tartalmazhatja.

### <a name="pkcs8-key"></a>PKCS#8 kulcs

Az [RFC 5208](https://tools.ietf.org/html/rfc5208)által meghatározott titkos kulcstároló formátuma.

### <a name="pkcs12-key-and-certificate"></a>PKCS #12 kulcs és tanúsítvány

Egy összetett formátum, amely tárolhatja és megvédheti a kulcsokat és a teljes tanúsítványláncot. Gyakran használják .pfx kiterjesztéssel. PKCS #12 a PFX formátum szinonimája.

## <a name="for-more-information"></a>További tudnivalók

További információkért tekintse át a következők témaköröket:

* [A layman útmutatója az X.509-tanúsítvány szakzsargonhoz](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Az X.509-es hitelesítésszolgáltatói tanúsítványok fogalmi ismerete az IoT-iparágban](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Következő lépések

Ha olyan teszttanúsítványokat szeretne létrehozni, amelyek segítségével eszközöket hitelesíthet a IoT Hub, tekintse meg a következő témaköröket:

* [Teszttanúsítványok Microsoft-Supplied szkriptek használatával](tutorial-x509-scripts.md)
* [Teszttanúsítványok létrehozása openSSL használatával](tutorial-x509-openssl.md)
* [OpenSSL használata teszttanúsítványok Self-Signed létrehozásához](tutorial-x509-self-sign.md)

Ha hitelesítésszolgáltatói (CA) tanúsítvánnyal vagy alárendelt CA-tanúsítvánnyal rendelkezik, és fel szeretné tölteni azt az IoT Hubra, és igazolni szeretné, hogy Ön a hitelesítésszolgáltatói tanúsítvány a birtokában van, lásd: Hitelesítésszolgáltatói tanúsítvány [birtoklásának igazolása.](tutorial-x509-prove-possession.md)
