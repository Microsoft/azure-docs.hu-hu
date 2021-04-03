---
title: A roll X. 509 tanúsítványok az Azure-ban IoT Hub Device Provisioning Service
description: X. 509 tanúsítványok leállítása az eszköz kiépítési szolgáltatásával (DPS) rendelkező példánnyal
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94951128"
---
# <a name="how-to-roll-x509-device-certificates"></a>X. 509 eszközök tanúsítványainak behelyezése

A IoT-megoldás életciklusa során be kell állítania a tanúsítványokat. A működés közbeni tanúsítványoknak két fő oka lehet a biztonsági szerződésszegés és a tanúsítványok lejárata. 

A működés közbeni tanúsítványok az ajánlott biztonsági eljárások, amelyekkel a rendszer biztonságban is biztonságossá tehető. A [szabálysértési módszer elvállalásának](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)részeként a Microsoft azt javasolja, hogy a megelőző intézkedésekkel párhuzamosan legyen szükség reaktív biztonsági folyamatokra. Az eszköz tanúsítványait a biztonsági folyamatok részeként kell szerepeltetni. A tanúsítványok bevezetésének gyakorisága a megoldás biztonsági igényeitől függ. A fokozottan bizalmas adatokat tartalmazó megoldásokkal rendelkező ügyfelek napi rendszerességgel lefoglalhatják a tanúsítványokat, míg mások a tanúsítványokat minden pár évben elérhetik.

