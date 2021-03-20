---
title: Az Azure IoT Hub X. 509 HITELESÍTÉSSZOLGÁLTATÓI biztonság áttekintése | Microsoft Docs
description: Áttekintés – eszközök hitelesítése az IoT Hub X. 509 hitelesítésszolgáltatók használatával.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92150712"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Eszköz hitelesítése X.509-es hitelesítésszolgáltatói tanúsítványokkal

Ez a cikk azt ismerteti, hogyan használható az X. 509 hitelesítésszolgáltatók (CA) tanúsítványai a IoT Hub csatlakozó eszközök hitelesítéséhez.  Ebben a cikkben a következőket fogja elsajátítani:

* X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány beszerzése
* Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálása IoT Hub
* Eszközök aláírása X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok használatával
* Az X. 509 HITELESÍTÉSSZOLGÁLTATÓval aláírt eszközök hitelesítése

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Áttekintés

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI szolgáltatás lehetővé teszi, hogy az eszköz hitelesítése IoT Hub hitelesítésszolgáltató (CA) használatával történjen. Nagy mértékben leegyszerűsíti a kezdeti eszközök regisztrálási folyamatát, és az eszközök gyártása során biztosítja a lánc logisztikáját. [Ebben a forgatókönyvben további információt talál az X. 509 hitelesítésszolgáltatói tanúsítványok használata](iot-hub-x509ca-concept.md) az eszközök hitelesítéséhez című cikkből.  Javasoljuk, hogy a továbblépés előtt olvassa el ezt a forgatókönyvet, mielőtt elmagyarázza, miért van a következő lépések.

## <a name="prerequisite"></a>Előfeltétel

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI szolgáltatás használatához IoT Hub fiókra van szükség.  [Megtudhatja, hogyan hozhat létre IoT hub-példányt](quickstart-send-telemetry-dotnet.md) , ha még nem rendelkezik ilyennel.

## <a name="how-to-get-an-x509-ca-certificate"></a>X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány beszerzése

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány az egyes eszközök tanúsítványainak láncának tetején található.  Vásárolhat vagy létrehozhat egyet attól függően, hogy hogyan kívánja használni.

Éles környezetben ajánlott egy X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vásárlása egy nyilvános legfelső szintű hitelesítésszolgáltatótól. A CA tanúsítvány megvásárlása a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓnak a megbízható harmadik féltől származó előnye, amely az eszközök legitimitásának igazolására szolgál. Vegye figyelembe ezt a lehetőséget, ha azt szeretné, hogy az eszközök egy olyan nyitott IoT-hálózat részévé legyenek, amelyben a harmadik féltől származó termékekkel vagy szolgáltatásokkal való interakció várható.

Létrehozhat egy önaláírt X. 509 HITELESÍTÉSSZOLGÁLTATÓT is a kísérletezéshez vagy a zárt IoT-hálózatokban való használathoz.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány beszerzésének módjától függetlenül győződjön meg róla, hogy a titkos kulcs és a védelem mindig meg van nyitva.  Erre azért van szükség, hogy az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítés megbízhatóságának kiépítése megbízható legyen.

Ismerje meg, hogyan [hozhat létre önaláírt hitelesítésszolgáltatói tanúsítványt](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), amelyet a funkció leírásában használhat a kísérletezéshez.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Eszközök aláírása a megbízhatósági tanúsítványláncbe

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa kriptográfiai módon aláírhat egy köztes HITELESÍTÉSSZOLGÁLTATÓT, amely egy másik közbenső HITELESÍTÉSSZOLGÁLTATÓT is aláírhat, és így tovább, egészen addig, amíg az utolsó közbenső HITELESÍTÉSSZOLGÁLTATÓ leállítja ezt a folyamatot egy eszköz aláírásával. Az eredmény egy megbízható tanúsítványláncként ismert tanúsítványok lépcsőzetes lánca. A valós életben ez a megbízhatóság delegálása az eszközök aláírása felé. Ez a delegálás azért fontos, mert titkosítási szempontból változó felügyeleti láncot hoz létre, és elkerüli az aláíró kulcsok megosztását.

![IMG-Generic-CERT-Chain-of-Trust](./media/generic-cert-chain-of-trust.png)

