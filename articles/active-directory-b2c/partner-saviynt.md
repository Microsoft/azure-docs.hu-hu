---
title: Oktatóanyag a Saviynt konfigurálásához a Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Oktatóanyag a Azure Active Directory B2C a Saviynt való konfigurálásához az alkalmazások integrálásához, az informatikai modernizáció egyszerűsítése és a jobb biztonság, irányítás és megfelelőség elősegítése érdekében. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b1889a693c08ce78a8bd06b6fe348c0c67bf3812
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465674"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Oktatóanyag a Saviynt konfigurálásához a Azure Active Directory B2C

Ebben a példában útmutatást adunk a Azure Active Directory (AD) B2C és a [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/)integrálásához. A Saviynt Security Manager platformja a mai üzleti igényeknek megfelelő, egységes platformon biztosítja a láthatóságot, a biztonságot és a kormányzást. A Saviynt magában foglalja az alkalmazások kockázatát és irányítását, az infrastruktúra kezelését, a Kiemelt fiókok felügyeletét és az ügyfelek kockázati elemzését.

Ebben a példában az oktatóanyagban be kell állítania a Saviynt, hogy részletes hozzáférés-vezérlési alapú delegált felügyeletet biztosítson Azure AD B2C felhasználók számára. A Saviynt a következő ellenőrzésekkel állapítja meg, hogy a felhasználó rendelkezik-e jogosultsággal Azure AD B2C felhasználók kezeléséhez.

- A szolgáltatás szintjének biztonsága annak megállapítására, hogy egy felhasználó végrehajthat-e egy adott műveletet. Például hozzon létre egy felhasználót, frissítse a felhasználót, állítsa alaphelyzetbe a felhasználói jelszót, és így tovább.

- A mezők szintjének biztonsága annak megállapítására, hogy egy felhasználó egy másik felhasználó adott attribútumát tudja-e olvasni/írni a felhasználói felügyeleti műveletek során. A helpdesk ügynök például csak a telefonszámot tudja frissíteni, és az összes többi attribútum csak olvasható.

- Adatszintű biztonság annak megállapításához, hogy egy felhasználó végezhet-e bizonyos műveletet egy adott felhasználónál. Például az Egyesült királyságbeli régió ügyfélszolgálati rendszergazdája csak az Egyesült királyságbeli felhasználókat tudja kezelni.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Azure ad B2C bérlő](./tutorial-create-tenant.md). A bérlő az Azure-előfizetéshez van csatolva.

