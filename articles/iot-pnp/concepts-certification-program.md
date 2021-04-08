---
title: Azure Certified eszköz program | Microsoft Docs
description: Ismerje meg az Azure Certified eszköz programját.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92048100"
---
# <a name="what-is-the-azure-certified-device-program"></a>Mi az Azure Certified eszköz program?

Az Azure Certified eszköz program gondoskodik az ügyfelek megoldásai zökkenőmentes működéséről az Azure-szolgáltatásokkal. A program eszközök, szolgáltatások és piactér használatával osztja meg az iparági ismereteket és az ajánlott eljárásokat az eszköz-és megoldás-építők közösségével.

A program célja a következő:

- **Bizalom nyújtása az ügyfelek számára:** Az ügyfelek magabiztosan vásárolhatják meg a Microsoft által az Azure-szolgáltatásokkal való használatra hitelesített eszközöket.

- **Segítse a felhasználókat a megoldás megfelelő eszközeinek megtalálásában:** Az eszközök építői a minősítési folyamat részeként tehetik közzé az eszközeik egyedi képességeit. Az ügyfelek könnyedén megtalálják az igényeiknek megfelelő termékeket.

- **Minősített eszközök előléptetése:** Az eszköz-építők nagyobb láthatóságot, kapcsolattartást biztosítanak az ügyfelekkel, és az Azure Certified eszköz márkáját használják.

Ez a cikk a következőket ismerteti:

- A vállalat bevezetését az Azure Certified eszköz programba.
- Határozza meg, hogy melyik minősítés alkalmazható az eszközre.
- Megkeresheti a program követelményeit és egyéb erőforrásait, hogy megismerkedjen a teszteléssel.
- Az Azure Certified eszköz portál használatával ellenőrizheti az eszközt.

## <a name="onboarding"></a>Előkészítés