Az eszköz tanúsítványának (más néven levél tanúsítványának) a *tulajdonos nevét* kell beállítania a IoT-eszköz Azure-IoT hub való regisztrálása során használt **eszköz-azonosítóra** ( `CN=deviceId` ). Ez a beállítás a hitelesítéshez szükséges.

Itt megtudhatja, hogyan [hozhat létre egy tanúsítványláncot](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) az eszközök aláírása során.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálása IoT Hub

Regisztrálja X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát, hogy IoT Hub, hol fogja használni az eszközöket a regisztráció és a csatlakozás során.  Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálása kétlépéses folyamat, amely magában foglalja a tanúsítványfájl feltöltését és a birtoklás igazolását.

A feltöltési folyamat magában foglalja a tanúsítványt tartalmazó fájl feltöltését.  Ez a fájl soha nem tartalmazhat titkos kulcsot.

A birtoklási lépés igazolása az Ön és a IoT Hub közötti titkosítási kihívás és reagálási folyamat.  Mivel a digitális tanúsítvány tartalma nyilvános, ezért a lehallgatásra fogékony, IoT Hub szeretné megállapítani, hogy valóban a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa.  Ezt olyan véletlenszerű kihívás létrehozásával kell elvégezni, amelyet a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megfelelő titkos kulcsával kell aláírnia.  Ha titokban tartotta a titkos kulcsot, és a korábban javasolt módon védett, akkor csak a lépés elvégzéséhez lesz az ismerete. A titkos kulcsok titkossága ennek a módszernek a megbízhatósági forrása.  A probléma aláírása után végezze el ezt a lépést az eredményeket tartalmazó fájl feltöltésével.

További információ a [hitelesítésszolgáltatói tanúsítvány regisztrálásáról](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Eszköz létrehozása IoT Hubon

Az eszköz megszemélyesítésének meggátolása érdekében IoT Hub megköveteli, hogy tájékoztassa a várható eszközöket.  Ehhez hozzon létre egy eszköz bejegyzést a IoT Hub eszközének beállításjegyzékében.  Ez a folyamat IoT Hub [eszköz kiépítési szolgáltatásának](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/)használatakor automatizálható. 

Itt megtudhatja, hogyan [hozhat létre manuálisan egy eszközt a IoT Hubban](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

X. 509 eszköz létrehozása az IoT hub számára

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokkal aláírt eszközök hitelesítése

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálása és az eszközök megbízhatósági láncba való bejelentkezve az eszköz akkor is az eszköz hitelesítése, amikor az eszköz csatlakozik, még az első alkalommal is.  Ha egy X. 509 HITELESÍTÉSSZOLGÁLTATÓ által aláírt eszköz csatlakozik, az ellenőrzi a tanúsítvány láncát az ellenőrzéshez. A lánc az összes köztes HITELESÍTÉSSZOLGÁLTATÓ és eszköz tanúsítványát tartalmazza.  Ezekkel az információkkal a IoT Hub kétlépéses folyamattal hitelesíti az eszközt.  IoT Hub kriptográfiailag ellenőrzi a tanúsítványlánc belső konzisztencia-ellenőrzését, majd kibocsátja az eszközre vonatkozó igazoló kihívást.  IoT Hub deklarálja az eszköz eredetiségét az eszköz sikeres, igazolásra adott válaszára.  Ez a nyilatkozat azt feltételezi, hogy az eszköz titkos kulcsa védett, és csak az eszköz tud válaszolni erre a kihívásra.  A titkos kulcsok védelme érdekében javasoljuk, hogy használjon biztonságos zsetonokat, például hardveres biztonságos modulokat (HSM) az eszközökön.

Egy sikeres eszköz-kapcsolódás IoT Hub befejezi a hitelesítési folyamatot, és a megfelelő beállításra is utal.

Itt megtudhatja, hogyan [végezheti el az eszköz kapcsolódási lépését](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Következő lépések

Ismerje meg [az X. 509 hitelesítésszolgáltatói hitelesítés értékét](iot-hub-x509ca-concept.md) a IoT-ben.

Ismerkedjen meg IoT Hub [Device kiépítési szolgáltatással](../iot-dps/index.yml).