---
title: X.509-tanúsítványok Azure IoT Central
description: X.509-tanúsítványok dobás a IoT Central alkalmazással
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c25af944b4c748307f4f974ca8616ecc9f7b28c3
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714523"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>X.509-eszköztanúsítványok begörgetése az IoT Central alkalmazásban

Az IoT-megoldás életciklusa során tanúsítványokat kell begördülni. A működés közbeni tanúsítványok egyik fő oka a biztonsági incidens és a tanúsítvány lejárata.

Biztonsági incidens esetén a működés közbeni tanúsítványok használata ajánlott biztonsági eljárás a rendszer biztonságának segítése érdekében. A Biztonsági incidens [feltételezése módszertan](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)részeként a Microsoft azt javasolja, hogy a megelőző intézkedések mellett reaktív biztonsági folyamatokra is szükség van. Az eszköztanúsítványok gördülő folyamatának szerepelnie kell a biztonsági folyamatokban. A tanúsítványok leváltásának gyakorisága a megoldás biztonsági igényeitől függ. A kiemelten bizalmas adatokat tartalmazó megoldásokkal az ügyfelek naponta, míg mások néhány évente tanúsítványokat dobnak be.


## <a name="obtain-new-x509-certificates"></a>Új X.509-tanúsítványok beszerzése

Létrehozhat saját X.509-tanúsítványokat egy olyan eszközzel, mint az OpenSSL. Ez a módszer remek az X.509-tanúsítványok tesztelésére, de kevés biztonsági garanciát nyújt. Ezt a módszert csak akkor használja tesztelésre, ha készen áll arra, hogy saját hitelesítésszolgáltatóként viselkedjen.

## <a name="enrollment-groups-and-security-breaches"></a>Regisztrációs csoportok és biztonsági incidensek

Ha biztonsági incidensre reagálva frissítenie kell egy csoportos regisztrációt, a következő módszert kell alkalmaznia, amely azonnal frissíti az aktuális tanúsítványt:

1. A bal **oldali panelen lépjen** az Adminisztráció elemre, és válassza az **Eszközkapcsolat lehetőséget.**

2. Válassza **a Regisztrációs csoportok lehetőséget,** majd válassza ki a csoport nevét a listában.

3. Tanúsítványfrissítéshez válassza az **Elsődleges kezelése vagy** a Másodlagos kezelése **lehetőséget.**

4. Adja hozzá és ellenőrizze az X.509-főtanúsítványt a regisztrációs csoportban.

   Ha mindkét tanúsítvány biztonsága sérül, kövesse ezeket a lépéseket az elsődleges és a másodlagos tanúsítványok esetében.

## <a name="enrollment-groups-and-certificate-expiration"></a>Regisztrációs csoportok és tanúsítvány lejárata

Ha működés közbeni tanúsítványokat használ a tanúsítvány lejáratának kezeléséhez, az alábbi módszer használatával azonnal frissítheti az aktuális tanúsítványt:

1. A bal **oldali panelen lépjen** az Adminisztráció elemre, és válassza az **Eszközkapcsolat lehetőséget.**

2. Válassza **a Regisztrációs csoportok lehetőséget,** majd válassza ki a csoport nevét a listában.

3. Tanúsítványfrissítéshez válassza az **Elsődleges kezelése lehetőséget.**

4. Adja hozzá és ellenőrizze az X.509 főtanúsítványt a regisztrációs csoportban.

5. Később, amikor a másodlagos tanúsítvány lejárt, vissza kell jönnie, és frissítenie kell a másodlagos tanúsítványt.

## <a name="individual-enrollments-and-security-breaches"></a>Egyéni regisztrációk és biztonsági incidensek

Ha biztonsági incidensre reagálva vezet be tanúsítványokat, az alábbi módszer használatával azonnal frissítheti az aktuális tanúsítványt:

1. Válassza **az Eszközök** lehetőséget, majd válassza ki az eszközt.

2. Válassza **a Csatlakozás** lehetőséget, majd válassza a Kapcsolódási módszer lehetőséget egyéni **regisztrációként**

3. Válassza **a Tanúsítványok (X.509) lehetőséget** mechanizmusként.

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Tanúsítványfrissítéshez válassza a mappa ikont a beléptetési bejegyzéshez feltölteni kívánt új tanúsítvány kiválasztásához. Kattintson a **Mentés** gombra.

    Ha mindkét tanúsítvány biztonsága sérül, kövesse az alábbi lépéseket az elsődleges és a másodlagos tanúsítványok esetében.

## <a name="individual-enrollments-and-certificate-expiration"></a>Egyéni regisztrációk és tanúsítvány lejárata

Ha működés közbeni tanúsítványokat használ a tanúsítvány lejáratának kezeléséhez, a következőképpen használja a másodlagos tanúsítványkonfigurációt a kiépítni próbáló eszközök állásideje csökkentéséhez.

Ha a másodlagos tanúsítvány lejárata közeledik, és meg kell újulni, az elsődleges konfiguráció használatával válthat a következőre: . Az elsődleges és a másodlagos tanúsítványok közötti rotozás ily módon csökkenti a kiépítni kívánt eszközök állásideje.

1. Válassza **az Eszközök** lehetőséget, majd válassza ki az eszközt.

2. Válassza **a Csatlakozás** lehetőséget, majd válassza a Kapcsolódási módszer lehetőséget egyéni **regisztrációként**

3. Válassza **a Tanúsítványok (X.509) lehetőséget** mechanizmusként.

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Másodlagos tanúsítvány frissítésekor válassza a mappa ikont a beléptetési bejegyzéshez feltölteni kívánt új tanúsítvány kiválasztásához. Kattintson a **Mentés** gombra.

5. Később, amikor az elsődleges tanúsítvány lejárt, vissza kell jönnie, és frissítenie kell az elsődleges tanúsítványt.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan dobhat be X.509-tanúsítványokat a Azure IoT Central-alkalmazásban, kapcsolódhat a [Azure IoT Central.](concepts-get-connected.md)


