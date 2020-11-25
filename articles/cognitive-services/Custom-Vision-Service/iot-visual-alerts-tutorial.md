---
title: 'Oktatóanyag: IoT vizuális riasztások mintája'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban Custom Visiont használ egy IoT-eszközzel, hogy felismerje és jelentse a vizualizációs állapotokat egy kamera Videójának hírcsatornájában.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: c6405e2fcddef9ae3228ede76dfa57f7542164c8
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020177"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Oktatóanyag: Custom Vision használata IoT-eszközzel a vizualizációs állapotok jelentéséhez

Ez a minta azt szemlélteti, hogyan használható a Custom Vision egy eszköz kamerával való betanításához a vizualizációs állapotok észlelése érdekében. Ezt az észlelési forgatókönyvet IoT-eszközön is futtathatja egy exportált ONNX-modell használatával.

A vizualizációs állapot leírja egy kép tartalmát: egy üres szobát vagy egy személyekkel rendelkező szobát, egy üres úttestről vagy egy teherautóval rendelkező úttestről, és így tovább. Az alábbi képen láthatja, hogy az alkalmazás felismeri, ha egy banán vagy egy Apple a kamera elé kerül.

![A kamera előtt található gyümölcsöt jelölő felhasználói felület animációja](./media/iot-visual-alerts-tutorial/scoring.gif)

Ez az oktatóanyag a következőket mutatja be:
> [!div class="checklist"]
> * Konfigurálja a minta alkalmazást saját Custom Vision használatára és IoT Hub erőforrásokra.
> * Az alkalmazás használatával betaníthatja Custom Vision projektjét.
> * Az alkalmazás segítségével valós időben lehet új képeket kipróbálni, és elküldeni az eredményeket az Azure-ba.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services). 

