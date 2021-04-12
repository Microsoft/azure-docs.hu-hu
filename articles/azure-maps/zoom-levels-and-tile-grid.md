---
title: Nagyítási szintek és csempék rácsa Microsoft Azure térképeken
description: Megtudhatja, hogyan állíthatja be a nagyítási szinteket a Azure Mapsban. Megtudhatja, hogyan alakíthatja át a földrajzi koordinátákat képpontos koordinátákba, csempe koordinátáiba és quadkeys. Megtekintheti a kódok mintáit.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 21c2329ec58e414ebfedaa4c49d5f690f47cac72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913891"
---
# <a name="zoom-levels-and-tile-grid"></a>Nagyítási szintek és csemperács

Azure Maps használja a gömb Mercator vetítési koordináta-rendszerrel (EPSG: 3857). A kivetítés az a matematikai modell, amellyel a gömb alakú földgömb átalakítható egy egyszerű térképre. A gömb Mercator-vetítése a pólusok térképét kiterjesztve egy szögletes térképet hoz létre. Ez a kivetítés jelentősen torzítja a Térkép méretét és területét, de két fontos tulajdonsága van, amely meghaladja a torzítást:

- Ez egy megfelelőségi vetület, ami azt jelenti, hogy megőrzi a viszonylag kis objektumok alakját. A kis objektumok alakjának megőrzése különösen fontos a légi felvételek megjelenítésekor. Tegyük fel például, hogy el szeretné kerülni az épületek alakjának torzítását. A négyzet alakú épületeknek szögletesnek kell lenniük, nem derékszögben.
- Ez egy hengeres kivetítés. Az Észak-és Dél-a mindig felfelé és lefelé, a nyugati és a keleti régió pedig mindig balra és jobbra van. 

A Térkép lekérésének és megjelenítésének teljesítményének optimalizálása érdekében a Térkép szögletes csempére van osztva. A Azure Maps SDK olyan csempét használ, amelynek mérete 512 x 512 képpont, valamint kisebb 256 x 256 képpont a műholdas képekhez. A Azure Maps raszteres és vektoros csempéket biztosít 23 nagyítási szinthez, 0 és 22 közötti számmal. A 0. nagyítási szinten a teljes világ egyetlen csempére illeszkedik:

:::image type="content" source="./media/zoom-levels-and-tile-grid/world0.png" alt-text="Globális Térkép csempe":::

A nagyítási szint 1 négy csempét használ a világ megjelenítéséhez: 2 x 2 négyzet

:::image type="content" source="./media/zoom-levels-and-tile-grid/map-2x2-tile-layout.png" alt-text="2x2-es Térkép csempe elrendezése":::

Minden további nagyítási szint quad – elosztja az előző csempéket, és létrehoz egy 2.<sup>zoom</sup> x 2<sup>nagyítású</sup>rácsot. A 22-es nagyítási szint a 2<sup>22</sup> x 2<sup>22</sup>vagy 4 194 304 x 4 194 304 csempe (17 592 186 044 416 csempék összesen).

Azure Maps a web és Android rendszerhez készült interaktív térkép-vezérlőelemek 25 nagyítási szintet (0 – 24) támogatnak. Bár a közúti adatmennyiség csak a csempék elérhető nagyítási szintjein lesz elérhető.

Az alábbi táblázat a nagyítási szintek azon értékeinek teljes listáját tartalmazza, amelyekben a csempe mérete 512 képpont, a (z) 0. szélességnél:

|Nagyítási szint|Mérőműszer/képpont|Méter/csempe oldal|
|--- |--- |--- |
| 0 | 156543 | 40075017 |
| 1 | 78271,5 | 20037508 |
| 2 | 39135,8 | 10018754 |
| 3 | 19567,88 | 5009377,1 |
| 4 | 9783,94 | 2504688,5 |
| 5 | 4891,97 | 1252344,3 |
| 6 | 2445,98 | 626172,1 |
| 7 | 1222,99 | 313086,1 |
| 8 | 611,5 | 156543 |
| 9 | 305,75 | 78271,5 |
| 10 | 152,87 | 39135,8 |
| 11 | 76,44 | 19567,9 |
| 12 | 38,219 | 9783,94 |
| 13 | 19,109 | 4891,97 |
| 14 | 9,555 | 2445,98 |
| 15 | 4,777 | 1222,99 |
| 16 | 2,3887 | 611,496 |
| 17 | 1,1943 | 305,748 |
| 18 | 0,5972 | 152,874 |
| 19 | 0,14929 | 76,437 |
| 20 | 0,14929 | 38,2185 |
| 21 | 0,074646 | 19,10926 |
| 22 | 0,037323 | 9,55463 |
| 23 | 0,0186615 | 4,777315 |
| 24 | 0,00933075 | 2,3886575 |

## <a name="pixel-coordinates"></a>Képpontok koordinátái

Miután kiválasztotta a kivetítést és a méretezést, amelyet minden nagyítási szinten használni szeretne, a földrajzi koordinátákat képpontos koordinátákba alakíthatja. Egy adott nagyítási szinthez tartozó Térkép képének teljes képpont szélességét és magasságát a következőképpen számítjuk ki:

```javascript
var mapWidth = tileSize * Math.pow(2, zoom);

var mapHeight = mapWidth;
```

