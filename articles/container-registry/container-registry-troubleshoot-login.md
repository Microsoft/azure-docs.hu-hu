---
title: A beállításjegyzékbe való bejelentkezés hibaelhárítása
description: Az Azure Container Registrybe való bejelentkezéskor gyakran jelentkező problémák tünetei, okai és megoldása
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 47186cc8256836e5367ecee520787b67662eb42f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780730"
---
# <a name="troubleshoot-registry-login"></a>Beállításjegyzékbeli bejelentkezés hibaelhárítása

Ez a cikk segítséget nyújt az Azure Container Registrybe való bejelentkezéskor felmerülő problémák elhárításában. 

## <a name="symptoms"></a>Hibajelenségek

A következők közül egyet vagy többet tartalmazhat:

* Nem lehet bejelentkezni a beállításjegyzékbe a `docker login` , `az acr login` vagy mindkettő használatával
* Nem lehet bejelentkezni a beállításjegyzékbe, és a következő hibaüzenet jelenik `unauthorized: authentication required` meg: vagy `unauthorized: Application not registered with AAD`
* Nem lehet bejelentkezni a beállításjegyzékbe, és Azure CLI-hibaüzenetet kap `Could not connect to the registry login server`
* A rendszerképek leküldése vagy leküldése nem sikerült, és Docker-hibaüzenetet kap `unauthorized: authentication required`
* Nem lehet hozzáférni a regisztrációs adatbázishoz Azure Kubernetes Service, az Azure DevOpsból vagy más Azure-szolgáltatásból
* Nem lehet hozzáférni a beállításjegyzékhez, és hibaüzenetet kap `Error response from daemon: login attempt failed with status: 403 Forbidden` – Lásd: [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
* Nem lehet elérni vagy megtekinteni a beállításjegyzék-beállításokat Azure Portal a regisztrációs adatbázist az Azure CLI használatával

## <a name="causes"></a>Okok

* A Docker nincs megfelelően konfigurálva a környezetben – [megoldás](#check-docker-configuration)
* A beállításjegyzék nem létezik, vagy a név helytelen – [megoldás](#specify-correct-registry-name)
* A beállításjegyzék hitelesítő adatai nem érvényesek – [megoldás](#confirm-credentials-to-access-registry)
* A hitelesítő adatok nem engedélyezettek leküldéses, leküldéses vagy Azure Resource Manager műveletekhez – [megoldás](#confirm-credentials-are-authorized-to-access-registry)
* A hitelesítő adatok lejártak – [megoldás](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>További diagnosztika 

Futtassa [az az acr check-health](/cli/azure/acr#az_acr_check_health) parancsot a beállításjegyzék-környezet állapotáról és opcionálisan a cél beállításjegyzékhez való hozzáférésről. Diagnosztizálhatja például a Docker konfigurációs hibáit, vagy Azure Active Directory bejelentkezési problémákat. 

A [parancsokkal kapcsolatos példákért](container-registry-check-health.md) tekintse meg az Azure Container Registry állapotának ellenőrzése. Ha a rendszer hibákat jelent, tekintse át a [hibareferenciát](container-registry-health-error-reference.md) és az alábbi szakaszokat az ajánlott megoldásokért.

Ha problémákat tapasztal a beállításjegyzék Azure Kubernetes Service, futtassa [az az aks check-acr](/cli/azure/aks#az_aks_check_acr) parancsot annak ellenőrzéséhez, hogy a beállításjegyzék elérhető-e az AKS-fürtből.

> [!NOTE]
> Bizonyos hitelesítési vagy engedélyezési hibák akkor is előfordulhatnak, ha tűzfal- vagy hálózati konfigurációk akadályozzák a beállításjegyzékhez való hozzáférést. Lásd: [A beállításjegyzék hálózati problémáinak elhárítása.](container-registry-troubleshoot-access.md)

## <a name="potential-solutions"></a>Lehetséges megoldások

### <a name="check-docker-configuration"></a>A Docker konfigurációjának ellenőrzése

A Azure Container Registry hitelesítési folyamatokhoz helyi Docker-telepítés szükséges, hogy hitelesítést tud végezni a beállításjegyzékben az olyan műveletekhez, mint a rendszerképek lekért vagy lekért használata. Ellenőrizze, hogy a Docker CLI-ügyfél és -démon (Docker Engine) fut-e a környezetben. A Docker-ügyfél 18.03-as vagy újabb verziójára lesz szüksége.

Kapcsolódó hivatkozások:

* [A hitelesítés áttekintése](container-registry-authentication.md#authentication-options)
* [Container Registry – gyakori kérdések](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Helyes beállításjegyzék-név megadása

A használata esetén adja meg a regisztrációs adatbázis teljes bejelentkezési kiszolgálónevét( például `docker login` *myregistry.azurecr.io.* Ügyeljen arra, hogy csak kisbetűket használjon. Példa:

```console
docker login myregistry.azurecr.io
```

Ha az [az acr login-t](/cli/azure/acr#az_acr_login) egy Azure Active Directory-identitással használja, először jelentkezzen be az [Azure CLI-be,](/cli/azure/authenticate-azure-cli)majd adja meg a regisztrációs adatbázis Azure-erőforrásnevét. Az erőforrás neve a beállításjegyzék létrehozásakor megadott név, például *myregistry* (tartomány utótag nélkül). Példa:

```azurecli
az acr login --name myregistry
```

Kapcsolódó hivatkozások:

* [az acr login succeeds but docker fails with error: unauthorized: authentication required](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>Hitelesítő adatok megerősítése a beállításjegyzék eléréséhez

Ellenőrizze a forgatókönyvhöz használt hitelesítő adatok érvényességét, vagy hogy a beállításjegyzék tulajdonosa biztosította-e Önnek. Néhány lehetséges probléma:

* Ha egyszerű Active Directory használ, győződjön meg arról, hogy a megfelelő hitelesítő adatokat használja a Active Directory bérlőben:
  * Felhasználónév – egyszerű szolgáltatás alkalmazásazonosítója (más néven *ügyfél-azonosító)*
  * Jelszó – egyszerű szolgáltatás jelszava (más néven *titkos ügyféljelszó)*
* Ha egy Azure-szolgáltatást, például a Azure Kubernetes Service vagy az Azure DevOpsot használja a regisztrációs adatbázis eléréséhez, ellenőrizze a szolgáltatás beállításjegyzék-konfigurációját. 
* Ha a beállítással futtatta a beállítást, amely lehetővé teszi a regisztrációs adatbázisba való bejelentkezést a Docker-démon használata nélkül, győződjön meg arról, hogy a felhasználónevével `az acr login` `--expose-token` `00000000-0000-0000-0000-000000000000` hitelesít.
* Ha a regisztrációs adatbázis névtelen lekért hozzáférésre van [konfigurálva,](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)a korábbi Docker-bejelentkezésből tárolt meglévő Docker-hitelesítő adatok megakadályozhatják a névtelen hozzáférést. Futtassa `docker logout` a következőt, mielőtt névtelen leküldi a műveletet a beállításjegyzékben.

Kapcsolódó hivatkozások:

* [A hitelesítés áttekintése](container-registry-authentication.md#authentication-options)
* [Egyéni bejelentkezés az Azure AD-val](container-registry-authentication.md#individual-login-with-azure-ad)
* [Bejelentkezés szolgáltatásnévvel](container-registry-auth-service-principal.md)
* [Bejelentkezés felügyelt identitással](container-registry-authentication-managed-identity.md)
* [Bejelentkezés adattárhatókörű jogkivonattal](container-registry-repository-scoped-permissions.md)
* [Bejelentkezés rendszergazdai fiókkal](container-registry-authentication.md#admin-account)
* [Azure AD-hitelesítési és -engedélyezési hibakódok](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az_acr_login) reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Győződjön meg arról, hogy a hitelesítő adatok jogosultak a beállításjegyzék elérésére

Ellenőrizze a hitelesítő adatokhoz társított beállításjegyzékbeli engedélyeket, például az Azure-szerepkört a rendszerképek regisztrációs adatbázisból való leküldéséhez, vagy a rendszerképek `AcrPull` `AcrPush` leküldésére használt szerepkört. 

A regisztrációs adatbázishoz a portálon vagy a beállításjegyzék kezeléséhez az Azure CLI használatával való hozzáféréshez legalább a szerepkör vagy azzal egyenértékű engedély szükséges a Azure Resource Manager `Reader` elvégzéséhez.

Ha az engedélyei nemrég úgy módosultak, hogy a portálon engedélyezze a beállításjegyzékhez való hozzáférést, előfordulhat, hogy inkognitó vagy privát munkamenetet kell kipróbálnia a böngészőben, hogy elkerülje az elavult böngésző-gyorsítótárat vagy cookie-kat.

Önnek vagy egy regisztrációs adatbázis tulajdonosának megfelelő jogosultságokkal kell rendelkezik az előfizetésben a szerepkör-hozzárendelések hozzáadásához vagy eltávolításához.

Kapcsolódó hivatkozások:

* [Azure-szerepkörök és -engedélyek – Azure Container Registry](container-registry-roles.md)
* [Bejelentkezés adattárhatókörű jogkivonattal](container-registry-repository-scoped-permissions.md)
* [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
* [Az erőforrásokhoz hozzáférő Azure AD-alkalmazás és -szolgáltatásnév létrehozása a portálon](../active-directory/develop/howto-create-service-principal-portal.md)
* [Új titkos alkalmazáskulcs létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD-hitelesítési és -engedélyezési kódok](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Ellenőrizze, hogy a hitelesítő adatok nem lejártak-e

A jogkivonatok és Active Directory hitelesítő adatok adott időszakok után lejárhatnak, ami meggátolja a beállításjegyzékhez való hozzáférést. A hozzáférés engedélyezéséhez előfordulhat, hogy a hitelesítő adatokat alaphelyzetbe kell állítani vagy újra létre kell hozni.

* Ha egyéni AD-identitást, felügyelt identitást vagy szolgáltatásneveket használ a regisztrációs adatbázisba való bejelentkezéshez, az AD-jogkivonat 3 óra után lejár. Jelentkezzen be újra a beállításjegyzékbe.  
* Ha lejárt titkos ügyféltitkos AD-szolgáltatásnévvel használ, az előfizetés tulajdonosának vagy a fiók rendszergazdájának alaphelyzetbe kell állítania a hitelesítő adatokat, vagy új egyszerű szolgáltatást kell létrehoznia.
* [Adattárhatókörű](container-registry-repository-scoped-permissions.md)jogkivonat használata esetén előfordulhat, hogy egy beállításjegyzék-tulajdonosnak új jelszót kell visszaállítania, vagy új jogkivonatot kell létrehoznia.

Kapcsolódó hivatkozások:

* [Szolgáltatásnév hitelesítő adatainak alaphelyzetbe állítása](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [Jogkivonatjelszavak újragenerálása](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Egyéni bejelentkezés az Azure AD-val](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

Ha [az erőforrásnaplók gyűjteménye](container-registry-diagnostics-audit-logs.md) engedélyezve van a beállításjegyzékben, tekintse át a ContainterRegistryLoginEvents naplót. Ez a napló tárolja a hitelesítési eseményeket és állapotokat, beleértve a bejövő identitást és IP-címet. A beállításjegyzék-hitelesítési [hibákat a naplóban kell lekérdezni.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Kapcsolódó hivatkozások:

* [Diagnosztikai kiértékelési és naplózási naplók](container-registry-diagnostics-audit-logs.md)
* [Container Registry – gyakori kérdések](container-registry-faq.md)
* [Az Azure Container Registry ajánlott eljárásai](container-registry-best-practices.md)

## <a name="next-steps"></a>Következő lépések

Ha itt nem oldja meg a problémát, tekintse meg az alábbi lehetőségeket.

* A beállításjegyzék egyéb hibaelhárítási témakörei a következők:
  * [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
  * [A beállításjegyzék teljesítményével kapcsolatos problémák elhárítása](container-registry-troubleshoot-performance.md)
* [Közösségi támogatási](https://azure.microsoft.com/support/community/) lehetőségek
* [Microsoft Q&A](/answers/products/)
* [Támogatási jegy megnyitása](https://azure.microsoft.com/support/create-ticket/) – a rendelkezésre adott információk alapján a beállításjegyzékben hitelesítési hibák esetén egy gyors diagnosztikai hiba is lefuthat