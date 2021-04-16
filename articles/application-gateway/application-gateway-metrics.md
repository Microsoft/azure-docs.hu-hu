---
title: Azure Monitor metrikák a Application Gateway
description: Ismerje meg, hogyan használhatja a metrikákat az Application Gateway teljesítményének figyelése érdekében
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: azhussai
ms.openlocfilehash: 3baaf49cb3d1c8c5502d96974f9729d05996c75b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519885"
---
# <a name="metrics-for-application-gateway"></a>Metrikák Application Gateway

Application Gateway közzétesz metrikáknak nevezett [](../azure-monitor/overview.md) adatpontokat a Azure Monitor és háttérpéldányok teljesítményének Application Gateway érdekében. Ezek a metrikák idősorozat-adatok rendezett készletében numerikus értékek, amelyek egy adott időpontban írják le az Alkalmazásátjáró valamilyen aspektusát. Ha a kérelmek áthaladnak a Application Gateway, 60 másodperces időközönként méri és küldi el a metrikákat. Ha nincsenek a metrikákon áthaladó kérések Application Gateway vagy nincsenek metrikák adatai, a rendszer nem jelenti a metrikát. További információ: Azure Monitor [metrikák.](../azure-monitor/essentials/data-platform-metrics.md)

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>A 2-es Application Gateway termékváltozat által támogatott metrikák

### <a name="timing-metrics"></a>Időzítési metrikák

Application Gateway a kéréshez és a válaszhoz kapcsolódó számos beépített időzítési metrikát biztosít, amelyek mindegyikét ezredmásodpercben mérjük. 

![Az ütemezési metrikák diagramja a Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.jpg)

> [!NOTE]
>
> Ha egynél több listener van a Application Gateway, akkor mindig szűrje a *Listener* dimenzió alapján, és hasonlítsa össze a különböző késési metrikákat, hogy értelmes dedokenciát kap.

- **Háttér-csatlakozás ideje**

  A háttéralkalmazással való kapcsolat kialakításával töltött idő. 

  Ez magában foglalja a hálózati késést, valamint a háttérkiszolgáló TCP-vermének az új kapcsolatok létesítése érdekében szükséges idejét. A TLS esetében magában foglalja a kézfogással töltött időt is. 

- **Háttér– első bájt válaszideja**

  A háttérkiszolgálóval való kapcsolat létesítését és a válaszfejléc első bájtjának fogadását eltelő időintervallum. 

  Ez a háttéralkalmazás kapcsolódási ideje, a háttéralkalmazás válaszideje (a tartalom létrehozásához szükséges idő, az adatbázis-lekérdezések beolvasása) és a válasz első bájtja által a háttéralkalmazástól a Application Gateway eléréséhez szükséges idő összege, a háttéralkalmazás által a válasz első bájtja által a háttérből való elolvasáshoz szükséges idő összege. Application Gateway

- **Háttér–utolsó bájt válaszideja**

  A háttérkiszolgálóval létesített kapcsolat létrehozásának kezdete és a válasz törzsének utolsó bájtjának fogadása között eltelt idő. 

  Ez nagyjából a *háttérrendszer első bájtjának válaszideje* és az adatátviteli idő összegét teszi ki (ez a szám nagy mértékben változhat a kért objektumok mérete és a kiszolgálói hálózat késésének függvényében).

- **Application Gateway – teljes idő**

  A kérések bekérésének és feldolgozásának átlagos ideje, valamint a válasz elküldését. 

  Ez az az időköz, amely Application Gateway http-kérés első bájtja és az utolsó válasz bájt az ügyfélnek való elküldését között eltelt idő között. Ez magában foglalja a Application Gateway által lekért feldolgozási időt, a háttéralkalmazás utolsó bájtja válaszidet, a Application Gateway által az összes válasz elküldését és a Client *RTT-t*.

- **Ügyfél RTT**

  Az ügyfelek és az ügyfelek közötti átlagos Application Gateway.



Ezek a metrikák annak meghatározására használhatók, hogy a megfigyelt lassulást az ügyfélhálózat, az Application Gateway-teljesítmény, a háttérhálózat és a háttérkiszolgáló TCP-veremtelítettség, a háttéralkalmazás teljesítménye vagy a nagy fájlméret határozza meg.

