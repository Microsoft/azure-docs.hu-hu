---
title: Azure AD Connect Cloud Sync – részletes útmutató – működés
description: Ez a témakör részletes információkat tartalmaz a felhőalapú szinkronizálás működéséről.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613841"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Cloud Sync – részletes útmutató – működés

## <a name="overview-of-components"></a>Az összetevők áttekintése

![Működés](media/concept-how-it-works/how-1.png)

A Felhőbeli szinkronizálás az Azure AD-szolgáltatásokra épül, és két kulcsfontosságú összetevővel rendelkezik:

- **Kiépítési ügynök**: a Azure ad Connect Cloud kiépítési ügynök ugyanaz az ügynök, mint a munkanapokhoz tartozó bejövő, és ugyanarra a kiszolgálóoldali technológiára épül, mint az alkalmazás-proxy, és továbbítja a hitelesítést. Csak a szükséges és a kimenő kapcsolatok, az ügynökök pedig automatikusan frissülnek. 
- **Kiépítési szolgáltatás**: ugyanaz a kiépítési szolgáltatás, mint a kimenő kiépítés és a munkanap bejövő kiépítés, amely Scheduler-alapú modellt használ. Felhőalapú szinkronizálás esetén a módosítások 2 percenként vannak kiépítve.


## <a name="initial-setup"></a>Kezdeti beállítás
A kezdeti beállítás során néhány dolog történik, amely lehetővé teszi a felhőalapú szinkronizálást.  Ezek a következők: 

- Az **ügynök telepítése során**: konfigurálja az ügynököt azokhoz az ad-tartományokhoz, amelyekről szeretne kiépíteni.  Ez a konfiguráció regisztrálja a tartományokat a hibrid identitás szolgáltatásban, és kimenő kapcsolatot létesít a Service Bus által a kérések figyeléséhez.
- A **kiépítés engedélyezésekor**: kiválaszthatja az ad-tartományt, és engedélyezheti a 2 percenként futtatott kiépítés engedélyezését. Opcionálisan kiválaszthatja a jelszó-kivonat szinkronizálását, és megadhatja az értesítő e-mailt. Microsoft Graph API-kkal is kezelheti az attribútumok átalakítását.


## <a name="agent-installation"></a>Ügynök telepítése
Az alábbiakban áttekintheti, hogy mi történik a felhőalapú kiépítési ügynök telepítésekor.

- Először a telepítő telepíti az ügynök bináris fájljait és az ügynök szolgáltatást, amely a virtuális szolgáltatásfiók (hálózati SERVICE\AADProvisioningAgent) alatt fut.  A virtuális szolgáltatásfiók olyan speciális fióktípus, amely nem rendelkezik jelszóval, és a Windows felügyeli.
- A telepítő ezután elindítja a varázslót.
- A varázsló kérni fogja az Azure AD hitelesítő adatait, majd hitelesíti és beolvassa a tokent.
- A varázsló ezután megkéri a számítógép aktuális tartományának rendszergazdai hitelesítő adatait.
- Ezekkel a hitelesítő adatokkal a tartományhoz tartozó általános felügyelt szolgáltatásfiók (GMSA) hozható létre vagy található, és a rendszer újra felhasználja, ha már létezik.
- Az ügynök szolgáltatás most már úgy lett konfigurálva, hogy a GMSA alatt fusson.
- A varázsló most kéri a tartományi konfigurációt, valamint az összes olyan tartományhoz tartozó vállalati rendszergazda (EA)/domain-rendszergazdai (DA) fiókot, amelyet az ügynöknek szeretne kiszolgálni.
- A rendszer ezután frissíti a GMSA-fiókot a fent megadott tartományokhoz való hozzáférés engedélyezéséhez szükséges engedélyekkel.
- Ezután a varázsló elindítja az ügynök regisztrációját
- Az ügynök létrehoz egy tanúsítványt, és az Azure AD-jogkivonatot használja, regisztrálja magát és a tanúsítványt a Hybrid Identity Service (a) regisztrációs szolgáltatásával.
- A varázsló egy AgentResourceGrouping hívást indít el. A rendszergazdai szolgáltatásának meghívásával az ügynököt hozzárendelheti egy vagy több AD-tartományhoz a saját konfigurációjában.
- A varázsló most újraindítja az ügynök szolgáltatást.
- Az ügynök meghívja a rendszerindítási szolgáltatást újraindítás után (és 10 percenként) a konfigurációs frissítések kereséséhez.  A bootstrap szolgáltatás érvényesíti az ügynök identitását.  Az utolsó rendszerindítási időt is frissíti.  Ez azért fontos, mert ha az ügynökök nem indulnak el, nem frissülnek Service Bus végpontok, és előfordulhat, hogy nem tudnak fogadni a kérelmeket. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Mi a rendszer a tartományok közötti Identitáskezelés kezelésére (SCIM)?

