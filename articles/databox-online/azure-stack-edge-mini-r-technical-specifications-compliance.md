---
title: Microsoft Azure Stack Edge mini R – technikai specifikációk és megfelelőség | Microsoft Docs
description: Ismerje meg az Azure Stack Edge mini R-eszköz műszaki specifikációit és megfelelőségét
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727481"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>A Azure Stack Edge mini R műszaki specifikációi

Az Microsoft Azure Stack Edge mini R-eszköz hardveres összetevői megfelelnek a jelen cikkben ismertetett technikai előírásoknak és szabályozási előírásoknak. A műszaki specifikációk a PROCESSZORt, a memóriát, az áramellátási egységeket, a tárolási kapacitást, a bekerítési dimenziókat és a súlyozást írják le.


## <a name="compute-memory-specifications"></a>Számítási, memória-specifikációk

Az Azure Stack Edge mini R-eszköz a következő, a számítási és a memória-specifikációkat tartalmazta:

| Specifikáció           | Érték                  |
|-------------------------|------------------------|
| CPU    | 16 Magos CPU, Intel Xeon-D 1577 |
| Memória              | 48 GB RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Számítási gyorsítási specifikációk

A Kubernetes, a mély neurális hálózat és a számítógép-alapú alkalmazásokhoz tartozó, minden Azure Stack Edge-beli mini R-eszköz tartalmaz egy vizuális feldolgozási egységet (VPU).

| Specifikáció           | Érték                  |
|-------------------------|------------------------|
| Számítási gyorsítási kártya         | Intel Movidius számtalan X VPU <br> További információ: [Intel Movidius számtalan X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Tárolási specifikációk

Az Azure Stack Edge mini R-eszköz 1 adatlemezzel és 1 rendszerindító lemezzel rendelkezik (amely operációsrendszer-tárolóként szolgál). Az alábbi táblázat az eszköz tárolókapacitásának részleteit tartalmazza.

|     Specifikáció                          |     Érték             |
|--------------------------------------------|-----------------------|
|    SSD-meghajtók száma     |    2 X 1 TB lemez <br> Egy adatlemez és egy rendszerindító lemez                  |
|    Egyetlen SSD-kapacitás                     |    1 TB               |
|    Teljes kapacitás (csak adatmennyiség)              |    1 TB              |
|    Teljes felhasználható kapacitás *                  |    ~ 750 GB        |

**Bizonyos területek belső használatra vannak fenntartva.*

## <a name="network-specifications"></a>Hálózati specifikációk

Az Azure Stack Edge mini R-eszköz a következő hálózati specifikációkkal rendelkezik:


|Specifikáció  |Érték  |
|---------|---------|
|Hálózati adapterek    |2 x 10 GbE SFP + <br> A helyi felhasználói felületen 3. és 4. PORTon látható           |
|Hálózati adapterek    |2 x 1 GbE RJ45 <br> A helyi felhasználói felületen az 1. és a 2. porton látható          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Tápegység-egységek specifikációi

Az Azure Stack Edge mini R-eszköz tartalmaz egy külső 85 W AC adaptert, amely biztosítja a teljesítményt és a bevezetési akkumulátor díját.

A következő táblázat az áramellátási egység specifikációit tartalmazza:

| Specifikáció           | Érték                      |
|-------------------------|----------------------------|
| Maximális kimeneti teljesítmény    | 85 W                       |
| Gyakoriság               | 50/60 Hz                   |
| Feszültség-tartomány kiválasztása | Automatikus hatókör: 100-240 V AC |



## <a name="included-battery"></a>Befoglalt akkumulátor

Az Azure Stack Edge mini R-eszköz tartalmaz egy, a tápegység által felszámított bevezető akkumulátort is.

A bevezető akkumulátorral együtt további 2590-es [típusú akkumulátort](https://www.bren-tronics.com/bt-70791ck.html) is használhat az eszköznek a díjak közötti kibővítéséhez. Az akkumulátornak meg kell felelnie a használati országban érvényes összes biztonsági, szállítási és környezeti előírásnak.


| Specifikáció           | Érték                      |
|-------------------------|----------------------------|
| Akkumulátor kapacitása | 73 WHr                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>A bekerítés méretei és súlyozási jellemzői

A következő táblázatok a méretek és a súlyozás különböző területekre vonatkozó specifikációit sorolja fel.

### <a name="enclosure-dimensions"></a>Bekerítés méretei

A következő táblázat az eszköz és az USP dimenzióit sorolja fel, a robusztus eset pedig milliméterben és hüvelykben.

|     Ház     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    68            |    2,68          |
|    Szélesség          |    208          |      8,19          |
|    Hossz          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Ház súlya

A következő táblázat felsorolja az eszköz súlyát, beleértve az akkumulátort is.

|     Ház                                 |     Tömeg          |
|-----------------------------------------------|---------------------|
|    Az eszköz teljes súlya     |    7 lbs.          |

## <a name="enclosure-environment-specifications"></a>Bekerítési környezet specifikációi


Ez a szakasz felsorolja a bekerítési környezettel kapcsolatos specifikációkat, például a hőmérsékletet, a páratartalmat és a magasságot.


|     Specifikációk             |     Leírás                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Hőmérséklet-tartomány          |     0 – 43 ° C (működési)                                              |
|     Rezgés                  |     MIL-STD-810 metódus 514,7 *<br> Eljárás I CAT 4, 20                  |
|     Sokk                      |     MIL-STD-810 metódus 516,7 *<br> IV. eljárás, logisztika                 |
|     Magasság                   |     Működési: 10 000 méter<br> Nem működő: 40 000 méter          |

**Minden hivatkozás a MIL-STD-810G változásra 1 (2014)*


## <a name="next-steps"></a>Következő lépések

- [Az Azure Stack Edge mini R üzembe helyezése](azure-stack-edge-placeholder.md)
