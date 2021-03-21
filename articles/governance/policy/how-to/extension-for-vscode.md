---
title: Azure Policy-bővítmény a Visual Studio Code-hoz
description: Megtudhatja, hogyan kereshet Azure Resource Manager aliasokat a Visual Studio Code-hoz készült Azure Policy bővítmény használatával.
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 4c4ba0eeb0506179ff92ead0ee86f048600d157e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107939"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Azure Policy-bővítmény használata a Visual Studio Code-hoz

> A Azure Policy bővítmény **0.1.1** és újabb verziójára vonatkozik

Megtudhatja, hogyan használhatja a Visual Studio Code Azure Policy bővítményét az [aliasok](../concepts/definition-structure.md#aliases)keresésére, az erőforrások és a házirendek áttekintésére, az objektumok exportálására és a szabályzat-definíciók kiértékelésére. Először is leírjuk, hogyan kell telepíteni a Azure Policy bővítményt a Visual Studio Code-ban. Ezután megismerheti az aliasok keresésének módját.

A Visual Studio Code Azure Policy-bővítmény telepíthető Windows rendszerre.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek végrehajtásához a következő elemek szükségesek:

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-and-configure-the-azure-policy-extension"></a>Az Azure Policy-bővítmény telepítése és konfigurálása

Az előfeltételek teljesítése után a következő lépésekkel telepítheti Azure Policy-bővítményt a Visual Studio Code-hoz:

1. Nyissa meg a Visual Studio Code-ot.
1. A menüsávban lépjen a   >  **bővítmények** megtekintése elemre.
1. A keresőmezőbe írja be a **Azure Policy** kifejezést.
1. Válassza a **Azure Policy** elemet a keresési eredmények közül, majd válassza a **telepítés** lehetőséget.
1. Szükség esetén válassza az **Újratöltés** lehetőséget.

A nemzeti Felhőbeli felhasználók esetében kövesse az alábbi lépéseket az Azure-környezet első beállításához:

1. Válassza a **File\Preferences\Settings** lehetőséget.
1. Keressen rá a következő sztringre: _Azure: Cloud_
1. Válassza ki a nemzet felhőjét a listából:

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Képernyőkép a Visual Studio Code-hoz készült ország Azure Cloud-bejelentkezés kiválasztásáról." border="false":::

## <a name="using-the-policy-extension"></a>A házirend-bővítmény használata

> [!NOTE]
> A következő, a Visual Studio Code-hoz készült Azure Policy-bővítményben megtekintett házirendek nem szinkronizálhatók az Azure-ba.

### <a name="connect-to-an-azure-account"></a>Kapcsolódás Azure-fiókhoz

Az erőforrások és a keresési aliasok kiértékeléséhez csatlakoznia kell az Azure-fiókjához. Kövesse az alábbi lépéseket az Azure-hoz való kapcsolódáshoz a Visual Studio Code-ból:

1. Jelentkezzen be az Azure-ba a Azure Policy bővítményből vagy a parancssorból.

   - Azure Policy bővítmény

     A Azure Policy bővítményben válassza a **Bejelentkezés az Azure**-ba lehetőséget.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Képernyőfelvétel a Visual Studio Code-ról és a Azure Policy-bővítmény ikonja." border="false":::

   - Parancs paletta

     A menüsávban lépjen a parancs-   >  **paletta** megtekintése elemre, és írja be az **Azure: bejelentkezés** lehetőséget.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="Képernyőkép a Visual Studio Code-hoz készült Azure Cloud bejelentkezési lehetőségeiről a parancs palettáján." border="false":::

1. Az Azure-ba való bejelentkezéshez kövesse a bejelentkezési utasításokat. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio Code ablak alján található állapotsorban.

### <a name="select-subscriptions"></a>Előfizetések kiválasztása

Amikor először jelentkezik be, csak az alapértelmezett előfizetési erőforrásokat és házirendeket tölti be a Azure Policy bővítmény. Az erőforrások és házirendek megjelenítéséhez szükséges előfizetések hozzáadásához vagy eltávolításához kövesse az alábbi lépéseket:

1. Indítsa el az előfizetés parancsot a parancssorból vagy az ablak láblécből.

   - Parancs paletta:

     A menüsávban lépjen a parancs-  > **paletta** megtekintése elemre, és írja be az **Azure: Select Subscriptions (előfizetések) lehetőséget**.

   - Ablak lábléce

     A képernyő alján található ablak láblécében válassza ki az Azure-nak megfelelő szegmenst **: \<your account\>**.

1. A szűrő mező használatával gyorsan megkeresheti az előfizetéseket név szerint. Ezután a Azure Policy-bővítmény által megjelenített előfizetések beállításához jelölje be vagy távolítsa el az egyes előfizetések ellenőrzését. Ha végzett a megjelenítendő előfizetések hozzáadásával vagy eltávolításával, kattintson **az OK gombra**.

### <a name="search-for-and-view-resources"></a>Erőforrások keresése és megtekintése

A Azure Policy bővítmény a kiválasztott előfizetések erőforrás-szolgáltatója és az **erőforrások** ablaktáblán erőforráscsoport alapján listázza az erőforrásokat. A TreeView vezérlő a kiválasztott előfizetésben vagy az előfizetési szinten lévő erőforrások alábbi csoportosításait tartalmazza:

- **Erőforrás-szolgáltatók**
  - Minden regisztrált erőforrás-szolgáltató erőforrásokkal és kapcsolódó alárendelt erőforrásokkal, amelyek szabályzat-aliasokkal rendelkeznek
- **Erőforráscsoportok**
  - Az erőforráscsoport összes erőforrása

Alapértelmezés szerint a bővítmény az "erőforrás-szolgáltató" részét a meglévő erőforrások és a házirend-aliasokkal rendelkező erőforrások alapján szűri. Módosítsa ezt a viselkedést a **Beállítások**  >  **bővítmények**  >  **Azure Policy** az összes szűrés nélküli erőforrás-szolgáltató megjelenítéséhez.

Az egyes előfizetésekben több száz vagy több ezer erőforrást használó ügyfelek kereshetnek keresési módot az erőforrásaik megkereséséhez. A Azure Policy bővítmény lehetővé teszi egy adott erőforrás keresését a következő lépésekkel:

1. Indítsa el a keresési felületet a Azure Policy-bővítményből vagy a parancssorból.

   - Azure Policy bővítmény

     Az Azure Policy-bővítményben vigye az egérmutatót az **erőforrások** panel fölé, válassza a három pontot, majd válassza az **erőforrások keresése** lehetőséget.

   - Parancs paletta:

     A menüsávban lépjen a parancs-  > **paletta** megtekintése elemre, és adja meg az **erőforrások: erőforrások keresése** lehetőséget.

1. Ha egynél több előfizetés van kiválasztva a megjelenítéshez, használja a szűrőt a keresendő előfizetés kiválasztásához.

1. A szűrő használatával kiválaszthatja, hogy melyik erőforráscsoport legyen a korábban kiválasztott előfizetés részét képező keresés.

1. A szűrő használatával kiválaszthatja, hogy melyik erőforrást szeretné megjeleníteni. A szűrő az erőforrás nevére és az erőforrás típusára is használható.

### <a name="discover-aliases-for-resource-properties"></a>Az erőforrás-tulajdonságok aliasának felderítése

Ha erőforrás van kiválasztva, akár a keresési felületen keresztül, akár a TreeView-ban való kiválasztásával, a Azure Policy-bővítmény megnyitja az adott erőforrást jelképező JSON-fájlt, valamint az összes Azure Resource Manager tulajdonság értékét.

Ha egy erőforrás meg van nyitva, a Resource Manager-tulajdonságnév vagy-érték fölé húzva megjelenik a Azure Policy alias, ha van ilyen. Ebben a példában az erőforrás egy erőforrástípus, `Microsoft.Compute/virtualMachines` a **Properties. StorageProfile. imageReference. Offer** tulajdonság pedig a fölé van helyezve. Az egérmutató a megfelelő aliasokat jeleníti meg.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Képernyőkép a Visual Studio Code-hoz készült Azure Policy-bővítményről, amely egy tulajdonsággal jeleníti meg az aliasok nevét." border="false":::

> [!NOTE]
> A VS Code bővítmény csak a Resource Manager üzemmód tulajdonságainak kiértékelését támogatja. További információ a módokról: [Mode-definíciók](../concepts/definition-structure.md#mode).

### <a name="search-for-and-view-policies-and-assignments"></a>Szabályzatok és hozzárendelések keresése és megtekintése

A Azure Policy bővítmény a házirendek ablaktáblán megjelenítendő előfizetések számára a **szabályzatok** és a házirendek hozzárendelését jeleníti meg. Egy adott előfizetésben több száz vagy akár több ezer szabályzatot vagy hozzárendelést is használhatnak, így kereshetik meg a szabályzatokat vagy a hozzárendeléseket. A Azure Policy bővítmény lehetővé teszi egy adott házirend vagy hozzárendelés keresését a következő lépésekkel:

1. Indítsa el a keresési felületet a Azure Policy-bővítményből vagy a parancssorból.

   - Azure Policy bővítmény

     Az Azure Policy-bővítményben vigye a kurzort a **házirendek** panel fölé, válassza a három pontot, majd válassza a **keresési szabályzatok** lehetőséget.

   - Parancs paletta:

     A menüsávban lépjen a parancs-  > **paletta** megtekintése elemre, és adja meg a **szabályzatok: keresési házirendek** elemet.

1. Ha egynél több előfizetés van kiválasztva a megjelenítéshez, használja a szűrőt a keresendő előfizetés kiválasztásához.

1. A szűrő használatával kiválaszthatja, hogy melyik házirend-típust vagy hozzárendelést szeretné megkeresni a korábban kiválasztott előfizetés részeként.

1. A szűrő használatával kiválaszthatja, hogy melyik házirendet vagy jelenjen meg. A szűrő a _DisplayName_ paraméterrel működik a házirend-definícióhoz vagy a házirend-hozzárendeléshez.

Amikor kijelöl egy házirendet vagy hozzárendelést, legyen az a keresési felületen keresztül, vagy kiválasztja azt a TreeView vezérlőben, a Azure Policy bővítmény megnyitja a szabályzatot vagy hozzárendelést jelölő JSON-értéket, valamint az összes erőforrás-kezelő tulajdonság értékét. A bővítmény ellenőrizheti a megnyitott Azure Policy JSON-sémát.

### <a name="export-objects"></a>Objektumok exportálása

Az előfizetések objektumai a helyi JSON-fájlba exportálhatók. Az **erőforrások** vagy **házirendek** ablaktáblán vigye az egérmutatót egy exportálható objektumra vagy válasszon ki. A kiemelt sor végén válassza a Mentés ikont, és válasszon ki egy mappát, amelybe menteni szeretné a JSON-t.

A következő objektumokat lehet helyileg exportálni:

- Erőforrások panel
  - Erőforráscsoportok
  - Egyéni erőforrások (vagy egy erőforráscsoport vagy egy erőforrás-szolgáltató alatt)
- Házirendek ablaktábla
  - Szabályzat-hozzárendelések
  - Beépített szabályzat-definíciók
  - Egyéni szabályzat-definíciók
  - Kezdeményezések

### <a name="on-demand-evaluation-scan"></a>Igény szerinti értékelési vizsgálat

A kiértékelési vizsgálat a Visual Studio Code-hoz készült Azure Policy bővítménnyel indítható el. A próbaverzió elindításához jelölje ki és rögzítse a következő objektumokat: egy erőforrást, egy házirend-definíciót és egy házirend-hozzárendelést.

1. Az egyes objektumok rögzítéséhez keresse meg az **erőforrások** ablaktáblán vagy a **házirendek** ablaktáblán, és jelölje ki a rögzítés a szerkesztési lapra ikont. Egy objektum rögzítésével hozzáadja azt a bővítmény **értékelési** ablaktáblájához.
1. Az **értékelés** ablaktáblán válassza ki az egyes objektumok egyikét, és a kiválasztás a kiértékeléshez ikon használatával jelölje meg a kiértékelésben szereplőként.
1. A **kiértékelés** ablaktábla tetején válassza a futtatási értékelés ikont. Megnyílik egy új panel a Visual Studio Code-ban, amely a kapott kiértékelési adatokat JSON formátumban.

> [!NOTE]
> A [AuditIfNotExists](../concepts/effects.md#auditifnotexists) vagy [DeployIfNotExists](../concepts/effects.md#deployifnotexists) házirend-definíciók esetében a **kiértékelés** ablaktáblán a plusz ikont használva válasszon ki egy _kapcsolódó_ erőforrást a létezés ellenőrzéséhez.

A kiértékelési eredmények a **policyEvaluations. evaluationResult** tulajdonsággal együtt tartalmazzák a házirend-definícióval és a házirend-hozzárendeléssel kapcsolatos információkat. A kimenet a következő példához hasonlóan néz ki:

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> A VS Code bővítmény csak a Resource Manager üzemmód tulajdonságainak kiértékelését támogatja. További információ a módokról: [Mode-definíciók](../concepts/definition-structure.md#mode).
>
> A kiértékelési funkció a bővítmény macOS és Linux rendszerű példányain nem működik.

### <a name="sign-out"></a>Kijelentkezés

A menüsávban válassza a parancs-   >  **paletta** megtekintése elemet, majd írja be az **Azure: kijelentkezés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
