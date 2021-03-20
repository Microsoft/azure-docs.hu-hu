---
title: A roll X. 509 tanúsítványok az Azure-ban IoT Central
description: X. 509 tanúsítványok elvégzése a IoT Central alkalmazással
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92000059"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>X. 509 eszköz tanúsítványának legördülő IoT Central alkalmazásban

A IoT-megoldás életciklusa során be kell állítania a tanúsítványokat. A működés közbeni tanúsítványoknak két fő oka lehet a biztonsági szerződésszegés és a tanúsítványok lejárata.

Ha biztonsági problémákba ütközik, a működés közbeni tanúsítványok a rendszer biztonságának biztosítása érdekében ajánlott biztonsági eljárás. A [szabálysértési módszer elvállalásának](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)részeként a Microsoft azt javasolja, hogy a megelőző intézkedésekkel párhuzamosan legyen szükség reaktív biztonsági folyamatokra. Az eszköz tanúsítványait a biztonsági folyamatok részeként kell szerepeltetni. A tanúsítványok bevezetésének gyakorisága a megoldás biztonsági igényeitől függ. A fokozottan bizalmas adatokat tartalmazó megoldásokkal rendelkező ügyfelek napi rendszerességgel lefoglalhatják a tanúsítványokat, míg mások a tanúsítványokat minden pár évben elérhetik.


## <a name="obtain-new-x509-certificates"></a>Új X. 509 tanúsítványok beszerzése

Saját X. 509 tanúsítványokat is létrehozhat egy olyan eszközzel, mint az OpenSSL. Ez a módszer az X. 509 tanúsítványok tesztelésére szolgál, de a biztonsággal kapcsolatos néhány garanciát nyújt. Csak akkor használja ezt a módszert tesztelésre, ha nem a saját HITELESÍTÉSSZOLGÁLTATÓI szolgáltatóként való használatra készült.

## <a name="enrollment-groups-and-security-breaches"></a>Regisztrációs csoportok és biztonsági rések

Ha a csoportos regisztrációt biztonsági szabálysértésre válaszul szeretné frissíteni, a következő módszert kell használnia, amely azonnal frissíti az aktuális tanúsítványt:

1. Navigáljon a **felügyelet**  elemre a bal oldali ablaktáblán, és válassza az **eszköz-kapcsolódás** lehetőséget.

2. Válassza a **regisztrációs csoportok** lehetőséget, majd válassza ki a csoport nevét a listában.

3. A tanúsítvány frissítése lapon válassza az **elsődleges kezelése** vagy a **másodlagos kezelése** lehetőséget.

4. Adja hozzá és ellenőrizze a root X. 509 tanúsítványt a beléptetési csoportban.

   Hajtsa végre ezeket a lépéseket az elsődleges és a másodlagos tanúsítvány esetében, ha mindkettő sérült.

## <a name="enrollment-groups-and-certificate-expiration"></a>Regisztrációs csoportok és a tanúsítvány lejárata

Ha a tanúsítványok lejáratának kezelésére végzi a tanúsítványokat, a következő módszer használatával azonnal frissítheti az aktuális tanúsítványt:

1. Navigáljon a **felügyelet**  elemre a bal oldali ablaktáblán, és válassza az **eszköz-kapcsolódás** lehetőséget.

2. Válassza a **regisztrációs csoportok** lehetőséget, majd válassza ki a csoport nevét a listában.

3. A tanúsítvány frissítése lapon válassza az **elsődleges kezelése** lehetőséget.

4. Adja hozzá és ellenőrizze a root X. 509 tanúsítványt a beléptetési csoportban.

5. Később, amikor a másodlagos tanúsítvány lejárt, térjen vissza a másodlagos tanúsítványra, és frissítse azt.

## <a name="individual-enrollments-and-security-breaches"></a>Egyéni regisztrációk és biztonsági rések

Ha a tanúsítványokat biztonsági szabálysértésre válaszul végzi, a következő módszer használatával azonnal frissítheti az aktuális tanúsítványt:

1. Válassza az **eszközök** lehetőséget, és válassza ki az eszközt.

2. Válassza a **Csatlakozás** lehetőséget, és válassza a csatlakozás metódust **Egyéni regisztrációként**

3. Válassza a **tanúsítványok (X. 509)** lehetőséget a mechanizmusként.

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-x509-certificates/certificate-update.png)

4. A tanúsítvány frissítése területen válassza a mappa ikont a beléptetési bejegyzéshez feltölteni kívánt új tanúsítvány kiválasztásához. Kattintson a **Mentés** gombra.

    Hajtsa végre ezeket a lépéseket az elsődleges és a másodlagos tanúsítvány esetében, ha mindkettő sérül

## <a name="individual-enrollments-and-certificate-expiration"></a>Egyéni regisztrációk és a tanúsítvány lejárata

Ha a tanúsítványok lejáratának kezeléséhez a tanúsítványokat végzi, a következő módon kell használnia a másodlagos tanúsítvány konfigurációját, hogy csökkentse a kiépíteni próbált eszközök leállását.

Ha a másodlagos tanúsítvány közel van a lejárati időponthoz, és azt el kell érnie, elforgathatja az elsődleges konfiguráció használatával. Az elsődleges és a másodlagos tanúsítványok közötti rotáció csökkenti az állásidőt a kiépíteni próbált eszközök esetében.

1. Válassza az **eszközök** lehetőséget, és válassza ki az eszközt.

2. Válassza a **Csatlakozás** lehetőséget, és válassza a csatlakozás metódust **Egyéni regisztrációként**

3. Válassza a **tanúsítványok (X. 509)** lehetőséget a mechanizmusként.

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-x509-certificates/certificate-update.png)

4. A másodlagos tanúsítvány frissítése elemnél válassza a mappa ikont a beléptetési bejegyzéshez feltölteni kívánt új tanúsítvány kiválasztásához. Kattintson a **Mentés** gombra.

5. Később, amikor az elsődleges tanúsítvány lejárt, térjen vissza az elsődleges tanúsítványra, és frissítse azt.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan lehet X. 509 tanúsítványokat beolvasni az Azure IoT Central alkalmazásban, csatlakozhat [az azure IoT Centralhoz](concepts-get-connected.md).


