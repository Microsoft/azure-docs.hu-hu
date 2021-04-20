---
title: Irányítópult létrehozása a Azure Portal
description: Ez a cikk azt ismerteti, hogyan hozhat létre és szabhat testre irányítópultot a Azure Portal.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726077"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Irányítópult létrehozása a Azure Portal

Az irányítópultok a felhőbeli erőforrások összpontosított és rendezett nézetét Azure Portal. Az irányítópultokat munkaterületként használhatja, ahol figyelheti az erőforrásokat, és gyorsan elindíthat feladatokat a napi műveletekhez. Létrehozhat egyéni irányítópultokat projektek, feladatok vagy felhasználói szerepkörök alapján, például.

A Azure Portal alapértelmezett irányítópultot biztosít kiindulási pontként. Szerkesztheti az alapértelmezett irányítópultot, és további irányítópultokat hozhat létre és szabhat testre.

> [!NOTE]
> Minden felhasználó legfeljebb 100 privát irányítópultot hozhat létre. Ha közzéteszi [és megosztja](azure-portal-dashboard-share-access.md)az irányítópultot, az Azure-erőforrásként lesz megvalósítva az előfizetésében, és nem számít bele ebbe a korlátba.

Ez a cikk bemutatja, hogyan hozhat létre és szabhat testre egy új irányítópultot. További információ az irányítópultok megosztásáról: [Azure-irányítópultok megosztása azure szerepköralapú hozzáférés-vezérléssel.](azure-portal-dashboard-share-access.md)

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása

