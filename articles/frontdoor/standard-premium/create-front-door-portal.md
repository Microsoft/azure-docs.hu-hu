---
title: 'Gyors útmutató: Azure-beli bejárati ajtó standard/prémium szintű profiljának létrehozása – Azure Portal'
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure bejárati ajtó standard/Premium szolgáltatása a magas rendelkezésre állású és nagy teljesítményű globális webalkalmazásokhoz a Azure Portal használatával.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 175fb82a5fdf300915f89c3d8cdc238638a742e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565129"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Gyors útmutató: Azure-beli bejárati ajtó standard/prémium szintű profiljának létrehozása – Azure Portal

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Tekintse meg az Azure-beli előtérben lévő [dokumentumokat](../front-door-overview.md).

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure-beli előtérben szabványos/prémium szintű profilt a Azure Portal használatával. Az Azure bejárati ajtót standard/prémium profilt az alapkonfigurációk *gyors létrehozásával* , illetve a fejlettebb *konfigurációk használatával* hozhatja létre. *Egyéni létrehozással* két Web Apps üzembe helyezése. Ezután hozza létre az Azure bejárati ajtót a standard/Premium profilt a forrásként szolgáló két Web Apps használatával. Ezután ellenőrizheti a Web Apps kapcsolódását az Azure bejárati standard/Premium előtér-gazdagép használatával.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Első ajtó profiljának létrehozása – gyors létrehozás

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Kezdőlap vagy az Azure menüben válassza az **+ erőforrás létrehozása** lehetőséget. Keressen a *bejárati ajtó standard/prémium (előzetes verzió)* kifejezésre. Ezután kattintson a **Létrehozás** elemre.

1. Az **ajánlatok összehasonlítása** lapon válassza a **gyors létrehozás** lehetőséget. Ezután válassza **a Folytatás lehetőséget a bejárati ajtó létrehozásához**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Képernyőkép az ajánlatok összehasonlításáról.":::

1. Az előtérben lévő **profil létrehozása** lapon adja meg vagy válassza ki a következő beállításokat.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Képernyőkép a bevezető ajtó gyors létrehozási oldaláról.":::    

    | Beállítások | Érték |
    | --- | --- |
    | **Előfizetés**  | Válassza ki előfizetését. |
    | **Erőforráscsoport**  | Válassza az **új létrehozása** lehetőséget, és írja be a *contoso-appservice* szöveget a szövegmezőbe.|
    | **Név** | Adjon nevet a profilnak. Ez a példa a **contoso-AFD-quickcreate** használja. |
    | **Szint** | Válassza a standard vagy a prémium SKU lehetőséget. A standard SKU a tartalom továbbítására optimalizált. A prémium SKU a standard SKU-ra épül, és a biztonságra összpontosít. Lásd a [rétegek összehasonlítását](tier-comparison.md).  |
    | **Végpont neve** | Adjon meg egy globálisan egyedi nevet a végpont számára. |
    | **Forrás típusa** | Válassza ki az erőforrás típusát a forráshoz. Ebben a példában egy olyan app Service-t választunk ki, amelyben a saját hivatkozás engedélyezve van. |
    | **Forrás állomásneve** | Adja meg a forráshoz tartozó állomásnevet. |
    | **Privát hivatkozás engedélyezése** | Ha privát kapcsolattal szeretne rendelkezni az Azure bejárati ajtaja és a forrás között. További részletekért tekintse meg a [Private link útmutatót](concept-private-link.md) , és [engedélyezze a privát hivatkozásokat](./how-to-enable-private-link-web-app.md).
    | **Gyorsítótárazás** | Jelölje be a jelölőnégyzetet, ha a tartalmakat globálisan szeretné gyorsítótárazni a felhasználók számára az Azure bejárati ajtó peremhálózati pop-i és Microsoft Network használatával. |
    | **WAF szabályzat** | Válassza az **új létrehozása** lehetőséget, vagy válasszon egy meglévő WAF-szabályzatot a legördülő listából, ha engedélyezni szeretné ezt a funkciót. |

    > [!NOTE]
    > Az Azure bejárati ajtó standard/prémium profiljának létrehozásakor ki kell választania egy forrást ugyanabból az előfizetésből, amelyből a bejárati ajtót létrehozza.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, hogy beolvassa a bejárati ajtót a profilt.

   > [!NOTE]
   > Eltarthat néhány percig, amíg a konfigurációk propagálva lesznek az összes Edge-pop-ra.

1. Ezután kattintson a **Létrehozás** gombra, hogy beolvassa és futtassa az előtérben lévő profilt.

