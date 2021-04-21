---
title: Hibahivatkozás a beállításjegyzék állapot-ellenőrzéséhez
description: Hibakódok és lehetséges megoldások a problémákra az az acr check-health diagnostic parancs futtatásával a Azure Container Registry
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773440"
---
# <a name="health-check-error-reference"></a>Állapot-ellenőrzés hibareferenciája

Az alábbiakban az [az acr check-health][az-acr-check-health] parancs által visszaadott hibakódokkal kapcsolatos részleteket olvashat. Minden hiba esetén a lehetséges megoldások listája megjelenik.

A futtatásával kapcsolatos `az acr check-healh` információkért lásd: [Azure Container Registry állapotának ellenőrzése.](container-registry-check-health.md)

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Ez a hiba azt jelenti, hogy a CLI-hez való Docker-ügyfél nem található. Ennek eredményeképpen a következő további ellenőrzések nem futnak: a Docker-verzió megkeresása, a Docker-démon állapotának kiértékelása és a Docker lekért parancsának futtatása.

*Lehetséges megoldások:* A Docker-ügyfél telepítése; Adja hozzá a Docker elérési útját a rendszerváltozókhoz.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Ez a hiba azt jelenti, hogy a Docker-démon állapota nem érhető el, vagy nem érhető el a CLI használatával. Ennek eredményeképpen a Docker-műveletek (például a és `docker login` `docker pull` a ) nem érhetők el a CLI-ről.

*Lehetséges megoldások:* Indítsa újra a Docker-démont, vagy ellenőrizze, hogy megfelelően van-e telepítve.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Ez a hiba azt jelenti, hogy a parancssori felület nem tudta futtatni a `docker --version` parancsot.

*Lehetséges megoldások:* Próbálja meg manuálisan lefutni a parancsot, győződjön meg arról, hogy a cli legújabb verzióját futtatja, és vizsgálja meg a hibaüzenetet.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Ez a hiba azt jelenti, hogy a CLI nem tudott mintaképet lekért a környezetbe.

*Lehetséges megoldások:* Ellenőrizze, hogy a rendszerkép lekért összes összetevője megfelelően fut-e.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Ez a hiba azt jelenti, hogy a Cli nem találta meg a Helm-ügyfelet, ami kizárja a többi Helm-műveletet.

*Lehetséges megoldások:* Ellenőrizze, hogy telepítve van-e a Helm-ügyfél, és hogy az elérési útja hozzá lett-e adva a rendszerkörnyezet változóihoz.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Ez a hiba azt jelenti, hogy a CLI nem tudta meghatározni a telepített Helm-verziót. Ez akkor fordulhat elő, ha a használt Azure CLI-verzió (vagy a Helm-verzió) elavult.

*Lehetséges megoldások:* Frissítsen az Azure CLI legújabb verziójára vagy a javasolt Helm-verzióra; futtassa manuálisan a parancsot, és vizsgálja meg a hibaüzenetet.

## <a name="cmk_error"></a>CMK_ERROR

Ez a hiba azt jelenti, hogy a beállításjegyzék nem fér hozzá a felhasználó vagy a sysem által hozzárendelt felügyelt identitáshoz, amely a beállításjegyzék titkosításának ügyfél által felügyelt kulccsal való konfigurálásához használható. Előfordulhat, hogy a felügyelt identitás törölve lett.  