Mivel a Térkép szélessége és magassága eltér a nagyítási szinttől, a képpontok koordinátáit is használják. A térkép bal felső sarkában található képpontnak mindig van képpont-koordinátái (0, 0). A Térkép jobb alsó sarkában található pixel koordinátái *(width-1, height-1)*, vagy az előző szakaszban szereplő egyenletekre utalnak *(tileSize \* 2 <sup>Nagyítás</sup>– 1, tileSize \* 2 <sup>Nagyítás</sup>– 1)*. Például, ha a 2. szinten a 512 négyzet alakú csempéket használja, a képpont koordinátái (0, 0) és (2047, 2047) közé esnek, a következőhöz hasonlóan:

:::image type="content" border="false" source="./media/zoom-levels-and-tile-grid/map-width-height.png" alt-text="Képpont dimenziókat ábrázoló Térkép":::

A szélességi és a hosszúsági fok, valamint a részletesség szintje a képpont XY koordinátáit a következőképpen számítja ki:

```javascript
var sinLatitude = Math.sin(latitude * Math.PI/180);

var pixelX = ((longitude + 180) / 360) * tileSize * Math.pow(2, zoom);

var pixelY = (0.5 – Math.log((1 + sinLatitude) / (1 – sinLatitude)) / (4 * Math.PI)) * tileSize * Math.pow(2, zoom);
```

A szélességi és a hosszúsági értékeket a WGS 84 Datum értékre kell feltételezni. Bár a Azure Maps gömb-kivetítést használ, fontos, hogy az összes földrajzi koordinátákat egy közös adatmennyiségre alakítsa át. A WGS 84 a kiválasztott adat. A hosszúsági értéket a-180 fok és a + 180 fok közé kell állítani, és a szélességi értéket a-85,05112878 és 85,05112878 közötti tartományba kell nyírni. Ezeknek az értékeknek a betartásával elkerülhető egy szingularitás a pólusoknál, és biztosítja, hogy a tervezett Térkép négyzet alakú legyen.

## <a name="tile-coordinates"></a>Csempe koordinátái

A leképezés lekérésének és megjelenítésének teljesítményének optimalizálása érdekében a renderelt Térkép csempére kerül. A képpontok száma és a csempék száma minden nagyítási szinten eltér:

```javascript
var numberOfTilesWide = Math.pow(2, zoom);

var numberOfTilesHigh = numberOfTilesWide;
```

Az egyes csempék a bal felső sarokban (0, 0) kezdve az XY koordinátákat adják meg *(2 <sup>Nagyítás</sup>– 1, 2 <sup>Nagyítás</sup>– 1)* a jobb alsó sarokban. A 3. nagyítási szint esetében például a csempe a következőtől kezdve a (0, 0) és a (7) közötti tartományban van:

:::image type="content" border="false" source="./media/zoom-levels-and-tile-grid/map-tiles-x-y-coordinates-7x7.png" alt-text="Csempe koordinátáinak térképe":::

A képpont XY koordinátáinak pár pontja alapján könnyedén meghatározható az adott képpontot tartalmazó csempe XY koordinátái:

```javascript
var tileX = Math.floor(pixelX / tileSize);

var tileY = Math.floor(pixelY / tileSize);
```

A csempéket a nagyítási szint hívja meg. Az x és y koordináták megfelelnek a rácsnak az adott nagyítási szinthez tartozó pozíciójának.

A használni kívánt nagyítási szint meghatározásakor ügyeljen arra, hogy az egyes helyek rögzített pozícióban legyenek a csempén. Ennek eredményeképpen az adott kiterjedésű terület megjelenítéséhez szükséges csempék száma a globális Térkép nagyítási rácsának adott elhelyezésén múlik. Ha például két pont 900 méter távolságra van, akkor csak három csempével *lehet* megjeleníteni a 17. nagyítási szinten közöttük lévő útvonalat. Ha azonban a nyugati pont a csempe jobb oldalán található, a csempe bal oldalán lévő keleti pont pedig négy csempét tartalmazhat:

:::image type="content" border="false" source="./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png" alt-text="Nagyítási bemutató méretezése":::

A nagyítási szint meghatározása után az x és az y értékeket is kiszámíthatja. Az egyes nagyítási rácsok bal felső csempe x = 0, y = 0; a jobb alsó csempe x = 2<sup>Nagyítás-1</sup>, y = 2<sup>zoom-1</sup>.

Itt látható az 1. nagyítási szint nagyítási rácsa:

:::image type="content" border="false" source="./media/zoom-levels-and-tile-grid/api_x_y.png" alt-text="1. nagyítási szint nagyítási rácsa":::

## <a name="quadkey-indices"></a>Quadkey indexek

Egyes leképezési platformok olyan `quadkey` indexelési elnevezési konvenciót használnak, amely a következő csempe-koordinátákat egyesíti egy `quadtree` kulcsok vagy rövid dimenzióval rendelkező egydimenziós karakterláncban `quadkeys` . Mindegyik `quadkey` egyedi módon azonosít egy csempét egy adott részletességi szinten, és kulcsként használható a "B" általános adatbázis-indexekben. A Azure Maps SDK-k támogatják az elnevezési konvenciót használó csempe-rétegek átfedését az `quadkey` egyéb elnevezési konvenciók mellett, a [csempék hozzáadása rétegbeli](map-add-tile-layer.md) dokumentum dokumentációjában leírtaknak megfelelően.

