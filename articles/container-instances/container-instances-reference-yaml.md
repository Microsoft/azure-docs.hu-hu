---
title: YAML-referencia tárolócsoporthoz
description: A tárolócsoport konfigurálási Azure Container Instances YAML-fájl referenciája
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: efbea7b64ccdf685d4c82bd406f2aa09227e53e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771136"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-referencia: Azure Container Instances

Ez a cikk a tárolócsoport konfigurálásán Azure Container Instances YAML-fájl szintaxisát [és tulajdonságait tartalmazza.](container-instances-container-groups.md) YaML-fájl használatával adja meg a csoportkonfigurációt az [az container create][az-container-create] parancshoz az Azure CLI-ben. 

A YAML-fájlokkal kényelmesen konfigurálható egy tárolócsoport reprodukálható üzembe helyezéshez. Ez egy tömör alternatíva a tárolócsoport Resource Manager vagy [a](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) Azure Container Instances DK-k használata egy tárolócsoport létrehozásához vagy frissítéséhez.

> [!NOTE]
> Ez a referencia a következő verziójú YAML Azure Container Instances REST API `2019-12-01` vonatkozik.

## <a name="schema"></a>Séma 

A YAML-fájl sémája következik, a kulcstulajdonságok kiemelése megjegyzésekkel együtt. A sémában található tulajdonságok leírását a Tulajdonságértékek [szakaszban](#property-values) láthatja.

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

Az alábbi táblázatok a sémában beállítatott értékeket ismertetik.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A tárolócsoport neve. |
|  apiVersion | Enum | Yes | 2018-10-01 |
|  location | sztring | No | Az erőforrás helye. |
|  tags | object | No | Az erőforráscímkék. |
|  identity | object | No | A tárolócsoport identitása, ha konfigurálva van. - [ContainerGroupIdentity objektum](#containergroupidentity-object) |
|  properties | object | Yes | [ContainerGroupProperties objektum](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  típus | Enum | No | A tárolócsoporthoz használt identitás típusa. A "SystemAssigned, UserAssigned" típus implicit módon létrehozott identitást és felhasználó által hozzárendelt identitásokat is tartalmaz. A "Nincs" típus eltávolítja az identitásokat a tárolócsoportból. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities (userAssignedIdentities) | object | No | A tárolócsoporthoz társított felhasználói identitások listája. A felhasználói identitás szótárkulcs-hivatkozásai Azure Resource Manager következő formában lesznek erőforrás-azonosítók: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  tárolók | array | Yes | A tárolócsoportban található tárolók. - [Tárolóobjektum](#container-object) |
|  imageRegistryCredentials | array | No | A rendszerkép-beállításjegyzék hitelesítő adatai, amelyek alapján a tárolócsoport létrejön. - [ImageRegistryCredential objektum](#imageregistrycredential-object) |
|  restartPolicy | Enum | No | Indítsa újra a szabályzatot a tárolócsoportban található összes tárolóra. - `Always` Mindig újraindítás – `OnFailure` Újraindítás hiba esetén – Soha ne `Never` indulja újra. - Always, OnFailure, Never |
|  ipAddress (Ip-cím) | object | No | A tárolócsoport IP-címének típusa. - [IpAddress objektum](#ipaddress-object) |
|  osType (osType) | Enum | Yes | A tárolócsoportban található tárolók által megkövetelt operációsrendszer-típus. - Windows vagy Linux |
|  volumes | array | No | Az ebben a tárolócsoportban található tárolók által csatlakoztatható kötetek listája. - [Kötetobjektum](#volume-object) |
|  diagnosztika | object | No | A tárolócsoport diagnosztikai adatai. - [ContainerGroupDiagnostics objektum](#containergroupdiagnostics-object) |
|  networkProfile | object | No | A tárolócsoport hálózati profilinformációi. - [ContainerGroupNetworkProfile objektum](#containergroupnetworkprofile-object) |
|  dnsConfig | object | No | A tárolócsoport DNS-konfigurációs információi. - [DnsConfiguration objektum](#dnsconfiguration-object) |
| Sku | Enum | No | Tárolócsoport termékváltozata – Standard vagy Dedikált |
| encryptionProperties (titkosítási beállítások) | object | No | A tárolócsoport titkosítási tulajdonságai. - [EncryptionProperties objektum](#encryptionproperties-object) | 
| initContainers (initContainers) | array | No | A tárolócsoport init tárolói. - [InitContainerDefinition objektum](#initcontainerdefinition-object) |




### <a name="container-object"></a>Tárolóobjektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A tárolópéldány felhasználó által megadott neve. |
|  properties | object | Yes | A tárolópéldány tulajdonságai. - [ContainerProperties objektum](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  kiszolgáló | sztring | Yes | A Docker-rendszerkép beállításjegyzék-kiszolgálója, amely nem használ olyan protokollt, mint a "http" és a "https". |
|  username | sztring | Yes | A privát regisztrációs adatbázis felhasználóneve. |
|  jelszó | sztring | No | A privát beállításjegyzék jelszava. |




### <a name="ipaddress-object"></a>IpAddress objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  ports | array | Yes | A tárolócsoporton elérhető portok listája. - [Portobjektum](#port-object) |
|  típus | Enum | Yes | Megadja, hogy az IP-cím elérhető-e a nyilvános interneten vagy a magánhálózati virtuális hálózaton. - Nyilvános vagy Privát |
|  Ip | sztring | No | A nyilvános interneten elérhető ip-cím. |
|  dnsNameLabel | sztring | No | Az IP-cím DNS-névcímkéje. |




### <a name="volume-object"></a>Kötetobjektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A kötet neve. |
|  azureFile | object | No | Az Azure File kötete. - [AzureFileVolume objektum](#azurefilevolume-object) |
|  emptyDir | object | No | Az üres könyvtárkötet. |
|  titkos kód | object | No | A titkos kötet. |
|  gitRepo | object | No | A Git-adattár kötete. - [GitRepoVolume objektum](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | Tárolócsoport naplóelemzési adatai. - [LogAnalytics objektum](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  id | sztring | Yes | A hálózati profil azonosítója. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Névszervereket | array | Yes | A tárolócsoport DNS-kiszolgálói. – sztring |
|  searchDomains (Tartománykeresés) | sztring | No | A tárolócsoportban található gazdagépnév-keresés DNS-keresési tartománya. |
|  Lehetőségek | sztring | No | A tárolócsoport DNS-beállításai. |


### <a name="encryptionproperties-object"></a>EncryptionProperties objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | sztring    | Yes   | A keyvault alap URL-címe. |
| Kulcsnév   | sztring    | Yes   | A titkosítási kulcs neve. |
| keyVersion    | sztring    | Yes   | A titkosítási kulcs verziója. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| name  | sztring |  Yes | Az init tároló neve. |
| properties    | object    | Yes   | Az init tároló tulajdonságai. - [InitContainerPropertiesDefinition objektum](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  image | sztring | Yes | A tárolópéldány létrehozásához használt rendszerkép neve. |
|  command | array | No | A tárolópéldányon belül végrehajtható parancsok exec formában. - sztring |
|  ports | array | No | A tárolópéldányon elérhetővé tért portok. - [ContainerPort objektum](#containerport-object) |
|  environmentVariables (környezeti változók) | array | No | A tárolópéldányban beállítható környezeti változók. - [EnvironmentVariable objektum](#environmentvariable-object) |
|  resources | object | Yes | A tárolópéldány erőforrás-követelményei. - [ResourceRequirements objektum](#resourcerequirements-object) |
|  volumeMounts | array | No | A kötet a tárolópéldány számára elérhető. - [VolumeMount objektum](#volumemount-object) |
|  livenessProbe | object | No | Az élőség-mintavétel. - [ContainerProbe objektum](#containerprobe-object) |
|  readinessProbe | object | No | A készenlét-mintavétel. - [ContainerProbe objektum](#containerprobe-object) |




### <a name="port-object"></a>Portobjektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Protokoll | Enum | No | A porthoz társított protokoll. - TCP vagy UDP |
|  port | egész szám | Yes | A portszám. |




### <a name="azurefilevolume-object"></a>AzureFileVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  shareName (megosztás neve) | sztring | Yes | A kötetként csatlakoztatni kívánt Azure-fájlmegosztás neve. |
|  readOnly (csak olvasható) | boolean | No | A jelölő, amely azt jelzi, hogy az Azure-fájl kötetként van-e csatlakoztatva. |
|  storageAccountName | sztring | Yes | Az Azure-fájlmegosztást tartalmazó tárfiók neve. |
|  storageAccountKey | sztring | No | Az Azure-fájlmegosztás eléréséhez használt tárfiók-hozzáférési kulcs. |




### <a name="gitrepovolume-object"></a>GitRepoVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  címtár | sztring | No | Célkönyvtár neve. Nem tartalmazhatja a "."-t, és nem kezdődnie kell a következővel: .  Ha a "." van megadva, a kötet könyvtára lesz a Git-adattár.  Ellenkező esetben, ha meg van adva, a kötet fogja tartalmazni a git-adattárat az alkönyvtárban a megadott névvel. |
|  repository | sztring | Yes | Adattár URL-címe |
|  változat | sztring | No | Véglegesítési kivonat a megadott változathoz. |



### <a name="loganalytics-object"></a>LogAnalytics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sztring | Yes | A Log Analytics munkaterület-azonosítója |
|  workspaceKey (munkaterületkulcs) | sztring | Yes | A Log Analytics munkaterületi kulcsa |
|  logType (logType) | Enum | No | A használni fog naplótípus. - ContainerInsights vagy ContainerInstanceLogs |
|  metaadatok | object | No | Metaadatok a log analyticshez. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| image | sztring    | No    | Az init tároló képe. |
| command   | array | No    | Az init tárolón belül végrehajtható parancs exec formában. - sztring |
| environmentVariables (környezeti változók) | array  | No |Az init tárolóban beállítható környezeti változók. - [EnvironmentVariable objektum](#environmentvariable-object)
| volumeMounts |array   | No    | A kötet csatlakoztatva van az init tárolóhoz. - [VolumeMount objektum](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Protokoll | Enum | No | A porthoz társított protokoll. - TCP vagy UDP |
|  port | egész szám | Yes | A tárolócsoporton belül elérhetővé tért portszám. |




### <a name="environmentvariable-object"></a>EnvironmentVariable objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A környezeti változó neve. |
|  érték | sztring | No | A környezeti változó értéke. |
|  secureValue (biztonságos érték) | sztring | No | A biztonságos környezeti változó értéke. |




### <a name="resourcerequirements-object"></a>ResourceRequirements objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Kérelmek | object | Yes | A tárolópéldány erőforrás-kérései. - [ResourceRequests objektum](#resourcerequests-object) |
|  Határok | object | No | A tárolópéldány erőforráskorlátja. - [ResourceLimits objektum](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Yes | A kötet csatlakoztatásának neve. |
|  mountPath | sztring | Yes | A tárolón belüli elérési út, ahová a kötetet csatlakoztatni kell. Nem tartalmazhat kettőspontot (:). |
|  readOnly (csak olvasható) | boolean | No | A jelölő, amely azt jelzi, hogy a kötet csatlakoztatása csak olvasható-e. |




### <a name="containerprobe-object"></a>ContainerProbe objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Exec | object | No | A mintavételre szolgáló végrehajtási parancs [– ContainerExec objektum](#containerexec-object) |
|  httpGet | object | No | A mintavétel HTTP Get-beállításai – [ContainerHttpGet objektum](#containerhttpget-object) |
|  initialDelaySeconds (initialDelaySeconds) | egész szám | No | A kezdeti késleltetés másodpercben. |
|  periodSeconds (periodSeconds) | egész szám | No | Az időszak másodpercben. |
|  failureThreshold | egész szám | No | A hiba küszöbértéke. |
|  successThreshold | egész szám | No | A sikeresség küszöbértéke. |
|  timeoutSeconds (időtúllépési másodpercek) | egész szám | No | Az időkorlát másodpercben. |




### <a name="resourcerequests-object"></a>ResourceRequests objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | Yes | A tárolópéldány memóriakérése GB-ban. |
|  Cpu | szám | Yes | A tárolópéldány CPU-kérése. |
|  Gpu | object | No | A tárolópéldány GPU-kérése. - [GpuResource objektum](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | No | A tárolópéldány memóriakorlátja GB-ban. |
|  Cpu | szám | No | A tárolópéldány CPU-korlátja. |
|  Gpu | object | No | A tárolópéldány GPU-korlátja. - [GpuResource objektum](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  command | array | No | A tárolón belül végrehajtható parancsok. – sztring |




### <a name="containerhttpget-object"></a>ContainerHttpGet objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  path | sztring | No | A mintavétel elérési útja. |
|  port | egész szám | Yes | A mintavételhez használnia kell a portszámot. |
|  Rendszer | Enum | No | A séma. - http vagy https |




### <a name="gpuresource-object"></a>GpuResource objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  count | egész szám | Yes | A GPU-erőforrás száma. |
|  Sku | Enum | Yes | A GPU-erőforrás termékváltozata. – K80, P100, V100 |


## <a name="next-steps"></a>Következő lépések

Lásd: Többtárolós csoport [üzembe helyezése YAML-fájllal.](container-instances-multi-container-yaml.md)

Példák arra, hogyan lehet YAML-fájl használatával tárolócsoportokat üzembe helyezni egy [virtuális hálózatban,](container-instances-vnet.md) vagy amelyek [egy külső kötetet csatlakoztatnak.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
