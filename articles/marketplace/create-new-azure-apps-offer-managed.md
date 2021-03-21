---
title: Felügyelt alkalmazáscsomag konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy felügyelt alkalmazási csomagot az Azure-alkalmazás ajánlatához a partner Centerben.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744635"
---
# <a name="configure-a-managed-application-plan"></a>Felügyelt alkalmazáscsomag konfigurálása

Ez a cikk csak az Azure-alkalmazásokra vonatkozó felügyelt alkalmazási csomagokra vonatkozik. Ha megoldást konfigurál a megoldáshoz, nyissa meg a [megoldási sablon beállítása](create-new-azure-apps-offer-solution.md)című témakört.

## <a name="define-markets-pricing-and-availability"></a>Piacok, díjszabás és rendelkezésre állás meghatározása

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. A **díjszabás és rendelkezésre állás** lapon konfigurálhatja azokat a piacokat, amelyeket a csomag elérhetővé fog tenni, a díjszabást, valamint azt, hogy a tervet mindenki számára, vagy csak bizonyos ügyfeleknek (más néven privát csomagnak) szeretné-e látni.

1. A **piacok** területen válassza a **piacok szerkesztése** hivatkozást.
1. A megjelenő párbeszédpanelen válassza ki azokat a piaci helyeket, amelyeken elérhetővé szeretné tenni a tervet. Legalább egy 141-es piacot ki kell választania.

    > [!NOTE]
    > Ezen a párbeszédpanelen egy keresőmező és egy olyan lehetőség található, amely csak a "Tax átutalt" országokra vonatkozik, amelyekben a Microsoft az Ön nevében kifizeti az értékesítési és használati adót.

1. A párbeszédpanel bezárásához kattintson a **Mentés** gombra.

## <a name="define-pricing"></a>Díjszabás meghatározása

A **Price (ár** ) mezőben adja meg a csomag havi árát. Ez az ár az Azure-infrastruktúra vagy a megoldás által központilag üzembe helyezett erőforrások esetében felmerülő használati díjakon felül van.

A havi díj mellett a nem standard egységek fogyasztásának díjszabását is megadhatja [mért számlázás](partner-center-portal/azure-app-metered-billing.md)használatával. Ha kívánja, a havonta megadott árat nulla értékre állíthatja, és kizárólag a mért számlázást kell fizetnie.

Az árak USD-ben (USD = Egyesült Államok dollár) lesznek átalakítva az összes kiválasztott piac helyi pénznemére az aktuális árfolyamok alapján, a mentéskor. A közzététel előtt érvényesítse ezeket az árakat a díjszabási táblázat exportálásával, és tekintse át az egyes piacokon érvényes díjakat. Ha egyéni árakat szeretne beállítani egyedi piacon, módosítsa és importálja a díjszabási táblázatot.

### <a name="add-a-custom-meter-dimension-optional"></a>Egyéni mérési dimenzió hozzáadása (nem kötelező)

1. A **Marketplace-mérési szolgáltatás dimenziói** területen válassza az **Egyéni fogyasztásmérő-dimenzió hozzáadása (max. 18)** hivatkozást.
1. Az **azonosító** mezőben adja meg a nem módosítható azonosító hivatkozását a használati események kibocsátásakor.
1. A **megjelenítendő név** mezőbe írja be a dimenzióhoz társított megjelenítendő nevet. Például a "szöveges üzenetek elküldése".
1. A **Mértékegység** mezőben adja meg a számlázási egység leírását. Például: "szöveges üzenet" vagy "per 100 e-mail".
1. Az **egységenkénti ár USD** mezőben adja meg a dimenzió egy egységének árát.
1. Az **alapszintbe foglalt havi mennyiség** mezőben adja meg a havonta szereplő dimenzió mennyiségét (egész számként) az ismétlődő havi díjat fizető ügyfelek számára. Korlátlan mennyiség beállításához jelölje be a jelölőnégyzetet.
1. Egy másik egyéni mérési dimenzió hozzáadásához ismételje meg az 1 – 7. lépést.

### <a name="set-custom-prices-optional"></a>Egyéni árak beállítása (nem kötelező)

Az USD-ben beállított árak (USD = Egyesült Államok dollár) az összes kiválasztott piac helyi pénznemére lesznek átalakítva, az aktuális árfolyamok alapján, a mentéskor. A közzététel előtt érvényesítse ezeket az árakat a díjszabási táblázat exportálásával, és tekintse át az egyes piacokon érvényes díjakat. Ha egyéni árakat szeretne beállítani egyedi piacon, módosítsa és importálja a díjszabási táblázatot.

