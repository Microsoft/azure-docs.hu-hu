---
title: A Azure Active Directory erőforrásaihoz való külső hozzáférés szabályozása érzékenységi címkékkel.
description: A külső hozzáférés általános biztonsági tervének részeként használjon érzékenységi címkéket.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565172"
---
# <a name="control-access-with-sensitivity-labels"></a>Hozzáférés szabályozása érzékenységi címkékkel 

Az [érzékenységi címkék](/microsoft-365/compliance/sensitivity-labels) segítségével szabályozhatja a tartalomhoz való hozzáférést az Office 365-alkalmazásokban, valamint a Microsoft Teams, Microsoft 365 csoportok és SharePoint-webhelyek tárolókban. A felhasználók együttműködésének és üzemi képességeinek akadályozása nélkül védik a tartalmakat. Az érzékenységi címkék lehetővé teszik a szervezet tartalmának elküldését az eszközök, alkalmazások és szolgáltatások között, az adatok védelme és a megfelelőségi és biztonsági szabályzatok teljesítése mellett. 

Az érzékenységi címkékkel a következőket teheti:

* **A tartalom besorolása védelmi beállítások hozzáadása nélkül**. Besorolást rendelhet a tartalomhoz (például egy matricához), amely továbbra is fennmarad és barangol a tartalommal a használat és a megosztás során. Ezen besorolás használatával jelentéseket generálhat és megtekintheti a bizalmas tartalom tevékenységadatait.

* **A védelmi beállítások, például a titkosítás, a vízjelek és a hozzáférési korlátozások betartatása**. Például a felhasználók bizalmas címkét alkalmazhatnak egy dokumentumra vagy e-mailre, és a címke [titkosíthatja a tartalmat](/microsoft-365/compliance/encryption-sensitivity-labels) , és hozzáadhat egy "bizalmas" vízjelet. Emellett [alkalmazhat egy érzékenységi címkét egy tárolóra](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) , például egy SharePoint-webhelyre, és megszabhatja, hogy a külső felhasználók hozzáférhessenek-e a benne található tartalomhoz.

Az e-mailek és más tartalmak érzékenységi címkéi a tartalommal utaznak. A tárolók érzékenységi címkéi korlátozhatják a tárolóhoz való hozzáférést, de a tárolóban lévő tartalom nem örökli a címkét. Egy felhasználó például megteheti a tartalmat egy védett helyről, letöltheti, majd korlátozás nélkül megoszthatja, kivéve, ha a tartalomnak is van érzékenységi címkéje.

 >[!NOTE]
>Az érzékenységi címkék alkalmazásához a felhasználóknak be kell jelentkezniük a Microsoft munkahelyi vagy iskolai fiókjába. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Az érzékenységi szintek létrehozásához és kezeléséhez szükséges engedélyek

A megfelelőségi címkéket létrehozó megfelelőségi csoport tagjainak engedélyeket kell biztosítaniuk a Microsoft 365 megfelelőségi központhoz, Microsoft 365 Security Centerhoz vagy a biztonsági & megfelelőségi központjához.

Alapértelmezés szerint a bérlő globális rendszergazdái hozzáférhetnek ezekhez a felügyeleti központokhoz, és hozzáférést biztosíthatnak a megfelelőségi tisztviselők és más személyek számára, anélkül, hogy a bérlői rendszergazda engedélyeit adnák nekik. Ehhez a delegált korlátozott rendszergazdai hozzáféréshez adja hozzá a felhasználókat a megfelelőségi adat rendszergazdájához, a megfelelőségi rendszergazdához vagy a biztonsági rendszergazda szerepkör csoportjához.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Az érzékenységi címke stratégiájának meghatározása

Ha úgy gondolja, hogy a külső hozzáférés szabályozható a tartalomhoz, a következőket kell megállapítania:

**Minden tartalomhoz és tárolóhoz**

* Hogyan határozható meg a magas, közepes vagy alacsony üzleti hatás (HBI, MBI, LBI)? Vegye figyelembe a szervezetre gyakorolt hatást, ha bizonyos tartalomtípusok nem megfelelőek.

   * A tartalom, amely a [bizalmasan megkülönböztetett tartalmak](/microsoft-365/compliance/apply-sensitivity-label-automatically), például a hitelkártyák vagy a Passport-számok meghatározott típusaival rendelkezik

   * Meghatározott csoportok vagy személyek által létrehozott tartalom (például megfelelőségi tisztviselők, pénzügyi tisztviselők vagy vezetők)

   * Adott könyvtárak vagy webhelyek tartalma. Például a szervezeti stratégiát vagy a magán pénzügyi adatszolgáltatásokat üzemeltető tárolók

   * Egyéb feltételek

* A tartalmak (például a HBI-tartalom) milyen kategóriáit korlátozzák a külső felhasználók hozzáférése?

   * A korlátozások lehetnek olyan műveletek, mint például a tárolók elérésének korlátozása és a tartalmak titkosítása.

* Milyen alapértelmezett értékekkel kell rendelkeznie a HBI-, a-vagy Microsoft 365-csoportoknak?

* Hol fogja használni az érzékenységi címkéket a [címkézéshez és a figyeléshez](/microsoft-365/compliance/sensitivity-labels), a [titkosítás kikényszerített](/microsoft-365/compliance/encryption-sensitivity-labels) vagy a [tárolók hozzáférési korlátozásának betartatásához](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)?