Ha például kiugróan magas  a háttérkiszolgáló első bájtja  válaszideje, de a háttérkapcsolati idő trendje stabil, akkor kikövetkeztetheti, hogy az Application Gateway és a háttér közötti késés, valamint a kapcsolat létesítésének ideje stabil, és a kiugrás a háttéralkalmazás válaszideje növekedésének köszönhető. Ha azonban a háttérkiszolgáló  első bájtjának válaszidejéhez tartozó kiugró érték a háttér-kapcsolódási idő megfelelő csúcsával van társítva, akkor az a következő lehet: a hálózat az Application Gateway és a háttérkiszolgáló között, vagy a háttérkiszolgáló TCP-vermének telített.  

Ha a háttér  utolsó bájtja válaszideje  kiugróan magas, de a háttér háttér-rendszer első bájtja válaszideje stabil, akkor a kiugrás oka egy nagyobb fájl kérése lehet.

Hasonlóképpen, ha az *Application Gateway* teljes  ideje kiugróan magas, de a háttérbeli utolsó bájt válaszideje stabil Application Gateway, akkor az a teljesítmény szűk keresztmetszetét vagy a hálózat szűk keresztmetszetét mutathatja az ügyfél és a Application Gateway. Emellett, ha az ügyfél *RTT-jének* is van egy megfelelő kiugró csúcsa, akkor azt jelzi, hogy a teljesítménycsökkenést az ügyfél és a kiszolgáló közötti hálózat Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway metrikák

A Application Gateway következő metrikák érhetők el:

- **Fogadott bájtok**

   Az ügyfelektől fogadott Application Gateway bájtok száma

- **Küldött bájtok**

   A Application Gateway által az ügyfeleknek küldött bájtok száma

- **Ügyfél TLS protokollja**

   Az ügyfél által kezdeményezett TLS- és nem TLS-kérelmek száma, amely kapcsolatot létesítet a Application Gateway. A TLS-protokoll terjesztésének megtekintéséhez szűrje a TLS-protokoll dimenziót.

- **Aktuális kapacitásegységek**

   A forgalom terheléselosztásához felhasznált kapacitásegységek száma. A kapacitásegységnek három tényezőt kell meghatároznia: a számítási egységet, az állandó kapcsolatokat és az átviteli sebességet. Minden kapacitásegység legalább 1 számítási egységből vagy 2500 állandó kapcsolatból vagy 2,22 Mbps átviteli sebességből áll.

- **Aktuális számítási egységek**

   A felhasznált processzorkapacitások száma. A számítási egységet befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-újraírási számítások és a WAF-szabályok feldolgozása. 

- **Aktuális kapcsolatok**

   Az ügyfelek és a Application Gateway
   
- **Becsült számlázott kapacitásegységek**

  A v2 termékváltozat esetében az árképzési modell alapjául a felhasználás szolgál. A kapacitásegységek a fix költség mellett felszámított felhasználáson alapuló költségeket mérik. *A becsült számlázható kapacitásegységek* azt jelzik, hogy hány kapacitásegységet használ a számlázás. Ennek kiszámítása az *aktuális kapacitásegységek* (a forgalom terheléselosztásához szükséges kapacitásegységek) és a *fix számlázandó kapacitásegységek* (a minimálisan biztosított kapacitásegységek) közül a nagyobb érték alapján történik.

- **Sikertelen kérelmek**

  Azon kérések száma, Application Gateway 5xx kiszolgálói hibakódokat tartalmaztak. Ez magában foglalja az alkalmazásból létrehozott 5xx Application Gateway a háttérből generált 5xx kódokat is. A kérések száma tovább szűrhető úgy, hogy az egyes/adott háttérkészlet-http beállításkombinációk számát mutassa.
   
- **Fix számlázandó kapacitásegységek**

  Az Application Gateway konfigurációjában a *Minimális skálázási egységek* beállítás (egy példány 10 kapacitásegységnek felel meg) alapján meghatározott minimális kapacitásegység-szám.
   
 - **Új kapcsolatok másodpercenként**

   Az ügyfelek és a Application Gateway és a Application Gateway közötti új TCP-kapcsolatok másodpercenkénti átlagos száma.