## <a name="prerequisites"></a>Előfeltételek

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > A projektnek **kompakt** képosztályozási projektnek kell lennie, mivel később ONNX exportáljuk a modellt.
* Emellett [létre kell hoznia egy IoT hub erőforrást](https://ms.portal.azure.com/#create/Microsoft.IotHub) az Azure-ban.
* [Visual Studio 2015 vagy újabb](https://www.visualstudio.com/downloads/)
* Opcionálisan a Windows 10 IoT Core 17763-es vagy újabb verzióját futtató IoT-eszköz. Az alkalmazást közvetlenül a SZÁMÍTÓGÉPRŐL is futtathatja.
   * A 2. és 3. málna esetében beállíthatja a Windows 10 rendszert közvetlenül a IoT irányítópult-alkalmazásból. Más eszközök, például a DrangonBoard esetében az [eMMC-módszer](/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices)használatával kell Flash-t használnia. Ha segítségre van szüksége egy új eszköz beállításához, tekintse [meg az eszköz beállítása](/windows/iot-core/tutorials/quickstarter/devicesetup) a Windows IoT dokumentációjában.

## <a name="about-the-visual-alerts-app"></a>Tudnivalók a vizualizációs riasztások alkalmazásról

A IoT vizuális riasztások alkalmazás folytonos hurokban fut, és szükség szerint négy különböző állapot közötti váltást tesz elérhetővé:

* **Nincs modell**: A No-op állapot. Az alkalmazás folyamatosan alvó állapotba kerül egy másodpercig, és megkeresi a kamerát.
* **Betanítási lemezképek rögzítése**: ebben az állapotban az alkalmazás rögzíti a képet, és a cél Custom Vision projektnek betanítási képként tölti fel. Az alkalmazás ezután alvó állapotba lép a 500 MS-ban, és megismétli a műveletet, amíg meg nem történik a megadott számú rendszerkép rögzítése. Ezután elindítja a Custom Vision modell betanítását.
* **Várakozás a betanított modellre**: ebben az állapotban az alkalmazás meghívja a Custom Vision API-t másodpercenként, hogy meggyőződjön arról, hogy a célként megadott projekt tartalmaz-e betanított iterációt. Ha megtalál egyet, letölti a megfelelő ONNX-modellt egy helyi fájlba, és a **pontozás** állapotra vált.
* **Pontozás**: ebben az állapotban az alkalmazás a Windows ml-t használja a kamera egyetlen keretének kiértékeléséhez a helyi ONNX modellből. Az eredményül kapott képbesorolás megjelenik a képernyőn, és üzenetet küld a IoT Hubnak. Az alkalmazás ezután egy másodpercig alvó állapotba helyezi az új rendszerkép pontozását.

## <a name="examine-the-code-structure"></a>A kód szerkezetének vizsgálata

A következő fájlok kezelik az alkalmazás fő funkcióit.

| Fájl | Leírás |
|-------------|-------------|
| [Főoldal. XAML](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Ez a fájl határozza meg a XAML felhasználói felületét. A webkamera vezérlőelemet futtatja, és tartalmazza az állapot frissítéséhez használt címkéket.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Ez a kód a XAML felhasználói felületének viselkedését szabályozza. Az állapot gép feldolgozási kódját tartalmazza.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Ez az osztály olyan burkoló, amely kezeli a Custom Vision Service integrációját.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Ez az osztály olyan burkoló, amely kezeli a Windows ML-vel való integrációt a ONNX-modell betöltéséhez és a képek pontozásához.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | Ez az osztály olyan burkoló, amely az IoT Hub való integrációt kezeli az Azure-ba való pontozási eredmények feltöltéséhez.|

## <a name="set-up-the-visual-alerts-app"></a>A Visual riasztások alkalmazás beállítása

Kövesse az alábbi lépéseket, hogy lekérje a IoT vizuális riasztások alkalmazást a számítógépen vagy a IoT-eszközön.

1. A [IoTVisualAlerts-minta](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) klónozása vagy letöltése a githubon.
1. Nyissa meg a _IoTVisualAlerts. SLN_ megoldást a Visual Studióban
1. Custom Vision projekt integrálása:
    1. A _CustomVision\CustomVisionServiceWrapper.cs_ parancsfájlban frissítse a `ApiKey` változót a betanítási kulccsal.
    1. Ezután frissítse a `Endpoint` változót a kulcshoz társított végpont URL-címmel.
    1. Frissítse a `targetCVSProjectGuid` változót a használni kívánt Custom Vision-projekt megfelelő azonosítójával. 
1. A IoT Hub erőforrás beállítása:
    1. A _IoTHub\IotHubWrapper.cs_ -parancsfájlban frissítse a `s_connectionString` változót az eszközéhez tartozó megfelelő kapcsolattípus-karakterláncra. 
    1. A Azure Portal töltse be IoT Hub példányát, kattintson a **IoT** alatt található eszközök **elemre**, válassza ki a kívánt eszközt (vagy hozzon létre egyet, ha szükséges), és keresse meg a kapcsolatok karakterláncát az **elsődleges kapcsolatok karakterlánca** alatt. A karakterlánc a IoT Hub nevét, az eszköz AZONOSÍTÓját és a közös elérési kulcsot fogja tartalmazni; a formátuma a következő: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}` .

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ha az alkalmazást a számítógépen futtatja, válassza a **helyi gép** lehetőséget a cél eszközhöz a Visual Studióban, majd válassza az **x64** vagy az **x86** lehetőséget a cél platformhoz. Ezután nyomja le az F5 billentyűt a program futtatásához. Az alkalmazásnak el kell indulnia, és meg kell jelennie az élő hírcsatornának a kamerából és az állapotjelző üzenetből.

Ha ARM-processzorral üzemelő IoT-eszközre végez üzembe helyezést, válassza ki a **ARM** -t célként szolgáló platformként és a **távoli gépen** a célként megadott eszközként. Ha a rendszer kéri, adja meg az eszköz IP-címét (a SZÁMÍTÓGÉPének azonos hálózaton kell lennie). Az IP-címet a Windows IoT alapértelmezett alkalmazásból szerezheti be az eszköz indításakor és a hálózathoz való csatlakoztatása után. Nyomja le az F5 billentyűt a program futtatásához.

Amikor első alkalommal futtatja az alkalmazást, az nem ismeri a vizualizációs állapotokat. Ekkor megjelenik egy olyan állapotjelző üzenet, amely nem érhető el modell. 

## <a name="capture-training-images"></a>Betanítási lemezképek rögzítése

Modell beállításához az alkalmazást be kell állítania a **betanítási képek** állapotba. Hajtsa végre az alábbi lépések egyikét:
* Ha számítógépen futtatja az alkalmazást, használja a felhasználói felület jobb felső sarkában található gombot.
* Ha az alkalmazást egy IoT-eszközön futtatja, hívja `EnterLearningMode` meg a metódust az eszközön a IoT hubon keresztül. A Azure Portal IoT Hub menüjében, illetve a [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp)eszközzel meghívhatja azt az eszközön.
 
Amikor az alkalmazás belép a **betanítási képek rögzítése** állapotba, a másodpercenként két képet fog rögzíteni, amíg el nem éri a megcélzott lemezképek számát. Alapértelmezés szerint a cél 30 kép, de ezt a paramétert úgy állíthatja be, hogy a kívánt számot a IoT Hub metódus argumentumaként adja meg `EnterLearningMode` . 

Az alkalmazás lemezképek rögzítése közben ki kell tenni a kamerát a felderíteni kívánt vizualizációs állapotok típusaira (például egy üres helyiségre, egy személyt tartalmazó helyiségre, egy üres asztalra, egy játék teherautóval rendelkező asztalra stb.).

## <a name="train-the-custom-vision-model"></a>A Custom Vision modell betanítása

Ha az alkalmazás befejezte a lemezképek rögzítését, feltölti őket, majd átvált a **betanított modell** állapotára. Ezen a ponton a [Custom Vision webhelyre](https://www.customvision.ai/) kell lépnie, és létre kell hoznia egy modellt az új betanítási lemezképek alapján. Az alábbi animáció a folyamat példáját mutatja be.

![Animáció: a banán több rendszerképének címkézése](./media/iot-visual-alerts-tutorial/labeling.gif)

A folyamat megismétlése saját forgatókönyv esetén:

1. Jelentkezzen be a [Custom Vision webhelyére](http://customvision.ai).
1. Keresse meg a célként megadott projektet, amely most már rendelkezik az alkalmazás által feltöltött összes betanítási képpel.
1. Minden olyan vizualizációs állapothoz, amelyet azonosítani szeretne, válassza ki a megfelelő lemezképeket, és manuálisan alkalmazza a címkét.
    * Ha például a cél az, hogy különbséget tegyen egy üres helyiség és egy olyan hely között, amelyben a személyek szerepelnek, javasoljuk, hogy öt vagy több képet válasszon ki az emberekkel új osztályként, **emberekként**, és öt vagy több, a **negatív** címkével nem rendelkező képet. Ez segít a modellnek a két állapot közötti különbségtételben.
    * Másik példaként, ha a cél az, hogy megközelítje a polc teljes értékét, olyan címkéket használhat, mint például a **EmptyShelf**, a **PartiallyFullShelf** és a **FullShelf**.
1. Ha elkészült, kattintson a **vonat** gombra.
1. A betanítás befejezése után az alkalmazás megállapítja, hogy elérhető-e egy betanított iteráció. A program elindítja a betanított modell exportálásának folyamatát, hogy ONNX és letöltse azt az eszközre.

## <a name="use-the-trained-model"></a>A betanított modell használata

Miután az alkalmazás letölti a betanított modellt, a rendszer átvált a **pontozási** állapotba, és a kamerából folytonos hurokban indítja el a képek pontozását.

Az alkalmazás minden rögzített képhez megjeleníti a képernyő felső címkéjét. Ha nem ismeri fel a vizualizáció állapotát, a rendszer nem jeleníti meg a **találatokat**. Az alkalmazás emellett elküldi ezeket az üzeneteket a IoT Hubnak, és ha a rendszer egy osztályt észlel, az üzenet tartalmazni fogja a címkét, a megbízhatósági pontot és egy nevű tulajdonságot `detectedClassAlert` , IoT hub amelyet a tulajdonságok alapján a gyors üzenetküldést végző ügyfelek is használhatnak.

Emellett a minta egy [Sense hat-függvénytárat](https://github.com/emmellsoft/RPi.SenseHat) használ annak észlelésére, hogy mikor fut egy olyan, a málna PI-ban, amely Sense hat egységet használ, így kimenetként jelenítheti meg, ha az összes megjelenítõ lámpát vörösre állítja, amikor az egy osztályt észlel, és üresen hagyja, ha nem észlel semmit.

## <a name="reuse-the-app"></a>Az alkalmazás újrafelhasználása

Ha vissza szeretné állítani az alkalmazást az eredeti állapotába, kattintson a felhasználói felület jobb felső sarkában lévő gombra, vagy a metódus meghívásával a `DeleteCurrentModel` IoT hub.

Bármikor megismételheti a betanítási lemezképek feltöltésének lépését a jobb felső szintű felhasználói felület gombra kattintva, vagy meghívja a `EnterLearningMode` metódust.

Ha az alkalmazást egy eszközön futtatja, és újra le kell kérnie az IP-címet (például távoli kapcsolat létesítéséhez a [Windows IoT távoli ügyfélen](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)keresztül), akkor a metódust IoT hub használatával hívhatja meg `GetIpAddress` .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a Custom Vision projektet, ha már nem szeretné karbantartani. A [Custom Vision webhelyén](https://customvision.ai)navigáljon a **projektekhez** , és válassza ki a szemetet az új projekt alatt.

![Képernyőfelvétel az új projekttel ellátott panelről a Kuka ikon használatával](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy olyan alkalmazást állít be és futtatott, amely észleli a vizualizációs állapotinformációkat egy IoT-eszközön, és elküldi az eredményeket a IoT Hubnak. Ezután vizsgálja meg a forráskódot, vagy hajtsa végre az alábbi, javasolt módosításokat.

> [!div class="nextstepaction"]
> [IoTVisualAlerts minta (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* IoT Hub metódus hozzáadásával az alkalmazást közvetlenül a **betanított modell** állapotára való várakozásra válthat. Így a modellt betaníthatja az eszköz által nem rögzített rendszerképekkel, majd leküldheti az új modellt az eszközre a parancsban.
* Kövesse a [valós idejű érzékelők megjelenítése](../../iot-hub/iot-hub-live-data-visualization-in-power-bi.md) oktatóanyagot, és hozzon létre egy Power bi irányítópultot a minta által elküldhető IoT hub-riasztások megjelenítéséhez.
* A [IoT távoli figyelési](../../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md) oktatóanyagot követve hozzon létre egy logikai alkalmazást, amely válaszol a IoT hub riasztásokra a vizualizációs állapotok észlelésekor.