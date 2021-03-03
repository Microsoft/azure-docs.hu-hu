---
title: OpenShift Container platform 3,11 Azure-beli üzembe helyezés utáni feladatok
description: További feladatok a OpenShift Container platform 3,11-fürt üzembe helyezését követően.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: c3f9aaa15a697202aa76c563ed62bf37443d69ec
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669383"
---
# <a name="post-deployment-tasks"></a>Üzembe helyezés utáni feladatok

OpenShift-fürt üzembe helyezése után további elemeket is beállíthat. Ez a cikk a következőket ismerteti:

- Egyszeri bejelentkezés konfigurálása Azure Active Directory (Azure AD) használatával
- Azure Monitor naplók konfigurálása a OpenShift figyeléséhez
- Metrikák és naplózás konfigurálása
- Az Open Service Broker for Azure (OSBA) telepítése

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Az egyszeri bejelentkezés konfigurálása Azure Active Directory használatával

Ha Azure Active Directoryt szeretne használni a hitelesítéshez, először létre kell hoznia egy Azure AD-alkalmazás regisztrációját. Ez a folyamat két lépést foglal magában: az alkalmazás regisztrációjának létrehozása és az engedélyek konfigurálása.

### <a name="create-an-app-registration"></a>Alkalmazásregisztráció létrehozása

Ezek a lépések az Azure CLI használatával hozhatják létre az alkalmazás regisztrációját és a grafikus felhasználói felületet (portál) az engedélyek megadásához. Az alkalmazás regisztrációjának létrehozásához a következő öt információra van szüksége:

- Megjelenítendő név: alkalmazás regisztrációs neve (például OCPAzureAD)
- Kezdőlap: OpenShift-konzol URL-címe (például `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Azonosító URI: OpenShift-konzol URL-címe (például `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Válasz URL-címe: fő nyilvános URL-cím és az alkalmazás regisztrációs neve (például `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Password (jelszó): biztonságos jelszó (erős jelszó használata)

Az alábbi példa az előző információk alapján létrehoz egy alkalmazást:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Ha a parancs sikeres, a következőhöz hasonló JSON-kimenetet kap:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Jegyezze fel a parancs által visszaadott appId tulajdonságot egy későbbi lépéshez.

Az Azure Portalon:

1. Válassza ki **Azure Active Directory** az  >  **alkalmazás regisztrációját**.
2. Keresse meg az alkalmazás regisztrációját (például OCPAzureAD).
3. Az eredmények között kattintson az alkalmazás regisztrációja elemre.
4. A **Beállítások** területen válassza a **szükséges engedélyek** lehetőséget.
5. A **szükséges engedélyek** területen válassza a **Hozzáadás** lehetőséget.

   ![Alkalmazás regisztrálása](media/openshift-post-deployment/app-registration.png)

6. Kattintson az 1. lépés: az API kiválasztása, majd a **Windows Azure Active Directory (Microsoft. Azure. ActiveDirectory)** elemre. Kattintson a lenti **kijelölés** gombra.

   ![Alkalmazás regisztrálása API kiválasztása](media/openshift-post-deployment/app-registration-select-api.png)

7. A 2. lépés: válassza az engedélyek lehetőséget, válassza a **Bejelentkezés és a felhasználói profil olvasása** a **delegált engedélyek** területen, majd kattintson a **kiválasztás** elemre.

   ![Alkalmazás-regisztrálási hozzáférés](media/openshift-post-deployment/app-registration-access.png)

8. Válassza a **Kész** lehetőséget.

### <a name="configure-openshift-for-azure-ad-authentication"></a>OpenShift konfigurálása az Azure AD-hitelesítéshez

Ahhoz, hogy a OpenShift az Azure AD hitelesítési szolgáltatóként való használatára konfigurálja, a/etc/Origin/Master/Master-config.YAML-fájlt minden főcsomóponton szerkeszteni kell.

Keresse meg a bérlő AZONOSÍTÓját az alábbi CLI-parancs használatával:

```azurecli
az account show
```

A YAML fájlban keresse meg a következő sorokat:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Szúrja be a következő sorokat közvetlenül az előző sorok után:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Győződjön meg arról, hogy a szöveg helyesen van igazítva a identityProviders alatt. Keresse meg a bérlő AZONOSÍTÓját az alábbi CLI-parancs használatával: ```az account show```

Indítsa újra a OpenShift Master Servicest az összes főcsomóponton:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

A OpenShift-konzolon mostantól két hitelesítési lehetőség látható: htpasswd_auth és [alkalmazás regisztrálása].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>OpenShift figyelése Azure Monitor naplókkal

Az Log Analytics-ügynök három módon adható hozzá a OpenShift.
- A Linux rendszerhez készült Log Analytics-ügynök telepítése közvetlenül az egyes OpenShift-csomópontokon
- Azure Monitor virtuálisgép-bővítmény engedélyezése az egyes OpenShift-csomópontokon
- A Log Analytics-ügynök telepítése OpenShift Daemon-set

További részletekért olvassa el a teljes [útmutatást](../../azure-monitor/containers/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) .

## <a name="configure-metrics-and-logging"></a>Metrikák és naplózás konfigurálása

Az ág alapján a OpenShift-tároló platform és a OKD Azure Resource Manager sablonjai a telepítés részeként adhatnak bemeneti paramétereket a metrikák és a naplózás engedélyezéséhez.

A OpenShift Container platform Marketplace ajánlat emellett lehetőséget biztosít a metrikák és a naplózás engedélyezésére a fürt telepítése során.

Ha a rendszer nem engedélyezte a metrikákat vagy a naplózást a fürt telepítése során, az a tény után egyszerűen engedélyezhető.

### <a name="azure-cloud-provider-in-use"></a>Használatban lévő Azure Cloud Provider

Az üzembe helyezés során megadott hitelesítő adatok használatával SSH-t használhat a megerősített csomóponthoz vagy az első főcsomóponthoz (a használatban lévő sablon és ág alapján). Adja ki a következő parancsot:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Az Azure Cloud Provider nincs használatban

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>A nyílt Service Broker telepítése az Azure-hoz (OSBA)

Az Azure-ban vagy a OSBA-ben megnyitott Service Broker lehetővé teszi, hogy közvetlenül a OpenShift hozzon létre Azure-Cloud Services. A OSBA egy nyílt Service Broker API-implementáció az Azure-hoz. A nyílt Service Broker API egy spec, amely közös nyelvet definiál a felhőalapú szolgáltatók számára, amelyekkel a Felhőbeli natív alkalmazások kezelhetik a felhőalapú szolgáltatásokat a zárolás nélkül.

A OSBA a OpenShift-on való telepítéséhez kövesse az itt található utasításokat: https://github.com/Azure/open-service-broker-azure#openshift-project-template . 
> [!NOTE]
> Csak a OpenShift-projekt sablonjának lépéseit kell végrehajtania, nem a teljes telepítés szakaszt.

## <a name="next-steps"></a>Következő lépések

- [OpenShift-tároló platform – első lépések](https://docs.openshift.com)