- **Válasz állapota**

   A http-válasz állapota, amelyet a Application Gateway. A válasz állapotkód-eloszlása tovább kategorizálható úgy, hogy a 2xx, 3xx, 4xx és 5xx kategóriákba tartozó válaszokat mutassa.

- **Átviteli sebesség**

   A másodpercenként kiszolgált Application Gateway bájtok száma

- **Összes kérelem**

   A sikeresen kiszolgált Application Gateway száma. A kérések száma tovább szűrhető úgy, hogy az egyes/adott háttérkészlet-http beállításkombinációk számát mutassa.

### <a name="backend-metrics"></a>Háttérmetrikák

A Application Gateway következő metrikák érhetők el:

- **Háttér-válasz állapota**

  A háttérbeliek által visszaadott HTTP-válasz állapotkódok száma. Ez nem tartalmazza az alkalmazás által létrehozott Application Gateway. A válasz állapotkód-eloszlása tovább kategorizálható úgy, hogy a 2xx, 3xx, 4xx és 5xx kategóriákba tartozó válaszokat mutassa.

- **Kifogástalan állapotú gazdagépek száma**

  Az állapot-mintavétel által kifogástalan állapotúként meghatározott háttér-háttérítők száma. A megfelelő állapotú gazdagépek számának megjelenítése egy adott háttérkészletben háttérkészletenként szűrhető.

- **Nem kifogástalan állapotú gazdagépek száma**

  Az állapotfigyelő által nem megfelelőnek meghatározott háttér-háttérítők száma. A nem megfelelő gazdagépek számát egy adott háttérkészletben háttérkészletenként szűrheti.
  
- **Kérések percenként kifogástalan állapotú gazdagépenként**

  A megfelelő állapotú tag által egy háttérkészletben fogadott kérelmek átlagos száma egy percen belül. A háttérkészletet a *BackendPool HttpSettings* dimenzió használatával kell megadnia.  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Az 1-es Application Gateway termékváltozat által támogatott metrikák

### <a name="application-gateway-metrics"></a>Application Gateway metrikák

A Application Gateway következő metrikák érhetők el:

- **Processzor kihasználtsága**

  Megmutatja az Application Gateway számára lefoglalt processzorok kihasználtságát.  Normál körülmények között a processzorhasználat nem haladhatja meg rendszeresen a 90%-ot, mivel ez késést okozhat az Application Gateway mögött futtatott webhelyeken, ami ronthatja az ügyfélélményt. Az Application Gateway konfigurációjának módosításával közvetve szabályozhatja vagy javíthatja a processzorkihasználtságot úgy, hogy megnöveli a példányszámot és/vagy nagyobb termékváltozat-méretre vált.

- **Aktuális kapcsolatok**

  A meglévő kapcsolatokkal létrehozott Application Gateway

- **Sikertelen kérelmek**

  A csatlakozási problémák miatt meghiúsult kérelmek száma. Ez a szám magában foglalja azokat a kérelmeket, amelyek meghiúsultak a "Kérés időkorrelációja" HTTP-beállítás túllépése miatt, valamint az Application Gateway és a háttérkiszolgáló közötti csatlakozási problémák miatt meghiúsult kérések. Ez a szám nem tartalmazza az olyan hibákat, amelyek nem állnak rendelkezésre kifogástalan állapotú háttérből. A háttérből származó 4xx és 5xx típusú válaszok szintén nem számítanak ennek a metrikának a részeként.

- **Válasz állapota**

  A http-válasz állapotát a Application Gateway. A válasz állapotkód-eloszlása tovább kategorizálható a válaszok 2xx, 3xx, 4xx és 5xx kategóriákban való megjelenítése érdekében.

- **Átviteli sebesség**

  A másodpercenként kiszolgált Application Gateway bájtok száma

- **Összes kérelem**

  A sikeresen kiszolgált Application Gateway száma. A kérések száma tovább szűrhető úgy, hogy az egyes/adott háttérkészlet-http beállításkombinációk számát mutassa.

