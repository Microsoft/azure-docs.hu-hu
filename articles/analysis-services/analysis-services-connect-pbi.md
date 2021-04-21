---
title: Csatlakozás Azure Analysis Services Power BI | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat egy Azure Analysis Services-kiszolgálóhoz a Power BI. A csatlakozás után a felhasználók megismerhetik a modelladatokat.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 4/20/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5a430c5bb24032a2665ad078311dcb5137d2bb9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816029"
---
# <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

Miután létrehozott egy kiszolgálót az Azure-ban, és üzembe helyezett rajta egy táblázatos modellt, a szervezet felhasználói készen állnak arra, hogy csatlakozzon és megkezdjék az adatok felfedezését. 

> [!NOTE]
> Ha közzéten egy Power BI Desktop-modellt a Power BI szolgáltatás- Azure Analysis Services-kiszolgálón, győződjön meg arról, hogy az Case-Sensitive- collation server tulajdonság nincs kiválasztva (alapértelmezés). A Case-Sensitive kiszolgáló tulajdonsága a következővel SQL Server Management Studio.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Csatlakozás a Power BI Desktopban

1. A Power BI Desktop kattintson az **Azure-adatbázis**  >    >  **lekérdezése Azure Analysis Services elemre.**

2. A **Kiszolgáló alatt** adja meg a kiszolgáló nevét. Győződjön meg arról, hogy a teljes URL-címet tartalmazza; Például: asazure://westcentralus.asazure.windows.net/advworks.

3. A **Database**(Adatbázis) lapon illessze be ide a táblázatos modell adatbázisának vagy perspektívának a nevét, amelyhez csatlakozni szeretne. Ellenkező esetben ezt a mezőt üresen hagyhatja, és később kiválaszthat egy adatbázist vagy perspektívát.

4. Válasszon egy kapcsolódási lehetőséget, majd nyomja le a **Csatlakozás gombot.** 

    Az **Élő csatlakozás és** az **Importálás** lehetőség is támogatott. Javasoljuk azonban, hogy használjon élő kapcsolatokat, mivel az importálási módnak vannak korlátai; A legfontosabb, hogy az importálás során hatással lehet a kiszolgáló teljesítményére. Emellett ha a modellt frissíteni kell a Power BI szolgáltatás, a  Hozzáférés engedélyezése a Power BI beállítás csak az Élő csatlakozás **lehetőségre vonatkozik.**

5. Ha a rendszer kéri, adja meg bejelentkezési hitelesítő adatait. 

   > [!NOTE]
   > Az egyszeri PIN-kód (OTP) fiókok nem támogatottak. 

6. A **Kezelőben bontsa** ki a kiszolgálót, válassza ki azt a modellt vagy perspektívát, amelyhez csatlakozni szeretne, majd kattintson a **Csatlakozás elemre.** Kattintson egy modellre vagy perspektívára az adott nézet összes objektumának megtekintéséhez.

    A modell a Power BI Desktop jelentéssel nyílik meg a Jelentés nézetben. A Mezők listában minden nem rejtett modellobjektum megjelenik. A csatlakozás állapota a jobb alsó sarokban látható.

## <a name="connect-in-power-bi-service"></a>Csatlakozás Power BI (szolgáltatás)

1. Hozzon létre Power BI Desktop fájlt, amely élő kapcsolattal rendelkezik a modellhez a kiszolgálón.
2. A [Power BI](https://powerbi.microsoft.com)a Get Data Files **(Adatfájlok**  >  **lekérte)** elemre, majd keresse meg és válassza ki a .pbix-fájlt.

## <a name="see-also"></a>Lásd még
[Csatlakozás Azure Analysis Services](analysis-services-connect.md)   
[Ügyfélkódtárak](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
