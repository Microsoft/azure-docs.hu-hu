---
title: 'Oktatóanyag: Azure gyermek DNS-zónák létrehozása'
titleSuffix: Azure DNS
description: Oktatóanyag gyermek DNS-zónák létrehozásához a Azure Portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 04/19/2021
ms.author: jonbeck
ms.openlocfilehash: 283ff2786a0b63c6263c62a13e27cce92c2368dd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737387"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Oktatóanyag: Új gyermek DNS-zóna létrehozása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Bejelentkezés az Azure Portalra.
> * Gyermek DNS-zóna létrehozása új DNS-zónán keresztül.
> * Gyermek DNS-zóna létrehozása szülő DNS-zónán keresztül.
> * Az új gyermek DNS-zóna NS-delegálásának ellenőrzése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók.  Ha nincs fiókja, ingyenesen létrehozhat egy [fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Meglévő szülő Azure DNS zónában.  

Ebben az oktatóanyagban szülőzónaként a contoso.com, a subdomain.contoso.com tartománynévként fogjuk használni.  Cserélje *contoso.com* a szülőtartomány nevére, az *altartományt* pedig a gyermektartományra.  Ha még nem hozta létre a szülő DNS-zónát, tekintse meg a DNS-zóna létrehozásához szükséges lépéseket [a Azure Portal.](./dns-getstarted-portal.md#create-a-dns-zone) 


## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.
Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot.

A gyermek DNS-zónát kétféleképpen hozhatja létre.
1.  A "DNS-zóna létrehozása" portáloldalon keresztül.
1.  A szülő DNS-zóna konfigurációs oldalán keresztül.

## <a name="create-child-dns-zone-via-create-dns-zone"></a>Gyermek DNS-zóna létrehozása DNS-zóna létrehozása használatával

Ebben a lépésben egy új gyermek DNS-zónát hozunk létre a következő **névvel subdomain.contoso.com** és delegáljuk egy meglévő szülő DNS-zónába **contoso.com.** A DNS-zónát a DNS-zóna létrehozása lapfülek használatával **fogja** létrehozni.
1.  Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. **Megjelenik** az Új ablak.
1.  Válassza **a Hálózat lehetőséget,** majd válassza a **DNS-zóna,** majd a **Hozzáadás gombot.**

1.  Az Alapvető **beállítások lapon** írja be vagy válassza ki a következő értékeket:
    * **Előfizetés:** Válasszon ki egy előfizetést a zóna létrehozásához.
    * **Erőforráscsoport:** Adja meg a meglévő erőforráscsoportot, vagy hozzon létre egy újat az **Új létrehozása lehetőség kiválasztásával.** Írja be *a MyResourceGroup gombra,* majd kattintson az **OK gombra.** Az erőforráscsoport nevének egyedinek kell lennie az Azure-előfizetésen belül.
    * Jelölje be ezt a jelölőnégyzetet: **Ez a zóna egy** meglévő, a szolgáltatásban már üzemeltetett zóna Azure DNS
    * **Szülőzóna-előfizetés:** Ebben a legördülő menüben keresse meg vagy válassza ki azt az előfizetést, amelyben a szülő *DNS-zóna contoso.com* létre.
    * **Szülőzóna:** A keresősávba írja be a *contoso.com,* hogy betöltse a legördülő listába. A betöltés után válassza *contoso.com* a legördülő listából.
    * **Név:** Az *oktatóanyag példához írja* be az altartományt. Figyelje meg, hogy a szülő *DNS-zóna contoso.com* automatikusan utótagként lesz hozzáadva a névhez, amikor kiválasztjuk a szülőzónát a fenti lépésből.

1. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.
1. Az Áttekintés **+ létrehozás lapon** tekintse át az összefoglalást, javítsa ki az érvényesítési hibákat, majd válassza a Létrehozás **lehetőséget.**
A zóna létrehozása eltarthat néhány percig.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="A DNS-zóna létrehozása lap képernyőképe." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Gyermek DNS-zóna létrehozása a szülő DNS-zóna áttekintési oldalán
Létrehozhat egy új gyermek DNS-zónát is, és delegálhatja a szülő DNS-zónába a szülőzóna áttekintő oldalának Gyermekzóna gombjával.  Ezzel a gombbal automatikusan feltölti a gyermekzóna szülőparaméterét. 

1.  A Azure Portal a Minden erőforrás **területen** nyissa meg contoso.com DNS-zónát a  **MyResourceGroup erőforráscsoportban.** A contoso.com  **a Szűrés név** alapján mezőbe.
1.  A DNS-zóna áttekintési lapján válassza a **+Gyermekzóna gombot.**

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Képernyőkép a gyermekzóna gombról." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Ekkor megnyílik a DNS-zóna létrehozása lap. A gyermekzóna beállítás már be van jelölve, és ezen az oldalon a szülőzóna-előfizetés és a szülőzóna lesz kitöltve.
1.  Írja be a nevet *gyermekként* az oktatóanyag példához. Figyelje meg, hogy a szülő DNS-zóna contoso.com automatikusan előtagként lesz hozzáadva a névhez.
1.  Válassza **a Tovább: Címkék,** majd **a Tovább: Áttekintés + létrehozás lehetőséget.**
1.  Az Áttekintés **+ létrehozás lapon** tekintse át az összefoglalást, javítsa ki az érvényesítési hibákat, majd válassza a Létrehozás **lehetőséget.**

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="A kiválasztott gyermekzóna képernyőképe" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::

## <a name="verify-child-dns-zone"></a>Gyermek DNS-zóna ellenőrzése
Most, hogy létrehozott egy új gyermek *DNS-zónát subdomain.contoso.com* létre. Annak ellenőrzéséhez, hogy a delegálás megfelelően történt-e, ellenőrizze, hogy a gyermekzóna névkiszolgálói (NS)-rekordjai a szülőzónában vannak-e az alább leírtak szerint.  

**Gyermek DNS-zóna névkiszolgálóinak lekérése:**

1.  A Azure Portal a **Minden** erőforrás területen nyissa meg subdomain.contoso.com DNS-zónát a  **MyResourceGroup erőforráscsoportban.** A jobb *subdomain.contoso.com* **a** Szűrés név alapján mezőbe.
1.  A névkiszolgálók lekérése a DNS-zóna áttekintési oldalából. Ebben a példában a zónakiszolgálókhoz contoso.com, *ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* és *ns4-08.azure-dns.info:*

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Képernyőkép a gyermekzóna névkiszolgálóiról" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Ellenőrizze az NS-rekordot a szülő DNS-zónában:**

Ebben a lépésben a szülő  DNS-zónát contoso.com, és ellenőrizzük, hogy létrejött-e az NS-rekordhalmaz bejegyzése a gyermekzónák névkiszolgálóihoz.

1. A Azure Portal a **Minden** erőforrás területen nyissa meg contoso.com DNS-zónát a **MyResourceGroup** erőforráscsoportban. A contoso.com **a Szűrés név** alapján mezőbe.
1.  Az *contoso.com* DNS-zónák áttekintési lapján ellenőrizze, hogy vannak-e rekordhalmazok.
1.  Azt fogja találni, hogy az NS típusú rekordhalmaz és a név altartomány már létre van hozva a szülő DNS-zónában. Ellenőrizze a rekordhalmaz értékeit, amelyek hasonlóak a fenti lépésben a gyermek DNS-zónából lekért névkiszolgálók listájához.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Képernyőkép a gyermekzóna-névkiszolgálók érvényesítéséről" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, távolítsa el őket a **MyResourceGroup erőforráscsoport** törlésével. Nyissa meg **a MyResourceGroup** erőforráscsoportot, és válassza az **Erőforráscsoport törlése lehetőséget.**

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure DNS privát zónák forgatókönyvei](private-dns-scenarios.md)
