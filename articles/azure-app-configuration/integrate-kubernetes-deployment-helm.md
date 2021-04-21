---
title: Integráció Azure App Configuration Kubernetes üzemelő példányával a Helm használatával
description: Megtudhatja, hogyan használhatja a dinamikus konfigurációkat a Kubernetes üzembe helyezésében a Helm használatával.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 6276fc2027e92d5b7baaf9237a928e7828a3b021
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775764"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrálás a Kubernetes üzemelő példányával a Helm használatával

A Helm segítségével definiálhatja, telepítheti és frissítheti a Kubernetesben futó alkalmazásokat. A Helm-diagramok a Kubernetes-alkalmazások példányának létrehozásához szükséges információkat tartalmaznak. A konfiguráció a diagramon kívül, a *values.yaml* nevű fájlban van tárolva. 

A kiadási folyamat során a Helm egyesíti a diagramot az alkalmazás futtatásához szükséges megfelelő konfigurációval. Például a *values.yaml* fájlban definiált változókra környezeti változókként lehet hivatkozni a futó tárolókban. A Helm támogatja a Kubernetes titkos kulcsok létrehozását is, amelyek adatkötetként csatlakoztathatóak vagy környezeti változókként tehetőek elérhetővé.

A *values.yaml* fájlban tárolt értékeket felülírhatja, ha további YAML-alapú konfigurációs fájlokat ad meg a parancssorban a Helm futtatásakor. Azure App Configuration támogatja a konfigurációs értékek YAML-fájlokba exportálását. Az exportálási képesség üzembe helyezésbe való integrálása lehetővé teszi, hogy a Kubernetes-alkalmazások kihasználják a környezetben tárolt App Configuration.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az alkalmazás Kubernetesben App Configuration a Helm használatával való üzembe helyezésekor használja a virtuális gép értékeit.
> * Hozzon létre egy Kubernetes Titkos referenciát a Key Vault hivatkozása App Configuration.

Ez az oktatóanyag feltételezi a Kubernetes Helm-sel való kezelésének alapvető ismereteit. További információ az alkalmazások Helm-sel való telepítéséről [a Azure Kubernetes Service.](../aks/kubernetes-helm.md)

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Az [Azure CLI telepítése](/cli/azure/install-azure-cli) (2.4.0-s vagy újabb verzió)
- A [Helm](https://helm.sh/docs/intro/install/) telepítése (2.14.0-s vagy újabb verzió)
- Egy Kubernetes-fürt.

## <a name="create-an-app-configuration-store"></a>Új App Configuration létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Válassza **a Configuration Explorer**  >  **Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | settings.color | Fehér |
    | settings.message | Adatok a Azure App Configuration |

    A **Címke és** a **Tartalomtípus mezőt** hagyja üresen.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Hivatkozás Key Vault a App Configuration
1. Jelentkezzen be a Azure Portal, és adjon [](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) hozzá egy titkos  Key Vault jelszóval és **myPassword értékkel.** [](https://portal.azure.com) 
2. Válassza ki App Configuration előző szakaszban létrehozott tárolópéldányt.

3. Válassza a **Configuration Explorer lehetőséget.**

4. Válassza **a +**  >  **Kulcstartó-referencia létrehozása lehetőséget,** majd adja meg a következő értékeket:
    - **Kulcs:** Válassza ki **a secrets.password et.**
    - **Címke:** Ezt az értéket hagyja üresen.
    - **Előfizetés,** **Erőforráscsoport** és **Kulcstartó:** Adja meg az előző lépésben létrehozott kulcstartóban megadott értékeknek megfelelő értékeket.
    - **Titkos:** Válassza ki az előző szakaszban létrehozott **Password** (Jelszó) nevű titkos jelszót.

## <a name="create-helm-chart"></a>Helm-diagram létrehozása ##
Először hozzon létre egy Helm-mintadiagramot a következő paranccsal
```console
helm create mychart
```

A Helm létrehoz egy mychart nevű új könyvtárat az alább látható struktúrával. 

> [!TIP]
> További [információért kövesse ezt](https://helm.sh/docs/chart_template_guide/getting_started/) a diagramok útmutatóját.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Ezután frissítse a *deployment.yaml* fájl **spec:template:spec:containers** szakaszát. Az alábbi kódrészlet két környezeti változót ad hozzá a tárolóhoz. Az értékeiket az üzembe helyezéskor dinamikusan fogja beállítani.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

A frissítés *után a teljes deployment.yaml* fájlnak az alábbihoz hasonlónak kell lennie.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Ha bizalmas adatokat szeretne Kubernetes titkos kulcsokként tárolni, adjon hozzá egy *secrets.yaml fájlt* a templates mappához.

> [!TIP]
> További információ a [Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)titkos kulcsok használatával kapcsolatban.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Végül frissítse a *values.yaml* fájlt a következő tartalommal, hogy az alapértelmezett értékeket adja meg a konfigurációs beállítások és titkos kulcsok számára, amelyekre a *deployment.yaml* és *secrets.yaml* fájl hivatkozik. A tényleges értékeiket a rendszer felülírja a kiszolgálóról lekért App Configuration.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Konfiguráció pass configuration from App Configuration in Helm install ##
Először töltse le a konfigurációt a App Configuration egy *myConfig.yaml fájlba.* Kulcsszűrővel csak azokat a kulcsokat töltse le, amelyek a beállításokkal **kezdődnek.**. Ha ebben az esetben a kulcsszűrő nem elegendő a Key Vault-hivatkozások kulcsának kizárására, a **--skip-keyvault argumentum** használatával kizárhatja őket. 

> [!TIP]
> További információ az [exportálási parancsról:](/cli/azure/appconfig/kv#az_appconfig_kv_export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Ezután töltse le a titkos okat a *mySecrets.yaml nevű fájlba.* A **--resolve-keyvault** parancssori argumentum úgy oldja fel a hivatkozásokat Key Vault hogy lekérte a tényleges értékeket a Key Vault. Ezt a parancsot olyan hitelesítő adatokkal kell futtatnia, amelyek megfelelő hozzáférési engedélyekkel Key Vault.

> [!WARNING]
> Mivel ez a fájl bizalmas adatokat tartalmaz, tartsa gondban a fájlt, és ha már nincs rá szükség, ne takarítsa meg.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

A Helm-frissítés **-f argumentumának** használatával adja át a két létrehozott konfigurációs fájlt. Felülbírálják a *values.yaml* fájlban definiált konfigurációs értékeket a kódból exportált App Configuration.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

A Helm-frissítés **--set** argumentumát is használhatja konstanskulcs-értékek áterítésére. A **--set argumentum** használatával elkerülheti a bizalmas adatok lemezen való megőrzését. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Ellenőrizze, hogy a konfigurációk és titkos kulcsok sikeresen be vannak-e állítva a [Kubernetes-irányítópult eléréséhez.](../aks/kubernetes-dashboard.md) Látni fogja, hogy  a  tároló szín- App Configuration üzenetértékei fel vannak töltve a tároló környezeti változóiba.

![Gyors üzembe helyezési alkalmazás helyi indítása](./media/kubernetes-dashboard-env-variables.png)

A kubernetes **titkos kulcsokhoz** Key Vault titkos App Configuration egy titkos adatokat is hozzáadtunk. 

![Képernyőkép az Adatok szakaszban kiemelt jelszóról.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban exportálta a Azure App Configuration adatokat, hogy a Helm segítségével kubernetes-környezetben használhatók legyen. Ha többet szeretne megtudni a App Configuration, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)
