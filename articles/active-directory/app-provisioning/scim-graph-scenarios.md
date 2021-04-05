---
title: A SCIM, a Microsoft Graph és az Azure AD használata a felhasználók kiépítéséhez és az alkalmazások bővítéséhez
description: A SCIM és a Microsoft Graph együttes használatával kiépítheti a felhasználókat, és gazdagíthatja az alkalmazást az általa igénybe venni kívánt adattal.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 0a5d84585f28f6d13cbceb1fec41d6cdabf6d08c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255627"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>A SCIM és a Microsoft Graph együttes használata a felhasználók kiépítéséhez és az alkalmazás bővítéséhez az általa igénybe venni kívánt adattal

**Célközönség:** Ez a cikk a Azure Active Directory (Azure AD) integrálására szolgáló fejlesztők számára készült. Ha az Azure AD-vel már integrált alkalmazásokat szeretne használni, például a nagyítást, a ServiceNow és a DropBoxot, kihagyhatja ezt a cikket, és áttekintheti az alkalmazásra vonatkozó [oktatóanyagokat](../saas-apps/tutorial-list.md) , vagy áttekintheti [a kiépítési szolgáltatás működését](./how-provisioning-works.md).

**Gyakori forgatókönyvek**

Az Azure AD az üzembe helyezéshez és az alkalmazások kiépítéséhez használható bővíthető platformot biztosít az alkalmazásokhoz. A döntési fa azt ismerteti, hogy a fejlesztők hogyan használják a [scim](https://aka.ms/scimoverview) és a [Microsoft Graph](/graph/overview) a kiépítés automatizálására. 

> [!div class="checklist"]
> * Felhasználók automatikus létrehozása az alkalmazásban
> * Automatikusan eltávolíthatja a felhasználókat az alkalmazásból, ha már nem férnek hozzá
> * Saját alkalmazás integrálása több identitás-szolgáltatóval az üzembe helyezéshez
> * A Microsoft-szolgáltatásokból, például a csapatokból, az Outlookból és az Office-ból származó adatokkal gazdagíthatja alkalmazásait.
> * Felhasználók és csoportok automatikus létrehozása, frissítése és törlése az Azure AD-ben és Active Directory

![SCIM gráf döntési fája](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>1. forgatókönyv: felhasználók automatikus létrehozása az alkalmazásban
Napjainkban a rendszergazdák kiépítik a felhasználókat a felhasználói fiókok manuális létrehozásával vagy a CSV-fájlok alkalmazásba való rendszeres feltöltésével. A folyamat időt vesz igénybe az ügyfelek számára, és lelassítja az alkalmazás bevezetését. A felhasználók létrehozásához szükség van az alapvető felhasználói információkra, például a név, az e-mail és a userPrincipalName. 

**Javaslat**: 
* Ha az ügyfelek különböző IDP használnak, és nem szeretné, hogy a szinkronizálási motort az egyes szolgáltatásokkal integrálja, támogassa a SCIM-kompatibilis [/Users](https://aka.ms/scimreferencecode) -végpontot. Az ügyfelek könnyedén használhatják ezt a végpontot az Azure AD-kiépítési szolgáltatásba való integráláshoz és a felhasználói fiókok automatikus létrehozásához, amikor hozzáférésre van szükségük. A végpontot egyszer is felépítheti, és minden IDP kompatibilis lesz. Tekintse meg az alábbi példában, hogy a felhasználók hogyan hozhatók létre a SCIM használatával.
* Ha felhasználói adatokra van szüksége az Azure AD-ben és a Microsofton kívül más adatokban található felhasználói objektumon, érdemes lehet SCIM-végpontot létrehozni a felhasználók kiépítésekor, és a további adatok beszerzéséhez meg kell hívni a Microsoft Graph. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>2. forgatókönyv: a felhasználók automatikus eltávolítása az alkalmazásból
Az alkalmazást használó ügyfeleink biztonságra vannak koncentrálva, és irányítási követelményekkel rendelkeznek a fiókok eltávolításához, ha az alkalmazottaknak már nincs rájuk szükségük. Hogyan automatizálható a kiépítés az alkalmazásból?

**Javaslat:** Támogatja a SCIM-kompatibilis/Users-végpontot. Az Azure AD kiépítési szolgáltatás a letiltási és törlési kérelmeket küldi el, ha a felhasználónak többé nem lesz hozzáférése. Javasoljuk, hogy a felhasználók letiltását és törlését is támogassa. Tekintse meg az alábbi példákat a letiltási és törlési kérelem kinézetéhez. 

Felhasználó letiltása
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Felhasználó törlése
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>3. forgatókönyv: csoporttagság kezelése az alkalmazásban
Az alkalmazás a különböző erőforrásokhoz való hozzáféréshez használt csoportokra támaszkodik, és az ügyfelek szeretnék újra felhasználni az Azure AD-ban található csoportokat. Hogyan importálhatók csoportok az Azure AD-ből, és hogyan frissíthetik őket a tagságok változásakor?  

**Javaslat:** Támogatja a SCIM-kompatibilis/groups- [végpontot](https://aka.ms/scimreferencecode). Az Azure AD-kiépítési szolgáltatás gondoskodik a csoportok létrehozásáról és a tagsági frissítések kezeléséről az alkalmazásban. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>4. forgatókönyv: az alkalmazás gyarapítása Microsoft-szolgáltatásokból, például csapatokból, Outlookból és OneDrive származó adatokkal
Az alkalmazásom be van építve a Microsoft Teams szolgáltatásba, és az üzenetek adataira támaszkodik. Emellett a OneDrive lévő felhasználók fájljait is tároljuk. Hogyan frissíthetem az alkalmazást a szolgáltatások adataival és a Microsofton keresztül?

**Javaslat:** A [Microsoft Graph](/graph/) a belépési pontja a Microsoft adataihoz való hozzáféréshez. Minden munkaterhelés elérhetővé teszi az API-kat a szükséges adatokkal. A Microsoft Graph a fenti forgatókönyvek esetében a [scim kiépítés](./use-scim-to-provision-users-and-groups.md) mellett is használható. A SCIM segítségével alapszintű felhasználói attribútumokat helyezhet üzembe az alkalmazásban, miközben diagramon is meghívja a szükséges egyéb adatok beszerzését. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>5. forgatókönyv: a Microsoft-szolgáltatások, például a csapatok, az Outlook és az Azure AD változásainak követése
Követni kell a csapatok és az Outlook üzeneteinek változásait, és valós időben reagálni kell rájuk. Hogyan szerezhetem be ezeket a módosításokat az alkalmazásba?

**Javaslat:** A Microsoft Graph a különböző erőforrások [változási értesítéseit](/graph/webhooks) és a [változások nyomon követését](/graph/delta-query-overview) teszi lehetővé. Vegye figyelembe a változási értesítések következő korlátozásait:
- Ha egy esemény fogadója tudomásul veszi az eseményt, de bármilyen okból nem sikerül rá lépni, az esemény elvész.
- A módosítások fogadásának sorrendje nem garantált időrendben.
- Az értesítések módosítása a fenti okok miatt nem mindig tartalmaz [erőforrásadatokat](/graph/webhooks-with-resource-data) , a fejlesztők gyakran a módosítási értesítéseket használják a szinkronizálási forgatókönyvek módosítási nyomon követésével együtt. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>6. forgatókönyv: felhasználók és csoportok kiépítése az Azure AD-ben
Az alkalmazás egy olyan felhasználóról hoz létre információt, amelyet az ügyfeleknek az Azure AD-ben kell megadniuk. Ez lehet egy HR-alkalmazás, mint a bérbeadás kezelése, egy kommunikációs alkalmazás, amely telefonszámokat hoz létre a felhasználók számára, vagy egy másik alkalmazás, amely az Azure AD-ben értékes adatokat generál. Hogyan feltölti a felhasználói rekordot az Azure AD-ben az adatokkal? 

**Javaslat** A Microsoft Graph olyan/Users-és/groups-végpontokat tesz elérhetővé, amelyeket ma integrálhat a felhasználók Azure AD-be való kiépítéséhez. Vegye figyelembe, hogy a Azure Active Directory nem támogatja a felhasználók Active Directoryba való visszaírását. 

> [!NOTE]
> A Microsoft olyan kiépítési szolgáltatással rendelkezik, amely HR-alkalmazásokból, például munkanapokból és SuccessFactors származó adatokra kéri le. Ezeket az integrációkat a Microsoft építette és felügyeli. Ha új HR-alkalmazást szeretne bevezetni a szolgáltatásba, azt [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)-on keresztül kérheti le. 

## <a name="related-articles"></a>Kapcsolódó cikkek

- [A szinkronizálási Microsoft Graph dokumentációjának áttekintése](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Egyéni SCIM-alkalmazás integrálása az Azure AD-vel](use-scim-to-provision-users-and-groups.md)