---
title: Azure CDN tartalmának korlátozása ország/régió szerint
description: Megtudhatja, hogyan korlátozhatja az ország/régió hozzáférését a Azure CDN tartalomhoz a Geo-szűrés funkció használatával.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539505"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Azure CDN tartalmának korlátozása ország/régió szerint

## <a name="overview"></a>Áttekintés
Amikor egy felhasználó megkéri a tartalmat, a tartalmat a rendszer minden helyen a felhasználók számára kézbesíti. Előfordulhat, hogy ország/régió szerint szeretné korlátozni a tartalmakhoz való hozzáférést. 

A *geo-szűrési* funkcióval szabályokat hozhat létre a CDN-végponton megadott elérési utakon. A szabályokat beállíthatja úgy, hogy engedélyezze vagy tiltsa le a tartalmat a kiválasztott országokban/régiókban.

> [!IMPORTANT]
> **A Microsoft-profilokból Azure CDN standard** nem támogatja az elérésiút-alapú földrajzi szűrést.
> 

## <a name="standard-profiles"></a>Standard profilok

Ezek az utasítások a Verizon-profilokból származó Akamai és **Azure CDN standard** szintű **Azure CDN szabványokra** vonatkoznak.

A Verizon-profilokból **származó Azure CDN Premium** esetén a Geo-szűrés aktiválásához a **Manage** Portalt kell használnia. További információ: [Azure CDN Premium from Verizon Profiles](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>A könyvtár elérési útjának megadása
A Geo-szűrés funkció eléréséhez válassza ki a CDN-végpontot a portálon belül, majd válassza a **geo-szűrés** lehetőséget a bal oldali menü beállítások területén. 

![A végpontok menüjéből kiválasztott geo-szűrést ábrázoló képernyőfelvétel.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Az **elérési út** mezőben határozza meg annak a helynek a relatív elérési útját, amelyhez a felhasználók engedélyezik vagy megtagadják a hozzáférést. 

A földrajzi szűrést alkalmazhatja az összes fájlhoz egy továbbítási perjel (/) használatával, vagy kiválaszthat meghatározott mappákat a könyvtár elérési útjának megadásával (például */Pictures/*). A földrajzi szűrést egyetlen fájlba is alkalmazhatja (például */pictures/city.png*). Több szabály is engedélyezett. A szabály megadása után egy üres sor jelenik meg, amely megadja a következő szabályt.

A következő könyvtár-elérésiút-szűrők például érvényesek:   
*/*                                 
*/Fotó/*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>A művelet típusának meghatározása

A **művelet** listából válassza az **Engedélyezés** vagy a **Letiltás** lehetőséget: 

- **Engedélyezés**: csak a megadott országokból/régiókból származó felhasználók férhetnek hozzá a rekurzív elérési útról kért eszközökhöz.

- **Letiltás**: a megadott országokból/régiókból származó felhasználók nem férhetnek hozzá a rekurzív elérési útról kért eszközökhöz. Ha ezen a helyen nincs más ország/régió szerinti szűrési lehetőség konfigurálva, az összes többi felhasználó hozzáférése is engedélyezett lesz.

Például a */photos/Strasbourg/* elérési út blokkolására szolgáló geo-szűrési szabály a következő fájlokat szűri:     
*http: \/ / \<endpoint> . azureedge.net/photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.net/photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Országok/régiók meghatározása

Az **országkódok** listából válassza ki azokat az országokat/régiókat, amelyeket le kíván tiltani, vagy engedélyezni kívánja az elérési utat. 

Miután befejezte az országok/régiók kijelölését, válassza a **Mentés** lehetőséget az új geo-szűrési szabály aktiválásához. 

![A képernyőképen az országok és régiók letiltására és engedélyezésére használt országkódok láthatók.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Egy szabály törléséhez válassza ki a listából a **geo-szűrés** lapon, majd válassza a **Törlés** lehetőséget.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Prémium szintű Azure CDN Verizon-profilokból

A Verizon-profilokból **származó Azure CDN Premium** esetén a Geo-szűrési szabály létrehozásához használt felhasználói felület különbözik:

1. A Azure CDN profil felső menüjében válassza a **kezelés** lehetőséget.

2. A Verizon portálon válassza a **http Large**, majd az **ország szűrése** lehetőséget.

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Képernyőfelvétel: az ország szűrésének kiválasztása Azure CDN" border="true":::
  
3. Válassza az **ország szűrő hozzáadása** lehetőséget.

4. Az első **lépés:** mezőben adja meg a könyvtár elérési útját. Válassza a **Letiltás** vagy **Hozzáadás** lehetőséget, majd kattintson a **tovább** gombra.

    > [!IMPORTANT]
    > A végpont nevének az elérési útnak kell lennie.  Példa: **/myendpoint8675/MyFolder**.  Cserélje le a **myendpoint8675** nevet a végpont nevére.
    > 
    
5. A **második lépésben** válasszon ki egy vagy több országot/régiót a listából. A szabály aktiválásához válassza a **Befejezés** gombot. 
    
    Az új szabály megjelenik az **ország szűrése** lap táblázatban.
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="A képernyőképen látható, hogy a szabály hol jelenik meg az ország-szűrésben." border="true":::
 
### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az ország/régió szűrési szabályok táblázatban válassza a törlés ikont egy szabály mellett a törléshez, vagy a Szerkesztés ikont a módosításhoz.

## <a name="considerations"></a>Megfontolandó szempontok
* A Geo-szűrési konfiguráció módosításai nem lépnek azonnal érvénybe:
   * A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
   * Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
   * A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
 
* Ez a funkció nem támogatja a helyettesítő karaktereket (például *).

* A relatív elérési úttal társított geo-szűrési konfiguráció rekurzív módon van alkalmazva erre az elérési útra.

* Ugyanarra a relatív elérési útra csak egy szabály alkalmazható. Vagyis nem hozható létre több ország/régió szűrő, amely ugyanarra a relatív elérési útra mutat. Mivel azonban az ország/régió szűrők rekurzívak, a mappának több ország/régió szűrője is lehet. Ez azt jelenti, hogy egy korábban konfigurált mappa almappája hozzárendelhető egy másik ország/régió szűrőhöz.

* A Geo-szűrési funkció országkódok használatával határozza meg azokat az országokat/régiókat, amelyekről egy biztonságos könyvtárra vonatkozó kérés engedélyezett vagy le van tiltva. Bár a Akamai és a Verizon-profilok a legtöbb azonos országkód használatát támogatják, néhány eltérés van. További információ: [Azure CDN országkódok](/previous-versions/azure/mt761717(v=azure.100)). 

