---
title: Támogatott operációs rendszerek, tárolómotorok – Azure IoT Edge
description: Megtudhatja, mely operációs rendszerek futtatják a Azure IoT Edge démont és a futásidőt, valamint az éles eszközökhöz támogatott tárolómotorokat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67532fce2cac0ec9d05b4caa069e63014b813bd8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576346"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge támogatott rendszerek

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ez a cikk részletesen be tudatja a IoT Edge által támogatott rendszerekkel és összetevőkkel, akár hivatalosan, akár előzetes verzióban.

## <a name="get-support"></a>Támogatás kérése

Ha problémákat tapasztal a Azure IoT Edge használata során, többféleképpen is igénybe lehet kérni a támogatást. A támogatáshoz próbálja ki az alábbi csatornák egyikét:

**Hibák jelentése** – A fejlesztés nagy része, amely a Azure IoT Edge termékbe kerül, a IoT Edge nyílt forráskódú projektben történik. A hibákat a projekt [problémák oldalán](https://github.com/azure/iotedge/issues) lehet jelenteni. A Linuxon Azure IoT Edge kapcsolatos hibákról az [iotedge-eflow hibák](https://github.com/azure/iotedge-eflow/issues)oldalán lehet jelentést látni. A javítások gyorsan áttértek a projektjeiket a termékfrissítésekbe.

**Microsoft ügyfélszolgálati csapat** – Azok [a](https://azure.microsoft.com/support/plans/) felhasználók, akik támogatási csomagokkal vannak, közvetlenül a támogatási jegy létrehozásával kapcsolatba [Azure Portal.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)

**Funkciókérések** – a Azure IoT Edge termék nyomon követi a funkciókéréseket a termék [User Voice (Felhasználói hang) oldalán.](https://feedback.azure.com/forums/907045-azure-iot-edge)

## <a name="container-engines"></a>Tárolómotorok

Azure IoT Edge modulok tárolóként vannak megvalósítva, ezért IoT Edge egy tárolómotorra van szüksége az indításukhoz. A Microsoft egy moby-motorral teljesíti ezt a követelményt. Ez a tárolómotor a Moby nyílt forráskódú projekten alapul. A Docker CE és a Docker EE más népszerű tárolómotorok. Emellett a Moby nyílt forráskódú projekten alapulnak, és kompatibilisek a Azure IoT Edge. A Microsoft a lehető legjobb támogatást biztosítja az ezeket a tárolómotorokat használó rendszerekhez; A Microsoft azonban nem tud javításokat kihozni a bennük kapcsolatos problémákhoz. Ezért a Microsoft a moby-engine használatát javasolja az éles rendszereken.

<br>
<center>

![A Moby-motor mint tároló-futtatás](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operációs rendszerek

Azure IoT Edge a tárolók futtatására képes legtöbb operációs rendszeren fut; azonban nem minden rendszer egyformán támogatott. Az operációs rendszerek szintekbe vannak csoportosítva, amelyek a felhasználók által elvárt támogatási szintet képviselik.

* Az 1. rétegbeli rendszerek támogatottak. Az 1. rétegbeli rendszerek esetében a Microsoft:
  * automatizált tesztekben rendelkezik ezzel az operációs rendszerrel
  * telepítési csomagokat biztosít számukra
* A 2. rétegbeli rendszerek kompatibilisek a Azure IoT Edge, és viszonylag egyszerűen használhatók. 2. rétegbeli rendszerek esetében:
  * A Microsoft informális tesztelést végzett a platformokon, vagy tud róla, hogy egy partner sikeresen Azure IoT Edge a platformon
  * Ezeken a platformokon más platformokon is használhatók a telepítőcsomagok

A gazda operációs rendszer családnak mindig egyeznie kell a modul tárolóiban használt vendég operációs rendszer családával.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Ez azt jelenti, hogy Linux-tárolók csak Linux- és Windows-tárolókon használhatók Windows rendszeren. Windows-tárolók használata esetén csak a folyamat elkülönített tárolók támogatottak, a Hyper-V elkülönített tárolók nem.  

IoT Edge Windows rendszeren futó IoT Edge windowsos gazdagépen futó Linux rendszerű virtuális gépeken futtatja a virtuális gépeket. Így Linux-modulokat futtathat Windows-eszközön.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Első réteg

Az alábbi táblázatokban felsorolt rendszereket a Microsoft általánosan elérhető vagy nyilvános előzetes verzióban támogatja, és minden új kiadással tesztelve vannak.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge a Linux- vagy Windows-tárolókként készült modulokat támogatja. A Linux-tárolók Linux-eszközökre, illetve Windows-eszközökre is telepíthetők a Linuxhoz IoT Edge Windows rendszeren. A Windows-tárolók csak Windows-eszközökre telepíthetők.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge 1.2-es verziója csak a Linux-tárolókként készült modulokat támogatja.

Jelenleg nincs támogatott módszer az 1.2-es IoT Edge Windows-eszközökön való futtatására. [IoT Edge Windows rendszeren](iot-edge-for-linux-on-windows.md) az ajánlott módszer a windowsos IoT Edge futtatása, de jelenleg csak az 1.1-es IoT Edge fut. További információt a cikk [IoT Edge 1.1-es](?view=iotedge-2018-06&preserve-view=true) verziójában talál.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux-tárolók

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
A Linux-tárolókként készült modulok Linux- vagy Windows-eszközökön is üzembe helyezhetők. Linux-eszközök esetén a IoT Edge futtatás közvetlenül a gazdaeszközre van telepítve. Windows-eszközök esetén a linuxos virtuális gép előre fel van építve a IoT Edge futtatja a gazdaeszközön.

[IoT Edge Windows rendszeren](iot-edge-for-linux-on-windows.md) futó linuxos alkalmazások jelenleg nyilvános előzetes verzióban érhetőek el, de ez az ajánlott módszer a IoT Edge Windows-eszközökön való futtatására.

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió |
| Windows 10 Pro | Nyilvános előzetes verzió |  |  |
| Windows 10 Enterprise | Nyilvános előzetes verzió |  |  |
| Windows 10 IoT Enterprise | Nyilvános előzetes verzió |  |  |
| Windows Server 2019 | Nyilvános előzetes verzió |  |  |

Minden Windows operációs rendszernek 1809-es (17763-as build) vagy újabb verziójúnak kell lennie.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Az Ubuntu Server 16.04 támogatása a IoT Edge 1.1-es verziójával ért véget.

#### <a name="windows-containers"></a>Windows-tárolók

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS az utolsó kiadási csatorna, amely támogatja a Windows-tárolókat. Az 1.2-es verziótól kezdve a Windows-tárolók nem támogatottak. Fontolja meg a linuxos [linuxos](iot-edge-for-linux-on-windows.md) IoT Edge vagy az arra való IoT Edge Windows-eszközökön való futtatásához.

A Windows-tárolókként készült modulok csak Windows-eszközökre telepíthetők.

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1 (1. ellenőrzés)](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1 (1. ellenőrzés)](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1 (1. ellenőrzés)](./media/tutorial-c-module/green-check.png) |  |  |

Minden Windows operációs rendszernek 1809-es verziójúnak kell lennie (17763-as build). A Windows adott buildje szükséges a windowsos IoT Edge, mivel a Windows-tárolók verziójának pontosan meg kell egyeznie a gazda Windows-eszköz verziójával. A Windows-tárolók jelenleg csak az 17763-as buildet használják.

>[!NOTE]
>Windows 10 IoT Core támogatása a IoT Edge 1.1-es verziójának kiadásával ért véget.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS az utolsó kiadási csatorna, amely támogatja a Windows-tárolókat. Az 1.2-es verziótól kezdve a Windows-tárolók nem támogatottak.

A Windows-tárolók által támogatott operációs rendszerekkel kapcsolatos információkért tekintse meg a cikk [IoT Edge 1.1-es](?view=iotedge-2018-06&preserve-view=true) verziójának cikkét.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Második réteg

Az alábbi táblázatban felsorolt rendszerek kompatibilisnek minősülnek a Azure IoT Edge, de a Microsoft nem teszteli vagy tartja fenn aktívan.

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Mert |  | ![Raspberry Pi OS Fog + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Fog + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Az Ubuntu Server 18.04 telepítésének lépései a Azure IoT Edge telepítése és eltávolítása Linux rendszeren [az](how-to-install-iot-edge.md) Ubuntu 20.04-es rendszeren módosítás nélkül működnek.

## <a name="releases"></a>Kiadások

IoT Edge és kibocsátási megjegyzések az [azure-iotedge kiadások oldalán érhetők](https://github.com/Azure/azure-iotedge/releases) el. Ez a szakasz a kibocsátási megjegyzésekből származó információkat tartalmazza, így könnyebben vizualizálhatja az egyes verziók összetevőit.

Az alábbi táblázat az egyes kiadásokban található összetevőket sorolja fel az 1.2.0-s verziótól kezdődően. A táblázatban felsorolt összetevők egyenként telepíthetők vagy frissíthetők, és visszamenőlegesen kompatibilisek a régebbi verziókkal.

| Kiadás | aziot-edge | edgeHub<br>edgeAgent | aziot-identity-service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0      | 1.2.0                | 1.2.0                  |

Az alábbi táblázat az egyes kiadásokban található összetevőket sorolja fel az 1.1 LTS-kiadásban. A táblázatban felsorolt összetevők egyenként telepíthetők vagy frissíthetők, és visszamenőlegesen kompatibilisek a régebbi verziókkal.

| Kiadás | iotedge | edgeHub<br>edgeAgent | libimutsm | Moby |
|--|--|--|--|--|
| **1.1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1<br><br> | 1.1.0<br>1.1.1<br>1.1.2 | 1.1.0<br>1.1.1<br><br> |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1.1 az első hosszú távú támogatási (LTS) kiadási csatorna. Ez a verzió nem vezetett be új funkciókat, de biztonsági frissítéseket és javításokat fog kapni a regresszióhoz. IoT Edge 1.1 LTS a .NET Core 3.1-et használja, és 2022. december 3-ig támogatott lesz, hogy megfeleljen a .NET Core és a [.NET 5](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)kiadási életciklusának.

>[!IMPORTANT]
>A hosszú távú támogatási csatorna megjelenése esetén javasoljuk, hogy az 1.0.x-es verzióval futó összes jelenlegi ügyfél frissítse az eszközét az 1.1.x verzióra, hogy folyamatosan támogatást kapjon.

IoT Edge Microsoft.Azure.Devices.Client SDK-t használja. További információért tekintse meg az [Azure IoT C# SDK GitHub-adattárát](https://github.com/Azure/azure-iot-sdk-csharp) vagy az [Azure SDK for .NET referenciatartalmat.](/dotnet/api/overview/azure/iot/client) Az alábbi lista az ügyfélOLDALI SDK azon verzióját sorolja fel, amely esetében az egyes kiadásokat tesztelték:

| IoT Edge-verzió | Microsoft.Azure.Devices.Client SDK-verzió |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge virtuális gépeken is futtathatók. A virtuális gépek gyakran IoT Edge, ha az ügyfelek a meglévő infrastruktúrát szeretnék kiegészíteni peremhálózati intelligenciával. A gazdagép virtuális gép operációs rendszerének egyeznie kell a modul tárolója által használt vendég operációs rendszer családával. Ez a követelmény ugyanaz, mint amikor a Azure IoT Edge közvetlenül egy eszközön futnak. Azure IoT Edge a mögöttes virtualizálási technológia független, és olyan platformokon működő virtuális gépeken működik, mint a Hyper-V és a vSphere.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge virtuális gépen](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge virtuális gépen](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>Minimális rendszerkövetelmények

Azure IoT Edge kis eszközökön, például Raspberry Pi3-on és kiszolgálói szintű hardveren is nagyszerűen fut. A forgatókönyvnek megfelelő hardver kiválasztása a futtatni kívánt számítási feladatoktól függ. Az eszköz végső döntésének meghozatala bonyolult lehet; A hagyományos laptopokon és asztali számítógépeken azonban könnyedén elkezdheti a megoldások prototípusának kiejtését.

A prototípus-sel szerzett tapasztalat segít a végső eszközkiválasztásban. A következő kérdéseket érdemes megfontolni:

* Hány modul van a számítási feladatban?
* Hány rétegen osztoznak a modulok tárolói?
* Milyen nyelven vannak megírva a modulok?
* Mennyi adatot fog feldolgozni a modul?
* A moduljainak speciális hardverre van szükségük a számítási feladatok felgyorsítása érdekében?
* Melyek a megoldás kívánt teljesítményjellemzői?
* Mekkora a hardveres költségkeret?
