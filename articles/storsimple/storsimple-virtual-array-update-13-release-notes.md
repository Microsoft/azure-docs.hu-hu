---
title: Microsoft Azure StorSimple Virtual Array 1.3-as frissítésének kibocsátási megjegyzései | Microsoft Docs
description: Az 1.3-as frissítést futtató Azure StorSimple Virtual Array kritikus fontosságú nyitott problémáit és megoldását ismerteti.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389519"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>A StorSimple Virtual Array 1.3-as frissítésének kibocsátási megjegyzései

A következő kibocsátási megjegyzések a Virtual Array frissítésekkel kapcsolatos kritikus fontosságú és megoldott Microsoft Azure StorSimple azonosítják.

A kibocsátási megjegyzések folyamatosan frissülnek. Ahogy ismertté válnak a megoldást igénylő kritikus fontosságú problémák, hozzáadjuk őket a dokumentumhoz. A StorSimple Virtual Array üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 1.3-as frissítés a 10.0.10319.0 szoftververziónak felel meg.

> [!IMPORTANT]
> - Az 1.3-as frissítés kritikus fontosságú frissítés. Határozottan javasoljuk, hogy a lehető leghamarabb telepítse.
> - Az 1.3-as frissítést csak az 1.2-es frissítést futtató eszközökön telepítheti.
> - A frissítések zavart okoznak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz leáll. A frissítés alkalmazására használt csomagok részletes utasításaiért olvassa el az [1.3-as](#download-update-13)frissítés letöltését.

## <a name="whats-new-in-update-13"></a>Az 1.3-as frissítés újdonsága

Ez a frissítés a következő fejlesztéseket tartalmazza:KB4540725

- Transport Layer Security (TLS) 1.2 egy kötelező frissítés, és telepíteni kell. Ettől a kiadástól kezdve a TLS 1.2 lesz a szabványos protokoll minden Azure Portal kommunikációhoz.
  
   Ha a következő figyelmeztetés jelenik meg, a folytatás előtt frissítenie kell az eszközön található szoftvert:

   Egy vagy több StorSimple-eszköz régebbi szoftververziót futtat. A TLS 1.2 legújabb elérhető frissítése kötelező frissítés, és azonnal telepíteni kell ezekre az eszközökre. A TLS 1.2 minden Azure Portal kommunikációhoz használható, és a frissítés nélkül az eszköz nem fog tudni kommunikálni a StorSimple szolgáltatással.

- A szemétgyűjtési hibajavítások javítják a szemétgyűjtési ciklus teljesítményét, ha az eszköz és a tárfiók két távoli régióban van.
- Javítás a blobok időtúllépése miatti biztonsági mentési hibákhoz.
- Az OS/.NET-keretrendszer frissített biztonsági javításai:
  - [KB4540725:](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86)2020. március SSU (Karbantartási verem frissítése)
  - [KB4565541:](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01)2020. júliusi összesítés
  - [KB4565622:](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00)2020. július .NET-keretrendszer frissítés

## <a name="download-update-13"></a>Az 1.3-as frissítés letöltése

A frissítés letöltéséhez a Microsoft Update [katalóguskiszolgálóra,](https://www.catalog.update.microsoft.com/Home.aspx) és töltse le a KB4575898 csomagot. Ez a csomag a következő csomagokat tartalmazza. Telepítse a csomagokat ebben a sorrendben:

1. **KB4540725,** amely a 2012. márciusig összesítve elérhető Windows-frissítéseket tartalmazza a 2020. márciusig. A jelen összesítésben foglalt információkról a márciusi havi biztonsági [összesítésben található további információ.](https://support.microsoft.com/help/4540725)
1. **KB4565541,** amely a 2012 R2 2020. júliusig összesítve elérhető Windows-frissítéseket tartalmazza. Az ebben az összesítésben foglalt információkért kattintson a júliusi havi biztonsági [összesítésre.](https://support.microsoft.com/help/4565541)
1. **KB4565622**, amely cumulative.NET keretrendszer 2020. júliusi frissítéseit tartalmazza. Az ebben az összesítésben található információkért kattintson a [KB4565622. cikkre.](https://support.microsoft.com/help/4565622)<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. **KB3011067**, amely az eszköz szoftverfrissítéseit tartalmazza.

Töltse le a KB4575898 frissítést, és kövesse ezeket az utasításokat a frissítés helyi webes felhasználói felületen [keresztül történő alkalmazáshoz.](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="known-issues-in-update-13"></a>Az 1.3-as frissítés ismert problémái
Az 1.3-as frissítés nem észlelt új problémákat. Minden, a kiadással kapcsolatban feljegyzett probléma át lesz hozva a korábbi kiadásokból. Az előző kiadásokban szereplő ismert problémák összegzését az [1.2-es](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)frissítés Ismert problémák lapja tartalmazza.

## <a name="next-steps"></a>Következő lépések
Töltse le a KB4575898 frissítést, és [alkalmazza a frissítést a helyi webes felhasználói felületen.](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)

## <a name="references"></a>Hivatkozások
Régebbi kiadási megjegyzést keres? Ugrás:

- [A StorSimple Virtual Array 1.2-es frissítésének kibocsátási megjegyzései](./storsimple-virtual-array-update-12-release-notes.md)
- [A StorSimple Virtual Array 1.0-s frissítésének kibocsátási megjegyzései](./storsimple-virtual-array-update-1-release-notes.md)
- [A StorSimple Virtual Array 0.6-os frissítésének kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)
- [A StorSimple Virtual Array 0.5-ös frissítés kibocsátási megjegyzései](./storsimple-virtual-array-update-05-release-notes.md)
- [A StorSimple Virtual Array 0.4-es frissítés kibocsátási megjegyzései](./storsimple-virtual-array-update-04-release-notes.md)
- [A StorSimple Virtual Array 0.3-as frissítés kibocsátási megjegyzései](./storsimple-ova-update-03-release-notes.md)
- [A StorSimple Virtual Array 0.1-es és 0.2-es frissítésének kibocsátási megjegyzései](./storsimple-ova-update-01-release-notes.md)
- [A StorSimple Virtual Array általánosan elérhető kiadási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)