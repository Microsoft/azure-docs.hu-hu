---
title: Biztonságos webszolgáltatások a TLS használatával
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan engedélyezheti a HTTPS-t a TLS 1,2-es verzióval a Azure Machine Learning használatával központilag telepített webszolgáltatás biztonságossá tételéhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.date: 03/11/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 71cb2e9e112c49d77a2a0b47c24c49cabfa86589
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149018"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével


Ez a cikk bemutatja, hogyan védheti meg az Azure Machine Learning használatával üzembe helyezett webszolgáltatásokat.

A [https](https://en.wikipedia.org/wiki/HTTPS) használatával korlátozhatja a webszolgáltatásokhoz való hozzáférést, és gondoskodhat az ügyfelek által elküldött adatvédelemről. A HTTPS segíti a kommunikációt az ügyfél és a webszolgáltatás között a kettő közötti kommunikáció titkosításával. A titkosítás [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)protokollt használ. A TLS-t néha *SSL* (SSL) néven is emlegetik, amely a TLS elődje volt.

> [!TIP]
> Az Azure Machine Learning SDK az "SSL" kifejezést használja a biztonságos kommunikációhoz kapcsolódó tulajdonságokhoz. Ez nem jelenti azt, hogy a webszolgáltatás nem használ *TLS*-t. Az SSL csak egy gyakrabban felismert kifejezés.
>
> Pontosabban, az Azure Machine Learning-en keresztül üzembe helyezett webszolgáltatások a 1,2-es TLS-es és ACI-s verzióra. ACI-környezetek esetén, ha régebbi TLS-verziót használ, javasoljuk, hogy a legújabb TLS-verzió beszerzéséhez telepítse újra a telepítést.
>
> A Azure Machine Learning-AK-hoz készült 1,3-es TLS-verzió nem támogatott.

A TLS és az SSL egyaránt *digitális tanúsítványokra* támaszkodik, amelyek segítenek a titkosítás és az identitások ellenőrzésében. A digitális tanúsítványok működésével kapcsolatos további információkért tekintse meg a wikipedia témakör [nyilvános kulcsokra épülő infrastruktúráját](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Ha nem használja a HTTPS-t a webszolgáltatáshoz, a szolgáltatásba érkező és onnan érkező adatok az interneten mások számára is láthatóvá válnak.
>
> A HTTPS azt is lehetővé teszi az ügyfél számára, hogy ellenőrizze annak a kiszolgálónak a hitelességét, amelyhez csatlakozik. Ez a szolgáltatás megvédi [az](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ügyfeleket a támadásokkal szemben.

Ez a webszolgáltatás biztonságossá tételének általános folyamata:

1. Tartománynév beszerzése.

2. Digitális tanúsítvány beszerzése.

3. Telepítse vagy frissítse a webszolgáltatást a TLS-t használó alkalmazással.

4. Frissítse a DNS-t, hogy a webszolgáltatásra mutasson.

> [!IMPORTANT]
> Ha az Azure Kubernetes szolgáltatásba (ak) végzi az üzembe helyezést, megvásárolhatja saját tanúsítványát, vagy a Microsoft által biztosított tanúsítványt is használhatja. Ha a Microsofttól származó tanúsítványt használ, nem kell beszereznie a tartománynevet vagy a TLS/SSL-tanúsítványt. További információkért lásd a jelen cikk [TLS engedélyezése és telepítése](#enable) című szakaszát.

Kis eltérések vannak, amikor a biztonsági s-t az [üzembe helyezési célok](how-to-deploy-and-where.md)között.

## <a name="get-a-domain-name"></a>Tartománynév beszerzése

Ha még nem rendelkezik tartománynévvel, vásároljon egyet a tartománynév- *regisztrálótól*. A folyamat és az ár eltér a regisztrátorok között. A regisztrátor a tartománynevet kezelő eszközöket biztosít. Ezeknek az eszközöknek a segítségével teljes tartománynevet (például www \. contoso.com) képezhető le a webszolgáltatást futtató IP-címhez.

## <a name="get-a-tlsssl-certificate"></a>TLS/SSL-tanúsítvány beszerzése

A TLS/SSL-tanúsítványok (digitális tanúsítványok) többféleképpen is beszerezhetők. A leggyakoribb a *hitelesítésszolgáltató (CA* ) egyikének megvásárlása. A tanúsítvány lekérésének helyétől függetlenül a következő fájlokra lesz szüksége:

* Egy **tanúsítvány**. A tanúsítványnak tartalmaznia kell a teljes tanúsítványláncot, és a "PEM-kódolt" értéknek kell lennie.
* Egy **kulcs**. A kulcsnak PEM-kódolású is kell lennie.

Ha tanúsítványt kér, meg kell adnia a webszolgáltatáshoz használni kívánt címek teljes tartománynevét (például a www- \. contoso.com). A rendszer a tanúsítványba pecsételő és az ügyfelek által használt címek összehasonlításával ellenőrzi a webszolgáltatás identitását. Ha ezek a címek nem egyeznek, az ügyfél hibaüzenetet kap.

> [!TIP]
> Ha a hitelesítésszolgáltató nem tudja megadni a tanúsítványt és a kulcsot PEM-kódolású fájlként, használhat egy olyan segédprogramot, mint például az [OpenSSL](https://www.openssl.org/) a formátum megváltoztatásához.

> [!WARNING]
> *Önaláírt* tanúsítványokat csak fejlesztéshez használhat. Ne használja őket éles környezetekben. Az önaláírt tanúsítványok problémákat okozhatnak az ügyfélalkalmazások számára. További információkért tekintse meg az ügyfélalkalmazás által használt hálózati kódtárak dokumentációját.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> A TLS engedélyezése és üzembe helyezése

**AK**-beli telepítés esetén engedélyezheti a TLS-t, amikor [egy AK-fürtöt hoz létre vagy csatlakoztat](how-to-create-attach-kubernetes.md) a pénzmosás-munkaterületen. Az AK-modell üzembe helyezésének ideje letilthatja a TLS-leállást a telepítési konfigurációs objektummal, ellenkező esetben az összes AK-modell üzembe helyezése alapértelmezés szerint a TLS-megszakítást engedélyezi az AK-fürt létrehozásakor vagy az idő csatolásakor

ACI-telepítés esetén engedélyezheti a TLS-megszakítást a modell központi telepítésének ideje alatt a telepítési konfigurációs objektummal.


### <a name="deploy-on-azure-kubernetes-service"></a>Üzembe helyezés az Azure Kubernetes Service-ben

  > [!NOTE]
  > Az ebben a szakaszban található információk akkor is érvényesek, ha biztonságos webszolgáltatást telepít a tervezőhöz. Ha nem ismeri a Python SDK használatát, tekintse [meg a mi a Pythonhoz készült Azure Machine learning SDK?](/python/api/overview/azure/ml/intro)című témakört.

Ha a pénzmosás-munkaterületen [létrehoz vagy csatol egy AK-fürtöt](how-to-create-attach-kubernetes.md) , engedélyezheti a TLS-megszakítást **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** és **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** konfigurációs objektummal. Mindkét metódus olyan konfigurációs objektumot ad vissza, amely **enable_ssl** metódussal rendelkezik, és a TLS engedélyezéséhez használhatja a **enable_ssl** metódust.

A TLS-t a Microsoft-tanúsítvánnyal vagy a CA-ból vásárolt egyéni tanúsítvánnyal engedélyezheti. 

* **Ha tanúsítványt használ a Microsofttól**, akkor a *leaf_domain_label* paramétert kell használnia. Ez a paraméter a szolgáltatás DNS-nevét hozza létre. A "contoso" érték például a "contoso" nevű tartománynevet hozza létre \<six-random-characters> \<azureregion> . cloudapp.azure.com ", ahol a a \<azureregion> szolgáltatást tartalmazó régió. Igény szerint a *overwrite_existing_domain* paraméterrel írhatja felül a meglévő *leaf_domain_label*. Az alábbi példa bemutatja, hogyan hozhat létre olyan konfigurációt, amely engedélyezi a TLS-t a Microsoft-tanúsítvánnyal:

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Ha tanúsítványt használ a Microsofttól, nem kell megvásárolnia a saját tanúsítványát vagy tartománynevét.

    > [!WARNING]
    > Ha az AK-fürt belső terheléselosztó használatával van konfigurálva, a Microsoft által biztosított tanúsítvány használata __nem támogatott__ , és a TLS engedélyezéséhez egyéni tanúsítványt kell használnia.

* A **megvásárolt egyéni tanúsítvány használatakor** a *ssl_cert_pem_file*, *ssl_key_pem_file* és *ssl_cname* paramétereket kell használnia. Az alábbi példa bemutatja, hogyan használható a. PEM fájlok olyan konfiguráció létrehozásához, amely egy megvásárolt TLS/SSL-tanúsítványt használ:
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

További információ a *enable_sslről*: [AksProvisioningConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) és [AksAttachConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Üzembe helyezés az Azure Container Instancesben

A Azure Container Instances-ra való központi telepítéskor megadja a TLS-hez kapcsolódó paraméterek értékeit, ahogy az alábbi kódrészlet is mutatja:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

További információ: [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>A DNS frissítése

Egyéni tanúsítvánnyal vagy ACI-vel történő üzembe helyezés esetén a DNS-rekordot úgy kell frissíteni, hogy az a pontozási végpont IP-címére mutasson.

  > [!IMPORTANT]
  > Ha a Microsofttól származó tanúsítványt használ az AK-alapú telepítéshez, nem kell manuálisan frissítenie a fürt DNS-értékét. Az értéket automatikusan kell beállítani.

Az alábbi lépéseket követve frissítheti az Egyéni tartománynév DNS-rekordját:
* Pontozási végpont IP-címének lekérése a pontozási végpont URI-ja alapján, amely általában a formátuma *http://104.214.29.152:80/api/v1/service/<service-name>/score* . 
* A tartománynév-regisztráló eszközeit használva frissítse a tartománynév DNS-rekordját. A rekordnak a pontozási végpont IP-címére kell mutatnia.
* A DNS-rekord frissítése után az *nslookup Custom-domain-name* paranccsal ellenőrizheti a DNS-feloldást. Ha a DNS-rekord megfelelően frissül, akkor az Egyéni tartománynév a pontozási végpont IP-címére mutat.
* Perc vagy óra késése is lehet, mielőtt az ügyfelek fel tudják oldani a tartománynevet a regisztrátortól és a tartománynévhez konfigurált "élettartamtól" (TTL) függően.


## <a name="update-the-tlsssl-certificate"></a>A TLS/SSL-tanúsítvány frissítése

A TLS/SSL-tanúsítványok lejárnak, és meg kell újítani. Ez általában évente történik. A következő részben található információk segítségével frissítheti és újíthatja meg az Azure Kubernetes Service-ben üzembe helyezett modellek tanúsítványát:

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft által generált tanúsítvány frissítése

Ha a tanúsítványt eredetileg a Microsoft generálta (ha a *leaf_domain_label* a szolgáltatás létrehozásához használja), **akkor a rendszer automatikusan megújítja** , ha szükséges. Ha manuálisan szeretné megújítani, használja az alábbi példák egyikét a tanúsítvány frissítéséhez:

> [!IMPORTANT]
> * Ha a meglévő tanúsítvány még érvényes, használja az `renew=True` (SDK) vagy a `--ssl-renew` (CLI) beállítást a konfiguráció megújításához. Ha például a meglévő tanúsítvány 10 napig érvényes, és nem használja `renew=True` , előfordulhat, hogy a tanúsítvány nem újítható meg.
> * A szolgáltatás eredetileg üzembe helyezése után a `leaf_domain_label` rendszer a mintát használva létrehozza a DNS-nevet `<leaf-domain-label>######.<azure-region>.cloudapp.azure.com` . Ha meg szeretné őrizni a meglévő nevet (beleértve az eredetileg létrehozott 6 számjegyet), használja az eredeti `leaf_domain_label` értéket. Ne adja meg az előállított 6 számjegyet.

**Az SDK használata**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**A parancssori felület (CLI) használata**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

További információkért tekintse meg a következő dokumentációs dokumentumokat:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

### <a name="update-custom-certificate"></a>Egyéni tanúsítvány frissítése

Ha a tanúsítványt eredetileg egy hitelesítésszolgáltató hozta létre, kövesse az alábbi lépéseket:

1. A tanúsítvány megújításához használja a hitelesítésszolgáltató által biztosított dokumentációt. Ez a folyamat új tanúsítványfájl-fájlokat hoz létre.

1. Az SDK vagy a CLI használatával frissítse a szolgáltatást az új tanúsítvánnyal:

    **Az SDK használata**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **A parancssori felület (CLI) használata**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

További információkért tekintse meg a következő dokumentációs dokumentumokat:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

## <a name="disable-tls"></a>TLS letiltása

Ha le szeretné tiltani a TLS-t az Azure Kubernetes szolgáltatásban üzembe helyezett modellhez, hozzon létre egy `SslConfiguration` `status="Disabled"` -t, majd hajtson végre egy frissítést:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Következő lépések
Az alábbiak végrehajtásának módját ismerheti meg:
+ [Webszolgáltatásként üzembe helyezett gépi tanulási modell felhasználása](how-to-consume-web-service.md)
+ [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)