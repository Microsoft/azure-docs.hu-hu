---
title: Az Azure bejárati ajtó standard/prémium végpontjának konfigurálása a Endpoint Managerrel
description: Ez a cikk bemutatja, hogyan konfigurálhat végpontot a Endpoint Managerrel.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098989"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Azure bejárati ajtó standard/prémium (előzetes verzió) végpontjának konfigurálása a Endpoint Managerrel

> [!NOTE]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Tekintse meg az Azure-beli előtérben lévő **[dokumentumokat](../front-door-overview.md)**.

Ebből a cikkből megtudhatja, hogyan hozhat létre végpontot egy meglévő Azure bejárati ajtó standard/prémium szintű profiljához a Endpoint Managerrel.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy létre lehessen hozni egy Azure-beli előtérben lévő standard/prémium szintű végpontot a Endpoint Managerrel, létre kell hoznia legalább egy Azure-beli előtérben létrehozott profilt. A profilnak legalább egy Azure Door standard/prémium végponttal kell rendelkeznie. Ha az Azure bejárati ajtót standard/prémium végpontokat internetes tartomány, webalkalmazás vagy más feltételek alapján szeretné szervezni, több profilt is használhat. 

Az Azure-beli bejárati profil létrehozásával kapcsolatban lásd: [új Azure-beli bejárati ajtó standard/prémium profiljának létrehozása](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Új Azure bejárati ajtó standard/prémium végpontjának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure bejárati ajtó standard/Premium profiljához.

1. Válassza a **Endpoint Manager** lehetőséget. Ezután válassza a **végpont hozzáadása** lehetőséget egy új végpont létrehozásához.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Képernyőkép a végpontok végpont-kezelőn keresztüli hozzáadásáról.":::

1. A **végpont hozzáadása** lapon adja meg a következő beállításokat, majd válassza ki az alábbi beállításokat:
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="A végpont hozzáadása lap képernyőképe.":::

    | Beállítások | Érték |
    | -------- | ----- |
    | Név | Adjon meg egy egyedi nevet az új Azure Door standard/prémium végpont számára. A rendszer ezt a nevet használja a gyorsítótárazott erőforrások eléréséhez a tartományban `<endpointname>.az01.azurefd.net` |
    | Forrás válaszának időtúllépése (mp) | Adja meg azt az időtúllépési értéket másodpercben, ameddig az Azure bejárati ajtaja meg fog várni, mielőtt a forrással létesített kapcsolat időtúllépéssel rendelkezik. |
    | Állapot | A végpont engedélyezéséhez jelölje be a jelölőnégyzetet. |

## <a name="add-domains-origin-group-routes-and-security"></a>Tartományok, származási csoportok, útvonalak és biztonság hozzáadása

1. Az útvonal konfigurálásához válassza a végpont **szerkesztése** lehetőséget a végponton.

1. A **végpont szerkesztése** lapon válassza a **+ Hozzáadás** a tartományok alatt lehetőséget.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Képernyőkép a tartomány kiválasztása elemről a végpont szerkesztése lapon.":::

### <a name="add-domain"></a>Tartomány hozzáadása

1. A **tartomány hozzáadása** lapon válassza a tartomány hozzárendelése *Az Azure bejárati profiljához* vagy *egy új tartomány hozzáadása* lehetőséget. Új tartomány létrehozásával kapcsolatos további információkért lásd: új Azure-beli előtérben lévő [standard/prémium szintű egyéni tartomány létrehozása](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Képernyőkép a tartomány hozzáadása lapról.":::

1. Válassza a **Hozzáadás** lehetőséget a tartomány aktuális végponthoz való hozzáadásához. A kiválasztott tartománynak a tartomány panelen kell megjelennie.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Képernyőkép a tartományok tartomány nézetében.":::

### <a name="add-origin-group"></a>Forrás csoport hozzáadása

1. Válassza a **Hozzáadás** lehetőséget a Origin groups nézetben. Megjelenik a **Origin csoport hozzáadása** lap 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="A Origin csoport hozzáadása lap képernyőképe":::

1. A **név** mezőben adjon meg egy egyedi nevet az új Origó csoport számára.

1. Válassza a **forrás hozzáadása** lehetőséget, ha új forrást szeretne hozzáadni az aktuális csoporthoz.
 
#### <a name="health-probes"></a>Állapot-mintavételek
A bejárati ajtó rendszeres HTTP/HTTPS mintavételi kérelmeket küld az egyes forrásoknak. A mintavételi kérelmek meghatározzák az egyes forrásokhoz tartozó közelséget és állapotot a végfelhasználói kérelmek elosztása érdekében. A származási csoport állapot-mintavételi beállításai határozzák meg, hogyan kérdezi le az alkalmazás forrásának állapotát. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

> [!WARNING]
> Mivel a bejárati ajtó számos peremhálózati környezettel rendelkezik globálisan, a forráshoz tartozó állapot-mintavételi mennyiség meglehetősen magas – 25 kérés percenként, akár 1200 kérelem percenként, a beállított állapottól függően. Az alapértelmezett, 30 másodperces mintavételi gyakorisággal a forrásban lévő mintavételi mennyiségnek percenként 200 kérelemnek kell lennie.

* **Állapot**: megadhatja, hogy be kell-e kapcsolni az állapotot. Ha a származási csoportban egyetlen forrás van, akkor letilthatja az alkalmazás-háttér terhelését csökkentő állapot-mintavételt. Még ha több is van a csoportban, de csak az egyikük engedélyezett állapotban van, letilthatja az állapot-mintavételt.
   
* **Elérési út**: az ebben a származási csoportban lévő összes forráshoz tartozó mintavételi kérelmekhez használt URL-cím. Ha például az egyik eredete a contoso-westus.azurewebsites.net, és az elérési út a/Probe/test.aspx értékre van állítva, akkor a bevezető környezetek, feltéve, hogy a protokoll HTTP-értékre van állítva, a rendszer az állapot-mintavételi kérelmeket küldi el `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protokoll**: meghatározza, hogy a rendszer elküldje-e az állapot-mintavételi kérelmeket a bejárati háztól a http-vagy HTTPS-protokollal.
   
* Mintavételi **módszer**: az állapotadatok küldéséhez használandó http-metódus. A lehetőségek közé tartozik a GET vagy a HEAD (alapértelmezett).

    > [!NOTE]
    > Az alacsonyabb terhelés és a költségek kihasználása érdekében a bejárati ajtó azt javasolja, hogy a HEAD kérelmeket használja az állapot-mintavételhez.

* **Időköz (másodpercben)**: meghatározza az állapot-mintavételek gyakoriságát a forrásra, illetve azt az intervallumot, amelyben az egyes bejárati környezetek egy mintavételt küldenek.
   
    >[!NOTE]
    >A gyorsabb feladatátvétel érdekében állítsa az intervallumot alacsonyabb értékre. Minél kisebb az érték, annál magasabb a forrástól kapott állapot mintavételi mennyisége. Ha például az intervallum 30 másodpercre van állítva, azaz a 100-es bejárati ajtó globálisan jelenik meg, az egyes háttérrendszer percenként körülbelül 200 mintavételi kérést fog kapni.

#### <a name="load-balancing"></a>Terheléselosztás
A Origin csoport terheléselosztási beállításai határozzák meg, hogyan értékeljük ki az állapotadatok kiértékelését. Ezek a beállítások határozzák meg, hogy a háttér állapota Kifogástalan-e. Azt is bemutatják, hogyan kell terheléselosztást kiosztani a különböző eredetű források között a forrás csoportban. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

- **Minta mérete** Meghatározza, hogy hány mintát kell figyelembe venni a származási állapot kiértékeléséhez.

- A **minta mérete sikeres**. Meghatározza a korábban említett minta méretét, a forrás kifogástalan meghívásához szükséges sikeres minták számát. Tegyük fel például, hogy a bejárati ajtó állapotának mintavételi intervalluma 30 másodperc, a minta mérete 5, a sikeres minta mérete pedig 3. Minden alkalommal, amikor kiértékeljük a forráshoz tartozó állapot-mintavételt, megvizsgáljuk az utolsó öt mintát 150 másodpercnél (5 x 30). Legalább három sikeres mintavételre van szükség ahhoz, hogy a hátteret Kifogástalan állapotba lehessen nyilvánítani.

- **Késleltetési érzékenység (extra késleltetés)**. Azt határozza meg, hogy a bejárati ajtón át kívánja-e küldeni a kérést a késés mértékének érzékenységi tartományán belül, vagy továbbítja a kérelmet a legközelebbi háttérbe.

A **Hozzáadás** gombra kattintva adja hozzá a forrás csoportot az aktuális végponthoz. A kezdőpontnak szerepelnie kell a forrás csoport paneljén.

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Képernyőkép az Origins csoportról.":::

### <a name="add-route"></a>Útvonal hozzáadása

Válassza a **Hozzáadás** lehetőséget az útvonalak nézetben, megjelenik az **útvonal hozzáadása** lap. A tartomány és a származási csoport összekapcsolásával kapcsolatos információkért lásd: új Azure-beli előtérben lévő [útvonal létrehozása](how-to-configure-route.md)

### <a name="add-security"></a>Biztonság hozzáadása

1. A biztonsági nézetben válassza a **Hozzáadás** lehetőséget, a **WAF házirend hozzáadása** lap jelenik meg
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Képernyőkép a WAF-házirend hozzáadása oldalról.":::

1. **WAF házirend**: válasszon ki egy olyan WAF-szabályzatot, amelyet a kijelölt tartományra alkalmazni szeretne a végponton belül. 
  
   Új WAF szabályzat létrehozásához válassza az **új létrehozása** lehetőséget.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Képernyőkép az új WAF szabályzat létrehozásáról.":::
   
    **Név**: adjon meg egy egyedi nevet az új WAF-házirendhez. A szabályzatot a webalkalmazási tűzfal oldaláról további konfigurációval szerkesztheti.

    **Tartományok**: válassza ki azt a tartományt, amelyre alkalmazni szeretné a WAF házirendet.

1. Kattintson a **Hozzáadás** gombra. A WAF szabályzatnak meg kell jelennie a biztonsági panelen

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Képernyőkép a WAF szabályzatról a biztonsági nézetben.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nem szükséges végpontot törölni, válassza a végpont végén található **végpont törlése** lehetőséget. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="A végpontok törlésének képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

Az egyéni tartományokkal kapcsolatos további tudnivalókért folytassa az [egyéni tartomány hozzáadásával](how-to-add-custom-domain.md).