A működés közbeni eszközök tanúsítványainak az eszközön és az IoT központban tárolt tanúsítvány frissítését is magukban foglalják. Ezt követően az eszköz [kiépítheti](about-iot-dps.md#provisioning-process) magát az IoT hub használatával a normál kiépítéssel az eszköz kiépítési szolgáltatásával (DPS).


## <a name="obtain-new-certificates"></a>Új tanúsítványok beszerzése

Számos módon szerezhet be új tanúsítványokat a IoT-eszközökhöz. Ezek közé tartozik az eszköz gyárból származó tanúsítványok beszerzése, a saját tanúsítványok generálása, valamint a tanúsítványok létrehozásával kapcsolatos harmadik fél felügyelete. 

A tanúsítványokat a rendszer aláírja egymással, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványból egy [levélbeli tanúsítványra](concepts-x509-attestation.md#end-entity-leaf-certificate)Bízzon. Az aláíró tanúsítvány a levél tanúsítványának a megbízhatósági lánc végén történő aláírásához használt tanúsítvány. Az aláíró tanúsítvány lehet legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy egy köztes tanúsítvány a megbízhatósági láncban. További információ: [X. 509 tanúsítványok](concepts-x509-attestation.md#x509-certificates).
 
Az aláíró tanúsítvány két különböző módon szerezhető be. Az éles rendszerekhez javasolt első módszer az, ha az aláíró tanúsítványt egy főtanúsítvány-szolgáltatótól (CA) vásárolja meg. Így a láncok biztonsága egy megbízható forrásig megtörténik. 

A második módszer a saját X. 509-tanúsítványok létrehozása egy olyan eszköz használatával, mint az OpenSSL. Ez a módszer az X. 509 tanúsítványok tesztelésére szolgál, de a biztonsággal kapcsolatos néhány garanciát nyújt. Javasoljuk, hogy csak akkor használja ezt a módszert tesztelésre, ha a saját HITELESÍTÉSSZOLGÁLTATÓI szolgáltatóként való használatra kész.
 

## <a name="roll-the-certificate-on-the-device"></a>A tanúsítvány behelyezése az eszközön

Az eszközön lévő tanúsítványokat mindig biztonságos helyen kell tárolni, mint például a [hardveres biztonsági modul (HSM)](concepts-service.md#hardware-security-module). Az eszközök tanúsítványainak bekapcsolásának módja attól függ, hogyan lettek létrehozva és telepítve az eszközökre az első helyen. 

Ha a tanúsítványait egy harmadik féltől kapta, meg kell vizsgálnia, hogyan használják a tanúsítványokat. A folyamat belefoglalható a Megállapodásba, vagy lehet egy különálló szolgáltatás is. 

Ha saját eszközökhöz tartozó tanúsítványokat kezel, a tanúsítványok frissítéséhez saját folyamatot kell létrehoznia. Győződjön meg arról, hogy a régi és az új levél tanúsítványának ugyanaz a köznapi neve (CN). Ha ugyanazzal a CN-vel rendelkezik, akkor az eszköz duplikált regisztrációs rekord létrehozása nélkül is újraépítheti magát. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>A tanúsítvány behelyezése az IoT hub-ban

Az eszköz tanúsítványát manuálisan is hozzáadhatja egy IoT hubhoz. A tanúsítvány automatizálható egy eszköz kiépítési szolgáltatási példányának használatával is. Ez a cikk azt feltételezi, hogy az eszköz kiépítési szolgáltatási példánya az automatikus kiépítés támogatására szolgál.

Amikor egy eszköz először az automatikus kiépítés révén lett kiépítve, elindul, és kapcsolatba lép a kiépítési szolgáltatással. A kiépítési szolgáltatás úgy válaszol, hogy az eszközhöz tartozó levél tanúsítványa hitelesítő adatokkal való létrehozása előtt megtekinti az IoT hub eszköz-identitását. A kiépítési szolgáltatás ezt követően közli az eszközzel, hogy melyik IoT hub van hozzárendelve, és az eszköz ezt követően a levél tanúsítványát használja a IoT hub hitelesítéséhez és az ahhoz való kapcsolódáshoz. 

Miután az új levél tanúsítványát beszállították az eszközre, az már nem tud csatlakozni az IoT hubhoz, mert új tanúsítványt használ a kapcsolódáshoz. Az IoT hub csak a régi tanúsítvánnyal rendelkező eszközt ismeri fel. Az eszköz csatlakozási kísérletének eredménye "jogosulatlan" kapcsolódási hiba lesz. A hiba megoldásához frissítenie kell az eszköz beléptetési bejegyzését az eszköz új levél-tanúsítványához. Ezt követően a kiépítési szolgáltatás szükség szerint frissítheti a IoT Hub-eszköz beállításjegyzék-információit az eszköz újraépítésekor. 

A kapcsolódási hiba egyik lehetséges kivétele olyan forgatókönyv, amelyben létrehozott egy [regisztrációs csoportot](concepts-service.md#enrollment-group) az eszközhöz a kiépítési szolgáltatásban. Ebben az esetben, ha nem a gyökér-vagy köztes tanúsítványokat az eszköz megbízhatósági láncában látja, akkor a rendszer felismeri az eszközt, ha az új tanúsítvány a beléptetési csoportban definiált megbízhatósági lánc részét képezi. Ha ez a forgatókönyv egy biztonsági szabálysértésre való reagálásra szolgál, akkor legalább a csoportba tartozó, a megszegett eszközök tanúsítványainak letiltása szükséges. További információ: [bizonyos eszközök letiltása egy regisztrációs csoportban](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group).

A beléptetési bejegyzések frissítése a **beléptetések kezelése** oldalon végezhető el. Az oldal eléréséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon ahhoz a IoT hub Device Provisioning Service-példányhoz, amely az eszköz beléptetési bejegyzésével rendelkezik.

2. Kattintson a **Regisztrációk kezelése** elemre.

    ![Regisztrációk kezelése](./media/how-to-roll-certificates/manage-enrollments-portal.png)


A beléptetési bejegyzés frissítésének módja attól függ, hogy egyéni regisztrációkat vagy csoportos regisztrációkat használ-e. Emellett az ajánlott eljárások eltérőek lehetnek attól függően, hogy biztonsági szerződésszegés vagy a tanúsítvány lejárata miatt van-e működésben a tanúsítvány. A következő szakaszok ismertetik, hogyan kezelheti ezeket a frissítéseket.


## <a name="individual-enrollments-and-security-breaches"></a>Egyéni regisztrációk és biztonsági rések

Ha a tanúsítványokat biztonsági szabálysértésre válaszul végzi, a következő módszert kell használnia, amely azonnal törli az aktuális tanúsítványt:

1. Kattintson az **Egyéni regisztrációk** elemre, majd a listában kattintson a regisztrációs azonosító bejegyzésre. 

2. Kattintson az **aktuális tanúsítvány törlése** gombra, majd kattintson a mappa ikonra a beléptetési bejegyzéshez feltöltendő új tanúsítvány kiválasztásához. Ha elkészült, kattintson a **Mentés** gombra.

    Ezeket a lépéseket az elsődleges és a másodlagos tanúsítvány esetében el kell végezni, ha mindkettő sérül.

    ![Biztonsági szabálysértéssel rendelkező egyéni regisztrációk kezelése](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Ha a feltört tanúsítvány el lett távolítva a kiépítési szolgáltatásból, a tanúsítvány továbbra is felhasználható az IoT hub-kapcsolat létesítésére, amennyiben az eszköz regisztrálása ott létezik. A következő két módszer közül választhat: 

    Első lépésként manuálisan navigáljon az IoT hub-ra, és azonnal távolítsa el a feltört tanúsítványhoz társított eszköz regisztrációját. Ezután amikor az eszköz ismét kiépít egy frissített tanúsítvánnyal, új eszköz regisztrálása lesz létrehozva.     

    ![IoT hub-eszköz regisztrációjának eltávolítása](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második módszer az, hogy a támogatás újralétesítésével újra kiépítse az eszközt ugyanahhoz az IoT-hubhoz. Ezzel a módszerrel lehet lecserélni az eszköz regisztrációjának tanúsítványát az IoT hub-ban. További információ: [az eszközök újraépítése](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Egyéni regisztrációk és a tanúsítvány lejárata

Ha a tanúsítványok lejáratának kezeléséhez a tanúsítványokat végzi, a következő módon kell használnia a másodlagos tanúsítvány konfigurációját, hogy csökkentse a kiépíteni próbált eszközök leállását.

Később, amikor a másodlagos tanúsítvány közel van a lejárathoz, és azt el kell érni, az elsődleges konfiguráció használatával forgatható. Az elsődleges és a másodlagos tanúsítványok közötti rotáció csökkenti az állásidőt a kiépíteni próbált eszközök esetében.


1. Kattintson az **Egyéni regisztrációk** elemre, majd a listában kattintson a regisztrációs azonosító bejegyzésre. 

2. Kattintson a **másodlagos tanúsítvány** elemre, majd kattintson a mappa ikonra a beléptetési bejegyzéshez feltölteni kívánt új tanúsítvány kiválasztásához. Kattintson a **Mentés** gombra.

    ![Egyéni regisztrációk kezelése a másodlagos tanúsítvány lejáratának használatával](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Később, amikor az elsődleges tanúsítvány lejárt, térjen vissza, és törölje az elsődleges tanúsítványt az **aktuális tanúsítvány törlése** gombra kattintva.

## <a name="enrollment-groups-and-security-breaches"></a>Regisztrációs csoportok és biztonsági rések

Ha a csoportos regisztrációt biztonsági szabálysértésre válaszul szeretné frissíteni, akkor az alábbi módszerek egyikét kell használnia, amely az aktuális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT vagy a köztes tanúsítványt azonnal törli.

#### <a name="update-compromised-root-ca-certificates"></a>Sérült legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok frissítése

1. Kattintson a **tanúsítványok** lapra az eszköz kiépítési szolgáltatási példányához.

2. Kattintson a feltört tanúsítványra a listában, majd kattintson a **Törlés** gombra. Erősítse meg a törlést a tanúsítvány nevének megadásával, és kattintson az **OK** gombra. Ismételje meg ezt a folyamatot az összes sérült tanúsítvány esetében.

    ![Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány törlése](./media/how-to-roll-certificates/delete-root-cert.png)

3. Kövesse az [ellenőrzött hitelesítésszolgáltatói tanúsítványok konfigurálása](how-to-verify-certificates.md) az új legfelső szintű hitelesítésszolgáltatói tanúsítványok hozzáadásához és ellenőrzéséhez című témakör lépéseit.

4. Kattintson a **regisztrációk kezelése** lapra az eszköz kiépítési szolgáltatási példányához, majd kattintson a **regisztrációs csoportok** listára. Kattintson a regisztrációs csoport nevére a listában.

5. Kattintson a **hitelesítésszolgáltatói tanúsítvány** elemre, majd válassza ki az új legfelső szintű hitelesítésszolgáltatói tanúsítványt. Ezután kattintson a **Mentés** gombra. 

    ![A feltört tanúsítványhoz tartozó új legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány kiválasztása](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Ha a feltört tanúsítvány el lett távolítva a kiépítési szolgáltatásból, a tanúsítvány továbbra is használható az IoT hub eszköz-kapcsolatainak használatára, amíg az eszköz regisztrálása ott létezik. A következő két módszer közül választhat: 

    Első lépésként manuálisan navigáljon az IoT hub-ra, és azonnal távolítsa el a feltört tanúsítványhoz társított eszköz regisztrációját. Ezután, amikor az eszközök ismét kiépítik a frissített tanúsítványokat, mindegyikhez új eszköz regisztrálása lesz létrehozva.     

    ![IoT hub-eszköz regisztrációjának eltávolítása](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második módszer a támogatás újraépítésének használata az eszközök ugyanarra az IoT-hubhoz való újraépítésére. Ezzel a módszerrel lehet lecserélni az IoT hub-beli eszközök regisztrációjának tanúsítványait. További információ: [az eszközök újraépítése](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Sérült közbenső tanúsítványok frissítése

1. Kattintson a **beléptetési csoportok** elemre, majd kattintson a csoport nevére a listában. 

2. Kattintson a **köztes tanúsítvány** elemre, és **törölje az aktuális tanúsítványt**. Kattintson a mappa ikonra, és navigáljon a beléptetési csoportba feltöltendő új köztes tanúsítványhoz. Ha elkészült, kattintson a **Mentés** gombra. Ezeket a lépéseket mind az elsődleges, mind a másodlagos tanúsítvány esetében el kell végezni, ha mindkettő sérül.

    Ezt az új közbenső tanúsítványt olyan ellenőrzött legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal kell aláírni, amely már hozzá lett adva a kiépítési szolgáltatáshoz. További információ: [X. 509 tanúsítványok](concepts-x509-attestation.md#x509-certificates).

    ![A feltört intermedierek egyéni regisztrációinak kezelése](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Ha a feltört tanúsítvány el lett távolítva a kiépítési szolgáltatásból, a tanúsítvány továbbra is használható az IoT hub eszköz-kapcsolatainak használatára, amíg az eszköz regisztrálása ott létezik. A következő két módszer közül választhat: 

    Első lépésként manuálisan navigáljon az IoT hub-ra, és azonnal távolítsa el a feltört tanúsítványhoz társított eszköz regisztrációját. Ezután, amikor az eszközök ismét kiépítik a frissített tanúsítványokat, mindegyikhez új eszköz regisztrálása lesz létrehozva.     

    ![IoT hub-eszköz regisztrációjának eltávolítása](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második módszer a támogatás újraépítésének használata az eszközök ugyanarra az IoT-hubhoz való újraépítésére. Ezzel a módszerrel lehet lecserélni az IoT hub-beli eszközök regisztrációjának tanúsítványait. További információ: [az eszközök újraépítése](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Regisztrációs csoportok és a tanúsítvány lejárata

Ha a tanúsítványok lejáratának kezeléséhez a tanúsítványokat végzi el, a következő módon kell használnia a másodlagos tanúsítvány konfigurációját, hogy az eszközök ne legyenek leállással a kiépítési kísérletekhez.

Később, amikor a másodlagos tanúsítvány közel van a lejárathoz, és azt el kell érni, az elsődleges konfiguráció használatával forgatható. Az elsődleges és a másodlagos tanúsítványok közötti elforgatás nem biztosít állásidőt a kiépíteni próbált eszközök számára. 

#### <a name="update-expiring-root-ca-certificates"></a>Lejáró legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok frissítése

1. Kövesse az [ellenőrzött hitelesítésszolgáltatói tanúsítványok konfigurálása](how-to-verify-certificates.md) az új legfelső szintű hitelesítésszolgáltatói tanúsítványok hozzáadásához és ellenőrzéséhez című témakör lépéseit.

2. Kattintson a **regisztrációk kezelése** lapra az eszköz kiépítési szolgáltatási példányához, majd kattintson a **regisztrációs csoportok** listára. Kattintson a regisztrációs csoport nevére a listában.

3. Kattintson a **hitelesítésszolgáltatói tanúsítvány** elemre, majd válassza ki az új legfelső szintű hitelesítésszolgáltatói tanúsítványt a **másodlagos tanúsítvány** konfigurálása területen. Ezután kattintson a **Mentés** gombra. 

    ![Válassza ki az új legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a lejárathoz](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Később, amikor az elsődleges tanúsítvány lejárt, kattintson az eszköz kiépítési szolgáltatási példányának **tanúsítványok** fülére. Kattintson a lejárt tanúsítványra a listában, majd kattintson a **Törlés** gombra. Erősítse meg a törlést a tanúsítvány nevének megadásával, majd kattintson **az OK** gombra.

    ![Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány törlése](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Lejáró közbenső tanúsítványok frissítése


1. Kattintson a **beléptetési csoportok** elemre, majd kattintson a csoport nevére a listában. 

2. Kattintson a **másodlagos tanúsítvány** elemre, majd kattintson a mappa ikonra a beléptetési bejegyzéshez feltölteni kívánt új tanúsítvány kiválasztásához. Kattintson a **Mentés** gombra.

    Ezt az új közbenső tanúsítványt olyan ellenőrzött legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal kell aláírni, amely már hozzá lett adva a kiépítési szolgáltatáshoz. További információ: [X. 509 tanúsítványok](concepts-x509-attestation.md#x509-certificates).

   ![Beléptetési csoportok kezelése a másodlagos tanúsítvány lejáratával](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Később, amikor az elsődleges tanúsítvány lejárt, térjen vissza, és törölje az elsődleges tanúsítványt az **aktuális tanúsítvány törlése** gombra kattintva.


## <a name="reprovision-the-device"></a>Az eszköz újraépítése

Ha a tanúsítvány az eszközön és az eszköz kiépítési szolgáltatásán is hengerelt, az eszköz maga is kiépítheti a kapcsolatot az eszköz kiépítési szolgáltatásával. 

Az eszközök újbóli kiépítésének egyik egyszerű módja, ha az eszköz beprogramozásával kapcsolatba lép a kiépítési szolgáltatással a kiépítési folyamaton, ha az eszköz "jogosulatlan" hibaüzenetet kap az IoT hub-hoz való kapcsolódás megkísérlése közben.

Egy másik lehetőség, hogy a régi és az új tanúsítványok is érvényesek legyenek egy rövid átfedésre, és az IoT hub használatával parancsokat küldenek az eszközöknek, hogy a kiépítési szolgáltatás segítségével újra regisztrálják a IoT Hub kapcsolódási adataikat. Mivel az egyes eszközök különbözőképpen tudják feldolgozni a parancsokat, meg kell programozni az eszközt, hogy megtudja, mi a teendő a parancs meghívásakor. Több módon is megadhatja az eszközt IoT Hubon keresztül, és azt javasoljuk, hogy a folyamat elindításához [közvetlen metódusokat](../iot-hub/iot-hub-devguide-direct-methods.md) vagy [feladatokat](../iot-hub/iot-hub-devguide-jobs.md) használjon.

Az újratelepítést követően az eszközök az új tanúsítványokkal csatlakozhatnak IoT Hubhoz.


## <a name="disallow-certificates"></a>Tanúsítványok letiltása

Biztonsági rések esetén előfordulhat, hogy le kell állítania az eszköz tanúsítványát. Az eszköz tanúsítványának letiltásához tiltsa le a beléptetési bejegyzést a célként megadott eszközhöz vagy tanúsítványhoz. További információkért lásd: az eszközök letiltása a nem [beléptetés kezelése](how-to-revoke-device-access-portal.md) cikkben.

Ha egy tanúsítvány egy letiltott beléptetési bejegyzés részeként szerepel, akkor a tanúsítványokat használó IoT hub-ban való regisztrálásra tett kísérletek akkor is sikertelenek lesznek, ha egy másik beléptetési bejegyzés részeként engedélyezve van.
 



## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni az eszköz kiépítési szolgáltatásában található X. 509 tanúsítványokról, tekintse meg az [x. 509](concepts-x509-attestation.md) tanúsítványok igazolása című témakört. 
- Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványoknak az Azure IoT Hub Device Provisioning Service használatával történő igazolásával kapcsolatos további információkért lásd: [tanúsítványok ellenőrzése](how-to-verify-certificates.md)
- Ha szeretne többet megtudni arról, hogyan használható a portál egy regisztrációs csoport létrehozásához, tekintse meg [az eszközök regisztrálásának kezelése a Azure Portal](how-to-manage-enrollments.md)használatával című témakört.