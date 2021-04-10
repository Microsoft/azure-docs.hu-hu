---
title: Az Azure IoT Hub eszköz frissítésének bemutatása | Microsoft Docs
description: Az IoT Hub eszköz frissítése olyan szolgáltatás, amely lehetővé teszi a IoT-eszközökön elérhető over-Air (OTA) frissítések telepítését.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 092078a79124682f7ee5c7824d4f7906c6e35475
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558499"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>A IoT Hub eszköz frissítése (előzetes verzió) – áttekintés

Az IoT Hub eszköz frissítése olyan szolgáltatás, amely lehetővé teszi a IoT-eszközökön elérhető over-Air (OTA) frissítések telepítését.

Mivel a szervezetek a termelékenység és a működési hatékonyság további engedélyezését is lehetővé teszik, a eszközök internetes hálózata (IoT) megoldások továbbra is egyre nagyobb arányban lesznek elfogadva. Ez alapvető fontosságú, hogy az ezeket a megoldásokat alkotó eszközök a megbízhatóság és a biztonság alapjaira épülnek, és könnyen csatlakoztathatók és kezelhetők. Az IoT Hub eszköz frissítése egy teljes körű platform, amelyet az ügyfelek a kis érzékelőktől az átjáró szintű eszközökig történő közzétételhez, terjesztéshez és felügyelethez használhatnak. 

Az IoT-kompatibilis digitális átalakítás teljes előnyeinek kihasználásához az ügyfeleknek szükségük van a nagy mennyiségű eszköz üzemeltetésére, karbantartására és frissítésére. Ismerkedjen meg az IoT Hub eszköz frissítésének előnyeivel, amelyekkel gyorsan reagálhat a biztonsági fenyegetésekre, és új funkciókat helyezhet üzembe az üzleti célok eléréséhez anélkül, hogy a saját frissítési platformok kiépítésének külön fejlesztési és karbantartási költségeit kellene kifizetnie.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Számos IoT-eszköz támogatása


