---
title: 'Oktatóanyag: elektromos járművek irányítása Azure Notebooks (Python) használatával Microsoft Azure Maps'
description: Útmutató az elektromos járművek átirányításához Microsoft Azure Maps Routing API-k és Azure Notebooks használatával
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680332"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Oktatóanyag: elektromos járművek átirányítása Azure Notebooks használatával (Python)

Azure Maps a térinformatikai szolgáltatási API-k portfóliója, amelyek natív módon vannak integrálva az Azure-ba. Ezek az API-k lehetővé teszik a fejlesztők, a vállalatok és az ISV-k számára, hogy helymeghatározó alkalmazásokat, IoT, mobilitást, logisztikai és Asset Tracking megoldásokat fejlesszenek. 

A Azure Maps REST API-k olyan nyelvekről hívhatók, mint például a Python és az R a térinformatikai adatok elemzésének és a gépi tanulási forgatókönyvek engedélyezéséhez. Azure Maps az [útválasztási API](/rest/api/maps/route) -k robusztus készletét kínálja, amelyek lehetővé teszik a felhasználók számára az útvonalak kiszámítását több adatpont között. A számítások különböző feltételeken, például járműtípuson vagy elérhető területen alapulnak. 

Ebben az oktatóanyagban egy olyan illesztőprogramot fog segíteni, amelynek az elektromos jármű akkumulátora alacsony. Az illesztőprogramnak meg kell keresnie a lehető legközelebb töltő állomást a jármű helyétől.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Hozzon létre és futtasson egy Jupyter Notebook fájlt [Azure Notebooks](https://notebooks.azure.com) a felhőben.
> * Azure Maps REST API-k hívása a Pythonban.
> * Keressen egy elérhető tartományt az elektromos jármű használati modellje alapján.
> * Keressen az elektromos járművek díjszabási állomásait a rendelkezésre álló tartományon belül, vagy isochrone.
> * A elérhető tartomány határának és a díjszabási állomások leképezése
> * Megkeresheti és megjelenítheti az útvonalat a legközelebbi elektromos jármű töltőállomásokon a meghajtó ideje alapján.


## <a name="prerequisites"></a>Előfeltételek 

Az oktatóanyag elvégzéséhez először létre kell hoznia egy Azure Maps fiókot, és le kell kérnie az elsődleges kulcsot (előfizetési kulcs). 

Azure Maps fiók előfizetésének létrehozásához kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)című témakör utasításait. Az S1 árszintje Azure Maps fiók-előfizetésre van szüksége. 

A fiók elsődleges előfizetési kulcsának beszerzéséhez kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account)című témakör utasításait.

A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Azure Notebooks projekt létrehozása

