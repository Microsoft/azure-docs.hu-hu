---
title: Oktatóanyag – A titkosítás és az X.509-tanúsítványok Azure IoT Hub | Microsoft Docs
description: Oktatóanyag – A titkosítás és az X.509 PKI Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378380"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Oktatóanyag: A nyilvános kulcsú titkosítás és az X.509 nyilvános kulcsú infrastruktúra ismertetése

X.509-tanúsítványokkal hitelesítheti az eszközöket egy Azure IoT Hub. A tanúsítvány egy digitális dokumentum, amely tartalmazza az eszköz nyilvános kulcsát, és segítségével ellenőrizhető, hogy az eszköz az eszköz-e, amit állítása szerint használ. Az X.509-tanúsítványokat és a visszavont tanúsítványok listáit (CRL-eket) az [RFC 5280 dokumentálja.](https://tools.ietf.org/html/rfc5280) A tanúsítványok csak egy részét képezi az X.509 nyilvános kulcsú infrastruktúrának (PKI). Az X.509 PKI-nak a titkosítási algoritmusokat, a titkosítási kulcsokat, a tanúsítványokat és a hitelesítésszolgáltatókat (HITELESÍTÉSSZOLGÁLTATÓK) kell megértenie:

* **Az algoritmusok** határozzák meg, hogy az eredeti egyszerű szöveges adatok hogyan alakulnak át titkosítási szöveggé, majd vissza egyszerű szöveggé.
* **A** kulcsok véletlenszerű vagy pszeudo-véletlen adatsringek, amelyek egy algoritmus bemeneteként használatosak.
* **A** tanúsítványok olyan digitális dokumentumok, amelyek tartalmazzák az entitás nyilvános kulcsát, és lehetővé teszik annak megállapítását, hogy a tanúsítvány tárgya ki vagy mit jelent.
* **A hitelesítésszolgáltatók** igazolják a tanúsítványokkal rendelkező személyek hitelességét.

Tanúsítványt hitelesítésszolgáltatótól (CA) vásárolhat. Emellett teszteléshez és fejlesztéshez, vagy ha önálló környezetben dolgozik, létrehozhat egy önaírt legfelső szintű hitelesítésszolgáltatót. Ha például egy vagy több eszköze van, és IoT Hub-hitelesítést tesztel, önatanúsíthatja a legfelső szintű hitelesítésszolgáltatót, és használhatja ezt az eszköztanúsítványok kiállításához. Önaírt eszköztanúsítványokat is ki lehet írni. Erről a következő cikkekben lesz szó.

Mielőtt részletesebben megvitatja az X.509-tanúsítványokat, és felhasználja őket az eszközök hitelesítésére egy IoT Hub, a titkosítást, amelyen a tanúsítványok alapulnak.

## <a name="cryptography"></a>Titkosítás

A titkosítás az információk és a kommunikáció védelmére használható. Ez általában kriptográfiai technikákkal történik az egyszerű szöveg (normál szöveg) titkosítása titkosítatlan szöveggé (kódolt szöveggé), majd vissza. Ezt az akadály nélküli folyamatot titkosításnak nevezzük. A fordított folyamatot visszafejtésnek nevezzük. A titkosítás a következő célkitűzésekkel van kapcsolatban:

* **Titkosság:** Az információt csak a célközönség tudja értelmezni.
* **Integritás:** Az adatok nem módosíthatók a tárolóban vagy az átvitel során.
* **Letagadhatatlanság:** Az információ létrehozója később nem tagadhatja meg ezt a létrehozást.
* **Hitelesítés:** A küldő és a fogadó megerősítheti egymás identitását.

## <a name="encryption"></a>Titkosítás

A titkosítási folyamathoz algoritmusra és kulcsra van szükség. Az algoritmus határozza meg, hogy az adatok hogyan alakulnak át egyszerű szövegből titkosítási szöveggé, majd vissza egyszerű szöveggé. A kulcs egy véletlenszerű adatsring, amely az algoritmus bemeneteként van használva. A kulcs a folyamat összes biztonságát tartalmazza. Ezért a kulcsot biztonságosan kell tárolni. A legnépszerűbb algoritmusok részletei azonban nyilvánosan elérhetők.

Kétféle titkosítási típus létezik. A szimmetrikus titkosítás ugyanazt a kulcsot használja a titkosításhoz és a visszafejtéséhez is. Az aszimmetrikus titkosítás különböző, de matematikailag kapcsolódó kulcsokat használ a titkosítás és a visszafejtés végrehajtásához.

### <a name="symmetric-encryption"></a>Szimmetrikus titkosítás

A szimmetrikus titkosítás ugyanazt a kulcsot használja az egyszerű szöveg titkosításához és a titkosított szöveg visszafejtéséhez egyszerű szöveggé. A kulcs szükséges hosszát bitszámban kifejezve az algoritmus határozza meg. Miután a kulcsot egyszerű szöveg titkosítására használta, a titkosított üzenet el lesz küldve a címzettnek, aki ezután visszafejti a titkosított szöveget. A szimmetrikus kulcsot biztonságosan kell továbbítani a címzettnek. Szimmetrikus algoritmus használata esetén a legnagyobb biztonsági kockázat a kulcs elküldése.

![Szimmetrikus titkosítás – példa](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Aszimmetrikus titkosítás

Ha csak szimmetrikus titkosítást használ, a probléma az, hogy a kommunikációban részt vevő összes félnek rendelkeznie kell a titkos kulccsal. Előfordulhat azonban, hogy jogosulatlan külső felek is rögzítik a kulcsot a jogosult felhasználóknak történő átvitel során. A probléma megoldásához használjon inkább aszimmetrikus vagy nyilvános kulcsú titkosítást.

Az aszimmetrikus titkosításban minden felhasználónak két matematikailag kapcsolódó kulcsa van, az úgynevezett kulcspárja. Az egyik kulcs nyilvános, a másik titkos. A kulcspár biztosítja, hogy csak a címzett fér hozzá az adatok visszafejtéséhez szükséges titkos kulcshoz. Az alábbi ábra az aszimmetrikus titkosítási folyamatot foglalja össze.

![Aszimmetrikus titkosítási példa](media/tutorial-x509-introduction/asymmetric-keys.png)

1. A címzett létrehoz egy nyilvános-titkos kulcspárt, és elküldi a nyilvános kulcsot egy hitelesítésszolgáltatónak. A hitelesítésszolgáltató egy X.509-tanúsítványba csomagolása a nyilvános kulcsot.

1. A küldő fél a hitelesítésszolgáltatótól szerezheti be a címzett nyilvános kulcsát.

1. A küldő titkosítja az egyszerű szöveges adatokat egy titkosítási algoritmussal. A rendszer a címzett nyilvános kulcsát használja a titkosítás végrehajtásához.

1. A küldő továbbítja a titkosítási szöveget a címzettnek. Nem szükséges elküldeni a kulcsot, mert a címzett már rendelkezik a titkosított szöveg visszafejtéséhez szükséges titkos kulccsal.

1. A címzett a megadott aszimmetrikus algoritmus és a titkos kulcs használatával visszafejti a titkosított szöveget.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Szimmetrikus és aszimmetrikus titkosítás kombinálása

A szimmetrikus és aszimmetrikus titkosítás kombinálható, hogy kihasználja a relatív erősségeiket. A szimmetrikus titkosítás sokkal gyorsabb, mint az aszimmetrikus, de mivel titkos kulcsokat kell küldeni más feleknek, nem olyan biztonságos. A két típus összevonása szimmetrikus titkosítással konvertálható titkosítatlan szöveggé. Az aszimmetrikus titkosítás a szimmetrikus kulcs cseréjére használható. Ezt az alábbi ábra szemlélteti.

![Szimmetrikus és aszimmetrikus titkosítás](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. A küldő lekéri a címzett nyilvános kulcsát.

1. A küldő létrehoz egy szimmetrikus kulcsot, és annak segítségével titkosítja az eredeti adatokat.

1. A küldő a címzett nyilvános kulcsát használja a szimmetrikus kulcs titkosításához.

1. A küldő továbbítja a titkosított szimmetrikus kulcsot és a titkosított szöveget a címzettnek.

1. A címzett a címzett nyilvános kulcsának megfelelő titkos kulcsot használja a küldő szimmetrikus kulcsának visszafejtéséhez.

1. A címzett a szimmetrikus kulccsal visszafejti a titkosított szöveget.

### <a name="asymmetric-signing"></a>Aszimmetrikus aláírás

Az aszimmetrikus algoritmusokkal megvédheti az adatokat a módosítástól, és igazolhatja az adatkészítő identitását. Az alábbi ábra azt mutatja be, hogyan segít az aszimmetrikus aláírás a küldő identitásának bizonyítására.

![Aszimmetrikus aláírási példa](media/tutorial-x509-introduction/asymmetric-signing.png)

1. A küldő aszimmetrikus titkosítási algoritmuson keresztül továbbítja az egyszerű szöveges adatokat a titkosításhoz használt titkos kulccsal. Figyelje meg, hogy ez a forgatókönyv megfordítja az előző szakaszban ismertetett, az aszimmetrikus titkosítást részletező titkos és nyilvános kulcsok használatát.

1. Az eredményül kapott titkosítási szöveget a rendszer elküldi a címzettnek.

1. A címzett a kezdeményező nyilvános kulcsát egy címtárból szerezheti be.

1. A címzett az kezdeményező nyilvános kulcsával visszafejti a titkosított szöveget. Az eredményül kapott egyszerű szöveg igazolja a kezdeményező identitását, mert csak a kezdeményező rendelkezik hozzáféréssel az eredeti szöveget eredetileg titkosító titkos kulcshoz.

## <a name="signing"></a>Aláírás

A digitális aláírással meghatározható, hogy az adatok módosultak-e az átvitel során vagy az adatok szabad átvitele során. Az adatok egy kivonat algoritmuson, egy egy módon, az adott üzenetből matematikai eredményt előállító függvényen keresztül vannak átadva. Az eredmény neve kivonatérték, *üzenetkivonat,* *kivonat,* *aláírás* vagy *ujjlenyomat.* A kivonatértékek nem fordíthatóak vissza az eredeti üzenet beszerzéséhez. Mivel az üzenet kisebb módosítása jelentős változást hoz az ujjlenyomatban, a kivonatérték segítségével meghatározható, hogy az üzenet módosult-e. Az alábbi ábra azt mutatja be, hogyan használhatók az aszimmetrikus titkosítási és kivonattitkosítási algoritmusok annak ellenőrzésére, hogy az üzenetek nem módosultak-e.

![Aláírási példa](media/tutorial-x509-introduction/signing.png)

1. A küldő létrehoz egy egyszerű szöveges üzenetet.

1. A küldő kivonatot küld az egyszerű szöveges üzenetnek, hogy létrehoz egy üzenetkivonatot.

1. A küldő titkos kulccsal titkosítja a kivonatot.

1. A küldő továbbítja az egyszerű szöveges üzenetet és a titkosított kivonatot a kívánt címzettnek.

1. A címzett a küldő nyilvános kulcsával visszafejti a kivonatot.

1. A címzett ugyanazt a kivonat algoritmust futtatja, mint amit a küldő az üzeneten használt.

1. A címzett összehasonlítja az eredményül kapott aláírást a visszafejtett aláírással. Ha a kivonatok azonosak, az üzenet nem módosult az átvitel során.

## <a name="next-steps"></a>Következő lépések

A tanúsítványt felfogó mezőkről az [X.509](tutorial-x509-certificates.md)nyilvános kulcsú tanúsítványok ismertetése cikkből olvashat bővebben.

Ha már sokat tud az X.509-tanúsítványokról, és olyan tesztverziókat szeretne létrehozni, amelyek segítségével hitelesítheti magát a IoT Hub, tekintse meg a következő témaköröket:

* [Teszttanúsítványok Microsoft-Supplied szkriptek használatával](tutorial-x509-scripts.md)
* [Az OpenSSL használata teszttanúsítványok létrehozásához](tutorial-x509-openssl.md)
* [OpenSSL használata teszttanúsítványok Self-Signed létrehozásához](tutorial-x509-self-sign.md)

Ha rendelkezik hitelesítésszolgáltatói (CA) tanúsítvánnyal vagy alárendelt CA-tanúsítvánnyal, és fel szeretné tölteni azt az IoT Hubra, és igazolni szeretné, hogy Ön a hitelesítésszolgáltatói tanúsítvány birtokában [van,](tutorial-x509-prove-possession.md)tekintse meg a hitelesítésszolgáltatói tanúsítvány birtoklását.
