---
title: Microsoft Azure StorSimple Virtual Array Update 1,3 kibocsátási megjegyzések | Microsoft Docs
description: Ismerteti a 1,3-es frissítést futtató Azure StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: b2c2af4267140370c2aa9421a67cb3a6f413165f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657552"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array Update 1,3 kibocsátási megjegyzések

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek. Ahogy ismertté válnak a megoldást igénylő kritikus fontosságú problémák, hozzáadjuk őket a dokumentumhoz. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 1,3-es frissítés megfelel a szoftververzió 10.0.10319.0.

> [!IMPORTANT]
> - Az 1,3-es frissítés kritikus fontosságú frissítés. Javasoljuk, hogy a lehető leghamarabb telepítse.
> - Az 1,3-es frissítést csak a 1,2-es frissítést futtató eszközökön telepítheti.
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz. A frissítés alkalmazásához használt csomagokra vonatkozó részletes utasításokért keresse fel az 1,3-es [frissítés letöltését](#download-update-13)ismertető témakört.

## <a name="whats-new-in-update-13"></a>Az 1,3-es frissítés újdonságai

Ez a frissítés a következő fejlesztési funkciókat tartalmazza: KB4540725

- A Transport Layer Security (TLS) 1,2 egy kötelező frissítés, és telepíteni kell. Ettől a kiadástól kezdve a TLS 1,2 a szabványos protokoll lesz az összes Azure Portal kommunikációhoz.
  
   Ha a következő figyelmeztetés jelenik meg, a továbblépés előtt frissítenie kell az eszközön lévő szoftvereket:

   Egy vagy több StorSimple-eszköz régebbi verziójú szoftvert futtat. A TLS 1,2 legújabb elérhető frissítése kötelező frissítés, amelyet azonnal telepíteni kell ezeken az eszközökön. A TLS 1,2 minden Azure Portal kommunikációhoz használatos, és a frissítés nélkül az eszköz nem fog tudni kommunikálni az StorSimple szolgáltatással.

- A szemetet gyűjtő hibajavítások javítják a begyűjtési ciklus teljesítményét, ha az eszköz-és a Storage-fiók két távoli régióban található.
- A blob időtúllépései miatti biztonsági mentési hibák elhárítása.
- Frissített operációs rendszer/. NET-keretrendszer biztonsági javítások:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): március 2020 SSU (karbantartási verem frissítése)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): 2020. júliusi összesítés
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): július 2020 .NET-keretrendszer frissítése

## <a name="download-update-13"></a>Az 1,3-es frissítés letöltése

A frissítés letöltéséhez nyissa meg a [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) Servert, és töltse le a KB4575898 csomagot. Ez a csomag a következő csomagokat tartalmazza:

- **KB4540725**, amely összesített Windows-frissítéseket tartalmaz a 2012 R2-ig, amely akár március 2020. További információ a jelen összesítésben található adatokról: [március havi biztonsági összesítés](https://support.microsoft.com/help/4540725).
- **KB4565541**, amely összesített Windows-frissítéseket tartalmaz 2012 R2 és július 2020 között. További információ a jelen összesítésben található adatokról: [február havi biztonsági összesítés](https://support.microsoft.com/help/4565541).
- **KB4565622**, amely a Cumulative.NET-keretrendszer frissítéseit akár július 2020-ig tartalmazza. További információ a jelen összesítésben található adatokról: [február havi biztonsági összesítés](https://support.microsoft.com/help/4565622).
- **KB3011067**, amely eszköz szoftverfrissítéseket tartalmaz.

Töltse le a KB4575898, és kövesse az alábbi utasításokat a [frissítés helyi webes felületen keresztüli alkalmazásához](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Az 1,3-es frissítés ismert problémái
Az 1,3-es frissítésben nincsenek megjelent új problémák. Az összes kiadási feljegyzett probléma a korábbi kiadásokból történik. Az előző kiadásokban található ismert problémák összegzésének megtekintéséhez lépjen az [1,2-es frissítés ismert problémáira](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Következő lépések
Töltse le a KB4575898, és [alkalmazza a frissítést helyi webes felületen keresztül](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Hivatkozások
Régebbi kiadási megjegyzést keres? Ugrás:

- [StorSimple Virtual Array Update 1,2 kibocsátási megjegyzések](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1,0 kibocsátási megjegyzések](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0,6 kibocsátási megjegyzések](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0,5 kibocsátási megjegyzések](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](./storsimple-ova-update-01-release-notes.md)
- [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)