Ennek az oktatóanyagnak a követéséhez létre kell hoznia egy Azure Notebooks projektet, és le kell töltenie és futtatnia kell a Jupyter Notebook fájlt. A Jupyter Notebook fájl Python-kódot tartalmaz, amely megvalósítja a forgatókönyvet ebben az oktatóanyagban. Azure Notebooks projekt létrehozásához és a Jupyter Notebook dokumentum feltöltéséhez tegye a következő lépéseket:

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. További információ: gyors útmutató [: bejelentkezés és felhasználói azonosító beállítása](https://notebooks.azure.com).
1. A nyilvános profil lap tetején válassza a **saját projektek** lehetőséget.

    ![A saját projektek gomb](./media/tutorial-ev-routing/myproject.png)

1. A **saját projektek** lapon válassza az **új projekt** lehetőséget.
 
   ![Az új projekt gomb](./media/tutorial-ev-routing/create-project.png)

1. Az **új projekt létrehozása** panelen adja meg a projekt nevét és a projekt azonosítóját.
 
    ![Az új projekt létrehozása panel](./media/tutorial-ev-routing/create-project-window.png)

1. Válassza a **Létrehozás** lehetőséget.

1. A projekt létrehozása után töltse le ezt a [Jupyter notebook dokumentumot](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) a [Azure Maps Jupyter notebook adattárból](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. A **saját projektek** lap projektek listájában válassza ki a projektet, majd válassza a **feltöltés** lehetőséget a Jupyter notebook dokumentum feltöltéséhez. 

    ![feltöltés Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Töltse fel a fájlt a számítógépről, majd válassza a **kész** lehetőséget.

1. Miután a feltöltés sikeresen befejeződött, a fájl megjelenik a projekt oldalon. Kattintson duplán a fájlra Jupyter Notebookként való megnyitásához.

Próbálja meg megérteni a Jupyter Notebook fájlban implementált funkciókat. Futtassa a kódot a Jupyter Notebook fájlban egyszerre egy cellát. A kódot minden cellában futtathatja, ha a Jupyter Notebook alkalmazás tetején található **Futtatás** gombra kattint.

  ![A Futtatás gomb](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Projekt szintű csomagok telepítése

A kód Jupyter Notebook-ben való futtatásához a következő lépések végrehajtásával telepítse a csomagokat a projekt szintjén:

1. Töltse le a [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) fájlt a [Azure Maps Jupyter notebook adattárból](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook), majd töltse fel a projektbe.
1. A projekt irányítópultján válassza a **projekt beállításai** lehetőséget. 
1. A **projekt beállításai** ablaktáblán válassza a **környezet** lapot, majd kattintson a **Hozzáadás** gombra.
1. A **környezet beállítása lépésekben** tegye a következőket:   
    a. Az első legördülő listában válassza a **Requirements.txt** lehetőséget.  
    b. A második legördülő listában válassza ki a *requirements.txt* fájlt.  
    c. A harmadik legördülő listában válassza ki a **Python 3,6** -es verzióját.
1. Kattintson a **Mentés** gombra.

    ![Csomagok telepítése](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>A szükséges modulok és keretrendszerek betöltése

Az összes szükséges modul és keretrendszer betöltéséhez futtassa az alábbi szkriptet.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Elérhető tartomány határának kérése

A Package Delivery Company rendelkezik néhány elektromos járművel a flottájában. A nap folyamán az elektromos járműveket újra kell számlázni anélkül, hogy vissza kellene térnie a tárházba. Minden alkalommal, amikor a fennmaradó díj egy óránál kevesebb időt vesz igénybe, megkeresi azokat a díjszabási állomásokat, amelyek egy elérhető tartományon belül vannak. A díjszabást alapvetően akkor érdemes megkeresni, ha az akkumulátor töltöttsége alacsony. Emellett a díjszabási állomások ezen tartományához tartozó határokra vonatkozó információkat is megkapja. 

Mivel a vállalat inkább olyan útvonalakat használ, amelyeknek a gazdaságosság és a sebesség egyensúlyát igénylik, a kért routeType *Eco*. A következő parancsfájl meghívja a Azure Maps útválasztási szolgáltatás [lekérési Route Range API](/rest/api/maps/route/getrouterange) -t. Paramétereket használ a jármű felhasználási modelljéhez. A szkript ezután elemzi a választ, hogy létrehoz egy geojson formátumú sokszög objektumot, amely az autó maximálisan elérhető tartományát jelöli.

Az elektromos jármű elérhető tartományának határainak meghatározásához futtassa a szkriptet a következő cellában:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Az elektromos járművek díjszabási állomásának keresése a elérhető tartományon belül

Miután meghatározta az elektromos jármű elérhető tartományát (isochrone), megkeresheti a díjszabási állomásokat az adott tartományon belül. 

A következő szkript meghívja a Azure Maps [utáni keresést a geometriai API](/rest/api/maps/search/postsearchinsidegeometry)-ban. Megkeresi az elektromos járművek díjszabását az autó maximálisan elérhető tartományának határain belül. Ezt követően a parancsfájl a elérhető helyeinek egy tömbje alapján elemzi a választ.

A rendelkezésre álló tartományon belüli elektromos járművek díjszabásának kereséséhez futtassa a következő parancsfájlt:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>A elérhető tartomány és a töltési pontok feltöltése Azure Maps adatszolgáltatásba (előzetes verzió)

A térképen a díjszabási állomásokat és a határt szeretné megjeleníteni az elektromos jármű maximálisan elérhető tartománya számára. Ehhez töltse fel a határokat és az állomások adatait geojson objektumként Azure Maps adatszolgáltatásba (előzetes verzió). Használja az [Adatfeltöltő API](/rest/api/maps/data/uploadpreview)-t. 

A határ és a töltési pont adatok Azure Maps adatszolgáltatásba való feltöltéséhez futtassa a következő két cellát:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>A díjszabási állomások és a rendelkezésre álló tartomány leképezése

Az adatok az adatszolgáltatásba való feltöltése után hívja meg a Azure Maps [Térkép rendszerkép-szolgáltatását](/rest/api/maps/render/getmapimage). Ezzel a szolgáltatással a következő parancsfájl futtatásával jelenítheti meg a kitöltési pontokat és a maximálisan elérhető határokat a statikus térképi képen:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![A hely tartományát ábrázoló Térkép](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Az optimális töltési állomás megkeresése

Először is meg szeretné határozni az összes lehetséges díjszabási állomást a rendelkezésre álló tartományon belül. Ezt követően tudni szeretné, hogy melyeket lehet a lehető legkevesebb idő alatt elérni. 

A következő parancsfájl meghívja a Azure Maps [Matrix Routing API](/rest/api/maps/route/postroutematrix)-t. Visszaadja a megadott jármű helyét, az utazási időt, valamint az egyes díjszabási állomások távolságát. A következő cellában lévő parancsfájl elemzi a választ, hogy megkeresse a legközelebbi elérhető díjszabási állomást az idő tekintetében.

A lehető legkevesebb idő alatt elérhető, legközelebb álló töltőállomás megtalálásához futtassa a szkriptet a következő cellában:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Az útvonal kiszámítása a legközelebbi töltési állomásra

Most, hogy megtalálta a legközelebbi töltési állomást, meghívhatja a [Get Route Directions API](/rest/api/maps/route/getroutedirections) -t, hogy a részletes útvonalat kérje az elektromos jármű aktuális helyéről a díjszabási állomásra.

A következő cellában futtassa a parancsfájlt a kitöltési állomáshoz tartozó útvonal beszerzéséhez, valamint az útvonalat jelképező geojson-objektum létrehozási válaszának elemzéséhez:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Az útvonal megjelenítése

Az útvonal megjelenítéséhez először töltse fel az útválasztási adatok geojson objektumként Azure Maps adatszolgáltatásba (előzetes verzió). Ehhez használja a Azure Maps [Adatfeltöltő API](/rest/api/maps/data/uploadpreview)-t. Ezt követően hívja meg a renderelési szolgáltatást, töltse le a [Térkép képapi](/rest/api/maps/render/getmapimage)-ját, hogy megjelenítse az útvonalat a térképen, és jelenítse meg.

A térképen megjelenített útvonalhoz tartozó rendszerkép beszerzéséhez futtassa a következő parancsfájlt:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Az útvonalat ábrázoló Térkép](./media/tutorial-ev-routing/route.png)

Ebből az oktatóanyagból megtudhatta, hogyan hívhatja közvetlenül a Azure Maps REST API-kat, és hogyan jelenítheti meg Azure Maps-adatok a Python használatával.

Az oktatóanyagban használt Azure Maps API-k megismeréséhez tekintse meg a következőt:

* [Útvonal tartományának beolvasása](/rest/api/maps/route/getrouterange)
* [Keresés közzététele a geometrián belül](/rest/api/maps/search/postsearchinsidegeometry)
* [Adatok feltöltése](/rest/api/maps/data/uploadpreview)
* [Render-Térkép képe](/rest/api/maps/render/getmapimage)
* [Útvonal-mátrix küldése](/rest/api/maps/route/postroutematrix)
* [Útvonal irányának beolvasása](/rest/api/maps/route/getroutedirections)
* [Azure Maps REST API-k](./consumption-model.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Notebooksről:

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)