- **Web Application Firewall kérelmek száma**
- **Web Application Firewall kérelmek eloszlása**
- **Web Application Firewall szabályeloszlás teljes száma**

### <a name="backend-metrics"></a>Háttérmetrikák

A Application Gateway következő metrikák érhetők el:

- **Kifogástalan állapotú gazdagépek száma**

  Az állapot-mintavétel által kifogástalan állapotúként meghatározott háttér-háttérítők száma. A megfelelő állapotú gazdagépek számának megjelenítése egy adott háttérkészletben háttérkészletenként szűrhető.

- **Nem kifogástalan állapotú gazdagépek száma**

  Az állapotfigyelő által nem megfelelőnek meghatározott háttér-háttérítők száma. A nem megfelelő gazdagépek számát egy adott háttérkészletben háttérkészletenként szűrheti.

## <a name="metrics-visualization"></a>Metrikák vizualizációja

Tallózással keresse meg az Alkalmazásátjárót, és **a Figyelés alatt válassza** a **Metrikák lehetőséget.** Az elérhető értékeket a **METRIKÁK** legördülő listában találja.

Az alábbi képen egy példa látható, amely három metrikát mutat be az elmúlt 30 percre:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Metrikanézet." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

A metrikák aktuális listáját a Támogatott metrikák a [Azure Monitor.](../azure-monitor/essentials/metrics-supported.md)

### <a name="alert-rules-on-metrics"></a>Metrikák riasztási szabályai

Az erőforrások metrika alapján riasztási szabályokat is elindíthat. A riasztások például hívhatnak egy webhookot, vagy e-mailben értesítheti a rendszergazdát, ha az Application Gateway átviteli sebességének átviteli sebessége egy adott időtartam alatt vagy felett van.

Az alábbi példa végigvezeti egy riasztási szabály létrehozásának folyamatán, amely e-mailt küld egy rendszergazdának, miután az átviteli sebesség túllépte a küszöbértéket:

1. válassza **a Metrikariasztás** hozzáadása lehetőséget a **Szabály hozzáadása lap megnyitásához.** Ezt az oldalt a metrikák oldalról is elérheti.

   !["Metrikariasztás hozzáadása" gomb][6]

2. A Szabály **hozzáadása lapon** adja meg a nevet, a feltételt és az értesítési szakaszt, majd kattintson az **OK gombra.**

   * A **Feltételválasztóban** válasszon egyet a következő négy érték közül: **Nagyobb,** mint **,** Nagyobb vagy egyenlő, **Kisebb,** mint , Vagy Kisebb **vagy egyenlő,** mint .

   * Az **Időszakválasztóban** válasszon ki egy időszakot öt perctől 6 óráig.

   * Ha a **Tulajdonos, közreműködők** és olvasók e-mail küldése lehetőséget választja, az e-mail dinamikusan használhatja az adott erőforráshoz hozzáféréssel felfelhasználókat. Ellenkező esetben meg lehet adni a felhasználók vesszővel elválasztott listáját a További rendszergazdai **e-mail-címek mezőben.**

   ![Szabály hozzáadása oldal][7]

Ha megsértik a küszöbértéket, a következő képen láthatóhoz hasonló e-mail érkezik:

![E-mail a küszöbérték túllépése esetén][8]

A metrikákra vonatkozó riasztások létrehozása után megjelenik a riasztások listája. Áttekintést nyújt az összes riasztási szabályról.

![Riasztások és szabályok listája][9]

További információ a riasztási értesítésekről: [Riasztási értesítések fogadása.](../azure-monitor/alerts/alerts-overview.md)

A webhookokkal és a riasztásokkal való használatukról további információért látogasson el a Configure [a webhook on an Azure metric alert (Webhook konfigurálása Azure-metrikák riasztásán) weblapra.](../azure-monitor/alerts/alerts-webhooks.md)

## <a name="next-steps"></a>Következő lépések

* Számláló- és eseménynaplók vizualizációja az [Azure Monitor használatával.](../azure-monitor/insights/azure-networking-analytics.md)
* [Az Azure-tevékenységnapló vizualizációja egy Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) blogbejegyzésben.
* [Megtekintheti és elemezheti az Azure-tevékenységnaplókat Power BI blogbejegyzésben.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
