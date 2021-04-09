---
title: Bevezető Azure Event Grid partnerként Azure Portal használatával
description: Azure Event Grid-partner bevezetéséhez használja a Azure Portal.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050952"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Bevezetést Azure Event Grid partnerként a Azure Portal használatával
Ez a cikk Event Grid azt ismerteti, hogy a harmadik féltől származó SaaS-szolgáltatók, más néven esemény-közzétevők vagy partnerek hogyan tudják közzétenni az eseményeket a szolgáltatásokból, és hogyan használják ezeket az eseményeket a végfelhasználók számára.

> [!IMPORTANT]
> Ha nem ismeri a partneri eseményeket, tekintse meg a [partneri események áttekintése](partner-events-overview.md) című cikket, amely részletesen ismerteti az ebben a cikkben ismertetett lépéseket.

## <a name="onboarding-process-for-event-publishers-partners"></a>Bevezetési folyamat esemény-közzétevők számára (partnerek)
Dióhéjban a szolgáltatás által a felhasználók által használt események használatának engedélyezése általában az alábbi folyamattal jár:

1. A következő lépések elvégzése előtt **tájékoztassa érdeklődését** abban, hogy partnere legyen a Event Grid szolgáltatás csapatának.
1. Hozzon létre egy partneri témakört a **regisztráció** létrehozásával. 
1. Hozzon létre egy **névteret**.
1. Hozzon létre egy **esemény-csatornát** és egy **partneri témakört** (egyetlen lépés).
1. Tesztelje a partneri események funkcióit teljes körűen.

A #4 lépésnél el kell döntenie, hogy milyen típusú felhasználói élményt szeretne biztosítani. Az alábbi lehetőségek állnak rendelkezésére:
- Az SDK-val és/vagy a REST API használatával a saját tartományában található webes grafikus felhasználói felület (GUI) segítségével saját megoldást biztosíthat, amely az Event Channel és a kapcsolódó partneri témakör létrehozásához szükséges. Ezzel a beállítással megkérheti a felhasználótól az előfizetés és az erőforráscsoport körét, amelyben létre fog hozni egy partneri témakört.
- Az Event Channel és a társított partner témakör létrehozásához használja a Azure Portal vagy a CLI-t. Ha ezt a lehetőséget választja, akkor a felhasználó Azure-előfizetésének valamilyen módon és erőforráscsoporthoz kell beszereznie, és létre kell hoznia egy partneri témakört. 

Ez a cikk bemutatja, hogyan végezheti el a bevezetést Azure Event Grid partnerként a Azure Portal használatával. 

