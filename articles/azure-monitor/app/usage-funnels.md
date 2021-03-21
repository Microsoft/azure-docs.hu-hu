---
title: Azure Application Insights-csatornák
description: Megtudhatja, hogyan használhatja a tölcséreket, hogy megtudja, hogyan kommunikálnak az ügyfelek az alkalmazással.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: d0e5cb5097a2f8833b10fec16a99b67c5c109fc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583464"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Ismerje meg, hogy az ügyfelek hogyan használják az alkalmazást Application Insights Tölcsérekkel

A felhasználói élmény megismerése rendkívül fontos a vállalat számára. Ha az alkalmazás több fázist is magában foglal, tudnia kell, hogy a legtöbb ügyfél a teljes folyamaton halad-e, vagy ha egy adott időpontban befejezi a folyamatot. Egy webalkalmazás lépéseinek sorozatán keresztüli előrehaladást *tölcsérnek* nevezzük. Az Azure Application Insights Tölcsérekkel betekintést nyerhet a felhasználókba, és figyelemmel kísérheti a részletes konverziós arányt. 

## <a name="create-your-funnel"></a>A tölcsér létrehozása
A tölcsér létrehozása előtt döntse el, hogy milyen kérdéssel szeretne válaszolni. Előfordulhat például, hogy tudni szeretné, hogy hány felhasználó tekinti meg a kezdőlapot, hogyan tekintheti meg az ügyfél profilját, és hogyan hozhat létre jegyet. Ebben a példában a fabrikam Fiber vállalat tulajdonosai szeretnék tudni, hogy az ügyfelek hány százalékát sikerült létrehozni az ügyfél-jegyet.

Ezek a lépések a tölcsér létrehozásához szükségesek.

1. A Application Insights tölcsérek eszközben válassza az **új** lehetőséget.
1. Az **időtartomány** legördülő menüben válassza az **utolsó 90 nap** lehetőséget. Válassza **a saját tölcsérek vagy a** **közös tölcsérek** lehetőséget.
1. Az **1. lépés** legördülő listából válassza ki az **index** elemet. 
1. A **2. lépés** listából válassza az **ügyfél** lehetőséget.
1. A **3. lépés** listából válassza a **Létrehozás** elemet.
1. Adjon hozzá egy nevet a tölcsérhez, majd kattintson a **Mentés** gombra.

Az alábbi képernyőképen egy példa látható a tölcsérek eszköz által generált adattípusra. A fabrikam tulajdonosai láthatják, hogy az elmúlt 90 napban a kezdőlapon meglátogatott ügyfeleinek 54,3 százaléka hozta létre az ügyfél-jegyet. Azt is láthatják, hogy az ügyfelek 2 700-as indexe a kezdőlapról érkezett. Ez frissítési problémát jelezhet.


![Képernyőfelvétel a tölcsérek eszközről az adattal](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>A tölcsérek funkciói
Az előző képernyőképen öt kiemelt terület szerepel. Ezek a tölcsérek funkciói. A következő lista részletesebben ismerteti a képernyőképen szereplő összes érintett régiót:
1. Ha az alkalmazás mintául szolgál, egy mintavételi szalagcím jelenik meg. A szalagcím kiválasztásával megnyílik a helyi ablaktábla, amely ismerteti a mintavételezés kikapcsolásának módját. 
2. A tölcsért [Power BIba](./export-power-bi.md)exportálhatja.
3. Válasszon egy lépést a jobb oldalon található további részletek megtekintéséhez. 
4. A korábbi konverziós gráf az elmúlt 90 napban mutatja be a konverziós arányt. 
5. A felhasználók eszköz elérésével jobban megismerheti a felhasználókat. Az egyes lépésekben szűrőket is használhat. 

## <a name="next-steps"></a>Következő lépések
  * [A használat áttekintése](usage-overview.md)
  * [Felhasználók, munkamenetek és események](usage-segmentation.md)
  * [Visszatartás](usage-retention.md)
  * [Munkafüzetek](../visualize/workbooks-overview.md)
  * [Felhasználói környezet hozzáadása](usage-send-user-context.md)
  * [Power BI-exportálás](./export-power-bi.md)

