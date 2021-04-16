---
title: SciM, Microsoft Graph és Azure AD használata felhasználók építéséhez és alkalmazások adatokkal való gazdagítására
description: Az SCIM és a Microsoft Graph a felhasználók kiépítése és az alkalmazás gazdagítása a szükséges adatokkal.
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
ms.openlocfilehash: 87df7efcbab89c87a42e611f5fc1219239de6873
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530520"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SciM és Microsoft Graph felhasználók kiépítése és az alkalmazás gazdagítása a szükséges adatokkal

**Célközönség:** Ez a cikk olyan fejlesztőknek szól, akik alkalmazásokat hoznak létre az Azure Active Directory (Azure AD) integrálva. Ha az Azure AD-val már integrált alkalmazásokat (például Zoom, ServiceNow és DropBox) használna, [](../saas-apps/tutorial-list.md) kihagyhatja ezt a cikket, és áttekintheti az alkalmazásspecifikus oktatóanyagokat, vagy áttekintheti a kiépítési szolgáltatás [működését.](./how-provisioning-works.md)

**Gyakori forgatókönyvek**

Az Azure AD használatra rendelkezésre áll szolgáltatást biztosít a kiépítéshez, valamint egy olyan, extenzív platformot, amely lehetővé teszi az alkalmazások építését. A döntési fa bemutatja, hogyan használhatja a fejlesztő az [SCIM-et,](https://aka.ms/scimoverview) [Microsoft Graph](/graph/overview) a kiépítés automatizálásához. 

> [!div class="checklist"]
> * Felhasználók automatikus létrehozása az alkalmazásban
> * Felhasználók automatikus eltávolítása az alkalmazásból, ha már nincs hozzáférésük
> * Alkalmazás integrálása több identitásszolgáltatóval a kiépítéshez
> * Bővítsem az alkalmazásomat olyan Microsoft-szolgáltatások adatokkal, mint a Teams, az Outlook és az Office.
> * Felhasználók és csoportok automatikus létrehozása, frissítése és törlése az Azure AD-ban és Active Directory

![SCIM Graph döntési fa](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>1. forgatókönyv: Felhasználók automatikus létrehozása az alkalmazásban
Napjainkban a rendszergazdák manuálisan hoznak létre felhasználói fiókokat, vagy rendszeresen feltöltik a CSV-fájlokat az alkalmazásomba. A folyamat időigényes az ügyfelek számára, és lelassítja az alkalmazásom alkalmazását. A felhasználó létrehozásához mindössze alapvető felhasználói adatokra van szükségem, például névre, e-mail-címre és userPrincipalName névre. 

**Javaslat**: 
* Ha az ügyfelek különböző idPs-eket használnak, és nem szeretne szinkronizációs motort fenntartani az integrációhoz, akkor támogatnia kell egy SCIM-kompatibilis [/Users végpontot.](https://aka.ms/scimreferencecode) Az ügyfelek könnyedén használhatja ezt a végpontot az Azure AD kiépítési szolgáltatásba való integrációhoz, és automatikusan létrehozhatnak felhasználói fiókokat, amikor hozzáférésre van szükségük. A végpontot egyszer is felépítheti, és az kompatibilis lesz az összes ip-azonosítóval. Tekintse meg az alábbi példakérést, amely azt mutatja be, hogyan jön létre egy felhasználó az SCIM használatával.
* Ha felhasználói adatokra van szüksége, amelyek az Azure AD-beli felhasználói objektumban találhatók, és más Microsoft-adatokra is szüksége van, érdemes lehet SCIM-végpontot építeni a felhasználók üzembe helyezése és a Microsoft Graph-be való hívása érdekében a többi adat lehívásához. 

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

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>2. forgatókönyv: Felhasználók automatikus eltávolítása az alkalmazásból
Az alkalmazásomat használó ügyfelek biztonságra összpontosítanak, és irányítási követelményekkel kell szembeniak a fiókok eltávolítására, ha az alkalmazottaknak már nincs rájuk szükségük. Hogyan automatizálható a megszüntetés az alkalmazásból?

**Javaslat:** SCIM-kompatibilis /Users végpont támogatása. Az Azure AD kiépítési szolgáltatás a letiltására és törlésére vonatkozó kéréseket küld, ha a felhasználónak már nincs hozzáférése. Javasoljuk, hogy a felhasználókat letiltsa és törölje is. Az alábbi példákban láthatja, hogyan néz ki a letiltására és törlésére vonatkozó kérés. 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>3. forgatókönyv: Csoporttagság-kezelés automatizálása az alkalmazásban
Az alkalmazásom csoportokra támaszkodik a különböző erőforrások eléréséhez, és az ügyfelek újra szeretnék használni az Azure AD-ban meglévő csoportokat. Hogyan importálhatók csoportok az Azure AD-ból, és hogyan tarthatók naprakészen a tagságok változásának megfelelően?  

**Javaslat:** SCIM-kompatibilis /Groups végpont [támogatása.](https://aka.ms/scimreferencecode) Az Azure AD kiépítési szolgáltatás gondoskodik a csoportok létrehozásáról és a tagságfrissítések kezeléséről az alkalmazásban. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>4. forgatókönyv: Az alkalmazás gazdagítása olyan Microsoft-szolgáltatások, mint a Teams, az Outlook és a OneDrive
Az alkalmazásom be van építve a Microsoft Teamsbe, és üzenetadatokra támaszkodik. Emellett a OneDrive-ban tároljuk a felhasználók fájljait. Hogyan gazdagíthatja az alkalmazásomat ezekkel a szolgáltatásokkal és a Microsofton keresztül származó adatokkal?

**Javaslat:** A [Microsoft Graph](/graph/) a Belépési pont a Microsoft-adatok eléréséhez. Minden számítási feladat elérhetővé teszi az API-kat a szükséges adatokkal. A Microsoft Graph az [SCIM-kiépítéssel](./use-scim-to-provision-users-and-groups.md) együtt használható a fenti forgatókönyvekhez. Az SCIM használatával alapszintű felhasználói attribútumokat is kiépíthet az alkalmazásba, miközben a gráfban hívja meg az adatokat, hogy bármilyen más szükséges adatot lekért. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>5. forgatókönyv: Változások követése a Microsoft-szolgáltatások, például a Teamsben, az Outlookban és az Azure AD-ben
Képesnek kell lennie a Teams- és Outlook-üzenetek változásainak nyomon követésére, és valós idejű reagálásra. Hogyan tudom lekellni ezeket a módosításokat az alkalmazásomba?

**Javaslat:** A Microsoft Graph [változásértesítéseket és](/graph/webhooks) változáskövetést [biztosít](/graph/delta-query-overview) a különböző erőforrásokhoz. Figyelje meg a változásértesítések alábbi korlátozását:
- Ha egy esemény fogadója nyugtáz egy eseményt, de valamilyen okból nem tud vele cselekedni, az esemény elveszhet.
- A módosítások bekért sorrendje nem garantáltan időrendi.
- A változásértesítések nem mindig tartalmazzák az erőforrásadatokat [A](/graph/webhooks-with-resource-data) fenti okokból a fejlesztők gyakran változásértesítéseket és változáskövetést is használhatnak szinkronizálási forgatókönyvekhez. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>6. forgatókönyv: Felhasználók és csoportok kiépítése az Azure AD-ben
Az alkalmazásom információkat hoz létre egy olyan felhasználóról, akire az ügyfeleknek szükségük van az Azure AD-ban. Ez lehet egy HR-alkalmazás, mint a munkaerő-felvétel kezelése, egy kommunikációs alkalmazás, amely telefonszámokat hoz létre a felhasználók számára, vagy egy másik alkalmazás, amely olyan adatokat hoz létre, amelyek hasznosak lesznek az Azure AD-ben. Hogyan a felhasználói rekordot az Azure AD-ban az adatokkal? 

**Javaslat** A Microsoft Graph elérhetővé teszi a /Users és /Groups végpontokat, amelyek integrálhatók a mai naptól a felhasználók Azure AD-be való építéshez. Vegye figyelembe, Azure Active Directory nem támogatja a felhasználók visszaírását a Active Directory. 

> [!NOTE]
> A Microsoft rendelkezik egy kiépítési szolgáltatással, amely olyan HR-alkalmazásokból is lekért adatokat, mint a Workday és a SuccessFactors. Ezeket az integrációkat a Microsoft építi és kezeli. Ha új HR-alkalmazást kell a szolgáltatáshoz bevetni, a [UserVoice-on kérheti.](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Tekintse át a szinkronizálási Microsoft Graph dokumentációját](/graph/api/resources/synchronization-overview)
- [Egyéni SCIM-alkalmazás integrálása az Azure AD-val](use-scim-to-provision-users-and-groups.md)