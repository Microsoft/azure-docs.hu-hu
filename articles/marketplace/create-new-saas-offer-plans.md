---
title: Csomagok létrehozása SaaS-ajánlathoz a Microsoft partner Centerben
description: Csomagok létrehozása a Microsoft kereskedelmi piactér portálon a partner Centerben a Service (SaaS) szolgáltatáshoz készült új szoftverekhez.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fb1514c146dcd352451597e6182c6d99ed8836d6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044719"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Csomagok létrehozása SaaS-ajánlathoz

A Microsoft kereskedelmi Piactéren keresztül eladott ajánlatoknak legalább egy csomaggal kell rendelkezniük. Több különböző csomagot is létrehozhat ugyanazon az ajánlaton belül. Ezek a csomagok (más néven SKU-ként) eltérőek lehetnek a verzió, a monetizálása vagy a szolgáltatási szintek tekintetében. A csomagokkal kapcsolatos részletes útmutatásért lásd: a [kereskedelmi piactéren kínált csomagok és díjszabások](plans-pricing.md).

> [!NOTE]
> Ha úgy dönt, hogy egymástól függetlenül dolgozza fel a tranzakciókat, akkor ez a beállítás nem jelenik meg. Ehelyett ugorjon az [SaaS-ajánlat értékesítésének módjára](create-new-saas-offer-marketing.md).

## <a name="create-a-plan"></a>Csomag létrehozása

1. A **terv áttekintése** lap tetején válassza az **+ új terv létrehozása** lehetőséget.

1. A megjelenő párbeszédpanel **terv azonosítója** mezőjében adjon meg egy egyedi csomagot. Legfeljebb 50 kisbetűs alfanumerikus karaktert, kötőjelet vagy aláhúzást használhat. A terv azonosítója a **Létrehozás** gombra kattintva nem módosítható.

1. A **terv neve** mezőbe írjon be egy egyedi nevet ehhez a csomaghoz. Legfeljebb 50 karaktert használhat.

1. Válassza a **Létrehozás** lehetőséget.

## <a name="define-the-plan-listing"></a>A csomag listájának megadása

A **csomag listázása** lapon megadhatja a terv nevét és leírását, ahogy szeretné, hogy megjelenjenek a kereskedelmi piactéren.

1. A **terv neve** mezőben a tervhez korábban megadott név jelenik meg. Bármikor megváltoztathatja. Ez a név fog megjelenni a kereskedelmi piactéren az ajánlat szoftvercsomag címeként.

1. A **terv leírása** mezőben magyarázza el, hogy mi teszi ezt a szoftvercsomagot egyedivé, és hogy az ajánlaton belül más csomagoktól milyen különbségek vannak. Ez a Leírás legfeljebb 500 karaktert tartalmazhat.
1. A következő lapra történő továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: **díjszabás és rendelkezésre állás**.

## <a name="define-markets-pricing-and-availability"></a>Piacok, díjszabás és rendelkezésre állás meghatározása

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. A **díjszabás és rendelkezésre állás** lapon konfigurálhatja azokat a piacokat, amelyekre a csomag elérhető lesz, a kívánt bevételi modell, ár és számlázási feltételek alapján. Emellett azt is jelezheti, hogy a terv mindenki számára elérhető-e, vagy csak bizonyos ügyfeleknek (más néven privát csomagnak).

1. A **piacok** területen válassza a **piacok szerkesztése** hivatkozást.
1. A megjelenő párbeszédpanelen válassza ki azokat a piaci helyeket, amelyeken elérhetővé szeretné tenni a tervet. Legalább egy 141-es piacot ki kell választania.

   > [!NOTE]
   > Ezen a párbeszédpanelen egy keresőmező és egy olyan lehetőség található, amely csak a "Tax átutalt" országokra vonatkozik, amelyekben a Microsoft az Ön nevében kifizeti az értékesítési és használati adót.

1. A párbeszédpanel bezárásához kattintson a **Mentés** gombra.

## <a name="define-a-pricing-model"></a>Árképzési modell meghatározása