1. Ha engedélyezte a privát hivatkozást, lépjen a forráshoz (ebben a példában az App Service-ben). Válassza a **hálózatkezelés**  >  **saját hivatkozás beállítása** lehetőséget. Ezután válassza ki a függőben lévő kérést az Azure bejárati ajtaján, majd kattintson a jóváhagyás gombra. Néhány másodperc elteltével az alkalmazás biztonságos módon elérhető lesz az Azure bejárati Ajtóján.

## <a name="create-front-door-profile---custom-create"></a>Bejárati ajtó profiljának létrehozása – egyéni létrehozás

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Hozzon létre egy webalkalmazást két példányban, a forrásként

Ha már van konfigurálva a forrás vagy a forrás csoport, ugorjon az alkalmazáshoz tartozó bejárati ajtó standard/prémium (előzetes verzió) elemre.

Ebben a példában egy webalkalmazást hozunk létre, amely két példányban fut, amelyek különböző Azure-régiókban futnak. Mind a webalkalmazás-példányok *aktív/aktív* módban futnak, így akár egy forgalom is elvégezhető. Ez a konfiguráció különbözik az *aktív/készenléti* konfigurációtól, ahol az egyik feladatátvételként működik.

Ha még nem rendelkezik webalkalmazással, a következő lépésekkel hozhat létre egy példaként szolgáló webalkalmazást.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **WebApp** elemet.

1. A **Webalkalmazás létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat.

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | **Előfizetés**               | Válassza ki előfizetését. |
    | **Erőforráscsoport**       | Válassza az **új létrehozása** elemet, és írja be a *FrontDoorQS_rg1* a szövegmezőbe.|
    | **Név**                   | Adjon egyedi **nevet** a webalkalmazásnak. Ez a példa a *WebAppContoso-001* protokollt használja. |
    | **Közzététel** | Válassza a **Kód** lehetőséget. |
    | **Futtatókörnyezet verme**         | Válassza a **.net Core 2,1 (LTS)** elemet. |
    | **Operációs rendszer**          | Válassza a **Windows** lehetőséget. |
    | **Régió**           | Válassza az **USA középső** régiója lehetőséget. |
    | **Windows-csomag** | Válassza az **új létrehozása** elemet, és írja be a *myAppServicePlanCentralUS* szöveget a szövegmezőbe. |
    | **Termékváltozat és méret** | Válassza **a standard S1 100 Total ACU, 1,75-GB memóriát**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Az előtérben elérhető prémium SKU gyors létrehozása a Azure Portalban":::

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át az összegzést, majd kattintson a **Létrehozás** gombra. Több percet is igénybe vehet, ha üzembe helyezi a

A telepítés befejezése után hozzon létre egy második webalkalmazást. Ugyanazokat a beállításokat használja, mint a fentiek, a következő beállítások kivételével:

| Beállítás          | Érték     |
| ---              | ---  |
| **Erőforráscsoport**   | Válassza az **új létrehozása** elemet, és adja meg a *FrontDoorQS_rg2*. |
| **Név**             | Adja meg a webalkalmazás egyedi nevét, ebben a példában a *WebAppContoso-002* nevet.  |
| **Régió**           | Egy másik régió, ebben a példában az *USA déli középső* régiója |
| **App Service terv**  >  **Windows-csomag**         | Válassza az **új** lehetőséget, és adja meg a *myAppServicePlanSouthCentralUS*, majd kattintson **az OK gombra**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Hozzon létre egy standard/prémium (előzetes verzió) előtérben az alkalmazáshoz

Konfigurálja az Azure bejárati ajtó standard/Premium (előzetes verzió) szolgáltatását a felhasználói forgalom közvetlen elérésére a két webalkalmazás-kiszolgáló közötti legalacsonyabb késés alapján. A webalkalmazási tűzfallal is biztonságossá teheti az előtérben. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
 
1. A Kezdőlap vagy az Azure menüben válassza az **+ erőforrás létrehozása** lehetőséget. Keressen a *bejárati ajtó standard/prémium (előzetes verzió)* kifejezésre. Ezután kattintson a **Létrehozás** elemre.

1. Az **ajánlatok összehasonlítása** lapon válassza az **Egyéni létrehozás** lehetőséget. Ezután válassza **a Folytatás lehetőséget a bejárati ajtó létrehozásához**.

