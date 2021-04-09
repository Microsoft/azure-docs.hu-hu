---
title: Datadoggal létrehozása – Azure partneri megoldások
description: Ez a cikk bemutatja, hogyan hozhat létre Datadoggal-példányt a Azure Portal használatával.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748638"
---
# <a name="quickstart-get-started-with-datadog"></a>Gyors útmutató: Ismerkedés a Datadoggal

Ebben a rövid útmutatóban a Datadoggal egy példányát fogja létrehozni. Létrehozhat egy új Datadoggal-szervezetet, vagy egy meglévő Datadoggal-szervezethez is kapcsolódhat.

## <a name="pre-requisites"></a>Előfeltételek

Az Azure Datadoggal-integráció beállításához **tulajdonosi** hozzáféréssel kell rendelkeznie az Azure-előfizetéshez. A telepítés megkezdése előtt győződjön meg arról, hogy rendelkezik a megfelelő hozzáféréssel.

Ha a Datadoggal-erőforráson belül szeretné használni a Security Assertion Markup Language (SAML) Single Sign-On (SSO) szolgáltatást, vállalati alkalmazást kell beállítania. Vállalati alkalmazás hozzáadásához a következő szerepkörök egyikére van szükség: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.

A vállalati alkalmazás beállításához kövesse az alábbi lépéseket:

