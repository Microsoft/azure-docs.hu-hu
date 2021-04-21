---
title: Gyakori kérdések
description: Válaszok a Azure Container Registry szolgáltatással kapcsolatos gyakori kérdésekre
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: a8c007d7f4419ddbe1555b50ceb6fb92ea0a6f98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783898"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Gyakori kérdések a Azure Container Registry

Ez a cikk a gyakori kérdéseket és az ismert problémákat Azure Container Registry.

A beállításjegyzék hibaelhárítási útmutatója:
* [A beállításjegyzékbe való bejelentkezés hibaelhárítása](container-registry-troubleshoot-login.md)
* [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
* [Beállításjegyzék teljesítményének hibaelhárítása](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Erőforrás-kezelés

- [Létrehozhatok Azure Container Registryt egy Resource Manager használatával?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Van biztonsági rések vizsgálata az ACR-hez használt rendszerképeken?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hogyan a Kubernetes-t a Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hogyan le a rendszergazdai hitelesítő adatokat egy tároló-beállításjegyzékhez?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hogyan a rendszergazdai hitelesítő adatokat egy Resource Manager sablonban?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A replikáció törlése Tiltott állapottal meghiúsul, bár a replikáció törlődik az Azure CLI vagy a Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [A tűzfalszabályok sikeresen frissülnek, de nem lépnek életbe](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Létrehozhatok egy Azure Container Registry egy Resource Manager sablonnal?

Igen. Itt egy [sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) használhat a beállításjegyzék létrehozásához.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Van biztonsági rések vizsgálata az ACR-hez használt rendszerképeken?

Igen. Tekintse meg az [Azure Security Center,](../security-center/defender-for-container-registries-introduction.md) [a Twistlock és](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) a [Aqua dokumentációját.](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hogyan a Kubernetes-t a Azure Container Registry?

Tekintse meg a [Kubernetes dokumentációját, valamint](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) a [Azure Kubernetes Service.](../aks/cluster-container-registry-integration.md)

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hogyan rendszergazdai hitelesítő adatokat egy tároló-beállításjegyzékhez?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiók egyetlen felhasználó számára lett kialakítva a regisztrációs adatbázis eléréséhez, főleg tesztelési célokra. Nem javasoljuk, hogy a rendszergazdai fiók hitelesítő adatait több felhasználóval is megossa. A felhasználók és szolgáltatásnévk számára javasolt az egyéni identitás használata a fej nélküli forgatókönyvekhez. Lásd: [Hitelesítés áttekintése.](container-registry-authentication.md)

Mielőtt rendszergazdai hitelesítő adatokat kap, győződjön meg arról, hogy a beállításjegyzék rendszergazdai felhasználója engedélyezve van.

Hitelesítő adatok lekérte az Azure CLI használatával:

```azurecli
az acr credential show -n myRegistry
```

Az Azure PowerShell-lel:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hogyan a rendszergazdai hitelesítő adatokat egy Resource Manager sablonban?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiók egyetlen felhasználó számára lett kialakítva a regisztrációs adatbázis eléréséhez, főleg tesztelési célokra. Nem javasoljuk, hogy a rendszergazdai fiók hitelesítő adatait több felhasználóval is megossa. A felhasználók és szolgáltatásnévk számára javasolt az egyéni identitás használata a fej nélküli forgatókönyvekhez. Lásd: [Hitelesítés áttekintése.](container-registry-authentication.md)

Mielőtt rendszergazdai hitelesítő adatokat kap, győződjön meg arról, hogy a beállításjegyzék rendszergazdai felhasználója engedélyezve van.

Az első jelszó lekért száma:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

A második jelszó lekért száma:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A replikáció törlése Tiltott állapottal meghiúsul, bár a replikáció törlődik az Azure CLI vagy a Azure PowerShell

A hiba akkor jelenik meg, ha a felhasználó engedéllyel rendelkezik egy beállításjegyzékhez, de nem rendelkezik Olvasó szintű engedélyekkel az előfizetéshez. A probléma megoldásához rendeljen olvasói engedélyeket az előfizetéshez a felhasználóhoz:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>A tűzfalszabályok frissítése sikeresen megtörtént, de nem lépnek életbe

A tűzfalszabályok módosításainak propagálása némi időt vesz igénybe. A tűzfalbeállítások módosítása után várjon néhány percet a módosítás ellenőrzése előtt.


## <a name="registry-operations"></a>Regisztrációs adatbázis műveletei

- [Hogyan Docker Registry HTTP API 2-es verzió?](#how-do-i-access-docker-registry-http-api-v2)
- [Hogyan törölni az összes olyan jegyzékfájlt, amelyekre egy adattár egyik címkéje sem hivatkozik?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Miért nem csökken a regisztrációs adatbázis kvótahasználata a lemezképek törlése után?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hogyan a tárolási kvóta változásait?](#how-do-i-validate-storage-quota-changes)
- [Hogyan a beállításjegyzékben, amikor a CLI-t egy tárolóban futtatja?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [A TLS 1.2 engedélyezése](#how-to-enable-tls-12)
- [Támogatja Azure Container Registry a tartalom megbízhatóságát?](#does-azure-container-registry-support-content-trust)
- [Hogyan hozzáférést ad a rendszerképek leküldéséhez a beállításjegyzék-erőforrás kezeléséhez szükséges engedély nélkül?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hogyan a rendszerkép automatikus karanténba kerül egy beállításjegyzékben?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Hogyan engedélyezhetem a névtelen lekérési hozzáférést?](#how-do-i-enable-anonymous-pull-access)
- [Hogyan nem terjeszthető rétegeket a regisztrációs adatbázisba?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hogyan Docker Registry HTTP API 2-es verzió?

Az ACR támogatja a Docker Registry HTTP API V2-t. Az API-k a következő ről érhetők el: `https://<your registry login server>/v2/` . Például: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hogyan törölni az összes olyan jegyzékfájlt, amelyekre egy adattár egyik címkéje sem hivatkozik?

Bash esetén:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

A PowerShellhez:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Megjegyzés: A törlési `-y` paranccsal kihagyhatja a megerősítést.

További információ: Tároló rendszerképének törlése [a Azure Container Registry.](container-registry-delete.md)

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Miért nem csökken a regisztrációs adatbázis kvótahasználata a lemezképek törlése után?

Ez akkor fordulhat elő, ha a mögöttes rétegekre továbbra is hivatkoznak más tároló-lemezképek. Ha hivatkozás nélkül töröl egy rendszerképet, a beállításjegyzék használata néhány percen belül frissül.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hogyan a tárolási kvóta változásait?

Hozzon létre egy 1 GB-os réteggel egy rendszerképet az alábbi Docker-fájl használatával. Ez biztosítja, hogy a rendszerkép olyan réteggel rendelkezik, amelyet a beállításjegyzék más lemezképe nem osztott meg.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

A rendszerképet a docker CLI használatával buildek és lekért adatok leküldése a regisztrációs adatbázisba.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Láthatja, hogy a tárterület kihasználtsága megnőtt a Azure Portal, vagy lekérdezheti a használatot a CLI használatával.

```azurecli
az acr show-usage -n myregistry
```

Törölje a rendszerképet az Azure CLI vagy a portál használatával, és néhány perc múlva ellenőrizze a frissített használatot.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hogyan a beállításjegyzékben, amikor a CLI-t egy tárolóban futtatja?

Az Azure CLI-tárolót a Docker-szoftvercsatorna csatlakoztatásával kell futtatnia:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

A tárolóban telepítse a `docker` következőt:

```bash
apk --update add docker
```

Ezután hitelesítse magát a beállításjegyzékben:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>A TLS 1.2 engedélyezése

Engedélyezze a TLS 1.2-t bármely újabb Docker-ügyféllel (18.03.0-s vagy újabb verzió). 

> [!IMPORTANT]
> 2020. január 13-tól Azure Container Registry a TLS 1.2 használatára minden biztonságos kapcsolat szükséges a kiszolgálóktól és alkalmazásoktól. A TLS 1.0 és 1.1 támogatása ki leszvezetve.

### <a name="does-azure-container-registry-support-content-trust"></a>Támogatja Azure Container Registry a tartalom megbízhatóságát?

Igen, használhatja a megbízható rendszerképeket a Azure Container Registry, mivel a [Docker-notary](https://docs.docker.com/notary/getting_started/) integrálva van, és engedélyezhető. Részletekért lásd: [Tartalom megbízhatósága a Azure Container Registry.](container-registry-content-trust.md)


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Hol található az ujjlenyomat fájlja?

A `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` alatt:

* A nyilvános kulcsokat és az összes szerepkör tanúsítványait (a delegálási szerepkörök kivételével) az `root.json` tárolja.
* A delegálás szerepkör nyilvános kulcsait és tanúsítványait a rendszer a szülőszerepk számára (például a szerepkörhöz) a JSON-fájlban `targets.json` `targets/releases` tárolja.

Ezeket a nyilvános kulcsokat és tanúsítványokat a Docker- és a notary-ügyfél által végzett teljes TUF-ellenőrzés után ajánlott ellenőrizni.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hogyan hozzáférést ad a rendszerképek leküldéséhez a beállításjegyzék-erőforrás kezeléséhez szükséges engedély nélkül?

Az ACR támogatja [az egyéni szerepköröket,](container-registry-roles.md) amelyek különböző szintű engedélyeket biztosítanak. Pontosabban a és a szerepkörök lehetővé teszik a felhasználók számára a rendszerképek leküldését és/vagy leküldését a regisztrációs adatbázis erőforrásának Az Azure-ban való `AcrPull` `AcrPush` kezeléséhez szükséges engedély nélkül.

* Azure Portal: A beállításjegyzék -> Access Control (IAM) -> (válassza ki a szerepkört vagy `AcrPull` `AcrPush` a szerepkört).
* Azure CLI: A következő parancs futtatásával keresse meg a regisztrációs adatbázis erőforrás-azonosítóját:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Ezután hozzárendelheti a vagy a `AcrPull` `AcrPush` szerepkört egy felhasználóhoz (az alábbi példa a következőt `AcrPull` használja):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Vagy rendelje hozzá a szerepkört egy, az alkalmazásazonosítója által azonosított szolgáltatásnévhez:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

A hozzárendelő ezután képes lesz a regisztrációs adatbázisban a rendszerképek hitelesítésére és elérésére.

* A beállításjegyzékben való hitelesítéshez:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Az adattárak listához:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Rendszerkép lekért száma:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Ha csak a vagy szerepkört használja, a hozzárendelő nem rendelkezik engedéllyel a regisztrációs adatbázis erőforrásának kezeléséhez az `AcrPull` `AcrPush` Azure-ban. A vagy `az acr list` a például nem mutatja a `az acr show -n myRegistry` beállításjegyzéket.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hogyan a rendszerkép automatikus karanténba kerül egy beállításjegyzékben?

A rendszerkép-karantén jelenleg az ACR előzetes verziójú funkciója. Engedélyezheti a regisztrációs adatbázis karanténba helyezett módját, így csak azok a lemezképek láthatók a normál felhasználók számára, amelyek sikeresen átesnek a biztonsági vizsgálaton. Részletekért lásd az [ACR GitHub-adattárat.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)

### <a name="how-do-i-enable-anonymous-pull-access"></a>Hogyan engedélyezhetem a névtelen lekérési hozzáférést?

Az Azure Container Registry beállítása névtelen (nem hitelesített) lehívott hozzáféréshez jelenleg előzetes verziójú funkció, amely a Standard és a Prémium szolgáltatási [szinten érhető el.](container-registry-skus.md) 

A névtelen lekért hozzáférés engedélyezéséhez frissítsen egy regisztrációs adatbázist az Azure CLI (2.21.0-s vagy újabb) verziójával, és adja át a paramétert az `--anonymous-pull-enabled` [az acr update parancsnak:](/cli/azure/acr#az_acr_update)

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

A névtelen lekért hozzáférést bármikor letilthatja a `--anonymous-pull-enabled` `false` beállításával.

> [!NOTE]
> * Mielőtt névtelen leküldi a műveletet, futtassa a(a)t, hogy törölje a `docker logout` meglévő Docker-hitelesítő adatokat.
> * Csak az adatsíkműveletek érhetők el a nem hitelesített ügyfelek számára.
> * Előfordulhat, hogy a beállításjegyzék nagy sebességű nem hitelesített kérelmeket fog lecsökkenteni.

> [!WARNING]
> A névtelen lekért hozzáférés jelenleg a beállításjegyzékben lévő összes adattárra vonatkozik. Ha az adattár hozzáférését [](container-registry-repository-scoped-permissions.md)adattárhatókörű jogkivonatokkal kezeli, vegye figyelembe, hogy minden felhasználó leküldheti ezeket az adattárakat egy névtelen lekért beállításjegyzékben. Javasoljuk a jogkivonatok törlését, ha a névtelen lekért hozzáférés engedélyezve van.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Hogyan nem terjeszthető rétegeket a regisztrációs adatbázisba?

A jegyzékfájl nem terjeszthető rétege tartalmaz egy URL-paramétert, amelyből a tartalom lekérhető. A nem terjeszthető rétegbeli leküldések engedélyezésének néhány lehetséges esete a korlátozott hálózati beállításregisztrálók, a korlátozott hozzáférésű, air gaped beállításregisztrálók vagy az internetkapcsolat nélküli beállításregisztrálók.

Ha például NSG-szabályok vannak beállítva, hogy egy virtuális gép csak az Azure Container Registryből tudja lekért rendszerképeket, a Docker le fogja állítani a külső/nem terjeszthető rétegek meghibásodását. Egy Windows Server Core-rendszerkép például az Azure Container Registryre mutató külső rétegbeli hivatkozásokat tartalmazna a jegyzékfájlban, és ebben a forgatókönyvben nem lenne lekért hivatkozás.

A nem terjeszthető rétegek lekért lekért adatok:

1. Szerkessze a Fájlt, amely a Linux-gazdagépek és a Helyen található `daemon.json` `/etc/docker/` a Windows `C:\ProgramData\docker\config\daemon.json` Serveren. Feltételezve, hogy a fájl korábban üres volt, adja hozzá a következő tartalmakat:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > Az érték a beállításjegyzékbeli címek vesszővel elválasztott tömbje.

2. Mentse a fájlt, majd lépjen ki a fájlból.

3. Indítsa újra a Docker-t.

Amikor rendszerképeket ad le a listában található beállításjegyzékekbe, azok nem terjeszthető rétegei a regisztrációs adatbázisba vannak leküldve.

> [!WARNING]
> A nem terjeszthető összetevők általában korlátozva vannak az elosztásuk és -elosztásuk mikéntj és hol. Ezt a funkciót csak akkor használja, ha az összetevők privát beállításregisztrálókba vannak leküldve. Győződjön meg arról, hogy megfelel a nem terjeszthető összetevők újraterjesztésére vonatkozó feltételeknek.

## <a name="diagnostics-and-health-checks"></a>Diagnosztika és állapot-ellenőrzések

- [Állapot ellenőrzése a `az acr check-health`](#check-health-with-az-acr-check-health)
- [A docker-lekérés a következő hibával meghiúsul: net/http: a kérés megszakadt a kapcsolatra való várakozás során (a Client.Timeout túllépve, miközben fejlécre vár)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push succeeds but docker pull fails with error: unauthorized: authentication required](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` sikeres, de a Docker-parancsok a következő hibával meghiúsulnak: jogosulatlan: hitelesítés szükséges](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [A docker-démon hibakeresési naplóinak engedélyezése és lekérte](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Előfordulhat, hogy az új felhasználói engedélyek nem lépnek azonnal hatályba a frissítés után](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [A hitelesítési adatok nem a megfelelő formátumban adták meg a közvetlen REST API esetén](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Miért nem Azure Portal az összes adattárat vagy címkét?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Miért nem sikerül Azure Portal adattárak vagy címkék beolvasása?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Miért nem engedélyezett művelettel meghiúsul a lekéréses vagy leküldéses kérelem?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Az adattár formátuma érvénytelen vagy nem támogatott](#repository-format-is-invalid-or-unsupported)
- [Hogyan gyűjt http-nyomkövetéseket Windows rendszeren?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Állapot ellenőrzése a `az acr check-health`

A környezettel és a beállításjegyzékekkel kapcsolatos gyakori hibák elhárításához tekintse meg [az Azure Container Registry állapotának ellenőrzésével kapcsolatos témakört.](container-registry-check-health.md)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>A docker lekérése a következő hibával meghiúsul: net/http: a kérés megszakadt a kapcsolatra való várakozás közben (a Client.Timeout túllépte a várakozás közben a fejléceket)

 - Ha ez a hiba átmeneti probléma, az újrapróbálkozás sikeres lesz.
 - Ha `docker pull` folyamatosan meghibásodik, akkor a Docker-démonnal lehet probléma. A problémát általában a Docker-démon újraindításával lehet enyhíteni. 
 - Ha a Docker-démon újraindítása után továbbra is ezt a problémát látja, akkor a probléma a gép hálózati kapcsolatával kapcsolatos lehet. Annak ellenőrzéséhez, hogy a gép általános hálózata kifogástalan állapotú-e, futtassa a következő parancsot a végpontkapcsolat teszteléséhez. A `az acr` kapcsolat-ellenőrző parancsot tartalmazó minimális verzió: 2.2.9. Ha régebbi verziót használ, frissítse az Azure CLI-t.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Minden Docker-ügyfélművelethez mindig legyen újrapróbálkozási mechanizmus.

### <a name="docker-pull-is-slow"></a>A Docker-lekért folyamat lassú
Ezzel [az eszközzel](http://www.azurespeed.com/Azure/Download) tesztelje a számítógép hálózatának letöltési sebességét. Ha a géphálózat lassú, fontolja meg Az Azure-beli virtuális gép használata ugyanabban a régióban, mint a regisztrációs adatbázis. Ez általában gyorsabb hálózati sebességet biztosít.

### <a name="docker-push-is-slow"></a>A Docker-leküldés lassú
Ezzel [az eszközzel](http://www.azurespeed.com/Azure/Upload) tesztelheti a gép hálózati feltöltési sebességét. Ha a géphálózat lassú, fontolja meg Az Azure-beli virtuális gép használata ugyanabban a régióban, mint a regisztrációs adatbázis. Ez általában gyorsabb hálózati sebességet biztosít.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>A Docker-leküldés sikeres, de a Docker-leküldés a következő hibával meghiúsul: jogosulatlan: hitelesítés szükséges

Ez a hiba a Docker-démon Red Hat verziójával fordulhat elő, ahol alapértelmezés szerint `--signature-verification` engedélyezve van. Az alábbi parancs futtatásával ellenőrizheti a Docker-démon Red Hat Enterprise Linux (RHEL) vagy a Fedora beállítását:

```bash
grep OPTIONS /etc/sysconfig/docker
```

A Fedora 28 Server például a következő Docker-démonbeállításokat tartalmazza:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Hiányzó `--signature-verification=false` hiba esetén a következő `docker pull` hibához hasonló hibával meghiúsul:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

A hiba elhárításához:
1. Adja hozzá a `--signature-verification=false` beállítást a Docker-démon konfigurációs `/etc/sysconfig/docker` fájlhoz. Például:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Indítsa újra a Docker-démon szolgáltatást a következő parancs futtatásával:
   
   ```bash
   sudo systemctl restart docker.service
   ```

A részletei `--signature-verification` a futtatásával találhatók `man dockerd` meg.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login succeeds but docker fails with error: unauthorized: authentication required

Ügyeljen arra, hogy csak kisbetűs kiszolgálói URL-címet használjon ( például : ) még akkor is, ha a beállításjegyzékbeli erőforrás neve kis- vagy `docker push myregistry.azurecr.io/myimage:latest` nagybetűs, például `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>A Docker-démon hibakeresési naplóinak engedélyezése és lekérte    

Kezdje `dockerd` a `debug` beállítással. Először hozza létre a Docker-démon konfigurációs fájlját ( ), ha az még nem `/etc/docker/daemon.json` létezik, majd adja hozzá a következő `debug` beállítást:

```json
{    
    "debug": true    
}
```

Ezután indítsa újra a démont. Például Ubuntu 14.04-es rendszerekkel:

```bash
sudo service docker restart
```

A részleteket a [Docker dokumentációjában találhatja meg.](https://docs.docker.com/engine/admin/#enable-debugging)    

 * A naplók a rendszertől függően különböző helyeken is létre lehetnek hozva. Az Ubuntu 14.04-es rendszer például a `/var/log/upstart/docker.log` következő: .    
A [részletekért tekintse meg a Docker](https://docs.docker.com/engine/admin/#read-the-logs) dokumentációját.    

 * A Windowshoz való Docker esetében a naplók a %LOCALAPPDATA%/docker/ mappában jönnek létre. Előfordulhat azonban, hogy még nem tartalmazza az összes hibakeresési információt.    

   A teljes démonnapló eléréséhez további lépésekre lehet szükség:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Most már rendelkezik hozzáféréssel a-t futtató virtuális gép összes `dockerd` fájlhoz. A napló a következő: `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Előfordulhat, hogy az új felhasználói engedélyek nem lesznek azonnal hatályba lépve a frissítés után

Amikor új engedélyeket (új szerepköröket) ad egy szolgáltatásnévnek, előfordulhat, hogy a módosítás nem lép azonnal életbe. Ennek két lehetséges oka lehet:

* Azure Active Directory szerepkör-hozzárendelés késleltetése. Általában gyors, de a propagálás késése miatt percekig is eltarthat.
* Engedélykésés az ACR-jogkivonat-kiszolgálón. Ez akár 10 percet is igénybe vehet. A probléma elhárításához 1 perc múlva újra hitelesítheti magát ugyanazokkal `docker logout` a felhasználóval:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Az ACR jelenleg nem támogatja a felhasználók által az otthoni replikáció törlését. Megkerülő megoldásként a sablonba foglalja bele az otthoni replikációs létrehozást, de hagyja ki a létrehozását a hozzáadással `"condition": false` az alábbiak szerint:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>A hitelesítési adatok nem a megfelelő formátumban adták meg a közvetlen REST API esetén

Előfordulhat, hogy hibába ütközik, különösen ha az eszközt használja a beállítással `InvalidAuthenticationInfo` `curl` `-L` `--location` (az átirányítások nyomon követéshez).
Például lekérheti a blobot az kapcsolóval és az `curl` `-L` alapszintű hitelesítéssel:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

A a következő választ eredményezheti:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Ennek az az oka, hogy egyes implementációk az eredeti kérés fejlécekkel való `curl` átirányítását követik.

A probléma megoldásához manuálisan kell követnie az átirányításokat a fejlécek nélkül. Nyomtassa ki a válaszfejléceket a `-D -` kapcsolóval, `curl` majd bontsa ki a `Location` fejlécet:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Miért nem Azure Portal az összes adattárat vagy címkét? 

Ha a Microsoft Edge/IE böngészőt használja, akkor a legtöbb 100 adattárat vagy címkét láthatja. Ha a regisztrációs adatbázis több mint 100 tárházat vagy címkét tartalmaz, javasoljuk, hogy használja a Firefox vagy a Chrome böngészőt az összes listához.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Miért nem sikerül Azure Portal adattárak vagy címkék beolvasása?

Előfordulhat, hogy a böngésző nem tudja elküldeni az adattárak vagy címkék lekérésére vonatkozó kérést a kiszolgálónak. Több oka is lehet, például:

* Hálózati kapcsolat hiánya
* Firewall
* Hirdetésblokkolók
* DNS-hibák

Forduljon a hálózati rendszergazdához, vagy ellenőrizze a hálózati konfigurációt és a kapcsolatot. Próbálja meg az Azure CLI-t használni a futtatásával annak ellenőrzéséhez, hogy a környezet tud-e `az acr check-health -n yourRegistry` csatlakozni a Container Registry. Emellett inkognitó vagy privát munkamenetet is kipróbálhat a böngészőben, hogy elkerülje az elavult böngésző-gyorsítótárat vagy cookie-kat.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Miért nem engedélyezett művelettel meghiúsul a lekéréses vagy leküldéses kérelem?

Az alábbi forgatókönyvekben a műveletek nem engedélyezettek lehetnek:
* A klasszikus beállításregisztrálók már nem támogatottak. Frissítsen egy támogatott szolgáltatási [szintre](./container-registry-skus.md) [az az acr update vagy](/cli/azure/acr#az_acr_update) a Azure Portal.
* Lehetséges, hogy a rendszerkép vagy adattár zárolva van, hogy ne törölhető vagy frissíthető legyen. Az aktuális attribútumok megtekintéséhez használhatja az [az acr show repository](./container-registry-image-lock.md) parancsot.
* Egyes műveletek nem engedélyezettek, ha a rendszerkép karanténban van. További információ a [karanténról.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)
* Előfordulhat, hogy a beállításjegyzék elérte a [tárhelykorlátját.](container-registry-skus.md#service-tier-features-and-limits)

### <a name="repository-format-is-invalid-or-unsupported"></a>Az adattár formátuma érvénytelen vagy nem támogatott

Ha "nem támogatott adattárformátum", "érvénytelen formátum" vagy "a kért adatok nem léteznek" hibaüzenet jelenik meg az adattár nevének adattárműveletben való megadásakor, ellenőrizze a név helyesírását és kis- és kis-ésaját. Az érvényes adattárnevek csak kisbetűs alfanumerikus karaktereket, pontokat, kötőjeleket, aláhúzásjeleket és perjeleket tartalmazhatnak. 

Az adattár teljes elnevezési szabályait lásd: [Open Container Initiative Distribution Specification (Open Container Initiative Distribution Specification).](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hogyan http-nyomkövetéseket gyűjteni Windows rendszeren?

#### <a name="prerequisites"></a>Előfeltételek

- Engedélyezze a https visszafejtése a Fiddlerben:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Engedélyezze a Docker számára, hogy proxyt használjon a Docker felhasználói felületén: <https://docs.docker.com/docker-for-windows/#proxies>
- Ha elkészült, mindenképpen visszaállítást kell végrehajtania.  A Docker nem fog működni ezzel az engedélyezett funkcióval, és a Fiddler nem fut.

#### <a name="windows-containers"></a>Windows-tárolók

A Docker-proxy konfigurálása a következőre: 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-tárolók

Keresse meg a Docker virtuális gép virtuális kapcsolója IP-címét:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurálja a Docker-proxyt az előző parancs kimenetére és a 8888-as portra (például: 10.0.75.1:8888)

## <a name="tasks"></a>Feladatok

- [Hogyan megszakítani a futtatásokat?](#how-do-i-batch-cancel-runs)
- [Hogyan bele a .git mappát az az acr build parancsba?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Támogatja a Tasks a GitLab for Source eseményindítókat?](#does-tasks-support-gitlab-for-source-triggers)
- [Milyen Git-adattárkezelő szolgáltatást támogat a Tasks?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hogyan megszakítani a futtatásokat?

Az alábbi parancsok megszakítják a megadott beállításjegyzékben futó összes feladatot.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hogyan bele a .git mappát az az acr build parancsba?

Ha helyi forrásmappát ad át a parancsnak, a mappa alapértelmezés szerint ki lesz zárva a `az acr build` `.git` feltöltött csomagból. A következő `.dockerignore` beállítással hozhat létre fájlt. Arra utasítja a parancsot, hogy állítsa vissza a feltöltött csomagban található `.git` összes fájlt. 

`!.git/**`

Ez a beállítás a parancsra is `az acr run` vonatkozik.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Támogatja a Tasks a GitLab for Source eseményindítókat?

A Forrás eseményindítókhoz jelenleg nem támogatjuk a GitLab-et.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Milyen Git-adattárkezelő szolgáltatást támogat a Tasks?

| Git-szolgáltatás | Forráskörnyezet | Manuális build | Automatikus felépítés véglegesítési eseményindítóval |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Igen | Yes |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Igen | Yes |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Igen | Nem |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Igen | Nem |

## <a name="run-error-message-troubleshooting"></a>Futtatás hibaüzenetének hibaelhárítása

| Hibaüzenet | Hibaelhárítási útmutató |
|---|---|
|Nem lett konfigurálva hozzáférés a virtuális géphez, ezért nem található előfizetés|Ez akkor fordulhat elő, ha az `az login --identity` -t használja az ACR-feladatban. Ez egy átmeneti hiba, amely akkor fordul elő, ha a felügyelt identitás szerepkör-hozzárendelése nem propagálva van. Várakozás néhány másodpercre az újrapróbálkozás előtt.|

## <a name="cicd-integration"></a>CI/CD-integráció

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Következő lépések

* [További információ a](container-registry-intro.md) Azure Container Registry.