Az [Azure Certified-eszköz portálon](https://aka.ms/acdp)az eszközök tanúsításához a következő lépéseket kell végrehajtania:

1. Győződjön meg arról, hogy vállalata rendelkezik egy munkahelyi vagy iskolai Bérlővel Azure Active Directory fiókkal.
2. Csatlakoztassa a [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) a fiók használatával.
3. Az MPN-hez való csatlakozást követően jelentkezzen be az [Azure Certified-eszköz portálra](https://aka.ms/acdp) .
4. A [programra vonatkozó szerződés](https://aka.ms/acdagreement) felülvizsgálata és aláírása a vállalati profil lapon

### <a name="company-profile"></a>Vállalati profil

Ha a vállalati profilt az Azure Certified Device portálon szeretné kezelni, válassza a **vállalati profil** lehetőséget. A vállalati profil tartalmazza a vállalati URL-címet, az e-mail-címet és az emblémát. A minősítési műveletek folytatása előtt fogadja el a programra vonatkozó szerződést ezen a lapon.

Az Azure Certified Device Catalog eszköz Leírás lapja a vállalati profil adatait használja.

## <a name="choose-the-certification"></a>A minősítés kiválasztása

Három különböző minősítés van, amelyek mindegyike egy másik ügyfél-érték megvalósítására koncentrál. Az Ön által felépített eszköz típusától és a célközönségtől függően kiválaszthatja a leginkább megfelelő minősítést vagy minősítéseket:

### <a name="azure-certified-device"></a>Azure Certified-eszköz

Az _Azure Certified eszköz minősítése_ ellenőrzi, hogy az eszköz csatlakozhat-e az Azure IoT hubhoz, és biztonságosan kiépíthető-e a Device kiépítési szolgáltatás (DPS) használatával. Ez a minősítés az eszköz funkcióit és együttműködési képességét tükrözi, amely a fejlettebb minősítésekhez szükséges alapkonfiguráció.

- További információt a [minősítési követelmények](https://aka.ms/acdrequirements)című témakörben talál.
- Ha többet szeretne megtudni a DPS használatával az eszköz Azure IoT Hubhoz való csatlakoztatásáról, tekintse meg a [kiépítési eszközök áttekintését](../iot-dps/about-iot-dps.md).

### <a name="iot-plug-and-play"></a>IoT Plug and Play

A _IoT Plug and Play minősítést_, az Azure Certified minősítésű tanúsítványok növekményes minősítését, leegyszerűsíti az eszközök egyéni eszköz kód nélküli kiépítési folyamatát. A IoT Plug and Play lehetővé teszi, hogy a hardveres partnerek olyan eszközöket építsenek ki, amelyek az Azure IoT Central és a harmadik féltől származó megoldások alapján könnyen integrálhatók a felhőalapú megoldásokkal.

- További információt a [minősítési követelmények](https://aka.ms/acdiotpnprequirements)című témakörben talál.
- Ha többet szeretne megtudni arról, hogyan készítse elő az eszközt a IoT Plug and Play teszteléshez, olvassa el a következő témakört: a [IoT Plug and Play-eszközök hitelesítése](howto-certify-device.md).

### <a name="edge-managed"></a>Edge által felügyelt

Az _Edge által felügyelt minősítés_, az Azure Certified minősítésű tanúsítvány növekményes minősítése a Windows, Linux vagy RTOS rendszerű Azure IoT-eszközökre koncentrál. Jelenleg ez a program-minősítés a modul üzembe helyezésének és felügyeletének Edge Runtime-kompatibilitására koncentrál.

> [!TIP]
> A program korábbi neve _IoT Edge minősítési program_ volt.

- További információt a [minősítési követelmények](https://aka.ms/acdedgemanagedrequirements)című témakörben talál.
- További információ a IoT Edgeről: [IoT Edge Overview (áttekintés](../iot-edge/about-iot-edge.md)).
- További információ a támogatott operációs rendszerekről és tárolókra: [IoT Edge támogatott rendszerek](../iot-edge/support.md).

## <a name="use-the-azure-certified-device-portal"></a>Az Azure Certified-eszköz portál használata

Ez a szakasz az [Azure Certified-eszköz portál](https://certify.azure.com)használatát foglalja össze. További információért lásd a [portál útmutatójának első lépéseit](https://aka.ms/acdhelp).

Ha hitelesíteni szeretne egy eszközt az Azure Certified eszköz programban, hajtsa végre a következő négy lépést:

1. Adja meg az eszköz adatait
2. Az eszköz tesztelése
3. A felülvizsgálat elküldése és befejezése
4. Közzététel az Azure Certified-eszköz katalógusában (opcionális)

### <a name="provide-device-details"></a>Eszköz adatainak megadása

Minden hitelesíteni kívánt eszközhöz használja a minősítési portál űrlapait az eszköz hardverével, a telepítési utasításokkal és a marketing anyaggal kapcsolatos részletek rögzítéséhez:

- **Eszköz adatai:** Adatokat gyűjt az eszközről, például a nevét, a leírását, a hardver részleteit és az operációs rendszert.
- Első **lépések útmutató**: egy PDF-dokumentum, amelyet az ügyfél a termék gyors használatához használhat fel. A [sablonok](https://aka.ms/GSTemplate) elérhetők.
- **Marketing részletei:** Megadhatja az eszközre felkészített marketing-információkat, például a képek és a forgalmazók adatait.
- **További iparági minősítések:** Egy választható szakasz, amely lehetővé teszi az eszközön található egyéb minősítésekkel kapcsolatos információk megadását.

### <a name="test-the-device"></a>Az eszköz tesztelése

Ez a fázis együttműködik az eszközzel, és több tesztet futtat, miután az eszköz a DPS használatával csatlakozik IoT Hubhoz. Befejezésekor megtekintheti a naplófájlok készletét az eszköz tesztelési eredményeivel.

A minősítési portál útmutatást nyújt a teszteléshez használt IoT Hub-példányhoz való kapcsolódáshoz. A DPS-kapcsolatot bármely [támogatott igazolási módszer](../iot-dps/concepts-service.md#attestation-mechanism)használatával létrehozhatja.

Az Azure Certified-eszköz csapata az eszköz további manuális ellenőrzéséhez felveheti a kapcsolatot az Eszközkezelővel.

### <a name="submit-and-publish"></a>Küldés és közzététel

Az utolsó szükséges szakasz a projekt véleményezésre való elküldése. Ez a lépés értesíti az Azure Certified eszköz csapattagot, hogy áttekintse a projekt teljességét, beleértve az eszköz és a marketing részleteit, valamint az első lépéseket ismertető útmutatót. A csapattag felveheti Önnel a kapcsolatot a korábban a kérdésekkel vagy a kérések szerkesztése előtt megadott vállalati e-mail-címen a jóváhagyás előtt.

Ha az eszközön további manuális ellenőrzésre van szükség a minősítés részeként, akkor a rendszer jelenleg értesítést fog kapni.

Egy eszköz minősítése **esetén a termék** összegzése lapon közzéteheti a termék részleteit az Azure Certified-eszköz katalógusában.

## <a name="if-you-have-questions"></a>Ha kérdése van

[iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question)Ha bármilyen kérdése van a minősítési programok, a minősítési portál vagy az Azure Certified-eszköz katalógusával kapcsolatban, vegye fel a kapcsolatot a csapattal.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure Certified eszköz programját, a következő lépés a [IoT Plug and Play-eszközök hitelesítésének](howto-certify-device.md)megismerése.