A IoT Hub eszközének frissítése úgy lett kialakítva, hogy az [Azure IoT hub](https://azure.microsoft.com/en-us/services/iot-hub/)-nal való integráció révén optimalizálható legyen az optimalizált frissítések telepítése és az egyszerűsített műveletek. Ez az integráció megkönnyíti az eszköz frissítésének elfogadását bármely meglévő megoldáson. Felhőalapú megoldást kínál a gyakorlatilag bármilyen eszköz csatlakoztatására. Az eszköz frissítése a IoT operációs rendszerek széles körét támogatja – például a Linux és az [Azure RTOS](https://azure.microsoft.com/en-us/services/rtos/) (valós idejű operációs rendszer) –, és a nyílt forráskódon keresztül bővíthető. A NXP, a STMicroelectronics, a Renesas és a microchip szolgáltatással együtt fejlesztjük az eszközök frissítését IoT Hub ajánlatokhoz. Tekintse meg a Key Semiconductors próbaverziós táblák [mintáit](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) , amely tartalmazza az első lépéseket ismertető útmutatót, amelyből megtudhatja, hogyan konfigurálhatja, építheti és helyezheti üzembe a over-the-Air (OTA) frissítéseket az MCU osztályú eszközökhöz. 

Az eszköz Update Agent Simulator bináris és málna PI-Yocto lemezképek is elérhetők.
A IoT Hub eszköz frissítése támogatja a Azure IoT Edge-eszközök frissítését is. Az Ubuntu Server 18,04 amd64 platformhoz egy eszköz-frissítési ügynök van megadva. A IoT Hub eszköz frissítése is biztosít nyílt forráskódot, ha nem a fenti platformok egyikét futtatja. Az ügynököt az Ön által futtatott disztribúcióhoz is elvégezheti.

Az eszköz frissítése IoT Plug and Play (PnP) működik, és képes a szükséges PnP-felületeket támogató bármely eszköz kezelésére. További információ: [IoT hub-és IoT-Plug and Play eszköz frissítése](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>A frissítési összetevők széles körének támogatása

A IoT Hub eszköz frissítése két frissítési formát támogat – a rendszerkép-alapú és a Package-alapú.

A Package-alapú frissítések olyan célzott frissítések, amelyek csak egy adott összetevőt vagy alkalmazást módosítanak az eszközön. Ez csökkenti a sávszélesség-használatot, és csökkenti a frissítés letöltésének és telepítésének idejét. A csomagok frissítései általában lehetővé teszik, hogy az eszközök kevesebb állásidőt alkalmazzanak, amikor frissítést alkalmaznak, és elkerülik a lemezképek létrehozásának terhelését.

A rendszerkép frissítései magasabb szintű megbízhatóságot biztosítanak az eszköz állapotában. Általában könnyebb replikálni az éles üzem előtti környezet és az éles környezet közötti rendszerképek eredményeit, mivel az nem ugyanazokat a kihívásokat eredményezi, mint a csomagok és a függőségeik.
Az Atom jellegéből adódóan az egyik A/B feladatátvételi modellt is könnyedén elvégezheti.

Nincs egy megfelelő válasz, és a konkrét használati esetek alapján eltérő választ kaphat. A IoT Hub eszköz frissítése támogatja a frissítés rendszerképét és a csomag formáját is, így kiválaszthatja az eszköz környezetének megfelelő frissítési modellt.

## <a name="flexible-features-for-updating-devices"></a>Rugalmas funkciók az eszközök frissítéséhez

A IoT Hub-funkciók eszközének frissítése hatékony és rugalmas élményt biztosít, többek között:

* Az Azure IoT Hub-vel integrált Update Management UX
* Fokozatos frissítés bevezetése az eszközök csoportosításával és az ütemezési vezérlők frissítésével
* Programozási API-k az automatizálás és az egyéni portálok felhasználói élményének engedélyezéséhez
* A frissítési megfelelőség és az állapot nézeteinek áttekintése a különböző-eszköz flottái között
* Rugalmas eszközök frissítéseinek támogatása (A/B) a zökkenőmentes visszaállítás érdekében
* Az előfizetés és a szerepköralapú hozzáférés-vezérlés az Azure.com-portálon keresztül érhető el
* Helyszíni tartalom-gyorsítótárazás és beágyazott peremhálózati támogatás a felhőben leválasztott eszközök frissítésének engedélyezéséhez
* Részletes frissítési felügyeleti és Jelentéskészítő eszközök 

A IoT Hub felügyeletére és üzembe helyezésére szolgáló eszközök frissítése révén a felhasználók maximalizálhatja a hatékonyságot, és értékes időt takaríthat meg. A IoT Hub eszköz frissítése magában foglalja az eszközök csoportosításának lehetőségét, valamint annak meghatározását, hogy mely eszközökre legyenek telepítve a frissítések. A felhasználók emellett megtekinthetik a frissítési központi telepítések állapotát, és gondoskodnak arról, hogy minden eszköz sikeresen alkalmazza a frissítéseket.

Ha frissítési hiba történik, az IoT Hub eszköz frissítése azt is lehetővé teszi a felhasználóknak, hogy azonosítsák azokat az eszközöket, amelyek nem tudták alkalmazni a frissítést, valamint a kapcsolódó hiba részleteinek megtekintése. A forrás azonosítására tett kísérlet során a rendszer nem tudja azonosítani, hogy mely eszközök nem frissíthetők.

### <a name="best-in-class-security-at-global-scale"></a>Legjobb szintű biztonság globális méretekben

Microsoft Azure a világ több mint egy milliárd IoT-eszközét támogatja – ez egy szám, amely a nap folyamán gyorsan növekszik. A IoT Hub eszköz frissítése erre a lehetőségre és a Windows Update platform által bizonyítottan bevált megbízhatóságra épül, így az eszközök globális méretekben zökkenőmentesen frissíthetők.

Az IoT Hub eszköz frissítése átfogó, a Microsoft Azure számára kifejlesztett Felhőbeli biztonsági biztonságot használ, így az ügyfeleknek nem kell időt fordítaniuk arra, hogy az alapoktól kezdve felépítsék őket.


## <a name="device-update-workflows"></a>Eszköz-frissítési munkafolyamatok

Az eszköz frissítési funkciója három területre bontható: az ügynök-integráció, az Importálás és a felügyelet.

### <a name="device-update-agent"></a>Eszköz frissítési ügynöke

Ha egy eszközön egy frissítési parancs érkezik, a rendszer végrehajtja a frissítés kért fázisát (letöltés, telepítés és alkalmazás). Az egyes fázisokban az állapot a IoT Hubon keresztül visszakerül az eszköz frissítéseire, így megtekintheti a központi telepítés aktuális állapotát. Ha nincsenek folyamatban lévő frissítések, a rendszer az állapotot "tétlen" értékre adja vissza. Egy központi telepítés bármikor megszakítható.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Az eszköz frissítési ügynöke munkafolyamatának ábrája." lightbox="media/understand-device-update/client-agent-workflow.png":::

[További](device-update-agent-overview.md) információ az eszköz frissítési ügynökéről. 

### <a name="importing"></a>Importálás

Az importálás módja, hogy a frissítések hogyan kerülnek be az eszköz frissítéseibe, hogy azok üzembe helyezhetők legyenek az eszközökön. Az eszköz frissítése minden eszközön egyetlen frissítést támogat. Ez ideális olyan teljes képfrissítésekhez, amelyek egy teljes operációsrendszer-partíciót egyszerre frissítenek, vagy egy olyan apt-jegyzékfájlt, amely az eszközön frissíteni kívánt összes csomagot leírja. A frissítések eszköz frissítésbe való importálásához először létre kell hoznia egy, a frissítést leíró importálási jegyzékfájlt, majd fel kell töltenie a frissítési fájl (oka) t és az importálási jegyzékfájlt egy internetről elérhető helyre. Ezt követően használhatja a Azure Portal vagy az [eszköz frissítésének importálási REST API](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) az importálás aszinkron folyamatának elindításához. Az eszköz frissítése feltölti a fájlokat, feldolgozza őket, és elérhetővé teszi azokat a IoT-eszközök számára történő terjesztéshez.

Bizalmas tartalom esetén a letöltést egy közös hozzáférési aláírás (SAS) használatával, például az Azure Blob Storage ad-hoc SAS-vel kell ellátni. [További információ az SAS-ról](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Az eszköz frissítésének diagramja IoT Hub importálási munkafolyamat." lightbox="media/understand-device-update/import-update.png":::

[További](import-concepts.md) információ a frissítések importálásáról. 

### <a name="grouping-and-deployment"></a>Csoportosítás és üzembe helyezés

A frissítés importálása után megtekintheti az eszközök és az eszközosztály kompatibilis frissítéseit.

Az eszköz frissítése támogatja a **csoportok** fogalmát a címkéken keresztül a IoT Hubban. A frissítések tesztelési csoportba való üzembe helyezése először is jó módja annak, hogy csökkentse a problémák kockázatát az éles környezetben.

Az eszköz frissítése során a központi telepítések lehetővé teszik a megfelelő tartalom összekapcsolását a kompatibilis eszközök adott készletéhez. Az eszköz frissítése összehangolja a parancsok küldésének folyamatát az egyes eszközökre, és arra utasítja őket, hogy töltse le és telepítse a frissítéseket, és az állapotot visszakapja.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="IoT Hub csoportosítási és üzembe helyezési munkafolyamatához tartozó eszköz frissítésének ábrája." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[További](device-update-compliance.md) információ az üzembe helyezési fogalmakról

[További](device-update-groups.md) információ az eszköz frissítési csoportjairól


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Eszköz frissítési fiókjának és példányának létrehozása](create-device-update-account.md)