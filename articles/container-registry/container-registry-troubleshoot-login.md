---
title: A beállításjegyzékbe való bejelentkezés hibáinak megoldása
description: Az Azure Container registrybe való bejelentkezés során felmerülő gyakori problémák tünetei, okai és megoldása
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5deb1717cf3886d8ea9c021d92afa358946b16dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052078"
---
# <a name="troubleshoot-registry-login"></a>Beállításjegyzékbeli bejelentkezés – problémamegoldás

Ez a cikk segítséget nyújt az Azure Container registrybe való bejelentkezés során felmerülő problémák elhárításában. 

## <a name="symptoms"></a>Hibajelenségek

A következők közül egyet vagy többet is tartalmazhat:

* Nem lehet bejelentkezni a beállításjegyzékbe a `docker login` , `az acr login` vagy mindkettő használatával
* Nem lehet bejelentkezni a beállításjegyzékbe, és hibaüzenetet kap, `unauthorized: authentication required` vagy `unauthorized: Application not registered with AAD`
* Nem lehet bejelentkezni a beállításjegyzékbe, és Azure CLI-hibaüzenetet kap `Could not connect to the registry login server`
* Nem lehet leküldeni vagy lekérni a képeket, és Docker-hibát kap `unauthorized: authentication required`
* Nem érhető el a beállításjegyzék az Azure Kubernetes Service, az Azure DevOps vagy egy másik Azure-szolgáltatásból
* Nem lehet hozzáférni a beállításjegyzékhez, és hibaüzenetet kap `Error response from daemon: login attempt failed with status: 403 Forbidden` – lásd: [hálózati problémák elhárítása a beállításjegyzékben](container-registry-troubleshoot-access.md)
* Nem lehet hozzáférni vagy megtekinteni a beállításjegyzék beállításait Azure Portal vagy a beállításjegyzéket az Azure CLI használatával felügyelni

## <a name="causes"></a>Okok

