---
title: Azure AD Connect V2-végpont szinkronizálása | Microsoft Docs
description: Ez a dokumentum az Azure AD Connect Sync v2 végpontok API frissítéseit tartalmazza.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4434b59044aed8c9814431864e5c3c9b7d98254c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575717"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect Sync v2 végpont API 
A Microsoft üzembe helyezett egy új végpontot (API- Azure AD Connect, amely javítja a szinkronizálási szolgáltatás műveleteinek teljesítményét a Azure Active Directory. Az új V2-végpont használatával észrevehető teljesítménynövekedést tapasztalhat az Azure AD-be való exportálás és importálás során. Ez az új végpont a következőket támogatja:
    
 - csoportok szinkronizálása legfeljebb 250 000 taggal
 - teljesítménynövekedés az Azure AD-be történő exportálás és importálás során
 
> [!NOTE]
> Az új végpont jelenleg nem rendelkezik konfigurált csoportméret-korláttal a Microsoft 365 visszaírt csoportokra. Ez hatással lehet a szinkronizálási és Active Directory késésére. Ajánlott növekményesen növelni a csoportméreteket.  

>[!NOTE]
> A Azure AD Connect V2-végpont API jelenleg csak ezekben az Azure-környezetekben érhető el:
> - Azure Commercial
> - Azure China-felhő
> - Azure US Government-felhő Az Azure német felhőben nem lesz elérhető

## <a name="prerequisites"></a>Előfeltételek  
Az új V2-végpont csak akkor használható, ha Azure AD Connect [1.5.30.0-s](https://www.microsoft.com/download/details.aspx?id=47594) vagy újabb verziót használja, és az alábbi üzembe helyezési lépéseket követve engedélyezi a V2-végpontot a Azure AD Connect-kiszolgálón.   

## <a name="deployment-guidance"></a>Üzembe helyezési útmutató 
A V2-végpont [Azure AD Connect 1.5.30.0-s](https://www.microsoft.com/download/details.aspx?id=47594) vagy újabb verzióját kell üzembe helyeznie. A letöltéshez használja a megadott hivatkozást. 

Javasoljuk, hogy kövesse [](./how-to-upgrade-previous-version.md#swing-migration) a swing migrálási módszert az új végpont környezetben való gördítéséhez. Ez egyértelmű válságtervet biztosít arra az esetre, ha nagyobb visszaállításra lenne szükség. Az alábbi példa bemutatja, hogyan használható ebben a forgatókönyvben a migrálás. A migrálásos üzembe helyezési módszerrel kapcsolatos további információkért tekintse meg a megadott hivatkozást. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>A 2-es végpont üzembe helyezésének átugró migrálása
A következő lépések végigvezetik a v2 végpont a swing metódussal való üzembe helyezésén.

1. Telepítse a V2 végpontot az aktuális előkészítési kiszolgálón. Az alábbi lépésekben ezt a kiszolgálót **V2 kiszolgálóként** fogjuk ismerni. Az aktuális aktív kiszolgáló a V1 végpont használatával folytatja az éles számítási feladat feldolgozását. Ezt az alábbi **V1 kiszolgálónak nevezzük.**
1. Ellenőrizze, hogy a **V2-kiszolgáló továbbra** is a várt módon feldolgozta-e az importokat. Ebben a szakaszban a nagy csoportok nem lesznek kiépítve az Azure AD-be vagy a virtuális AD-be, de ellenőrizheti, hogy a frissítés nem eredményezett-e semmilyen más váratlan hatást a meglévő szinkronizálási folyamatra. 
2. Az érvényesítés befejezése után váltsa át a **V2** kiszolgálót aktív kiszolgálóra, a **V1** kiszolgálót pedig átmeneti kiszolgálóra. A szinkronizálás hatókörében jelenleg nagy méretű csoportok lesznek kiépítve az Azure AD-be Microsoft 365, valamint nagy méretű, egységesített csoportok lesznek kiépítve az AD-be, ha engedélyezve van a csoportvisszaírás.
3. Ellenőrizze, hogy a **V2-kiszolgáló** sikeresen végrehajtja-e és feldolgoz-e a nagy csoportokat. Dönthet úgy, hogy ebben a lépésben marad, és egy ideig figyeli a szinkronizálási folyamatot.
  >[!NOTE]
  > Ha vissza kell térte a korábbi konfigurációra, végrehajthat egy áttűnéses migrálást a **V2** kiszolgálóról a **V1 kiszolgálóra.** Mivel a V1-végpont nem támogatja az 50 000-esnál több tagú csoportokat, az Azure AD Connect által az Azure AD-ben vagy a saját AD-ben létesített nagyméretű csoportokat a rendszer ezután törli. 
4. Ha magabiztosan használja a V2-végpontot, frissítse a **V1** kiszolgálót a V2-végpont használatának megkezdéséhez. 
 

## <a name="expectations-of-performance-impact"></a>A teljesítményre gyakorolt hatásra vonatkozó elvárások  
A V2 végpont használata esetén a teljesítménynövekedés a szinkronizált csoportok számának, a csoportok méretének és a csoportváltozásuknak (a felhasználók csoporttagként való hozzáadásából és eltávolításából eredő tevékenység) függvénye. Az új végpont használata a szinkronizált csoportok számának, méretének vagy adatváltozásának növelése nélkül rövidebb exportálási és importálási időt eredményez az Azure AD-be. 
 
A teljesítménynövekedést azonban a nagy csoportok szinkronizálásához szükséges további feldolgozás is negatívan használhatja. A teljes szinkronizálási idő úgy növekedhet, hogy túl sok nagy csoportot ad hozzá a szinkronizálási folyamathoz.  

Annak jobb megértése érdekében, hogy az új csoportok hogyan befolyásolják a szinkronizálási teljesítményt, javasoljuk, hogy először csak néhány, 100 000 tagnál kevesebb taggal bíró nagy csoportot szinkronizál. Ezután növelheti a csoportok számát és méretét, ha több csoportot hoz létre a hatókörben, a szervezeti egység, az attribútum vagy a csoport maximális méretének szűrésével. A teljesítménybeli fejlesztések az Azure AD-összekötő exportálási és importálási feladatain fognak megvalósulni, nem a helyszíni AD-összekötőn. 

## <a name="deployment-step-by-step"></a>Üzembe helyezés lépésről lépésre 
Az alábbi három fázis egy részletes példa az új V2 végpont üzembe helyezésére.  A fázisokat használhatja útmutatóként az üzembe helyezéshez.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>1. fázis – az Azure AD Connect 
Javasoljuk, hogy először végezze el Azure AD Connect [1.5.30.0-s](https://www.microsoft.com/download/details.aspx?id=47594) vagy újabb verzióra való telepítés vagy frissítés lépéseit, és ellenőrizze a szinkronizálási folyamatot, mielőtt a második fázisra lép, amelyben engedélyezni fogja a V2-végpontot. A Azure AD Connect kiszolgálón: 


1. [Nem kötelező] Adatbázis biztonsági mentésének készítése 
2. Telepítse vagy [frissítsen az Azure AD Connect 1.5.30.0-s](https://www.microsoft.com/download/details.aspx?id=47594) vagy újabb verziójára.
3. A telepítés ellenőrzése 

### <a name="phase-2--enable-the-v2-endpoint"></a>2. fázis – a V2 végpont engedélyezése 
A következő lépés a V2 végpont engedélyezése. 

> [!NOTE]
> Miután engedélyezte a V2-végpontot a kiszolgálóhoz, láthatja a meglévő számítási feladatok teljesítménybeli javulását. Az 50 000 tagból több csoporttal azonban még nem fog tudni csoportokat szinkronizálni. 

A V2 végpontra való váltáshoz kövesse az alábbi lépéseket: 

1. Nyisson meg egy PowerShell-parancssort rendszergazdaként. 
2. Tiltsa le a szinkronizálásütemezőt annak ellenőrzése után, hogy nem futnak-e szinkronizálási műveletek: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importálja az új modult: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Váltson a v2-végpontra:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Engedélyezte a V2-végpontot a kiszolgálóhoz. Mielőtt továbblép a következő fázisra, amelyben növelni fogja a csoport méretkorlátját, ellenőrizze, hogy nincsenek-e váratlan eredmények a V2 végpont engedélyezése után. 
>[!NOTE]
>A fájlok/modulok elérési útjai eltérő meghajtóbetűjelet használhatnak, attól függően, hogy milyen telepítési útvonalat adott meg a Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>3. fázis – a csoporttagsági korlát növelése 
Miután ellenőrizte, hogy a szolgáltatás nem várt eredmények nélkül fut-e, folytathatja a csoporttagsági korlát emelését. Javasoljuk, hogy először valamivel magasabb értékre növelje a tagsági korlátot, például: 75 000 tag, hogy a nagyobb csoportok szinkronizálva legyenek az Azure AD-be. Ha elégedett az eredményekkel, tovább emelheti a tagkorlátot.  

A maximális korlát csoportonként 250 000 tag. 

A következő lépésekkel növelheti a tagsági korlátot:  

1. Az Azure AD Szinkronizálási szabályok szerkesztőjének megnyitása 
2. A szerkesztőben az **Irány beállításhoz válassza** a Kimenő lehetőséget 
3. Kattintson a **Ki az AAD-be – Csoporthoz való csatlakozás szinkronizálási** szabályra 
4. Kattintson a **Szerkesztés gombra** Képernyőkép a "Szinkronizálási szabályok megtekintése és kezelése" gombra a "Ki az ![ AAD-be – Csoporthoz való csatlakozás" beállítással.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Kattintson az **Igen gombra** az alapértelmezett szabály letiltásához és egy szerkeszthető másolat létrehozásához.
 ![Képernyőkép a "Fenntartott szabály megerősítése szerkesztése" ablakról, az "Igen" gomb kijelölve.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. A Leírás lap előugró ablakában állítsa az elsőbbségi értéket egy 1 és 99 közötti értékre. Képernyőkép a "Kimenő szinkronizálási szabály szerkesztése" ablakról kiemelt  ![ "Elsőbbség" beállítással.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Az **Átalakítások lapon** frissítse  a tagátalakítás Forrás értékét úgy, hogy az 50000 értéket 50001 és 250000 közötti értékre cseréli.  Ez a csere megnöveli az Azure AD-be szinkronizálni kívánt csoportok maximális tagsági méretét. Javasoljuk, hogy 100 000-ről indulva megértse, milyen hatással lesz a nagy csoportok szinkronizálása a szinkronizálási teljesítményre. 
 
 **Példa** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Szinkronizálási szabály szerkesztése](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Kattintson a Save (Mentés) gombra. 
10. Rendszergazdai PowerShell-parancssor megnyitása 
11. A szinkronizálásütemező újra engedélyezése 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Ha Azure AD Connect Health nincs engedélyezve, módosítsa a Windows-alkalmazás eseménynapló-beállításait a naplók archiválásához ahelyett, hogy felülírja őket. A naplók felhasználhatók a későbbi hibaelhárítási erőfeszítésekhez. 

>[!NOTE]
> Az új végpont engedélyezése után további exportálási hibákat láthat az AAD-összekötőn "dn-attributes-failure" néven. Minden 6949-es azonosítójú hibához egy megfelelő eseménynapló-bejegyzés lesz. A hibák tájékoztatóak, és nem jeleznek problémát a telepítéssel kapcsolatban, hanem azt jelzik, hogy a szinkronizálási folyamat nem tudott hozzáadni bizonyos tagokat az Azure AD-csoportokhoz, mert maga a tagobjektum nem lett szinkronizálva az Azure AD-be. 

Az új V2-végpontkód némileg eltérő exportálási hibákat kezel, mint a V1-kód.  A V2 végpont használata esetén több tájékoztató hibaüzenet is használhatja. 

>[!NOTE]
> A Azure AD Connect során győződjön meg arról, hogy a 2. fázis lépései újrafutnak, mivel a módosítások nem maradnak meg a frissítési folyamat során. 

Az Out **to AAD – Group Join** sync szabály csoporttag-korlátra való későbbi növekedése során nincs szükség teljes szinkronizálásra, ezért a teljes szinkronizálás letiltható a következő parancs PowerShellben való futtatásával. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Ha több Microsoft 365 mint 50 000 taggal rendelkező egyesített csoportokkal is van, a rendszer beolvassa a csoportokat az Azure AD Connect-ba, és ha a csoportvisszaírás engedélyezve van, a rendszer a helyszíni AD-be írja őket. 

## <a name="rollback"></a>Visszaállítás 
Ha engedélyezte a v2-végpontot, és visszaállításra van szüksége, kövesse az alábbi lépéseket: 

1. A Azure AD Connect kiszolgálón: a. [Nem kötelező] Adatbázis biztonsági mentésének készítése 
2. Nyisson meg egy rendszergazdai PowerShell-parancssort:
3. Tiltsa le a szinkronizálásütemezőt annak ellenőrzése után, hogy nem futnak-e szinkronizálási műveletek
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Váltson a V1-végpontra * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Az Azure AD Szinkronizálási szabályok szerkesztőjének megnyitása 
6. Törölje a Ki az AAD-be – Csoporthoz való csatlakozás **szinkronizálási szabály szerkeszthető** példányát 
7. A Ki- és AAD-csoporthoz való csatlakozás szinkronizálási szabály **alapértelmezett másolatának** engedélyezése 
8. Rendszergazdai PowerShell-parancssor megnyitása 
9. A szinkronizálásütemező újra engedélyezése 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> A V2-végpontról a V1-végpontokra való visszaváltáskor az 50 000-esnél több taggal szinkronizált csoportok a teljes szinkronizálás futtatása után törlődnek mind az Azure AD-be kiépített AD-csoportok, mind pedig az A Microsoft 365 D-be kiépített egységes csoportok esetében. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések  
 
**Mikor lesz az új végpont a frissítések és az új telepítések alapértelmezett beállítása?**  
</br>Azt tervezzük, hogy az AADConnect egy új kiadása lesz közzétéve letöltésre 2021 februárjában. Ez a kiadás alapértelmezés szerint a V2 végpontot használja, és további konfiguráció nélkül engedélyezi az 50 000-esnél nagyobb csoportok szinkronizálását. Ezt a kiadást később közzé fogjuk tenni a jogosult kiszolgálók automatikus frissítéséhez.
 
## <a name="next-steps"></a>Következő lépések

* [Azure AD Connect szinkronizálás: A szinkronizálás érthető és testre szabható](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