1. Az **alapvető beállítások**   lapon adja meg vagy válassza ki a következő adatokat, majd válassza a **Tovább: titkos** elemet. 

    | Beállítás | Érték |
    | --- | --- |
    | **Előfizetés** | Válassza ki előfizetését. |
    | **Erőforráscsoport** | Válassza az **új létrehozása** elemet, és írja be a *FrontDoorQS_rg0* a szövegmezőbe. |
    | **Erőforráscsoport helye** | Válassza ki az **USA keleti** régióját |
    | **Profil neve** | Adjon meg egy egyedi nevet ebben az előfizetésben **WebApp-contoso-AFD** |
    | **Szint** | Válassza a **prémium** lehetőséget. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Bejárati ajtó profiljának létrehozása":::

1. Nem *kötelező*: **titkok**. Ha felügyelt tanúsítványok használatát tervezi, ez a lépés nem kötelező. Ha van olyan meglévő Key Vault az Azure-ban, amelyet az egyéni tartományhoz tartozó saját tanúsítvány használatára tervez használni, válassza **a tanúsítvány hozzáadása** lehetőséget. A létrehozás után a felügyeleti élményben is hozzáadhat tanúsítványokat.

    > [!NOTE]
    > A tanúsítvány Azure Key Vault felhasználóként való hozzáadásához megfelelő jogosultsággal kell rendelkeznie. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Képernyőkép a titkos kód hozzáadása egyéni létrehozásához.":::

1. A **végpont** lapon válassza a **végpont hozzáadása** lehetőséget, és adjon meg egy globálisan egyedi nevet a végpontnak. A létrehozási élmény befejezése után több végpontot is létrehozhat az Azure bejárati ajtó standard/prémium profiljába. Ez a példa a *contoso-frontend felületet* használja. A kiinduló válasz időtúllépése (másodpercben) és az alapértelmezett állapot. A végpont hozzáadásához válassza a **Hozzáadás** lehetőséget.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Képernyőkép a végpontok hozzáadásáról.":::

1. Ezután adjon hozzá egy, a két webalkalmazást tartalmazó Origin csoportot. Válassza a **+ Hozzáadás** elemet   a **Origin csoport hozzáadása** lap megnyitásához. A név mezőbe írja be a *MyOrignGroup* nevet, majd válassza a **+ forrás hozzáadása** elemet.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Képernyőkép a Origin csoport hozzáadásáról.":::

1. A **forrás hozzáadása**   lapon adja meg vagy válassza ki az alábbi adatokat. Ezután válassza a **Hozzáadás** elemet.

    | Beállítás | Érték |
    | --- | --- |
    | **Név** | **Webapp1** megadása |
    | **Forrás típusa** | **App Services** kiválasztása |
    | **Állomásnév** | A következők szerint válasszon: `WebAppContoso-001.azurewebsites.net` |
    | **Forrás állomásfejléce** | A következők szerint válasszon: `WebAppContoso-001.azurewebsites.net` |
    | **Egyéb mezők** | Hagyja meg az összes többi mezőt alapértelmezettként. |

    > [!NOTE]
    > Egy Azure-beli bejárati standard/prémium profil létrehozásakor ki kell választania egy forrást ugyanabból az előfizetésből, az Azure bejárati ajtót (standard/prémium) pedig a ben hozza létre.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Képernyőkép a további eredetek hozzáadásáról.":::

1. Ismételje meg a 8. lépést a második forrás webapp002 hozzáadásához. Válassza ki `webappcontoso-002.azurewebsite.net` a **forrásként szolgáló állomásnév** és a **forrás gazdagép fejlécét**.

1. A **Origin csoport hozzáadása** lapon két hozzáadott forrás jelenik meg, az összes többi mező alapértelmezett értékének megadása.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Képernyőkép a Origin csoport hozzáadása oldalról.":::

1. Ezután adjon hozzá egy útvonalat, amely leképezi a előtér-végpontot a forrás csoportba. Ez az útvonal továbbítja a kéréseket a végponttól a myOriginGroup. Az útvonal konfigurálásához válassza a **+ Hozzáadás** útvonalon lehetőséget.  

