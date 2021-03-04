---
title: Szolgáltatási információk megjelenítése az Android Maps szolgáltatásban | Microsoft Azure térképek
description: Megtudhatja, hogyan jelenítheti meg az információkat, amikor a felhasználók a Térkép funkcióit használják. A Azure Maps Android SDK segítségével megjelenítheti a Toast-üzeneteket és más típusú üzeneteket.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b9926d5d6a70d959c0baacd9602341bb69abe924
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097244"
---
# <a name="display-feature-information"></a>Funkcióinformációk megjelenítése

A térbeli adataikat gyakran pontok, vonalak és sokszögek használatával jelölik. Ezek az adatok gyakran metaadatokkal kapcsolatos adatokkal rendelkeznek. Előfordulhat például, hogy egy pont egy étterem és a metaadatok helyét jelöli az adott étteremben, a neve, a címe és a kiszolgált élelmiszer típusa. Ez a metaadatok hozzáadhatók a GeoJSON tulajdonságaihoz `Feature` . A következő kód egy egyszerű pont funkciót hoz létre egy olyan `title` tulajdonsággal, amelynek értéke "Hello World!".

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Az [adatforrások létrehozásához](create-data-source-android-sdk.md) és a térképhez való hozzáadásához tekintse meg az adatforrás létrehozása című dokumentációt.

Ha a felhasználó a térképen található szolgáltatással kommunikál, az események felhasználhatók az adott műveletekre való reagálásra. Gyakori forgatókönyv egy olyan szolgáltatás metaadat-tulajdonságaiból álló üzenet megjelenítése, amellyel a felhasználó az általa kommunikált. Az `OnFeatureClick` esemény a fő esemény, amellyel észlelhető, ha a felhasználó kihasznál egy funkciót a térképen. Van egy esemény is `OnLongFeatureClick` . Az `OnFeatureClick` eseménynek a térképhez való hozzáadásakor a réteg azonosítójának átadásával egyetlen rétegre korlátozható. Ha a rendszer nem adja át a réteg AZONOSÍTÓját, koppintson a Térkép bármelyik szolgáltatására, függetlenül attól, hogy melyik rétegben található, az eseményt. A következő kód létrehoz egy szimbólum réteget a térkép megjelenítéséhez, majd feltesz egy eseményt, `OnFeatureClick` és korlátozza azt erre a szimbólum rétegre.

::: zone pivot="programming-language-java-android"

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Toast-üzenet megjelenítése

A Toast-üzenet az egyik legegyszerűbb módja a felhasználó információinak megjelenítésének, és az Android minden verziójában elérhető. Semmilyen típusú felhasználói bevitelt nem támogat, és csak rövid ideig jelenik meg. Ha azt szeretné, hogy a felhasználó gyorsan tudja, mit találtak a szolgáltatásban, a Toast üzenet jó választás lehet. Az alábbi kód azt mutatja be, hogyan használható a Toast-üzenet az `OnFeatureClick` eseménnyel.

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![A kihasznált szolgáltatás és a megjelenő Toast-üzenet animációja](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

A pirítós üzenetein kívül számos más módon is bemutathatja egy szolgáltatás metaadat-tulajdonságait, például:

- [Snackbar widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` egyszerű visszajelzést ad a műveletről. Egy rövid üzenetet jelenítenek meg a képernyő alján a mobilon és a bal alsó sarokban a nagyobb eszközökön. `Snackbars` a képernyő minden más eleme felett jelenik meg, és egyszerre csak egy látható.
- [Párbeszédpanelek](https://developer.android.com/guide/topics/ui/dialogs) – a párbeszédpanel egy kis ablak, amely arra kéri a felhasználót, hogy hozzon döntést, vagy adjon meg további információkat. Egy párbeszédpanel nem tölti ki a képernyőt, és általában olyan modális eseményekhez használatos, amelyek megkövetelik, hogy a felhasználók a folytatás előtt végrehajtsák a műveletet.
- Adjon hozzá egy [töredéket](https://developer.android.com/guide/components/fragments) az aktuális tevékenységhez.
- Navigáljon egy másik tevékenységhez vagy nézethez.

## <a name="display-a-popup"></a>Felugró ablak megjelenítése

A Azure Maps Android SDK olyan osztályt biztosít, amely megkönnyíti a `Popup` térképen való elhelyezésre rögzített felhasználói felületi megjegyzések létrehozását. A felugró ablakokhoz relatív elrendezéssel rendelkező nézetben kell átadni a `content` felugró ablak beállítását. Íme egy egyszerű elrendezési példa, amely a háttérben a sötét szöveget jeleníti meg.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Ha a fenti elrendezést egy `popup_text.xml` alkalmazás mappájában található fájl tárolja `res -> layout` , a következő kód létrehoz egy előugró ablakokat, hozzáadja azt a térképhez. Ha egy szolgáltatásra kattint, a `title` tulajdonság az elrendezés használatával jelenik meg, `popup_text.xml` és az elrendezés alsó középpontja a térképen megadott pozícióra van rögzítve.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the coordinates from the clicked feature and create a position object.
    val c: List<Double> = (f.geometry() as Point?).coordinates()
    val pos = Position(c[0], c[1])

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    )

    //Open the popup.
    popup.open()
})
```

::: zone-end

A következő képernyőfelvételen láthatók azok a felugró ablakok, amelyek akkor jelennek meg, amikor a funkciók rákattintanak, és az adott helyen vannak rögzítve a térképen, ahogy mozog.

![Megjelenik egy előugró ablak animációja, és a Térkép az előugró ablakban rögzített állapotba került](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>Következő lépések

További információ hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Reagálás a leképezési eseményekre](android-map-events.md)

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