> [!NOTE]
> Az `quadkeys` elnevezési konvenció csak egy vagy több nagyítási szint esetén működik. A Azure Maps SDK támogatja a 0. méretet, amely a teljes világ egyetlen Térkép csempéje. 

A csempe koordinátáinak a értékre való átalakításához `quadkey` az Y és az X koordinátáinak bitei összekapcsolva lesznek, és az eredmény egy alap-4 számként lesz értelmezve (a vezető nullákkal megőrzött), és egy karakterlánccá konvertálva. Ha például a csempe XY koordinátáit (3, 5) a 3. szinten adja meg, a a `quadkey` következőképpen van meghatározva:

```
tileX = 3 = 011 (base 2)

tileY = 5 = 101 (base 2)

quadkey = 100111 (base 2) = 213 (base 4) = "213"
```

`Qquadkeys` több érdekes tulajdonsága van. Első lépésként a `quadkey` (számjegyek száma) a megfelelő csempe nagyítási szintjét egyenlővé kell tennie. Másodszor, a `quadkey` csempék a `quadkey` szülő csempével kezdődnek (az előző szinten található csempét tartalmazó csempe). Ahogy az alábbi példában is látható, a csempe 2 a 20 és 23 közötti csempe szülőjének:

:::image type="content" border="false" source="./media/zoom-levels-and-tile-grid/quadkey-tile-pyramid.png" alt-text="Quadkey csempe piramis":::

Végül `quadkeys` adjon meg egy egydimenziós index-kulcsot, amely általában megőrzi a csempék közelségét az XY térben. Ez azt jelenti, hogy két, az XY koordinátákat tartalmazó csempének általában viszonylag közel kell lennie `quadkeys` egymáshoz. Ez azért fontos, hogy optimalizálja az adatbázis teljesítményét, mivel a szomszédos csempéket gyakran kérik a csoportokban, és érdemes megtartani ezeket a csempéket ugyanarra a lemezre, hogy a lemezes olvasások számát csökkenteni lehessen.

## <a name="tile-math-source-code"></a>Csempe matematikai forráskódja

Az alábbi mintakód a jelen dokumentumban ismertetett függvények megvalósítását szemlélteti. A függvények igény szerint egyszerűen lefordíthatók más programozási nyelvekre.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Text;

namespace AzureMaps
{
    /// <summary>
    /// Tile System math for the Spherical Mercator projection coordinate system (EPSG:3857)
    /// </summary>
    public static class TileMath
    {
        //Earth radius in meters.
        private const double EarthRadius = 6378137;

        private const double MinLatitude = -85.05112878;
        private const double MaxLatitude = 85.05112878;
        private const double MinLongitude = -180;
        private const double MaxLongitude = 180;

        /// <summary>
        /// Clips a number to the specified minimum and maximum values.
        /// </summary>
        /// <param name="n">The number to clip.</param>
        /// <param name="minValue">Minimum allowable value.</param>
        /// <param name="maxValue">Maximum allowable value.</param>
        /// <returns>The clipped value.</returns>
        private static double Clip(double n, double minValue, double maxValue)
        {
            return Math.Min(Math.Max(n, minValue), maxValue);
        }

        /// <summary>
        /// Calculates width and height of the map in pixels at a specific zoom level from -180 degrees to 180 degrees.
        /// </summary>
        /// <param name="zoom">Zoom Level to calculate width at</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>Width and height of the map in pixels</returns>
        public static double MapSize(double zoom, int tileSize)
        {
            return Math.Ceiling(tileSize * Math.Pow(2, zoom));
        }

        /// <summary>
        /// Calculates the Ground resolution at a specific degree of latitude in meters per pixel.
        /// </summary>
        /// <param name="latitude">Degree of latitude to calculate resolution at</param>
        /// <param name="zoom">Zoom level to calculate resolution at</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>Ground resolution in meters per pixels</returns>
        public static double GroundResolution(double latitude, double zoom, int tileSize)
        {
            latitude = Clip(latitude, MinLatitude, MaxLatitude);
            return Math.Cos(latitude * Math.PI / 180) * 2 * Math.PI * EarthRadius / MapSize(zoom, tileSize);
        }

        /// <summary>
        /// Determines the map scale at a specified latitude, level of detail, and screen resolution.
        /// </summary>
        /// <param name="latitude">Latitude (in degrees) at which to measure the map scale.</param>
        /// <param name="zoom">Level of detail, from 1 (lowest detail) to 23 (highest detail).</param>
        /// <param name="screenDpi">Resolution of the screen, in dots per inch.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>The map scale, expressed as the denominator N of the ratio 1 : N.</returns>
        public static double MapScale(double latitude, double zoom, int screenDpi, int tileSize)
        {
            return GroundResolution(latitude, zoom, tileSize) * screenDpi / 0.0254;
        }

