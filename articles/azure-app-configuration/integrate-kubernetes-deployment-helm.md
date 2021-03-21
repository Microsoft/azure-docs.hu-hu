---
title: Az Azure-alkalmazások konfigurációjának integrálása Kubernetes-telepítéssel a Helm használatával
description: Ismerje meg, hogyan használhatók a dinamikus konfigurációk a Kubernetes-telepítésben a Helm használatával.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 4e38366ddcee07f38ca390acf9d580b8764c1c00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979827"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrálás a Kubernetes üzembe helyezésével a Helm használatával

A Helm lehetővé teszi a Kubernetes-ben futó alkalmazások definiálását, telepítését és frissítését. A Helm diagram a Kubernetes-alkalmazások egy példányának létrehozásához szükséges információkat tartalmazza. A konfiguráció a diagramon kívül tárolódik, egy *Values. YAML* nevű fájlban. 

A kiadási folyamat során a Helm a megfelelő konfigurációval egyesíti a diagramot az alkalmazás futtatásához. Például az *Values. YAML* definiált változók környezeti változókként is szerepelhetnek a futó tárolókban. A Helm támogatja a Kubernetes-titkok létrehozását is, amelyek adatkötetként vagy környezeti változókként tehetők elérhetővé.

A *Values. YAML* tárolt értékek felülbírálása érdekében további YAML konfigurációs fájlok is megadhatók a parancssorban a Helm futtatásakor. Az Azure app Configuration támogatja a konfigurációs értékek exportálását a YAML-fájlokba. Az exportálási funkció integrálása az üzembe helyezésbe lehetővé teszi, hogy a Kubernetes-alkalmazások kihasználják az alkalmazás konfigurációjában tárolt konfigurációs értékeket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Alkalmazások konfigurációjának használata az alkalmazás Kubernetes való telepítésekor a Helm használatával.
> * Hozzon létre egy Kubernetes titkot egy Key Vault hivatkozás alapján az alkalmazás konfigurációjában.

Ez az oktatóanyag azt feltételezi, hogy a Kubernetes a Helmtel való felügyeletének alapvető ismerete. További információ az alkalmazások az [Azure Kubernetes szolgáltatásban](../aks/kubernetes-helm.md)történő telepítéséről.

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Az [Azure CLI](/cli/azure/install-azure-cli) telepítése (2.4.0 vagy újabb verzió)
- A [Helm](https://helm.sh/docs/intro/install/) telepítése (2.14.0 vagy újabb verzió)
- Egy Kubernetes-fürt.

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Válassza a **Configuration Explorer**  >  **Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | Settings. Color | Fehér |
    | Settings. Message | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Key Vault-hivatkozás hozzáadása az alkalmazás konfigurációjához
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és adjon hozzá egy titkos kulcsot a **jelszó** és az érték **SajátJelszó** [Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) . 
2. Válassza ki az előző szakaszban létrehozott alkalmazás-konfigurációs tároló példányát.

3. Válassza a **Configuration Explorer** lehetőséget.

4. Válassza a **+**  >  **Key Vault-hivatkozás** létrehozása lehetőséget, majd adja meg a következő értékeket:
    - **Kulcs**: válassza a **Secrets. password** elemet.
    - **Címke**: hagyja üresen ezt az értéket.
    - **Előfizetés**, **erőforráscsoport** és **Key Vault**: adja meg az előző lépésben létrehozott kulcstartóban szereplőknek megfelelő értékeket.
    - **Titkos** kód: válassza ki az előző szakaszban létrehozott **jelszó** nevű titkos kulcsot.

## <a name="create-helm-chart"></a>Helm-diagram létrehozása ##
Először hozzon létre egy minta Helm-diagramot a következő paranccsal
```console
helm create mychart
```

A Helm létrehoz egy mychart nevű új könyvtárat az alább látható struktúrával. 

> [!TIP]
> További információért kövesse ezt a [diagramot ismertető útmutatót](https://helm.sh/docs/chart_template_guide/getting_started/) .

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

Következő lépésként frissítse az *Deployment. YAML* fájl **spec: Template: spec: containers** szakaszát. Az alábbi kódrészlet két környezeti változót hoz létre a tárolóhoz. Az értékeket a telepítéskor dinamikusan kell beállítani.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

A frissítés után a teljes *telepítési. YAML* fájlnak az alábbihoz hasonlóan kell kinéznie.

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

Bizalmas adatok Kubernetes-titokként való tárolásához vegyen fel egy *Secrets. YAML* fájlt a sablonok mappában.

> [!TIP]
> További információ a [Kubernetes-titkok](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)használatáról.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Végül frissítse a *Values. YAML* fájlt a következő tartalommal, hogy opcionálisan adja meg az *üzembe helyezés. YAML* és *Secrets. YAML* fájlokban hivatkozott konfigurációs beállítások és titkos kódok alapértelmezett értékeit. Az alkalmazás konfigurációjától származó konfiguráció felülírja a tényleges értékeket.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Konfiguráció átadása az alkalmazás konfigurációjában a Helm install szolgáltatásban ##
Először töltse le a konfigurációt az alkalmazás konfigurációjától egy *konfig. YAML* fájlba. A kulcs szűrő használatával csak azokat a kulcsokat töltse le, amelyek a **beállításokkal** kezdődnek. Ha az adott esetben a kulcs szűrője nem elegendő a Key Vault hivatkozások kulcsainak kizárásához, akkor a **--skip-** kulcstartó argumentumot használhatja a kizáráshoz. 

> [!TIP]
> További információ az [exportálási parancsról](/cli/azure/appconfig/kv#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Ezután töltse le a titkokat egy *mySecrets. YAML* nevű fájlba. A parancssori argumentum **--feloldás-** kulcstartó feloldja a Key Vault hivatkozásokat, ha beolvassa a tényleges értékeket a Key Vaultban. Ezt a parancsot olyan hitelesítő adatokkal kell futtatnia, amelyek hozzáférési jogosultságokkal rendelkeznek a megfelelő Key Vaulthoz.

> [!WARNING]
> Mivel ez a fájl bizalmas adatokat tartalmaz, tartsa a fájlt körültekintően, és törölje, ha már nincs rá szükség.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

A Helm upgrade 's **-f** argumentum használatával adja át a két létrehozott konfigurációs fájlt. A *Values. YAML* megadott konfigurációs értékeket felülbírálják az alkalmazás konfigurációjától származó értékekkel.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

A Helm verziófrissítéshez a **--set** argumentumot is használhatja a literális kulcs értékének átadásához. A **--set** argumentum használata jó módszer a bizalmas adatok lemezre való megőrzésének elkerülésére. 

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

A [Kubernetes irányítópultjának](../aks/kubernetes-dashboard.md)használatával ellenőrizze, hogy a konfigurációk és a titkos kulcsok beállítása sikeres volt-e. Láthatja, hogy az alkalmazás konfigurációjának **szín** -és **üzenet** -értékei a tároló környezeti változói között lettek feltöltve.

![Gyorsindítás alkalmazás elindítása helyi](./media/kubernetes-dashboard-env-variables.png)

Az alkalmazás konfigurációjában az Key Vault-referenciák egyik titka, **jelszava**, tárolása is hozzá lett adva a Kubernetes Secrets szolgáltatáshoz. 

![Képernyőkép, amely kiemeli a jelszót az adatszakaszban.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure-alkalmazás konfigurációs beállításait exportálta egy Kubernetes-telepítésben a Helm használatával. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)
