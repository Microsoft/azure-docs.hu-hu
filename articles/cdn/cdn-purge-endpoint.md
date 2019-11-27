---
title: Azure CDN végpont kiürítése | Microsoft Docs
description: Megtudhatja, hogyan törölheti az összes gyorsítótárazott tartalmat egy Azure CDN végpontról.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: magattus
ms.openlocfilehash: 1bfbc1b730811e1111a08a957db3a747f90fb587
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546202"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Azure CDN végpont kiürítése
## <a name="overview"></a>Áttekintés
Azure CDN peremhálózati csomópontok gyorsítótárazzák az eszközöket, amíg az eszköz élettartama (TTL) lejár.  Miután az eszköz ÉLETTARTAMa lejár, amikor egy ügyfél a peremhálózati csomóponttól kéri az adategységet, a peremhálózati csomópont új, frissített példányt kér le az adatforrásból az ügyfél kérésére, és tárolja a gyorsítótár frissítését.

Az ajánlott eljárás annak biztosítására, hogy a felhasználók mindig megkapják az adategységek legújabb példányát, hogy minden egyes frissítéshez a saját eszközeiket, és azokat új URL-ként tegye közzé.  A CDN azonnal lekéri az új eszközöket a következő ügyfelek kéréseire.  Előfordulhat, hogy a gyorsítótárazott tartalmat törölni kívánja az összes peremhálózati csomópontról, és az összeset kényszeríti az új eszközök beolvasására.  Ennek oka lehet a webalkalmazás frissítései, vagy a helytelen adatokat tartalmazó eszközök gyors frissítése.

> [!TIP]
> Vegye figyelembe, hogy a végleges törlés csak a CDN peremhálózati kiszolgálókon lévő gyorsítótárazott tartalmat törli.  Az alsóbb rétegbeli gyorsítótárak, például a proxykiszolgálók és a helyi böngésző gyorsítótárai továbbra is tárolhatják a fájl gyorsítótárazott másolatát.  A fájlok élettartamának beállításakor fontos megjegyezni ezt a lehetőséget.  Egy alsóbb rétegbeli ügyfél kényszerítheti a fájl legújabb verziójának megadását úgy, hogy minden frissítéskor egyedi nevet ad, vagy a [lekérdezési karakterlánc gyorsítótárazásának](cdn-query-string.md)kihasználásával.  
> 
> 

Ez az oktatóanyag végigvezeti a végpontok összes peremhálózati csomópontjának eszközeinek kiürítésén.

## <a name="walkthrough"></a>Útmutatás
1. Az [Azure Portalon](https://portal.azure.com)keresse meg a törölni kívánt végpontot tartalmazó CDN-profilt.
2. A CDN-profil panelen kattintson a kiürítés gombra.
   
    ![CDN-profil panel](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Megnyílik a kiürítés panel.
   
    ![CDN-kiürítés panel](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. A kiürítés panelen válassza ki a törölni kívánt szolgáltatási címet az URL legördülő listából.
   
    ![Űrlap kiürítése](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > A kiürítés panelt a CDN-végpont paneljének **kiürítés** gombjára kattintva is elérheti.  Ebben az esetben az **URL** mező előre ki lesz töltve az adott végpont szolgáltatási címével.
   > 
   > 
4. Válassza ki, hogy milyen eszközöket kíván kiüríteni a peremhálózati csomópontokból.  Ha törölni kívánja az összes eszközt, kattintson az **összes kiürítés** jelölőnégyzetre.  Ellenkező esetben írja be **az elérési út szövegmezőben** az összes törölni kívánt eszköz elérési útját. Az alábbi formátumok támogatottak az elérési úton.
    1. **Egy URL-cím kiürítése**: az egyes eszközök kiürítése a teljes URL-cím megadásával, a fájlkiterjesztés vagy anélkül, például`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Helyettesítő karakteres törlés**: a csillag (\*) helyettesítő karakterként is használható. Az összes mappa, almappa és fájl végleges törlése egy `/*` az elérési úton, illetve az összes almappa és fájl kiürítése egy adott mappában, a mappa, majd a `/*`(például`/pictures/*`) alapján.  Vegye figyelembe, hogy a helyettesítő karakterek törlését a Akamai jelenleg nem támogatja Azure CDN. 
    3. **Gyökértartomány kiürítése**: Ürítse ki a végpont gyökerét az elérési úton található "/" értékkel.
   
   > [!TIP]
   > A törléshez meg kell adni az elérési utakat, és olyan relatív URL-címnek kell lennie, amely megfelel a következő [reguláris kifejezésnek](/dotnet/standard/base-types/regular-expression-language-quick-reference). Az **összes törlése** és a **helyettesítő karakteres törlés** nem támogatott **Azure CDN a Akamai** jelenleg.
   > > Egyetlen URL-cím kiürítése `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Lekérdezési karakterlánc `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Helyettesítő karakterek törlése `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > A szöveg beírása után további **elérésiút** -szövegmezők jelennek meg, amelyek lehetővé teszik több eszköz listájának összeállítását.  Az eszközöket a három pont (...) gombra kattintva törölheti a listából.
   > 
5. Kattintson a **kiürítés** gombra.
   
    ![Törlés gomb](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> A végleges törlési kérelmek körülbelül 10 percet vesznek igénybe a **Microsoft Azure CDNával**, körülbelül 2 perc **Azure CDN a verizontól** (standard és prémium), és körülbelül 10 másodpercig, a **Akamai Azure CDN**.  A Azure CDN legfeljebb 50 egyidejű kiürítési kérelmet a profil szintjén megadott időben. 
> 
> 

## <a name="see-also"></a>Lásd még
* [Eszközök előzetes betöltése Azure CDN-végponton](cdn-preload-endpoint.md)
* [Azure CDN REST API hivatkozás – végpont kiürítése vagy előzetes betöltése](/rest/api/cdn/endpoints)

