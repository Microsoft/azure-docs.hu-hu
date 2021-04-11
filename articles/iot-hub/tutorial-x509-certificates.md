---
title: Oktatóanyag – az X. 509 nyilvános kulcsú tanúsítványok ismertetése az Azure IoT Hubhoz | Microsoft Docs
description: Oktatóanyag – az X. 509 nyilvános kulcsú tanúsítványok ismertetése az Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: fada68ba395b959e557542eb8c230561aad84214
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384323"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Oktatóanyag: az X. 509 nyilvános kulcsú tanúsítványok ismertetése

Az X. 509 tanúsítványok olyan digitális dokumentumok, amelyek egy felhasználót, számítógépet, szolgáltatást vagy eszközt képviselnek. Ezeket hitelesítésszolgáltató (CA), alárendelt HITELESÍTÉSSZOLGÁLTATÓ vagy regisztrációs szolgáltató állítja ki, és tartalmazzák a tanúsítvány tulajdonosának nyilvános kulcsát. Nem tartalmazzák a tulajdonos titkos kulcsát, amelyet biztonságosan kell tárolni. A nyilvános kulcsú tanúsítványokat az [RFC 5280](https://tools.ietf.org/html/rfc5280)dokumentálja. Ezek digitális aláírással rendelkeznek, és általánosságban a következő információkat tartalmazzák:

* A tanúsítvány tárgyával kapcsolatos információk
* A tulajdonos titkos kulcsának megfelelő nyilvános kulcs
* A kiállító HITELESÍTÉSSZOLGÁLTATÓra vonatkozó információk
* A támogatott titkosítási és/vagy digitális aláírási algoritmusok
* A tanúsítvány visszavonási és érvényességi állapotának meghatározására szolgáló információk

## <a name="certificate-fields"></a>Tanúsítvány mezői

Az idő múlásával három tanúsítvány létezik. Az egyes verziók mezőket tesznek hozzá. A 3-as verzió a jelenlegi, és a 3. verziójú mezők mellett tartalmazza az 1. és a 2. verziójú mezőket is. Az 1. verzió a következő mezőket definiálta:

* **Verzió**: egy érték (1, 2 vagy 3), amely a tanúsítvány verziószámát azonosítja.
* **Sorozatszám**: egyedi szám a hitelesítésszolgáltató által kiállított minden egyes tanúsítványhoz
* **Hitelesítésszolgáltatói aláírási algoritmus**: a hitelesítésszolgáltató által a tanúsítvány tartalmának aláírásához használt algoritmus neve
* **Kiállító neve**: a tanúsítvány kiállító hitelesítésszolgáltatójának megkülönböztető neve (DN)
* **Érvényességi időszak**: az az időtartam, ameddig a tanúsítvány érvényesnek minősül
* **Tulajdonos neve**: a tanúsítvány által jelölt entitás neve
* **Tulajdonos nyilvános kulcsának adatai**: a tanúsítvány tárgyát képező nyilvános kulcs

A 2. verzió a tanúsítvány kiállítójának adatait tartalmazó következő mezőket adta hozzá. Ezeket a mezőket azonban ritkán használják.

* **Kiállító egyedi azonosítója**: a kiállító hitelesítésszolgáltató egyedi azonosítója a hitelesítésszolgáltató által meghatározott módon
* **Tulajdonos egyedi azonosítója**: a tanúsítvány tulajdonosának a kiállító hitelesítésszolgáltató által meghatározott egyedi azonosítója

A 3. verziójú tanúsítvány a következő bővítményeket adta hozzá:

* **Hitelesítésszolgáltató kulcsának azonosítója**: Ez a következő két érték egyike lehet:
  * A tanúsítványt kiállító hitelesítésszolgáltatói tanúsítvány tárgya és sorozatszáma
  * A tanúsítványt kiállító HITELESÍTÉSSZOLGÁLTATÓ nyilvános kulcsának kivonata
* **Tulajdonosi kulcs azonosítója**: az aktuális tanúsítvány nyilvános kulcsának kivonata
* **Kulcshasználat** Meghatározza azt a szolgáltatást, amelyhez tanúsítvány használható. Ez a következő értékek közül egy vagy több lehet:
  * **Digitális aláírás**
  * **Nem megtagadási**
  * **Kulcstitkosítás**
  * **Az adattitkosítási**
  * **Kulcsfontosságú szerződés**
  * **Kulcs tanúsítványának aláírása**
  * **CRL-aláírás**
  * **Csak jellemzően**
  * **Csak visszafejtés**
* **Titkos kulcs használati időtartama**: kulcspár titkos kulcsának érvényességi ideje
* **Tanúsítvány-házirendek**: a tanúsítvány tulajdonosának ellenőrzéséhez használt házirendek
* **Szabályzat-hozzárendelések**: az egyik szervezet szabályzatának leképezése egy másikra
* **Tulajdonos alternatív neve**: a tárgy alternatív neveinek listája
* **Kiállító alternatív neve**: a kiállító hitelesítésszolgáltató alternatív neveinek listája
* **Tulajdonos dir attribútum**: X. 500 vagy LDAP könyvtár attribútumai
* **Alapszintű megkötések**: lehetővé teszi a tanúsítvány számára, hogy kiadja azt a hitelesítésszolgáltatónak, illetve egy felhasználó, számítógép, eszköz vagy szolgáltatás számára. Ez a bővítmény egy elérésiút-hossz korlátozást is tartalmaz, amely korlátozza a létezhető alárendelt hitelesítésszolgáltatók számát.
* **Nevek megkötései**: kijelöli, hogy mely névterek ENGEDÉLYEZETTEK a hitelesítésszolgáltató által kiállított tanúsítványokban
* **Házirend-korlátozások**: a hitelesítésszolgáltatók közötti szabályzat-hozzárendelések tiltására használható.
* **Kibővített kulcshasználat**: azt jelzi, hogyan használható a tanúsítvány nyilvános kulcsa a **kulcshasználati** bővítményben azonosított célokon túl
* **CRL-terjesztési pontok**: egy vagy több olyan URL-címet tartalmaz, ahol a rendszer közzéteszi az Alaptanúsítvány-visszavonási listát (CRL).
* **AnyPolicy gátlása**: az **összes kiállítási szabályzat** OID (2.5.29.32.0) használatának gátlása alárendelt hitelesítésszolgáltatói tanúsítványokban
* **Legfrissebb CRL**: egy vagy több olyan URL-címet tartalmaz, ahol a kiállító hitelesítésszolgáltató különbözeti CRL-je közzé van téve
* **Szolgáltatói adatokhoz való hozzáférés**: egy vagy több olyan URL-címet tartalmaz, ahol a kiállító hitelesítésszolgáltatói tanúsítvány közzé van téve
* **Tulajdonosi információhoz való hozzáférés**: a tanúsítvány tárgya további részleteinek beolvasásával kapcsolatos információkat tartalmaz.

## <a name="certificate-formats"></a>Tanúsítványok formátuma

A tanúsítványok különböző formátumokban menthetők. Az Azure IoT Hub-hitelesítés általában a PEM-és PFX-formátumot használja.

### <a name="binary-certificate"></a>Bináris tanúsítvány

Ez egy nyers formátumú bináris tanúsítványt tartalmaz a DER ASN. 1 kódolás használatával.

### <a name="ascii-pem-format"></a>ASCII PEM-formátum

A PEM-tanúsítvány (. PEM kiterjesztésű) egy Base64 kódolású tanúsítványt tartalmaz, amely a-----BEGIN CERTIFICATe-----és a-----END-tanúsítvánnyal végződik-----. A PEM formátuma nagyon gyakori, és a IoT Hub bizonyos tanúsítványok feltöltésekor szükséges.

### <a name="ascii-pem-key"></a>ASCII-(PEM-) kulcs

Base64-kódolású DER-kulcsot tartalmaz, amely esetleg további metaadatokkal rendelkezik a jelszavas védelemhez használt algoritmussal kapcsolatban.

### <a name="pkcs7-certificate"></a>PKCS # 7 tanúsítvány

Az aláírt vagy titkosított adatátvitelre szolgáló formátum. Ezt az [RFC 2315](https://tools.ietf.org/html/rfc2315)határozza meg. Ez magában foglalhatja a teljes tanúsítványláncot is.

### <a name="pkcs8-key"></a>PKCS # 8 kulcs

Az [RFC 5208](https://tools.ietf.org/html/rfc5208)által definiált titkos kulcs tárolójának formátuma.

### <a name="pkcs12-key-and-certificate"></a>PKCS # 12 kulcs és tanúsítvány

Egy összetett formátum, amely képes tárolni és védelemmel ellátni a kulcsot és a teljes tanúsítványláncot. Általában. pfx kiterjesztéssel használják. A PKCS # 12 a PFX formátum szinonimája.

## <a name="for-more-information"></a>További tudnivalók

További információkért tekintse át a következők témaköröket:

* [A laikusok útmutatója az X. 509 tanúsítvány szakzsargonhoz](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok fogalmi megértése a IoT-iparágban](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Következő lépések

Ha olyan tesztelési tanúsítványokat szeretne előállítani, amelyeket az eszközök a IoT Hub való hitelesítéséhez használhat, tekintse meg a következő témaköröket:

* [Microsoft-Supplied szkriptek használata tesztelési tanúsítványok létrehozásához](tutorial-x509-scripts.md)
* [Az OpenSSL használata tesztelési tanúsítványok létrehozásához](tutorial-x509-openssl.md)
* [Az OpenSSL használata Self-Signed tesztelési tanúsítványok létrehozásához](tutorial-x509-self-sign.md)

Ha hitelesítésszolgáltató (CA) tanúsítványa vagy alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa van, és fel szeretné tölteni a IoT hub-ba, és bizonyítania kell, hogy rendelkezik a [hitelesítésszolgáltató tanúsítványának](tutorial-x509-prove-possession.md)igazolásával.
