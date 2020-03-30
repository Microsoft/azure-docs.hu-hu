---
title: Forgalmi adatok megjelenítése android térképen | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a forgalmi adatokat a térképen a Microsoft Azure Maps Android SDK használatával.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335379"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Forgalmi adatok megjelenítése a térképen az Azure Maps Android SDK használatával

A folyamatadatok és az incidensek adatai a térképen megjeleníthető forgalmi adatok két típusa. Ez az útmutató bemutatja, hogyan jelenítheti meg mindkét típusú forgalmi adatokat. Az incidensadatok pont- és vonalalapú adatokat tartalmaznak olyan dolgokra vonatkozóan, mint az építmények, útlezárások és balesetek. A folyamatadatok az útforgalmának forgalmával kapcsolatos mutatókat jelenítik meg.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megjelenítheti a forgalmat a térképen, [azure-fiókot kell készítenie,](quick-demo-map-app.md#create-an-account-with-azure-maps)és be kell [szereznie egy előfizetési kulcsot.](quick-demo-map-app.md#get-the-primary-key-for-your-account) Ezután telepítenie kell az [Azure Maps Android SDK-t,](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) és be kell töltenie egy térképet.

## <a name="incidents-traffic-data"></a>Incidensek forgalmi adatai 

A híváshoz `setTraffic` a következő tárakat `incidents`kell importálnia:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 A következő kódrészlet bemutatja, hogyan jelenítheti meg a forgalmi adatokat a térképen. Átadunk egy logikai értéket a `incidents` metódusnak, és átadjuk a `setTraffic` metódusnak. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Folyamatforgalmi adatok

Először a következő tárakat kell `setTraffic` `flow`importálnia a híváshoz, és:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

A forgalmi adatok beállításához használja a következő kódrészletet. Az előző szakaszban található kódhoz hasonlóan a `flow` metódus visszatérési értékét adjuk át a `setTraffic` metódusnak. Négy érték adható át a `flow`lehetőségnek, és `flow` minden egyes érték a megfelelő értéket adja vissza. A visszatérési `flow` érték ezután argumentumként kerül átadásra a számára. `setTraffic` Az alábbi táblázatban az alábbi négy értéket talál:

| | |
| :-- | :-- |
| TrafficFlow.NONE | Nem jeleníti meg a forgalmi adatokat a térképen |
| TrafficFlow.RELATIVE | Az út szabad áramlási sebességéhez viszonyított forgalmi adatok megjelenítése |
| TrafficFlow.RELATIVE_DELAY | Az átlagos várt késleltetésnél lassabb területeket jeleníti meg |
| TrafficFlow.ABSOLUTE | Megmutatja az összes jármű abszolút sebességét az utakon |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Incidensforgalom-adatok megjelenítése egy funkcióra kattintva

Az incidensek beszerzéséhez egy adott funkció, használhatja az alábbi kódot. Amikor egy szolgáltatásra kattintanak, a kódlogika ellenőrzi az incidenseket, és üzenetet készít az incidensről. Megjelenik egy üzenet a képernyő alján a részletekkel.

1. Először is meg kell szerkeszteni **e res > elrendezést > activity_main.xml**, hogy úgy néz ki, mint az alábbi. A , `mapcontrol_centerLat` `mapcontrol_centerLng`és `mapcontrol_zoom` a kívánt értékeket is lecserélheti. Visszahívás, a nagyítási szint 0 és 22 közötti érték. A 0-s nagyítási szinten az egész világ elfér egyetlen csempén.

   ```XML
   <?xml version="1.0" encoding="utf-8"?>
   <FrameLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       >
    
       <com.microsoft.azure.maps.mapcontrol.MapControl
           android:id="@+id/mapcontrol"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Adja hozzá a következő kódot a **MainActivity.java** fájlhoz. A csomag alapértelmezés szerint benne van, ezért ügyeljen arra, hogy a csomag a tetején maradjon.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Miután beépítette a fenti kódot az alkalmazásba, rákattinthat egy funkcióra, és megtekintheti a forgalmi események részleteit. A **activity_main.xml** fájlban használt szélességi, hosszúsági és nagyítási szintértékektől függően az alábbi hoz hasonló eredmények jelennek meg:

   <center>

   ![Incidens-forgalom-on-the-térkép](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi útmutatókat, amelyekből megtudhatja, hogyan adhat hozzá további adatokat a térképhez:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása androidos térképhez](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Funkcióinformációk megjelenítése](display-feature-information-android.md)
