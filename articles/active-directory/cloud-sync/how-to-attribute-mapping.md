---
title: Attribútumok leképezése Azure AD Connect Cloud syncben
description: Ez a cikk azt ismerteti, hogyan használható a Azure AD Connect Cloud Sync funkciója az attribútumok leképezéséhez.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555210"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Attribútumok leképezése Azure AD Connect Cloud syncben

A (z) Azure Active Directory (Azure AD) felhőalapú szinkronizálási funkciójával összekapcsolhatja a helyszíni felhasználói vagy csoportosítási objektumok és az Azure AD-objektumok közötti leképezési attribútumokat. Ez a funkció hozzá lett adva a felhőalapú szinkronizálási konfigurációhoz.

Az alapértelmezett attribútumok hozzárendeléseit az üzleti igényeknek megfelelően testreszabhatja (módosíthatja, törölheti vagy létrehozhatja). A szinkronizált attribútumok listáját itt tekintheti [meg: Azure Active Directory szinkronizált attribútumok](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Az attribútumok leképezésének ismertetése
Az attribútum-hozzárendeléssel szabályozhatja, hogy az attribútumok hogyan legyenek feltöltve az Azure AD-ben. Az Azure AD négy leképezési típust támogat:

- **Direct (közvetlen**): a TARGET attribútum a Active Directory csatolt objektum attribútumának értékével van feltöltve.
- **Állandó**: a TARGET attribútum egy megadott karakterlánccal van feltöltve.
- **Kifejezés**: a TARGET attribútum a parancsfájl-szerű kifejezés eredménye alapján van feltöltve. További információ: [kifejezések írása a Azure Active Directory attribútum-hozzárendelésekhez](reference-expressions.md).
- **Nincs**: a cél attribútum változatlan marad. Ha azonban a cél attribútum még mindig üres, akkor a rendszer a megadott alapértelmezett értékkel tölti fel.

Ezekkel az alaptípusokkal együtt az egyéni attribútumok hozzárendelése egy opcionális *alapértelmezett* érték-hozzárendelés fogalmát is támogatja. Az alapértelmezett érték-hozzárendelés biztosítja, hogy a TARGET attribútum értéke akkor legyen feltöltve, ha az Azure AD vagy a célobjektum nem rendelkezik értékkel. A leggyakoribb beállítás az, ha üresen hagyja ezt a beállítást.

## <a name="understand-properties-of-attribute-mapping"></a>Az attribútum-hozzárendelés tulajdonságainak megismerése

A Type tulajdonsággal együtt az attribútum-hozzárendelések a következő attribútumokat támogatják:

- **Forrás attribútum**: a forrásrendszer felhasználói attribútuma (példa: Active Directory).
- **Target attribútum**: a cél rendszer felhasználói attribútuma (példa: Azure Active Directory).
- **Alapértelmezett érték, ha Null (nem kötelező)**: a célként megadott rendszernek átadandó érték, ha a forrás attribútum értéke null. Ez az érték csak a felhasználó létrehozásakor lesz kiépítve. Egy meglévő felhasználó frissítésekor nem lesz kiépítve.  
- **A leképezés alkalmazása**:
  - **Mindig**: alkalmazza ezt a leképezést a felhasználó-létrehozási és a frissítési műveletekre is.
  - **Csak a létrehozás során**: alkalmazza ezt a leképezést csak a felhasználó-létrehozási műveletekre.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan használható a Azure Portal az attribútumok leképezéséhez.  További információ a Microsoft Graph használatáról: [átalakítások](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Attribútum-hozzárendelés hozzáadása

Az új képesség használatához kövesse az alábbi lépéseket:

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
2.  Válassza a **Azure ad Connect** lehetőséget.
3.  Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.

    ![A Felhőbeli szinkronizálás kezelésére mutató hivatkozást bemutató képernyőkép.](media/how-to-install/install-6.png)

4. A **konfiguráció** területen válassza ki a konfigurációt.
5. Válassza **a kattintson ide a leképezések szerkesztéséhez**.  Ez a hivatkozás megnyitja az **attribútum-hozzárendelések** képernyőt.

    ![Az attribútumok hozzáadására mutató hivatkozást bemutató képernyőkép.](media/how-to-attribute-mapping/mapping-6.png)

6.  Válassza az **attribútum hozzáadása** elemet.

    ![Képernyőfelvétel: az attribútumok hozzáadására szolgáló gomb megjelenítése, valamint az attribútumok és a leképezési típusok listája.](media/how-to-attribute-mapping/mapping-1.png)

7. Válassza ki a leképezés típusát. Ebben a példában a **kifejezést** használjuk.
8. Adja meg a kifejezést a mezőben. Ebben a példában a következőt használjuk: `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Adja meg a cél attribútumot. Ebben a példában a **ExtensionAttribute15**-t használjuk.
10. Válassza ki, hogy mikor szeretné alkalmazni a leképezést, majd válassza az **alkalmaz** lehetőséget.

    ![Képernyőkép, amely megjeleníti a kitöltött mezőket az attribútumok leképezésének létrehozásához.](media/how-to-attribute-mapping/mapping-2a.png)

11. Az attribútum- **hozzárendelések** képernyőn megjelenik az új attribútum-hozzárendelés.  
12. Válassza a **séma mentése** lehetőséget.

    ![A séma mentése gombot megjelenítő képernyőkép.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Az attribútumok leképezésének tesztelése

Az attribútumok leképezésének teszteléséhez [igény szerinti üzembe](how-to-on-demand-provision.md)helyezést is használhat: 

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
2. Válassza a **Azure ad Connect** lehetőséget.
3. Válassza a **kiépítés kezelése** lehetőséget.
4. A **konfiguráció** területen válassza ki a konfigurációt.
5. Az **Érvényesítés** területen válassza a **felhasználó kiépítése** gombot. 
6. A **kiépítés igény szerint** képernyőn adja meg egy felhasználó vagy csoport megkülönböztető nevét, és válassza a **kiépítés** gombot. 

   A képernyőn látható, hogy a kiépítés folyamatban van.

   ![A kiépítés folyamatát bemutató képernyőkép.](media/how-to-attribute-mapping/mapping-4.png)

8. A kiépítés befejezését követően egy sikeres képernyő jelenik meg négy zöld pipa jel. 

   A **művelet végrehajtása** területen válassza a **részletek megtekintése** lehetőséget. A jobb oldalon az új attribútum szinkronizálása és az alkalmazott kifejezés látható.

   ![A sikeres és az Exportálás részleteit bemutató képernyőkép.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [Kifejezések írása attribútum-hozzárendelésekhez](reference-expressions.md)
- [Az Azure Active Directoryval szinkronizált attribútumok](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
