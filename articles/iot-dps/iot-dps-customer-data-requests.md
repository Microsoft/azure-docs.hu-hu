---
title: Az Azure DPS-eszközök ügyféladatok-kérelmi funkciói
description: A személyes Azure Device kiépítési szolgáltatásban (DPS) felügyelt eszközök esetében ez a cikk a személyes adatexportálás vagy-törlés rendszergazdáit mutatja be.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 75a2762a0937efbceaa168f8a2d6409e2e3a7ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967227"
---
# <a name="summary-of-customer-data-request-features"></a>Az ügyféladat-kérési funkciók összegzése

Az Azure IoT Hub Device Provisioning Service olyan nagyvállalati ügyfeleknek szánt, REST API alapú felhőalapú szolgáltatás, amely lehetővé teszi, hogy az eszközök zökkenőmentes, automatizált, nulla szintű kiépíthető legyenek az Azure IoT Hubba az eszközön kezdődő és a felhővel végződő biztonsággal.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

A bérlői rendszergazda a regisztrációs azonosítót és az eszköz AZONOSÍTÓját rendeli hozzá az egyes eszközökhöz. Ezekről az eszközökről származó adatok ezen azonosítók alapján jelennek meg. A Microsoft nem tart fenn információt, és nem fér hozzá olyan adatokhoz, amelyek lehetővé tennék ezeknek az eszközöknek a megfeleltetését egy adott személy számára.

A Device kiépítési szolgáltatásban kezelt eszközök többsége nem személyes eszköz, például irodai termosztát vagy gyári robot. Előfordulhat azonban, hogy egyes eszközök személyes azonosításra alkalmasak, és saját belátásuk szerint fenntartják saját eszközeiket vagy leltározási követési módszereiket, amelyek az eszközöket az egyéni felhasználók számára használják. A Device kiépítési szolgáltatás az eszközökhöz kapcsolódó összes olyan adatmennyiséget kezeli és tárolja, mintha személyes adatként lenne.

A bérlői rendszergazdák vagy a Azure Portal vagy a szolgáltatás REST API-jai segítségével teljesítik az adatkéréseket az eszköz-AZONOSÍTÓhoz vagy a regisztrációs AZONOSÍTÓhoz társított adatok exportálásával vagy törlésével.

> [!NOTE]
> Az Azure IoT Hub eszköz kiépítési szolgáltatásán keresztül kiépített eszközökön további, az Azure IoT Hub szolgáltatásban tárolt információk találhatók. Az adott eszközre vonatkozó teljes kérelem elvégzéséhez tekintse meg az [Azure IoT hub dokumentációját](../iot-hub/iot-hub-customer-data-requests.md) .

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A Device kiépítési szolgáltatás a regisztrációkat és a regisztrációs rekordokat tárolja. A regisztrációk tartalmazzák a kiépíthető eszközökre vonatkozó információkat, a regisztrációs rekordok pedig azt mutatják be, hogy mely eszközökre van már lehetőség a kiépítési folyamat során.

A bérlői rendszergazdák eltávolíthatják a regisztrációkat a Azure Portalból, és ezzel eltávolítja a hozzá tartozó regisztrációs rekordokat is.

További információ: [az eszközök regisztrálásának kezelése](how-to-manage-enrollments.md).

A REST API-k használatával a beléptetésekhez és a regisztrációs rekordokhoz is végezhet törlési műveleteket:

* Egyetlen eszköz regisztrációs adatainak törléséhez használhatja az [eszközök beléptetése – törlés](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment)lehetőséget.
* Az eszközök egy csoportjára vonatkozó regisztrációs információk törléséhez használhatja az [eszköz beléptetési csoportjának törlését](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* A kiépített eszközökre vonatkozó információk törléséhez használhatja a regisztráció [állapotát – a regisztráció](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate)állapotának törlése lehetőséget.

## <a name="exporting-customer-data"></a>Ügyféladatok exportálása

A Device kiépítési szolgáltatás a regisztrációkat és a regisztrációs rekordokat tárolja. A regisztrációk tartalmazzák a kiépíthető eszközökre vonatkozó információkat, a regisztrációs rekordok pedig azt mutatják be, hogy mely eszközökre van már lehetőség a kiépítési folyamat során.

A bérlői rendszergazdák a Azure Portal keresztül tekinthetik meg a regisztrációkat és a regisztrációs rekordokat, és a másolás és Beillesztés paranccsal exportálhatók.

A regisztrációk kezelésével kapcsolatos további információkért lásd: [az eszközök regisztrálásának kezelése](how-to-manage-enrollments.md).

A REST API-k használatával a beléptetésekhez és a regisztrációs rekordokhoz is végezhet exportálási műveleteket:

* Egyetlen eszköz regisztrálási adatainak exportálásához használhatja az [eszközök beléptetése – lekérés](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment)lehetőséget.
* Az eszközök egy csoportjára vonatkozó regisztrációs információk exportálásához használja az eszközök [beléptetése csoportot – Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* A már kiépített eszközökre vonatkozó információk exportálásához használhatja a [regisztráció állapotát – regisztrációs állapot beolvasása](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> A Microsoft nagyvállalati szolgáltatásainak használatakor a Microsoft bizonyos adatokat hoz létre a rendszer által létrehozott naplók néven. Egyes eszközök kiépítési szolgáltatás rendszer által generált naplói nem érhetők el és nem exportálhatók a bérlői rendszergazdák számára. Ezek a naplók a szolgáltatáson belüli, illetve az egyes eszközökhöz kapcsolódó diagnosztikai adatokból származó ténybeli műveleteket alkotnak.

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

A Device kiépítési szolgáltatás API-jai teljes dokumentációja a következő helyen található: [https://docs.microsoft.com/rest/api/iot-dps](/rest/api/iot-dps) .

Az Azure IoT Hub [ügyfél-adatkérés funkciói](../iot-hub/iot-hub-customer-data-requests.md).