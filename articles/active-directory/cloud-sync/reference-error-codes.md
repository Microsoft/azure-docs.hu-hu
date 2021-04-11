---
title: Azure AD Connect Cloud Sync-hibakódok és-leírások
description: a Cloud Sync-hibakódokra vonatkozó hivatkozási cikk
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0aeb2e9649a4bbbb2520eac683836ebbf7dfd0a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075049"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect Cloud Sync-hibakódok és-leírások
A következő lista a hibakódokat és azok leírását tartalmazza


## <a name="error-codes"></a>Hibakódok

|Hibakód|Részletek|Eset|Feloldás|
|-----|-----|-----|-----|
|Időtúllépés|Hibaüzenet: a rendszer időtúllépési hibát észlelt, amikor felvette a kapcsolatot a helyszíni ügynökkel, és szinkronizálja a konfigurációt. A felhőalapú szinkronizálási ügynökkel kapcsolatos további problémákról tekintse meg a hibaelhárítási útmutatót.|Időtúllépési kérelem. Az aktuális időtúllépési érték 10 perc.|Tekintse meg a [hibaelhárítási útmutatót](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|Hibaüzenet: ezen a ponton nem sikerült feldolgozni a kérelmet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg a következő feladathoz tartozó azonosítót: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. További részletek: a HTTP-kérelem feldolgozása kivételt okozott. |Nem lehet feldolgozni a SCIM kérelemben fogadott paramétereket egy keresési kérelemre.|A részletekért tekintse meg a jelen kivétel "Response" tulajdonsága által visszaadott HTTP-választ.|
|HybridIdentityServiceNoAgentsAssigned|Hibaüzenet: nem található aktív ügynök a szinkronizálni próbált tartományhoz. Ellenőrizze, hogy az ügynökök el lettek-e távolítva. Ha igen, telepítse újra az ügynököt.|Nincsenek futó ügynökök. Az ügynökök valószínűleg el lettek távolítva. Regisztráljon egy új ügynököt.|"Ebben az esetben a portálon nem jelenik meg a tartományhoz rendelt ügynök.|
|HybridIdentityServiceNoActiveAgents|Hibaüzenet: nem található aktív ügynök a szinkronizálni próbált tartományhoz. Ellenőrizze, hogy fut-e az ügynök a kiszolgálón, ahol az ügynök telepítve van, és ellenőrizze, hogy fut-e a "Microsoft Azure AD Cloud Sync Agent" elem a szolgáltatások alatt.|"Az ügynökök nem figyelik az ServiceBus-végpontot. [Az ügynök olyan tűzfal mögött van, amely nem engedélyezi a Service Bus-kapcsolat használatát](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Hibaüzenet: ezen a ponton nem sikerült feldolgozni a kérelmet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg a következő feladathoz tartozó azonosítót: AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. További részletek: úgy tűnik, hogy probléma van a felhőalapú szinkronizálás beállításával. Regisztrálja újra a felhőalapú szinkronizálási ügynököt a helyszíni AD-tartományon, és indítsa újra a konfigurációt az Azure Portalon.|Az erőforrás nevét be kell állítani, hogy tudja, melyik ügynöknek kell kapcsolódnia.|Regisztrálja újra a felhőalapú szinkronizálási ügynököt a helyszíni AD-tartományon, és indítsa újra a konfigurációt az Azure Portalon.|
|HybridIdentityServiceAgentSignalingError|Hibaüzenet: ezen a ponton nem sikerült feldolgozni a kérelmet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg a következő feladathoz tartozó azonosítót: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-b495-18d5272eb3f9. További részletek: ezen a ponton nem sikerült feldolgozni ezt a kérelmet. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz (a konfiguráció állapot ablaktábláján).|A Service Bus nem tud üzenetet küldeni az ügynöknek. Lehet leállás a Service Bus szolgáltatásban, vagy az ügynök nem válaszol.|Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz (a konfiguráció állapot ablaktábláján).|
|AzureDirectoryServiceServerBusy|Hibaüzenet: hiba történt. Hibakód: 81. Hiba leírása: a Azure Active Directory jelenleg foglalt. A rendszer automatikusan újrapróbálkozik a művelettel. Ha a probléma 24 óránál hosszabb ideig nem szűnik meg, forduljon a technikai támogatási szolgálathoz. Követési azonosító: 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f-kiszolgáló neve:|Azure Active Directory jelenleg foglalt.|Ha a probléma 24 óránál hosszabb ideig nem szűnik meg, forduljon a technikai támogatási szolgálathoz.|
|AzureActiveDirectoryInvalidCredential|Hibaüzenet: a Azure AD Connect Cloud Sync futtatásához használt szolgáltatásfiók hibát észlelt. A Cloud Service-fiók javításához kövesse az [itt](./how-to-troubleshoot.md)található utasításokat. Ha a hiba továbbra is fennáll, forduljon a támogatási szolgálathoz (a konfiguráció állapot ablaktábláján). További hiba részletei: CredentialsInvalid AADSTS50034: a (z) {EmailHidden} felhasználói fiók nem létezik a skydrive365.onmicrosoft.com könyvtárban. Az alkalmazásba való bejelentkezéshez a fiókot hozzá kell adni a címtárhoz. Nyomkövetési azonosító: 14b63033-3bc9-4BD4-b871-5eb4b3500200 korrelációs azonosítója: 57d93ed1-be4d-483c-997c-a3b6f03deb00 timestamp: 2021-01-12 21:08:29Z |Ez a hiba akkor fordul elő, ha a szinkronizálási szolgáltatásfiók ADToAADSyncServiceAccount nem létezik a bérlőben. Ennek oka lehet a fiók véletlen törlése.|A Service fiók kijavításához használja a [Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) .|
|AzureActiveDirectoryExpiredCredentials|Hibaüzenet: ezen a ponton nem sikerült feldolgozni a kérelmet. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz (a konfiguráció állapot ablaktábláján). További hiba részletei: CredentialsExpired AADSTS50055: a jelszó lejárt. Nyomkövetési azonosító: 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 korrelációs azonosítója: 1c69b196-1c3a-4381-9187-c84747807155 timestamp: 2021-01-12 20:59:31Z | A válasz állapotkód nem jelzi a sikerességet: 401 (nem engedélyezett).<br> AAD-szinkronizáló szolgáltatásfiók hitelesítő adatai lejártak.|A Cloud Service-fiók kijavításához kövesse a következő témakör utasításait: https://go.microsoft.com/fwlink/?linkid=2150988 . Ha a hiba továbbra is fennáll, forduljon a támogatási szolgálathoz (a konfiguráció állapot ablaktábláján).  További hiba részletei: a felügyeleti Azure Active Directory bérlői hitelesítő adatai egy lejárt OAuth-jogkivonat esetében lettek kicserélve. "|
|AzureActiveDirectoryAuthenticationFailed|Hibaüzenet: ezen a ponton nem sikerült feldolgozni a kérelmet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg a következő feladathoz tartozó azonosítót: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac. További részletek: ezen a ponton nem sikerült feldolgozni ezt a kérelmet. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz (a konfiguráció állapot ablaktábláján). További hiba részletei: UnexpectedError.|Ismeretlen hiba.|Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz (a konfiguráció állapot ablaktábláján).|

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
