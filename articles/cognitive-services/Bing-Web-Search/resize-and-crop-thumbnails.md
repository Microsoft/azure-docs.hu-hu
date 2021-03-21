---
title: Képminiatűrök átméretezése és körülvágása – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Néhány válasz a Bing Search API-k tartalmaz a Bing által szolgáltatott miniatűr rendszerképekre mutató URL-címeket, amelyek átméretezhetők és elhelyezhetők, és lekérdezési paramétereket is tartalmazhatnak.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350483"
---
# <a name="resize-and-crop-thumbnail-images"></a>Miniatűr képek átméretezése és körülvágása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Néhány válasz a Bing Search API-k tartalmaz a Bing által szolgáltatott miniatűr rendszerképekre mutató URL-címeket, amelyek átméretezhetők és elhelyezhetők, és lekérdezési paramétereket is tartalmazhatnak. Például:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Ha megjeleníti a miniatűrök egy részhalmazát, adjon meg egy lehetőséget a fennmaradó képek megtekintéséhez.

> [!NOTE]
> Ügyeljen arra, hogy a miniatűr lemezképek kivágása és átméretezése olyan keresési forgatókönyvet biztosítson, amely tiszteletben tartja a harmadik fél jogosultságait, ahogy azt a Bing Search API [-használat és a megjelenítési követelmények](use-display-requirements.md)megkövetelik.

## <a name="resize-a-thumbnail"></a>Miniatűr átméretezése 

A miniatűr átméretezéséhez a Bing javasolja, hogy `w` `h` a miniatűr URL-címében csak egy (szélesség) vagy (magasság) lekérdezési paramétert határozzon meg. Csak a magasság vagy a szélesség meghatározása lehetővé teszi, hogy a Bing fenntartsa a rendszerkép eredeti aspektusát. Itt adhatja meg a szélességet és a magasságot képpontban megadva. 

Ha például az eredeti miniatűr a 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

Ha csökkenteni szeretné a méretét, állítsa a `w` paramétert egy új értékre (például `336` ), és távolítsa el a `h`  paramétert:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Ha csak a miniatűr magasságát vagy szélességét állítja be, a kép eredeti oldalaránya megőrződik. Ha mindkét paramétert megadja, és a méretarány nem marad meg, a Bing a kép szegélyéhez adja hozzá a fehér margót.

Ha például egy 480x359-rendszerképet a 200x200-re való méretezés nélkül lakik, a teljes szélesség tartalmazni fogja a képet, de a magasság 25 képpontot tartalmaz a képen látható tetején és alján. Ha a képet 359x480, a bal és a jobb oldali szegély fehér margót fog tartalmazni. Ha levágja a képet, a fehér kitöltés nincs hozzáadva.  

Az alábbi képen a miniatűr kép eredeti mérete (480x300) látható.  
  
