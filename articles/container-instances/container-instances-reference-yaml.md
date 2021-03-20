---
title: YAML-hivatkozás a tároló csoportjához
description: A Azure Container Instances által támogatott YAML-fájlra mutató hivatkozás egy tároló csoport konfigurálásához
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87084760"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-hivatkozás: Azure Container Instances

Ez a cikk az Azure Container Instances által támogatott YAML-fájl szintaxisát és tulajdonságait ismerteti egy [tároló csoport](container-instances-container-groups.md)konfigurálásához. YAML-fájl használatával adja meg a csoport konfigurációját az Azure CLI-ben az az [Container Create][az-container-create] paranccsal. 

A YAML-fájlok kényelmes módot biztosítanak egy tároló csoport konfigurálására a reprodukálható központi telepítésekhez. Ez egy rövid alternatíva egy [Resource Manager-sablon](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) vagy a Azure Container instances SDK-k használatával egy tároló csoport létrehozásához vagy frissítéséhez.

> [!NOTE]
> Ez a hivatkozás a Azure Container Instances REST API verziójának YAML-fájljaira vonatkozik `2019-12-01` .

## <a name="schema"></a>Séma 

A YAML-fájl sémája a következőt követi, beleértve a legfontosabb tulajdonságok kiemeléséhez szükséges megjegyzéseket is. A séma tulajdonságainak leírását a [tulajdonságértékek](#property-values) című szakaszban találja.

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Tulajdonságértékek

A következő táblázatok ismertetik a sémában beállítani kívánt értékeket.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. ContainerInstance/containerGroups objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A tároló csoport neve. |
|  apiVersion | Enum | Yes | 2018-10-01 |
|  location | sztring | No | Az erőforrás helye. |
|  tags | object | No | Az erőforrás-címkék. |
|  identity | object | No | A tároló csoport identitása, ha be van állítva. - [ContainerGroupIdentity objektum](#containergroupidentity-object) |
|  properties | object | Yes | [ContainerGroupProperties objektum](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  típus | Enum | No | A tároló csoporthoz használt identitás típusa A "SystemAssigned, UserAssigned" típus egy implicit módon létrehozott identitást és egy felhasználó által hozzárendelt identitást is tartalmaz. A "None" típus eltávolítja az összes identitást a tároló csoportjából. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, none |
|  userAssignedIdentities | object | No | A tároló csoporthoz társított felhasználói identitások listája. A felhasználói azonosító szótár kulcsára vonatkozó hivatkozások a következő formában lesznek Azure Resource Manager erőforrás-azonosítók: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  tárolók | array | Yes | A tároló csoportban lévő tárolók. - [Tároló objektum](#container-object) |
|  imageRegistryCredentials | array | No | A rendszerkép beállításjegyzékbeli hitelesítő adatai, amelyek alapján a rendszer létrehozza a tároló csoportját. - [ImageRegistryCredential objektum](#imageregistrycredential-object) |
|  restartPolicy | Enum | No | A tároló csoportban lévő összes tárolóra vonatkozó újraindítási szabályzat. - `Always` Mindig újraindítás – `OnFailure` Újraindítás hiba esetén – `Never` Soha ne indítsa újra a rendszert. -Mindig, OnFailure, soha |
|  IP-cím | object | No | A tároló csoport IP-címének típusa. - [IP-cím objektum](#ipaddress-object) |
|  osType | Enum | Yes | A tároló csoportban lévő tárolók által igényelt operációs rendszer típusa. – Windows vagy Linux |
|  volumes | array | No | Azoknak a köteteknek a listája, amelyeket tárolók csatlakoztatnak a tároló csoportba. - [Kötet objektum](#volume-object) |
|  diagnosztika | object | No | Egy tároló csoport diagnosztikai adatai. - [ContainerGroupDiagnostics objektum](#containergroupdiagnostics-object) |
|  networkProfile | object | No | Egy tároló csoport hálózati profiljának adatai. - [ContainerGroupNetworkProfile objektum](#containergroupnetworkprofile-object) |
|  dnsConfig | object | No | Egy tároló csoport DNS-konfigurációs adatai. - [DnsConfiguration objektum](#dnsconfiguration-object) |
| SKU | Enum | No | A Container Group standard vagy dedikált SKU |
| encryptionProperties | object | No | Egy tároló csoport titkosítási tulajdonságai. - [EncryptionProperties objektum](#encryptionproperties-object) | 
| initContainers | array | No | A tárolók csoportjához tartozó init-tárolók. - [InitContainerDefinition objektum](#initcontainerdefinition-object) |




### <a name="container-object"></a>Tároló objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A tároló példány felhasználó által megadott neve. |
|  properties | object | Yes | A tároló példányának tulajdonságai. - [ContainerProperties objektum](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  kiszolgáló | sztring | Yes | A Docker-rendszerkép beállításjegyzék-kiszolgálója olyan protokoll nélkül, mint a "http" és a "https". |
|  username | sztring | Yes | A privát beállításjegyzék felhasználóneve. |
|  jelszó | sztring | No | A privát beállításjegyzék jelszava. |




### <a name="ipaddress-object"></a>IP-cím objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  ports | array | Yes | A tároló csoporton elérhető portok listája. - [Port objektum](#port-object) |
|  típus | Enum | Yes | Meghatározza, hogy az IP-cím elérhető-e a nyilvános internethez vagy a privát VNET. -Nyilvános vagy privát |
|  IP | sztring | No | A nyilvános internethez elérhető IP-cím. |
|  dnsNameLabel | sztring | No | Az IP-címhez tartozó DNS-név címkéje. |




### <a name="volume-object"></a>Kötet objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A kötet neve. |
|  azureFile | object | No | Az Azure-fájl kötete. - [AzureFileVolume objektum](#azurefilevolume-object) |
|  emptyDir | object | No | Az üres könyvtár kötete. |
|  titkos kód | object | No | A titkos kötet. |
|  gitRepo | object | No | A git-tárház kötete. - [GitRepoVolume objektum](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | A Container Group log Analytics információi. - [LogAnalytics objektum](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  id | sztring | Yes | Egy hálózati profil azonosítója. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Névkiszolgálók | array | Yes | A tároló csoport DNS-kiszolgálói. -string |
|  searchDomains | sztring | No | A DNS-keresési tartományok az állomásnév kereséséhez a tároló csoportban. |
|  beállítások | sztring | No | A tároló csoport DNS-beállításai. |


### <a name="encryptionproperties-object"></a>EncryptionProperties objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | sztring    | Yes   | A kulcstartó alap URL-címe. |
| Kulcsnév   | sztring    | Yes   | A titkosítási kulcs neve. |
| Verziószám    | sztring    | Yes   | A titkosítási kulcs verziója. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| name  | sztring |  Yes | Az init-tároló neve. |
| properties    | object    | Yes   | Az init-tároló tulajdonságai. - [InitContainerPropertiesDefinition objektum](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  image | sztring | Yes | A Container-példány létrehozásához használt rendszerkép neve. |
|  command | array | No | A tároló-példányon belül végrehajtandó parancsok az exec formában. -string |
|  ports | array | No | A tároló példányon elérhető portok. - [ContainerPort objektum](#containerport-object) |
|  environmentVariables | array | No | A tároló-példányban beállított környezeti változók. - [EnvironmentVariable objektum](#environmentvariable-object) |
|  resources | object | Yes | A Container-példány erőforrás-követelményei. - [ResourceRequirements objektum](#resourcerequirements-object) |
|  volumeMounts | array | No | A tároló példány számára elérhető kötetek csatlakoztatása. - [VolumeMount objektum](#volumemount-object) |
|  livenessProbe | object | No | Az élettartam-mintavétel. - [ContainerProbe objektum](#containerprobe-object) |
|  readinessProbe | object | No | A készültségi mintavétel. - [ContainerProbe objektum](#containerprobe-object) |




### <a name="port-object"></a>Port objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  protokoll | Enum | No | A porthoz társított protokoll. -TCP vagy UDP |
|  port | egész szám | Yes | A portszám. |




### <a name="azurefilevolume-object"></a>AzureFileVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  shareName | sztring | Yes | A kötetként csatlakoztatni kívánt Azure-fájlmegosztás neve. |
|  readOnly | boolean | No | Az a jelző, amely azt jelzi, hogy a kötetként csatlakoztatott Azure-fájl írásvédett-e. |
|  storageAccountName | sztring | Yes | Az Azure-fájlmegosztást tartalmazó Storage-fiók neve. |
|  storageAccountKey | sztring | No | Az Azure-fájlmegosztás eléréséhez használt Storage-fiók elérési kulcsa. |




### <a name="gitrepovolume-object"></a>GitRepoVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  címtár | sztring | No | Célkönyvtár neve. A nem tartalmazhat ".." karaktert.  Ha a "." meg van adva, a kötet könyvtára lesz a git-tárház.  Ellenkező esetben, ha meg van adva, a kötet a megadott nevű alkönyvtárban található git-tárházat fogja tartalmazni. |
|  repository | sztring | Yes | Tárház URL-címe |
|  változat | sztring | No | A megadott változat kivonatának véglegesítve. |



### <a name="loganalytics-object"></a>LogAnalytics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sztring | Yes | A log Analytics-munkaterület azonosítója |
|  workspaceKey | sztring | Yes | A log Analytics-munkaterület kulcsa |
|  logType | Enum | No | A használandó naplózási típus. -ContainerInsights vagy ContainerInstanceLogs |
|  metaadatok | object | No | A log Analytics metaadatai. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| image | sztring    | No    | Az init-tároló képe. |
| command   | array | No    | Az init-tárolón belül végrehajtandó parancs exec formában. -string |
| environmentVariables | array  | No |Az init-tárolóban beállított környezeti változók. - [EnvironmentVariable objektum](#environmentvariable-object)
| volumeMounts |array   | No    | Az init-tároló számára elérhető kötetek csatlakoztatása. - [VolumeMount objektum](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  protokoll | Enum | No | A porthoz társított protokoll. -TCP vagy UDP |
|  port | egész szám | Yes | A tároló csoporton belül elérhető portszám. |




### <a name="environmentvariable-object"></a>EnvironmentVariable objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A környezeti változó neve. |
|  érték | sztring | No | A környezeti változó értéke. |
|  Securevalue paraméterét | sztring | No | A biztonságos környezeti változó értéke. |




### <a name="resourcerequirements-object"></a>ResourceRequirements objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  kérelmek | object | Yes | A tároló-példány erőforrás-kérései. - [ResourceRequests objektum](#resourcerequests-object) |
|  határértékeket | object | No | A tároló-példány erőforrás-korlátai. - [ResourceLimits objektum](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A kötet csatlakoztatásának neve. |
|  mountPath | sztring | Yes | Az elérési út a tárolón belül, ahol a kötetet csatlakoztatni kell. Nem tartalmazhat kettőspontot (:). |
|  readOnly | boolean | No | Az a jelző, amely azt jelzi, hogy a kötet csatlakoztatása írásvédett-e. |




### <a name="containerprobe-object"></a>ContainerProbe objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  exec | object | No | A [ContainerExec objektum](#containerexec-object) végrehajtási parancsa |
|  httpGet | object | No | A http-lekérési beállítások mintavételi [ContainerHttpGet objektum](#containerhttpget-object) |
|  initialDelaySeconds | egész szám | No | A kezdeti késleltetés másodpercben. |
|  periodSeconds | egész szám | No | Az időtartam másodpercben. |
|  failureThreshold | egész szám | No | A hiba küszöbértéke. |
|  successThreshold | egész szám | No | A siker küszöbértéke. |
|  timeoutSeconds | egész szám | No | Az időkorlát másodpercben. |




### <a name="resourcerequests-object"></a>ResourceRequests objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | Yes | A tároló példányának GB-ban megadott memória-kérelem. |
|  CPU | szám | Yes | A tároló-példány CPU-kérelme. |
|  GPU | object | No | A tároló-példány GPU-kérelme. - [GpuResource objektum](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | No | A tároló példány GB-ban megadott memória-korlátja. |
|  CPU | szám | No | A tároló-példány CPU-korlátja. |
|  GPU | object | No | A Container-példány GPU-korlátja. - [GpuResource objektum](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  command | array | No | A tárolón belül végrehajtandó parancsok. -string |




### <a name="containerhttpget-object"></a>ContainerHttpGet objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  path | sztring | No | A mintavétel elérési útja. |
|  port | egész szám | Yes | A mintavételhez használandó portszám. |
|  séma | Enum | No | A séma. -http vagy https |




### <a name="gpuresource-object"></a>GpuResource objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  count | egész szám | Yes | A GPU-erőforrás száma. |
|  SKU | Enum | Yes | A GPU-erőforrás SKU-jának. -K80, P100, V100 |


## <a name="next-steps"></a>Következő lépések

Tekintse meg a [többtárolós csoport üzembe helyezése YAML-fájl használatával](container-instances-multi-container-yaml.md)című oktatóanyagot.

Tekintse át a YAML-fájl használatával történő üzembe helyezési példákat egy [virtuális hálózatban](container-instances-vnet.md) , vagy [egy külső kötet csatlakoztatásához](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

