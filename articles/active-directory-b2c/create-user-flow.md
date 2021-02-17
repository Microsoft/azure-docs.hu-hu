---
title: Felhasználói folyamat létrehozása – Azure Active Directory B2C
description: Megtudhatja, hogyan hozhat létre felhasználói folyamatokat a Azure Portalban az alkalmazások regisztrálásának, bejelentkezésének és felhasználói profiljának szerkesztésének engedélyezéséhez Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bd436b972dfb1549232831b1f07c3726ff459dd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556505"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Felhasználói folyamat létrehozása Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) bérlőben különböző típusú [felhasználói folyamatokat](user-flow-overview.md) hozhat létre, és igény szerint használhatja azokat az alkalmazásokban. A felhasználói folyamatok az alkalmazások között újra felhasználhatók.

> [!IMPORTANT]
> Megváltoztattuk a felhasználói folyamatok verzióinak elnevezéseit. Korábban V1 (az éles környezetben való használatra kész) verziókat, valamint V1.1 és V2 (előzetes) verziókat kínáltunk. Most összevontuk a felhasználói folyamatokat a **javasolt** (következő generációs előzetes verzió) és **standard** (általánosan elérhető) verziókra. Az összes, 1.1-es és v2-es korábbi előzetes verziójú felhasználói folyamat a 2021-es **augusztus 1-től** az elavult elérési úton van. Részletekért lásd: [a felhasználói folyamatok verziói Azure ad B2Cban](user-flow-versions.md).

## <a name="before-you-begin"></a>Előkészületek

- **Regisztrálja az** új felhasználói folyamat teszteléséhez használni kívánt alkalmazást. Példaként tekintse meg az [oktatóanyag: webalkalmazás regisztrálása a Azure ad B2Cban](tutorial-register-applications.md)című témakört.
- **Külső identitás-szolgáltatók hozzáadása** , ha engedélyezni szeretné a felhasználói bejelentkezést olyan szolgáltatókkal, mint például az Azure ad, az Amazon, a Facebook, a GitHub, a LinkedIn, a Microsoft vagy a Twitter. Lásd: [identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure ad B2C](add-identity-provider.md).
- **Konfigurálja a helyi fiók identitás-szolgáltatóját** a bérlő helyi fiókjainak támogatásához használni kívánt identitási típusok (e-mail, Felhasználónév, telefonszám) megadásához. Ezeket a támogatott identitási típusok közül választhat, ha egyéni felhasználói folyamatokat hoz létre. Amikor a felhasználó befejezi a felhasználói folyamatot, a rendszer létrehoz egy helyi fiókot a Azure AD B2C könyvtárban, és a **helyi fiók** identitás-szolgáltatója hitelesíti a felhasználó adatait. Konfigurálja a bérlő helyi fiókjának identitás-szolgáltatóját a következő lépésekkel:

   1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 
   2. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
   3. A Azure Portal tetején található keresési sávban keresse meg és válassza ki a **Azure ad B2C**.
   4. A **kezelés** területen válassza az **identitás-szolgáltatók** elemet.
   5. Az identitás-szolgáltató listában válassza a **helyi fiók** lehetőséget.
   6. A **helyi identitásszolgáltató konfigurálása** lapon válassza ki az összes támogatni kívánt identitási típust. Az itt kiválasztott beállítások egyszerűen elérhetővé válnak a később létrehozott felhasználói folyamatok számára:
      - **Telefon** (előzetes verzió): lehetővé teszi a felhasználó számára a telefonszám megadását, amelyet a rendszer a regisztráláskor ellenőriz, és a felhasználói azonosítóját adja meg.
      - **E-mail** (alapértelmezett): lehetővé teszi, hogy a felhasználó megadjon egy e-mail-címet, amelyet a rendszer a regisztráláskor ellenőriz, és a felhasználói azonosítóját adja meg.
      - **Username**: lehetővé teszi, hogy a felhasználó saját egyedi felhasználói azonosítót hozzon létre. A rendszer begyűjti az e-mail-címet a felhasználótól, és ellenőrizte azokat.
    7. Kattintson a **Mentés** gombra.

## <a name="create-a-user-flow"></a>Felhasználói folyamat létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

    ![B2C-bérlő, címtár és előfizetés panel, Azure Portal](./media/create-user-flow/directory-subscription-pane.png)

3. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
4. A **házirendek** területen válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.

    ![Felhasználói folyamatok lap a portálon új felhasználói folyamat gomb kiemelve](./media/create-user-flow/signup-signin-user-flow.png)

5. A **felhasználói folyamat létrehozása** lapon válassza ki a létrehozni kívánt felhasználói folyamat típusát (lásd: [felhasználói folyamatok Azure ad B2C](user-flow-overview.md) áttekintése).

    ![Válassza ki a felhasználói folyamat lapot a regisztrációs és bejelentkezési folyamat kiemelésével](./media/create-user-flow/select-user-flow-type.png)

6. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget. ([További](user-flow-versions.md) információ a felhasználói folyamatok verzióiról.)

    ![Felhasználói folyamat létrehozása lap Azure Portal a tulajdonságok kiemelve](./media/create-user-flow/select-version.png)

7. Adja meg a felhasználói folyamat **nevét** (például *signupsignin1*, *profileediting1*, *passwordreset1*).
8. Az **identitás-szolgáltatók** területen válassza a létrehozandó felhasználói folyamat típusától függően a beállításokat:

   - **Helyi fiók**. Ha engedélyezni szeretné, hogy a felhasználók helyi fiókokat hozzanak létre a Azure AD B2C-bérlőben, válassza ki a használni kívánt azonosító típusát (például e-mail, felhasználói azonosító vagy telefon). A lista csak azokat az identitási típusokat tartalmazza, amelyek a [helyi fiók identitás-szolgáltatójának](#before-you-begin) beállításaiban vannak konfigurálva.

   - **Közösségi identitás-szolgáltatók**. Ha engedélyezni szeretné a felhasználói bejelentkezést a [hozzáadott közösségi identitás-szolgáltatókkal](add-identity-provider.md), például az Azure ad, az Amazon, a Facebook, a GitHub, a LinkedIn, a Microsoft vagy a Twitter, válassza ki a szolgáltatókat a listából.

9. **Felhasználói attribútumok és jogcímek** esetében válassza ki azokat a jogcímeket és attribútumokat, amelyeket szeretne összegyűjteni, és a felhasználótól a regisztráció során elküldeni. Válassza a **továbbiak megjelenítése** lehetőséget. Válassza ki az attribútumokat és jogcímeket, majd kattintson **az OK gombra**.

    ![Attribútumok és jogcímek kiválasztása lap három jogcímek kiválasztásával](./media/create-user-flow/signup-signin-attributes.png)

10. A felhasználói folyamat hozzáadásához válassza a **Létrehozás** lehetőséget. A *B2C_1* előtagja automatikusan előtagértéke a nevet.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza a **szabályzatok**  >  **felhasználói folyamatok** lehetőséget, majd válassza ki a létrehozott felhasználói folyamatot. A felhasználói folyamat áttekintése lapon válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki az 1. lépésben regisztrált webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Válassza a **felhasználói folyamat futtatása** lehetőséget.
2. A tesztelni kívánt felhasználói folyamat típusától függően vagy regisztráljon egy érvényes e-mail-címmel, és kövesse a regisztrációs folyamatot, vagy jelentkezzen be egy korábban létrehozott fiók használatával.

    ![Felhasználói folyamat futtatása lap a portálon a felhasználói folyamat futtatása gomb kiemelve](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Kövesse a felhasználói folyamatra vonatkozó utasításokat. A felhasználói folyamat befejezése után a rendszer visszaküldi a tokent, `https://jwt.ms` és megjeleníti az Ön számára.

> [!NOTE]
> A "felhasználói folyamat futtatása" élmény jelenleg nem kompatibilis a SPA válasz URL-címével, amely az engedélyezési kód folyamatát használja. Ha a "felhasználói folyamat futtatása" funkciót szeretné használni az ilyen típusú alkalmazásokhoz, regisztráljon egy "web" típusú válasz URL-címet, és az [itt](tutorial-register-spa.md)leírtak alapján engedélyezze az implicit folyamatot.

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés hozzáadása Azure AD B2C felhasználói folyamatokhoz](conditional-access-user-flow.md)
- [Felhasználói felület testreszabása Azure AD B2C felhasználói folyamatban](customize-ui-with-html.md)