A [scim-specifikáció](https://tools.ietf.org/html/draft-scim-core-schema-01) egy olyan szabvány, amely automatizálja a felhasználó vagy a csoport identitási adatainak cseréjét az identitás-tartományok, például az Azure ad között. A SCIM a kiépítés de facto szabványa, és az összevonási szabványok, például az SAML vagy az OpenID Connect együttes használata esetén a rendszergazdák teljes körű, a hozzáférés-vezérlésre vonatkozó szabványokon alapuló megoldást biztosítanak.

A Azure AD Connect Cloud kiépítési ügynök SCIM használ az Azure AD-vel a felhasználók és csoportok kiépítéséhez és megszüntetéséhez.

## <a name="synchronization-flow"></a>Szinkronizációs folyamat
![kiépítés ](media/concept-how-it-works/provisioning-4.png) az ügynök telepítése és az engedélyezett kiépítés után a következő folyamat következik be.

1.  A konfigurálást követően az Azure AD-kiépítési szolgáltatás meghívja az Azure AD Hybrid Service-t, hogy adjon hozzá egy kérést a Service Bus számára. Az ügynök állandóan kimenő kapcsolatot tart fenn a kérelmeket figyelő Service Bus figyelésével, és felveszi a rendszert a tartományok közötti Identitáskezelés (SCIM) kérelmére. 
2.  Az ügynök az Objektumtípus alapján különálló lekérdezésekre bontja a kérést. 
3.  Az AD adja vissza az eredményt az ügynöknek, és az ügynök szűri ezeket az adatokat, mielőtt elküldené az Azure AD-nek.  
4.  Az ügynök visszaadja az SCIM választ az Azure AD-nek.  Ezek a válaszok az ügynökön belül történt szűrésen alapulnak.  Az ügynök a hatókört használja az eredmények szűréséhez. 
5.  A kiépítési szolgáltatás a módosításokat az Azure AD-be írja.
6. Ha ez egy különbözeti szinkronizálás, amely nem teljes szinkronizálást használ, a rendszer a cookie-t/vízjelet használja. Az új lekérdezések a cookie/vízjeltől kezdve változnak.

## <a name="supported-scenarios"></a>Támogatott forgatókönyvek:
A Cloud Sync a következő forgatókönyveket támogatja.


- **Meglévő hibrid ügyfél új erdővel**: az elsődleges erdők Azure ad Connect szinkronizálást használnak. A Felhőbeli szinkronizálást az AD-erdőből való kiépítés (beleértve a leválasztott) használatával végezheti el. További információ [az oktatóanyagban](tutorial-existing-forest.md)található.

 ![Meglévő hibrid](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Új hibrid ügyfél**: Azure ad Connect szinkronizálás nincs használatban. A Felhőbeli szinkronizálás az AD-erdőből való kiépítés során használatos.  További információ [az oktatóanyagban](tutorial-single-forest.md)található.
 
 ![Új ügyfelek](media/tutorial-single-forest/diagram-2.png)

- **Meglévő hibrid ügyfél**: az elsődleges erdők Azure ad Connect szinkronizálást használnak. A Felhőbeli szinkronizálást az [elsődleges erdőkben](tutorial-existing-forest.md)lévő felhasználók egy kis halmaza teszteli.

 ![Meglévő pilóta](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

További információ: [támogatott topológiák](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
