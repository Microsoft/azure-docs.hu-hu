---
title: 'Gyors útmutató: interaktív térképes keresés a Azure Maps'
description: 'Rövid útmutató: interaktív, kereshető térképeket hozhat létre. Lásd: Azure Maps-fiók létrehozása, elsődleges kulcs beszerzése és a web SDK használata térképes alkalmazások beállításához'
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24a834c87fe34d90dec5961bb3f8d376c6e5e62d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373215"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Gyors útmutató: interaktív keresési Térkép létrehozása Azure Maps

Ez a cikk bemutatja, hogyan használható a Azure Maps egy olyan Térkép létrehozásához, amely interaktív keresési élményt biztosít a felhasználóknak. Végigvezeti a következő alapvető lépéseken:

* Hozzon létre egy saját Azure Maps fiókot.
* Szerezze be az elsődleges kulcsot a bemutató webalkalmazásban való használathoz.
* Töltse le és nyissa meg a bemutató Térkép alkalmazást.

Ez a rövid útmutató a Azure Maps web SDK-t használja, azonban a Azure Maps Services bármely Térkép vezérlőelemmel használható. [Íme](open-source-projects.md#third-part-map-control-plugins) néhány népszerű, nyílt forráskódú Térkép vezérlőelem, amelyet a Azure Maps csapat készített a beépülő modulhoz.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

Hozzon létre egy új Azure Maps fiókot a következő lépésekkel:

1. Kattintson az [Azure Portal](https://portal.azure.com) bal felső sarkában az **Erőforrás létrehozása** gombra.
2. A *Keresés a piactéren* mezőbe írja be a következőt: **Azure Maps**.
3. Az *eredmények* közül válassza a **Azure Maps** lehetőséget. Kattintson a térkép alatt megjelenő **Létrehozás** gombra.
4. A **Maps-fiók létrehozása** lapon adja meg a következő értékeket:
    * A fiókhoz használni kívánt *előfizetés*.
    * A fiókhoz tartozó *erőforráscsoport* neve. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    * Az új fiók *neve*.
    * A fiók *díjszabási szintje* .
    * Olvassa el a *licencfeltételeket* és az *adatvédelmi nyilatkozatot*, és jelölje be az azok elfogadását jelző jelölőnégyzetet.
    * Kattintson a **Létrehozás** gombra.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Maps-fiók létrehozása a portálon":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>A fiók elsődleges kulcsának lekérése

Miután sikeresen létrejött a Maps-fiókja, kérje le az elsődleges kulcsot, amely lehetővé teszi a Maps API-k lekérdezését.

1. Nyissa meg a Maps-fiókot a portálon.
2. A beállítások szakaszban válassza a **hitelesítés** lehetőséget.
3. Másolja ki az **elsődleges kulcsot** a vágólapra. Mentse a helyi gépre, hogy később felhasználhassa ebben az oktatóanyagban.

>[!NOTE]
> Ha az elsődleges kulcs helyett az előfizetési kulcsot használja, a Térkép nem jelenik meg megfelelően. Biztonsági okokból javasolt az elsődleges és másodlagos kulcsok közötti váltás. A kulcsok elforgatásához frissítse az alkalmazást a másodlagos kulcs használatára, telepítse, majd nyomja le az elsődleges kulcs melletti ciklus/frissítés gombot egy új elsődleges kulcs létrehozásához. A régi elsődleges kulcs le lesz tiltva. A kulcsok elforgatásával kapcsolatos további információkért lásd: [Azure Key Vault beállítása kulcsfontosságú rotációs és naplózási](../key-vault/secrets/tutorial-rotation-dual.md) szolgáltatással

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Elsődleges kulcs Azure Maps kulcs beolvasása Azure Portal":::

## <a name="download-the-demo-application"></a>A bemutató alkalmazás letöltése

1. Ugrás [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Másolja a fájl tartalmát.
2. Mentse a fájl tartalmát helyileg **AzureMapDemo.html**-ként. Nyissa meg egy szövegszerkesztőben.
3. Keresse meg a karakterláncot `<Your Azure Maps Key>` . Cserélje le az elemet az előző szakasz **elsődleges kulcs** értékére.

## <a name="open-the-demo-application"></a>A bemutató alkalmazás megnyitása

1. Nyissa meg az **AzureMapDemo.html** fájlt egy tetszőleges böngészőben.
2. Figyelje meg a Los Angeles városának látható térképét. Nagyítson és kicsinyítsen, ekkor a térkép automatikusan több vagy kevesebb információt jelenít meg a nagyítás mértékétől függően.
3. Módosítsa a térkép alapértelmezett középpontját. Az **AzureMapDemo.html** fájlban keresse meg a **center** nevű változót. A változó szélesség–hosszúság értékpárt cserélje le a következő új értékre: **[-74.0060, 40.7128]**. Mentse a fájlt, és frissítse a böngészőt.
4. Próbálja ki az interaktív keresést. A bemutató webalkalmazás bal felső sarkában található keresőmezőbe keressen az **éttermek** kifejezésre.
5. Vigye az egérmutatót a keresési mező alatt megjelenő címek és helyszínek listájára. Figyelje meg, hogy a térképen a megfelelő PIN-kód jelenik meg. A magánvállalkozások adatainak védelme érdekében az itt látható nevek és címek nem valósak.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Interaktív térképes keresési webalkalmazás":::


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

>[!WARNING]
>A [következő lépések](#next-steps) szakaszban felsorolt oktatóanyagok részletesen ismertetik, hogyan használhatja és konfigurálhatja a Azure Maps a fiókjával. Ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat, ha azt tervezi, hogy folytatja az oktatóanyagokat.

Ha nem tervezi tovább az oktatóanyagokat, hajtsa végre az alábbi lépéseket az erőforrások törléséhez:

1. Zárjuk be a **AzureMapDemo.html** webalkalmazást futtató böngészőt.
2. Navigáljon a Azure Portal lapra. A fő portál lapon válassza az **összes erőforrás** lehetőséget. Vagy kattintson a bal felső sarokban található menü ikonra. Válassza a **Minden erőforrás** elemet.
3. Kattintson a Azure Maps-fiókjára. A lap tetején kattintson a **Törlés** elemre.

További példákat és egy interaktív kódolási élményt a következő útmutatók tartalmaznak:

[Azure Maps keresési szolgáltatással rendelkező címek keresése](how-to-search-for-address.md)

[Használja a Azure Maps térképkezelés](how-to-use-map-control.md)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta Azure Maps-fiókját, és létrehozott egy bemutató alkalmazást. Tekintse meg az alábbi oktatóanyagokat, amelyekben többet tudhat meg Azure Maps:

> [!div class="nextstepaction"]
> [Közeli érdekes helyek keresése Azure Maps](tutorial-search-location.md)