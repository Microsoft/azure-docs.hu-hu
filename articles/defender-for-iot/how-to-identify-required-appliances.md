---
title: A szükséges berendezések azonosítása
description: Ismerje meg a IoT érzékelőkhöz és a helyszíni felügyeleti konzolhoz készült Certified Defender hardvereit és virtuális készülékeit.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2ad5bf08542cd98f7acae36827b1a7b284a893b0
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149298"
---
# <a name="identify-required-appliances"></a>A szükséges berendezések azonosítása

Ez a cikk a Certified Defender IoT-érzékelő készülékekről nyújt információt. A Defender Fort IoT fizikai és virtuális készülékeken is üzembe helyezhető.

Ez magában foglalja a hitelesített *előre konfigurált* berendezéseket, amelyeken már telepítve van a szoftver, valamint olyan nem konfigurált tanúsítvánnyal rendelkező készülékek, amelyeken le lehet tölteni és telepíteni kell a szükséges szoftvereket.

A cikk a helyszíni felügyeleti konzol berendezésének specifikációit is tartalmazza. A helyszíni felügyeleti konzol nem érhető el előre konfigurált készülékként.

- Ha előre konfigurált érzékelőt szeretne vásárolni, tekintse át az [érzékelő berendezések](#sensor-appliances) szakaszban elérhető modelleket, és folytassa a vásárlással.

- Ha a saját készülékét szeretné megvásárolni, tekintse át az [érzékelő készülékek](#sensor-appliances) szakaszban és a [további tanúsított készülékek](#additional-certified-appliances) szakaszban elérhető modelleket. A készülék beolvasását követően letöltheti és telepítheti a szoftvert.

- Ha a helyszíni felügyeleti konzolt szeretné megvásárolni, tekintse át a helyszíni [felügyeleti konzol berendezése](#on-premises-management-console-appliance) szakaszban található információkat. Az eszköz beolvasása után letöltheti és telepítheti a szoftvert.

Miután elvégezte a feladatokat, telepítheti a szoftvert, és beállíthatja a hálózatot.

## <a name="sensor-appliances"></a>Szenzoros készülékek

A IoT Defender a fizikai és a virtuális üzemelő példányokat egyaránt támogatja.

### <a name="physical-sensors"></a>Fizikai érzékelők

Ez a szakasz áttekintést nyújt a fizikai érzékelők elérhető modelljeiről. Előre konfigurált szoftverekkel vagy olyan érzékelőkkel vásárolhat érzékelőket, amelyek nincsenek előre konfigurálva.

| Központi telepítés típusa | Vállalat | Enterprise | SMB rack csatlakoztatása| SMB-robusztus|
|--|--|--|--|--|
| Kép | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="A vállalati szintű modell."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="A nagyvállalati szintű modell."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Az SMB-szintű modell."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="Az SMB-robusztus szintű modell."::: |
| Modellezés | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| Portok figyelése | Legfeljebb 15 RJ45 vagy 8 OPT | Legfeljebb 8 RJ45 vagy 6 OPT | 4 RJ45 | Legfeljebb 5 |
| Maximális sávszélesség [1](#anchortext) | 3 GB/mp | 1 GB/s | 200 MB/s | 100 MB/s |
| Maximálisan védett eszközök | 30 000 | 15 000 | 1,000 | 800 |

Lásd: a [készülék specifikációi](#appliance-specifications) a szállító adataihoz.

Az előre konfigurált érzékelőkkel kapcsolatban: a Microsoft az előre konfigurált érzékelők megadására szolgáló nyíllal rendelkezik. Előre konfigurált érzékelő megvásárlásához lépjen a következő címen található Contact (kapcsolat) nyílra: <hardware.sales@arrow.com>

Tudnivalók a saját berendezések létrehozásáról: az itt ismertetett támogatott modellek áttekintése. A készülék beszerzése után lépjen a **Defender for IoT**  >  **Network sensors ISO**-  >  **telepítésre** a szoftver letöltéséhez.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Hálózati érzékelők ISO.":::

<a id="anchortext">1</a> a sávszélesség-kapacitás a protokollok eloszlása alapján változhat.

### <a name="virtual-sensors"></a>Virtuális érzékelők

Ez a szakasz áttekintést nyújt az elérhető virtuális érzékelőkről.

| Központi telepítés típusa | Vállalat | Enterprise | SMB |
|--|--|--|--|
| Maximális sávszélesség | 2,5 GB/s | 800 MB/s | 160 MB/s |
| Maximálisan védett eszközök | 30 000 | 10,000 | 2500 |

## <a name="on-premises-management-console-appliance"></a>Helyszíni felügyeleti konzol berendezése

A felügyeleti konzol virtuális központi telepítésként érhető el.

| Központi telepítés típusa | Enterprise |
|--|--|
| Berendezés típusa | HPE DL20, VM |
| Felügyelt érzékelők száma | Akár 300 |

A helyszíni felügyeleti konzol megvásárlása után nyissa meg a **Defender for IoT** helyszíni  >  **felügyeleti konzoljának**  >  **ISO-telepítését** az ISO letöltéséhez.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Helyszíni felügyeleti konzol.":::

## <a name="appliance-specifications"></a>Berendezések specifikációi

Ez a szakasz a következő berendezések hardveres specifikációit ismerteti:

- Vállalati telepítés: HPE ProLiant DL360

- Vállalati telepítés: HPE ProLiant DL20

- SMB-telepítés: HPE ProLiant DL20

- Virtuális készülék specifikációi

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Vállalati telepítés: HPE ProLiant DL360

| Összetevő | Technikai specifikációk |
|--|--|
| Váz | 1U rack-kiszolgáló |
| Dimenziók | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(in) |
| Tömeg | Max. 16,27 kg (35,86 LB) |
| Processzor | Intel Xeon Silver 4215 R 3,2 GHz, 11M cache, 8c/16T, 130 W |
| Lapkakészletének | Intel C621 |
| Memória | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Tárolás | 6 x 1,2 – TB SAS 12G Enterprise 10K SFF (2,5 a-ben) Hot-Plug merevlemez – RAID 5 |
| Hálózati vezérlő | Fedélzeti: 2 x 1 GB-os Broadcom BCM5720<br>Fedélzeti LOM: iDRAC 1 – GB-os Broadcom BCM5720<br><br>Külső: 1 x Intel Ethernet i350 QP 1 GB-os kiszolgáló adapter, alacsony profil |
| Kezelés | HPE iLO – speciális |
| Eszköz-hozzáférés | Két hátsó USB 3,0<br>Egy elülső USB 2,0<br>Egy belső USB 3,0 |
| Energiaellátás | 2 x HPE 500 W Flex bővítőhely Platinum gyors elérésű, alacsony halogén tápegység-készlet |
| Rack-támogatás | HPE 1U Gen10 SFF Easy Installation Rail Kit |

### <a name="appliance-bom"></a>Készülék ANYAGJEGYZÉKe

| PN | Leírás | Mennyiség |
|--|--|--|
| P19766 – B21 | HPE DL360 Gen10 8SFF NC műszaki kiszolgáló | 1 |
| P19766 – B21 | Európa – többnyelvű honosítás | 1 |
| P24479 – L21 | Intel Xeon-S 4215 R FIO Kit for DL360 G10 | 1 |
| P24479 – B21 | Intel Xeon-S 4215 R csomag a DL360 Gen10 | 1 |
| P00922 – B21 | HPE 16 GB-2Rx8 PC4-2933Y-R intelligens csomag | 2 |
| 872479 – B21 | HPE 1,2 – TB SAS 10K SFF SC DS HDD | 6 |
| 811546 – B21 | HPE 1 – GbE 4 – p BASE-T I350 adapter | 1 |
| P02377 – B21 | HPE Smart Hybrid kondenzátor w \_ 145 mm kábel | 1 |
| 804331 – B21 | HPE Smart Array P408i – SR Gen10 vezérlő | 1 |
| 665240 – B21 | HPE 1 – GbE 4 – p FLR-T I350 adapter | 1 |
| 871244 – B21 | HPE DL360 Gen10 nagy teljesítményű ventilátor szett | 1 |
| 865408 – B21 | HPE 500 – W FS Plate – gyors elérésű, LH tápegység-készlet | 2 |
| 512485 – B21 | HPE iLO ADV 1 – Server License 1 év támogatás | 1 |
| 874543 – B21 | HPE 1U Gen10 SFF Easy Installation Rail Kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Vállalati telepítés: HPE ProLiant DL20

| Összetevő | Technikai specifikációk |
|--|--|
| Váz | 1U rack-kiszolgáló |
| Méretek (magasság x szélesség x mélység) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 hüvelyk |
| Tömeg | 7,9 kg/17.41 LB |
| Processzor | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Lapkakészletének | Intel C242 |
| Memória | 2 x 16 GB-os kettős rangsorú x8 DDR4 – 2666 |
| Tárolás | 3 x 1 – TB SATA 6G középvonal 7,2 K SFF (2,5 a-ben) – RAID 5 intelligens tömb P408i – SR-vezérlő |
| Hálózati vezérlő | A fedélzeten: 2 x 1 GB <br>A fedélzeten: iLO port kártya 1 GB <br>Külső: 1 x HPE Ethernet 1 – GB 4 – Port 366FLR adapter |
| Kezelés | HPE iLO – speciális |
| Eszköz-hozzáférés | Elöl: 1 x USB 3,0, 1 x USB iLO szolgáltatási port <br>Hátul: 2 x USB 3,0 <br>Belső: 1 x USB 3,0 |
| Energiaellátás | Kettős, gyors plug-tápegységek 500 W |
| Rack-támogatás | HPE 1U rövid súrlódású vasúti készlet |

### <a name="appliance-bom"></a>Készülék ANYAGJEGYZÉKe

| PN | Leírás: felső vége | Mennyiség |
|--|--|--|
| P06963 – B21 | HPE DL20 Gen10 4SFF műszaki igazgató kiszolgáló | 1 |
| P06963 – B21 | HPE DL20 Gen10 4SFF műszaki igazgató kiszolgáló | 1 |
| P17104 – L21 | HPE DL20 Gen10 E-2234 FIO csomag | 1 |
| 879507 – B21 | HPE 16 GB-2Rx8 PC4-2666V-E STND készlet | 2 |
| 655710 – B21 | HPE 1 – TB SATA 7,2 K SFF SC DS HDD | 3 |
| P06667 – B21 | HPE DL20 Gen10 x8x16 FLOM Felkelő csomag | 1 |
| 665240 – B21 | HPE Ethernet 1 – GB 4 – Port 366FLR adapter | 1 |
| 782961 – B21 | HPE 12 – W intelligens tároló akkumulátor | 1 |
| 869081 – B21 | HPE Smart Array P408i – SR G10 LH-vezérlő | 1 |
| 865408 – B21 | HPE 500 – W FS Plate – gyors elérésű, LH tápegység-készlet | 2 |
| 512485 – B21 | HPE iLO ADV 1 – Server License 1 év támogatás | 1 |
| P06722 – B21 | HPE DL20 Gen10 RPS FIO-készlet engedélyezése | 1 |
| 775612 – B21 | HPE 1U rövid súrlódású vasúti készlet | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB-telepítés: HPE ProLiant DL20

| Összetevő | Technikai specifikációk |
|--|--|
| Váz | 1U rack-kiszolgáló |
| Méretek (magasság x szélesség x mélység) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 hüvelyk |
| Tömeg | 7,88 kg/17.37 LB |
| Processzor | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Lapkakészletének | Intel C242 |
| Memória | 1 x 8 – GB kettős rangsorú x8 DDR4 – 2666 |
| Tárolás | 2 x 1 – TB SATA 6G középvonal 7,2 K SFF (2,5 a-ben) – RAID 1 intelligens tömb P208i-a |
| Hálózati vezérlő | A fedélzeten: 2 x 1 GB <br>A fedélzeten: iLO port kártya 1 GB <br>Külső: 1 x HPE Ethernet 1 – GB 4 – Port 366FLR adapter |
| Kezelés | HPE iLO – speciális |
| Eszköz-hozzáférés | Elöl: 1 x USB 3,0, 1 x USB iLO szolgáltatási port <br>Hátul: 2 x USB 3,0 <br>Belső: 1 x USB 3,0 |
| Energiaellátás | 290 W-os gyorscsatlakozós tápegység |
| Rack-támogatás | HPE 1U rövid súrlódású vasúti készlet |

### <a name="appliance-bom"></a>Készülék ANYAGJEGYZÉKe

| PN | Leírás | Mennyiség |
|--|--|--|
| P06961 – B21 | HPE DL20 Gen10 NHP 2LFF műszaki igazgató kiszolgáló | 1 |
| P06961 – B21 | HPE DL20 Gen10 NHP 2LFF műszaki igazgató kiszolgáló | 1 |
| P17102 – L21 | HPE DL20 Gen10 E-2224 FIO csomag | 1 |
| 879505 – B21 | HPE 8 – GB 1Rx8 PC4-2666V-E STND készlet | 1 |
| 801882 – B21 | HPE 1 – TB SATA 7,2 K LFF RW HDD | 2 |
| P06667 – B21 | HPE DL20 Gen10 x8x16 FLOM Felkelő csomag | 1 |
| 665240 – B21 | HPE Ethernet 1 – GB 4 – Port 366FLR adapter | 1 |
| 869079 – B21 | HPE Smart Array E208i – SR G10 LH-vezérlő | 1 |
| P21649 – B21 | HPE DL20 Gen10 Plat 290 W FIO PSU-készlet | 1 |
| P06683 – B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC Cable Kit | 1 |
| 512485 – B21 | HPE iLO ADV 1 – Server License 1 év támogatás | 1 |
| 775612 – B21 | HPE 1U rövid súrlódású vasúti készlet | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB robusztus: HPE Edgeline EL300

| Összetevő | Technikai specifikációk |
|--|--|
| Építőipar | Alumínium, fanless & por-ellenőrző kialakítás |
| Méretek (magasság x szélesség x mélység) | 200.5 mm (7,9 ") magas, 232mm (9,14") Wide by 100mm (3,9 ") Deep |
| Tömeg | 4,91 KG (10,83 lbs.) |
| CPU | Intel Core i7 – 8650U (1,9 GHz/4 – mag/15W) |
| Lapkakészletének | Intel® Q170 platform Controller hub |
| Memória | 8GB DDR4 2133MHz Wide hőmérséklete (nátrium) |
| Tárolás | 128GB 3ME3 Wide hőmérséklet mSATA SSD |
| Hálózati vezérlő | 6x Gigabit Ethernet-portok Intel® I219 |
| Eszköz-hozzáférés  | 4 USBs: 2 elülső oldal; 2 hátulsó; 1 belső |
| Hálózati adapter | 250V/10A |
| Szerelési | Mounting Kit, DIN sín |
| Működési hőmérséklet | 0C a + 70C  |
| Páratartalom | 10% ~ 90%, nem kondenzációs |
| Rezgés | 0,3 grms 10Hz 300Hz, 15 perc/tengely – DIN sín   |
| Sokk | 10G 10ms, félórás, három az egyes tengelyekhez. (Pozitív & negatív impulzus) – DIN sín |

### <a name="appliance-bom"></a>Készülék ANYAGJEGYZÉKe
| Termék | Leírás |
|--|--|
| P25828 – B21 | HPE Edgeline EL300 v2 konvergált Edge System |
| P25828 – B21 B19 | HPE EL300 v2 konvergált Edge System |
| P25833 – B21 | Intel Core i7-8650U (1,9 GHz/4-Core/15W) FIO alapszintű processzor-készlet HPE Edgeline EL300 |
| P09176 – B21 | HPE Edgeline 8GB (1x8GB) Dual Rank x8 DDR4-2666 nátrium-WT CAS-19-19-19 regisztrált memória FIO készlet |
| P09188 – B21 | HPE Edgeline 256GB SATA 6G Read intenzív M. 2 2242 3yr Wty Wide Temp SSD |
| P04054 – B21 | HPE Edgeline EL300 SFF – M. 2 engedélyezési csomag |
| P08120 – B21 | HPE Edgeline EL300 12VDC FIO-adatátviteli tábla |
| P08641 – B21 | HPE Edgeline EL300 80W 12VDC tápegység |
| AF564A | HPE C13-SI-32 IL 250V 10Amp 1.83 m tápkábel |
| P25835 – B21 | HPE EL300 v2 FIO Carrier Board |
| R1P49AAE | HPE EL300 izmus ADV 3yr nonstop Sup_Upd E-LTU |
| P08018 – B21 nem kötelező | HPE Edgeline EL300, alacsony profilú zárójeles készlet  |
| P08019 – B21 nem kötelező | HPE Edgeline EL300 DIN Rail Mount Kit |
| P08020 – B21 nem kötelező | HPE Edgeline EL300 fali csatlakoztatási csomag |
| P03456 – B21 nem kötelező | HPE Edgeline 1 GbE 4 – Port TSN FIO lánya kártya |

## <a name="virtual-appliance-specifications"></a>Virtuális készülék specifikációi

### <a name="sensors"></a>Érzékelők

| Típus | Vállalat | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Memória | 32 GB | 32 GB | 8 GB |
| Tárolás | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Helyszíni felügyeleti konzol berendezése

| Típus | Enterprise |
|--|--|
| Leírás | Virtuális berendezés vállalati telepítési típusokhoz |
| vCPU | 8 |
| Memória | 32 GB |
| Tárolás | 1,8 TB |

Támogatott hypervisorok: VMware ESXi 5,0-es és újabb verzió, Hyper-V

## <a name="additional-certified-appliances"></a>További tanúsított készülékek

Ez a szakasz azokat a további készülékeket részletezi, amelyeket a Microsoft tanúsított, de nem előre konfigurált készülékként kínál.

| Központi telepítés típusa | Enterprise |
|--|--|
| Kép | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Vállalati központi telepítési típus."::: |
| Modellezés | Dell PowerEdge R340 XL |
| Portok figyelése | Legfeljebb kilenc RJ45 vagy hat OPT |
| Maximális sávszélesség [1](#anchortext2)| 1 GB/s |
| Maximálisan védett eszközök | 10,000 |

<a id="anchortext2">Egy</a> A sávszélesség kapacitása a protokollok eloszlása függvényében változhat.

A készülék megvásárlása után nyissa meg a **Defender for IoT**  >  **Network sensors ISO**  >  **Installation** alkalmazást a szoftver letöltéséhez.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Hálózati érzékelők ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Vállalati telepítés: Dell PowerEdge R340 XL

| Összetevő | Technikai specifikációk |
|--|--|
| Váz | 1U rack-kiszolgáló
| Dimenziók | 42,8 x 434,0 x 596 (mm)/1,67 "x 17,09" x 23,5 "(a) |
| Tömeg | Max. 29,98 LB/13.6 kg |
| Processzor | Intel Xeon E-2144G 3,6 GHz, 8P cache, 4C/8T, Turbo (71 W) |
| Lapkakészletének | Intel C246 |
| Memória | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Tárolás | 3 x 2 – TB 7,2 K RPM SATA 6 – Gbps 512n 3,5 – Hot-Plug merevlemez – RAID 5 |
| Hálózati vezérlő | Fedélzeti: 2 x 1 GB-os Broadcom BCM5720<br>Fedélzeti LOM: iDRAC 1 – GB-os Broadcom BCM5720 <br><br>Külső: 1 x Intel Ethernet i350 QP 1 GB-os kiszolgáló adapter, alacsony profil |
| Kezelés | iDRAC kilenc vállalat |
| Eszköz-hozzáférés | Két hátsó USB 3,0 <br> Egy elülső USB 3,0 |
| Energiaellátás | Kettős, gyors plug-tápegységek 350 W |
| Rack-támogatás | ReadyRails II-es csúszó sínek az eszközre kevésbé szerelhető 4-post állványokban szögletes vagy többszálú kör alakú lyukakkal, illetve a 4 – Threading Hole rackben található, az opcionális eszköz nélküli kábelek felügyeletére szolgáló állványok támogatásával. |

## <a name="dell-r340-bom"></a>Dell R340 ANYAGJEGYZÉK

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 ANYAGJEGYZÉK.":::

## <a name="next-steps"></a>Következő lépések

[Tudnivalók az Azure Defender for IoT telepítéséről](how-to-install-software.md)

[Tudnivalók az Azure Defender for IoT Network telepítőről](how-to-set-up-your-network.md)

