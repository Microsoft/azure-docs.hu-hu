---
title: A díjszabás ismertetése – Azure Application Gateway
description: Ez a cikk az Azure Application Gateway és a webalkalmazási tűzfal számlázási folyamatát ismerteti a v1 – v2 SKU-hoz
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96601611"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Az Azure Application Gateway és a webalkalmazási tűzfal díjszabásának ismertetése

>[!NOTE]
>A cikkben látható árak példák, és csak illusztrációs célokat szolgálnak. A díjszabással kapcsolatos információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-gateway/).

Az Azure Application Gateway egy 7. rétegbeli terheléselosztási megoldás, amely méretezhető, magasan elérhető és biztonságos webalkalmazás-kézbesítést tesz lehetővé az Azure-ban.

A Application Gatewayhoz kapcsolódó előzetes költségek vagy megszüntetési költségek nincsenek.
Csak az előre kiépített és a tényleges óránkénti felhasználás alapján felhasznált erőforrások után kell fizetni. A Application Gatewayhöz kapcsolódó költségek két összetevőbe sorolhatók: rögzített költségek és változó költségek. Az egyes összetevőkön belüli tényleges költségek a felhasznált SKU alapján változnak.

Ez a cikk az egyes SKU-kal kapcsolatos költségeket ismerteti, és javasoljuk, hogy a felhasználók ezt a dokumentumot használják az Azure Application Gateway kapcsolódó költségek megtervezéséhez és kezeléséhez.

## <a name="v1-skus"></a>V1 SKU

A standard Application Gateway és a WAF v1 SKU-t a következők kombinációjából számítjuk fel:

* Fix díj

    Egy adott típusú Application Gateway/WAF kiépített és a feldolgozási kérelmekhez való futásának ideje alapján kell fizetni.
    A fix Cost összetevő a következő tényezőket veszi figyelembe:
    * Standard szintű Application Gateway vagy WAF
    * Méret – kicsi, közepes & nagyméretű
    * Példányok száma – telepítendő példányok száma

    N példány esetén a kapcsolódó költségek N * egy adott réteg (standard & WAF) egy példányának költsége, & méret (kis, közepes & nagy) kombináció.

* Változó díja

    A Application Gateway/WAF által feldolgozott adatmennyiségen alapuló díj. A Application Gateway által feldolgozott kérelmek és válaszok bájtjai is megtekinthetők a számlázáshoz.

Teljes ár = rögzített ár + változó költsége

### <a name="standard-application-gateway"></a>Standard Application Gateway

A fix Cost & változó díja a Application Gateway típusa alapján lesz kiszámítva.
Az alábbi táblázat az USA keleti régiójának díjszabásán alapuló, példaként szolgáló árakat mutatja be, és kizárólag illusztrációs célokat szolgál.

#### <a name="fixed-cost-east-us-region-pricing"></a>Fix költség (az USA keleti régiójának díjszabása)

|              Application Gateway típusa             |  Költségek ($/óra)  |
| ------------------------------------------------- | ---------------|
|                     Kicsi                         |    $0,025      |
|                     Közepes                        |    $0,07       |
|                     Nagy                         |    $0,32       |

A havi díjak becslése havi 730 órányi használaton alapul.

#### <a name="variable-cost-east-us-region-pricing"></a>Változó költség (az USA keleti régiójának díjszabása)

|              Feldolgozott adatfeldolgozás             |  Kicsi ($/GB)  |  Közepes ($/GB) |  Nagyméretű ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Első 10 TB/hó                       |     $0,008     |      Ingyenes      |     Ingyenes      |
| Következő 30 TB (10 – 40 TB)/hó             |     $0,008     |     $0,007     |     Ingyenes      |
| Több mint 40 TB/hó                        |     $0,008     |     $0,007     |     $0,0035   |

A régiójának megfelelő díjszabási információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

A fix költségek & változó díjait a kiépített Application Gateway típusa alapján számítjuk ki.

Az alábbi táblázat az USA keleti régiójának díjszabása alapján mutatja be a díjakat, és csak illusztrációs célokat szolgál.

#### <a name="fixed-cost-east-us-region-pricing"></a>Fix költség (az USA keleti régiójának díjszabása)

|              Application Gateway típusa             |  Költségek ($/óra)  |
| ------------------------------------------------- | ---------------|
|                     Kicsi                         |       NA       |
|                     Közepes                        |     $0,126     |
|                     Nagy                         |     $0,448     |

A havi díjak becslése havi 730 órányi használaton alapul.

#### <a name="variable-cost-east-us-region-pricing"></a>Változó költség (az USA keleti régiójának díjszabása)