Ez a példa bemutatja, hogyan hozhat létre egy új privát irányítópultot egy hozzárendelt névvel. A létrehozáskor minden irányítópult privát, de ha szeretné, közzéteheti és megoszthatja az irányítópultot a szervezet más felhasználóival.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal válassza az Irányítópult **lehetőséget.** Lehetséges, hogy az alapértelmezett nézet már irányítópultra van állítva.

    ![Képernyőkép a Azure Portal irányítópultot kiválasztva.](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Válassza **az Új irányítópult, majd** az Üres irányítópult **lehetőséget.**

    ![Képernyőkép az Új irányítópult beállításairól.](./media/azure-portal-dashboards/create-new-dashboard.png)

    Ez a művelet megnyitja a **Csempetárat,** amelyből kiválaszthatja a csempéket, valamint egy üres rácsot, ahol elrendezheti a csempéket.

1. Válassza ki **a Saját irányítópult** szöveget az irányítópult címkéjén, és adjon meg egy nevet, amely segít az egyéni irányítópult egyszerű azonosításában.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Képernyőkép egy üres rácsról a Csempetár segítségével.":::

1. Az irányítópult mentéséhez válassza a **Testreszabás** kész lehetőséget az oldal fejlécében. Vagy folytassa a következő szakaszt a csempék hozzáadásához és az irányítópult mentéshez.

Az irányítópult nézetben most megjelenik az új irányítópult. Az irányítópult neve melletti nyílra kattintva érhetők el az Ön számára elérhető irányítópultok. A lista tartalmazhat más felhasználók által létrehozott és megosztott irányítópultokat is.

## <a name="edit-a-dashboard"></a>Irányítópult szerkesztése

Most szerkessze az irányítópultot az Azure-erőforrásokat képviselő csempék hozzáadásához, átméretezéséhez és elrendezéséhez.

### <a name="add-tiles-from-the-tile-gallery"></a>Csempék hozzáadása a Csempetárból

Ha csempéket szeretne hozzáadni egy irányítópulthoz, kövesse az alábbi lépéseket:

1. Válassza ![ a Szerkesztés ikon ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Szerkesztés** lehetőséget az irányítópult oldalfejlécén.

    ![A Szerkesztés lehetőséget kiemelő irányítópult képernyőképe.](./media/azure-portal-dashboards/dashboard-edit.png)

1. Tallózhat **a Csempetárban,** vagy a keresőmezővel megkereshet egy adott csempét. Válassza ki az irányítópulthoz hozzáadni kívánt csempét.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="A Csempetár képernyőképe.":::

1. Válassza **a Hozzáadás** lehetőséget a csempe alapértelmezett méretű és helyű irányítópulthoz való hozzáadásához. Vagy húzza a csempét a rácsra, és helyezze a kívánt helyre. Adja hozzá a kívánt csempéket, de íme néhány ötlet:

    - Adja **hozzá a Minden** erőforrást a már létrehozott erőforrásokhoz.

    - Ha egynél több szervezettel dolgozik, adja hozzá a Szervezeti identitás csempét az irányítópulthoz, hogy egyértelműen meg tudja mutatni, melyik szervezethez tartoznak az erőforrások. 

1. Szükség esetén átméretezheti a csempét a csempe jobb alsó sarkának húzásával.

1. A módosítások mentéséhez válassza a **Mentés lehetőséget** az oldal fejlécében. A módosítások előnézetét mentés nélkül is megtekintheti, ha **kiválasztja** az Előnézet gombra az oldal fejlécében. Az előnézeti képernyőn a  Mentés gombra kattintva megtarthatja **a** módosításokat, az Elvetés lehetőséggel eltávolíthatja őket, a Szerkesztés lehetőséggel pedig visszamehet a szerkesztési lehetőségekre, és további módosításokat is eszközelhet. 

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Az Előnézet, a Mentés és az Elvetés lehetőség képernyőképe.":::

### <a name="pin-content-from-a-resource-page"></a>Tartalom kitűzása erőforrásoldalról

A csempék irányítópulthoz való hozzáadásának másik módja közvetlenül egy erőforrásoldalról.

Számos erőforrásoldal tartalmaz rögzítés ikont a parancssávon. Ha ezt az ikont választja, a forrásoldalt jelképezett csempét rögzítheti egy meglévő irányítópulton vagy egy új létrehozott irányítópulton.

![Képernyőkép az oldalparancssávról a rögzítés ikonnal](./media/azure-portal-dashboards/dashboard-pin-blade.png)

Bizonyos esetekben a rögzítés ikon egy oldalon belüli adott tartalom alapján is megjelenhet, ami azt jelenti, hogy az adott tartalomhoz a teljes oldal helyett az adott tartalomhoz rögzíthet csempét.

### <a name="resize-or-rearrange-tiles"></a>Csempék átméretezése vagy átrendezése

A csempék méretének módosítása vagy az irányítópulton lévő csempék átrendezése az alábbi lépésekkel változott:

1. Az ![ oldal ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **fejlécében válassza** a Szerkesztés ikon Szerkesztés lehetőséget.

1. Válassza ki a helyi menüt a csempe jobb felső sarkában. Ezután válassza ki a csempe méretét. A bármilyen méretet támogató csempék a jobb alsó sarokban található "fogópontot" is tartalmaznak, amellyel a kívánt méretre húzhatja a csempét.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Képernyőkép az irányítópultról megnyitott csempeméret menüvel.":::

1. Válasszon ki egy csempét, és húzza egy új helyre a rácson az irányítópult elrendezéseként.

### <a name="additional-tile-configuration"></a>További csempekonfiguráció

Egyes csempéken több konfigurálásra lehet szükség a kívánt információk megjelenítése érdekében. A Metrikadiagram **csempét** például úgy kell beállítani, hogy megjelenítsen egy metrikát a Azure Monitor. A csempeadatokat testre is szabhatja az irányítópult alapértelmezett időbeállításának felülbírálása érdekében.

Minden beállítani szükséges csempe egy szalagcímet jelenít meg, amíg testre nem szabja a csempét. A **Metrikadiagramhoz a** szalagcím **a Metrikákban való szerkesztés.** A csempe testreszabása:

1. Az oldal fejlécében válassza a **Mentés lehetőséget** a szerkesztési módból való kilépéshez.

1. Válassza ki a szalagcímet, majd tegye meg a szükséges beállítást.

    ![Konfigurációt igénylő csempe képernyőképe](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> A Markdown-csempék segítségével egyéni statikus tartalmak jeleníthetőek meg az irányítópulton. Ez lehet alapszintű utasítás, kép, hivatkozáskészlet vagy akár kapcsolattartási adatok is. A Markdown-csempék használatával kapcsolatos további információkért lásd: Markdown-csempe használata Az Azure-irányítópultokon [egyéni tartalmak megjelenítése.](azure-portal-markdown-tile.md)

### <a name="customize-tile-data"></a>Csempeadatok testreszabása

Az irányítópulton lévő adatok automatikusan megjelenítik az elmúlt 24 óra tevékenységét. Ha más időtartományt is meg kell mutatnia ehhez a csempéhez, kövesse az alábbi lépéseket:

1. Válassza **a Helyi menü** Csempeadatok testreszabása parancsát, vagy a csempe bal felső sarkában található szűrő ![ ](./media/azure-portal-dashboards/dashboard-filter.png) ikonszűrőt.

    ![A csempe helyi menüjének képernyőképe.](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Jelölje be a jelölőnégyzetet **az irányítópult időbeállításának felülbírálása csempeszinten.**

    ![Képernyőkép a csempeidő-beállításokat konfiguráló párbeszédpanelről.](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Válassza ki a csempe időtartományát. Választhat az elmúlt 30 perc és az elmúlt 30 nap között, vagy definiálhat egyéni tartományt.

1. Válassza ki a megjelenítendő idő részletességét. Az egyperces növekmények és az egy hónap közötti értékeket bárhol meg lehet mutatni.

1. Kattintson az **Alkalmaz** gombra.

## <a name="delete-a-tile"></a>Csempe törlése

Ha el szeretne távolítani egy csempét egy irányítópultról, tegye a következők egyikét:

- Válassza a csempe jobb felső sarkában található helyi menüt, majd az Eltávolítás **az irányítópultról lehetőséget.**

- Válassza ![ a Szerkesztés ikon Szerkesztés ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **lehetőséget** a testreszabási módba való belépéshez. Vigye a kurzort a csempe jobb felső sarkába, majd válassza a törlés ikont törlés ikon a csempe irányítópultról ![ ](./media/azure-portal-dashboards/dashboard-delete-icon.png) való eltávolításához.

   ![Képernyőkép a csempe irányítópultról való eltávolításáról.](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Irányítópult klónozása

Ha egy meglévő irányítópultot új irányítópult sablonjaként használ, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az irányítópult nézetben látható a másolni kívánt irányítópult.

1. Az oldal fejlécében válassza a ![ klónozás ](./media/azure-portal-dashboards/dashboard-clone.png) **ikont Klónozás.**

1. Szerkesztési módban megnyílik az irányítópult klónja nevű másolat.   A cikkben leírt lépésekkel átnevezheti és testre szabhatja az irányítópultot.

## <a name="publish-and-share-a-dashboard"></a>Irányítópult közzététele és megosztása

Amikor létrehoz egy irányítópultot, az alapértelmezés szerint privát, ami azt jelenti, hogy csak Ön láthatja. Ha mások számára is elérhetővé tenni az irányítópultokat, közzéteheti és megoszthatja őket. További információ: [Azure-irányítópultok megosztása az Azure szerepköralapú hozzáférés-vezérlés használatával.](azure-portal-dashboard-share-access.md)

### <a name="open-a-shared-dashboard"></a>Megosztott irányítópult megnyitása

Megosztott irányítópultok kereséshez és megnyitásához kövesse az alábbi lépéseket:

1. Válassza az irányítópult neve melletti nyilat.

1. Válasszon az irányítópultok listájából. Ha a megnyitni kívánt irányítópult nem szerepel a listán:

    1. válassza **az Összes irányítópult tallózása lehetőséget.**

        ![Képernyőkép az irányítópult kijelölési menüjéről](./media/azure-portal-dashboards/dashboard-browse.png)

    1. A Típus **mezőben** válassza a **Megosztott irányítópultok lehetőséget.**

        ![Az összes irányítópult kijelölési menüjének képernyőképe](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Válasszon ki egy vagy több előfizetést. Szöveget is beírhat az irányítópultok név alapján való szűréséhez.

    1. Válasszon ki egy irányítópultot a megosztott irányítópultok listájából.

## <a name="delete-a-dashboard"></a>Irányítópult törlése

Privát vagy megosztott irányítópult végleges törléséhez kövesse az alábbi lépéseket:

1. Válassza ki a törölni kívánt irányítópultot az irányítópult neve melletti listából.

1. Válassza ![ a törlés ikon ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Törlés** lehetőséget az oldal fejlécében.

1. Privát irányítópultként a megerősítő **párbeszédpanelEN kattintson** az OK gombra az irányítópult eltávolításához. Megosztott irányítópultnál a megerősítő párbeszédpanelen jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy a közzétett irányítópultot a továbbiakban nem fogják látni mások. Ez után válassza az **OK** gombot.

    ![Képernyőkép a törlés megerősítéséről.](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Törölt irányítópult helyreállítása

Ha a globális Azure-felhőben van, és töröl egy közzétett irányítópultot _a_ Azure Portal, a törlést követő 14 napon belül helyreállíthatja az irányítópultot. További információ: Törölt irányítópult helyreállítása [a Azure Portal.](recover-shared-deleted-dashboard.md)

## <a name="next-steps"></a>Következő lépések

- [Azure-irányítópultok megosztása azure-beli szerepköralapú hozzáférés-vezérléssel](azure-portal-dashboard-share-access.md)
- [Azure-irányítópultok létrehozása programozott módon](azure-portal-dashboards-create-programmatically.md)
