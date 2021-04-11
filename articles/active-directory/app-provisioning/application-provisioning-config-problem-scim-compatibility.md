---
title: Ismert problémák a rendszeren a tartományok közötti Identitáskezelés (SCIM) 2,0 protokoll megfelelősége – Azure AD
description: A SCIM 2,0-et az Azure AD-t támogató nem katalógusbeli alkalmazások hozzáadásakor felmerülő általános protokoll-kompatibilitási problémák megoldása
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/07/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 268931715500631ac2d10feacebff1d5c65931bf
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028791"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Az Azure AD felhasználói kiépítési szolgáltatás SCIM 2,0-es protokoll-megfelelőségének ismert problémái és megoldásai

Azure Active Directory (Azure AD) automatikusan kiépítheti a felhasználókat és a csoportokat bármely olyan alkalmazásra vagy rendszerre, amelyet egy webszolgáltatásnak kell ellátnia a [rendszeren a tartományok közötti Identitáskezelés (scim) 2,0 protokoll specifikációja](https://tools.ietf.org/html/draft-ietf-scim-api-19)alapján meghatározott felületen. 

A SCIM 2,0 protokoll Azure AD-támogatását a [rendszer a tartományok közötti Identitáskezelés (scim) használatával című témakör ismerteti, amellyel automatikusan kiépítheti a felhasználókat és csoportokat a Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md), amelyek a scim 2,0-et támogató alkalmazások számára automatikusan kiépítik a felhasználókat és csoportokat az Azure ad-ből.

Ez a cikk az Azure AD-felhasználó kiépítési szolgáltatásának az SCIM 2,0-es protokollal való betartásával kapcsolatos aktuális és múltbeli problémákat ismerteti, valamint a problémák megoldását.

## <a name="understanding-the-provisioning-job"></a>A kiépítési feladatok ismertetése
A kiépítési szolgáltatás a feladatok koncepcióját használja egy alkalmazáson való működéshez. A jobID a [folyamatjelző sávban](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)található. Az új kiépítési alkalmazások a "scim" kezdetű jobID jönnek létre. A scim feladat a szolgáltatás aktuális állapotát jelöli. A régebbi feladatok "customappsso" AZONOSÍTÓval rendelkeznek. Ez a feladat a szolgáltatás állapotát mutatja 2018-ben. 

Ha alkalmazást használ a katalógusban, a feladatokban általában az alkalmazás neve szerepel (például nagyítási hópehely, dataBricks stb.). Ezt a dokumentációt katalógus-alkalmazás használatakor is kihagyhatja. Ez elsősorban a nem katalógusos alkalmazásokra vonatkozik a jobID SCIM vagy a customAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2,0 megfelelőségi problémák és állapot
Az alábbi táblázatban a rögzítettként megjelölt elemek a SCIM feladatokban található megfelelő viselkedést jelentik. Dolgozunk, hogy visszafelé is kompatibilisek legyenek a végrehajtott módosítások. Azonban nem javasoljuk a régi viselkedés megvalósítását. Azt javasoljuk, hogy minden új implementációhoz használja az új viselkedést, és frissítse a meglévő implementációkat.

> [!NOTE]
> Az 2018-ben végrehajtott módosítások esetén visszaállíthatja a customappsso viselkedését. A 2018 óta végrehajtott módosítások esetében az URL-címek használatával visszatérhet a régebbi viselkedésre. Az általunk készített módosítások visszamenőleges kompatibilitásának biztosítása érdekében a rendszer lehetővé teszi, hogy visszaállítsa a régi jobID vagy egy jelzőt. A korábban említettek szerint azonban nem javasoljuk a régi viselkedés megvalósítását. Azt javasoljuk, hogy minden új implementációhoz használja az új viselkedést, és frissítse a meglévő implementációkat.

| **SCIM 2,0 megfelelőségi probléma** |  **Rögzített?** | **Javítás dátuma**  |  **Visszamenőleges kompatibilitás** |
|---|---|---|
| Az Azure AD-nek a "/scim" értéknek kell lennie az alkalmazás SCIM végpont URL-címének gyökerében.  | Yes  |  December 18., 2018 | Visszalépés a customappSSO |
| A bővítmény attribútumai a "." jelölést használják az attribútumok neve előtt a kettőspont ":" jelölése helyett |  Yes  | December 18., 2018  | Visszalépés a customappSSO |
| A többértékű attribútumok javítására vonatkozó kérelmek érvénytelen elérésiút-szűrési szintaxist tartalmaznak. | Yes  |  December 18., 2018  | Visszalépés a customappSSO |
| A csoportos létrehozási kérelmek érvénytelen séma-URI-t tartalmaznak | Yes  |  December 18., 2018  |  Visszalépés a customappSSO |
| A javítási viselkedés frissítése a megfelelőség biztosítása érdekében (például a logikai és a megfelelő csoporttagság-eltávolítások esetén) | No | TBD| előnézet jelző használata |

## <a name="flags-to-alter-the-scim-behavior"></a>A SCIM viselkedését megváltoztató jelzők
Az alapértelmezett SCIM-ügyfél működésének módosításához használja az alábbi jelzőket az alkalmazás bérlői URL-címében.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="A SCIM jelzői a későbbi működéshez.":::

* A következő URL-cím használatával frissítheti a javítási viselkedést, és biztosíthatja a SCIM megfelelőségét (például az aktív logikai és a megfelelő csoporttagság-eltávolítások esetén). Ez a viselkedés jelenleg csak a jelző használatakor érhető el, de a következő néhány hónap során az alapértelmezett viselkedés lesz. Megjegyzés Ez az előnézet jelölő jelenleg nem működik igény szerinti kiépítés esetén. 
  * **URL-cím (scim-kompatibilis):** aadOptscim062020
  * **SCIM RFC-referenciák:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Viselkedés**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **Visszalépési URL-cím:** Ha az új SCIM-kompatibilis viselkedés a nem katalógus alkalmazás alapértelmezett értéke lesz, a következő URL-cím használatával visszatérhet a régi, nem SCIM megfelelő viselkedésre: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Frissítés a régebbi customappsso-feladatokból a SCIM-feladatokra
Az alábbi lépésekkel törli a meglévő customappsso-feladatot, és létrehoz egy új scim-feladatot. 
 
1. Jelentkezzen be Azure Portal a következő címen: https://portal.azure.com .
2. A Azure Portal **Azure Active Directory > vállalati alkalmazások** szakaszában keresse meg és válassza ki a meglévő scim alkalmazást.
3. A meglévő SCIM-alkalmazás **Tulajdonságok** szakaszában másolja át az **objektumazonosítót**.
4. Egy új böngészőablakban nyissa meg a következőt:, https://developer.microsoft.com/graph/graph-explorer és jelentkezzen be rendszergazdaként az Azure ad-bérlőhöz, amelyhez az alkalmazás hozzá van adva.
5. A Graph Explorerben futtassa az alábbi parancsot a kiépítési feladatok AZONOSÍTÓjának megkereséséhez. Cserélje le az "[Object-id]" kifejezést a harmadik lépésből másolt egyszerű szolgáltatásnév (objektumazonosító) helyére.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Feladatok beolvasása](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Feladatok beolvasása") 


6. Az eredmények között másolja a "customappsso" vagy a "scim" kifejezéssel kezdődő teljes "ID" karakterláncot.
7. Futtassa az alábbi parancsot az attribútum-leképezési konfiguráció lekéréséhez, így biztonsági mentést készíthet. Használja ugyanazokat az [Object-id]-t, mint korábban, és cserélje le a [Job-id] elemet az utolsó lépésből másolt kiépítési feladatokhoz tartozó AZONOSÍTÓra.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Séma beolvasása](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Séma beolvasása") 

8. Másolja a JSON-kimenetet az utolsó lépésből, és mentse egy szövegfájlba. A JSON a régi alkalmazáshoz hozzáadott egyéni attribútumokat tartalmazza, és körülbelül néhány ezer sornyi JSON-t kell használnia.
9. Futtassa az alábbi parancsot a kiépítési feladatok törléséhez:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Az alábbi parancs futtatásával hozzon létre egy új kiépítési feladatot, amely a legújabb szolgáltatás-javításokkal rendelkezik.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Az utolsó lépés eredményeiben másolja a "scim" karakterlánccal kezdődő teljes "ID" karakterláncot. Szükség esetén a régi attribútum-hozzárendeléseket újra alkalmazhatja az alábbi parancs futtatásával, a [New-Job-id] lecserélése a másolt új AZONOSÍTÓJÚ feladatokkal, és a JSON-kimenet beírása a #7 lépésből a kérelem törzsében.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Térjen vissza az első böngészőablakba, és válassza ki az alkalmazás **kiépítési** lapját.
13. Ellenőrizze a konfigurációt, majd indítsa el a kiépítési feladatot. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Visszalépés a SCIM-feladatokból a customappsso-feladatokra (nem ajánlott)
 Lehetővé tesszük, hogy visszaminősítse a régi viselkedést, de ne javasolja azt, mert a customappsso nem használja az általunk készített frissítések előnyeit, és előfordulhat, hogy örökre nem támogatott. 

1. Jelentkezzen be Azure Portal a következő címen: https://portal.azure.com .
2. a **Azure Active Directory > vállalati alkalmazások > alkalmazás létrehozása** című Azure Portal szakaszában hozzon létre egy új, **nem** katalógusbeli alkalmazást.
3. Az új egyéni alkalmazás **Tulajdonságok** szakaszában másolja ki az **objektumazonosítót**.
4. Egy új böngészőablakban nyissa meg a következőt:, https://developer.microsoft.com/graph/graph-explorer és jelentkezzen be rendszergazdaként az Azure ad-bérlőhöz, amelyhez az alkalmazás hozzá van adva.
5. A Graph Explorerben futtassa az alábbi parancsot az alkalmazás létesítési konfigurációjának inicializálásához.
   Cserélje le az "[Object-id]" kifejezést a harmadik lépésből másolt egyszerű szolgáltatásnév (objektumazonosító) helyére.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Térjen vissza az első böngészőablakba, és válassza ki az alkalmazás **kiépítési** lapját.
7. A szokásos módon végezze el a felhasználó kiépítési konfigurációját.


## <a name="next-steps"></a>Következő lépések
[További információ az SaaS-alkalmazásokhoz való kiépítés és kiépítés ellen](user-provisioning.md)