* A Docker nincs megfelelően konfigurálva a környezetben – [megoldás](#check-docker-configuration)
* A beállításjegyzék nem létezik, vagy a név helytelen – [megoldás](#specify-correct-registry-name)
* A beállításjegyzékbeli hitelesítő adatok nem érvényesek – [megoldás](#confirm-credentials-to-access-registry)
* A hitelesítő adatok nem engedélyezettek leküldéses, lekéréses vagy Azure Resource Manager műveletekhez – [megoldás](#confirm-credentials-are-authorized-to-access-registry)
* A hitelesítő adatok lejártak – [megoldás](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>További diagnosztika 

Futtassa az az [ACR renézz-Health](/cli/azure/acr#az-acr-check-health) parancsot, hogy további információkat kapjon a beállításjegyzék-környezet állapotáról, és igény szerint hozzáférhessen a cél beállításjegyzékhez. Például diagnosztizálhatja a Docker konfigurációs hibáit, vagy Azure Active Directory a bejelentkezési problémákat. 

A példákat az [Azure Container Registry állapotának ellenőrzését](container-registry-check-health.md) ismertető szakaszban találja. Ha a rendszer hibákat jelez, tekintse át a hibákra vonatkozó [referenciát](container-registry-health-error-reference.md) és a javasolt megoldásokat a következő fejezetekben.

Ha problémákat tapasztal az Azure Kubernetes szolgáltatás beállításjegyzék-wih, futtassa az az [AK-ellenőrzés – ACR](/cli/azure/aks#az_aks_check_acr) parancsot annak ellenőrzéséhez, hogy a beállításjegyzék elérhető-e az AK-fürtről.

> [!NOTE]
> Néhány hitelesítési vagy engedélyezési hiba akkor is előfordulhat, ha olyan tűzfal vagy hálózati konfiguráció van, amely megakadályozza a beállításjegyzék elérését. Lásd: [a beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Lehetséges megoldások

### <a name="check-docker-configuration"></a>Docker-konfiguráció keresése

A legtöbb Azure Container Registry hitelesítési folyamatnál helyi Docker-telepítésre van szükség, hogy a beállításjegyzékben olyan műveleteket végezzen, mint például a képek leküldése és a húzása. Győződjön meg arról, hogy a Docker CLI-ügyfél és démon (Docker-motor) fut a környezetben. A Docker-ügyfél 18,03-es vagy újabb verziójára lesz szüksége.

Kapcsolódó hivatkozások:

* [A hitelesítés áttekintése](container-registry-authentication.md#authentication-options)
* [Container Registry – gyakori kérdések](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Adja meg a beállításjegyzék helyes nevét

A használatakor adja meg a `docker login` beállításjegyzék teljes bejelentkezési kiszolgálójának nevét, például *myregistry.azurecr.IO*. Ügyeljen arra, hogy csak kisbetűket használjon. Példa:

```console
docker login myregistry.azurecr.io
```

Ha az [az ACR logint](/cli/azure/acr#az-acr-login) Azure Active Directory identitással használja, először [Jelentkezzen be az Azure CLI-be](/cli/azure/authenticate-azure-cli), majd adja meg a beállításjegyzék Azure-erőforrásának nevét. Az erőforrás neve a beállításjegyzék létrehozásakor megadott név, például *myregistry* (tartományi utótag nélkül). Példa:

```azurecli
az acr login --name myregistry
```

Kapcsolódó hivatkozások:

* [az ACR login sikeres, de a Docker sikertelen a következő hibával: jogosulatlan: hitelesítés szükséges](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Hitelesítő adatok megerősítése a beállításjegyzék eléréséhez

Tekintse át a forgatókönyvhöz használt hitelesítő adatok érvényességét, vagy egy beállításjegyzék tulajdonosa adta meg. Néhány lehetséges probléma:

* Ha Active Directory egyszerű szolgáltatást használ, ügyeljen arra, hogy a Active Directory bérlőben a megfelelő hitelesítő adatokat használja:
  * Felhasználónév – egyszerű szolgáltatásnév alkalmazásának azonosítója (más néven *ügyfél-azonosító*)
  * Jelszó – egyszerű szolgáltatás jelszava (más néven *ügyfél-titok*)
* Ha egy Azure-szolgáltatást, például az Azure Kubernetes Service-t vagy az Azure DevOps-t használja a beállításjegyzék eléréséhez, erősítse meg a szolgáltatás beállításjegyzék-konfigurációját. 
* Ha `az acr login` a kapcsolóval futtatta a `--expose-token` beállításjegyzék-bejelentkezést a Docker-démon használata nélkül, győződjön meg arról, hogy a felhasználónévvel van hitelesítve `00000000-0000-0000-0000-000000000000` .
* Ha a beállításjegyzék [Névtelen lekéréses hozzáférésre](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)van konfigurálva, akkor az előző Docker-bejelentkezésből tárolt meglévő Docker-hitelesítő adatok megakadályozhatják a névtelen hozzáférést. Futtassa `docker logout` a parancsot a névtelen lekérési művelet a beállításjegyzékben való megkísérlése előtt.

Kapcsolódó hivatkozások:

* [A hitelesítés áttekintése](container-registry-authentication.md#authentication-options)
* [Egyéni bejelentkezés az Azure AD-vel](container-registry-authentication.md#individual-login-with-azure-ad)
* [Bejelentkezés az egyszerű szolgáltatással](container-registry-auth-service-principal.md)
* [Bejelentkezés felügyelt identitással](container-registry-authentication-managed-identity.md)
* [Bejelentkezés adattár-hatókörű jogkivonattal](container-registry-repository-scoped-permissions.md)
* [Bejelentkezés rendszergazdai fiókkal](container-registry-authentication.md#admin-account)
* [Azure AD-hitelesítési és-engedélyezési hibakódok](../active-directory/develop/reference-aadsts-error-codes.md)
* [az ACR login](/cli/azure/acr#az-acr-login) Reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Hitelesítő adatok megerősítése jogosult a beállításjegyzék elérésére

Erősítse meg a hitelesítő adatokhoz társított beállításjegyzék-engedélyeket, például az `AcrPull` Azure-szerepkört a lemezképek a beállításjegyzékből való lekéréséhez, vagy a `AcrPush` lemezképek leküldési szerepköréhez. 

Az Azure CLI-vel a portálon vagy a beállításjegyzék-kezelésben lévő beállításjegyzékhez való hozzáféréshez legalább a `Reader` szerepkör vagy az azzal egyenértékű engedélyek szükségesek Azure Resource Manager műveletek végrehajtásához.

Ha az engedélyei nemrég változnak, hogy lehetővé tegyék a beállításjegyzék hozzáférését a portálon, előfordulhat, hogy egy inkognitóban vagy privát munkamenetet kell kipróbálnia a böngészőben, hogy elkerülje az elavult böngésző gyorsítótárának vagy cookie-jait.

A szerepkör-hozzárendelések hozzáadásához vagy eltávolításához az előfizetés megfelelő jogosultságokkal kell rendelkeznie a beállításjegyzékben.

Kapcsolódó hivatkozások:

* [Azure-szerepkörök és-engedélyek – Azure Container Registry](container-registry-roles.md)
* [Bejelentkezés adattár-hatókörű jogkivonattal](container-registry-repository-scoped-permissions.md)
* [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
* [Az erőforrásokhoz hozzáférő Azure AD-alkalmazás és -szolgáltatásnév létrehozása a portálon](../active-directory/develop/howto-create-service-principal-portal.md)
* [Új titkos alkalmazáskulcs létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD-hitelesítés és-engedélyezési kódok](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Győződjön meg arról, hogy a hitelesítő adatok nem jártak le

A tokenek és a Active Directory hitelesítő adatok meghatározott időszakok után lejárnak, ami megakadályozza a beállításjegyzék elérését. A hozzáférés engedélyezéséhez szükség lehet a hitelesítő adatok alaphelyzetbe állítására vagy újbóli létrehozására.

* Ha egyéni AD-identitást, felügyelt identitást vagy szolgáltatásnevet használ a beállításjegyzék-bejelentkezéshez, az AD-jogkivonat 3 óra elteltével lejár. Jelentkezzen be újra a beállításjegyzékbe.  
* Ha egy, a lejárt ügyfél-titkos kulccsal rendelkező AD-szolgáltatást használ, az előfizetés tulajdonosának vagy a fiók rendszergazdájának alaphelyzetbe kell állítania a hitelesítő adatokat, vagy létre kell hoznia egy új szolgáltatásnevet.
* Ha [adattárat tartalmazó hatókörrel rendelkező tokent](container-registry-repository-scoped-permissions.md)használ, előfordulhat, hogy egy beállításjegyzék-tulajdonosnak új jelszót kell visszaállítania, vagy újat kell létrehoznia.

Kapcsolódó hivatkozások:

* [Egyszerű szolgáltatásnév hitelesítő adatainak alaphelyzetbe állítása](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Jogkivonat-jelszavak újbóli előállítása](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Egyéni bejelentkezés az Azure AD-vel](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

Ha a beállításjegyzékben engedélyezve van az [erőforrás-naplók gyűjtése](container-registry-diagnostics-audit-logs.md) , tekintse át a ContainterRegistryLoginEvents-naplót. Ez a napló a hitelesítési eseményeket és az állapotot tárolja, beleértve a bejövő identitást és az IP-címet is. A [beállításjegyzék-hitelesítési hibák](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)naplójának lekérdezése. 

Kapcsolódó hivatkozások:

* [Naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)
* [Container Registry – gyakori kérdések](container-registry-faq.md)
* [Az Azure Container Registry ajánlott eljárásai](container-registry-best-practices.md)

## <a name="next-steps"></a>Következő lépések

Ha itt nem oldja meg a problémát, tekintse meg a következő beállításokat.

* A beállításjegyzék egyéb hibaelhárítási témakörei a következők:
  * [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
  * [Beállításjegyzékbeli teljesítmény – problémamegoldás](container-registry-troubleshoot-performance.md)
* [Közösségi támogatási](https://azure.microsoft.com/support/community/) lehetőségek
* [Microsoft Q&A](/answers/products/)
* [Támogatási jegy megnyitása](https://azure.microsoft.com/support/create-ticket/) – az Ön által megadott információk alapján egy gyors diagnosztika is futtatható a beállításjegyzékben található hitelesítési hibák esetén.