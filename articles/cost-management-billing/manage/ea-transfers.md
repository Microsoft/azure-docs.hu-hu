---
title: Azure Enterprise-átvitelek
description: Az Azure EA-átvitelek részleteit írja le.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7aa57fa20c3a043cdb210ccd8a5ddbf61323716d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943682"
---
# <a name="azure-enterprise-transfers"></a>Azure Enterprise-átvitelek

A cikk a vállalati átvitelekről nyújt áttekintést.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Vállalati fiók átvitele egy új regisztrációba

A fiókátvitellel egy fióktulajdonos áthelyezhető az egyik regisztrációból egy másikba. A fióktulajdonos alá tartozó összes kapcsolódó előfizetés átkerül a célregisztrációba. Akkor használjon fiókátvitelt, ha több aktív regisztráció van, és csak a kiválasztott fióktulajdonosokat szeretné áthelyezni.

Ez a szakasz csak tájékoztató célt szolgál, mert a műveletet vállalati rendszergazda nem végezheti el. Vállalati fiók új regisztrációba történő átviteléhez támogatási kérés leadása szükséges.

Amikor vállalati fiókokat visz át egy új regisztrációba, tartsa szem előtt a következőket:

- A rendszer csak a kérelemben megadott fiókokat viszi át. Ha az összes fiókot kiválasztotta, az átvitel mindegyikre vonatkozni fog.
- A forrásregisztráció megőrzi az állapotát (aktív vagy meghosszabbítva). Tovább folytathatja a használatát, amíg le nem jár.

### <a name="prerequisites"></a>Előfeltételek

A fiókátvitel kérésekor adja meg az alábbi adatokat:

- A célregisztráció számát, az átvinni kívánt fiók nevét és a fióktulajdonos e-mail-címét
- A forrásregisztrációhoz a regisztrációs számot és az átvinni kívánt fiókot

Egyéb szempontok, amelyeket érdemes észben tartani a fiókok átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására
- Ha egy fiókátvitel nem felel meg az elvárásainak, vegye fontolóra a regisztráció átvitelét.
- A fiókátvitel az adott fiókokhoz tartozó összes szolgáltatást és előfizetést átviszi.
- Az átvitel befejezését követően az átvitt fiók inaktívként jelenik meg a forrásregisztráció alatt, és aktívként a célregisztráció alatt.
- A fiók a záró dátumot a forrásregisztráció hatályos átviteli dátumaként jelenít meg és a célregisztráció kezdő dátumaként.
- A fiók esetében a hatályos átviteli dátum előtti bármilyen használat a forrásregisztrációhoz tartozik.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Vállalati regisztráció átvitele egy új regisztrációba

A regisztrációátvitel a következő esetben megfontolandó:

- A jelenlegi regisztráció előrefizetési időtartama véget ért.
- A regisztráció lejárt/meghosszabbított állapotban van, és egy új szerződés egyeztetése folyamatban van.
- Több regisztrációval rendelkezik, és egyetlen regisztráció keretében szeretné összevonni az összes fiókot és számlázást.

