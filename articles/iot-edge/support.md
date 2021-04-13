---
title: Támogatott operációs rendszerek, Container Engine-Azure IoT Edge
description: Megtudhatja, hogy mely operációs rendszerek futtathatják a Azure IoT Edge démont és a futtatókörnyezetet, valamint a támogatott tárolókat az üzemi eszközökhöz
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b60deee6ccb95245839ee740c2b237b98315d483
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313198"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge támogatott rendszerek

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ez a cikk részletesen ismerteti, hogy mely rendszerek és összetevők támogatottak a IoT Edge, akár hivatalosan, akár előzetes verzióban.

## <a name="get-support"></a>Támogatás kérése

Ha a Azure IoT Edge szolgáltatás használata során problémákba ütközik, többféleképpen is kérhet támogatást. Próbálja ki a következő csatornák egyikét a támogatáshoz:

**Hibajelentések** – a Azure IoT Edge termékbe bekerülő fejlesztés többsége a IoT Edge nyílt forráskódú projektben történik. A hibákat a projekt [problémák lapján](https://github.com/azure/iotedge/issues) lehet jelenteni. A Windows rendszerhez készült Linux Azure IoT Edge kapcsolatos hibák a [iotedge-eFlow problémák oldalon](https://github.com/azure/iotedge-eflow/issues)olvashatók be. Gyorsan megtörténik a javítások kijavítása a projektekben a termékek frissítéseibe.

A **Microsoft ügyfélszolgálati csapata** – a [támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkező felhasználók a támogatási jegyet közvetlenül a [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)hozhatják létre a Microsoft ügyfélszolgálati csapatának.

**Szolgáltatásra vonatkozó kérelmek** – a Azure IoT Edge termék a termék [felhasználói hangoldalán](https://feedback.azure.com/forums/907045-azure-iot-edge)keresztül követi nyomon a szolgáltatásra vonatkozó kéréseket.

## <a name="container-engines"></a>Tároló-motorok

Azure IoT Edge-modulok tárolóként vannak megvalósítva, ezért IoT Edge a tároló motorjának kell elindítania azokat. A Microsoft tároló-motort biztosít a Moby-Engine számára a követelmény teljesítéséhez. Ez a tároló motor a Moby nyílt forráskódú projekten alapul. A Docker CE és a Docker EE más népszerű tároló-motor. Emellett a nyílt forráskódú Moby-projekten alapulnak, és kompatibilisek Azure IoT Edgeokkal. A Microsoft a tároló-motorokat használó rendszerek esetében nyújt legjobb támogatást. a Microsoft azonban nem tudja kiszolgálni a javításokat a bennük felmerülő problémák miatt. Ezért a Microsoft a Moby Engine használatát javasolja az éles rendszereken.

<br>
<center>

![A Moby Engine tároló futtatókörnyezete](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operációs rendszerek

Azure IoT Edge a tárolók futtatására képes legtöbb operációs rendszeren fut; azonban nem mindegyik rendszer egyformán támogatott. Az operációs rendszerek olyan rétegekbe vannak csoportosítva, amelyek a támogatási felhasználók által elvárt szintet jelölik.

* Az 1. szintű rendszerek támogatottak. Az 1. szintű rendszerek esetében a Microsoft:
  * az operációs rendszer automatikus tesztek
  * telepítési csomagokat biztosít számukra
* A 2. szintű rendszerek kompatibilisek Azure IoT Edgeekkel, és viszonylag könnyen használhatók. 2. szintű rendszerek esetén:
  * A Microsoft informális tesztelést végzett a platformokon, vagy olyan partnert ismer, amely sikeresen futtatott Azure IoT Edge a platformon
  * A más platformokhoz tartozó telepítési csomagok működhetnek ezeken a platformokon

A gazda operációs rendszer családjának mindig egyeznie kell a modul tárolójában használt vendég operációs rendszer családjának.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Ez azt jelenti, hogy a Linux-tárolókat csak Linux-és Windows-tárolókban használhatja Windows rendszeren. Windows-tárolók használata esetén csak az elkülönített tárolók feldolgozása támogatott, nem pedig a Hyper-V elkülönített tárolók.  

A Windows rendszerhez készült Linux-IoT Edge a Windows-gazdagépen futó Linux virtuális gépen IoT Edget használ. Így Linux-modulokat futtathat Windows-eszközökön.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Első réteg

A Microsoft a következő táblázatokban felsorolt rendszereket támogatja: általánosan elérhető vagy nyilvános előzetes verzióban, és minden új kiadással tesztelve van.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge támogatja a Linux vagy Windows rendszerű tárolóként létrehozott modulokat. A Linux-tárolók telepíthetők Linux rendszerű eszközökre, vagy Windows rendszerű eszközökre üzembe helyezhetők a Windows IoT Edge for Linux használatával. A Windows-tárolók csak Windows-eszközökre telepíthetők.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge a 1,2-es verzió csak a Linux-tárolóként létrehozott modulokat támogatja.

Jelenleg nem támogatott a IoT Edge 1,2-es verziójának futtatása Windows-eszközökön. A Windows rendszerhez készült [Linux-IoT Edge](iot-edge-for-linux-on-windows.md) az ajánlott módszer a IoT Edge futtatására Windows-eszközökön, de jelenleg csak IoT Edge 1,1 fut. További információkért tekintse meg a jelen cikk [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) -es verzióját.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux-tárolók

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
A Linux-tárolóként létrehozott modulok Linux vagy Windows rendszerű eszközökön is üzembe helyezhetők. Linux rendszerű eszközök esetén a IoT Edge futtatókörnyezet közvetlenül települ a gazdagép eszközén. Windows-eszközökön a IoT Edge futtatókörnyezettel előre összeépített linuxos virtuális gép fut a gazdagépen.

A Windows rendszerhez készült [Linux-IoT Edge](iot-edge-for-linux-on-windows.md) jelenleg nyilvános előzetes verzióban érhető el, de a IoT Edge Windows-eszközökön való futtatásának ajánlott módja.

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Málna PI-OS stretch |  | ![Málna PI OS stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió |
| Windows 10 Pro | Nyilvános előzetes verzió |  |  |
| Windows 10 Enterprise | Nyilvános előzetes verzió |  |  |
| Windows 10 IoT Enterprise | Nyilvános előzetes verzió |  |  |
| Windows Server 2019 | Nyilvános előzetes verzió |  |  |

Az összes Windows operációs rendszernek 1809 (Build 17763) vagy újabb verziójúnak kell lennie.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Málna PI-OS stretch |  | ![Málna PI OS stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Az Ubuntu Server 16,04 támogatása a 1,1-es IoT Edge-verzióval végződött.

#### <a name="windows-containers"></a>Windows-tárolók

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1,1 LTS az utolsó kiadási csatorna, amely támogatni fogja a Windows-tárolókat. A 1,2-es verziótól kezdődően a Windows-tárolók nem lesznek támogatottak. A Windows rendszerű eszközökön [IoT Edge Linux rendszeren](iot-edge-for-linux-on-windows.md) való használatának vagy áthelyezésének megfontolnia, hogy Windows-eszközökön fusson IoT Edge.

A Windows-tárolóként létrehozott modulok csak Windows-eszközökre telepíthetők.

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Az összes Windows operációs rendszernek 1809 (Build 17763) verziójúnak kell lennie. A Windows adott Windows-buildje szükséges a Windows IoT Edge, mert a Windows-tárolók verziószámának pontosan egyeznie kell a gazdagép Windows-eszközének verziójával. A Windows-tárolók jelenleg csak a Build 17763-et használják.

>[!NOTE]
>A Windows 10 IoT alapszintű támogatása IoT Edge 1,1-es verziójának kiadásával végződött.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1,1 LTS az utolsó kiadási csatorna, amely támogatja a Windows-tárolókat. A 1,2-es verziótól kezdődően a Windows-tárolók nem támogatottak.

A Windows-tárolók által támogatott operációs rendszerekkel kapcsolatos információkért tekintse meg a jelen cikk [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) -es verzióját.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Második réteg

Az alábbi táblázatban felsorolt rendszerek kompatibilisek Azure IoT Edge, de a Microsoft nem teszteli vagy nem tartja karban azokat.

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [A mentor Embedded Linux Flex operációs rendszer](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni operációs rendszer](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![A Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Málna PI OS Buster |  | ![Málna PI OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Málna PI OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> az ubuntu Server 18,04 telepítésének lépései a [Linux-Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge.md) során az Ubuntu 20,04 módosítása nélkül is működnek.

## <a name="releases"></a>Kiadások

A kiadási eszközök és a kibocsátási megjegyzések az [Azure-iotedge kiadások](https://github.com/Azure/azure-iotedge/releases) oldalon érhetők el. IoT Edge Ez a szakasz a kibocsátási megjegyzések információit mutatja be, így könnyebben ábrázolhatja az egyes verziók összetevőit.

A következő táblázat felsorolja az egyes kiadásokban található összetevőket az 1.2.0-től kezdődően. A táblázatban felsorolt összetevők egyenként telepíthetők vagy frissíthetők, és visszamenőleg kompatibilisek a régebbi verziókkal.

| Kiadás | aziot – Edge | edgeHub<br>edgeAgent | aziot-Identity-Service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0      | 1.2.0                | 1.2.0                  |

A következő táblázat felsorolja az egyes kiadásokban található összetevőket az 1,1 LTS-kiadásban. A táblázatban felsorolt összetevők egyenként telepíthetők vagy frissíthetők, és visszamenőleg kompatibilisek a régebbi verziókkal.

| Kiadás | iotedge | edgeHub<br>edgeAgent | libiothsm | Moby |
|--|--|--|--|--|
| **1,1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> A IoT Edge 1,1 az első hosszú távú támogatási (LTS) kiadási csatorna. Ez a verzió nem tartalmaz új funkciókat, de biztonsági frissítéseket és javításokat fog kapni a regressziók számára. IoT Edge 1,1 LTS a .NET Core 3,1-et használja, és a 2022. december 3. előtt támogatott, hogy megfeleljen a [.net Core és a .net 5 kiadási életciklusának](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

>[!IMPORTANT]
>A hosszú távú támogatási csatorna megjelenése esetén azt javasoljuk, hogy az 1.0. x verziót futtató összes aktuális ügyfél frissítse eszközét 1.1. x verzióra, hogy a folyamatos támogatást kapjon.

IoT Edge a Microsoft. Azure. Devices. Client SDK-t használja. További információkért tekintse meg az [Azure IoT C# SDK GitHub](https://github.com/Azure/azure-iot-sdk-csharp) -tárházat vagy a [.net-hez készült Azure SDK-referenciát](/dotnet/api/overview/azure/iot/client). Az alábbi lista az ügyfél SDK azon verzióját mutatja be, amelyet az egyes kiadások a következővel teszteltek:

| IoT Edge-verzió | Microsoft. Azure. Devices. Client SDK-verzió |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1,1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

A Azure IoT Edge virtuális gépeken is futtathatók. A virtuális gépek IoT Edge eszközként való használata gyakori, ha az ügyfelek a meglévő infrastruktúrát szeretnék kibővíteni a peremhálózati intelligenciával. A gazda VM operációs rendszer családjának meg kell egyeznie a modul tárolójában használt vendég operációs rendszer családjának. Ez a követelmény ugyanaz, mint amikor a Azure IoT Edge közvetlenül egy eszközön fut. Azure IoT Edge a mögöttes virtualizációs technológia, és olyan platformokon működik, mint például a Hyper-V és a vSphere.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge egy virtuális gépen](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge egy virtuális gépen](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>Minimális rendszerkövetelmények

A Azure IoT Edge nagyszerűen fut az eszközökön, mint a Pi3 a kiszolgálói minőségű hardverek számára. Ha kiválasztja a megfelelő hardvert a forgatókönyvhöz, a futtatni kívánt munkaterheléstől függ. Az eszköz végső döntése bonyolult lehet; a hagyományos laptopokon és asztali számítógépeken azonban könnyedén elindíthatja a prototípust.

A prototípus-készítés során a rendszer segít a végső eszköz kiválasztásában. Vegye figyelembe a következőket:

* Hány modul van a munkaterhelésben?
* Hány réteget osztanak meg a modulok tárolói?
* Milyen nyelven íródott modulok?
* Mennyi adattal fog dolgozni a modulok feldolgozása?
* Szükség van-e a modulok speciális hardverre a számítási feladatok felgyorsításához?
* Mik a megoldás kívánt teljesítménybeli jellemzői?
* Mi a hardveres költségkeret?
