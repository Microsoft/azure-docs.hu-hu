---
title: Kockázat vizsgálata Azure Active Directory B2C Identity Protectionvel
description: Ismerje meg, hogyan vizsgálhatja meg a kockázatos felhasználókat és észleléseket a Azure AD B2C Identity Protectionben
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102055704"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Kockázatelemzés a Azure AD B2C Identity Protection szolgáltatásával

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Az Identity Protection folyamatos kockázati észlelést biztosít a Azure AD B2C bérlő számára. Lehetővé teszi a szervezetek számára az identitás-alapú kockázatok felderítését, kivizsgálását és szervizelését. Az Identity Protection olyan kockázati jelentésekkel is rendelkezik, amelyek a Azure AD B2C bérlők személyazonossági kockázatának kivizsgálására használhatók. Ebből a cikkből megtudhatja, hogyan vizsgálhatja és csökkentheti a kockázatokat.

## <a name="overview"></a>Áttekintés

Azure AD B2C Identity Protection két jelentést biztosít. A *kockázatos felhasználók* jelentés az a hely, ahol a rendszergazdák megtalálhatják, hogy mely felhasználók veszélyeztetettek, és melyek az észlelésekkel kapcsolatos részletek. A *kockázati észlelésekről* szóló jelentés információt nyújt az egyes kockázatok észleléséről, beleértve a típust, a más, egyszerre kiváltott kockázatokat, a bejelentkezési kísérlet helyét és egyebeket.

Minden jelentés a jelentés tetején látható időszakra vonatkozó összes észlelési listával elindul. A jelentések a jelentés tetején lévő szűrők használatával szűrhetők. A rendszergazdák dönthetnek úgy, hogy letöltik az adatfájlokat, vagy az [MS Graph API és Microsoft Graph POWERSHELL SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md) használatával folyamatosan exportálják az adatfájlokat.

## <a name="service-limitations-and-considerations"></a>A szolgáltatásra vonatkozó korlátozások és megfontolások

Az Identity Protection használatakor vegye figyelembe a következőket:

- Az Identity Protection alapértelmezés szerint be van kapcsolva.
- A személyazonosság védelme a helyi és a közösségi identitások, például a Google vagy a Facebook esetében egyaránt elérhető. A közösségi identitások esetében aktiválni kell a feltételes hozzáférést. Az észlelés korlátozott, mert a közösségi fiók hitelesítő adatait a külső identitás szolgáltatója kezeli.
- Azure AD B2C bérlők esetében csak a [Azure ad Identity Protection kockázati észlelések](../active-directory/identity-protection/overview-identity-protection.md) egy részhalmaza érhető el. Azure AD B2C a következő kockázati észleléseket támogatja:  

|Kockázat észlelésének típusa  |Description  |
|---------|---------|
| Szokatlan utazás     | Bejelentkezés szokatlan helyről a felhasználó legutóbbi bejelentkezései alapján.        |
|Névtelen IP-cím     | Bejelentkezés névtelen IP-címről (például: Tor-böngésző, névtelenség VPN-ek).        |
|Kártevők társított IP-címe     | Bejelentkezés kártevővel összekapcsolt IP-címről.         |
|Szokatlan bejelentkezési tulajdonságok     | Bejelentkezés az adott felhasználóhoz a közelmúltban nem látott tulajdonságokkal.        |
|A rendszergazda által megerősített felhasználó sérült    | A rendszergazda azt jelezte, hogy a felhasználót feltörték.             |
|Jelszó spray     | Bejelentkezés jelszó-szórásos támadással.      |
|Azure AD-fenyegetések felderítése     | A Microsoft belső és külső veszélyforrások hírszerzési forrásai ismert támadási mintát azonosítottak.        |

## <a name="pricing-tier"></a>Tarifacsomag

Azure AD B2C prémium P2 szükséges egyes Identity Protection-funkciókhoz. Ha szükséges, [módosítsa a Azure ad B2C díjszabási szintjét a prémium P2-](./billing.md)re. Az alábbi táblázat összefoglalja az Identity Protection szolgáltatásait és a szükséges díjszabási szintet.  

|Szolgáltatás   |P1   |P2|
|----------|:-----------:|:------------:|
|A kockázatos felhasználók jelentése     |&#x2713; |&#x2713; |
|Kockázatos felhasználók jelentés részletei  | |&#x2713; |
|Kockázatos felhasználók jelentés szervizelése    | &#x2713; |&#x2713; |
|Kockázati észlelési jelentés   |&#x2713;|&#x2713;|
|Kockázati észlelések – jelentés részletei  ||&#x2713;|
|Jelentés letöltése |  &#x2713;| &#x2713;|
|MS Graph API hozzáférés |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Kockázatos felhasználók vizsgálata

A kockázatos felhasználók jelentés által biztosított információkkal a rendszergazdák a következőket kereshetik:

- Mely felhasználók vannak kitéve kockázatoknak, vagy kockázatos volt a kockázata?
- Az észlelések részletei
- Az összes kockázatos bejelentkezés előzményei
- Kockázati előzmények
 
A rendszergazdák ezután dönthetnek úgy, hogy műveleteket hajtanak végre ezekről az eseményekről. A rendszergazdák a következőket választhatják:

- Felhasználói jelszó alaphelyzetbe állítása
- Felhasználói biztonság megerősítése
- Felhasználói kockázat elvetése
- A felhasználó bejelentkezésének tiltása
- További vizsgálat az Azure ATP használatával

### <a name="navigating-the-risky-users-report"></a>A kockázatos felhasználók jelentésének navigálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

1. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget. Vagy a keresőmező használatával megkeresheti és kiválaszthatja a **Azure ad B2C**.

1. A **Biztonság** területen válassza a **kockázatos felhasználók (előzetes verzió)** lehetőséget.

   ![Kockázatos felhasználók](media/identity-protection-investigate-risk/risky-users.png)

    Az egyes bejegyzések kijelölésekor a rendszer kibontja a részletek ablakot az észlelések alatt. A részletek nézet lehetővé teszi, hogy a rendszergazdák megvizsgálják és végrehajtsák az egyes észlelések műveleteit.

    ![Kockázatos felhasználók műveletei](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Kockázati észlelési jelentés

A kockázati észlelési jelentés az elmúlt 90 napra (három hónapra) szűrt adattípusokat tartalmaz.

A kockázati észlelések jelentés által biztosított információkkal a rendszergazdák a következőket kereshetik:

- Az egyes kockázatok észlelésére vonatkozó információk, beleértve a típust.
- Az adott időpontban kiváltott egyéb kockázatok.
- Bejelentkezési kísérlet helye.

A rendszergazdák ezután dönthetnek úgy, hogy visszatérnek a felhasználó kockázati vagy bejelentkezési jelentéséhez, hogy a gyűjtött információk alapján hajtsák végre a műveleteket.

### <a name="navigating-the-risk-detections-report"></a>A kockázati észlelések jelentésének navigálása

1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **Biztonság** területen válassza a **kockázati észlelések (előzetes verzió)** lehetőséget.

   ![Kockázatészlelések](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés hozzáadása egy felhasználói folyamathoz](conditional-access-user-flow.md).