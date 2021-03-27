---
title: Oktatóanyag – az Azure IoT Hub titkosítási és X. 509 tanúsítványainak ismertetése | Microsoft Docs
description: Oktatóanyag – a titkosítás és az X. 509 PKI ismertetése az Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: 88f5803e1d4348b79c7675d627a541ff47700dc0
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630749"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Oktatóanyag: a nyilvános kulcsú titkosítás és az X. 509 nyilvános kulcsokra épülő infrastruktúra ismertetése

Az eszközök Azure-IoT Hub való hitelesítéséhez X. 509 tanúsítványokat használhat. A tanúsítvány egy olyan digitális dokumentum, amely tartalmazza az eszköz nyilvános kulcsát, és annak ellenőrzéséhez használható, hogy az eszköz mit követel. Az X. 509 tanúsítványokat és a visszavont tanúsítványok listáját (CRL) az [RFC 5280](https://tools.ietf.org/html/rfc5280)dokumentálja. A tanúsítványok csak az X. 509 nyilvános kulcsú infrastruktúra (PKI) egy részét képezik. Az X. 509 PKI megismeréséhez meg kell ismernie a titkosítási algoritmusokat, a titkosítási kulcsokat, a tanúsítványokat és a hitelesítésszolgáltatót (CAs):

* Az **algoritmusok** határozzák meg, hogy a rendszer hogyan alakítja át az eredeti egyszerű szöveges adattípusokat a rejtjelezett, és vissza
* A **kulcsok** véletlenszerű vagy bájtján elvégeznek adatsztringek, amelyeket bemenetként használnak egy algoritmusban.
* A **tanúsítványok** olyan digitális dokumentumok, amelyek egy entitás nyilvános kulcsát tartalmazzák, és lehetővé teszik annak meghatározását, hogy a tanúsítvány tárgya ki van-e téve.
* A **hitelesítésszolgáltatók** tanúsítják a tanúsítvány tulajdonosának hitelességét.

A tanúsítványokat hitelesítésszolgáltatótól (CA) is megvásárolhatja. Tesztelésre és fejlesztésre is használható, vagy ha önálló környezetben dolgozik, hozzon létre egy önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT. Ha például egy vagy több eszköz rendelkezik az IoT hub hitelesítésének teszteléséhez, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT saját maga aláírhatja, és az eszköz tanúsítványait is kibocsáthatja. Önaláírt tanúsítványok is kiadhatók. Ezt a következő cikkekben tárgyaljuk.

Az X. 509 tanúsítványok részletesebb megvitatása és az eszközök egy IoT Hub való hitelesítése előtt megbeszéljük a kriptográfiai tanúsítványokat, amelyeken a tanúsítványok alapulnak.

## <a name="cryptography"></a>Titkosítás

A titkosítás az információk és a kommunikáció elleni védelemre szolgál. Ez általában olyan titkosítási technikák használatával történik, amelyek az egyszerű szöveges (normál) szövegeket rejtjelezett (kódolt szöveg) és vissza. Ezt a kódolási folyamatot titkosításnak nevezzük. A fordított folyamatot visszafejtésnek nevezzük. A kriptográfia a következő célkitűzésekkel foglalkozik:

* **Titkosság**: az információkat csak a célközönségnek lehet érteni.
* **Integritás**: az adatok tárolása vagy továbbítása nem módosítható.
* **Nem megtagadás**: az információ létrehozója később nem tagadhatja meg a létrehozását.
* **Hitelesítés**: a küldő és a fogadó képes megerősíteni egymás identitását.

## <a name="encryption"></a>Titkosítás

A titkosítási folyamathoz szükség van egy algoritmusra és egy kulcsra. Az algoritmus azt határozza meg, hogy az adatok hogyan alakíthatók át egyszerű szövegből a rejtjelezett-be és vissza a szöveges formátumba. A kulcs az algoritmus bemenetként használt véletlenszerű karakterlánc. A folyamat minden biztonsági lépését a kulcs tárolja. Ezért a kulcsot biztonságosan kell tárolni. A legnépszerűbb algoritmusok részletei azonban nyilvánosan elérhetők.

A titkosításnak két típusa van. A szimmetrikus titkosítás ugyanazt a kulcsot használja, mint a titkosítás és a visszafejtés. Az aszimmetrikus titkosítás különböző, de matematikailag kapcsolódó kulcsokat használ a titkosítás és a visszafejtés végrehajtásához.

### <a name="symmetric-encryption"></a>Szimmetrikus titkosítás

A szimmetrikus titkosítás ugyanazzal a kulccsal titkosítja az egyszerű szövegeket a rejtjelezett-be, és visszafejti a rejtjelezett-t egyszerű szöveges formátumba. A kulcs szükséges hosszát, a bitek számában kifejezve az algoritmus határozza meg. Miután a kulcsot az egyszerű szöveg titkosítására használják, a rendszer elküldi a titkosított üzenetet a címzettnek, aki ezt követően visszafejti a rejtjelezett. A szimmetrikus kulcsot biztonságosan továbbítani kell a címzettnek. Szimmetrikus algoritmus használata esetén a kulcs elküldése a legnagyobb biztonsági kockázat.

![Szimmetrikus titkosítási példa](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Aszimmetrikus titkosítás

Ha csak szimmetrikus titkosítást használ, a probléma az, hogy a kommunikáció valamennyi résztvevője rendelkezzen a titkos kulccsal. Azonban lehetséges, hogy a jogosulatlan harmadik felek rögzíthetik a kulcsot a hitelesített felhasználóknak való továbbítás során. A probléma megoldásához használja inkább az aszimmetrikus vagy a nyilvános kulcsú titkosítást.

Az aszimmetrikus titkosításban minden felhasználó két matematikai kapcsolatban álló kulcsot tartalmaz, amelyek egy kulcspár. Az egyik kulcs nyilvános, a másik kulcs pedig privát. A kulcspár biztosítja, hogy csak a címzett férhet hozzá az adatvisszafejtéshez szükséges titkos kulcshoz. A következő ábra összefoglalja az aszimmetrikus titkosítási folyamatot.

![Aszimmetrikus titkosítási példa](media/tutorial-x509-introduction/asymmetric-keys.png)

1. A címzett létrehoz egy nyilvános titkos kulcspárt, és elküldi a nyilvános kulcsot egy HITELESÍTÉSSZOLGÁLTATÓNAK. A HITELESÍTÉSSZOLGÁLTATÓ egy X. 509 tanúsítványban csomagolja ki a nyilvános kulcsot.

1. A küldő fél megszerzi a címzett nyilvános kulcsát a HITELESÍTÉSSZOLGÁLTATÓTÓL.

1. A küldő titkosító algoritmus használatával titkosítja az egyszerű szöveges adataikat. A címzett nyilvános kulcsa a titkosítás elvégzésére szolgál.

1. A küldő továbbítja a rejtjelezett a címzettnek. Nem szükséges elküldeni a kulcsot, mert a címzett már rendelkezik a rejtjelezett visszafejtéséhez szükséges titkos kulccsal.

1. A címzett visszafejti a rejtjelezett a megadott aszimmetrikus algoritmus és a titkos kulcs használatával.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Szimmetrikus és aszimmetrikus titkosítás egyesítése

A szimmetrikus és aszimmetrikus titkosítás kombinálható a relatív erősségek kihasználása érdekében. A szimmetrikus titkosítás sokkal gyorsabb, mint az aszimmetrikus, azonban a titkos kulcsok más felek számára történő elküldésének szükségessége miatt nem biztonságos. A két típus együttes összevonásához a szimmetrikus titkosítás használható a szöveges rejtjelezett konvertálására. Az aszimmetrikus titkosítás a szimmetrikus kulcs cseréjére szolgál. Ezt a következő ábra mutatja be.

![Szimmetrikus és assymetric titkosítás](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. A küldő lekéri a címzett nyilvános kulcsát.

1. A küldő létrehoz egy szimmetrikus kulcsot, és a használatával titkosítja az eredeti adatátvitelt.

1. A küldő a címzett nyilvános kulcsát használja a szimmetrikus kulcs titkosításához.

1. A küldő továbbítja a titkosított szimmetrikus kulcsot és a rejtjelezett a kívánt címzettnek.

1. A címzett olyan titkos kulcsot használ, amely megfelel a címzett nyilvános kulcsának, hogy visszafejtse a küldő szimmetrikus kulcsát.

1. A címzett a szimmetrikus kulcs használatával visszafejti a rejtjelezett.

### <a name="asymmetric-signing"></a>Aszimmetrikus aláírás

Az aszimmetrikus algoritmusok segítségével biztosítható az adatok védelme a módosításból, és bizonyítható az adatok létrehozójának identitása. Az alábbi ábra bemutatja, hogyan segíti az aszimmetrikus aláírás a küldő identitásának bizonyítását.

![Aszimmetrikus aláírási példa](media/tutorial-x509-introduction/asymmetric-signing.png)

1. A küldő egy aszimmetrikus titkosítási algoritmuson keresztül továbbítja a szöveges adatbevitelt a titkosításhoz használt titkos kulccsal. Figyelje meg, hogy ez a forgatókönyv visszafordítja az előző szakaszban leírt titkos és nyilvános kulcsokat az aszimmetrikus titkosítás részletes használatával.

1. A rendszer elküldi az eredményül kapott rejtjelezett a címzettnek.

1. A címzett egy címtárból szerzi be a kezdeményező nyilvános kulcsát.

1. A címzett a kezdeményező nyilvános kulcsának használatával visszafejti a rejtjelezett. Az eredményül kapott egyszerű szöveg igazolja a kezdeményező identitását, mert csak a kezdeményező fér hozzá az eredeti szöveget eredetileg titkosító titkos kulcshoz.

## <a name="signing"></a>Aláírás

A digitális aláírással megállapítható, hogy az adatok átvitele vagy nyugalmi állapotban módosult-e. Az adatok átadása egy kivonatoló algoritmuson keresztül történik, amely egy egyirányú függvény, amely matematikai eredményt hoz létre a megadott üzenetből. Az eredmény neve *kivonatoló érték*, üzenet- *kivonatoló*, *kivonatoló*, *aláírás* vagy *ujjlenyomat*. Egy kivonatoló érték nem vonható vissza az eredeti üzenet beszerzéséhez. Mivel az üzenet kis változása jelentős változást eredményez az *ujjlenyomatban*, a kivonatoló érték segítségével megállapítható, hogy az üzenet módosult-e. Az alábbi ábra bemutatja, hogyan használható az aszimmetrikus titkosítási és kivonatoló algoritmus annak ellenőrzéséhez, hogy az üzenet nem lett-e módosítva.

![Példa aláírása](media/tutorial-x509-introduction/signing.png)

1. A küldő egy egyszerű szöveges üzenetet hoz létre.

1. A küldő kivonatot készít az egyszerű szöveges üzenetből egy üzenet kivonatának létrehozásához.

1. A küldő titkos kulccsal titkosítja a kivonatot.

1. A küldő továbbítja a szöveges üzenetet és a titkosított kivonatot a kívánt címzettnek.

1. A címzett a küldő nyilvános kulcsának használatával visszafejti a kivonatot.

1. A címzett ugyanazt a kivonatoló algoritmust futtatja, amelyet a küldő az üzenetben használt.

1. A címzett összehasonlítja az eredményül kapott aláírást a visszafejtett aláírással. Ha a kivonatok megegyeznek, az üzenet nem módosult az átvitel során.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a tanúsítványt alkotó mezőkről, tekintse meg az [X. 509 nyilvános kulcsú tanúsítványok ismertetése](tutorial-x509-certificates.md)című témakört.

Ha már ismeri az X. 509 tanúsítványokat, és olyan tesztelési verziókat kíván előállítani, amelyeket a IoT Hub való hitelesítéshez használhat, tekintse meg a következő témaköröket:

* [Microsoft-Supplied szkriptek használata tesztelési tanúsítványok létrehozásához](tutorial-x509-scripts.md)
* [Az OpenSSL használata tesztelési tanúsítványok létrehozásához](tutorial-x509-openssl.md)
* [Az OpenSSL használata Self-Signed tesztelési tanúsítványok létrehozásához](tutorial-x509-self-sign.md)

Ha hitelesítésszolgáltató (CA) tanúsítványa vagy alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa van, és fel szeretné tölteni a IoT hub-ba, és bizonyítania kell, hogy rendelkezik a [hitelesítésszolgáltató tanúsítványának](tutorial-x509-prove-possession.md)igazolásával.