|              Feldolgozott adatfeldolgozás             |  Kicsi ($/GB)  |  Közepes ($/GB) |  Nagyméretű ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Első 10 TB/hó                       |     $0,008     |      Ingyenes      |     Ingyenes      |
| Következő 30 TB (10 – 40 TB)/hó             |     $0,008     |     $0,007     |     Ingyenes      |
| Több mint 40 TB/hó                        |     $0,008     |     $0,007     |     $0,0035   |

A régiójának megfelelő díjszabási információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Kimenő adatforgalom – az Azure-adatközpontokból az Application gatewaytől kiinduló adatokra a standard szintű [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/bandwidth/)alapján kerül sor.

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>1. példa (a) – standard Application Gateway 1 példányok számával

Tegyük fel, hogy kiépített egy standard Application Gateway 1 példánnyal, és egy hónapon belül 500 GB-ot dolgoz fel. A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

Rögzített ár = $0,07 * 730 (óra) = $51,1 a havi díjak becslése havi 730 órányi használaton alapul.

Változó költségek = ingyenes (a közepes szinten a havonta feldolgozott első 10 TB-ot nem számítjuk fel) teljes költség = $51,1 + 0 = $51,1 

> [!NOTE]
> A magas rendelkezésre állási forgatókönyvek támogatásához legalább 2 példányt kell beállítani v1 SKU-hoz. Lásd: [SLA a Application Gateway számára](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>1. példa (b) – standard Application Gateway > 1 példányok számával

Tegyük fel, hogy kiépített egy közepes típusú standard Application Gateway öt példánytal, és havonta 500 GB-ot dolgoz fel. A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

Rögzített ár = 5 (példányszám) * $0,07 * 730 (óra) = $255,5 a havi díjak becslése havi 5 órányi használaton 730 alapul.

Változó költségek = ingyenes (a közepes szinten a havonta feldolgozott első 10 TB-ot nem számítjuk fel) teljes költség = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>2. példa – WAF Application Gateway

Tegyük fel, hogy kiépített egy kisméretű standard Application Gateway és egy nagyméretű WAF Application Gateway a hónap első 15 napjára. A kisméretű Application Gateway a 15 TB-ot az aktív állapotban dolgozza fel, a nagy WAF Application Gateway pedig a 100 TB-ot az aktív időtartam alatt. A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki: 

###### <a name="small-instance-standard-application-gateway"></a>Kisméretű példányok standard Application Gateway

24 óra * 15 nap = 360 óra

Rögzített ár = $0,025 * 360 (óra) = $9

Változó költségek = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Teljes költség = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>Nagyméretű példányok WAF Application Gateway
24 óra * 15 nap = 360 óra

Rögzített ár = $0,448 * 360 (óra) = $161,28

Változó költségek = 60 * 1000 * $0.0035/GB = $210 (a nagyméretű szinten nem jár költség a havonta feldolgozott első 40 TB esetében)

Teljes költség = $161,28 + $210 = $371,28

## <a name="v2-skus"></a>V2 SKU  

Az Application Gateway v2 és a WAF v2 SKU támogatja az automatikus skálázást, és alapértelmezés szerint garantálja a magas rendelkezésre állást.

### <a name="key-terms"></a>Kulcsfontosságú kifejezések

##### <a name="capacity-unit"></a>Kapacitási egység 
A kapacitási egység a kapacitás kihasználtságának mértéke egy Application Gateway több paraméter között.

Egyetlen kapacitási egység a következő paraméterekből áll:
* 2500 állandó kapcsolatok
* 2,22 – Mbps átviteli sebesség
* 1 számítási egység

Ha túllépi a paraméterek bármelyikét, akkor még egy másik n kapacitású egység is szükséges, akkor is, ha a másik két paraméter nem haladja meg ezt az egyetlen kapacitási egység korlátait.
A fenti három közül a legmagasabb kihasználtságú paraméter belsőleg használatos a kapacitási egységek kiszámításához, amely viszont számlázás alatt áll.

##### <a name="compute-unit"></a>Számítási egység
A számítási egység a felhasznált számítási kapacitás mértékét számítja ki. A számítási egység felhasználását befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-Újraírási számítások és a WAF-szabályok feldolgozása. A számítási egységek által kezelhető kérelmek száma különböző feltételektől függ, például a TLS-tanúsítvány kulcsának méretétől, a kulcscsere-algoritmustól, a fejléc újraírásának és a WAF érkező kérések méretétől függően.

Számítási egységre vonatkozó útmutató:
* Standard_v2 – minden számítási egység képes körülbelül 50 kapcsolat másodpercenként az RSA 2048-bites Key TLS-tanúsítvánnyal.

* WAF_v2 – az egyes számítási egységek körülbelül 10 egyidejű kérést támogatnak másodpercenként a 70-30%-os adatforgalomhoz, 70%-nál kevesebb kérés/POST, és annál nagyobb marad. A WAF teljesítményét jelenleg nem érinti a válasz mérete.

##### <a name="instance-count"></a>Példányszám 
A Application Gateway v2 SKU-hoz tartozó erőforrások előzetes kiépítés a példányszám alapján van meghatározva. Az egyes példányok a feldolgozási képesség szempontjából legalább 10 kapacitást garantálnak. Ugyanez a példány több mint 10 kapacitást is támogat a különböző forgalmi mintákhoz a kapacitási egység paramétereinek függvényében.

Az automatikus skálázáshoz (minimum vagy maximum) beállított méretezési és határértékek manuális meghatározása a példányszámban történik. A példányok számának manuális beállítása és a példányok minimális száma az autoskálázási konfigurációban 10 kapacitási egység/példány marad le. Ezeket a fenntartott ke-ket a rendszer akkor számlázza, ha a Application Gateway a tényleges erőforrás-felhasználástól függetlenül aktív. Ha a tényleges fogyasztás átlépi a 10 kapacitási egység/példány küszöbértékét, a változó összetevőben további kapacitási egységek lesznek kiszámlázva.

A v2 SKU-ket a felhasználás alapján számítjuk fel, és két részből állnak:

* Rögzített költségek

    A Application Gateway v2/WAF v2 üzembe helyezése és a kérelmek feldolgozására való rendelkezésre állása alapján megjelenő díj. Ez biztosítja a magas rendelkezésre állást – még akkor is, ha 0 példány van lefoglalva, ha az automatikus skálázás részeként 0 értéket ad meg a példányok minimális száma mezőben. 
    
    A fix díj magában foglalja a Application Gatewayhoz csatolt nyilvános IP-címhez kapcsolódó költségeket is.

    Az egyes időpontokban futó példányok száma nem számít tényezőnek a v2 SKU-ra rögzített költségeknek. Egy Standard_V2 (vagy WAF_V2) futtatásának rögzített költségei óránként megegyeznek, függetlenül attól, hogy az adott Azure-régióban futnak-e a példányok száma.

* Kapacitási egység költségei 

    A beérkező kérések feldolgozásához igénybe vett kapacitási egységek száma alapján.  A felhasználáson alapuló költségeket óránként számítjuk ki.

Teljes költség = rögzített költségek és kapacitási egység költségei

> [!NOTE]
> A részleges óra teljes óradíjat számol fel.

Az alábbi táblázat az USA keleti régiójának díjszabása alapján mutatja be a díjakat, és csak illusztrációs célokat szolgál.

#### <a name="fixed-costs-east-us-region-pricing"></a>Rögzített költségek (az USA keleti régiójának díjszabása)

|              2-es verziójú termékváltozat             |  Költségek ($/óra)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0,246     |
|              WAF_V2             |     $0,443     |

A havi díjak becslése havi 730 órányi használaton alapul.

#### <a name="variable-costs-east-us-region-pricing"></a>Változó költségek (az USA keleti régiójának díjszabása)

|        Kapacitási egység         |  Standard_V2 ($/óra)  |  WAF_V2 ($/óra) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $0,008        |     $0,0144    |

A régiójának megfelelő díjszabási információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Kimenő adatforgalom – az Azure-adatközpontokból az Application gatewaytől kiinduló adatokra a standard szintű [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/bandwidth/)alapján kerül sor.

### <a name="example-1-a--manual-scaling"></a>1. példa (a) – manuális skálázás 
Tegyük fel, hogy kiépített egy Standard_V2 Application Gateway manuális skálázással, amely az egész hónapra 8 példányra van beállítva. Ebben az időszakban átlagosan 88,8 MB/s adatátvitelt kap.

A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

1 a CU 2,22 – Mbps átviteli sebességet tud kezelni.

88,8 MB/s = 88,8/2,22 = 40 ke 

Előre kiépített ke = 8 (példányszám) * 10 = 80 

Mivel 80 (fenntartott kapacitás) > 40 (szükséges kapacitás), nincs szükség további ke-ra. 

Rögzített ár = $0,246 * 730 (óra) = $179,58

Változó költségek = $0,008 * 8 (példány egység) * 10 (kapacitási egységek) * 730 (óra) = $467,2

Teljes költség = $179,58 + $467,2 = $646,78

![Az 1. manuális méretezés diagramja](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>1. példa (b) – manuális skálázás a kiosztott kapacitást meghaladó forgalomnál

Tegyük fel, hogy kiépített egy Standard_V2 Application Gateway manuális skálázással, amely az egész hónapra 3 példányra van beállítva. Ebben az időszakban átlagosan 88,8 MB/s adatátvitelt kap.

A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

1 a CU 2,22 Mbps átviteli sebességet tud kezelni.

88,8 Mbit/s = 88,8/2,22 = 40 szükséges. 

Előre kiépített ke = 3 (példányszám) * 10 = 30 

40 óta (a szükséges kapacitás) > 30 (fenntartott kapacitás) esetén további ke-t kell megadni.
A további felhasznált ke-azonosítók száma az egyes példányok által rendelkezésre álló szabad kapacitástól függ.

Ha a kapacitás 10 további ke-val egyenértékű, a 3 fenntartott példányon belül használható.

Rögzített ár = $0,246 * 730 (óra) = $179,58

Változó költségek = $0,008 * (3 (példány egység) * 10 (kapacitási egység) + 10 (további kapacitási egységek)) * 730 (óra) = $233,6

Teljes költség = $179,58 + $233,6 = $413,18

Ha azonban a kapacitás csak azt jelenti, hogy a 3 fenntartott példányban csak 7 további ke-t használtak fel.
Ebben a forgatókönyvben a Application Gateway erőforrás méretezés alatt áll, és lehetséges, hogy a késés vagy a kérések eldobásának növekedéséhez vezethet.

Rögzített ár = $0,246 * 730 (óra) = $179,58

Változó költségek = $0,008 * (3 (példány egység) * 10 (kapacitási egység) + 7 (további kapacitási egységek)) * 730 (óra) = $216,08

Teljes költség = $179,58 + $216,08 = $395,66


![A 2. manuális méretezés diagramja](./media/pricing/manual-scale-2.png)

> [!NOTE]
> Manuális skálázás esetén a fenntartott példányok maximális feldolgozási kapacitását meghaladó további kérések befolyásolhatják az alkalmazás rendelkezésre állását. Nagy terhelés esetén a fenntartott példányok a beérkező kérések konfigurációjától és típusától függően több mint 10 kapacitást is biztosíthatnak a feldolgozási kapacitáshoz. Ajánlott azonban a példányok számának kiépítése a forgalmi követelmények szerint.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>2. példa – WAF_V2-példány automatikus skálázással

Tegyük fel, hogy kiépített egy WAF_V2t az automatikus skálázás engedélyezésével, és a példányok minimális száma 6-ra van állítva az egész hónapban. A kérések terhelése miatt a WAF-példány felskálázásra és a 65-es kapacitású egységek használatára (az 5 kapacitású egységek kiskálázása, míg a 60 egység le lett foglalva) a teljes hónapra.
A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

A havi díjak becslése havi 730 órányi használaton alapul.

Rögzített ár = $0,443 * 730 (óra) = $323,39

Változó költségek = $0,0144 * 65 (kapacitási egységek) * 730 (óra) = $683,28

Teljes költség = $323,39 + $683,28 = $1006,67

![A 2. automatikus méretezés diagramja](./media/pricing/auto-scale-1.png)

> [!NOTE]
> A Application Gateway megfigyelt tényleges forgalom nem valószínű, hogy az ilyen állandó adatforgalom és a Application Gateway megfigyelt terhelése a tényleges használat alapján ingadozik.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>3. példa (a) – WAF_V2 példány automatikus skálázással és 0 perces méretezési konfigurációval

Tegyük fel, hogy kiépített egy WAF_V2t az automatikus skálázás engedélyezésével, és a példányok minimális száma 0-ra van állítva az egész hónapban. A kérelem terhelése a WAF minimális, de a teljes hónapra vonatkozóan óránként folyamatosan jelen van. A terhelés egy adott kapacitási egység kapacitása alá esik.
A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

A havi díjak becslése havi 730 órányi használaton alapul.

Rögzített ár = $0,443 * 730 (óra) = $323,39

Változó költségek = $0,0144 * 1 (kapacitási egységek) * 730 (óra) = $10,512

Teljes költség = $323,39 + $10,512 = $333,902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>3. példa (b) – WAF_V2 példány automatikus skálázással, 0 perces példányszámmal

Tegyük fel, hogy kiépített egy WAF_V2t az automatikus skálázás engedélyezésével, és a példányok minimális száma a teljes hónapra 0-ra van állítva. A teljes hónapra vonatkozóan azonban 0 forgalom van irányítva a WAF-példányra.
A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

Rögzített ár = $0,443 * 730 (óra) = $323,39

Változó költségek = $0,0144 * 0 (kapacitási egységek) * 730 (óra) = $0

Teljes költség = $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>3. példa (C) – WAF_V2 példány manuális skálázással 1 példányra beállítva

Tegyük fel, hogy kiépített egy WAF_V2, és beállította az egész hónapra érvényes 1 példány minimális elfogadható értékének megfelelő manuális skálázást. A teljes hónapra vonatkozóan azonban 0 forgalom van irányítva a WAF.
A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

A havi díjak becslése havi 730 órányi használaton alapul.

Rögzített ár = $0,443 * 730 (óra) = $323,39

Változó költségek = $0,0144 * 1 (példányok száma) * 10 (kapacitási egységek) * 730 (óra) = $105,12

Teljes költség = $323,39 + $105,12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>4. példa – WAF_V2 automatikus skálázással, kapacitási egység számításai

Tegyük fel, hogy kiépített egy WAF_V2t az automatikus skálázás engedélyezésével, és a példányok minimális száma a teljes hónapra 0-ra van állítva. Ez idő alatt 25 új TLS-kapcsolatot kap/s átlagosan 8,88 MB/s adatátvitelt.
A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

A havi díjak becslése havi 730 órányi használaton alapul.

Rögzített ár = $0,443 * 730 (óra) = $323,39

Változó költségek = $0,0144 * 730 (óra) * {Max (25/50, 8.88/2.22)} = $23,36 (4 kapacitási egység szükséges a következőhöz: 8,88 Mbps)

Teljes költség = $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>5. példa (a) – Standard_V2 automatikus skálázással, időalapú számításokkal

Tegyük fel, hogy kiépített egy standard_V2t az automatikus skálázás engedélyezésével, és a példányok minimális száma 0 értékre van állítva, és ez az Application Gateway 2 óráig aktív.
Az első órában olyan forgalmat kap, amely 10 kapacitási egységgel kezelhető, és a második órában olyan forgalmat kap, amely 20 kapacitást igényel a terhelés kezeléséhez.
A fent említett díjszabás alapján a Application Gateway költségeit a következőképpen számítjuk ki:

Rögzített ár = $0,246 * 2 (óra) = $0,492

Változó költségek = $0,008 * 10 (kapacitási egységek) * 1 (óra) + $0,008 * 20 (kapacitási egységek) * 1 (óra) = $0,24

Teljes költség = $0,492 + $0,24 = $0,732


## <a name="monitoring-billed-usage"></a>Számlázott használat figyelése

Megtekintheti a különböző paraméterek felhasználásának mennyiségét (számítási egység, átviteli sebesség & állandó kapcsolatok esetében), valamint a **figyelés** szakaszban a Application Gateway metrikák részeként felhasznált kapacitási egységeket is.

![A metrikák szakasz diagramja](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Hasznos mérőszámok a költségbecslés számára

* Aktuális kapacitásegységek

    A három paraméter – aktuális kapcsolatok, átviteli sebesség és számítási egység – közötti forgalom elosztásához felhasznált kapacitások száma

* Fix számlázandó kapacitásegységek

    A minimális példányszám-beállításnak megfelelően kiépített kapacitási egységek minimális száma (az egyik példány 10 kapacitási egységre van lefordítva) a Application Gateway konfigurációban.

* Becsült számlázott kapacitásegységek

    A becsült számlázott kapacitásegységek azon kapacitásegységek számát jelzik, amelyek használatát a számlázás alapul veszi. Ennek kiszámítása az aktuális kapacitásegységek (a forgalom terheléselosztásához szükséges kapacitásegységek) és a fix számlázandó kapacitásegységek (a minimálisan biztosított kapacitásegységek) közül a nagyobb érték alapján történik.

További mérőszámok, például az átviteli sebesség, a jelenlegi kapcsolatok és a számítási egységek is elérhetők a szűk keresztmetszetek megismeréséhez és a szükséges kapacitási egységek számának becsléséhez. A részletes információk [Application Gateway metrikákban](application-gateway-metrics.md) érhetők el

#### <a name="example---estimating-capacity-units-being-utilized"></a>Példa – a felhasznált kapacitási egységek becslése

**Megfigyelt metrikák:**

* Számítási egységek = 17,38
* Átviteli sebesség = 1.37 M bájt/mp-10,96 Mbps
* Aktuális kapcsolatok = 123.08 k
* Kapacitási egységek kiszámítva = Max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Megfigyelt kapacitási egységek a mérőszámokban = 49,23

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekből megtudhatja, hogyan működik a díjszabás az Azure Application Gatewayban:

* [Az Azure Application Gateway díjszabási oldala](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Azure Application Gateway díjszabás kalkulátor](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
