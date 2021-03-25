---
title: Gyakori kérdések
description: A Azure Container Registry szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: 5550c53289228f154fab485b4b7bbff17555aad7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045739"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Gyakori kérdések a Azure Container Registry

Ez a cikk a Azure Container Registryával kapcsolatos gyakori kérdésekre és ismert problémákra mutat.

A beállításjegyzék hibaelhárítási útmutatóját lásd:
* [Beállításjegyzékbeli bejelentkezés – problémamegoldás](container-registry-troubleshoot-login.md)
* [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
* [Beállításjegyzékbeli teljesítmény – problémamegoldás](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Erőforrás-kezelés

- [Létrehozhatok Azure Container registryt egy Resource Manager-sablon használatával?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Biztonsági rést okoz a rendszerképek keresése az ACR-ben?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hogyan konfigurálja a Kubernetes-t a Azure Container Registrykal?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hogyan beolvasni a tároló-beállításjegyzék rendszergazdai hitelesítő adatait?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hogyan rendszergazdai hitelesítő adatok beszerzése egy Resource Manager-sablonban?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A replikáció törlése a tiltott állapottal meghiúsul, bár a replikálás az Azure CLI-vel vagy a Azure PowerShell használatával törlődik](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [A tűzfalszabályok frissítése sikeresen megtörtént, de nem lépnek érvénybe](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Létrehozhatok egy Azure Container Registry Resource Manager-sablon használatával?

Igen. Itt látható [egy olyan sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) , amellyel létrehozhat egy beállításjegyzéket.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Biztonsági rést okoz a rendszerképek keresése az ACR-ben?

Igen. Tekintse meg a [Azure Security Center](../security-center/defender-for-container-registries-introduction.md), a [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) és az [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)dokumentációját.

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hogyan konfigurálja a Kubernetes-t a Azure Container Registrykal?

Tekintse meg az [Azure Kubernetes szolgáltatás](../aks/cluster-container-registry-integration.md) [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) és lépéseinek dokumentációját.

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hogyan beolvasni a tároló-beállításjegyzék rendszergazdai hitelesítő adatait?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiók úgy van kialakítva, hogy egyetlen felhasználó hozzáférjen a beállításjegyzékhez, főleg tesztelési célokra. A rendszergazdai fiók hitelesítő adatait nem ajánlott több felhasználóval megosztani. A felhasználók és a szolgáltatásokhoz egyéni identitást kell használni a fej nélküli forgatókönyvek esetében. Lásd: a [hitelesítés áttekintése](container-registry-authentication.md).

A rendszergazdai hitelesítő adatok beszerzése előtt győződjön meg arról, hogy a beállításjegyzék rendszergazda felhasználója engedélyezve van.

Hitelesítő adatok beszerzése az Azure CLI használatával:

```azurecli
az acr credential show -n myRegistry
```

Az Azure PowerShell-lel:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hogyan rendszergazdai hitelesítő adatok beszerzése egy Resource Manager-sablonban?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiók úgy van kialakítva, hogy egyetlen felhasználó hozzáférjen a beállításjegyzékhez, főleg tesztelési célokra. A rendszergazdai fiók hitelesítő adatait nem ajánlott több felhasználóval megosztani. A felhasználók és a szolgáltatásokhoz egyéni identitást kell használni a fej nélküli forgatókönyvek esetében. Lásd: a [hitelesítés áttekintése](container-registry-authentication.md).

A rendszergazdai hitelesítő adatok beszerzése előtt győződjön meg arról, hogy a beállításjegyzék rendszergazda felhasználója engedélyezve van.

Az első jelszó beszerzése:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

A második jelszó beszerzése:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A replikáció törlése a tiltott állapottal meghiúsul, bár a replikálás az Azure CLI-vel vagy a Azure PowerShell használatával törlődik

A hiba akkor jelenik meg, ha a felhasználó rendelkezik jogosultságokkal a beállításjegyzékben, de nem rendelkezik olvasó szintű engedélyekkel az előfizetéshez. A probléma megoldásához rendeljen olvasói engedélyeket az előfizetéshez a felhasználó számára:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>A tűzfalszabályok frissítése sikeresen megtörtént, de nem lépnek érvénybe

Időbe telik a tűzfalszabály módosításainak propagálása. A tűzfalbeállítások módosítása után várjon néhány percet, mielőtt ellenőrzi a változást.


## <a name="registry-operations"></a>Regisztrációs adatbázis műveletei

- [Hogyan Access Docker Registry HTTP API v2?](#how-do-i-access-docker-registry-http-api-v2)
- [Hogyan törölni az összes olyan jegyzékfájlt, amelyre nem hivatkozik az adattár egyik címkéje sem?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Miért nem csökkenti a beállításjegyzékbeli kvóta használatát a lemezképek törlése után?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hogyan ellenőrzi a tárolási kvóta változásait?](#how-do-i-validate-storage-quota-changes)
- [Hogyan hitelesíteni a beállításjegyzéket a CLI egy tárolóban való futtatásakor?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [A TLS 1,2 engedélyezése](#how-to-enable-tls-12)
- [Támogatja a Azure Container Registry a tartalom megbízhatóságát?](#does-azure-container-registry-support-content-trust)
- [Hogyan hozzáférést biztosítanak a lekéréses vagy leküldéses képekhez engedély nélkül a beállításjegyzék-erőforrás kezeléséhez?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hogyan lehetővé teszi az automatikus rendszerképek karanténba helyezését a beállításjegyzékben?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Hogyan engedélyezze a névtelen lekéréses hozzáférést?](#how-do-i-enable-anonymous-pull-access)
- [A nem terjeszthető rétegek leküldése a beállításjegyzékbe Hogyan](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hogyan Access Docker Registry HTTP API v2?

Az ACR támogatja a Docker beállításjegyzék HTTP API v2-es verzióját. Az API-k a következő címen érhetők el: `https://<your registry login server>/v2/` . Például: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hogyan törölni az összes olyan jegyzékfájlt, amelyre nem hivatkozik az adattár egyik címkéje sem?

Ha a bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

PowerShell esetén:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Megjegyzés: `-y` a jóváhagyás kihagyása érdekében a DELETE paranccsal adhat hozzá.

További információ: [Container-lemezképek törlése Azure Container Registryban](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Miért nem csökkenti a beállításjegyzékbeli kvóta használatát a lemezképek törlése után?

Ez akkor fordulhat elő, ha az alapul szolgáló rétegeket más tárolói képek is hivatkoznak rájuk. Ha olyan képet töröl, amely nem hivatkozik, a beállításjegyzék-használat néhány perc múlva frissül.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hogyan ellenőrzi a tárolási kvóta változásait?

Hozzon létre egy 1 GB-os réteget tartalmazó rendszerképet a következő Docker-fájl használatával. Ez biztosítja, hogy a rendszerkép olyan réteggel rendelkezik, amelyet a beállításjegyzékben lévő többi rendszerkép sem oszt meg.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Hozza létre és küldje le a rendszerképet a beállításjegyzékbe a Docker CLI használatával.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Látnia kell, hogy a tárhely kihasználtsága nőtt a Azure Portalban, vagy lekérdezheti a használatot a CLI használatával.

```azurecli
az acr show-usage -n myregistry
```

Törölje a rendszerképet az Azure CLI vagy a Portal használatával, és néhány perc alatt vizsgálja meg a frissített használatot.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hogyan hitelesíteni a beállításjegyzéket a CLI egy tárolóban való futtatásakor?

Az Azure CLI-tárolót a Docker-szoftvercsatorna csatlakoztatásával kell futtatnia:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

A tárolóban telepítse a `docker` következőket:

```bash
apk --update add docker
```

Ezután végezze el a hitelesítést a beállításjegyzékben:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>A TLS 1,2 engedélyezése

Engedélyezze a TLS 1,2-et a legutóbbi Docker-ügyfél (18.03.0 vagy újabb verzió) használatával. 

> [!IMPORTANT]
> 2020. január 13-án Azure Container Registry a TLS 1,2 használatához a kiszolgálók és alkalmazások összes biztonságos kapcsolata szükséges. A TLS 1,0 és 1,1 támogatása megszűnik.

### <a name="does-azure-container-registry-support-content-trust"></a>Támogatja a Azure Container Registry a tartalom megbízhatóságát?

Igen, a Azure Container Registryban megbízható rendszerképeket használhat, mivel a [Docker közjegyző](https://docs.docker.com/notary/getting_started/) integrálva van, és engedélyezhető. Részletekért lásd: [a tartalom megbízhatósága Azure Container Registryban](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Hol található a fájl az ujjlenyomathoz?

Alatt `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Az összes szerepkör nyilvános kulcsait és tanúsítványait (kivéve a delegálási szerepköröket) a () tárolja `root.json` .
* A delegálási szerepkör nyilvános kulcsait és tanúsítványait a szülő szerepkör JSON-fájlja (például `targets.json` a `targets/releases` szerepkör) tárolja.

Javasoljuk, hogy ellenőrizze ezeket a nyilvános kulcsokat és tanúsítványokat a Docker és a közjegyző ügyfél által végzett teljes TUF-ellenőrzés után.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hogyan hozzáférést biztosítanak a lekéréses vagy leküldéses képekhez engedély nélkül a beállításjegyzék-erőforrás kezeléséhez?

Az ACR olyan [Egyéni szerepköröket](container-registry-roles.md) támogat, amelyek különböző szintű engedélyeket biztosítanak. A `AcrPull` és `AcrPush` a szerepkörök lehetővé teszik a felhasználók számára a rendszerképek lekérését és/vagy leküldését anélkül, hogy engedélyt kellene kapniuk a beállításjegyzék erőforrásának felügyeletére az Azure

* Azure Portal: a beállításjegyzék-> Access Control (IAM) – > Hozzáadás (válassza ki `AcrPull` vagy a `AcrPush` szerepkört).
* Azure CLI: keresse meg a beállításjegyzék erőforrás-AZONOSÍTÓját a következő parancs futtatásával:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Ezt követően hozzárendelheti a `AcrPull` `AcrPush` (z) vagy szerepkört egy felhasználóhoz (a következő példa használ `AcrPull` ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Vagy rendeljen hozzá egy szerepkört egy egyszerű szolgáltatáshoz, amelyet az alkalmazás azonosítója azonosított:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

A megbízott ezután képes hitelesíteni és elérni a rendszerképeket a beállításjegyzékben.

* Hitelesítés a beállításjegyzékben:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Adattárak listázása:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Rendszerkép lekérése:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

A (z) és a (z) csak a (z) és a (z `AcrPull` `AcrPush` ) szerepkör használatával nem jogosult a beállításjegyzék-erőforrás kezelésére az Azure-ban. Például `az acr list` vagy `az acr show -n myRegistry` nem jelenik meg a beállításjegyzék.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hogyan lehetővé teszi az automatikus rendszerképek karanténba helyezését a beállításjegyzékben?

A kép karanténba helyezése jelenleg az ACR előzetes verziójú funkciója. Engedélyezheti egy beállításjegyzék karantén üzemmódját, hogy csak azok a lemezképek legyenek láthatók a normál felhasználók számára, akik sikeresen átadtak a biztonsági vizsgálatot. Részletekért tekintse meg az [ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)-tárházat.

### <a name="how-do-i-enable-anonymous-pull-access"></a>Hogyan engedélyezze a névtelen lekéréses hozzáférést?

A névtelen (nem hitelesített) Azure Container Registry szolgáltatás beállítása jelenleg előzetes verzióként érhető el, amely a standard és a prémium [szintű szolgáltatási](container-registry-skus.md)szinten érhető el. 

A névtelen lekéréses hozzáférés engedélyezéséhez frissítsen egy beállításjegyzéket az Azure CLI használatával (2.21.0 vagy újabb verzió), és adja át a `--anonymous-pull-enabled` paramétert az az [ACR Update](/cli/azure/acr#az_acr_update) parancsnak:

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

A (z) beállítással bármikor letilthatja a névtelen lekéréses hozzáférést `--anonymous-pull-enabled` `false` .

> [!NOTE]
> * A névtelen lekérési művelet megkísérlése előtt futtassa `docker logout` a parancsot, és győződjön meg arról, hogy a meglévő Docker-hitelesítő adatok törlődnek.
> * Csak az adatsík műveletek érhetők el a nem hitelesített ügyfelek számára.
> * A beállításjegyzék nagy mértékben szabályozhatja a nem hitelesített kérelmeket.

> [!WARNING]
> A névtelen lekéréses hozzáférés jelenleg a beállításjegyzék összes tárházára érvényes. Ha [adattár-hatókörű jogkivonatokkal](container-registry-repository-scoped-permissions.md)kezeli a tárház-hozzáférést, vegye figyelembe, hogy az összes felhasználó lehívhatja ezeket a tárházait egy olyan beállításjegyzékből, amely engedélyezve van a névtelen lekéréshez. Ajánlott törölni a jogkivonatokat, ha a névtelen lekéréses hozzáférés engedélyezve van.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>A nem terjeszthető rétegek leküldése a beállításjegyzékbe Hogyan

A jegyzékfájlban található nem terjeszthető réteg olyan URL-paramétert tartalmaz, amelyből a tartalom beolvasható. A nem terjeszthető rétegek engedélyezésének egyes lehetséges használati esetei a hálózati korlátozott nyilvántartások, a korlátozott hozzáférésű gapped-jegyzékek, illetve az internetkapcsolattal nem rendelkező kibocsátásiegység-forgalmi jegyzékek esetében használhatók.

Ha például úgy állítja be a NSG-szabályokat, hogy a virtuális gép csak az Azure Container registryből tudja lekérni a lemezképeket, a Docker lekéri a külső/nem terjeszthető rétegek hibáit. Egy Windows Server Core-rendszerkép például az Azure Container registryre mutató idegen rétegbeli hivatkozásokat tartalmazhatja a jegyzékfájljában, és nem fogja tudni lekérni ezt a forgatókönyvet.

Nem terjeszthető rétegek kitolásának engedélyezése:

1. Szerkessze a `daemon.json` fájlt, amely a `/etc/docker/` Linux-gazdagépeken és a `C:\ProgramData\docker\config\daemon.json` Windows Server rendszeren található. Ha feltételezi, hogy a fájl korábban üres, adja hozzá a következő tartalmakat:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > Az érték a beállításjegyzékbeli címek tömbje, vesszővel elválasztva.

2. Mentse és zárja be a fájlt.

3. A Docker újraindítása.

Ha a listában lévő beállításjegyzékbe leküldi a lemezképeket, a nem terjeszthető rétegeket a rendszer leküldi a beállításjegyzékbe.

> [!WARNING]
> A nem terjeszthető összetevők általában korlátozásokkal rendelkeznek a terjesztésük és a megosztásuk módjára vonatkozóan. Ezt a funkciót csak az összetevők privát beállításjegyzékbe való leküldésére használhatja. Győződjön meg arról, hogy megfelel a nem terjeszthető összetevők újraterjesztését lefedő feltételeknek.

## <a name="diagnostics-and-health-checks"></a>Diagnosztika és állapot-ellenőrzések

- [Állapot-ellenõrzés `az acr check-health`](#check-health-with-az-acr-check-health)
- [a Docker-lekérés sikertelen a következő hibával: net/http: a kérelem megszakadt a kapcsolatra való várakozás közben (az ügyfél. időtúllépési korlátja túllépte a fejléceket)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [a Docker leküldése sikeres, de a Docker-lekérés sikertelen a következő hibával: jogosulatlan: hitelesítés szükséges](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` sikeres, de a Docker-parancsok sikertelenek, a következő hibával: jogosulatlan: hitelesítés szükséges](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [A Docker-démon hibakeresési naplóinak engedélyezése és lekérése](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Előfordulhat, hogy az új felhasználói engedélyek nem lépnek azonnal érvénybe a frissítés után](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [A hitelesítési adatok nem a megfelelő formátumban vannak megadva a közvetlen REST API hívásokban](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Miért nem sorolja fel a Azure Portal az összes saját tárházat vagy címkét?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Miért nem sikerül beolvasni a Azure Portal adattárakat vagy címkéket?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Miért sikertelen a lekéréses vagy leküldéses kérelem a nem engedélyezett művelettel?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [A tárház formátuma érvénytelen vagy nem támogatott](#repository-format-is-invalid-or-unsupported)
- [A Hogyan http-nyomkövetéseket gyűjt a Windows rendszeren?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Állapot-ellenõrzés `az acr check-health`

A gyakori környezet-és beállításjegyzék-problémák megoldásához tekintse meg [Az Azure Container Registry állapotának ellenőrzését](container-registry-check-health.md)ismertető témakört.

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>a Docker-lekérés sikertelen a következő hibával: net/http: a kérelem megszakadt a kapcsolatra való várakozás közben (az ügyfél. időtúllépési korlátja túllépte a fejléceket)

 - Ha ez a hiba átmeneti probléma, akkor az Újrapróbálkozás sikeres lesz.
 - Ha `docker pull` a művelet folyamatosan meghiúsul, akkor probléma lehet a Docker-démonsal. A probléma általában a Docker-démon újraindításával csökkenthető. 
 - Ha továbbra is ezt a problémát látja a Docker-démon újraindítása után, akkor előfordulhat, hogy a probléma a gép hálózati kapcsolatával kapcsolatos problémába ütközik. Annak ellenőrzéséhez, hogy a gép általános hálózata kifogástalan állapotú-e, futtassa a következő parancsot a végponti kapcsolat teszteléséhez. A `az acr` kapcsolat-ellenőrzési parancsot tartalmazó minimális verzió 2.2.9. Ha régebbi verziót használ, frissítse az Azure CLI-t.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Mindig legyen újrapróbálkozási mechanizmus az összes Docker-ügyfél-művelethez.

### <a name="docker-pull-is-slow"></a>A Docker-lekérés lassú
[Ezzel](http://www.azurespeed.com/Azure/Download) az eszközzel tesztelheti a gép hálózati letöltési sebességét. Ha a gépi hálózat lassú, érdemes lehet az Azure-beli virtuális gépet a beállításjegyzékével megegyező régióban használni. Ez általában gyorsabb hálózati sebességet biztosít.

### <a name="docker-push-is-slow"></a>A Docker leküldése lassú
[Ezzel](http://www.azurespeed.com/Azure/Upload) az eszközzel tesztelheti a gép hálózati feltöltési sebességét. Ha a gépi hálózat lassú, érdemes lehet az Azure-beli virtuális gépet a beállításjegyzékével megegyező régióban használni. Ez általában gyorsabb hálózati sebességet biztosít.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>A Docker leküldése sikeres, de a Docker-lekérés sikertelen a következő hibával: jogosulatlan: hitelesítés szükséges

Ez a hiba a Docker-démon Red Hat verziójával is megtörténhet, ahol `--signature-verification` alapértelmezés szerint engedélyezve van a beállítás. A következő parancs futtatásával tekintheti meg Red Hat Enterprise Linux (RHEL) vagy Fedora Docker-démoni beállításait:

```bash
grep OPTIONS /etc/sysconfig/docker
```

A Fedora 28 kiszolgáló például a következő Docker-démoni beállításokkal rendelkezik:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

A `--signature-verification=false` hiányzó `docker pull` művelettel a következőhöz hasonló hibaüzenet jelenik meg:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

A hiba elhárítása:
1. Adja hozzá a kapcsolót `--signature-verification=false` a Docker-démon konfigurációs fájljához `/etc/sysconfig/docker` . Például:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Indítsa újra a Docker Daemon szolgáltatást a következő parancs futtatásával:
   
   ```bash
   sudo systemctl restart docker.service
   ```

A részletes adatai a `--signature-verification` futtatásával találhatók `man dockerd` .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az ACR login sikeres, de a Docker sikertelen a következő hibával: jogosulatlan: hitelesítés szükséges

Ügyeljen arra, hogy az összes kisbetűs kiszolgáló URL-címét használja, például `docker push myregistry.azurecr.io/myimage:latest` akkor is, ha a beállításjegyzék-erőforrás neve nagybetűs vagy kevert eset, például: `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>A Docker-démon hibakeresési naplóinak engedélyezése és lekérése    

Kezdje `dockerd` a `debug` kapcsolóval. Először hozza létre a Docker-démon konfigurációs fájlját ( `/etc/docker/daemon.json` ), ha az nem létezik, és adja hozzá a következő `debug` lehetőséget:

```json
{    
    "debug": true    
}
```

Ezután indítsa újra a démont. Például Ubuntu 14,04 esetén:

```bash
sudo service docker restart
```

A részletek a [Docker dokumentációjában](https://docs.docker.com/engine/admin/#enable-debugging)találhatók.    

 * A naplók a rendszertől függően különböző helyekről hozhatók létre. Például Ubuntu 14,04 esetén ez a következő: `/var/log/upstart/docker.log` .    
Részletekért lásd a [Docker dokumentációját](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * A Windows rendszerhez készült Docker esetében a naplók a (z)% LOCALAPPDATA%/Docker/. alatt jönnek létre. Előfordulhat azonban, hogy még nem tartalmazza az összes hibakeresési információt.    

   A teljes démoni napló eléréséhez szükség lehet néhány további lépésre:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Most már hozzáférhet a-t futtató virtuális gép összes fájljához `dockerd` . A napló a következő helyen található: `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Előfordulhat, hogy az új felhasználói engedélyek nem lépnek azonnal érvénybe a frissítés után

Ha új engedélyeket (új szerepköröket) ad meg egy egyszerű szolgáltatáshoz, előfordulhat, hogy a módosítás azonnal érvénybe lép. Két lehetséges oka van:

* Azure Active Directory a szerepkör-hozzárendelés késleltetését. Általában gyors, de a terjesztési késleltetés miatt percek alatt eltarthat.
* Engedély késleltetése az ACR-jogkivonat-kiszolgálón. Ez akár 10 percet is igénybe vehet. A megoldáshoz `docker logout` 1 perc múlva ugyanazzal a felhasználóval is elvégezheti a hitelesítést.

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Az ACR jelenleg nem támogatja a felhasználók által végrehajtott otthoni replikálást. A megkerülő megoldás az, hogy belefoglalja a Kezdőlap replikációját a sablonba, de kihagyhatja a létrehozását az `"condition": false` alább látható módon:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>A hitelesítési adatok nem a megfelelő formátumban vannak megadva a közvetlen REST API hívásokban

Előfordulhat `InvalidAuthenticationInfo` , hogy hiba történt, különösen az `curl` eszközt használja a `-L` `--location` (átirányítások követése) lehetőséggel.
Például a blob beolvasása a paranccsal `curl` `-L` és az alapszintű hitelesítéssel:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

a következő választ eredményezheti:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Az alapvető ok az, hogy egyes `curl` implementációk az eredeti kérelem fejléceit követik.

A probléma megoldásához a fejlécek nélkül manuálisan kell átirányítani az átirányítást. Nyomtassa ki a válasz fejléceit a (z) és a (z) `-D -` `curl` kibontásával: a `Location` fejlécet:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Miért nem sorolja fel a Azure Portal az összes saját tárházat vagy címkét? 

Ha a Microsoft Edge/IE böngészőt használja, legfeljebb 100 adattárat vagy címkét láthat. Ha a beállításjegyzék több mint 100 adattárat vagy címkét tartalmaz, javasoljuk, hogy a Firefox vagy a Chrome böngészőt használja az összes elem listázásához.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Miért nem sikerül beolvasni a Azure Portal adattárakat vagy címkéket?

Előfordulhat, hogy a böngésző nem tudja elküldeni a tárolók vagy címkék kiszolgálónak való beolvasására vonatkozó kérést. Többek között a következők lehetnek:

* Hálózati kapcsolat hiánya
* Firewall
* Ad-blokkolók
* DNS-hibák

Forduljon a hálózati rendszergazdához, vagy ellenőrizze a hálózati konfigurációt és a kapcsolatot. Próbálja meg futtatni az `az acr check-health -n yourRegistry` Azure CLI-vel, és ellenőrizze, hogy a környezet képes-e kapcsolódni a Container Registryhoz. Emellett a böngészőben egy inkognitóban-vagy privát munkamenetet is kipróbálhat, hogy elkerülje az elavult böngésző gyorsítótárának vagy cookie-jait.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Miért sikertelen a lekéréses vagy leküldéses kérelem a nem engedélyezett művelettel?

Íme néhány forgatókönyv, ahol a műveletek nem lehetnek engedélyezve:
* A klasszikus kibocsátásiegység-forgalmi jegyzékek már nem támogatottak. Frissítsen egy támogatott [szolgáltatási szintre](./container-registry-skus.md) az [az ACR update](/cli/azure/acr#az-acr-update) vagy a Azure Portal használatával.
* Előfordulhat, hogy a rendszerkép vagy a tárház zárolva van, ezért nem törölhető vagy nem frissíthető. A jelenlegi attribútumok megtekintéséhez használja az az [ACR show adattár](./container-registry-image-lock.md) parancsot.
* Néhány művelet nem engedélyezett, ha a rendszerkép karanténban van. További információ a [karanténba helyezésről](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* Lehetséges, hogy a beállításjegyzék elérte a [tárolási korlátot](container-registry-skus.md#service-tier-features-and-limits).

### <a name="repository-format-is-invalid-or-unsupported"></a>A tárház formátuma érvénytelen vagy nem támogatott

Ha olyan hibaüzenetet lát, mint például a "nem támogatott adattár formátuma", "érvénytelen formátum" vagy "a kért adatok nem léteznek", amikor az adattár nevét adja meg a tárház műveleteiben, ellenőrizze a név helyesírását és az esetét. Az érvényes Tárházak neve csak kisbetűs alfanumerikus karaktereket, pontokat, kötőjeleket, aláhúzásokat és perjeleket tartalmazhat. 

A Tárházak elnevezési szabályainak elvégzéséhez tekintse [meg a nyílt tároló kezdeményezésének terjesztési specifikációját](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>A Hogyan http-nyomkövetéseket gyűjt a Windows rendszeren?

#### <a name="prerequisites"></a>Előfeltételek

- Https-visszafejtés engedélyezése a hegedűsben:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Engedélyezze a Docker számára a proxy használatát a Docker felhasználói felületén: <https://docs.docker.com/docker-for-windows/#proxies>
- Győződjön meg arról, hogy a befejezés után visszaállnak.  A Docker nem működik ezzel az engedélyezve és a Hegedűs nem fut.

#### <a name="windows-containers"></a>Windows-tárolók

A Docker proxy konfigurálása a 127.0.0.1-re: 8888

#### <a name="linux-containers"></a>Linux-tárolók

A Docker VM virtuális kapcsoló IP-címének megkeresése:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurálja a Docker-proxyt az előző parancs és a 8888-es port kimenetére (például 10.0.75.1:8888).

## <a name="tasks"></a>Feladatok

- [Hogyan batch-megszakítást futtat?](#how-do-i-batch-cancel-runs)
- [Hogyan tartalmazza a. git mappát az az ACR Build paranccsal?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Támogatja a feladatok a GitLab a forrás-eseményindítók esetében?](#does-tasks-support-gitlab-for-source-triggers)
- [Milyen git-tárház-kezelési szolgáltatást támogat a feladatok?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hogyan batch-megszakítást futtat?

A következő parancsok megszakítják az összes futó feladatot a megadott beállításjegyzékben.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hogyan tartalmazza a. git mappát az az ACR Build paranccsal?

Ha a parancshoz átadja a helyi forrás mappáját `az acr build` , a `.git` rendszer alapértelmezés szerint kizárja a mappát a feltöltött csomagból. `.dockerignore`A következő beállítással hozhat létre fájlt. Azt jelzi, hogy a parancs a feltöltött csomagban található összes fájlt visszaállítja `.git` . 

`!.git/**`

Ez a beállítás a `az acr run` parancsra is érvényes.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Támogatja a feladatok a GitLab a forrás-eseményindítók esetében?

Jelenleg nem támogatjuk a GitLab a forrás-eseményindítók esetében.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Milyen git-tárház-kezelési szolgáltatást támogat a feladatok?

| Git szolgáltatás | Forrás kontextus | Manuális létrehozás | Automatikus létrehozás a commit trigger használatával |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Igen | Igen |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Igen | Igen |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Igen | Nem |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Igen | Nem |

## <a name="run-error-message-troubleshooting"></a>Hibaüzenetek futtatása – hibaelhárítás

| Hibaüzenet | Hibaelhárítási útmutató |
|---|---|
|Nincs hozzáférés konfigurálva a virtuális géphez, ezért nem találhatók előfizetések|Ez akkor fordulhat elő, ha az `az login --identity` ACR-feladatban használja. Ez egy átmeneti hiba, amely akkor fordul elő, ha a felügyelt identitás szerepkör-hozzárendelése nem lett propagálva. Várakozás néhány másodpercre az újrapróbálkozások előtt.|

## <a name="cicd-integration"></a>CI/CD-integráció

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Következő lépések

* [További](container-registry-intro.md) információ a Azure Container Registryról.
