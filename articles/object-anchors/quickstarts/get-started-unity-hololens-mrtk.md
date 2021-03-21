---
title: 'Gyors útmutató: HoloLens-alkalmazás létrehozása Unity és MRTK'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy HoloLens Unity-alkalmazást a MRTK és az Object-horgonyok használatával.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049710"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Gyors útmutató: HoloLens-alkalmazás létrehozása Azure Object-Horgonyokkal, egységben a MRTK

Ebben a rövid útmutatóban létrehoz egy Unity HoloLens-alkalmazást, amely az [Azure Object-horgonyokat](../overview.md)használja. Az Azure Object-horgonyok egy felügyelt felhőalapú szolgáltatás, amely a 3D-eszközöket olyan AI-modellekre alakítja át, amelyek lehetővé teszik az objektum-Aware vegyes valóságot a HoloLens. Ha elkészült, egy olyan egységgel rendelkező HoloLens-alkalmazás fog rendelkezni, amely képes a fizikai világ objektumainak észlelésére.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Készítse elő az egység létrehozási beállításait.
> * Exportálja a HoloLens Visual Studio-projektet.
> * Telepítse az alkalmazást, és futtassa egy HoloLens 2 eszközön.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Az egység területen nyissa meg a `quickstarts/apps/unity/mrtk` projektet.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Ha a "TMP-importáló" párbeszédpanel felszólítja a TextMesh Pro-erőforrások importálására, válassza a "TMP alapismeretek importálása" lehetőséget.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="TextMesh Pro-erőforrások importálása":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

Kapcsolja be az eszközt, válassza a **minden alkalmazás** lehetőséget, majd keresse meg és indítsa el az alkalmazást. Az Unity splash képernyő után egy fehér határvonalat kell látnia. A határolókeret mozgatásához, méretezéséhez vagy elforgatásához használhatja a kezét. Helyezze a mezőt a felderíteni kívánt objektumra.

Nyissa meg a <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">kéz menüt</a> , és válassza a **zárolás SearchArea** lehetőséget a határolókeret további mozgatásának megakadályozásához. Válassza a **Keresés indítása** lehetőséget az objektum észlelésének megkezdéséhez. Az objektum észlelésekor a rendszer egy rácsvonalat jelenít meg az objektumon. Az észlelt példányok részletei megjelennek a képernyőn, például a frissített időbélyeg és a felületi lefedettség aránya. Válassza a **Keresés leállítása** lehetőséget a nyomkövetés leállításához, és az összes észlelt példány el lesz távolítva.

#### <a name="the-app-menus"></a>Az alkalmazás menüi

Más műveleteket is végrehajthat a <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">kéz menü</a>használatával.

##### <a name="primary-menu"></a>Elsődleges menü

* **Keresés indítása/keresés leállítása** – elindítja vagy leállítja az objektum észlelési folyamatát.
* **Térbeli leképezés váltása** – térbeli leképezések megjelenítésének vagy elrejtésének megjelenítése. Ezzel a beállítással lehet hibakeresést végezni, ha a vizsgálat befejeződött.
* **Nyomkövetési beállítások** – a nyomkövetési beállítások menü aktiválásának bekapcsolása.
* **Keresési terület beállításai** – átváltja a keresési terület beállításai menü aktiválását.
* **Nyomkövetés indítása** – diagnosztikai eszközök rögzítése és mentése az eszközre. További részleteket a **hibakeresési észlelési problémák és a diagnosztika rögzítése** című szakaszban talál.
* **Nyomkövetés feltöltése** – diagnosztikai adatok feltöltése az objektum-horgonyok szolgáltatásba. A felhasználónak meg kell adnia az előfizetési fiókját, `subscription.json` és fel kell töltenie a `LocalState` mappába. Az alábbiakban egy minta `subscription.json` fájl található.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Unity elsődleges oldali menü":::

##### <a name="tracker-settings-menu"></a>Követési beállítások menü

* **Nagy pontosság** – egy kísérleti funkció, amellyel pontosabban jelentheti a problémát. Ha engedélyezi ezt a beállítást, további rendszererőforrásokra lesz szükség az objektumok észlelése során. Ebben a módban az objektum rácsvonala rózsaszínkor jelenik meg. Válassza ismét ezt a gombot, ha vissza szeretne váltani normál nyomkövetési módba.
* **Nyugodt függőleges igazítás** – ha engedélyezve van, lehetővé teszi, hogy az objektum nem függőleges szögben legyen észlelve. Hasznos a rámpán lévő objektumok észleléséhez.
* **Méretezési módosítás engedélyezése** – lehetővé teszi, hogy a Szemléző a környezeti információk alapján megváltoztassa az észlelt objektum méretét.
* **Lefedettségi arány csúszka** – a nyomon követheti a képpontok azon részét, amelyeknek meg kell egyezniük a nyomkövetéssel egy objektum észlelése érdekében.  Az alacsonyabb értékek lehetővé teszik a Szemléző számára, hogy jobban azonosítsa azokat az objektumokat, amelyek nagy kihívást jelentenek a HoloLens érzékelők észleléséhez, például sötét vagy nagy teljesítményű, tükröző objektumokhoz. A magasabb értékek csökkentik a hamis észlelések gyakoriságát.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Unity Tracker Hand menü":::

##### <a name="search-area-settings-menu"></a>Keresési területi beállítások menü

* **Zárolási hely zárolása** – a zárolási területek határoló mezője megakadályozza a véletlen áthelyezést.
* **Keresési területek automatikus igazítása** – lehetővé teszi, hogy a keresési területek újra megpozícionálják magát az objektumok észlelése során.
* **Ciklus rácsvonala** – a betöltött rácsvonalak vizualizáción keresztüli megjelenítését a keresési területeken.  Ez a beállítás segítheti a felhasználóknak a keresőmező igazítását a nehezen azonosítható objektumok észleléséhez.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Unity-keresési munkaterület legördülő menüje":::

Példa `subscription.json` :

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Fogalmak: az SDK áttekintése](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Gyakori kérdések](../faq.md)
