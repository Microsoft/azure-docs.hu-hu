---
title: Microsoft Azure Stack Edge Pro R műszaki specifikációk és megfelelőség | Microsoft Docs
description: Ismerje meg az Azure Stack Edge Pro R-eszköz műszaki specifikációit és megfelelőségét
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466597"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R műszaki specifikációk

Az Azure Stack Edge Pro R-eszköz hardveres összetevői megfelelnek a jelen cikkben ismertetett technikai előírásoknak. A műszaki specifikációk a tápegységek (PSUs), a tárolókapacitás, a bekerítések és a környezeti szabványok leírását írják le.


## <a name="compute-memory-specifications"></a>Számítási, memória-specifikációk

Az Azure Stack Edge Pro R-eszköz a következő specifikációkkal rendelkezik a számításhoz és a memóriához:

| Specifikáció       | Érték                  |
|---------------------|------------------------|
| CPU    | 2 X 10 mag CPU, Intel Xeon Silver 4114 |
| Memória              | 256 GB RAM (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>Számítási gyorsítási specifikációk

A grafikus processzorok (GPU) minden olyan eszközön elérhetők, amely lehetővé teszi a Kubernetes, a Deep learning és a gépi tanulási forgatókönyvek használatát.

| Specifikáció           | Érték                  |
|-------------------------|----------------------------|
| GPU   | Egy nVidia T4 GPU <br> További információ: [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 

## <a name="power-supply-unit-specifications"></a>Tápegység-egységek specifikációi

Az Azure Stack Edge Pro R-eszköz két 100-240 V-os tápegységgel (PSUs) rendelkezik, nagy teljesítményű ventilátorokkal. A két PSUs redundáns energiaellátási konfigurációt biztosít. Ha egy PSU meghibásodik, az eszköz továbbra is általában a másik PSU-gépen működik, amíg le nem cseréli a hibás modult. A következő táblázat a PSUs műszaki specifikációit sorolja fel.

| Specifikáció           | 550 W PSU                  |
|-------------------------|----------------------------|
| Maximális kimeneti teljesítmény    | 550 W                      |
| Hő elszóródása (maximum)                   | 2891 BTU/HR                |
| Gyakoriság               | 50/60 Hz                   |
| Feszültség-tartomány kiválasztása | Automatikus hatókör: 115-230 V AC |
| Gyors csatlakoztatás           | Yes                        |

## <a name="network-specifications"></a>Hálózati specifikációk

Az Azure Stack Edge Pro R-eszközhöz négy hálózati adapter tartozik, a PORT1-PORT4. 


|Specifikáció  |Description                              |
|----------------------|----------------------------------|
|Hálózati adapterek    |**2 x 1 GbE RJ45** <br> Az 1-es PORTOT a rendszer felügyeleti felületként használja a kezdeti beállításhoz, és alapértelmezés szerint statikus. A kezdeti beállítás befejeződése után bármely IP-címmel rendelkező adatkapcsolatot használhat. Alaphelyzetbe állításkor azonban a felület visszaáll a statikus IP-címekre. <br>A másik 2. PORT a felhasználó által konfigurálható, adatátvitelre is használható, és alapértelmezés szerint a DHCP.     |
|Hálózati adapterek    |**2 x 25 GbE SFP28** <br> Ezek az adatillesztők 3. és 4. PORT konfigurálható DHCP (alapértelmezett) vagy statikusként.            |

Az Azure Stack Edge Pro R-eszköz a következő hálózati hardverrel rendelkezik:

* **Mellanox Dual port 25G ConnectX-4 csatornás hálózati adapter** -3. port és 4. port. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

A hálózati kártyák által támogatott kábelek, kapcsolók és adóvevők teljes listájáért keresse fel a következőt: [Mellanox Dual port 25G ConnectX-4 Channel hálózati adapter kompatibilis termékek](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Tárolási specifikációk

Az Azure Stack Edge Pro R-eszközök 8 adatlemezzel és 2 M. 2 SATA lemezzel rendelkeznek, amelyek operációsrendszer-lemezként szolgálnak. További információért látogasson el az [M. 2 SATA-lemezekre](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>1 csomópontos eszköz tárterülete

Az alábbi táblázat az 1 csomópontos eszköz tárolási kapacitását tartalmazza.

|     Specifikáció                          |     Érték             |
|--------------------------------------------|-----------------------|
|    SSD-meghajtók száma     |    8                  |
|    Egyetlen SSD-kapacitás                     |    8 TB               |
|    Teljes kapacitás                          |    64 TB              |
|    Teljes felhasználható kapacitás *                  |    ~ 42 TB          |

**Bizonyos területek belső használatra vannak fenntartva.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>A bekerítés méretei és súlyozási jellemzői

A következő táblázatok a méretek és a súlyozás különböző területekre vonatkozó specifikációit sorolja fel.

### <a name="enclosure-dimensions"></a>Bekerítés méretei 

A következő táblázat az eszköz és a UPS dimenzióit sorolja fel a robusztus esettel milliméterben és hüvelykben.

|     Ház     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    301,2            |    11,86       |
|    Szélesség          |    604,5            |    23,80       |
|    Hossz         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Ház súlya 

Az eszköz súlya a ház konfigurációjától függ.

|     Ház                                 |     Tömeg          |
|-----------------------------------------------|---------------------|
|    1 – csomópontos eszköz + robusztus eset súlya a záró sapkával     |    ~ 114 lbs.          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Bekerítési környezet specifikációi

Ez a szakasz felsorolja a bekerítési környezettel kapcsolatos specifikációkat, például a hőmérsékletet, a vibrációt, a sokkot és a magasságot.


|     Specifikáció              |     Érték    |
|--------------------------------|-------------------------------------------------------------------|
|     Hőmérséklet-tartomány          |     0 – 43 ° C (működési)    |
|     Rezgés                  |     MIL-STD-810 metódus 514,7 *<br>Eljárás I CAT 4, 20                  |
|     Sokk                      |     MIL-STD-810 metódus 516,7 *<br>IV. eljárás, logisztika                 |
|     Magasság                   |     Működési: 10 000 méter<br>Nem működő: 40 000 méter          |

**Minden hivatkozás a MIL-STD-810G változásra 1 (2014)*

## <a name="next-steps"></a>Következő lépések

- [Az Azure Stack Edge üzembe helyezése](azure-stack-edge-placeholder.md)