*Lehetséges megoldás:* A probléma megoldásához és a kulcs másik felügyelt identitással való váltogatásához tekintse meg a felhasználó által hozzárendelt identitás [hibaelhárításának lépéseit.](container-registry-customer-managed-keys.md#troubleshoot)

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Ez a hiba azt jelenti, hogy az adott regisztrációs adatbázis bejelentkezési kiszolgálójának DNS-e pingelve lett, de nem válaszolt, ami azt jelenti, hogy nem érhető el. Ez kapcsolódási problémákat jelezhet. Másik lehetőségként előfordulhat, hogy a regisztrációs adatbázis nem létezik, előfordulhat, hogy a felhasználó nem rendelkezik a beállításjegyzékhez szükséges engedélyekkel (a bejelentkezési kiszolgáló megfelelő lekéréséhez), vagy a céljegyzék az Azure CLI-hez használttól eltérő felhőben található.

*Lehetséges megoldások:* A kapcsolat ellenőrzése; ellenőrizze a beállításjegyzék helyesírását, és hogy létezik-e a beállításjegyzék; ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel, és hogy a regisztrációs adatbázis felhőjéhez ugyanaz-e az Azure CLI-hez használt felhő.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ez a hiba azt jelenti, hogy az adott regisztrációs adatbázis feladatvégpontja 403 Tiltott HTTP-állapottal válaszolt. Ez a hiba azt jelenti, hogy a felhasználók nem férnek hozzá a beállításjegyzékhez, valószínűleg egy virtuális hálózati konfiguráció miatt, vagy mert a beállításjegyzék nyilvános végpontjának elérése nem engedélyezett. A jelenleg konfigurált tűzfalszabályokat a futtatásával `az acr show --query networkRuleSet --name <registry>` láthatja.

*Lehetséges megoldások:* Távolítsa el a virtuális hálózati szabályokat, vagy adja hozzá az aktuális ügyfél IP-címét az engedélyezett listához.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Ez a hiba azt jelenti, hogy a céljegyzék feladatvégpontja nem adott ki kihívást.

*Lehetséges megoldások:* Próbálkozzon újra egy idő után. Ha a hiba továbbra is fennáll, nyisson egy problémát a következőnél: https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ez a hiba azt jelenti, hogy a céljegyzék feladatvégpontja egy feladatot adott ki, de a beállításjegyzék nem támogatja a Azure Active Directory hitelesítést.

*Lehetséges megoldások:* Próbálkozzon egy másik hitelesítési módszersel, például rendszergazdai hitelesítő adatokkal. Ha a felhasználóknak hitelesítést kell végeznie a Azure Active Directory, nyisson egy problémát a(nak) https://aka.ms/acr/issues gombra.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ez a hiba azt jelenti, hogy a beállításjegyzék bejelentkezési kiszolgálója nem válaszolt frissítési jogkivonattal, ezért a rendszer megtagadta a hozzáférést a cél beállításjegyzékhez. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, vagy ha az Azure CLI felhasználói hitelesítő adatai elavultak.

*Lehetséges megoldások:* Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben; az `az login` parancs futtatásával frissítheti az engedélyeket, a jogkivonatokat és a hitelesítő adatokat.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ez a hiba azt jelenti, hogy a beállításjegyzék bejelentkezési kiszolgálója nem válaszolt hozzáférési jogkivonattal, így a rendszer megtagadta a hozzáférést a cél beállításjegyzékhez. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, vagy ha az Azure CLI felhasználói hitelesítő adatai elavultak.

*Lehetséges megoldások:* Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben; az `az login` parancs futtatásával frissítheti az engedélyeket, a jogkivonatokat és a hitelesítő adatokat.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Ez a hiba azt jelenti, hogy az ügyfél nem tudott biztonságos kapcsolatot létesíteni a tároló-beállításjegyzékkel. Ez a hiba általában akkor fordul elő, ha proxykiszolgálót futtat vagy használ.

*Lehetséges megoldások:* A proxyk mögötti munkavégzésről itt [talál további információt.](/cli/azure/use-cli-effectively)

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Ez a hiba azt jelenti, hogy a CLI nem találta az adott regisztrációs adatbázis bejelentkezési kiszolgálóját, és nem található alapértelmezett utótag az aktuális felhőhöz. Ez a hiba akkor fordulhat elő, ha a regisztrációs adatbázis nem létezik, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, ha a regisztrációs adatbázis felhőjéhez és az aktuális Azure CLI-felhőhöz nem egyezik, vagy ha az Azure CLI verziója elavult.

*Lehetséges megoldások:* Ellenőrizze, hogy a helyesírás helyes-e, és hogy létezik-e a beállításjegyzék; ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben, és hogy a regisztrációs adatbázis és a CLI-környezet felhői megegyeznek-e; frissítse az Azure CLI-t a legújabb verzióra.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Ez a hiba azt jelenti, hogy a CLI nem kompatibilis a Docker/Notary aktuálisan telepített verziójával. Próbálja meg a notary.exe 0.6.0-snál korábbi verzióra frissíteni a Docker-telepítési Notary-ügyfél manuális cseréjével a probléma megoldásához.

## <a name="next-steps"></a>Következő lépések

A regisztrációs adatbázis állapotának ellenőrzésével kapcsolatos beállításokért lásd: Azure Container [Registry állapotának ellenőrzése.](container-registry-check-health.md)

A gyakori [kérdéseket](container-registry-faq.md) és az egyéb ismert problémákat a gyakori kérdések között Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
