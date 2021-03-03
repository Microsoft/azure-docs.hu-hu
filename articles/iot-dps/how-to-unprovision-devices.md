---
title: Az Azure-IoT Hub Device Provisioning Service kiépített eszközök megszüntetése
description: Az Azure IoT Hub Device Provisioning Service (DPS) használatával kiépített eszközök megszüntetése
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 7c91cd8cbb1a927a88f05fcea603c6a4ed6e1c14
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719032"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Korábban automatikusan kiépített eszközök kiépítése 

Előfordulhat, hogy meg kell szüntetnie az eszköz kiépítési szolgáltatásával korábban automatikusan kiépített eszközök kiépítését. Előfordulhat például, hogy egy eszközt egy másik IoT-hubhoz lehet értékesíteni vagy áthelyezni, vagy az elveszett, ellopták vagy más módon sérült. 

Általánosságban elmondható, hogy az eszköz kiépítése két lépést tesz szükségessé:

1. Az eszköz regisztrációjának törlése a kiépítési szolgáltatásból a jövőbeli automatikus kiépítés megelőzése érdekében. Attól függően, hogy átmenetileg vagy véglegesen szeretné-e visszavonni a hozzáférést, érdemes lehet letiltani vagy törölni egy beléptetési bejegyzést. Az X. 509 igazolást használó eszközök esetében érdemes lehet letiltani/törölni egy bejegyzést a meglévő regisztrációs csoportok hierarchiájában.  
 
   - További információ az eszközök regisztrálásáról: az [eszköz regisztrálása az Azure-ból IoT hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Ha meg szeretné tudni, hogyan lehet az eszközöket programozott módon felvenni az egyik kiépítési szolgáltatási SDK használatával, tekintse meg az [eszközök regisztrálásának kezelése a Service SDK-val](./quick-enroll-device-x509-java.md)című témakört.

2. Regisztrálja az eszközt a IoT Hub, hogy elkerülje a jövőbeli kommunikációt és az adatátvitelt. Újra ideiglenesen letilthatja vagy véglegesen törölheti az eszköz bejegyzését az azonosító beállításjegyzékében arra a IoT Hub, ahol üzembe lett helyezve. További információ a [tiltásról: eszközök letiltása](../iot-hub/iot-hub-devguide-identity-registry.md#disable-devices) . A [Azure Portalban](https://portal.azure.com)tekintse meg az IoT hub erőforrás "eszközkezelés/IoT eszközei" című témakört.

Az eszközök kiépítésének pontos lépései az igazolási mechanizmustól és a kiépítési szolgáltatáshoz kapcsolódó regisztrációs bejegyzéstől függenek. A következő szakasz áttekintést nyújt a folyamatról a regisztráció és az igazolás típusa alapján.

## <a name="individual-enrollments"></a>Egyéni regisztrációk
A TPM-igazolást vagy az X. 509 igazolást használó eszközöket egy külön beléptetési bejegyzés keretében kell kiépíteni. 

Egyéni regisztrációval rendelkező eszköz kiépítése: 

1. Az eszköz regisztrációjának törlése a kiépítési szolgáltatásból:

   - A TPM-igazolást használó eszközök esetében törölje az egyéni beléptetési bejegyzést, hogy véglegesen visszavonja az eszköz hozzáférését a kiépítési szolgáltatáshoz, vagy tiltsa le a bejegyzést a hozzáférés ideiglenes visszavonásához. 
   - Az X. 509 igazolást használó eszközök esetében törölheti vagy letilthatja a bejegyzést. Ügyeljen arra, hogy ha töröl egy, az X. 509 protokollt használó eszközhöz tartozó egyéni regisztrációt, és létezik egy engedélyezett regisztrációs csoport az adott eszközhöz tartozó tanúsítványlánc aláíró tanúsítványához, az eszköz újra regisztrálhat. Ilyen eszközök esetén biztonságosabb lehet a beléptetési bejegyzés letiltása. Ezzel megakadályozza, hogy az eszköz újból regisztrálja az eszközt, függetlenül attól, hogy van-e engedélyezve beléptetési csoport az egyik aláíró tanúsítványához.

2. Tiltsa le vagy törölje az eszközt azon az IoT hub Identity registryben, amelyről kiépített. 


## <a name="enrollment-groups"></a>Beléptetési csoportok
Az X. 509 igazolásával az eszközök beléptetési csoporton keresztül is üzembe helyezhetők. A beléptetési csoportok az aláíró tanúsítvánnyal, vagy egy köztes vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal vannak konfigurálva, és a tanúsítványhoz tartozó eszközökhöz való hozzáférés szabályozása a tanúsítvány láncában. További információ a regisztrációs csoportokról és az X. 509 tanúsítványokról a kiépítési szolgáltatással kapcsolatban: [x. 509 tanúsítvány igazolása](concepts-x509-attestation.md). 

A regisztrációs csoporton keresztül kiépített eszközök listájának megtekintéséhez tekintse meg a regisztrációs csoport adatait. Ezzel a módszerrel egyszerűen megtudhatja, hogy melyik IoT hub-eszköz lett kiépítve. Az eszközök listájának megtekintése: 

1. Jelentkezzen be a Azure Portalba, és a bal oldali menüben kattintson az **összes erőforrás** elemre.
2. Az erőforrások listájában kattintson a kiépítési szolgáltatásra.
3. A kiépítési szolgáltatásban kattintson a **regisztrációk kezelése**, majd a **beléptetési csoportok** lap elemre.
4. Kattintson a beléptetési csoportra a megnyitásához.

   ![Regisztrációs csoport bejegyzésének megtekintése a portálon](./media/how-to-unprovision-devices/view-enrollment-group.png)

A beléptetési csoportok esetében kétféle esetben érdemes figyelembe venni a következőket:

- A beléptetési csoporton keresztül kiépített összes eszköz kivonása:
  1. Tiltsa le a beléptetési csoportot, hogy ne engedélyezze az aláíró tanúsítványát. 
  2. A regisztrációs csoport kiépített eszközeinek listájával letilthatja vagy törölheti az egyes eszközöket a megfelelő IoT hub azonosító beállításjegyzékében. 
  3. Miután letiltotta vagy törölte az összes eszközt a megfelelő IoT-hubokból, lehetősége van törölni a beléptetési csoportot. Ügyeljen arra, hogy ha törli a beléptetési csoportot, és van egy engedélyezett regisztrációs csoport egy vagy több eszközhöz tartozó tanúsítványláncnél magasabb szintű aláíró tanúsítványhoz, akkor ezek az eszközök újra regisztrálhatnak. 

- Egyetlen eszköz kiépítése egy beléptetési csoportból:
  1. Hozzon létre egy letiltott egyéni regisztrációt a levél (eszköz) tanúsítványához. Ez visszavonja az eszköz kiépítési szolgáltatáshoz való hozzáférését, miközben továbbra is engedélyezi a hozzáférést azokhoz az eszközökhöz, amelyeken a regisztrációs csoport aláíró tanúsítványa szerepel a láncban. Ne törölje az eszköz letiltott egyéni regisztrációját. Ezzel lehetővé teszi, hogy az eszköz újra regisztrálja a regisztrációs csoportot. 
  2. A regisztrációs csoport kiépített eszközeinek listájával megkeresheti azt az IoT hubot, amelyet az eszköz kiépített, illetve amelyről letiltotta vagy törölheti az adott hub identitás-beállításjegyzékében.