        /// <summary>
        /// Global Converts a Pixel coordinate into a geospatial coordinate at a specified zoom level. 
        /// Global Pixel coordinates are relative to the top left corner of the map (90, -180)
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A position value in the format [longitude, latitude].</returns>
        public static double[] GlobalPixelToPosition(double[] pixel, double zoom, int tileSize)
        {
            var mapSize = MapSize(zoom, tileSize);

            var x = (Clip(pixel[0], 0, mapSize - 1) / mapSize) - 0.5;
            var y = 0.5 - (Clip(pixel[1], 0, mapSize - 1) / mapSize);

            return new double[] {
                360 * x,    //Longitude
                90 - 360 * Math.Atan(Math.Exp(-y * 2 * Math.PI)) / Math.PI  //Latitude
            };
        }

        /// <summary>
        /// Converts a point from latitude/longitude WGS-84 coordinates (in degrees) into pixel XY coordinates at a specified level of detail.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param> 
        /// <returns>A global pixel coordinate.</returns>
        public static double[] PositionToGlobalPixel(double[] position, int zoom, int tileSize)
        {
            var latitude = Clip(position[1], MinLatitude, MaxLatitude);
            var longitude = Clip(position[0], MinLongitude, MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.Sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.Log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            var mapSize = MapSize(zoom, tileSize);

            return new double[] {
                 Clip(x * mapSize + 0.5, 0, mapSize - 1),
                 Clip(y * mapSize + 0.5, 0, mapSize - 1)
            };
        }

        /// <summary>
        /// Converts pixel XY coordinates into tile XY coordinates of the tile containing the specified pixel.
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="tileX">Output parameter receiving the tile X coordinate.</param>
        /// <param name="tileY">Output parameter receiving the tile Y coordinate.</param>
        public static void GlobalPixelToTileXY(double[] pixel, int tileSize, out int tileX, out int tileY)
        {
            tileX = (int)(pixel[0] / tileSize);
            tileY = (int)(pixel[1] / tileSize);
        }

        /// <summary>
        /// Performs a scale transform on a global pixel value from one zoom level to another.
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="oldZoom">The zoom level in which the input global pixel value is from.</param>  
        /// <returns>A scale pixel coordinate.</returns>
        public static double[] ScaleGlobalPixel(double[] pixel, double oldZoom, double newZoom)
        {
            var scale = Math.Pow(2, oldZoom - newZoom);

            return new double[] { pixel[0] * scale, pixel[1] * scale };
        }

        /// <summary>
        /// Performs a scale transform on a set of global pixel values from one zoom level to another.
        /// </summary>
        /// <param name="pixels">A set of global pixel value from the old zoom level. Points are in the format [x,y].</param>
        /// <param name="oldZoom">The zoom level in which the input global pixel values is from.</param>
        /// <param name="newZoom">The new zoom level in which the output global pixel values should be aligned with.</param>
        /// <returns>A set of global pixel values that has been scaled for the new zoom level.</returns>
        public static double[][] ScaleGlobalPixels(double[][] pixels, double oldZoom, double newZoom)
        {
            var scale = Math.Pow(2, oldZoom - newZoom);

            var output = new System.Collections.Generic.List<double[]>();
            foreach (var p in pixels)
            {
                output.Add(new double[] { p[0] * scale, p[1] * scale });
            }

            return output.ToArray();
        }

        /// <summary>
        /// Converts tile XY coordinates into a global pixel XY coordinates of the upper-left pixel of the specified tile.
        /// </summary>
        /// <param name="tileX">Tile X coordinate.</param>
        /// <param name="tileY">Tile Y coordinate.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="pixelX">Output parameter receiving the X coordinate of the point, in pixels.</param>  
        /// <param name="pixelY">Output parameter receiving the Y coordinate of the point, in pixels.</param>  
        public static double[] TileXYToGlobalPixel(int tileX, int tileY, int tileSize)
        {
            return new double[] { tileX * tileSize, tileY * tileSize };
        }

        /// <summary>
        /// Converts tile XY coordinates into a quadkey at a specified level of detail.
        /// </summary>
        /// <param name="tileX">Tile X coordinate.</param>
        /// <param name="tileY">Tile Y coordinate.</param>
        /// <param name="zoom">Zoom level</param>
        /// <returns>A string containing the quadkey.</returns>
        public static string TileXYToQuadKey(int tileX, int tileY, int zoom)
        {
            var quadKey = new StringBuilder();
            for (int i = zoom; i > 0; i--)
            {
                char digit = '0';
                int mask = 1 << (i - 1);
                if ((tileX & mask) != 0)
                {
                    digit++;
                }
                if ((tileY & mask) != 0)
                {
                    digit++;
                    digit++;
                }
                quadKey.Append(digit);
            }
            return quadKey.ToString();
        }

        /// <summary>
        /// Converts a quadkey into tile XY coordinates.
        /// </summary>
        /// <param name="quadKey">Quadkey of the tile.</param>
        /// <param name="tileX">Output parameter receiving the tile X coordinate.</param>
        /// <param name="tileY">Output parameter receiving the tile Y coordinate.</param>
        /// <param name="zoom">Output parameter receiving the zoom level.</param>
        public static void QuadKeyToTileXY(string quadKey, out int tileX, out int tileY, out int zoom)
        {
            tileX = tileY = 0;
            zoom = quadKey.Length;
            for (int i = zoom; i > 0; i--)
            {
                int mask = 1 << (i - 1);
                switch (quadKey[zoom - i])
                {
                    case '0':
                        break;

                    case '1':
                        tileX |= mask;
                        break;

                    case '2':
                        tileY |= mask;
                        break;

                    case '3':
                        tileX |= mask;
                        tileY |= mask;
                        break;

                    default:
                        throw new ArgumentException("Invalid QuadKey digit sequence.");
                }
            }
        }

        /// <summary>
        /// Calculates the XY tile coordinates that a coordinate falls into for a specific zoom level.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="tileX">Output parameter receiving the tile X position.</param>
        /// <param name="tileY">Output parameter receiving the tile Y position.</param>
        public static void PositionToTileXY(double[] position, int zoom, int tileSize, out int tileX, out int tileY)
        {
            var latitude = Clip(position[1], MinLatitude, MaxLatitude);
            var longitude = Clip(position[0], MinLongitude, MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.Sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.Log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            //tileSize needed in calculations as in rare cases the multiplying/rounding/dividing can make the difference of a pixel which can result in a completely different tile. 
            var mapSize = MapSize(zoom, tileSize);
            tileX = (int)Math.Floor(Clip(x * mapSize + 0.5, 0, mapSize - 1) / tileSize);
            tileY = (int)Math.Floor(Clip(y * mapSize + 0.5, 0, mapSize - 1) / tileSize);
        }

        /// <summary>
        /// Calculates the tile quadkey strings that are within a specified viewport.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="width">The width of the map viewport in pixels.</param>
        /// <param name="height">The height of the map viewport in pixels.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A list of quadkey strings that are within the specified viewport.</returns>
        public static string[] GetQuadkeysInView(double[] position, int zoom, int width, int height, int tileSize)
        {
            var p = PositionToGlobalPixel(position, zoom, tileSize);

            var top = p[1] - height * 0.5;
            var left = p[0] - width * 0.5;

            var bottom = p[1] + height * 0.5;
            var right = p[0] + width * 0.5;

            var tl = GlobalPixelToPosition(new double[] { left, top }, zoom, tileSize);
            var br = GlobalPixelToPosition(new double[] { right, bottom }, zoom, tileSize);

            //Boudning box in the format: [west, south, east, north];
            var bounds = new double[] { tl[0], br[1], br[0], tl[1] };

            return GetQuadkeysInBoundingBox(bounds, zoom, tileSize);
        }

        /// <summary>
        /// Calculates the tile quadkey strings that are within a bounding box at a specific zoom level.
        /// </summary>
        /// <param name="bounds">A bounding box defined as an array of numbers in the format of [west, south, east, north].</param>
        /// <param name="zoom">Zoom level to calculate tiles for.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A list of quadkey strings.</returns>
        public static string[] GetQuadkeysInBoundingBox(double[] bounds, int zoom, int tileSize)
        {
            var keys = new System.Collections.Generic.List<string>();

            if (bounds != null && bounds.Length >= 4)
            {
                PositionToTileXY(new double[] { bounds[3], bounds[0] }, zoom, tileSize, out int tlX, out int tlY);
                PositionToTileXY(new double[] { bounds[1], bounds[2] }, zoom, tileSize, out int brX, out int brY);

                for (int x = tlX; x <= brX; x++)
                {
                    for (int y = tlY; y <= brY; y++)
                    {
                        keys.Add(TileXYToQuadKey(x, y, zoom));
                    }
                }
            }

            return keys.ToArray();
        }

        /// <summary>
        /// Calculates the bounding box of a tile.
        /// </summary>
        /// <param name="tileX">Tile X coordinate</param>
        /// <param name="tileY">Tile Y coordinate</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A bounding box of the tile defined as an array of numbers in the format of [west, south, east, north].</returns>
        public static double[] TileXYToBoundingBox(int tileX, int tileY, double zoom, int tileSize)
        {
            //Top left corner pixel coordinates
            var x1 = (double)(tileX * tileSize);
            var y1 = (double)(tileY * tileSize);

            //Bottom right corner pixel coordinates
            var x2 = (double)(x1 + tileSize);
            var y2 = (double)(y1 + tileSize);

            var nw = GlobalPixelToPosition(new double[] { x1, y1 }, zoom, tileSize);
            var se = GlobalPixelToPosition(new double[] { x2, y2 }, zoom, tileSize);

            return new double[] { nw[0], se[1], se[0], nw[1] };
        }

        /// <summary>
        /// Calculates the best map view (center, zoom) for a bounding box on a map.
        /// </summary>
        /// <param name="bounds">A bounding box defined as an array of numbers in the format of [west, south, east, north].</param>
        /// <param name="mapWidth">Map width in pixels.</param>
        /// <param name="mapHeight">Map height in pixels.</param>
        /// <param name="padding">Width in pixels to use to create a buffer around the map. This is to keep markers from being cut off on the edge</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="latitude">Output parameter receiving the center latitude coordinate.</param>
        /// <param name="longitude">Output parameter receiving the center longitude coordinate.</param>
        /// <param name="zoom">Output parameter receiving the zoom level</param>
        public static void BestMapView(double[] bounds, double mapWidth, double mapHeight, int padding, int tileSize, out double centerLat, out double centerLon, out double zoom)
        {
            if (bounds == null || bounds.Length < 4)
            {
                centerLat = 0;
                centerLon = 0;
                zoom = 1;
                return;
            }

            double boundsDeltaX;

            //Check if east value is greater than west value which would indicate that bounding box crosses the antimeridian.
            if (bounds[2] > bounds[0])
            {
                boundsDeltaX = bounds[2] - bounds[0];
                centerLon = (bounds[2] + bounds[0]) / 2;
            }
            else
            {
                boundsDeltaX = 360 - (bounds[0] - bounds[2]);
                centerLon = ((bounds[2] + bounds[0]) / 2 + 360) % 360 - 180;
            }

            var ry1 = Math.Log((Math.Sin(bounds[1] * Math.PI / 180) + 1) / Math.Cos(bounds[1] * Math.PI / 180));
            var ry2 = Math.Log((Math.Sin(bounds[3] * Math.PI / 180) + 1) / Math.Cos(bounds[3] * Math.PI / 180));
            var ryc = (ry1 + ry2) / 2;

            centerLat = Math.Atan(Math.Sinh(ryc)) * 180 / Math.PI;

            var resolutionHorizontal = boundsDeltaX / (mapWidth - padding * 2);

            var vy0 = Math.Log(Math.Tan(Math.PI * (0.25 + centerLat / 360)));
            var vy1 = Math.Log(Math.Tan(Math.PI * (0.25 + bounds[3] / 360)));
            var zoomFactorPowered = (mapHeight * 0.5 - padding) / (40.7436654315252 * (vy1 - vy0));
            var resolutionVertical = 360.0 / (zoomFactorPowered * tileSize);

            var resolution = Math.Max(resolutionHorizontal, resolutionVertical);

            zoom = Math.Log(360 / (resolution * tileSize), 2);
        }
    }
}
```

#### <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
module AzureMaps {

    /** Tile System math for the Spherical Mercator projection coordinate system (EPSG:3857) */
    export class TileMath {
        //Earth radius in meters.
        private static EarthRadius = 6378137;

        private static MinLatitude = -85.05112878;
        private static MaxLatitude = 85.05112878;
        private static MinLongitude = -180;
        private static MaxLongitude = 180;

        /**
         * Clips a number to the specified minimum and maximum values.
         * @param n The number to clip.
         * @param minValue Minimum allowable value.
         * @param maxValue Maximum allowable value.
         * @returns The clipped value.
         */
        private static Clip(n: number, minValue: number, maxValue: number): number {
            return Math.min(Math.max(n, minValue), maxValue);
        }

        /**
         * Calculates width and height of the map in pixels at a specific zoom level from -180 degrees to 180 degrees.
         * @param zoom Zoom Level to calculate width at.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Width and height of the map in pixels.
         */
        public static MapSize(zoom: number, tileSize: number): number {
            return Math.ceil(tileSize * Math.pow(2, zoom));
        }

        /**
         * Calculates the Ground resolution at a specific degree of latitude in the meters per pixel.
         * @param latitude Degree of latitude to calculate resolution at.
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Ground resolution in meters per pixels.
         */
        public static GroundResolution(latitude: number, zoom: number, tileSize: number): number {
            latitude = this.Clip(latitude, this.MinLatitude, this.MaxLatitude);
            return Math.cos(latitude * Math.PI / 180) * 2 * Math.PI * this.EarthRadius / this.MapSize(zoom, tileSize);
        }

        /**
         * Determines the map scale at a specified latitude, level of detail, and screen resolution.
         * @param latitude Latitude (in degrees) at which to measure the map scale.
         * @param zoom Zoom level.
         * @param screenDpi Resolution of the screen, in dots per inch.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns The map scale, expressed as the denominator N of the ratio 1 : N.
         */
        public static MapScale(latitude: number, zoom: number, screenDpi: number, tileSize: number): number {
            return this.GroundResolution(latitude, zoom, tileSize) * screenDpi / 0.0254;
        }

        /**
         * Global Converts a Pixel coordinate into a geospatial coordinate at a specified zoom level.
         * Global Pixel coordinates are relative to the top left corner of the map (90, -180).
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A position value in the format [longitude, latitude].
         */
        public static GlobalPixelToPosition(pixel: number[], zoom: number, tileSize: number): number[] {
            var mapSize = this.MapSize(zoom, tileSize);

            var x = (this.Clip(pixel[0], 0, mapSize - 1) / mapSize) - 0.5;
            var y = 0.5 - (this.Clip(pixel[1], 0, mapSize - 1) / mapSize);

            return [
                360 * x,    //Longitude
                90 - 360 * Math.atan(Math.exp(-y * 2 * Math.PI)) / Math.PI  //Latitude
            ];
        }

        /**
         * Converts a point from latitude/longitude WGS-84 coordinates (in degrees) into pixel XY coordinates at a specified level of detail.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A pixel coordinate 
         */
        public static PositionToGlobalPixel(position: number[], zoom: number, tileSize: number): number[] {
            var latitude = this.Clip(position[1], this.MinLatitude, this.MaxLatitude);
            var longitude = this.Clip(position[0], this.MinLongitude, this.MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            var mapSize = this.MapSize(zoom, tileSize);

            return [
                this.Clip(x * mapSize + 0.5, 0, mapSize - 1),
                this.Clip(y * mapSize + 0.5, 0, mapSize - 1)
            ];
        }

        /**
         * Converts pixel XY coordinates into tile XY coordinates of the tile containing the specified pixel.
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Tile XY coordinates.
         */
        public static GlobalPixelToTileXY(pixel: number[], tileSize: number): { tileX: number, tileY: number } {
            return {
                tileX: Math.round(pixel[0] / tileSize),
                tileY: Math.round(pixel[1] / tileSize)
            };
        }

        /**
         * Performs a scale transform on a global pixel value from one zoom level to another.
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param oldZoom The zoom level in which the input global pixel value is from.
         * @param newZoom The new zoom level in which the output global pixel value should be aligned with.
         */
        public static ScaleGlobalPixel(pixel: number[], oldZoom: number, newZoom: number): number[] {
            var scale = Math.pow(2, oldZoom - newZoom);

            return [pixel[0] * scale, pixel[1] * scale];
        }

        /// <summary>
        /// Performs a scale transform on a set of global pixel values from one zoom level to another.
        /// </summary>
        /// <param name="points">A set of global pixel value from the old zoom level. Points are in the format [x,y].</param>
        /// <param name="oldZoom">The zoom level in which the input global pixel values is from.</param>
        /// <param name="newZoom">The new zoom level in which the output global pixel values should be aligned with.</param>
        /// <returns>A set of global pixel values that has been scaled for the new zoom level.</returns>
        public static ScaleGlobalPixels(pixels: number[][], oldZoom: number, newZoom: number): number[][] {
            var scale = Math.pow(2, oldZoom - newZoom);

            var output: number[][] = [];
            for (var i = 0, len = pixels.length; i < len; i++) {
                output.push([pixels[i][0] * scale, pixels[i][1] * scale]);
            }

            return output;
        }

        /**
         * Converts tile XY coordinates into a global pixel XY coordinates of the upper-left pixel of the specified tile.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Pixel coordinates in the format of [x, y].
         */
        public static TileXYToGlobalPixel(tileX, tileY, tileSize): number[] {
            return [tileX * tileSize, tileY * tileSize];
        }

        /**
         * Converts tile XY coordinates into a quadkey at a specified level of detail.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param zoom Zoom level.
         * @returns A string containing the quadkey.
         */
        public static TileXYToQuadKey(tileX: number, tileY: number, zoom: number): string {
            var quadKey: number[] = [];
            for (var i = zoom; i > 0; i--) {
                var digit = 0;
                var mask = 1 << (i - 1);

                if ((tileX & mask) != 0) {
                    digit++;
                }

                if ((tileY & mask) != 0) {
                    digit += 2
                }

                quadKey.push(digit);
            }
            return quadKey.join('');
        }

        /**
         * Converts a quadkey into tile XY coordinates.
         * @param quadKey Quadkey of the tile.
         * @returns Tile XY cocorindates and zoom level for the specified quadkey.
         */
        public static QuadKeyToTileXY(quadKey: string): { tileX: number, tileY: number, zoom: number } {
            var tileX = 0;
            var tileY = 0;
            var zoom = quadKey.length;

            for (var i = zoom; i > 0; i--) {
                var mask = 1 << (i - 1);
                switch (quadKey[zoom - i]) {
                    case '0':
                        break;

                    case '1':
                        tileX |= mask;
                        break;

                    case '2':
                        tileY |= mask;
                        break;

                    case '3':
                        tileX |= mask;
                        tileY |= mask;
                        break;

                    default:
                        throw "Invalid QuadKey digit sequence.";
                }
            }

            return {
                tileX: tileX,
                tileY: tileY,
                zoom: zoom
            };
        }

        /**
         * Calculates the XY tile coordinates that a coordinate falls into for a specific zoom level.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Tiel XY coordinates.
         */
        public static PositionToTileXY(position: number[], zoom: number, tileSize: number): { tileX: number, tileY: number } {
            var latitude = this.Clip(position[1], this.MinLatitude, this.MaxLatitude);
            var longitude = this.Clip(position[0], this.MinLongitude, this.MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            //tileSize needed in calculations as in rare cases the multiplying/rounding/dividing can make the difference of a pixel which can result in a completely different tile. 
            var mapSize = this.MapSize(zoom, tileSize);

            return {
                tileX: Math.floor(this.Clip(x * mapSize + 0.5, 0, mapSize - 1) / tileSize),
                tileY: Math.floor(this.Clip(y * mapSize + 0.5, 0, mapSize - 1) / tileSize)
            };
        }

        /**
         * Calculates the tile quadkey strings that are within a specified viewport.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param width The width of the map viewport in pixels.
         * @param height The height of the map viewport in pixels.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A list of quadkey strings that are within the specified viewport.
         */
        public static GetQuadkeysInView(position: number[], zoom: number, width: number, height: number, tileSize: number): string[] {
            var p = this.PositionToGlobalPixel(position, zoom, tileSize);

            var top = p[1] - height * 0.5;
            var left = p[0] - width * 0.5;

            var bottom = p[1] + height * 0.5;
            var right = p[0] + width * 0.5;

            var tl = this.GlobalPixelToPosition([left, top], zoom, tileSize);
            var br = this.GlobalPixelToPosition([right, bottom], zoom, tileSize);

            //Boudning box in the format: [west, south, east, north];
            var bounds = [tl[0], br[1], br[0], tl[1]];

            return this.GetQuadkeysInBoundingBox(bounds, zoom, tileSize);
        }

        /**
         * Calculates the tile quadkey strings that are within a bounding box at a specific zoom level.
         * @param bounds A bounding box defined as an array of numbers in the format of [west, south, east, north].
         * @param zoom Zoom level to calculate tiles for.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A list of quadkey strings.
         */
        public static GetQuadkeysInBoundingBox(bounds: number[], zoom: number, tileSize: number): string[] {
            var keys: string[] = [];

            if (bounds != null && bounds.length >= 4) {
                var tl = this.PositionToTileXY([bounds[0], bounds[3]], zoom, tileSize);
                var br = this.PositionToTileXY([bounds[2], bounds[1]], zoom, tileSize);

                for (var x = tl[0]; x <= br[0]; x++) {
                    for (var y = tl[1]; y <= br[1]; y++) {
                        keys.push(this.TileXYToQuadKey(x, y, zoom));
                    }
                }
            }

            return keys;
        }

        /**
         * Calculates the bounding box of a tile.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A bounding box of the tile defined as an array of numbers in the format of [west, south, east, north].
         */
        public static TileXYToBoundingBox(tileX: number, tileY: number, zoom: number, tileSize: number): number[] {
            //Top left corner pixel coordinates
            var x1 = tileX * tileSize;
            var y1 = tileY * tileSize;

            //Bottom right corner pixel coordinates
            var x2 = x1 + tileSize;
            var y2 = y1 + tileSize;

            var nw = this.GlobalPixelToPosition([x1, y1], zoom, tileSize);
            var se = this.GlobalPixelToPosition([x2, y2], zoom, tileSize);

            return [nw[0], se[1], se[0], nw[1]];
        }

        /**
         * Calculates the best map view (center, zoom) for a bounding box on a map.
         * @param bounds A bounding box defined as an array of numbers in the format of [west, south, east, north]. 
         * @param mapWidth Map width in pixels.
         * @param mapHeight Map height in pixels.
         * @param padding Width in pixels to use to create a buffer around the map. This is to keep markers from being cut off on the edge.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns The center and zoom level to best position the map view over the provided bounding box.
         */
        public static BestMapView(bounds: number[], mapWidth: number, mapHeight: number, padding: number, tileSize: number): { center: number[], zoom: number } {
            if (bounds == null || bounds.length < 4) {
                return {
                    center: [0, 0],
                    zoom: 1
                };
            }

            var boundsDeltaX: number;
            var centerLat: number;
            var centerLon: number;

            //Check if east value is greater than west value which would indicate that bounding box crosses the antimeridian.
            if (bounds[2] > bounds[0]) {
                boundsDeltaX = bounds[2] - bounds[0];
                centerLon = (bounds[2] + bounds[0]) / 2;
            }
            else {
                boundsDeltaX = 360 - (bounds[0] - bounds[2]);
                centerLon = ((bounds[2] + bounds[0]) / 2 + 360) % 360 - 180;
            }

            var ry1 = Math.log((Math.sin(bounds[1] * Math.PI / 180) + 1) / Math.cos(bounds[1] * Math.PI / 180));
            var ry2 = Math.log((Math.sin(bounds[3] * Math.PI / 180) + 1) / Math.cos(bounds[3] * Math.PI / 180));
            var ryc = (ry1 + ry2) / 2;

            centerLat = Math.atan(Math.sinh(ryc)) * 180 / Math.PI;

            var resolutionHorizontal = boundsDeltaX / (mapWidth - padding * 2);

            var vy0 = Math.log(Math.tan(Math.PI * (0.25 + centerLat / 360)));
            var vy1 = Math.log(Math.tan(Math.PI * (0.25 + bounds[3] / 360)));
            var zoomFactorPowered = (mapHeight * 0.5 - padding) / (40.7436654315252 * (vy1 - vy0));
            var resolutionVertical = 360.0 / (zoomFactorPowered * tileSize);

            var resolution = Math.max(resolutionHorizontal, resolutionVertical);

            var zoom = Math.log2(360 / (resolution * tileSize));

            return {
                center: [centerLon, centerLat],
                zoom: zoom
            };
        }
    }
}
```

* * *

> [!NOTE]
> A Azure Maps SDK interaktív térképének vezérlői a térinformatikai pozíciók és a nézetablak képpontja közötti átalakításra szolgáló segítő függvényekből állnak. 
> - [Web SDK: Térkép képpontban és pozícióban számítások](/javascript/api/azure-maps-control/atlas.map#pixelstopositions-pixel---)

## <a name="next-steps"></a>Következő lépések

A Azure Maps REST-szolgáltatásokból közvetlenül elérhetők a Térkép csempéi:

> [!div class="nextstepaction"]
> [Térkép csempék lekérése](/rest/api/maps/render/getmaptile)

> [!div class="nextstepaction"]
> [Forgalmi flow csempék beolvasása](/rest/api/maps/traffic/gettrafficflowtile)

> [!div class="nextstepaction"]
> [Forgalmi incidens csempék beolvasása](/rest/api/maps/traffic/gettrafficincidenttile)

További információ a térinformatikai fogalmakról:

> [!div class="nextstepaction"]
> [Azure Maps-szószedet](glossary.md)