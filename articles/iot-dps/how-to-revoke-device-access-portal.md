---
title: Eszköz regisztrációjának törlése az Azure IoT Hub Device Provisioning Service
description: Eszköz regisztrációjának törlése az Azure IoT Hub Device Provisioning Service (DPS) használatával történő kiépítés megakadályozása érdekében
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c75fcd1fd20e41df5018fcaa07fe83051d7e5f1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97740368"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Eszköz regisztrálása az Azure-ból IoT Hub Device Provisioning Service

Az eszköz hitelesítő adatainak megfelelő kezelése elengedhetetlen a nagy horderejű rendszerek, például a IoT-megoldások számára. Az ilyen rendszerekre vonatkozó ajánlott eljárás az, hogy az eszközök hozzáférésének visszavonása a hitelesítő adataik miatt visszavonható legyen, függetlenül attól, hogy a megosztott hozzáférési aláírások (SAS) tokenje vagy az X. 509 tanúsítványa sérült-e. 

Az eszközök kiépítési szolgáltatásban [való regisztrálása](about-iot-dps.md#provisioning-process)lehetővé teszi az eszköz kiosztását. A kiépített eszköz olyan, amely IoT Hub regisztrálva van, lehetővé téve, hogy megkapja a kezdeti [eszköz Twin](~/articles/iot-hub/iot-hub-devguide-device-twins.md) állapotát, és megkezdje a jelentéskészítési telemetria-adatgyűjtést. Ez a cikk azt ismerteti, hogyan lehet lemondani egy eszköz regisztrációját a kiépítési szolgáltatási példányról, ami megakadályozza, hogy a jövőben újra kiépíthető legyen.

> [!NOTE] 
> Ügyeljen arra, hogy a hozzáférését visszavonó eszközök újrapróbálkozási szabályzata. Előfordulhat például, hogy egy végtelen újrapróbálkozási házirenddel rendelkező eszköz folyamatosan próbálkozik a kiépítési szolgáltatással való regisztrálással. Ez a helyzet felhasználja a szolgáltatási erőforrásokat, és valószínűleg hatással van a teljesítményre.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Eszközök letiltása egyéni beléptetési bejegyzés használatával

Az egyes regisztrációk egyetlen eszközre érvényesek, és az X. 509 tanúsítványokat, a TPM-kiterjesztési kulcsokat (valós vagy virtuális TPM-ben) vagy SAS-jogkivonatokat használhatnak igazolási mechanizmusként. Ha egy olyan eszközt szeretne letiltani, amely egyéni regisztrációval rendelkezik, letilthatja vagy törölheti a beléptetési bejegyzést. 

Ha átmenetileg le szeretné tiltani az eszközt a beléptetési bejegyzés letiltásával: 

1. Jelentkezzen be a Azure Portalba, és válassza ki a **minden erőforrás** elemet a bal oldali menüben.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyről az eszközt le szeretné állítani.
3. A kiépítési szolgáltatásban válassza a **regisztrációk kezelése** lehetőséget, majd válassza az **Egyéni regisztrációk** lapot.
4. Válassza ki a letiltani kívánt eszköz beléptetési bejegyzését. 

    ![Egyéni regisztráció kiválasztása](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. A beléptetési lapon görgessen le az aljára, és válassza a **Letiltás** lehetőséget a **belépés engedélyezése** kapcsolóhoz, majd kattintson a **Mentés** gombra.  

   ![Egyéni beléptetési bejegyzés letiltása a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Az eszköz végleges letiltása a beléptetési bejegyzés törlésével:

1. Jelentkezzen be a Azure Portalba, és válassza ki a **minden erőforrás** elemet a bal oldali menüben.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyről az eszközt le szeretné állítani.
3. A kiépítési szolgáltatásban válassza a **regisztrációk kezelése** lehetőséget, majd válassza az **Egyéni regisztrációk** lapot.
4. Jelölje be a letiltani kívánt eszköz beléptetési bejegyzése melletti jelölőnégyzetet. 
5. Válassza a **Törlés** lehetőséget az ablak tetején, majd az **Igen** gombot választva erősítse meg, hogy el kívánja távolítani a beléptetést. 

   ![Egyéni beléptetési bejegyzés törlése a portálon](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Az eljárás befejezése után a bejegyzést a rendszer az egyéni regisztrációk listájáról távolítja el.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>X. 509 közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány letiltása egy regisztrációs csoport használatával

Az X. 509 tanúsítványok általában megbízhatósági tanúsítványlánc szerint vannak rendezve. Ha egy lánc bármely fázisában egy tanúsítvány sérül, a megbízhatóság megszakad. A tanúsítványnak nem szabad megelőznie, hogy az eszköz kiépítési szolgáltatása az adott tanúsítványt tartalmazó láncon kívülről kiépítse az eszközöket. Ha többet szeretne megtudni az X. 509 tanúsítványokról és azok használatáról a kiépítési szolgáltatással kapcsolatban, tekintse meg az [x. 509 tanúsítványokat](./concepts-x509-attestation.md#x509-certificates). 

A beléptetési csoport olyan eszközökre vonatkozó bejegyzés, amelyek közös igazolási mechanizmussal rendelkeznek az X. 509 tanúsítványokban, amelyek ugyanabban a közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓN vannak aláírva. A beléptetési csoport bejegyzése a közbenső vagy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓhoz társított X. 509 tanúsítvánnyal van konfigurálva. A bejegyzés is konfigurálható bármilyen konfigurációs értékkel (például Twin State és IoT hub-kapcsolatok), amelyeket az adott tanúsítvánnyal rendelkező eszközök közösen használnak a tanúsítványlánc számára. A tanúsítvány letiltásához letilthatja vagy törölheti a regisztrációs csoportot.

A tanúsítvány ideiglenes letiltása a beléptetési csoport letiltásával: 

1. Jelentkezzen be a Azure Portalba, és válassza ki a **minden erőforrás** elemet a bal oldali menüben.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyből le szeretné állítani az aláíró tanúsítványt.
3. A kiépítési szolgáltatásban válassza a **regisztrációk kezelése** lehetőséget, majd válassza a **regisztrációs csoportok** lapot.
4. Válassza ki a beléptetési csoportot a letiltani kívánt tanúsítvány használatával.
5. Válassza a **Letiltás** lehetőséget a **belépés engedélyezése** kapcsolón, majd kattintson a **Mentés** gombra.  

   ![Beléptetési csoport bejegyzésének letiltása a portálon](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
A tanúsítvány végleges letiltása a beléptetési csoport törlésével:

1. Jelentkezzen be a Azure Portalba, és válassza ki a **minden erőforrás** elemet a bal oldali menüben.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyről az eszközt le szeretné állítani.
3. A kiépítési szolgáltatásban válassza a **regisztrációk kezelése** lehetőséget, majd válassza a **regisztrációs csoportok** lapot.
4. Jelölje be a beléptetési csoport melletti jelölőnégyzetet a letiltani kívánt tanúsítványhoz. 
5. Válassza a **Törlés** lehetőséget az ablak tetején, majd az **Igen** gombot választva erősítse meg, hogy el kívánja távolítani a beléptetési csoportot. 

   ![Regisztrációs csoport bejegyzésének törlése a portálon](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Az eljárás befejezése után a bejegyzés el lett távolítva a regisztrációs csoportok listájából.  

> [!NOTE]
> Ha töröl egy tanúsítványigénylési csoportot a tanúsítványhoz, előfordulhat, hogy azok az eszközök, amelyeken a tanúsítványa szerepel a tanúsítvány láncában, továbbra is regisztrálhatók, ha a főtanúsítványhoz vagy egy másik közbenső tanúsítványhoz tartozó engedélyezett beléptetési csoport létezik a tanúsítvány láncában.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Megadott eszközök letiltása egy regisztrációs csoportban

Az X. 509 igazolási mechanizmust megvalósító eszközök az eszköz tanúsítványláncot és titkos kulcsát használják a hitelesítéshez. Amikor egy eszköz csatlakoztatja és hitelesíti az eszközt az eszköz kiépítési szolgáltatásával, a szolgáltatás először egy egyéni regisztrációt keres, amely megfelel az eszköz hitelesítő adatainak. A szolgáltatás ezután megkeresi a regisztrációs csoportokat annak megállapítására, hogy az eszköz üzembe helyezhető-e. Ha a szolgáltatás letiltott egyéni regisztrációt talál az eszközhöz, azzal megakadályozza, hogy az eszköz csatlakozzon. A szolgáltatás megakadályozza a csatlakozást, még akkor is, ha az eszköz tanúsítványlánc egyik közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja számára engedélyezett beléptetési csoport van. 

Ha egyetlen eszközt szeretne letiltani egy regisztrációs csoportban, kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure Portalba, és válassza ki a **minden erőforrás** elemet a bal oldali menüben.
2. Az erőforrások listájából válassza ki a letiltani kívánt eszköz beléptetési csoportját tartalmazó kiépítési szolgáltatást.
3. A kiépítési szolgáltatásban válassza a **regisztrációk kezelése** lehetőséget, majd válassza az **Egyéni regisztrációk** lapot.
4. Válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 
5. A **regisztráció hozzáadása** lapon válassza az **X. 509** lehetőséget az eszköz igazolási **mechanizmusa** .

    Töltse fel az eszköz tanúsítványát, és adja meg a letiltani kívánt eszköz AZONOSÍTÓját. A tanúsítvány esetében használja az eszközre telepített, aláírt végfelhasználói tanúsítványt. Az eszköz az aláírt végfelhasználói tanúsítványt használja a hitelesítéshez.

    ![Az eszköz tulajdonságainak beállítása a nem engedélyezett eszközhöz](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Görgessen a **regisztráció hozzáadása** lap aljára, és válassza a **Letiltás** lehetőséget a **belépés engedélyezése** kapcsolón, majd kattintson a **Mentés** gombra. 

    [![Letiltott egyéni beléptetési bejegyzés használata az eszköz csoportos regisztrációjának letiltásához a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

A regisztráció sikeres létrehozásakor az **Egyéni regisztrációk** lapon megjelenik a letiltott eszközök regisztrációja. 

## <a name="next-steps"></a>Következő lépések

A regisztráció a nagyobb megszüntetési folyamat része is. Az eszközök megszüntetése magában foglalja a kiépítési szolgáltatásból való kivonást és a IoT hub-ból való regisztrációt is. A teljes folyamattal kapcsolatos további tudnivalókért lásd: a [korábban automatikusan kiépített eszközök kiépítése](how-to-unprovision-devices.md) .