1. Az **útvonal hozzáadása** lapon adja meg vagy válassza ki az alábbi adatokat. Ezután válassza a **Hozzáadás** elemet.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Útvonal hozzáadása gyorsítótárazás nélkül":::

    | Beállítás | Érték |
    | --- | --- |
    | **Név** | **Jelölést** megadása |    
    | **Tartomány** | A következők szerint válasszon: `contoso-frontend.z01.azurefd.net` | 
    | **Állomásnév** | A következők szerint válasszon: `WebAppContoso-001.azurewebsites.net` |
    | **Egyeztetendő minták** | Hagyja meg az alapértelmezett beállítást. |
    | **Elfogadott protokollok** | Hagyja meg az alapértelmezett beállítást. | 
    | **Átirányítás** | Hagyja meg az alapértelmezett beállítást a **HTTPS-t használó összes forgalom átirányításához**. | 
    | **Forrás csoport** | Válassza a **MyOriginGroup** lehetőséget. | 
    | **Forrás elérési útvonala** | Hagyja meg az alapértelmezett beállítást. | 
    | **Továbbító protokoll** | Válassza a **bejövő kérelem egyeztetése** lehetőséget. | 
    | **Gyorsítótárazás** | Ebben a rövid útmutatóban ne törölje a jelölést. Ha szeretné, hogy a tartalom a szélein legyen gyorsítótárazva, jelölje be a **gyorsítótárazás engedélyezése** jelölőnégyzetet. |
    | **Szabályok** | Hagyja meg az alapértelmezett beállítást. Miután létrehozta az előtérben lévő profilt, létrehozhat egyéni szabályokat, és alkalmazhatja őket az útvonalakra. |  
       
    >[!WARNING]
    > **Győződjön** meg arról, hogy az egyes végpontok útvonala van. Egy útvonal hiánya miatt a végpont sikertelen lehet.

1. A WAF-szabályzat hozzáadásához válassza a **+ Hozzáadás** a biztonsághoz lehetőséget. Válassza az új **hozzáadása** lehetőséget, és adjon meg egy egyedi nevet a házirendnek. Jelölje be a bot- **védelem hozzáadásának** jelölőnégyzetét. Válassza ki a végpontot a **tartományok** területen, majd válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="WAF szabályzat hozzáadása":::

1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget. Eltarthat néhány percig, amíg a konfigurációk propagálva lesznek az összes Edge-pop-ra. Most már megkapta az első bejárati ajtót és a végpontot.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Egyéni létrehozás áttekintése":::

## <a name="verify-azure-front-door"></a>Azure-beli bejárati ajtó ellenőrzése

Az Azure bejárati ajtó standard/Premium profiljának létrehozásakor a konfigurációt a rendszer globálisan üzembe helyezése néhány percet vesz igénybe. Ha elkészült, elérheti a létrehozott előtér-gazdagépet. A böngészőben nyissa meg a következőt: `contoso-frontend.z01.azurefd.net` . A rendszer automatikusan átirányítja a kérést a legközelebbi kiszolgálóra a forrás csoport megadott kiszolgálóiról.

Ha létrehozta ezeket az alkalmazásokat ebben a rövid útmutatóban, megjelenik egy információs oldal.

Az azonnali globális feladatátvétel teszteléséhez a következő lépéseket fogjuk használni:

1. Nyisson meg egy böngészőt a fentiekben leírtak szerint, és nyissa meg a frontend címe: `contoso-frontend.azurefd.net` .

1. A Azure Portal keresse meg és válassza ki az *app Services* elemet. Görgessen le, és keresse meg a **WebAppContoso-001** webalkalmazások egyikét ebben a példában.

1. Válassza ki a webalkalmazást, majd kattintson a **Leállítás**, majd az **Igen** gombra az ellenőrzéshez.

1. Frissítse a böngészőjét. Ugyanezt az információs oldalt kell látnia.

   >[!TIP]
   >Ezekhez a műveletekhez kevés a késés. Előfordulhat, hogy újra kell frissítenie.

1. Keresse meg a másik webalkalmazást, és állítsa le is.

1. Frissítse a böngészőjét. Ekkor egy hibaüzenet jelenik meg.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="A webalkalmazás mindkét példánya leállt":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután elkészült, eltávolíthatja az összes létrehozott elemet. Egy erőforráscsoport törlésekor a tartalma is törlődik. Ha nem kívánja ezt a bejárati ajtót használni, távolítsa el az erőforrásokat a szükségtelen költségek elkerülése érdekében.

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportot**, vagy válassza az **erőforráscsoportok** lehetőséget a Azure Portal menüből.

1. Szűrje vagy görgessen lefelé, és keresse meg az erőforráscsoportot, például **FrontDoorQS_rg0**.

1. Válassza ki az erőforráscsoportot, majd válassza az **erőforráscsoport törlése** elemet.

   >[!WARNING]
   >Ez a művelet Irreversable.

1. Írja be az erőforráscsoport nevét az ellenőrzéshez, majd válassza a **Törlés** lehetőséget.

Ismételje meg a másik két csoport eljárását.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan adhat hozzá egyéni tartományt az előtérben.
> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](how-to-add-custom-domain.md)