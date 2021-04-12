---
title: 'Oktatóanyag: az érzékelő adataihoz való csatlakozás időjárás-előrejelzési adataival Azure Notebooks (Python) és Microsoft Azure Maps használatával'
description: A Azure Notebooks (Python) használatával Microsoft Azure Maps időjárási szolgáltatásokból származó időjárás-előrejelzési adatokkal való összekapcsolásáról szóló oktatóanyag.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 8ab3458003366416e10588d3f2edb29b51619ecf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257636"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Oktatóanyag: az érzékelő adataihoz való csatlakozás időjárás-előrejelzési adataival Azure Notebooks (Python) használatával

A szélenergia egy alternatív energiaforrás a fosszilis tüzelőanyagok számára a klímaváltozás elleni küzdelem érdekében. Mivel a szél nem felel meg a természetnek, a szélenergia-kezelőknek gépi tanulási (ML) modelleket kell létrehozniuk a szélenergia kapacitásának előrejelzéséhez. Ez az előrejelzés szükséges a villamosenergia-kereslet kielégítéséhez, valamint a hálózat stabilitásának biztosításához. Ebben az oktatóanyagban bemutatjuk, hogyan történik a Azure Maps időjárási előrejelzési információk összevonása a bemutatóval az időjárási olvasmányok esetében. A Azure Maps időjárási szolgáltatások meghívásával az időjárás-előrejelzési adatkérést kéri a rendszer.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * A felhőben [Azure Notebooks](https://notebooks.azure.com) adatfájlokkal dolgozhat.
> * Betöltheti a bemutató adatait a fájlból.
> * Azure Maps REST API-k hívása a Pythonban.
> * Helyadatok leképezése a térképen.
> * Azure Maps [napi előrejelzési](/rest/api/maps/weather/getdailyforecast) időjárási adattal gazdagíthatja a bemutatókat.
> * Előrejelzési adatdiagramok ábrázolása.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez először a következőket kell tennie:

1. Hozzon létre egy Azure Maps fiók-előfizetést a S0 díjszabási szinten a [fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)című témakör utasításait követve.
2. Szerezze be a fiókjához tartozó elsődleges előfizetési kulcsot, kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account)című témakör utasításait.


A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

Az Azure-jegyzetfüzetek megismeréséhez és az első lépésekhez kövesse az [Azure notebook létrehozása](./tutorial-ev-routing.md#create-an-azure-notebooks-project)című témakör utasításait.

> [!Note]
> A projekthez tartozó Jupyter-jegyzetfüzet-fájl az [időjárási térképek Jupyter notebook adattárból](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)tölthető le.

## <a name="load-the-required-modules-and-frameworks"></a>A szükséges modulok és keretrendszerek betöltése

Az összes szükséges modul és keretrendszer betöltéséhez futtassa a következő parancsfájlt:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Időjárási adatimportálás

Ennek az oktatóanyagnak az elvégzése érdekében a négy különböző szélturbinák esetében telepített érzékelőkből származó időjárási adatok beolvasását fogjuk használni. A mintaadatok 30 napos időjárási olvasmányokból állnak. Ezeket az adatokat a rendszer az időjárási adatközpontokból gyűjti össze az egyes turbina-helyek közelében. A bemutatóban szereplő adatolvasások a hőmérséklet, a szélsebesség és a irányában szerepelnek. A bemutató [adatait innen töltheti le.](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data) Az alábbi szkript a bemutatót az Azure jegyzetfüzetbe importálja.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Napi előrejelzési adatgyűjtés kérése

Ebben az esetben napi előrejelzést szeretnénk kérni az egyes érzékelők helyéről. Az alábbi szkript meghívja a Azure Maps időjárási szolgáltatások [napi előrejelzési API](/rest/api/maps/weather/getdailyforecast) -ját. Ez az API az aktuális dátumtól számított 15 napra visszaadja az egyes szélturbinák időjárási előrejelzéseit.

```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps Weather services to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Az alábbi parancsfájl a turbina-helyet a térképen jeleníti meg a Azure Maps [Térkép rendszerkép-szolgáltatásának](/rest/api/maps/render/getmapimage)meghívásával.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Turbina-helyszínek](./media/weather-service-tutorial/location-map.png)


Az előrejelzési adatgyűjtést az állomás azonosítója alapján csoportosítjuk a bemutatóba. Az állomás azonosítója az időjárási adatközponthoz. Ez a csoportosítás fokozza a bemutatót az előrejelzési adattal.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

A következő táblázat az egyik turbina-helyszín összesített előzmény-és előrejelzési adatait jeleníti meg.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Csoportosított adathalmazok](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Előrejelzési adatgyűjtés ábrázolása

Az előre jelzett értékeket azon napokon fogjuk ábrázolni, amelyeknek az előrejelzését elvégezték. Ez a mintaterület lehetővé teszi, hogy a következő 15 nap során megtekintse a szél sebességét és irányának változásait.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Az alábbi diagramok megjelenítik az előrejelzési adatértékeket. A szél sebességének változását lásd a bal oldali diagramon. A szél irányának változásához tekintse meg a megfelelő gráfot. Ezek az adatok az adatok kérelmezésének napjától számított 15 napra vonatkozó előrejelzések.

<center>

![Szél sebességét ábrázoló ](./media/weather-service-tutorial/speed-date-plot.png) ![ szél irányának ábrázolása](./media/weather-service-tutorial/direction-date-plot.png)</center>

Ebben az oktatóanyagban megtanulta, hogyan hívhat meg Azure Maps REST API-kat az időjárási előrejelzési adatok beszerzéséhez. Azt is megtanulta, hogyan jelenítheti meg a gráfokban tárolt adatmegjelenítést.

Ha többet szeretne megtudni arról, hogyan hívhat Azure Maps REST API-kat Azure Notebookson belül, tekintse meg az [EV-útválasztást a Azure Notebooks használatával](./tutorial-ev-routing.md).

Az oktatóanyagban használt Azure Maps API-k megismeréséhez tekintse meg a következőt:

* [Napi előrejelzés](/rest/api/maps/weather/getdailyforecast)
* [Render-Térkép képe](/rest/api/maps/render/getmapimage)

Azure Maps REST API-k teljes listájáért lásd: [Azure Maps REST API](./consumption-model.md)-k.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Notebooksről:

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)