A közzététel előtt körültekintően tekintse át az árakat, mivel bizonyos korlátozások miatt a csomag közzététele után is változhatnak.

> [!NOTE]
> Miután közzétette a csomag egy piacának árát, később nem módosítható.

Egyéni árak egyéni piacon történő beállításához exportálja, módosítsa, majd importálja a díjszabási táblázatot. Ön felelős a díjszabás ellenőrzésével és a beállítások tulajdonlásával. Részletes információ: [Egyéni árak](plans-pricing.md#custom-prices).

1. Először mentenie kell a díjszabási módosításokat, hogy engedélyezze a díjszabási adatai exportálását. A **díjszabás és rendelkezésre állás** lap alján kattintson a **Piszkozat mentése** gombra.
1. A **díjszabás** területen válassza ki az **exportálási díjszabási** hivatkozást.
1. Nyissa meg a exportedPrice.xlsx fájlt a Microsoft Excelben.
1. A számolótáblában végezze el a kívánt frissítéseket a díjszabási adatokhoz, majd mentse a fájlt.

   Előfordulhat, hogy a fájl frissítése előtt engedélyeznie kell a szerkesztést az Excelben.

1. A díjszabás **és rendelkezésre állás** lapon, a **díjszabás** területen válassza a **díjszabási adat importálása** hivatkozást.
1. A megjelenő párbeszédpanelen kattintson az **Igen** gombra.
1. Válassza ki a frissített exportedPrice.xlsx fájlt, majd kattintson a **Megnyitás** gombra.

## <a name="choose-who-can-see-your-plan"></a>Válassza ki, hogy ki láthatja a csomagot

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott közönség számára. Az Azure-előfizetési azonosítók használatával hozzáférést biztosíthat egy privát célközönségnek, amely tartalmazza az összes hozzárendelt előfizetés-azonosító leírását. A használatával legfeljebb 10 előfizetés-azonosítót adhat meg manuálisan vagy akár 10 000 előfizetés-azonosítóhoz. CSV-fájl. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisbetűsnek kell lenniük.

> [!NOTE]
> Ha közzétesz egy privát csomagot, később megtekintheti a nyilvános láthatóságot. Ha azonban egy nyilvános csomagot tesz közzé, a saját láthatósága nem módosítható.

A **terv láthatósága** területen tegye a következők egyikét:

- A csomag nyilvános beállításához válassza a **nyilvános** választógombot (más néven _választógombot_).
- A csomag magánjellegűvé tételéhez válassza a **privát** beállítás gombot, és adja hozzá manuálisan vagy CSV-fájllal az Azure-előfizetési azonosítókat.

> [!NOTE]
> A privát vagy a korlátozott célközönség nem azonos az **Előnézet lapon megadott** előnézeti célközönséggel. Az előnézeti közönség hozzáférhet az ajánlathoz a piactéren közzétett élő állapot előtt. Míg a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (magán vagy nem) megtekintheti érvényesítési célból.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Azure-előfizetési azonosítók manuális hozzáadása egy privát csomaghoz

1. A **terv láthatósága** területen kattintson a **privát** beállítás gombra.
1. A megjelenő **Azure előfizetés-azonosító** mezőben adja meg annak a célközönségnek az Azure-ELŐfizetési azonosítóját, amelyhez hozzáférést szeretne adni ehhez a privát csomaghoz. Legalább egy előfizetés-azonosítót kötelező megadni.
1. Választható Adja meg a célközönség leírását a **Leírás** mezőben.
1. Egy másik előfizetés-azonosító hozzáadásához válassza az **azonosító hozzáadása (legfeljebb 10)** hivatkozást, és ismételje meg a 2. és a 3. lépést.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Használja a. CSV-fájl egy privát csomaghoz tartozó Azure-előfizetési azonosítók hozzáadásához

1. A **terv láthatósága** területen kattintson a **privát** beállítás gombra.
1. Válassza ki a **célközönség exportálása (CSV)** hivatkozást.
1. Nyissa meg a t. CSV-fájlt, és adja hozzá azokat az Azure-előfizetési azonosítókat, amelyekhez hozzáférést szeretne adni a privát ajánlathoz az **azonosító** oszlophoz.
1. Szükség esetén megadhatja az egyes célközönségek leírását a **Leírás** oszlopban.
1. Adja hozzá a "SubscriptionId" kifejezést a **Type (típus** ) oszlopban az előfizetés-azonosítóval rendelkező sorokhoz.
1. Mentse a t. CSV-fájl.
1. A **rendelkezésre állás** lapon, a **terv láthatósága** területen válassza ki a **célközönség importálása (CSV)** hivatkozást.
1. A megjelenő párbeszédpanelen válassza az **Igen** lehetőséget.
1. Válassza a elemet. CSV-fájl, majd válassza a **Megnyitás** lehetőséget. Megjelenik egy üzenet, amely jelzi, hogy a. A CSV-fájl importálása sikeresen megtörtént.

## <a name="define-the-technical-configuration"></a>A technikai konfiguráció megadása

A **technikai konfiguráció** lapon fel kell töltenie azt a központi telepítési csomagot, amely lehetővé teszi, hogy az ügyfelek üzembe helyezsék a csomagot, és megadják a csomag verziószámát. További technikai információkat is megadhat.

> [!NOTE]
> Ez a lap nem jelenik meg, ha úgy döntött, hogy újrahasználja a csomagokat egy másik csomagból a **terv beállítása** lapon. Ha igen, lépjen a [csomagok megtekintése](#view-your-plans)elemre.

### <a name="assign-a-version-number-for-the-package"></a>A csomag verziószámának kiosztása

A **verzió** mezőben adja meg a technikai konfiguráció aktuális verzióját. Minden alkalommal növelje ezt a verziót, amikor módosításokat tesz közzé ezen az oldalon. A verziószámnak a (z) Integer. egész_szám. Integer formátumban kell lennie. Például: `1.0.2`.

### <a name="upload-a-package-file"></a>Csomagfájl feltöltése

A **csomagfájl (. zip)** területen húzza a csomagfájl a szürke mezőbe, vagy válassza a **fájl (ok) csatolásának tallózását** .

> [!NOTE]
> Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a `https://upload.xboxlive.com` partner központ által használt szolgáltatást.

#### <a name="previously-published-packages"></a>Korábban közzétett csomagok

A **korábban közzétett csomagok** almenü lehetővé teszi, hogy megtekintse a technikai konfiguráció összes közzétett verzióját.

### <a name="enable-just-in-time-jit-access-optional"></a>Igény szerinti (JIT) hozzáférés engedélyezése (nem kötelező)

A tervhez tartozó JIT-hozzáférés engedélyezéséhez jelölje be az igény szerinti **(JIT) hozzáférés engedélyezése** jelölőnégyzetet. Ha szeretné megkövetelni, hogy a felügyelt alkalmazás felhasználói a fiókjának állandó hozzáférését adják meg, hagyja meg ezt a beállítást, ha nincs bejelölve. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg [az igény szerinti (JIT) hozzáférést](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Telepítési mód kiválasztása

Válassza ki a **teljes** vagy **növekményes** üzembe helyezési módot.

- **Teljes** módban az ügyfél által az alkalmazás újratelepítése a felügyelt erőforráscsoport erőforrásainak eltávolítását eredményezi, ha az erőforrások nincsenek meghatározva a [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template).
- A **növekményes** módban az alkalmazás újratelepítése változatlanul hagyja a meglévő erőforrásokat.

További információ az üzembe helyezési módokról: [Azure Resource Manager telepítési módok](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Értesítési végpont URL-címének megadása

Az **értesítési végpont URL-címe** mezőben adjon meg egy https-webhook-végpontot, hogy értesítést kapjon a csomag ezen verziójának felügyelt alkalmazás példányain lévő összes szifiliszi műveletről.

### <a name="customize-allowed-customer-actions-optional"></a>Engedélyezett felhasználói műveletek testreszabása (nem kötelező)

1. Annak megadásához, hogy az ügyfelek milyen műveleteket hajthatnak végre a felügyelt erőforrásokon a " `*/read` " alapértelmezés szerint elérhető műveletek mellett, jelölje be az **engedélyezett felhasználói műveletek testreszabása** jelölőnégyzetet.
1. A megjelenő mezőkben adja meg a további vezérlési műveleteket és az engedélyezett adatműveleteket, amelyeket az ügyfélnek engedélyezni kíván, pontosvesszővel elválasztva. Ha például engedélyezni szeretné a felhasználók számára a virtuális gépek újraindítását, vegye fel a következőt `Microsoft.Compute/virtualMachines/restart/action` az **engedélyezett vezérlési műveletek** mezőbe.

### <a name="choose-who-can-manage-the-application"></a>Válassza ki, hogy kik kezelhetik az alkalmazást

Adja meg, hogy kinek legyen felügyeleti hozzáférése ehhez a felügyelt alkalmazáshoz az egyes kiválasztott Azure-régiókban: _globális Azure_ és _Azure Government felhő_. Az Azure AD-identitások segítségével azonosíthatja azokat a felhasználókat, csoportokat vagy alkalmazásokat, amelyeknek engedélyt kíván adni a felügyelt erőforráscsoport számára. További információ: Azure-beli [felügyelt alkalmazás tervezése Azure-alkalmazási ajánlathoz](plan-azure-application-offer.md).

Hajtsa végre az alábbi lépéseket a globális Azure-és Azure Government-felhőben, a megfelelő módon.

1. A **Azure Active Directory bérlő azonosítója** mezőben adja meg az Azure ad-bérlő azonosítóját (más néven CÍMTÁR-azonosítót), amely tartalmazza azon felhasználók, csoportok vagy alkalmazások identitását, amelyekhez engedélyeket szeretne adni.
1. A **résztvevő azonosítója** mezőben adja meg annak a felhasználónak, csoportnak vagy alkalmazásnak az Azure ad Object azonosítóját, amelyet a felügyelt erőforráscsoport számára szeretne engedélyezni. Azonosítsa a felhasználót a résztvevő azonosítója alapján, amely a Azure Portal [Azure Active Directory felhasználók](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) paneljén található.
1. A **szerepkör-definíció** listából válassza ki az Azure ad beépített szerepkörét. A kiválasztott szerepkör leírja azokat az engedélyeket, amelyekre a rendszerbiztonsági tag az ügyfél-előfizetésben lévő erőforrásokra vonatkozóan fog rendelkezni.
1. Egy másik engedélyezés hozzáadásához válassza az **Engedélyezés hozzáadása (max 100)** hivatkozást, és ismételje meg az 1 – 3. lépést.

### <a name="policy-settings-optional"></a>Házirend-beállítások (nem kötelező)

Legfeljebb öt házirendet konfigurálhat, és az egyes házirendek közül csak egy példányt lehet beállítani. Egyes házirendek további paramétereket igényelnek.

1. A **házirend-beállítások** területen válassza a **+ házirend hozzáadása (max. 5)** hivatkozást.
1. A **név** mezőbe írja be a szabályzat-hozzárendelés nevét (legfeljebb 50 karakter).
1. A **szabályzatok** listából válassza ki a felügyelt alkalmazás által az ügyfél-előfizetésben létrehozott erőforrásokra alkalmazandó Azure-házirendet.
1. A **házirend-paraméterek** mezőben adja meg azt a paramétert, amelyre alkalmazni kívánja a naplózási és diagnosztikai beállítások házirendjét.
1. A **házirend SKU** listáról válassza ki a házirend SKU-típusát.

    > [!NOTE]
    > A naplózási házirendekhez a _szabványos házirend_ -SKU szükséges.

## <a name="view-your-plans"></a>A csomagok megtekintése

- Válassza a **Piszkozat mentése** lehetőséget, majd a lap bal felső részén kattintson a **terv áttekintése** lehetőségre a **terv áttekintő** lapjára való visszatéréshez.

Egy vagy több csomag létrehozása után megtekintheti a csomag nevét, a terv AZONOSÍTÓját, a csomag típusát, a rendelkezésre állást (nyilvános vagy magánjellegű), az aktuális közzétételi állapotot, valamint az összes elérhető műveletet a **terv áttekintése** lapon.

A **terv áttekintés** lapjának **művelet** oszlopában elérhető műveletek a csomag állapotától függően változhatnak, és a következőket foglalhatják magukban:

- Ha a terv állapota **Piszkozat**, a **művelet** oszlopban szereplő hivatkozás a **delete piszkozatot** fogja mondani.
- Ha a terv állapota **élő**, a **művelet** oszlopban található hivatkozás nem fogja **megszüntetni a terv értékesítését** vagy a **privát célközönség szinkronizálását**. A **privát célközönség szinkronizálása** hivatkozás csak a privát célközönségek módosításait teszi közzé anélkül, hogy közzé kellene tenni az ajánlatban esetlegesen végrehajtott egyéb frissítéseket.
- Ha egy másik csomagot szeretne létrehozni ehhez az ajánlathoz, a **terv áttekintése** lap tetején válassza az **+ új terv létrehozása** lehetőséget. Ezután ismételje meg az [Azure-alkalmazási ajánlathoz tartozó csomagok létrehozása](create-new-azure-apps-offer-plans.md)című témakör lépéseit. Ellenkező esetben, ha elkészült a csomagok létrehozásával, ugorjon a következő szakaszra: további lépések.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-alkalmazás ajánlatának tesztelése és közzététele](create-new-azure-apps-offer-test-publish.md).
- Ismerje meg [, hogyan értékesítheti Azure-alkalmazásait](create-new-azure-apps-offer-marketing.md) a Microsofttal való közös értékesítéssel és a CSP-programokkal való viszonteladással.