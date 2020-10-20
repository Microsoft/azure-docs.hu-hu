---
title: Rendszerkövetelmények
description: Az Azure távoli renderelés rendszerkövetelményeit sorolja fel
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 536f5e7d2fa12d1f9a9f09a31b463d2096dcad37
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207410"
---
# <a name="system-requirements"></a>Rendszerkövetelmények

> [!IMPORTANT]
> Az **Azure távoli renderelés** jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a fejezet az *Azure Remote rendering* (ARR) használatával való együttműködés minimális rendszerkövetelményeit sorolja fel.

## <a name="development-pc"></a>Fejlesztői számítógép

* Windows 10 1903-es vagy újabb verzió.
* Naprakész grafikus illesztőprogramok.
* Nem kötelező: [H265 hardveres videó-dekóder](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7), ha a távolról megjelenített tartalom helyi előnézetét szeretné használni (például Unity).

> [!IMPORTANT]
> A Windows Update nem mindig biztosítja a legújabb GPU-illesztőprogramokat, és a GPU gyártójának webhelyén találja a legújabb illesztőprogramokat:
>
> * [AMD-illesztőprogramok](https://www.amd.com/en/support)
> * [Intel-illesztőprogramok](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA-illesztőprogramok](https://www.nvidia.com/Download/index.aspx)

Az alábbi táblázat felsorolja, hogy mely GPU-k támogatják a H265 hardveres videó-dekódolást.

| GPU-gyártó | Támogatott modellek |
|-----------|:-----------|
| NVIDIA | A [lap alján](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix)keresse meg a **NVDEC támogatási mátrixot** . A GPU-nak igen értékűnek kell lennie a **H. 265 4:2:0 8 bites** oszlopban. |
| AMD | GPU-k az AMD [egyesített videó-dekóderének](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)legalább 6-os verziójával. |
| Intel | Skylake és újabb processzorok |

Annak ellenére, hogy a megfelelő H265-kodek is telepítve van, a kodek-DLL-ek biztonsági tulajdonságai a kodek inicializálási hibáit eredményezhetik. A [hibaelhárítási útmutató](../resources/troubleshoot.md#h265-codec-not-available) ismerteti a probléma megoldásának lépéseit. A DLL-probléma csak akkor fordulhat elő, ha egy asztali alkalmazásban használja a szolgáltatást, például az Unity-ban.

## <a name="devices"></a>Eszközök

Az Azure távoli renderelés jelenleg csak a 2. és a Windows asztali **HoloLens** támogatja. Lásd a [platform korlátozásait](../reference/limits.md#platform-limitations) ismertető szakaszt.

Fontos, hogy a legújabb HEVC-kodeket használja, mivel az újabb verziók jelentős mértékben javítják a késést. Az eszközön telepített verzió megkeresése:

1. Indítsa el a **Microsoft Store**.
1. Kattintson a jobb felső sarokban található **"..."** gombra.
1. Válassza **a letöltések és frissítések**lehetőséget.
1. Keresse meg az **eszköz gyártójától származó HEVC-videók**listáját. Ha ez az elem nem szerepel a frissítések területen, a legújabb verzió már telepítve van.
1. Győződjön meg arról, hogy a felsorolt kodek legalább verziójú **1.0.21821.0**rendelkezik.
1. Kattintson a **frissítések beolvasása** gombra, és várjon, amíg a telepítés megtörténik.

## <a name="network"></a>Hálózat

A jó felhasználói élmény érdekében elengedhetetlen a stabil, kis késleltetésű hálózati kapcsolatok használata.

Tekintse meg a [hálózati követelmények](../reference/network-requirements.md)dedikált fejezetét.

Hálózati problémák elhárításához tekintse meg a [hibaelhárítási útmutatót](../resources/troubleshoot.md#unstable-holograms).

### <a name="network-ports"></a>Hálózati portok

Győződjön meg arról, hogy a tűzfalak (az eszközökön belül, az útválasztók stb.) nem blokkolja a következő portokat:

| Port              | Protokoll | Engedélyezés    | Leírás |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Kimenő | Kezdeti kapcsolat (HTTP-kézfogás) |
| 8266              | UDP      | Kimenő | Adatátvitel |
| 5000, 5433, 8443  | TCP      | Kimenő | A [ArrInspector eszközhöz](../resources/tools/arr-inspector.md) szükséges|


## <a name="software"></a>Szoftverek

A következő szoftvereket kell telepíteni:

* A **Visual Studio 2019** legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio-eszközök vegyes valósághoz](/windows/mixed-reality/install-the-tools). A következő számítási *feladatok* telepítése kötelező:
  * **Asztali fejlesztés C++ nyelven**
  * **Univerzális Windows-platform (UWP) fejlesztése**
* **Windows SDK 10.0.18362.0** [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(letöltés)](https://git-scm.com/downloads)
* Nem kötelező: Ha a videó streamet szeretné megtekinteni egy asztali számítógépen lévő kiszolgálóról, szüksége lesz a **HEVC** [(Microsoft Store hivatkozás)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Győződjön meg arról, hogy a legújabb verzió van telepítve a frissítések keresése az áruházban.

## <a name="unity"></a>Unity

Az egységgel való fejlesztéshez telepítse a következőt:

* Unity 2019.3.1 [(letöltés)](https://unity3d.com/get-unity/download)
* Telepítse ezeket a modulokat az Unity-ben:
  * **UWP** – univerzális Windows-platform-Build támogatása
  * **IL2CPP** – Windows Build-támogatás (IL2CPP)

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: modell megjelenítése egységgel](../quickstarts/render-model.md)