1. Lépjen [Azure Portal](https://portal.azure.com). Válassza a **Azure Active Directory** lehetőséget.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet.
1. Válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból területen keressen rá a *datadoggal* kifejezésre. Válassza a keresés eredményét, majd válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Datadoggal alkalmazás a HRE Enterprise Galleryben." border="true":::

1. Az alkalmazás létrehozása után lépjen a Tulajdonságok elemre az oldal paneljén. Meg kell adni a **felhasználó-hozzárendelést?** **nem** értékre, majd válassza a **Mentés** lehetőséget.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="A Datadoggal alkalmazás tulajdonságainak beállítása" border="true":::

1. Nyissa **meg az egyszeri bejelentkezést** az oldalsó panelen. Ezután válassza az **SAML** lehetőséget.

   :::image type="content" source="media/create/saml-sso.png" alt-text="SAML-hitelesítés." border="true":::

1. Válassza az **Igen** lehetőséget, ha a rendszer felszólítja az **egyszeri bejelentkezési beállítások mentésére**.

   :::image type="content" source="media/create/save-sso.png" alt-text="Egyszeri bejelentkezés mentése a Datadoggal alkalmazáshoz" border="true":::

1. Az egyszeri bejelentkezés beállítása már befejeződött.

## <a name="find-offer"></a>Ajánlat keresése

A Datadoggal megkereséséhez használja a Azure Portal.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be.

1. Ha egy nemrégiben látogatott munkamenetben látogatta meg a **piactért** , válassza ki az ikont az elérhető lehetőségek közül. Egyéb esetben keressen rá a _Marketplace_ kifejezésre.

    :::image type="content" source="media/create/marketplace.png" alt-text="Piactér ikon.":::

1. A piactéren keresse meg a **datadoggal**.

1. A terv áttekintése képernyőn válassza a **beállítás + előfizetés** lehetőséget.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Datadoggal-alkalmazás az Azure Marketplace-en.":::

## <a name="create-a-datadog-resource-in-azure"></a>Datadoggal-erőforrás létrehozása az Azure-ban

A portál megjelenít egy űrlapot a Datadoggal-erőforrás létrehozásához.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Datadoggal-erőforrás létrehozása" border="true":::

Adja meg a következő értékeket.

|Tulajdonság | Leírás
|:-----------|:-------- |
| Előfizetés | Válassza ki a Datadoggal-erőforrás létrehozásához használni kívánt Azure-előfizetést. Tulajdonosi hozzáféréssel kell rendelkeznie. |
| Erőforráscsoport | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az [erőforráscsoport](../../azure-resource-manager/management/overview.md#resource-groups) egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. |
| Erőforrás neve | Adja meg a Datadoggal-erőforrás nevét. Ez a név lesz az új Datadoggal-szervezet neve, amikor új Datadoggal-szervezetet hoz létre. |
| Hely | Válassza az USA 2. nyugati régióját. Jelenleg az USA 2. nyugati régiója az egyetlen támogatott régió. |
| Datadoggal-szervezet | Új Datadoggal-szervezet létrehozásához válassza az **új** lehetőséget. Meglévő Datadoggal-szervezethez való hivatkozáshoz válassza a **meglévő** lehetőséget. |
| Díjszabási csomag | Új szervezet létrehozásakor válasszon az elérhető Datadoggal-csomagok listájáról. |
| Számlázási időszak | Havi. |

Ha egy meglévő Datadoggal-szervezethez kapcsolódik, tekintse meg a következő szakaszt. Ellenkező esetben válassza a **Tovább: mérőszámok és naplók** lehetőséget, és hagyja ki a következő szakaszt.

## <a name="link-to-existing-datadog-organization"></a>Csatolás meglévő Datadog-szervezethez

Az új Datadoggal-erőforrást az Azure-ban egy meglévő Datadoggal-szervezethez is összekapcsolhatja.

Válassza a **meglévő** lehetőséget az adatszervezetek számára, majd válassza **a hivatkozás az datadoggal szervezeti** elemre lehetőséget.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Hivatkozás meglévő Datadoggal-szervezetre." border="true":::

A hivatkozás egy Datadoggal hitelesítési ablakot nyit meg. Jelentkezzen be a Datadoggal.

Alapértelmezés szerint az Azure az aktuális Datadoggal-szervezetet az Datadoggal-erőforráshoz csatolja. Ha másik szervezethez szeretne hivatkozni, válassza ki a megfelelő szervezetet a hitelesítési ablakban az alább látható módon.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Válassza ki a megfelelő Datadoggal-szervezetet a csatoláshoz" border="true":::

## <a name="configure-metrics-and-logs"></a>Metrikák és naplók konfigurálása

Az Azure-erőforrás-címkék használatával konfigurálhatja, hogy mely metrikákat és naplókat kell elküldeni a Datadoggal. Adott erőforrásokra vonatkozó mérőszámokat és naplókat is hozzáadhat vagy kizárhat.

A **metrikák** küldésére vonatkozó szabályok a következők:

- Alapértelmezés szerint a rendszer az összes erőforráshoz, a virtuális gépekhez, a virtuálisgép-méretezési csoportokhoz és az App Service-csomagokhoz tartozó mérőszámokat gyűjti.
- A virtuális gépek, a virtuálisgép-méretezési csoportok és az App Service-csomagok, amelyek *tartalmazzák* a címkéket, metrikákat küldenek a datadoggal.
- A virtuális gépek, a virtuálisgép-méretezési csoportok és az App Service-csomagok a *kizárási* címkékkel nem küldenek mérőszámokat a datadoggal.
- Ha ütközés lép fel a befoglalási és a kizárási szabályok között, a kizárás elsőbbséget élvez

A **naplók** küldésére vonatkozó szabályok a következők:

- Alapértelmezés szerint a rendszer az összes erőforráshoz gyűjti a naplókat.
- Az olyan Azure-erőforrások, amelyek *tartalmazzák* a címkéket, naplókat küldenek a datadoggal.
- A  *kizárási* címkékkel rendelkező Azure-erőforrások nem küldenek naplókat a datadoggal.
- Ha ütközés lép fel a befoglalási és a kizárási szabályok között, a kizárás elsőbbséget élvez.

Az alábbi képernyőképen például egy címke szabály látható, ahol csak azok a virtuális gépek, a virtuálisgép-méretezési csoportok és az App Service-csomagok vannak címkézve, mint a *datadoggal = True* küldési metrikák a datadoggal.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Naplókat és mérőszámokat konfigurálhat." border="true":::

Az Azure-ból Datadoggal két típusú naplót lehet kiállítani.

1. **Előfizetési szintű naplók** – betekintést nyújt az erőforrásokon végzett műveletekre a [vezérlési síkon](../../azure-resource-manager/management/control-plane-and-data-plane.md). A szolgáltatás állapotára vonatkozó frissítéseket is tartalmazza. A tevékenység naplójának használatával meghatározhatja a mi, ki és mikor minden írási műveletet (PUT, POST, DELETE). Az egyes Azure-előfizetések egyetlen tevékenységi naplóval rendelkeznek.

1. **Azure-erőforrás-naplók** – betekintést nyújt az Azure-erőforrásokon az [adatsíkon](../../azure-resource-manager/management/control-plane-and-data-plane.md)végrehajtott műveletekre. Például egy Key Vault titkos kulcsának beolvasása egy adatsík-művelet. Vagy egy adatbázisra irányuló kérelem is egy adatsík-művelet. Az erőforrás-naplók tartalma az Azure-szolgáltatás és az erőforrás típusa szerint változik.

Az előfizetési szintű naplók Datadoggal való elküldéséhez válassza az **előfizetési tevékenység naplófájljainak küldése** lehetőséget. Ha ez a beállítás nincs bejelölve, a rendszer az előfizetési szint naplói egyikét sem továbbítja a Datadoggal.

Az Azure-erőforrás naplófájljainak Datadoggal való elküldéséhez válassza **Az Azure erőforrás-naplók küldése az összes megadott erőforráshoz** lehetőséget. Az Azure-erőforrás-naplók típusai a [Azure monitor erőforrás-naplózási kategóriákban](../../azure-monitor/essentials/resource-logs-categories.md)vannak felsorolva.  Az Azure-erőforrások Datadoggal való küldésére szolgáló naplók szűréséhez használja az Azure-erőforrás címkéit.  

A Datadoggal elküldett naplókat az Azure fogja felszámítani. További információ: az Azure Marketplace-partnereknek eljuttatott [platform-naplók díjszabása](https://azure.microsoft.com/pricing/details/monitor/) .

A metrikák és naplók konfigurálásának befejezése után válassza a **Next (tovább) lehetőséget: egyszeri bejelentkezés**.

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Ha a szervezete az Azure Active Directory identitás-szolgáltatóként használja, akkor az Azure Portalból Datadoggal is létrehozhat egyszeri bejelentkezést. Ha a szervezete más identitás-szolgáltatót használ, vagy ha jelenleg nem szeretne egyszeri bejelentkezést létrehozni, kihagyhatja ezt a szakaszt.

Ha a Datadoggal-erőforrást egy meglévő Datadoggal-szervezethez kapcsolja, akkor ebben a lépésben nem állíthatja be az egyszeri bejelentkezést. Ehelyett be kell állítania az egyszeri bejelentkezést a Datadoggal-erőforrás létrehozása után. További információ: az [egyszeri bejelentkezés újrakonfigurálása](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Egyszeri bejelentkezés a meglévő Datadoggal-szervezethez való kapcsolódáshoz." border="true":::

Ha az Azure Active Directoryn keresztül szeretne egyszeri bejelentkezést létrehozni, jelölje be az **egyszeri bejelentkezés engedélyezése Azure Active Directory használatával** jelölőnégyzetet.

A Azure Portal lekéri a megfelelő Datadoggal alkalmazást a Azure Active Directoryból. Az alkalmazás megfelel a korábbi lépésben megadott vállalati alkalmazásnak.

Válassza ki a Datadoggal-alkalmazás nevét.

:::image type="content" source="media/create/sso.png" alt-text="Engedélyezze az egyszeri bejelentkezést a Datadoggal." border="true":::

Kattintson a **Tovább gombra: címkék**.

## <a name="add-custom-tags"></a>Egyéni Címkék hozzáadása

Egyéni címkéket is megadhat az új Datadoggal-erőforráshoz. Adja meg a Datadoggal erőforrásra alkalmazni kívánt címkék nevét és érték párokat.

:::image type="content" source="media/create/tags.png" alt-text="Egyéni Címkék hozzáadása a Datadoggal-erőforráshoz." border="true":::

Ha befejezte a címkék hozzáadását, válassza a **Tovább: felülvizsgálat + létrehozás** elemet.

## <a name="review--create-datadog-resource"></a>Áttekintés + Datadoggal-erőforrás létrehozása

Tekintse át a beállításokat és a használati feltételeket. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget.

:::image type="content" source="media/create/review-create.png" alt-text="Tekintse át és hozza létre a Datadoggal-erőforrást." border="true":::

Az Azure üzembe helyezi a Datadoggal-erőforrást.

Amikor a folyamat befejeződik, válassza az **erőforrás** megnyitása lehetőséget a datadoggal-erőforrás megtekintéséhez.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Datadoggal erőforrás üzembe helyezése." border="true":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Datadoggal-erőforrás kezelése](manage.md)
