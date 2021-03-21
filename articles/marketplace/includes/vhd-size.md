---
title: fájl belefoglalása
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92284313"
---
Ha a virtuális gépek egyikét (és választható további szolgáltatásokat) előre konfigurálta, akkor már megvásárolta a standard Azure-beli virtuálisgép-méretet. Az ajánlott módszer a megoldás elindítása előre konfigurált operációs rendszerrel. Ha azonban manuálisan telepíti az operációs rendszert, az elsődleges virtuális merevlemezt a virtuálisgép-rendszerképbe kell méreteznie. Győződjön meg arról, hogy az operációsrendszer-lemez mérete a Linux vagy a Windows korlátain belül van.

| Operációs rendszer | VHD-méret |
| --- | --- |
| Linux | 30 – 1023 GB |
| Windows | 30 – 250 GB |
|

A jóváhagyott alapként megadott alapszintű Windows-vagy SQL Server-lemezképek már megfelelnek ezeknek a követelményeknek, ezért ne módosítsa a VHD formátumát vagy méretét.

Az adatlemezek mérete akár 1 TB is lehet. A méret meghatározásakor ne feledje, hogy az ügyfelek nem tudják átméretezni a lemezképen belüli virtuális merevlemezeket az üzembe helyezéskor. Hozzon létre adatlemezes virtuális merevlemezeket rögzített formátumú VHD-ként. Emellett bővíthetők (ritka/dinamikus). Az adatlemezek kezdetben üresek vagy tartalmazhatnak adatfájlokat.