![Eredeti tájképi rendszerkép](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Az alábbi ábrán a 200x200-re átméretezett kép látható. A rendszer karbantartja a méretarányt, és a felső és az alsó szegély fehér színű (a fekete szegély itt látható a kitöltés megjelenítéséhez).  
  
![Átméretezett tájképi rendszerkép](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Ha olyan dimenziókat ad meg, amelyek nagyobbak, mint a rendszerkép eredeti szélessége és magassága, a Bing a bal és a felső szegélyhez adja hozzá a fehér margót.  

## <a name="request-different-thumbnail-sizes"></a>Különböző miniatűr méretek kérése

Ha más miniatűr képméretet szeretne kérni, távolítsa el az összes lekérdezési paramétert a miniatűr URL-címéből, kivéve a `id` és a `pid` paramétereket. Ezután adja hozzá a `&w` (szélesség) vagy a `&h` (magasság) lekérdezési paramétert a kívánt képmérethez képpontban, de ne mindkettőt. A Bing a kép eredeti oldalarányát fogja fenntartani. 

A fenti URL-cím által a 165 képpont értékre megadott képek szélességének növeléséhez használja a következő URL-címet:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Ha a rendszerkép eredeti méreténél nagyobb méretű képet kér, a Bing szükség szerint fehér margót hoz létre a rendszerkép körül. Ha például a rendszerkép eredeti mérete 474x316, és a 500 értékre van állítva `&w` , a Bing egy 500x333-képet ad vissza. Ennek a képnek 8,5 képpont fehér kitöltése lesz a felső és az alsó szélen, a bal és a jobb szélen pedig 13 képpont kitöltéssel.

Ha meg szeretné akadályozni, hogy a Bing ne adjon hozzá fehér kitöltést, ha a kért méret nagyobb, mint a rendszerkép eredeti mérete, állítsa `&p` 0-ra a lekérdezési paramétert. Ha például belefoglalja a `&p=0` paramétert a fenti URL-címre, a Bing a 500x333-rendszerkép helyett egy 474x316-képet ad vissza:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Ha `&w` `&h` a és a lekérdezési paramétereket is megadja, a Bing megtartja a kép méretarányát, és szükség esetén fehér kitöltés hozzáadását teszi szükségessé. Ha például a rendszerkép eredeti mérete 474x316, és a szélességi és a magassági paramétereket a 200x200 () értékre állítja `&w=200&h=200` , a Bing egy olyan képet ad vissza, amely a felső és az alsó 33 képpontos fehér kitöltést tartalmaz. Ha a `&p` lekérdezési paramétert is tartalmazza, a Bing egy 200x134-képet ad vissza.

## <a name="crop-a-thumbnail"></a>Miniatűr körülvágása 

Egy kép körülvágásához adja meg a `c` (Crop) lekérdezési paramétert. A következő értékeket használhatja:
  
- `4`&mdash;Vak arány  
- `7`&mdash;Intelligens arány  

### <a name="smart-ratio-cropping"></a>Intelligens oldalarány levágása

Ha intelligens oldalarány-levágást kér (a paraméter értékének beállításával `c` `7` ), a Bing egy képet fog kivenni a régiójának közepéről, a kép méretarányának megtartása mellett. Az érdeklődési régió a Bing által a legtöbb importálási rész részét képező rendszerkép területe. A következő példa egy érdekes régiót mutat be.  
  
![Érdekes régió](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Ha átméretezi a rendszerképet, és intelligens arányt szeretne leképezni, a Bing a Méretarány megtartása mellett csökkenti a kívánt méretet. A Bing ezt követően kivágja a képet az átméretezett méretek alapján. Ha például az átméretezett szélesség kisebb vagy egyenlő, mint a magasság, a Bing az érdeklődési régió középpontjának bal és jobb oldalán fogja kivágni a képet. Ellenkező esetben a Bing az érdeklődési régió központjának tetején és alján fogja levágni.  
  
 
Az alábbi ábrán a 200x200-re csökkenthető az intelligens oldalarány levágása. Mivel a Bing a képet a bal felső sarokban méri, a kép alsó részét a rendszer levágja. 
  
![Fekvő kép kivágása a 200x200-be](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Az alábbi ábrán látható, hogy a képet a 200x100 az intelligens oldalarányok levágása segítségével csökkenti. Mivel a Bing a képet a bal felső sarokban méri, a kép alsó részét a rendszer levágja. 
   
![A fekvő kép bevágása a 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Az alábbi ábrán látható, hogy a képet a 100x200 az intelligens oldalarányok levágása segítségével csökkenti. Mivel a Bing méri a képet a központból, a kép bal és jobb oldali részének bevágása történik.
  
![A fekvő kép bevágása a 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Ha a Bing nem tudja meghatározni a rendszerkép régióját, a szolgáltatás a vak arányok levágását fogja használni.  

### <a name="blind-ratio-cropping"></a>Vak arányok levágása

Ha a vak arány levágását kéri (a paraméter értékének beállításával `c` `4` ), a Bing a következő szabályok használatával vágja le a rendszerképet.  
  
- Ha `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` a kép a bal felső sarokban található, és a lemez alul van levágva.  
- Ha `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` a rendszer a képet a középpontból méri, a kép a bal és a jobb oldalon van.  

Az alábbi képen egy 225x300 álló portré látható.  
  
![Eredeti napraforgó képe](./media/resize-crop/bing-resize-crop-sunflower.png)
  
A következő ábrán látható, hogy a képet a "a" vak arányok levágásával csökkentettük. A képet a bal felső sarokban kell mérni, ami a bevágási kép alsó részét eredményezi.  
  
![A napraforgó képének bevágása a 200x200-be](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Az alábbi ábrán látható, hogy a 200x100 a vak arányok levágásával csökkenthető. A képet a bal felső sarokban kell mérni, ami a bevágási kép alsó részét eredményezi.  
  
![A napraforgó-kép bevágása a 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Az alábbi ábrán látható, hogy a 100x200 a vak arányok levágásával csökkenthető. A képet a középpontból kell mérni, ami a levágott kép bal és jobb oldali részét eredményezi.  
  
![A napraforgó-kép bevágása a 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Következő lépések

* [Mi a Bing Search API-k?](bing-api-comparison.md)
* [Bing Search API – követelmények használata és megjelenítése](use-display-requirements.md)