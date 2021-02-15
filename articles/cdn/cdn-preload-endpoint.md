---
title: Eszközök előzetes betöltése egy Azure CDN végponton | Microsoft Docs
description: Megtudhatja, hogyan tölthető be előre a gyorsítótárazott tartalom egy Azure Content Delivery Network-végponton. Ez a funkció a termék bizonyos verzióiban érhető el.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385047"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Eszközök előzetes betöltése Azure CDN-végponton
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Alapértelmezés szerint az eszközök csak akkor lesznek gyorsítótárazva, amikor a rendszer kéri. Mivel a peremhálózati kiszolgálók még nem gyorsítótárazták a tartalmat, és a kérést továbbítani kell a forráskiszolgálón, az egyes régiók első kérelme hosszabb időt vehet igénybe a későbbi kérelmeknél. Ennek elkerüléséhez az adategységeket előre be kell tölteni. Amellett, hogy jobb felhasználói élményt nyújt, a gyorsítótárazott eszközök előzetes betöltése csökkentheti a hálózati forgalmat a forráskiszolgálón.

> [!NOTE]
> Az eszközök előzetes betöltése olyan nagyméretű események vagy tartalmak esetében hasznos, amelyek egyszerre több felhasználó számára is elérhetők, például egy új film kiadása vagy egy szoftverfrissítés.
> 
> 

Ez az oktatóanyag végigvezeti a gyorsítótárazott tartalom előzetes betöltésén az összes Azure CDN peremhálózati csomóponton.

## <a name="to-pre-load-assets"></a>Eszközök előzetes betöltése
1. A [Azure Portal](https://portal.azure.com)keresse meg azt a CDN-profilt, amely az előre betölteni kívánt végpontot tartalmazza. Megnyílik a profil panel.
    
2. Kattintson a végpontra a listában. Megnyílik a végpont ablaktábla.
3. A CDN-végpont ablaktáblán válassza a **Betöltés** lehetőséget.
   
    ![CDN-végpont panel](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Megnyílik a **Load (Betöltés** ) ablaktábla.
   
    ![CDN betöltési panel](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. A **tartalom elérési útja** mezőbe írja be a betölteni kívánt eszközök teljes elérési útját (például: `/pictures/kitten.png` ).
   
   > [!TIP]
   > A szöveg bevitelének megkezdése után a további **tartalom elérési útja** szövegmezők lehetővé teszik, hogy több eszköz listáját is felépítse. Ha törölni szeretné az eszközöket a listából, válassza a három pontot (...), majd kattintson a **Törlés** gombra.
   > 
   > Minden tartalom elérési útjának relatív URL-címnek kell lennie, amely megfelel a következő [reguláris kifejezéseknek](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Egyetlen fájl elérési útjának betöltése: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Egyetlen fájl betöltése lekérdezési karakterlánccal: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Mivel minden eszköznek saját elérési útnak kell lennie, nincs helyettesítő karakter a betöltés előtti eszközökhöz.
   > 
   > 
   
    ![Betöltés gomb](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Ha végzett a tartalom elérési útjainak beírásával, válassza a **Betöltés** lehetőséget.
   

> [!NOTE]
> A CDN-profilok száma percenként 10 betöltési kérést és 50 egyidejű feldolgozási útvonalat is feldolgozhat. Az egyes elérési utak hossza 1024 karakter.
> 
> 

## <a name="see-also"></a>Lásd még
* [Azure CDN végpont kiürítése](cdn-purge-endpoint.md)
* [Azure CDN REST API-hivatkozás: tartalom előzetes betöltése egy végponton](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Azure CDN REST API-hivatkozás: tartalom kiürítése egy végpontból](/rest/api/cdn/cdn/endpoints/purgecontent)