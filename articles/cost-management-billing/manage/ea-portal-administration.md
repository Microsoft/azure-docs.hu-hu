---
title: A nagyvállalati szerződéses Azure Portal adminisztrációja
description: Ez a cikk a rendszergazdák Azure EA Portalon elvégzendő gyakori feladatait ismerteti.
author: bandersmsft
ms.author: banders
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: 787f902311437e6ca8a679299ef7a28b34cee4ab
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728409"
---
# <a name="azure-ea-portal-administration"></a>A nagyvállalati szerződéses Azure Portal adminisztrációja

Ez a cikk a rendszergazdák Azure EA Portalon (https://ea.azure.com) ) elvégzendő gyakori feladatait ismerteti. Az Azure EA Portal egy online felügyeleti portál, amely segítséget nyújt az ügyfeleknek az Azure EA-szolgáltatások költségének kezelésében. Az Azure EA Portallal kapcsolatos bevezető információkért lásd: [Ismerkedés az Azure EA Portallal](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Regisztráció aktiválása

A szolgáltatás aktiválásához a kezdeti vállalati rendszergazda megnyitja az [Azure Enterprise portált](https://ea.azure.com), és bejelentkezik a meghívási e-mail e-mail-címével.

Ha Ön vállalati rendszergazdaként lett beállítva, nem kell megvárnia az aktiválási e-mail megérkezését. Az [Azure Enterprise portálon](https://ea.azure.com) bejelentkezhet a munkahelyi, iskolai vagy Microsoft-fiókjához tartozó e-mail-címével és jelszavával.

Ha több regisztrációval rendelkezik, válasszon ki egyet az aktiváláshoz. Alapértelmezés szerint csak az aktív regisztrációk jelennek meg. A regisztrációs előzmények megtekintéséhez törölje az **Aktív** beállítás jelölőnégyzetét az Azure Enterprise portál jobb felső sarkában.

A **Regisztráció** alatt az állapot **Aktív**.

![Aktív regisztrációt bemutató példa](./media/ea-portal-administration/ea-enrollment-status.png)

Csak meglévő Azure vállalati rendszergazdák hozhatnak létre más vállalati rendszergazdákat.

### <a name="create-another-enterprise-administrator"></a>Másik vállalati rendszergazda létrehozása

A helyzettől függően használja az alábbi lehetőségek egyikét.

#### <a name="if-youre-already-an-enterprise-administrator"></a>Ha Ön már vállalati rendszergazda

1. Jelentkezzen be az [Azure Enterprise portálra](https://ea.azure.com).
1. Lépjen a **Kezelés** > **Regisztráció részletei** menüpontra.
1. A lap jobb felső részén kattintson az **+Adminisztrátor hozzáadása** elemre.

Győződjön meg arról, hogy tudja a felhasználó e-mail-címét és előnyben részesített hitelesítési módszerét (például munkahelyi, iskolai vagy Microsoft-fiók használatával végzett hitelesítés).

#### <a name="if-youre-not-an-enterprise-administrator"></a>Ha Ön nem vállalati rendszergazda

Ha Ön nem vállalati rendszergazda, forduljon egy vállalati rendszergazdához, és kérje meg, hogy adja hozzá a regisztrációhoz. A vállalati rendszergazda az előző lépések alapján adja hozzá Vállalati rendszergazdaként. Miután hozzá lett adva egy regisztrációhoz, egy aktiválási e-mailt fog kapni.

#### <a name="if-your-enterprise-administrator-cant-help-you"></a>Ha a vállalati rendszergazda nem tud segíteni

Ha a vállalati rendszergazda nem tud segíteni Önnek, hozzon létre egy [támogatási kérést az Azure Enterprise portálon](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Adja meg az alábbi információkat:

- Regisztrációs szám
- Hozzáadandó e-mail-cím és hitelesítési típus (munkahelyi, iskolai vagy Microsoft-fiók)
- E-mailes jóváhagyás egy meglévő vállalati rendszergazdától
  - Ha a meglévő vállalati rendszergazda nem érhető el, forduljon a partneréhez vagy a szoftvertanácsadójához, és kérje meg, hogy a Mennyiségilicenc-szolgáltatási központ (VLSC) eszközön keresztül módosítsa a kapcsolattartási adatokat.

## <a name="create-an-azure-enterprise-department"></a>Azure Enterprise-részleg létrehozása

A vállalati rendszergazdák és a rendszerszintű rendszergazdák a részlegek segítségével rendszerezik a vállalati Azure-szolgáltatásokat és a használati adatokat, és készítenek róluk jelentéseket részleg és költséghely szerint. A vállalati rendszergazdák képesek:

- Részlegek felvételére vagy eltávolítására.
- Fiókok részlegekhez történő hozzárendelésére.
- Részlegszintű rendszergazdák létrehozására.
- Díjak és költségek megtekintésének engedélyezésére a részlegszintű rendszergazdák esetén.

A részlegszintű rendszergazdák új fiókokat adhatnak a részlegeikhez. Eltávolíthatnak fiókokat a részlegekből, de a regisztrációból nem.

Részleg hozzáadása:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A bal oldali panelen válassza a **Kezelés** lehetőséget.
1. Válassza a **Részleg** fület, majd a **+ Részleg hozzáadása** elemet.
1. Adja meg az információkat.
   A részleg neve az egyetlen kötelezően kitöltendő mező. A névnek legalább három karakterből kell állnia.
1. Amikor végzett, válassza a **Hozzáadás** elemet.

## <a name="add-a-department-administrator"></a>Részlegszintű rendszergazda hozzáadása

A részleg létrehozása után a vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és mindegyiket hozzárendelheti egy részleghez. A részlegszintű rendszergazdák a következő műveleteket hajthatják végre a részlegeiken:

- Más részlegszintű rendszergazdák létrehozására
- Olyan részlegtulajdonságok megtekintése és módosítása, mint a név vagy a költségközpont
- Fiókok hozzáadása
- Fiókok eltávolítása
- Használati adatok letöltése
- Havi használati adatok és költségek megtekintése <sup>1</sup>

> <sup>1</sup>A vállalati rendszergazdának kell megadnia ezeket az engedélyeket. Ha engedélyt kapott a részleg havi használati adatainak és díjainak megtekintésére, de nem látja őket, forduljon a partneréhez.

### <a name="to-add-a-department-administrator"></a>Részlegszintű rendszergazda hozzáadása

Vállalati rendszergazdaként:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A bal oldali panelen válassza a **Kezelés** lehetőséget.
1. Válassza a **Részleg** fület, majd válassza ki az adott részleget.
1. Válassza az **+ Adminisztrátor hozzáadása** elemet, és adja meg a kért adatokat.
1. Csak olvasási hozzáféréshez állítsa a **Csak olvasás** beállítást **Igen** értékre, majd válassza a **Hozzáadás** elemet.

![A részlegszintű rendszergazda hozzáadására szolgáló párbeszédpanelt bemutató példa](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Csak olvasási hozzáférés beállítása

A részlegszintű rendszergazdáknak adhat csak olvasási hozzáférést a.

- Ha létrehoz egy új részlegszintű rendszergazdát, állítsa a csak olvasási jogosultság beállítást **Igen** értékre.

- Meglévő részlegszintű rendszergazda szerkesztéséhez:
   1. Válassza ki a részleget, majd válassza a ceruza szimbólumot azon **Részlegszintű rendszergazda** elem mellett, amelyet szerkeszteni kíván.
   1. Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre, majd válassza a **Mentés** elemet.

A vállalati rendszergazdák automatikusan megkapják a részlegszintű rendszergazda engedélyeit.

## <a name="add-an-account"></a>Fiók hozzáadása

A fiókok és az előfizetések szerkezete befolyásolja azok felügyeletét és azt, hogy azok hogyan jelennek meg a számlákon és a jelentéseken. Jellemző szervezeti szerkezet például a vállalati részlegek, a funkció szerinti csapatok és a földrajzi helyszínek alapján történő bontás.

Fiók hozzáadásához:

1. Az Azure Enterprise portálon válassza a **Kezelés** elemet a bal oldali navigációs területen.
1. Válassza a **Fiók** fület. A **Fiók** lapon válassza a **+ Fiók hozzáadása** elemet.
1. Válasszon ki egy részleget, vagy hagyja hozzárendelés nélkül, majd válassza ki a kívánt hitelesítési típust.
1. Adjon meg egy felhasználóbarát nevet a fiók jelentéskészítéskor történő beazonosításához.
1. Adja meg a **Fióktulajdonos e-mail-címét** az új fiókhoz történő hozzárendeléshez.
1. Erősítse meg az e-mail-címet, majd válassza a **Hozzáadás** elemet.

![A fiókok listáját bemutató példa és a + Fiók hozzáadása lehetőség](./media/ea-portal-administration/create-ea-add-an-account.png)

Hozzáadhat egy másik fiókot a **Másik fiók hozzáadása** elemre, vagy a bal oldali eszköztár jobb alsó sarkában lévő **Hozzáadás** gombra kattintva.

A fióktulajdonjog megerősítéséhez:

1. Jelentkezzen be az Azure Enterprise portálra.
1. Állapot megtekintése.

   Az állapotnak **Függőben** állapotról **Kezdő és záró dátum** állapotra kell átváltania. A Kezdő és záró dátum a felhasználó első bejelentkezésének és a szerződés végének a dátuma.
1. Amikor megjelenik a **Figyelmeztetés** üzenet, a fiók tulajdonosának a **Folytatás** gombot kell kiválasztania a fiók aktiválásához, amikor először jelentkezik be az Azure Enterprise portálra.

## <a name="change-azure-subscription-or-account-ownership"></a>Az Azure-előfizetés vagy a fiók tulajdonjogának módosítása

A vállalati rendszergazdák az Azure Enterprise portál használatával átruházhatják a kiválasztott vagy az összes előfizetés fióktulajdonjogát egy regisztrációban.

Előfizetés- vagy fióktulajdonjog-átadás elvégzésekor a Microsoft frissíti a fióktulajdonost.

A tulajdonjog-átadás elindítása előtt ismerje meg az Azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) alábbi szabályzatait:

- Ha ugyanazon bérlő két szervezeti azonosítója közötti Azure-előfizetés- vagy fióktulajdonjog-átadást hajt végre, az Azure RBAC-szabályzatok és a meglévő szolgáltatásadminisztrátori és társadminisztrátori szerepkörök megmaradnak.
- Több bérlős előfizetés- vagy fióktulajdonjog-átadások az Azure RBAC-szabályzatok és a szerepkör-hozzárendelések elvesztését eredményezhetik.
- A szabályzatok és a rendszergazdai szerepkörök nem vihetők át különböző címtárakba. A szolgáltatásadminisztrátorokból a célfiók tulajdonosa lesz.
- Ha el szeretné kerülni az Azure RBAC szabályzatok és a szerepkör-hozzárendelések elvesztését a bérlők közötti előfizetés-átadáskor, győződjön meg arról, hogy **Az előfizetés áthelyezése a címzett Azure AD-bérlőjébe** jelölőnégyzet **nincs bejelölve**. Ez megőrzi a szolgáltatásokat, az Azure-szerepköröket és a szabályzatokat az aktuális Azure AD-bérlőn, és csak a fiók számlázási tulajdonjogát adja át.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="A kép azt mutatja, hogy az előfizetés Azure AD-bérlőnek történő áthelyezésére vonatkozó jelölőnégyzet nincs bejelölve" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


A fióktulajdonos módosítása előtt:

1. Az Azure Enterprise portálon tekintse meg a **Fiók** lapot, és határozza meg a forrásfiókot. A forrásfióknak aktívnak kell lennie.
1. Azonosítsa be a célfiókot, és győződjön meg arról, hogy aktív.

A fiók tulajdonjogának átvitele az összes előfizetésre vonatkozóan:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A navigációs terület bal oldalán válassza ki a **Kezelés** elemet.
1. Válassza a **Fiók** fület, és vigye a mutatót a fiók fölé.
1. Válassza a fióktulajdonos módosításának ikonját a jobb oldalon. Az ikon egy személyt ábrázol.  
    ![A Fióktulajdonos módosítása szimbólumot bemutató kép](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Válassza ki az átadás célfiókját, majd válassza a **Tovább** lehetőséget.
1. Ha Azure AD-bérlők között akarja átadni a fiók tulajdonjogát, jelölje be **Az előfizetés áthelyezése a címzett Azure AD-bérlőjébe** jelölőnégyzetet.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="A kép azt mutatja, hogy az előfizetés Azure AD-bérlőnek történő áthelyezésére vonatkozó jelölőnégyzet be van jelölve" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Erősítse meg az átvitelt, majd válassza a **Küldés** gombot.

A fiók tulajdonjogának átvitele egy előfizetésre vonatkozóan:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A navigációs terület bal oldalán válassza ki a **Kezelés** elemet.
1. Válassza a **Fiók** fület, és vigye a mutatót a fiók fölé.
1. Válassza az előfizetések átvitelének ikonját a jobb oldalon. Az ikon egy lapot ábrázol.  
    ![Az Előfizetések átvitele szimbólumot bemutató kép](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Válassza ki az előfizetés-átadás célfiókját, majd válassza a **Tovább** lehetőséget.
1. Ha Azure AD-bérlők között szeretné átadni az előfizetés tulajdonjogát, jelölje be **Az előfizetés áthelyezése a címzett Azure AD-bérlőjébe** jelölőnégyzetet.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="A kép azt mutatja, hogy az előfizetés Azure AD-bérlőnek történő áthelyezésére vonatkozó jelölőnégyzet be van jelölve" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Erősítse meg az átvitelt, majd válassza a **Küldés** gombot.


## <a name="associate-an-account-to-a-department"></a>Fiókok részlegekhez történő hozzárendelésére

A vállalati rendszergazdák a részlegekhez társíthatják a regisztrációban meglévő fiókokat.

### <a name="to-associate-an-account-to-a-department"></a>Fiókok részlegekhez történő hozzárendelése

1. Jelentkezzen be az Azure EA Portalra vállalati rendszergazdaként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Részleg** lehetőséget.
1. Vigye az egérmutatót a fiókot tartalmazó sor fölé, és válassza a jobbra található ceruza ikont.
1. Válassza ki a részleget a legördülő menüből.
1. Kattintson a **Mentés** gombra.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Meglévő fiók társítása használatalapú előfizetéssel

Ha már rendelkezik meglévő Microsoft Azure-fiókkal az Azure Portalon, akkor adja meg a társított iskolai, munkahelyi vagy Microsoft-fiókot, hogy a Nagyvállalati Szerződés regisztrációhoz rendelhesse.

### <a name="associate-an-existing-account"></a>Meglévő fiók hozzárendelése

1. Az Azure Enterprise portálon válassza a **Kezelés** elemet.
1. Válassza a **Fiók** fület.
1. Válassza a **+Fiók hozzáadása** elemet.
1. Adja meg a meglévő Azure-fiókhoz társított munkahelyi, iskolai vagy Microsoft-fiókot.
1. Erősítse meg a meglévő Azure-fiókhoz társított fiókot.
1. Adjon meg egy nevet a fiók jelentéskor történő azonosításához.
1. Válassza a **Hozzáadás** lehetőséget.
1. További fiók hozzáadásához válassza újra a **+ Fiók hozzáadása** lehetőséget, vagy térjen vissza a kezdőlapra a **Rendszergazda** gomb kiválasztásával.
1. Ha megtekinti a **Fiók** lapot, az újonnan hozzáadott fiók **Függőben** állapotban fog megjelenni.

### <a name="confirm-account-ownership"></a>Fiók tulajdonjogának megerősítése

1. Jelentkezzen be a munkahelyi, iskolai vagy Microsoft-fiókhoz társított e-mail-fiókba.
1. Nyissa meg az _„Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing”_ (A Microsoft mennyiségi licencelés meghívója fiókja aktiválására a Microsoft Azure Service-ben) című e-mail-értesítést.
1. Válassza a meghívóban a **Log into the Microsoft Azure Enterprise Portal** (Bejelentkezés a Microsoft Azure Enterprise Portalra) hivatkozást.
1. Válassza a **Bejelentkezés** lehetőséget.
1. Adja meg a munkahelyi, iskolai vagy Microsoft-fiók nevét és jelszavát a bejelentkezéshez és annak megerősítéséhez, hogy Ön a fiók tulajdonosa.

### <a name="azure-marketplace"></a>Azure Piactér

Bár a legtöbb előfizetés a használatalapú fizetési környezetből átváltható Azure Nagyvállalati Szerződésre, az Azure Marketplace szolgáltatásai nem. Az összes előfizetés és díj egyesített nézetéhez javasoljuk, hogy adja hozzá az Azure Marketplace-szolgáltatásokat az Azure Enterprise portálhoz.

1. Jelentkezzen be az Azure Enterprise portálra.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Regisztráció** fület.
1. Tekintse meg a **Regisztráció részletei** szakaszt.
1. Kattintson az Azure Marketplace mező jobb oldalán lévő ceruza ikonra az engedélyezéshez. Kattintson a **Mentés** gombra.

A fiók tulajdonosa így már megvásárolhatja a korábban használatalapú előfizetésben tulajdonolt Azure Marketplace-szolgáltatásokat.

Miután az új Azure Marketplace-előfizetéseket aktiválta az Azure EA-regisztrációjában, mondja le a használatalapú környezetben létrehozott Azure Marketplace-szolgáltatásokat. Ez a lépés kritikus fontosságú, így az Azure Marketplace-előfizetések nem válnak rossz állapotúvá, amikor a használatalapú fizetési eszköz lejár.

### <a name="msdn"></a>MSDN

Az MSDN-előfizetések automatikusan az MSDN Dev/Test szolgáltatássá alakulnak át, és az Azure EA-ajánlat elveszít minden meglévő pénzügyi kreditet.

### <a name="azure-in-open"></a>Azure in Open

Az Azure in Open licencprogramon keresztül vásárolt Azure-előfizetések Nagyvállalati Szerződéssel való társításával a fel nem használt kreditek elvesznek. Így azt javasoljuk, hogy használja fel az Azure in Open-előfizetéshez tartozó összes kreditet, mielőtt Nagyvállalati Szerződéshez adná hozzá a fiókját.  

### <a name="accounts-with-support-subscriptions"></a>Támogatási előfizetésekkel rendelkező fiókok

Ha az Ön Nagyvállalati Szerződése nem rendelkezik támogatási előfizetéssel, és egy meglévő fiókot támogatási előfizetéssel ad hozzá az Azure Enterprise portálhoz, akkor a MOSA támogatási előfizetésének átvitele automatikusan nem történik meg. Újra meg kell vásárolnia egy támogatási előfizetést az Azure EA-ban a türelmi idő alatt a következő hónap végéig.

## <a name="department-spending-quotas"></a>Részleg költségkvótái

Az EA-ügyfelek megadhatják vagy módosíthatják a részlegek költségkvótáit a regisztráción belül. A költségkvóta összege az aktuális előrefizetési időszakra állítható be. Az aktuális előrefizetési időszak végén a rendszer meghosszabbítja a meglévő költségkvótát a következő előrefizetési időszakra, kivéve, ha az értékek frissülnek.

A részlegszintű rendszergazda megtekintheti a költségkvótát, de csak a vállalati rendszergazda frissítheti a kvóta összegét. A vállalati rendszergazda és a részlegszintű rendszergazda értesítést kap a kvóta 50, 75, 90 és 100 százalékának elérésekor.

### <a name="enterprise-administrator-to-set-the-quota"></a>A vállalati rendszergazda a következőképp állíthatja be a kvótát:

 1. Nyissa meg az Azure EA Portalt.
 1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
 1. Válassza a **Részleg** lapot.
 1. Válassza ki a részleget.
 1. Válassza a ceruza szimbólumot a Részleg adatai részben, vagy válassza a **+ Részleg hozzáadása** lehetőséget, ha egy új részleggel együtt szeretne költségkvótát hozzáadni.
 1. A Részleg adatai területen adja meg a költségkvóta összegét a regisztráció pénznemében a Költségkvóta $ mezőben (0-nál nagyobb értéket kell megadni).
    - Jelenleg a Részleg neve és a Költséghely is szerkeszthető.
 1. Kattintson a **Mentés** gombra.

A részleg költségkvótája mostantól megjelenik a Részleg fül részlegek listáját tartalmazó nézetében. A jelenlegi előrefizetési időszak végén az Azure EA Portal fenntartja a költségkvótákat a következő előrefizetési időszakra.

A részlegkvóta összege független az aktuális Azure-előrefizetéstől, és a kvóta összege és a riasztások csak belső használatra szolgálnak. A részleg költségkvótái csak tájékoztató jellegűek, és nem kényszerítenek a költségkeretek betartására.

### <a name="department-administrator-to-view-the-quota"></a>A részlegszintű rendszergazda így tekintheti meg a kvótát:

1. Nyissa meg az Azure EA Portalt.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Részleg** lapot, és tekintse meg a részlegek listáját tartalmazó nézetet a költségkvótákkal.

Ha Ön közvetett ügyfél, a költségfunkciókat a csatornapartnerének kell engedélyeznie.

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az Azure EA Portal segítséget nyújt az Azure EA-költségek és -használat felügyeletéhez. Az Azure EA Portalon három fő szerepkör van:

- EA-rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos

Minden szerepkör különböző szintű hozzáféréssel és jogosultsággal rendelkezik.

A felhasználói szerepkörökkel kapcsolatos további információkért lásd: [Vállalati felhasználók szerepkörei](./understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Azure EA-fiók hozzáadása

Az Azure EA-fiók az Azure EA Portal szervezeti egysége. Ez a fiók az előfizetések felügyeletére, valamint jelentéskészítésre használatos. Az Azure-szolgáltatások eléréséhez és használatához létre kell hoznia vagy hozatnia egy fiókot.

Az Azure-fiókokkal kapcsolatos további információkért lásd a [fiók hozzáadását](#add-an-account) ismertető szakaszt.

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test ajánlat

Az Azure vállalati rendszergazdájaként engedélyezheti a szervezeten belüli fióktulajdonosok számára, hogy előfizetéseket hozzanak létre az EA Dev/Test ajánlat alapján. Ezt a fióktulajdonos Dev/Test jelölőnégyzetének bejelölésével teheti meg az Azure EA Portalon.

Ha bejelölte a Dev/Test jelölőnégyzetet, tudassa a fióktulajdonossal, hogy be tudja állítani a Dev/Test-előfizetőkből álló csapata számára szükséges EA Dev/Test-előfizetéseket.

Ez lehetővé teszi, hogy az aktív Visual Studio-előfizetők speciális fejlesztési és tesztelési díjszabás mellett futtassanak fejlesztési és tesztelési számítási feladatokat az Azure-ban. Hozzáférést biztosít a Dev/Test-képek teljes galériájához, beleértve a Windows 8.1 és a Windows 10 rendszert.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Az Enterprise Dev/Test ajánlat beállítása:

1. Jelentkezzen be vállalati rendszergazdaként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Fiók** fület.
1. Válassza annak a fióknak a sorát, amelyhez engedélyezni szeretné a Dev/Test-hozzáférést.
1. Válassza a sortól jobbra található ceruza szimbólumot.
1. Jelölje be a Dev/Test jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

Ha egy felhasználót fióktulajdonosként adnak hozzá az Azure EA Portalon keresztül, akkor a fióktulajdonoshoz társított Azure-előfizetések, amelyek a használatalapú Dev/Test ajánlaton vagy a Visual Studio-előfizetőknek szóló havi kreditajánlatokon alapulnak, az EA Dev/Test ajánlatra lesznek konvertálva. A fióktulajdonoshoz társított egyéb ajánlattípusokon (például használatalapú ajánlattípusokon) alapuló előfizetések Microsoft Azure Enterprise-ajánlatokra lesznek konvertálva.

A Dev/Test ajánlat jelenleg nem érhető el az Azure Government-ügyfelek számára.

## <a name="create-a-subscription"></a>Előfizetés létrehozása

A fióktulajdonosok megtekinthetik és kezelhetik az előfizetéseket. Az előfizetésekkel hozzáférést adhat a szervezeti csoportoknak a fejlesztési környezetekhez és a projektekhez. Például a teszteléshez, az éles környezethez, a fejlesztéshez és az előkészítéshez.

Amikor különböző előfizetéseket hoz létre az egyes alkalmazáskörnyezetekhez, elősegíti a környezetek védelmét.

- Mindegyik előfizetéshez különböző szolgáltatásadminisztrátori fiókot rendelhet.
- Az előfizetéseket korlátlan számú szolgáltatáshoz hozzárendelheti.
- A fiók tulajdonosa hoz létre előfizetéseket, és rendel hozzá egy szolgáltatásadminisztrátori fiókot a fiókjában lévő összes előfizetéshez.

### <a name="add-a-subscription"></a>Előfizetés hozzáadása

Az alábbiak alapján adhat hozzá előfizetést.

Amikor első alkalommal ad előfizetést a fiókjához, el kell fogadnia a Microsoft Online Előfizetői Szerződést (MOSA) és egy díjkonstrukciót. Bár a Nagyvállalati Szerződéssel rendelkező ügyfelekre nem vonatkoznak, a MOSA és a díjkonstrukció szükséges az előfizetés létrehozásához. A Microsoft Azure Nagyvállalati Szerződés regisztrációs kiegészítése felülírja a fenti elemeket, így az Ön szerződéses jogviszonya nem módosul. Amikor a rendszer kéri, jelölje be a feltételek elfogadására szolgáló jelölőnégyzetet.

Előfizetés létrehozásakor a _Microsoft Azure Enterprise_ az alapértelmezett név. Megváltoztathatja a nevet, hogy megkülönböztesse az új előfizetést a regisztráció többi előfizetésétől, és gondoskodhat arról, hogy az felismerhető legyen a vállalati szintű jelentésekben.

Előfizetés hozzáadásához:

1. Az Azure Enterprise portálon jelentkezzen be a fiókba.
1. Válassza a **Rendszergazda** fület, majd az **Előfizetés** elemet az oldal tetején.
1. Ellenőrizze, hogy a fiók tulajdonosaként jelentkezett-e be.
1. Válassza az **+Előfizetés hozzáadása**, majd a **Vásárlás** elemet.

   Amikor első alkalommal ad előfizetést a fiókjához, meg kell adnia a kapcsolattartási adatait. További előfizetések hozzáadásakor a rendszer hozzáadja a megadott kapcsolattartási adatait.

1. Válassza az **Előfizetések** elemet, majd válassza ki a létrehozott előfizetést.
1. Válassza **Az előfizetés adatainak szerkesztése** elemet.
1. Módosítsa az **Előfizetés neve** és a **Szolgáltatásadminisztrátor** adatait, majd jelölje be a jelölőnégyzetet.

   Az előfizetés neve megjelenik a jelentéseken. Ez a fejlesztési portálon az előfizetéshez rendelt projekt neve.

Akár 24 órába is telhet, amíg az új előfizetések megjelennek az előfizetések listáján. Az előfizetés létrehozása után elvégezheti az alábbiakat:

- [Az előfizetés adatainak szerkesztése](https://portal.azure.com)
- [Az előfizetési szolgáltatások kezelése](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Előfizetés törlése

Egy olyan előfizetés törlése, amely esetében Ön a fióktulajdonos:

1. Jelentkezzen be az Azure Portalra a fiókjához tartozó hitelesítő adatokkal.
1. A központi menüben válassza az **Előfizetések** elemet.
1. Az előfizetések lapján, az oldal bal felső sarkában válassza ki azt az előfizetést, amelyet le szeretne mondani, és válassza az **Előfizetés megszüntetése** lehetőséget a megszüntetési lap megnyitásához.
1. Írja be az előfizetés nevét, válassza ki a megszüntetés okát, majd válassza az **Előfizetés megszüntetése** lehetőséget.

Csak a fiókadminisztrátorok szüntethetik meg az előfizetéseket.

További információ: [Mi történik az előfizetés lemondása után?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation)

## <a name="delete-an-account"></a>Fiók eltávolítása

A fiókeltávolítást csak aktív előfizetésekkel nem rendelkező aktív fiókok esetében lehet elvégezni.

1. Az Enterprise Portalon válassza a **Kezelés** lehetőséget a bal oldali navigációs sávon.
1. Válassza a **Fiók** fület.
1. A fiókok táblázatából válassza ki a törölni kívánt fiókot.
1. Válassza a fiók sorának jobb oldalán található X szimbólumot.
1. Ha a fiók nem rendelkezik aktív előfizetéssel, válassza a fiók sora alatti **Igen** lehetőséget a fiók eltávolításának megerősítéséhez.

## <a name="update-notification-settings"></a>Frissítési értesítés beállításai

A rendszer automatikusan regisztrálja a vállalati rendszergazdákat, hogy megkapják a regisztrációjukkal kapcsolatos használati értesítéseket. A vállalati rendszergazdák módosíthatják az értesítések időközét, vagy teljesen ki is kapcsolhatják őket.

Az értesítési kapcsolattartók az Azure EA Portal **Értesítési kapcsolattartó** területén jelennek meg. Az értesítési kapcsolattartók kezelésével gondoskodhat arról, hogy a szervezet megfelelő tagjai kapják meg az Azure EA-értesítéseket.

Az aktuális értesítési beállítások megtekintése:

1. Az Azure EA Portalon lépjen a **Kezelés** > **Értesítési kapcsolattartó** elemre.
2. E-mail-cím – A vállalati rendszergazda Microsoft-fiókjához, illetve munkahelyi vagy iskolai fiókjához társított e-mail-cím, amelyre a rendszer az értesítéseket küldi.
3. Számlázatlan egyenlegről szóló értesítés gyakorisága – Az egyes vállalati rendszergazdáknak küldött értesítésekhez beállított gyakoriság.

Kapcsolattartó hozzáadása:

1. Válassza a **+Kapcsolattartó hozzáadása** lehetőséget.
2. Írja be az e-mail-címet, majd erősítse meg.
3. Kattintson a **Mentés** gombra.

Az új értesítési kapcsolattartó megjelenik az **Értesítendő fél** területen. Az értesítés gyakoriságának módosításához válassza ki az értesítendő felet, majd válassza a kiválasztott sor jobb oldalán található ceruza szimbólumot. Állítsa be a gyakorisághoz a **naponta**, **hetente**, **havonta** vagy a **nincs** értéket.

Figyelmen kívül hagyhatja a _közelgő fedezeti időszak záró dátumát_, valamint _letilthatja és megszüntetheti a közelgő dátummal_ kapcsolatos életciklus-értesítéseket. Az életciklus-értesítések letiltásával figyelmen kívül hagyja a fedezeti időszakról és a szerződés záró dátumáról szóló értesítéseket.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-ajánlat

Az Azure Sponsorship-ajánlat korlátozott számú szponzorált Microsoft Azure-fiókot takar. A Microsoft által kiválasztott, korlátozott számú ügyfél az e-mailben kapott meghívóval csatlakozhat hozzá. Ha Ön jogosult a Microsoft Azure Sponsorship-ajánlatára, akkor kapni fog egy e-mailes meghívót a fiókazonosítójára.

További információért hozzon létre egy [támogatási kérést a szponzorálás aktiválásához](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Váltás munkahelyi vagy iskolai fiókkal történő hitelesítésre

Az Azure Enterprise-felhasználók Microsoft-fiókról (MSA vagy Live ID) Azure Active Directoryt használó munkahelyi vagy iskolai fiókkal történő hitelesítési típusra válthatnak.

A kezdéshez:

1. Adja hozzá a munkahelyi vagy iskolai fiókot az Azure EA Portalhoz a szükséges szerepkörrel/szerepkörökkel.
1. Ha hibaüzenet jelenik meg, előfordulhat, hogy a fiók nem érvényes az Active Directoryban.  Az Azure egyszerű felhasználónevet (UPN) használ, amely nem mindig egyezik meg az e-mail-címmel.
1. Az Azure EA Portalon történő hitelesítéshez a munkahelyi vagy iskolai fiókot használhatja.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Microsoft-fiókokból származó előfizetések átváltása munkahelyi vagy iskolai fiókokra:

1. Jelentkezzen be a felügyeleti portálra azzal a Microsoft-fiókkal, amely az előfizetések tulajdonosa.
1. Az új fiókra való váltáshoz végezze el a fiók tulajdonjogának átruházását.
1. A Microsoft-fiók most már nem rendelkezik aktív előfizetésekkel, és törölhető.
1. A törölt fiókok korábbi számlázási okokból inaktív állapotban megtekinthetőek maradnak a portálon.  Kiszűrheti őket a nézetből, ha bejelöli a kizárólag aktív fiókok megjelenítésére vonatkozó jelölőnégyzetet.

## <a name="azure-ea-term-glossary"></a>Azure EA-kifejezések szószedete

- **Fiók**: Az Azure Enterprise portálon található szervezeti egység. Az előfizetések felügyeletére, valamint jelentéskészítésre szolgál.
- **Fióktulajdonos**: Az Azure-ban az előfizetések és a szolgáltatásadminisztrátorok kezeléséhez azonosított személy. Megtekinthetik ezen fiók és a társított előfizetéseinek használati adatait.
- **Módosítási előfizetés**: A regisztrációs módosítás alá eső egy egyéves vagy egybeeső előfizetés.
- **Előrefizetés**: Az Azure-szolgáltatásokra kedvezményes előrefizetési díjszabással fizetett éves pénzügyi összeg előrefizetése az előrefizetéssel való használathoz.
- **Részlegszintű rendszergazda**: A részlegek kezelésére, új fiókok és fióktulajdonosok létrehozására, az általuk kezelt részlegek használati adatainak megtekintésére és megadott engedélyek esetén a költségek megtekintésére jogosult személy.
- **Regisztrációs szám**: A Microsoft által a Nagyvállalati Szerződéshez társított adott regisztráció azonosítására megadott egyedi azonosító.
- **Vállalati rendszergazda**: Az Azure-on a részlegek, részlegtulajdonosok, fiókok és fióktulajdonosok kezelésére jogosult személy. Képesek kezelni a vállalati rendszergazdákat, valamint megtekinthetik a használati adatokat, a számlázott mennyiségeket és a nem számlázott díjakat a nagyvállalati regisztrációhoz társított összes fiókra és előfizetésre vonatkozóan.
- **Nagyvállalati szerződés**: Központosított vásárlást használó Microsoft-licencszerződés olyan ügyfelek számára, akik a Microsoft technológiájára alapozva szeretnék szabványosítani teljes szervezetüket, és szabványos Microsoft-szoftverekkel szeretnék fenntartani az informatikai infrastruktúrájukat.
- **Nagyvállalati szerződéses regisztráció**: Regisztráció a Nagyvállalati Szerződési programba, amely kedvezményes áron nyújt Microsoft-termékeket nagy mennyiségben.
- **Microsoft-fiók**: Webalapú szolgáltatás, amellyel a részt vevő helyek egyetlen hitelesítőadat-készlettel hitelesíthetik a felhasználókat.
- **Microsoft Azure nagyvállalati regisztrációs módosítás (regisztrációs módosítás)** : Egy vállalat által aláírt módosítás, amely a vállalati regisztráció részeként hozzáférést biztosít számukra az Azure-hoz.
- **Azure Enterprise portál**: A vállalati ügyfeleink által az Azure-fiókok és a kapcsolódó előfizetések kezeléséhez használt portál.
- **Felhasznált erőforrások mennyisége**: Az egy adott hónapban felhasznált egyes Azure-szolgáltatások mennyisége.
- **Szolgáltatásadminisztrátor**: Az Azure Enterprise portálon az előfizetések és fejlesztési projektek elérésére és kezelésére jogosult személy.
- **Előfizetés**: Egy Azure Enterprise portál-előfizetést jelöl, és az ugyanazon szolgáltatásadminisztrátor által kezelt Azure-szolgáltatások tárolója.
- **Munkahelyi vagy iskolai fiók**: Olyan szervezetek esetén, amelyek felhőbeli összevonással állították be az Azure Active Directoryt, és amelyek esetében az összes fiók egyetlen bérlőn található.

### <a name="enrollment-statuses"></a>Regisztrációs állapotok

- **Újdonság**: Ez az állapot egy 24 órán belül létrehozott regisztrációhoz van hozzárendelve, és 24 órán belül Függőben állapotba kerül.
- **Függőben**: A regisztrációs rendszergazdának be kell jelentkeznie az Azure Enterprise portálra. Ha bejelentkezett, a regisztráció aktív állapotra vált.
- **Aktív**: A regisztráció aktív, és az Azure Enterprise portálon fiókok és előfizetések hozhatók létre. A regisztráció a Nagyvállalati Szerződés záró dátumáig aktív marad.
- **Határozatlan időre meghosszabbítva**: A határozatlan időre meghosszabbítva állapot a Nagyvállalati Szerződés záró dátuma után jelenik meg. Lehetővé teszi a meghosszabbított időszakot választó, Nagyvállalati Szerződéssel rendelkező Azure EA-ügyfelek számára, hogy a Nagyvállalati Szerződésük végén továbbra is határozatlan ideig használják az Azure-szolgáltatásokat.

   Mielőtt az Azure EA-regisztráció elérné a Nagyvállalati Szerződés záró dátumát, a regisztrációs rendszergazdának el kell döntenie, hogy az alábbi lehetőségek melyikével szeretne élni:

  - Megújítja a regisztrációt további Azure-előrefizetés hozzáadásával.
  - Áttér egy új regisztrációba.
  - Migrálást végez a Microsoft Online Előfizetési Programba (MOSP).
  - Jóváhagyja a regisztrációhoz társított szolgáltatások letiltását.
- **Lejárt**: Az Azure EA-ügyfél nem kérte az időtartam meghosszabbítását, és az Azure EA-regisztráció elérte a Nagyvállalati Szerződés záró dátumát. A regisztráció le fog járni, és az összes kapcsolódó szolgáltatás le lesz tiltva.
- **Átvitt**: Az átvitt állapotban jelennek meg azok a regisztrációk, amelyek társított fiókjainak és szolgáltatásainak mindegyikét egy új regisztrációba vitték át.
  >[!NOTE]
  > A regisztrációk nem kerülnek át automatikusan, ha a megújítás során új regisztrációs szám jön létre. Az előző regisztrációs számának szerepelnie kell a megújítási dokumentumaiban az automatikus átvitel megkönnyítése érdekében.

## <a name="next-steps"></a>További lépések

- Olvassa el, hogyan takaríthat meg pénzt a [virtuális gépek lefoglalásával](ea-portal-vm-reservations.md).
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](ea-portal-troubleshoot.md).
- Az [EA-Cost Management + Billing tulajdonjogára](../cost-management-billing-faq.yml) vonatkozó kérdésekért olvassa el a gyakori kérdéseket.