- Saviynt- [előfizetés](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>Forgatókönyv leírása

A Saviynt-integráció a következő összetevőket tartalmazza:

- [Azure ad B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – a vállalatok közötti identitás olyan szolgáltatás, amely lehetővé teszi, hogy az ügyfelek regisztráljanak, jelentkezzenek be és kezeljék a profiljaikat.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) – a személyazonosság-irányítási platform, amely részletesen delegált felügyeletet biztosít a felhasználói életciklus kezeléséhez és a Azure ad B2C felhasználók hozzáférésének irányításához.  

- [Microsoft Graph API](/graph/use-the-api) – ez az API biztosítja a Saviynt számára a Azure ad B2C felhasználók és a hozzájuk tartozó hozzáférések Azure ad B2Cban való kezeléséhez szükséges interfészeket.

A következő architektúra-diagram a megvalósítást mutatja be.

![Saviynt architektúra diagramot ábrázoló kép](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Lépés | Leírás |
|:-----| :-----------|
| 1. | A meghatalmazott rendszergazda elindítja Azure AD B2C felhasználói művelet kezelését a Saviynt-on keresztül.
| 2. | Ha a meghatalmazott rendszergazda végrehajtja az adott műveletet, a Saviynt ellenőrzi az engedélyezési motorját.
| 3. | A Saviynt engedélyezési motorja sikeres és sikertelen válaszadási választ küld.
| 4. | A Saviynt lehetővé teszi a delegált rendszergazda számára a szükséges művelet elvégzését.
| 5. | A Saviynt meghívja a Microsoft Graph API-t, valamint a felhasználói attribútumokat a felhasználó kezeléséhez Azure AD B2C
| 6. | A Microsoft Graph API-ban a felhasználó létrehozása/frissítése/törlése Azure AD B2C-ben történik.
| 7. | A Azure AD B2C sikeres/sikertelen választ fog küldeni.
| 8. | Microsoft Graph API ezután visszaadja a választ a Saviynt.

## <a name="onboard-with-saviynt"></a>Saviynt

1. Saviynt-fiók létrehozásához forduljon a [Saviynt](https://saviynt.com/contact-us/)

2. Delegált felügyeleti házirendek létrehozása és felhasználók társítása delegált rendszergazdaként különböző szerepkörökkel.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Azure AD B2C konfigurálása a Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Azure AD-alkalmazás létrehozása a Saviynt-hez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/#home).

2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

3. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.

4. Válassza **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.

5. Adja meg az alkalmazás nevét. Például Saviynt, majd válassza a **Létrehozás** lehetőséget.

6. Lépjen az **API-engedélyek** elemre, és válassza **a + engedély hozzáadása elemet.**

7. Megjelenik a kérelem API-engedélyek lap. Válassza a **Microsoft API** -k fület, és a leggyakrabban használt Microsoft API-kkal válassza a **Microsoft Graph** lehetőséget.

8. Lépjen a következő lapra, és válassza az **alkalmazás engedélyei** lehetőséget.

9. Válassza a **könyvtár** lehetőséget, majd a **könyvtár. Read. All** és a **Directory. ReadWrite. All** jelölőnégyzetet.

10. Válassza az **engedélyek hozzáadása** lehetőséget. Tekintse át a hozzáadott engedélyeket.

11. Válassza **a rendszergazdai jóváhagyás megadása az alapértelmezett címtár számára**  >  **Mentés** lehetőséget.

12. Nyissa meg a **tanúsítványok és titkok** lehetőséget, majd válassza az **+ ügyfél titkos kulcsának hozzáadása** elemet. Adja meg az ügyfél titkos kulcsának leírását, válassza a lejárat lehetőséget, majd kattintson a **Hozzáadás** gombra.

13. Létrejön a titkos kulcs, és megjelenik az ügyfél titkos kulcsa szakaszban.

    >[!NOTE]
    > Az ügyfél titkos kulcsát később kell megadnia.

14. Nyissa meg az **áttekintést** , és szerezze be az **ügyfél-azonosítót** és a **bérlői azonosítót**.

15. A telepítőnek a Saviynt-ben való végrehajtásához a bérlő AZONOSÍTÓját, az ügyfél-azonosítót és az ügyfél-titkos kulcsot is meg kell adni.

### <a name="enable-saviynt-to-delete-users"></a>Felhasználók törlésének engedélyezése a Saviynt

Az alábbi lépések azt ismertetik, hogyan engedélyezhető a Saviynt a felhasználói törlési műveletek végrehajtásához a Azure AD B2Cban.

>[!NOTE]
>[Értékelje ki a kockázatot, mielőtt hozzáférést adna a rendszergazdai szerepkörökhöz egy egyszerű szolgáltatáshoz.](../active-directory/develop/app-objects-and-service-principals.md)

1. Telepítse a MSOnline PowerShell-modul legújabb verzióját egy Windows Workstation/Server rendszerre.

2. Kapcsolódjon a AzureAD PowerShell-modulhoz, és hajtsa végre a következő parancsokat:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>A megoldás tesztelése

Keresse meg a Saviynt-bérlőt, és tesztelje a felhasználói életciklus kezelésének és hozzáférésének szabályozására vonatkozó használati esetet.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

- [Webes API-alkalmazás létrehozása](./add-web-api-application.md)