Az árképzési modellt minden csomaghoz hozzá kell rendelnie: vagy _átalány_ vagy _felhasználónként._ Az azonos ajánlat összes csomagjának ugyanazt az árképzési modellt kell használnia. Egy ajánlathoz például nem tartozhat egy átalánydíjas és egy másik, felhasználónként megvásárolt csomag. További információ: Saas- [díjszabási modellek](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Az ajánlat közzététele után a díjszabási modell nem módosítható. Emellett az ugyanarra az ajánlatra vonatkozó összes csomagnak ugyanazzal az árképzési modellel kell rendelkeznie.

### <a name="configure-flat-rate-pricing"></a>Átalánydíjas díjszabás konfigurálása

1. A **díjszabás és rendelkezésre állás** lapon, a **díjszabás** területen válassza az **átalány** lehetőséget.
1. Válassza a **havi** vagy az **éves** jelölőnégyzetet, vagy mindkettőt, majd adja meg az árat.

### <a name="add-a-custom-meter-dimension"></a>Egyéni mérési dimenzió hozzáadása

Ez a beállítás csak akkor érhető el, ha az átalány díjszabását választotta. További információ: [az SaaS díjas számlázása a kereskedelmi piactér mérési szolgáltatásával](./partner-center-portal/saas-metered-billing.md).

1. A **Marketplace-mérési szolgáltatás dimenziói** területen válassza az **Egyéni fogyasztásmérő-dimenzió hozzáadása (max. 30)** hivatkozást.
1. Az **azonosító** mezőben adja meg a nem módosítható azonosító hivatkozását a használati események kibocsátásakor.
1. A **megjelenítendő név** mezőbe írja be a dimenzióhoz társított megjelenítendő nevet. Például a "szöveges üzenetek elküldése".
1. A **Mértékegység** mezőben adja meg a számlázási egység leírását. Például: "szöveges üzenet" vagy "per 100 e-mail".
1. Az **egységenkénti ár USD** mezőben adja meg a dimenzió egy egységének árát.
1. Az **alapszintbe foglalt havi mennyiség** mezőben adja meg a havonta szereplő dimenzió mennyiségét (egész számként) az ismétlődő havi díjat fizető ügyfelek számára. Korlátlan mennyiség beállításához jelölje be a jelölőnégyzetet.
1. Az **alapszintbe foglalt éves mennyiség** mezőben adja meg a dimenzió (egész szám) mennyiségét, amely havonta szerepel az ismétlődő éves díj megfizetése esetén. Korlátlan mennyiség beállításához jelölje be a jelölőnégyzetet.
1. Egy másik egyéni mérési dimenzió hozzáadásához jelölje be a **további dimenzió hozzáadása** hivatkozást, majd ismételje meg az 1 – 7. lépést.

### <a name="configure-per-user-pricing"></a>Felhasználónkénti díjszabás beállítása

1. A **díjszabás és rendelkezésre állás** lapon, a **díjszabás** területen válassza a **felhasználónként** lehetőséget.
2. Ha alkalmazható, a **felhasználói korlátok** területen határozza meg a csomaghoz tartozó felhasználók minimális és maximális számát.
3. A **számlázási időszak** alatt a havi árat, az éves árat vagy mindkettőt kell megadnia.

### <a name="validate-custom-prices"></a>Egyéni árak ellenőrzése

Egyéni árak egyéni piacon történő beállításához exportálja, módosítsa, majd importálja a díjszabási táblázatot. Ön felelős a díjszabás ellenőrzésével és a beállítások tulajdonlásával. Részletes információ: [Egyéni árak](plans-pricing.md#custom-prices).

1. Először mentenie kell a díjszabási módosításokat, hogy engedélyezze a díjszabási adatai exportálását. A **díjszabás és rendelkezésre állás** lap alján kattintson a **Piszkozat mentése** gombra.
1. A **díjszabás** területen válassza ki az **exportálási díjszabási** hivatkozást.
1. Nyissa meg a exportedPrice.xlsx fájlt a Microsoft Excelben.
1. A számolótáblában végezze el a kívánt frissítéseket a díjszabási adatokhoz, majd mentse a következőt:. CSV-fájl.<br> Előfordulhat, hogy a fájl frissítése előtt engedélyeznie kell a szerkesztést az Excelben.
2. A díjszabás **és rendelkezésre állás** lapon, a **díjszabás** területen válassza a **díjszabási adat importálása** hivatkozást.
3. A megjelenő párbeszédpanelen kattintson az **Igen** gombra.
4. Válassza ki a frissített exportedPrice.xlsx fájlt, majd kattintson a **Megnyitás** gombra.

### <a name="enable-a-free-trial"></a>Ingyenes próbaverzió engedélyezése

Az ajánlat minden egyes csomagjának ingyenes próbaverzióját konfigurálhatja. Jelölje be a jelölőnégyzetet, ha engedélyezni szeretné az egy hónapos ingyenes próbaverziót. Ez a jelölőnégyzet nem érhető el a piactér-mérési szolgáltatást használó csomagokhoz. További információ: [ingyenes próbaverziók](plans-pricing.md#free-trials).

> [!IMPORTANT]
> Ha a visszagörgethető ajánlatot ingyenes próbaverzióval tették közzé, az adott csomag esetében nem tiltható le. Győződjön meg arról, hogy ez a beállítás megfelelő, mielőtt közzéteszi az ajánlatot, hogy ne kelljen újra létrehoznia a csomagot.

- Az **ingyenes próbaverzió** területen jelölje be az egy **hónapos ingyenes próbaverzió engedélyezése** jelölőnégyzetet.

## <a name="choose-who-can-see-your-plan"></a>Válassza ki, hogy ki láthatja a csomagot

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott közönség számára. A bérlői azonosítók használatával hozzáférést biztosíthat egy privát csomaghoz, amely tartalmazza az egyes hozzárendelt bérlői AZONOSÍTÓk leírását. Legfeljebb 10 bérlői azonosítót adhat hozzá manuálisan vagy legfeljebb 20 000 bérlői azonosítóhoz a használatával. CSV-fájl.

> [!NOTE]
> Ha közzétesz egy privát csomagot, később megtekintheti a nyilvános láthatóságot. Ha azonban egy nyilvános csomagot tesz közzé, a saját láthatósága nem módosítható.

### <a name="make-your-plan-public"></a>A csomag nyilvánosvé tétele

1. A **terv láthatósága** területen válassza a **nyilvános** mezőt.
1. Válassza a **Piszkozat mentése** lehetőséget, majd a lap bal felső részén kattintson a **terv áttekintése** lehetőségre a **terv áttekintése** lapra való visszatéréshez.
1. Ha egy másik csomagot szeretne létrehozni ehhez az ajánlathoz, a **terv áttekintése** lap tetején válassza az **+ új terv létrehozása** lehetőséget. Ezután ismételje meg a lépéseket a [terv létrehozása](#create-a-plan) szakaszban. Ellenkező esetben lépjen [a csomagok megtekintése](#view-your-plans)elemre.

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Bérlői azonosítók manuális hozzáadása egy privát csomaghoz 

1. A **terv láthatósága** területen jelölje be az **Ez egy privát csomag** jelölőnégyzetet.
1. A megjelenő **bérlői azonosító** mezőben adja meg annak a célközönségnek az Azure ad-BÉRLŐi azonosítóját, amelyhez hozzáférést kíván adni ehhez a privát csomaghoz. Legalább egy bérlői azonosítót kötelező megadni.
1. Választható Adja meg a célközönség leírását a **Leírás** mezőben.
1. Egy másik bérlői azonosító hozzáadásához ismételje meg a 2. és a 3. lépést.
1. Amikor elkészült a bérlői azonosítók hozzáadásával, válassza a **Piszkozat mentése** lehetőséget, majd a lap bal felső részén kattintson a **terv áttekintése** lehetőségre a **terv áttekintése** lapra való visszatéréshez.
1. Ha egy másik csomagot szeretne létrehozni ehhez az ajánlathoz, a **terv áttekintése** lap tetején válassza az **+ új terv létrehozása** lehetőséget. Ezután ismételje meg a lépéseket a [terv létrehozása](#create-a-plan) szakaszban. Ellenkező esetben lépjen [a csomagok megtekintése](#view-your-plans)elemre.

### <a name="use-a-csv-file-for-a-private-plan"></a>Használja a. Privát csomag CSV-fájlja

1. A **terv láthatósága** területen jelölje be az **Ez egy privát csomag** jelölőnégyzetet.
2. Válassza ki a **célközönség exportálása (CSV)** hivatkozást.
3. Nyissa meg a t. CSV-fájlt, és adja hozzá azokat az Azure-azonosítókat, amelyekhez hozzáférést szeretne adni a privát ajánlathoz az **azonosító** oszlophoz.
4. Szükség esetén megadhatja az egyes célközönségek leírását a **Leírás** oszlopban.
5. Adja hozzá a "TenantID" kifejezést a **Type (típus** ) oszlopban az egyes sorokhoz egy Azure-azonosítóval.
6. Mentse a t. CSV-fájl.
7. A **díjszabás és rendelkezésre állás** lapon, a **terv láthatósága** területen válassza a **célközönség importálása (CSV)** hivatkozást.
8. A megjelenő párbeszédpanelen válassza az **Igen** lehetőséget.
9. Válassza a elemet. CSV-fájl, majd válassza a **Megnyitás** lehetőséget.
10. Válassza a **Piszkozat mentése** lehetőséget, majd a lap bal felső részén kattintson a **terv áttekintése** lehetőségre a **terv áttekintése** lapra való visszatéréshez.
11. Ha egy másik csomagot szeretne létrehozni ehhez az ajánlathoz, a **terv áttekintése** lap tetején válassza az **+ új terv létrehozása** lehetőséget. Ezután ismételje meg a lépéseket a [terv létrehozása](#create-a-plan) szakaszban. Ellenkező esetben, ha elkészült a csomagok létrehozásával, ugorjon a következő szakaszra: **a csomagok megtekintése**.

## <a name="view-your-plans"></a>A csomagok megtekintése

Egy vagy több csomag létrehozása után megtekintheti a csomag nevét, a terv AZONOSÍTÓját, a díjszabási modellt, a rendelkezésre állást (nyilvános vagy magánjellegű), az aktuális közzétételi állapotot, valamint az összes elérhető műveletet a **terv áttekintése** lapon.

A **terv áttekintés** lapjának **művelet** oszlopában elérhető műveletek a csomag állapotától függően változhatnak, és a következőket foglalhatják magukban:

- Ha a terv állapota **Piszkozat**, a **művelet** oszlopban szereplő hivatkozás a **delete piszkozatot** fogja mondani.
- Ha a terv állapota **élő**, a **művelet** oszlopban található hivatkozás **vagy a** **saját célközönség szinkronizálása vagy a privát közönség szinkronizálása** nem történik meg. A **privát célközönség szinkronizálása** hivatkozás csak a privát célközönségek módosításait teszi közzé anélkül, hogy közzé kellene tenni az ajánlatban esetlegesen végrehajtott egyéb frissítéseket.

## <a name="before-you-publish-your-offer"></a>Az ajánlat közzététele előtt

Ha még nem tette meg, hozzon létre egy fejlesztési és tesztelési (fejlesztői) ajánlatot az ajánlat teszteléséhez, mielőtt közzéteszi az éles üzemi ajánlatot. További információt a [fejlesztési és tesztelési ajánlat létrehozása](create-saas-dev-test-offer.md)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan értékesítheti SaaS-ajánlatát](create-new-saas-offer-marketing.md) a **Microsofttal való közös értékesítéssel** és a **CSP** -programokkal való viszonteladással.
- [SaaS-ajánlat tesztelése és közzététele a kereskedelmi piactéren](test-publish-saas-offer.md).
