---
title: Csoportos licenceléssel kapcsolatos további forgatókönyvek – Azure AD | Microsoft Docs
description: További forgatókönyvek Azure Active Directory csoport alapú licenceléshez
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72a28aebe71809f3e47f9cc6936366cd027a32bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546675"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Forgatókönyvek, korlátozások és ismert problémák csoportok használatával a licencelés kezeléséhez Azure Active Directory

Az alábbi információk és példák segítségével összetettebb ismereteket szerezhet Azure Active Directory (Azure AD) csoport alapú licencelésről.

## <a name="usage-location"></a>Használat helye

Nem minden Microsoft-szolgáltatás érhető el minden területen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, a rendszergazdának meg kell adnia a **használat helye** tulajdonságot a felhasználónál. [A Azure Portal](https://portal.azure.com)a **felhasználói** &gt; **profil** &gt; **beállításaiban** megadhatja a használati helyet.

A csoport licencének hozzárendelésekor a megadott használati hely nélküli felhasználók öröklik a címtár helyét. Ha több helyen is vannak felhasználók, ügyeljen arra, hogy a felhasználói erőforrásokban megfelelően tükrözze, mielőtt felhasználókat adna hozzá a licencekhez.

> [!NOTE]
> A csoportos licenc-hozzárendelés soha nem módosítja egy felhasználó meglévő használati helyének értékét. Azt javasoljuk, hogy mindig a használati helyet állítsa be a felhasználói létrehozási folyamat részeként az Azure AD-ben (például HRE-kapcsolat konfigurálása), amely biztosítja, hogy a licenc-hozzárendelés eredménye mindig helyes legyen, és a felhasználók nem kapnak olyan helyeket, amely nem engedélyezett.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Csoport alapú licencelés használata dinamikus csoportokkal

A Group-alapú licencelést bármely biztonsági csoporttal használhatja, ami azt jelenti, hogy az Azure AD dinamikus csoportjaival kombinálható. A dinamikus csoportok szabályokat futtatnak a felhasználói erőforrás attribútumain a felhasználók csoportokból való automatikus hozzáadásához és eltávolításához.

Létrehozhat például egy dinamikus csoportot néhány olyan termékhez, amelyet hozzá szeretne rendelni a felhasználókhoz. Minden csoportot egy szabály tölt fel, amely a felhasználókat az attribútumaik szerint adja hozzá, és minden csoporthoz hozzá van rendelve a kapni kívánt licencek. A helyszíni attribútumot hozzárendelheti, és szinkronizálhatja az Azure AD-vel, vagy közvetlenül a felhőben is kezelheti az attribútumot.

A licencek hozzárendelése a felhasználóhoz röviddel a csoportba való felvételük után történik. Ha az attribútum megváltozik, a felhasználó elhagyja a csoportokat, és a licencek el lesznek távolítva.

### <a name="example"></a>Példa

Tekintse át a helyszíni Identitáskezelő megoldás példáját, amely eldönti, hogy mely felhasználók férhetnek hozzá a Microsoft Web Serviceshez. A **extensionAttribute1** használatával tárolja a felhasználó által birtokolt licenceket jelképező karakterlánc-értéket. Azure AD Connect szinkronizálja az Azure AD-vel.

Előfordulhat, hogy a felhasználóknak egy licencre van szükségük, de nem egy másikra, vagy mindkettőre szüksége lehet. Íme egy példa, amelyben az Office 365 Enterprise E5-és Enterprise Mobility + Security-(EMS-) licenceket a csoportba tartozó felhasználók számára terjeszti:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: alapszolgáltatások

![Képernyőfelvétel az Office 365 Enterprise E5 alapszolgáltatásairól](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licenccel rendelkező felhasználók

![Képernyőkép a Enterprise Mobility + Security licenccel rendelkező felhasználókról](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Ehhez a példához módosítsa az egyik felhasználót, és állítsa be a extensionAttribute1 a értékre, `EMS;E5_baseservices;` Ha azt szeretné, hogy a felhasználó mindkét licenccel rendelkezzen. Ezt a módosítást a helyszínen végezheti el. Miután a módosítás szinkronizálva lett a felhővel, a rendszer automatikusan hozzáadja a felhasználót mindkét csoporthoz, és hozzárendeli a licenceket.

![A felhasználó extensionAttribute1 beállítását bemutató képernyőfelvétel](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Meglévő csoport tagsági szabályának módosításakor legyen körültekintő. Egy szabály módosításakor a rendszer újraértékeli a csoport tagságát, és az új szabálynak nem megfelelő felhasználókat eltávolítja (a folyamat nem érinti azokat a felhasználókat, akik továbbra is megfelelnek az új szabálynak). Ezek a felhasználók a folyamat során törlődnek a licenccel, ami a szolgáltatás elvesztését okozhatja, vagy bizonyos esetekben adatvesztést eredményezhet.
> 
> Ha nagy dinamikus csoporttal rendelkezik, akkor a licenc-hozzárendeléstől függ, hogy a fő csoportba való alkalmazása előtt érdemes-e a kisebb tesztelési csoportok jelentősebb módosításait érvényesíteni.

## <a name="multiple-groups-and-multiple-licenses"></a>Több csoport és több licenc

Egy felhasználó több, licenccel rendelkező csoport tagja is lehet. Íme néhány megfontolandó szempont:

- Ugyanannak a terméknek több licence is átfedésben van, és az összes engedélyezett szolgáltatást alkalmazza a felhasználóra. Az alábbi példa két licencelési csoportot mutat be: az *E3 alapszolgáltatások* az elsőként üzembe helyezett alapszolgáltatásokat tartalmazzák az összes felhasználó számára. Az *E3 bővített szolgáltatásai* pedig további szolgáltatásokat (Sway és Planner) tartalmaznak, amelyek csak bizonyos felhasználók számára telepíthetők. Ebben a példában a felhasználó mindkét csoporthoz hozzá lett adva:

  ![Az engedélyezett szolgáltatások képernyőképe](./media/licensing-group-advanced/view-enabled-services.png)

  Ennek eredményeképpen a felhasználó csak egy licenccel rendelkezik a termék 12 szolgáltatásával, míg a termékhez csak egy licenc használható.

- Az *E3* licenc kiválasztásával további részletek jelennek meg, például arról, hogy mely szolgáltatásokra van engedélyezve a felhasználó által a csoport licencének hozzárendelése.

## <a name="direct-licenses-coexist-with-group-licenses"></a>A közvetlen licencek együtt léteznek a csoportos licencekkel

Ha a felhasználó egy csoporttól örökli a licencet, a licenc-hozzárendelést nem lehet közvetlenül eltávolítani vagy módosítani a felhasználó tulajdonságaiban. Módosításokat kell végezni a csoportban, majd az összes felhasználó számára propagálni kell.

Az örökölt licenc mellett azonban lehetséges, hogy ugyanezt a licencet közvetlenül a felhasználóhoz rendeli hozzá. A termékből további szolgáltatásokat is engedélyezhet, anélkül, hogy ez hatással lenne a többi felhasználóra.

A közvetlenül hozzárendelt licencek eltávolíthatók, és nincsenek hatással az örökölt licencekre. Vegye figyelembe azt a felhasználót, aki az Office 365 nagyvállalati E3 licencet örökli egy csoportból.

Kezdetben a felhasználó csak az *alapszintű E3 Services* -csoportból örökli a licencet, amely négy szolgáltatáscsomag használatát teszi lehetővé.

1. Válassza a **hozzárendelés** lehetőséget, hogy közvetlenül rendeljen hozzá egy E3-licencet a felhasználóhoz. Ebben az esetben az összes szervizcsomagot le fogja tiltani a Yammer Enterprise kivételével.

    Ennek eredményeképpen a felhasználó továbbra is csak egy licencet használ az E3 termékhez. A közvetlen hozzárendelés azonban csak az adott felhasználó számára engedélyezi a Yammer Enterprise szolgáltatást. Láthatja, hogy mely szolgáltatásokat engedélyezte a csoporttagság a közvetlen hozzárendeléssel szemben.

1. Közvetlen hozzárendelés használata esetén a következő műveletek engedélyezettek:

   - A Yammer Enterprise közvetlenül is kikapcsolható a felhasználói erőforráson. Az ábrán a **be-és kikapcsolási** váltógomb engedélyezve lett ehhez a szolgáltatáshoz, nem pedig a másik szolgáltatás váltására. Mivel a szolgáltatás közvetlenül a felhasználón van engedélyezve, módosítható.
   - További szolgáltatásokat is engedélyezheti a közvetlenül hozzárendelt licenc részeként.
   - Az **Eltávolítás** gomb használatával eltávolíthatja a közvetlen licencet a felhasználótól. Láthatja, hogy a felhasználó most már csak az örökölt csoport licenccel rendelkezik, és csak az eredeti szolgáltatások maradnak engedélyezve:

## <a name="managing-new-services-added-to-products"></a>A termékekhez hozzáadott új szolgáltatások kezelése

Ha a Microsoft új szolgáltatást hoz létre egy termék-licencelési csomaghoz, alapértelmezés szerint engedélyezve van minden olyan csoportban, amelyhez hozzárendelte a licencet. A szervezet azon felhasználói, akik a termék módosításaira vonatkozó értesítésekre előfizetnek, e-maileket kapnak a közelgő szolgáltatási kiegészítésekkel kapcsolatos értesítésekről.

Rendszergazdaként áttekintheti a változás által érintett összes csoportot, és megteheti a műveletet, például letilthatja az új szolgáltatást az egyes csoportokban. Ha például olyan csoportokat hozott létre, amelyek csak meghatározott szolgáltatásokat céloznak meg a központi telepítéshez, akkor újra megtekintheti ezeket a csoportokat, és gondoskodhat arról, hogy az újonnan hozzáadott szolgáltatások le legyenek tiltva.

Íme egy példa arra, hogy a folyamat milyen módon nézhet ki:

1. Eredetileg az *Office 365 Enterprise E5* terméket rendelte több csoporthoz. A *O365 E5-Exchange-* nek nevezett csoportok egyike csak az *Exchange Online (2. csomag)* szolgáltatásnak a tagjai számára történő engedélyezését szolgálja.

2. A Microsofttól értesítést kapott arról, hogy az E5 termék ki lesz bővítve egy új szolgáltatás- *Microsoft stream*. Ha a szolgáltatás elérhetővé válik a szervezetében, a következőket teheti:

3. Lépjen a [**Azure Active Directory > licencek > minden termék**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) panelre, és válassza az *Office 365 Enterprise E5* lehetőséget, majd válassza a **licencelt csoportok** lehetőséget az adott termékkel rendelkező csoportok listájának megtekintéséhez.

4. Kattintson az áttekinteni kívánt csoportra (ebben az esetben az *O365 E5-Exchange-* re). Ekkor megnyílik a **licencek** lap. Ha az E5-licencre kattint, megnyílik egy panel, amely felsorolja az összes engedélyezett szolgáltatást.
   > [!NOTE]
   > A *Microsoft stream* szolgáltatás automatikusan lett hozzáadva és engedélyezve ebben a csoportban, az *Exchange Online* szolgáltatás mellett:

   ![Képernyőfelvétel a csoport licencéhez hozzáadott új szolgáltatásról](./media/licensing-group-advanced/manage-new-services.png)

5. Ha le szeretné tiltani az új szolgáltatást ebben a csoportban, kattintson a szolgáltatás melletti **be-és kikapcsolási** kapcsolóra, majd a módosítás megerősítéséhez kattintson a **Save (Mentés** ) gombra. Az Azure AD most feldolgozza a csoport összes felhasználóját, hogy alkalmazza a módosítást; a csoportba felvett új felhasználókhoz nem lesz engedélyezve a *Microsoft stream* szolgáltatás.

   > [!NOTE]
   > A felhasználók továbbra is használhatják a szolgáltatást valamilyen más licenc-hozzárendeléssel (egy másik csoport tagjai vagy egy közvetlen licenc-hozzárendelés).

6. Ha szükséges, hajtsa végre ugyanezeket a lépéseket a termékhez rendelt más csoportokhoz.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>A PowerShell használatával megtekintheti, hogy ki örökölt és közvetlen licenceket
A PowerShell-parancsfájlok segítségével ellenőrizhető, hogy a felhasználók rendelkeznek-e közvetlenül vagy egy csoporttól örökölt licenccel.

1. Futtassa a `connect-msolservice` parancsmagot a hitelesítéshez és a szervezethez való kapcsolódáshoz.

2. `Get-MsolAccountSku` Az Azure AD-szervezet összes kiépített termék-licencének felderítésére használható.

   ![A Get-Msolaccountsku parancsmag képernyőképe](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Használja az *AccountSkuId* értéket a [PowerShell-szkripttel](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)érintett licenchez. Ekkor megjelenik azoknak a felhasználóknak a listája, akik rendelkeznek ezzel a licenccel a licenc hozzárendelésével kapcsolatos információkkal.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Naplózási naplók használata a csoport alapú licencelési tevékenységek figyeléséhez

Az [Azure ad-naplók](../reports-monitoring/concept-audit-logs.md#audit-logs) használatával megtekintheti a csoport alapú licenceléssel kapcsolatos összes tevékenységet, beleértve a következőket:
- a csoport licenceit módosította
- Amikor a rendszer elindította a csoport licencének feldolgozását, és amikor elkészült
- a felhasználó által a csoportos licenc-hozzárendelés eredményeképpen végrehajtott licencek módosításai.

>[!NOTE]
> A naplók a portál Azure Active Directory szakaszának legtöbb panelén elérhetők. Attól függően, hogy hol fér hozzá, a szűrők előre alkalmazhatók, hogy csak a panel környezetéhez kapcsolódó tevékenység jelenjen meg. Ha nem látja a várt eredményeket, vizsgálja meg [a szűrési beállításokat](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) , vagy a [**Azure Active Directory > tevékenység > a naplók**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)területen nyissa meg a nem szűrt naplókat.

### <a name="find-out-who-modified-a-group-license"></a>Megtudhatja, hogy ki módosította a csoport licencét

1. Állítsa be a **tevékenység** szűrőt a *csoport licencének beállításához* , majd kattintson az **alkalmaz** gombra.
2. Az eredmények közé tartozik a csoportokon beállított vagy módosított licencek összes esete.
   >[!TIP]
   > A *cél* szűrőben a csoport nevét is beírhatja az eredmények hatókörének meghatározásához.

3. Válasszon ki egy elemet a listában a változás részleteinek megtekintéséhez. A *módosított tulajdonságok* területen a licenc-hozzárendelés régi és új értékei szerepelnek a felsorolásban.

Íme egy példa a legutóbbi csoport licencének változásaira, a részletekkel:

![Képernyőkép, amely a "naplók" lapot jeleníti meg egy kijelölt listaelemtel, és megnyílik a "tevékenység részletei naplója" panel.](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Annak megállapítása, hogy a csoport változásai megkezdődött és befejeződött-e

Amikor egy licenc megváltozik egy csoporton, az Azure AD elkezdi alkalmazni a módosításokat az összes felhasználóra.

1. Ha szeretné megtekinteni, hogy a csoportok mikor kezdték meg a feldolgozást, állítsa be a **tevékenység** szűrőt, hogy *megkezdje a csoport alapú licenc alkalmazását* Vegye figyelembe, hogy a művelet szereplője *Microsoft Azure AD Group-Based licencelése* – egy olyan rendszerfiók, amely az összes csoportos licencelési módosítás végrehajtásához használatos.
   >[!TIP]
   > Kattintson a lista egyik elemére a *módosított tulajdonságok* mező megjelenítéséhez – ez megjeleníti a feldolgozásra kiválasztott licencek változásait. Ez akkor hasznos, ha egy csoporton több módosítást hajtott végre, és nem biztos benne, hogy melyik lett feldolgozva.

2. Hasonlóképpen, ha szeretné megtekinteni, hogy a csoportok Mikor dolgozzák fel a feldolgozást, használja a szűrési érték *befejezése csoport alapú licenc alkalmazása a felhasználók számára* lehetőséget.
   > [!TIP]
   > Ebben az esetben a *módosított tulajdonságok* mező az eredmények összegzését tartalmazza – ez hasznos lehet annak gyors ellenőrzéséhez, hogy a feldolgozás hibákat eredményezett-e. Példa a kimenetre:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Ha meg szeretné tekinteni a csoport feldolgozásának teljes naplóját, beleértve az összes felhasználó módosítását, állítsa be a következő szűrőket:
   - **Kezdeményező (színész)**: "Microsoft Azure ad Group-Based licencelés"
   - **Dátumtartomány** (nem kötelező): egyéni tartomány, ha tudja, hogy egy adott csoport elindult, és befejeződött a feldolgozás.

Ez a minta kimenet a feldolgozás kezdetét, az összes eredményül kapott felhasználói változást és a feldolgozás befejezését mutatja.

![Képernyőfelvétel-csoport licencének változásai](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> A *felhasználói licencek módosításához* kapcsolódó elemek elemre kattintva megtekintheti az egyes felhasználókra alkalmazott licenc-módosítások részleteit.

## <a name="deleting-a-group-with-an-assigned-license"></a>Hozzárendelt licenccel rendelkező csoport törlése

Nem lehet törölni egy olyan csoportot, amelyhez aktív licenc van rendelve. Egy rendszergazda törölheti azt a csoportot, amely nem veszi észre, hogy a licenceket el kell távolítani a felhasználóktól, ezért a törléshez előbb el kell távolítani a csoportból a licenceket.

Ha a Azure Portal egy csoport törlését kísérli meg, a következőhöz hasonló hibaüzenet jelenhet meg: ![ képernyőkép-csoport törlése sikertelen](./media/licensing-group-advanced/groupdeletionfailed.png)

Lépjen a csoport **licencek** fülére, és ellenőrizze, hogy vannak-e hozzárendelve licencek. Ha igen, távolítsa el ezeket a licenceket, és próbálja meg újból törölni a csoportot.

Hasonló hibák jelenhetnek meg, amikor a csoportot a PowerShell vagy Graph API használatával próbálta törölni. Ha a helyszíni rendszerből szinkronizált csoportot használ, akkor a Azure AD Connect hibákat is jelenthet, ha nem törli a csoportot az Azure AD-ben. Minden ilyen esetben ellenőrizze, hogy vannak-e hozzárendelve licencek a csoporthoz, és először távolítsa el őket.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

Ha Group-alapú licencelést használ, érdemes megismernie az alábbi korlátozásokat és ismert problémákat.

- A csoportalapú licenckezelés jelenleg nem támogatja az olyan csoportokat, amelyek más (beágyazott) csoportokat tartalmaznak. Ha egy beágyazott csoportra alkalmaz egy licencet, az csak a csoport közvetlen első szintű felhasználótagjaira lesz alkalmazva.

- A szolgáltatás csak biztonsági csoportokkal használható, és Microsoft 365 a securityEnabled = TRUE értékkel rendelkező csoportokat.

- A [Microsoft 365 felügyeleti központ](https://admin.microsoft.com) jelenleg nem támogatja a csoport alapú licencelést. Ha a felhasználó egy csoporttól örökli a licencet, ez a licenc az Office felügyeleti portálon normál felhasználói licencként jelenik meg. Ha megpróbálja módosítani a licencet, vagy megpróbálja eltávolítani a licencet, a portál egy hibaüzenetet ad vissza. Az örökölt csoportos licencek nem módosíthatók közvetlenül a felhasználón.

- Ha a licencek egy nagy csoporthoz vannak rendelve vagy módosítva (például 100 000 felhasználó), az hatással lehet a teljesítményre. Az Azure AD Automation által generált változások mennyisége negatív hatással lehet az Azure AD és a helyszíni rendszerek közötti címtár-szinkronizálás teljesítményére.

- Ha dinamikus csoportokat használ a felhasználóhoz tartozó tagság kezeléséhez, ellenőrizze, hogy a felhasználó a csoport tagja-e, mert ez szükséges a licenc hozzárendeléséhez. Ha nem, [ellenőrizze dinamikus csoporthoz tartozó tagsági szabály feldolgozási folyamatának állapotát](groups-create-rule.md).

- Bizonyos nagy terhelésű helyzetekben hosszú időt is igénybe vehet a csoportok és a tagsági változások a meglévő licencekkel rendelkező csoportokba való módosítása során. Ha úgy látja, hogy a módosítások több mint 24 órát vesznek igénybe a 60K-felhasználók vagy annál kisebb méretű csoportok feldolgozásához, [Nyisson meg egy támogatási jegyet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) , amely lehetővé teszi számunkra a vizsgálatát. 

- A licencelési szolgáltatás automatizálása nem reagál automatikusan a környezet összes változására. Előfordulhat például, hogy elfogyott a licencek, ami miatt egyes felhasználók hibás állapotba kerülhetnek. A rendelkezésre álló ülőhelyek számának felszabadításához eltávolíthat néhány közvetlenül hozzárendelt licencet a többi felhasználótól. A rendszer azonban nem reagál automatikusan erre a változásra, és kijavítja a felhasználókat az adott hiba állapotában.

  Az ilyen típusú korlátozások megkerülő megoldásként nyissa meg a **csoport** panelt az Azure ad-ben, és kattintson az **újrafeldolgozás** gombra. Ez a parancs feldolgozza a csoport összes felhasználóját, és ha lehetséges, feloldja a hibák állapotait.

## <a name="next-steps"></a>Következő lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Mi a Azure Active Directory csoportos licencelése?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Felhasználók áttelepítése licencek között a csoport alapú licencelés használatával Azure Active Directory](licensing-groups-change-licenses.md)