Ez a szakasz csak tájékoztató célt szolgál, mert a műveletet vállalati rendszergazda nem végezheti el. Támogatási kérelemre van szükség egy vállalati beléptetés új rendszerbe való átviteléhez, kivéve, ha a regisztráció az [automatikus beléptetési átvitelre](#auto-enrollment-transfer)jogosult.

Ha egy teljes vállalati regisztráció átvitelét kéri egy regisztrációba, a következő műveletek mennek végbe:

- A rendszer az összes Azure-szolgáltatást, előfizetést, fiókot, részleget, valamint a teljes regisztrációs struktúrát átviszi az új célregisztrációba, az EA-részlegek rendszergazdáival együtt.
- A regisztráció állapota _Átvitt_ értékre módosul. Az átvitt regisztrációt csak a korábbi használatról szóló jelentések elkészítéséhez lehet elérni.
- Az átvitt regisztrációkhoz nem adhat hozzá szerepköröket vagy előfizetéseket. Az átvitt állapot megakadályozza a regisztráción belüli további használatot.
- A szerződés fennmaradó előrefizetési Azure-egyenlege elveszik, a későbbi időszakokkal együtt.
-    Ha az Ön által átvitt beléptetés az RI-beszerzésekkel van elküldve, az RI-vásárlási díj továbbra is a forrás-regisztrációban marad, azonban az új regisztráció során a rendszer az összes RI kedvezményt átadja.
-    A piactér egyszeri vásárlási díja és a régi regisztráció során már felmerülő havi rögzített díjak nem kerülnek át az új regisztrációra. A használatalapú Marketplace-díjak átvitele megtörténik.

### <a name="effective-transfer-date"></a>Átvitel hatálybalépési dátuma

Az átvitel hatályba lépésének napja a célregisztráció kezdődátuma vagy egy későbbi időpont lehet.

A forrásregisztráció használatának költsége az Azure-előrefizetési keretből lesz levonva, vagy túlhasználatként lesz kiszámlázva. Az érvénybe léptetési dátum után megjelenő használat az új regisztrációra és számlázásra kerül.

### <a name="prerequisites"></a>Előfeltételek

A regisztrációátvitel kérésekor adja meg az alábbi adatokat:

- A forrásregisztrációhoz a regisztrációs számot.
- A célregisztrációhoz az átvitel céljának regisztrációs számát.
- A regisztrációátvitel hatálybalépési dátuma lehet a célregisztráció kezdő dátuma vagy egy későbbi időpont. A kiválasztott dátum nem befolyásolhatja a már kibocsátott túlhasználati számla használatát.

Egyéb szempontok, amelyeket érdemes észben tartani a regisztrációk átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására.
- Ha egy regisztrációátvitel nem felel meg az elvárásainak, vegye fontolóra a fiókátvitelt.
- A forrásregisztráció állapota átvitt lesz, és ezt a regisztrációt csak a korábbi használatról szóló jelentések elkészítéséhez lehet majd elérni.

### <a name="auto-enrollment-transfer"></a>Automatikus beléptetési átvitel

Láthatja, hogy egy beléptetési állapot **átadása** akkor is lehetséges, ha nem küldött támogatási jegyet a beléptetési átvitel igényléséhez. Az **átvitt** állapot az automatikus beléptetési továbbítási folyamat eredményeiből származik. Ahhoz, hogy az automatikus regisztrálási folyamat a megújítási kifejezésben szerepeljen, néhány elemnek szerepelnie kell az új szerződésben:

- Előzetes regisztrációs szám (az EA Portalon léteznie kell)
- Az előző regisztrációs szám lejárati dátuma egy nappal az új szerződés hatályba lépésének napja előtt
- Az új szerződés egy olyan számlázott Azure-előfizetési rendeléssel rendelkezik, amely aktuális dátummal vagy visszakeltezett rendelkezik
- Az új regisztráció az EA portálon jön létre

Ha nincs hiányzó használati adatok az EA-portálon a korábbi regisztráció és az új regisztráció között, akkor nem kell létrehoznia az átvitel támogatási jegyét.

### <a name="azure-prepayment"></a>Azure-előrefizetés

Az Azure-előrefizetést nem lehet átvinni egyik regisztrációból a másikba. Az Azure-előrefizetési egyenlegeket a szerződés ahhoz a regisztrációhoz köti, amelyhez megrendelték őket. A rendszer nem viszi át az Azure-előrefizetést a fiók vagy a regisztráció átviteli folyamatának részeként.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>A fiókok és a regisztrációk átvitele nem érinti a szolgáltatásokat

A fiókok és a regisztrációk átvitele során nincs állásidő. Ha az összes szükséges információt megadja, a kérelem benyújtásának napján is el lehet végezni.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Vállalati regisztráció átvitele egy használatalapú fizetéses előfizetésbe

Ha egy Enterprise-előfizetést használatalapú fizetésű önálló előfizetésbe szeretne átvinni, új támogatási kérést kell létrehoznia az Azure Enterprise portálon. Támogatási kérés létrehozásához válassza az **+ Új támogatási kérés** elemet a **Súgó és támogatás** területen.

## <a name="change-azure-subscription-or-account-ownership"></a>Az Azure-előfizetés vagy a fiók tulajdonjogának módosítása

Az Azure EA Portalon át lehet adni az előfizetéseket egyik fióktulajdonostól a másiknak. További információért lásd: [Az Azure-előfizetés vagy a fiók tulajdonjogának módosítása](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Az előfizetés-áthelyezés hatásai

Ha az Azure-előfizetést ugyanazon Azure Active Directory-bérlő egy másik fiókjába helyezi át, akkor az összes [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév megtartja az erőforrások kezeléséhez való hozzáférését.

Az előfizetéshez RBAC-hozzáféréssel rendelkező felhasználók megtekintése:

1. Az Azure Portalon nyissa meg az **Előfizetések** oldalt.
2. Válassza ki a megtekinteni kívánt előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** elemet.
3. Válassza a **Szerepkör-hozzárendelések** lehetőséget. A szerepkör-hozzárendelések oldala felsorolja az összes felhasználót, aki RBAC-hozzáféréssel rendelkezik az előfizetéshez.

Ha az előfizetést egy másik Azure AD-bérlő fiókjába helyezi át, akkor az összes [RBAC-vel](../../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév _elveszíti_ a hozzáférését az erőforrások kezeléséhez. Bár nincs RBAC-hozzáférés, mégis előfordulhat, hogy az előfizetést el lehet érni biztonsági mechanizmusokon keresztül, például:

- Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információért lásd: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../../cloud-services/cloud-services-certs-create.md).
- A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókok áttekintésében](../../storage/common/storage-account-overview.md) találhat.
- Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha a címzettnek korlátoznia kell a hozzáférését az Azure-erőforrásokhoz, érdemes megfontolnia a szolgáltatáshoz társított titkok frissítését. A legtöbb erőforrást a következő lépésekkel lehet frissíteni:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben válassza a **Minden erőforrás** elemet.
3. Válassza ki az erőforrást.
4. Az erőforrás oldalán válassza a **Beállítások** lehetőséget a meglévő titkos kulcsok megtekintéséhez és frissítéséhez.

## <a name="next-steps"></a>További lépések

- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](ea-portal-troubleshoot.md).