> [!NOTE]
> A partneri témakör típusának regisztrálása választható lépés. Ha szeretné eldönteni, hogy létre kell-e hoznia egy partneri témakört, tekintse meg az [esemény-közzétevő által kezelt erőforrások](partner-events-overview.md#resources-managed-by-event-publishers)című szakaszt

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Tájékoztassa érdeklődését a partnernek
Töltse ki [ezt az űrlapot](https://aka.ms/gridpartnerform) , és lépjen kapcsolatba a Event Grid csapatával a következő címen: [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . A partneri események használati eseteivel, a Personával, a bevezetési folyamattal, a funkcionalitással és a díjszabással kapcsolatos részletes információkat biztosítunk.

## <a name="prerequisites"></a>Előfeltételek
A további lépések végrehajtásához ellenőrizze, hogy rendelkezik-e a következő lépésekkel:

- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- Egy Azure- [bérlő](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="register-a-partner-topic-type-optional"></a>Partneri témakör típusának regisztrálása (nem kötelező)
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali navigációs ablaktáblán válassza a **minden szolgáltatás** lehetőséget, majd írja be **Event Grid partner regisztrációját** a keresősávba, és jelölje ki. 
1. Az **Event Grid partner regisztrációi** lapon válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Partner regisztrációs hivatkozásának hozzáadása":::
1. A **partneri témakör típusa regisztrációk – alapismeretek** lapon adja meg a következő adatokat: 
    1. A **Project Details (projekt részletei** ) szakaszban kövesse az alábbi lépéseket:
        1. Válassza ki az Azure- **előfizetését**. 
        1. Válasszon ki egy meglévő Azure- **erőforráscsoportot** , vagy hozzon létre egy új erőforráscsoportot. 
    1. A **regisztráció részletei** szakaszban kövesse az alábbi lépéseket:
        1. A **regisztráció neve** mezőben adja meg a regisztráció nevét. 
        1. A **szervezet neve** mezőben adja meg a szervezet nevét. 
    1. A **partner erőforrástípus** szakaszban adja meg a **partneri témakör létrehozás** lapján megjelenő erőforrástípus adatait: 
        1. A **partner erőforrástípus neve** mezőben adja meg az erőforrástípus nevét. Ez lesz az Azure-előfizetésében létrehozandó partneri témakör típusa. 
        2. A **megjelenítendő név** mezőben adja meg a partneri témakör (erőforrás) típusú felhasználóbarát megjelenítendő nevet. 
        3. Adja meg **az erőforrástípus leírását**. 
        4. Adja meg a **forgatókönyv leírását**. Meg kell magyarázni azokat a módszereket vagy forgatókönyveket, amelyekben az erőforrásokhoz kapcsolódó partneri témaköröket fel lehet használni.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Partner regisztrációjának létrehozása":::            
1. Válassza a **Next (tovább): egyéni szolgáltatás** elemet az oldal alján. A **partner regisztrálása** lap **ügyfél-szolgáltatás** lapján adja meg azokat az adatokat, amelyeket az előfizető felhasználók az esemény forrásával kapcsolatos probléma esetén felvesznek Önnel:
    1. Adja meg a **telefonszámot**.
    1. Adja meg a telefonszám **kiterjesztését** .
    1. Adja meg a támogatási webhely **URL-címét**. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Partner regisztrációjának létrehozása – Customer Service":::        
1. Kattintson a **Next (tovább) gombra** a lap alján. 
1. A **címkék** lapon adja meg a következő értékeket. 
    1. Adja meg a hozzáadni kívánt címke **nevét** és **értékét** . Ez a lépés **nem kötelező**. 
    1. A regisztráció létrehozásához kattintson a lap alján található **felülvizsgálat + létrehozás** gombra.

## <a name="create-a-partner-namespace"></a>Partneri névtér létrehozása

1. A Azure Portal a bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget, majd írja be **Event Grid partner névterek** kifejezést a keresősávba, majd válassza ki a listából. 
1. Az **Event Grid partner névterek** lapon válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Partneri névterek – hivatkozás hozzáadása":::
1. A **partneri névtér létrehozása – alapok** lapon válassza a következő adatokat.
    1. A **Project Details (projekt részletei** ) szakaszban hajtsa végre a következő lépéseket: 
        1. Válasszon ki egy Azure- **előfizetést**.
        1. Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egy erőforráscsoportot. 
    1. A **névtér részletei** szakaszban hajtsa végre a következő lépéseket:
        1. Adja meg a névtér **nevét** . 
        1. Válassza ki a névtér **helyét** . 
    1. A **regisztráció részletei** szakaszban a következő lépésekkel társítsa a névteret egy partner-regisztrációhoz. 
        1. Válassza ki azt az **előfizetést** , amelyben a partner regisztrálása található. 
        1. Válassza ki azt az **erőforráscsoportot** , amely tartalmazza a partner regisztrációját. 
        1. Válassza ki a **partner regisztrációját** a legördülő listából.
    1. Kattintson a **Next (tovább) gombra** a lap alján.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Partneri névtér létrehozása – alapismeretek lap":::
1. A **címkék** lapon adja hozzá a címkéket (nem kötelező).
    1. Adja meg a hozzáadni kívánt címke **nevét** és **értékét** . Ez a lépés **nem kötelező**.
    1. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre.         
1. A **felülvizsgálat + létrehozás** oldalon tekintse át a részleteket, és válassza a **Létrehozás** lehetőséget. 

## <a name="create-an-event-channel"></a>Esemény-csatorna létrehozása
> [!IMPORTANT]
> A felhasználótól egy Azure-előfizetést, egy erőforráscsoportot, egy helyet és egy partneri témakört kell kérnie, hogy hozzon létre egy partneri témakört, amelyet a felhasználó a saját maga és a felügyelete alatt fog kezelni.

1. Nyissa meg a létrehozott névtér **Áttekintés** lapját. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Partneri névtér – áttekintő lap":::
    partner-namespace-overview.png
1. Válassza az **+ esemény-csatorna** lehetőséget az eszköztáron. 
1. Az **esemény-csatorna létrehozása – alapok** lapon határozza meg a következő információkat. 
    1. A **Channel Details (csatorna részletei** ) szakaszban hajtsa végre a következő lépéseket:
        1. Az **esemény-csatorna neve** mezőben adja meg az esemény csatornájának nevét. 
        1. Adja meg a **forrást**. Tekintse meg a [Felhőbeli események 1,0 specifikációit](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) a forrás megfelelő értékének megismeréséhez. Tekintse meg [a felhőalapú események sémájának példáját](cloud-event-schema.md#sample-event-using-cloudevents-schema)is.
    1. A **cél részletei** szakaszban adja meg a cél partnerhez tartozó témakör részleteit, amelyek az adott esemény csatornához lesznek létrehozva. 
        1. Adja meg annak az **előfizetésnek az azonosítóját** , amelyben a partneri témakört létre kívánja hozni. 
        1. Adja meg annak **az erőforráscsoportnak a nevét** , amelyben a partneri témakör erőforrását létre kívánja hozni. 
        1. Adja meg a **partner témakör nevét**. 
    1. Válassza a **Tovább: szűrők** elemet az oldal alján. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Event Channel létrehozása – alapismeretek lap":::
1. A **szűrők** lapon adjon hozzá szűrőket. Tegye az alábbiakat:
    1. Szűrés az egyes események attribútumain. Csak az összes szűrőnek megfelelő események érkeznek meg. Akár 25 szűrőt is megadhat. Az összehasonlítások kis-és nagybetűk megkülönböztetését jelentik. A szűrőkhöz használt érvényes kulcsok az esemény sémája alapján változnak. A következő példában a, a, `eventid` `source` és a `eventtype` `eventtypeversioin` kulcsokat használhatja. Az adattartalomban egyéni tulajdonságokat is használhat, az `.` as a beágyazási operátor használatával. Például: `data` , `data.key` , `data.key1.key2` .
    1. Válassza a **Tovább: további funkciók** elemet a lap alján. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Event Channel – szűrők lap létrehozása":::
        create-event-channel-filters-page.png
1. A **További szolgáltatások** lapon megadhatja **a partner témakör** **lejárati idejét** és leírását. 
    1. A **lejárati idő** az az idő, amikor a témakört és a kapcsolódó esemény csatornáját automatikusan törli a rendszer, ha az ügyfél nem aktiválja. Ha nincs megadva idő, a rendszer alapértelmezés szerint hét napot használ. Jelölje be a jelölőnégyzetet a saját lejárati idejének megadásához. 
    1. Mivel ez a témakör olyan erőforrás, amelyet a felhasználó nem hozott létre, a **Leírás** segíthet a felhasználónak a témakör természetének megismerésében. Ha nincs beállítva, általános leírást fog adni. Jelölje be a saját partneri témakör leírásának beállításához tartozó jelölőnégyzetet. 
    1. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Event Channel – további funkciók lap létrehozása":::
1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd kattintson a **Létrehozás** elemre az esemény-csatorna létrehozásához. 

## <a name="next-steps"></a>Következő lépések
- [A partneri témakörök áttekintése](./partner-events-overview.md)
- [Partneri témakörök bevezetésének űrlapja](https://aka.ms/gridpartnerform)
- [Auth0-partneri témakör](auth0-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)