**E-mailek és tartalmak esetén**

* Szeretné [automatikusan alkalmazni az érzékenységi címkéket](/microsoft-365/compliance/apply-sensitivity-label-automatically) a tartalomra, vagy manuálisan?

   * Ha úgy dönt, hogy manuálisan szeretné elvégezni, [javasolja, hogy a felhasználók címkét alkalmazzanak](/microsoft-365/compliance/apply-sensitivity-label-automatically)?

**Tárolók esetén**

* Milyen feltételekkel határozható meg, hogy a M365-csoportok, a csapatok vagy a SharePoint-webhelyek milyen mértékben korlátozzák a hozzáférést az érzékenységi címkék használatával?

* Csak a tárolókban lévő tartalmat szeretné felcímkézni, vagy a meglévő fájlokat szeretné [automatikusan címkézni](/microsoft-365/compliance/apply-sensitivity-label-automatically) a SharePointban és a OneDrive?

Tekintse meg ezeket a [gyakori forgatókönyveket az](/microsoft-365/compliance/get-started-with-sensitivity-labels) érzékeny címkék használatára vonatkozó egyéb ötletekhez.

### <a name="sensitivity-labels-on-email-and-content"></a>E-mailek és tartalmak érzékenységi címkéi

Ha egy dokumentumhoz vagy e-mailhez társít egy érzékenységi címkét, akkor az olyan, mint a testreszabható, a szöveg és az állandó tartalomra alkalmazott bélyegző. 

* A **testre szabható** beállítás azt jelenti, hogy a szervezet számára megfelelő címkéket hozhat létre, és meghatározhatja, hogy mi történik az alkalmazásakor.

* A **szöveg törlése** azt jelenti, hogy az elem metaadatai részét képezi, és az alkalmazások és a szolgáltatások is olvashatók, így saját védelmi műveleteket alkalmazhatnak.

* Az **állandó** érték azt jelenti, hogy a címke és a kapcsolódó védelem a tartalommal együtt barangol, és a házirendek alkalmazásának és érvényesítésének alapja lesz.

 

> [!NOTE]
> A tartalom minden egyes eleme egyetlen érzékenységi címkével is rendelkezhet.


### <a name="sensitivity-labels-on-containers"></a>A tárolók érzékenységi címkéi

Érzékeny címkéket alkalmazhat a tárolók, például a [Microsoft 365 csoportok](../enterprise-users/groups-assign-sensitivity-labels.md), a [Microsoft-csapatok](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)és a SharePoint- [webhelyek](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)számára. Ha ezt az érzékenységi címkét egy támogatott tárolóra alkalmazza, a címke automatikusan alkalmazza a besorolási és védelmi beállításokat a csatlakoztatott helyre vagy csoportra. A tárolók érzékenységi címkéi a tárolók következő aspektusait szabályozhatják:

* **Adatvédelem**. Kiválaszthatja, hogy ki láthatja a webhelyet: adott felhasználók, minden belső felhasználó vagy bárki.

* **Külső felhasználói hozzáférés**. Meghatározza, hogy a csoport tulajdonosa adhat-e hozzá vendégeket a csoporthoz.

* **Hozzáférés nem felügyelt eszközökről**. Meghatározza, hogy a nem felügyelt eszközök hogyan férhetnek hozzá a tartalomhoz.

 

![Az érzékenységi címkék szerkesztését bemutató képernyőkép](media/secure-external-access/8-edit-label.png)

 

Ha egy adott tárolóra, például egy SharePoint-webhelyre alkalmazza az érzékenységi címkét, a rendszer nem alkalmazza az ott található tartalomra: a tárolók érzékenységi címkéi a tárolóban lévő tartalomhoz való hozzáférést szabályozzák. 

* Ha szeretné automatikusan alkalmazni a címkét a tárolón belüli tartalomra, olvassa el az [érzékenység tartalomra történő automatikus alkalmazása](/microsoft-365/compliance/apply-sensitivity-label-automatically)című témakört.

* Ha azt szeretné, hogy a felhasználók manuálisan tudják alkalmazni a címkéket a tartalomra, ügyeljen arra, hogy az [Office-fájlokra vonatkozó érzékenységi címkéket engedélyezte a SharePointban és a OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files).

### <a name="plan-to-implement-sensitivity-labels"></a>Az érzékenységi címkék megvalósításának megtervezése

Miután meghatározta, hogyan szeretné használni az érzékenységi címkéket, és hogy milyen tartalmat és webhelyeket kíván alkalmazni, tekintse meg az alábbi dokumentációt, amely segít a megvalósításában.

1. [Az érzékenységi címkék első lépései](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Központi telepítési stratégia létrehozása](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Érzékenységi Címkék létrehozása és közzététele](/microsoft-365/compliance/create-sensitivity-labels)

4. [Tartalomhoz való hozzáférés korlátozása érzékenységi címkék használatával a titkosítás alkalmazásához](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Érzékenységi címkék használata csapatokkal, csoportokkal és webhelyekkel](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [Az Office-fájlok érzékenységi feliratainak engedélyezése a SharePointban és a OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A kívánt biztonsági helyzet meghatározása a külső hozzáféréshez](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md) (itt van.)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)