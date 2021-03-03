---
title: 'Oktatóanyag: Azure-beli alárendelt DNS-zónák létrehozása'
titleSuffix: Azure DNS
description: Útmutató a gyermek DNS-zónák létrehozásához Azure Portalban.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 1e2eddd821bb7a9d2050913efef3d73b406e32f7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733210"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Oktatóanyag: új gyermek DNS-zóna létrehozása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Bejelentkezés az Azure Portalra.
> * Gyermek DNS-zóna létrehozása új DNS-zónán keresztül.
> * Gyermek DNS-zóna létrehozása a szülő DNS-zónán keresztül.
> * Az új alárendelt DNS-zónához tartozó NS-delegálás ellenőrzése.



## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók.  Ha nem rendelkezik fiókkal, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Meglévő szülő Azure DNS zóna.  

Ebben az oktatóanyagban a contoso.com fogja használni a szülőtartomány neveként és a subdomain.contoso.com.  Cserélje le az *contoso.com* -t a szülőtartomány nevére és *altartományára* a gyermektartomány tartományával.  Ha nem hozta létre a szülő DNS-zónáját, tekintse meg a [DNS-zóna a Azure Portal használatával történő létrehozásának](./dns-getstarted-portal.md#create-a-dns-zone)lépéseit. 


## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.
Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot.

Kétféle módon hozhatja létre a gyermek DNS-zónáját.
1.  A "DNS-zóna létrehozása" portálon.
1.  A szülő DNS-zóna konfigurációs oldalán.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Gyermek DNS-zóna létrehozása DNS-zóna létrehozása révén

Ebben a lépésben létrehozunk egy új, **subdomain.contoso.com** nevű gyermek DNS-zónát, és delegáljuk a meglévő szülő DNS-zóna **contoso.com**. A DNS-zónát a **DNS-zóna létrehozása** lap lapfülei segítségével hozza létre.
1.  Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Megjelenik az **új** ablak.
1.  Válassza a **hálózatkezelés**, majd a **DNS-zóna** lehetőséget, majd kattintson a **Hozzáadás** gombra.

1.  Az **alapvető beállítások** lapon írja be vagy válassza ki a következő értékeket:
    * **Előfizetés**: válassza ki azt az előfizetést, amelyben létre kívánja hozni a zónát.
    * **Erőforráscsoport**: írja be a meglévő erőforráscsoportot, vagy hozzon létre egy újat az **új létrehozása** elem kiválasztásával, írja be a *MyResourceGroup*, majd kattintson **az OK gombra**. Az erőforráscsoport nevének egyedinek kell lennie az Azure-előfizetésen belül.
    * Jelölje be ezt a jelölőnégyzetet: **Ez a zóna egy már meglévő, Azure DNS-ban üzemeltetett zóna gyermeke** .
    * **Szülő zóna előfizetése**: ebből a legördülő listából keresse meg és/vagy válassza ki azt az előfizetési nevet, amely alatt a SZÜLŐ DNS-zóna *contoso.com* létrejött.
    * **Szülő zóna**: a keresősáv *contoso.com* betöltéséhez írja be a legördülő listában. A betöltést követően válassza a *contoso.com* lehetőséget a legördülő listából.
    * **Név:** Írja be a példa *altartományt* ehhez az oktatóanyaghoz. Figyelje meg, hogy a szülő DNS-zóna neve *contoso.com* automatikusan utótagként lesz hozzáadva, ha a fenti lépésből kiválasztjuk a fölérendelt zónát.

1. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.
1. A **felülvizsgálat + létrehozás** lapon tekintse át az összegzést, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás** lehetőséget.
A zóna létrehozása eltarthat néhány percig.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="A DNS-zóna létrehozása lap képernyőképe." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Gyermek DNS-zóna létrehozása a szülő DNS-zóna Áttekintés oldalán
Létrehozhat egy új alárendelt DNS-zónát is, és delegálhatja azt a szülő DNS-zónába a szülő zóna áttekintés lapján található **gyermek zóna** gomb használatával. Ennek a gombnak a használata automatikusan előre kitölti a szülői paramétereket az alárendelt zónához. 

1.  A Azure Portal az **összes erőforrás** területen nyissa meg a *contoso.com* DNS-zónát a **MyResourceGroup** erőforráscsoporthoz. Megadhatja a *contoso.com* a **szűrés név szerint** mezőben, hogy könnyebben megtalálja.
1.  A DNS-zónák áttekintése lapon válassza a **+ gyermek zóna** gombot.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Képernyőfelvétel – alárendelt zóna gombja" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Ekkor megnyílik a DNS-zóna létrehozása lap. A gyermek zóna beállítás már be van jelölve, és a szülő zóna előfizetése és a fölérendelt zóna már fel van töltve ezen a lapon.
1.  Adja meg a nevet *gyermekként* ebben az oktatóanyagban. Figyelje meg, hogy a szülő DNS-zóna neve contoso.com automatikusan előtagként lesz hozzáadva a névben.
1.  Válassza a **Next (tovább** ) gombot, majd a **következőt: felülvizsgálat + létrehozás**.
1.  A **felülvizsgálat + létrehozás** lapon tekintse át az összegzést, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Képernyőkép a kijelölt gyermek zónáról" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::

## <a name="verify-child-dns-zone"></a>Gyermek DNS-zóna ellenőrzése
Most, hogy létrehozta a *subdomain.contoso.com* új alárendelt DNS-zónáját. Annak ellenőrzéséhez, hogy a delegálás megfelelően történt-e, ellenőrizze, hogy a alárendelt zónához tartozó névkiszolgáló-(NS-) rekordok szerepelnek-e a szülő zónában az alább leírtak szerint.  

**A gyermek DNS-zóna névkiszolgálók beolvasása:**

1.  A Azure Portal az **összes erőforrás** területen nyissa meg a *subdomain.contoso.com* DNS-zónát a **MyResourceGroup** erőforráscsoporthoz. Megadhatja a *subdomain.contoso.com* a **szűrés név szerint** mezőben, hogy könnyebben megtalálja.
1.  Kérje le a névszervereket a DNS-zóna áttekintő oldaláról. Ebben a példában a contoso.com a *ns1-08.Azure-DNS.com, a ns2-08.Azure-DNS.net, a ns3-08.Azure-DNS.org* és a *ns4-08.Azure-DNS.info* névvel rendelkező kiszolgálók vannak hozzárendelve:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="A gyermek zóna névszerverek képernyőképe" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Ellenőrizze az NS-rekordot a szülő DNS-zónában:**

Most ebben a lépésben a szülő DNS-zónát *contoso.com* , és megvizsgáljuk, hogy létrejött-e az NS Record set bejegyzése a gyermek zónákhoz tartozó névszerverek számára.

1. A Azure Portal az **összes erőforrás** területen nyissa meg a contoso.com DNS-zónát a **MyResourceGroup** erőforráscsoporthoz. Megadhatja a contoso.com a **szűrés név szerint** mezőben, hogy könnyebben megtalálja.
1.  A *contoso.com* DNS-zónák áttekintése lapon keresse meg a rekordhalmazokat.
1.  Az NS és a name altartomány típusú rekordtípusok már a szülő DNS-zónában lettek létrehozva. Keresse meg a rekord értékeit a fenti lépésben a gyermek DNS-zónából beolvasott névszerverek listájához hasonló módon.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Képernyőkép a gyermek-zónához tartozó névszerverek ellenőrzéséről" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, távolítsa el őket a **MyResourceGroup** -erőforráscsoport törlésével. Nyissa meg a **MyResourceGroup** erőforráscsoportot, majd válassza az **erőforráscsoport törlése** elemet.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure DNS privát zónák forgatókönyvei](private-dns-scenarios.md)