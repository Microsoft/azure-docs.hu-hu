---
title: A webszolgáltatásként üzembe helyezett modellek hitelesítésének konfigurálása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan konfigurálhatja a Azure Machine Learning webszolgáltatásaiba telepített gépi tanulási modellek hitelesítését.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94447650"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>A webszolgáltatásként üzembe helyezett modellek hitelesítésének konfigurálása

A Azure Machine Learning segítségével webszolgáltatásként helyezheti üzembe a betanított gépi tanulási modelleket. Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hitelesítést ezekhez a központi telepítésekhez.

A Azure Machine Learning által létrehozott modell-telepítések a következő két hitelesítési módszer egyikének használatára konfigurálhatók:

* **kulcs-alapú**: a rendszer statikus kulcsot használ a webszolgáltatáshoz való hitelesítéshez.
* **jogkivonat-alapú**: ideiglenes jogkivonatot kell beszerezni a Azure Machine learning munkaterületről (Azure Active Directory használatával) és a webszolgáltatáshoz való hitelesítéshez. Ez a jogkivonat egy adott idő elteltével lejár, és frissíteni kell a webszolgáltatással folytatott munka folytatásához.

    > [!NOTE]
    > A jogkivonat-alapú hitelesítés csak az Azure Kubernetes szolgáltatásban való üzembe helyezéskor érhető el.

## <a name="key-based-authentication"></a>Kulcsalapú hitelesítés

Az Azure Kubernetes Service (ak) szolgáltatásban üzembe helyezett webszolgáltatások alapértelmezés szerint *engedélyezve* vannak a kulcs alapú hitelesítéssel.

Azure Container Instances (ACI) központilag telepített szolgáltatások esetében a kulcs-alapú hitelesítés alapértelmezés szerint *le van tiltva* , de engedélyezheti `auth_enabled=True` az ACI webszolgáltatások létrehozásakor. A következő kód egy példa arra, hogyan hozható létre egy ACI központi telepítési konfiguráció a kulcs alapú hitelesítéssel.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Ezt követően használhatja az egyéni ACI-konfigurációt az üzembe helyezés során a `Model` osztály használatával.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Az Auth kulcsok beolvasásához használja a következőt: `aci_service.get_keys()` . A kulcs újragenerálása érdekében használja a `regen_key()` függvényt, és adja át az **elsődleges** vagy a **másodlagos** műveletet.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Jogkivonat-alapú hitelesítés

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználóknak egy Azure Machine Learning JSON Web Token kell bemutatnia a webszolgáltatásnak az eléréséhez. A jogkivonat egy megadott időkeret után lejár, és a hívások folytatásához frissíteni kell.

* **Alapértelmezés szerint** a jogkivonat-hitelesítés le van tiltva az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A jogkivonat-hitelesítés **nem támogatott** , ha Azure Container instances telepíti.
* A jogkivonat-hitelesítés **nem használható a kulcs alapú hitelesítéssel megegyező időpontban**.

A jogkivonat-hitelesítés vezérléséhez használja a (z) `token_auth_enabled` paramétert a központi telepítés létrehozásakor vagy frissítésekor:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Ha engedélyezve van a jogkivonat-hitelesítés, a metódus használatával kérhet `get_token` le egy JSON web token (JWT) és a jogkivonat lejárati idejét:

> [!TIP]
> Ha a jogkivonat lekéréséhez egyszerű szolgáltatásnevet használ, és azt szeretné, hogy a jogkivonat lekéréséhez minimálisan szükséges legyen a hozzáférés, rendelje hozzá a munkaterülethez tartozó **olvasó** szerepkörhöz.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Új jogkivonatot kell kérnie a jogkivonat `refresh_by` ideje után. Ha a Python SDK-n kívülre kell frissítenie a jogkivonatokat, az egyik lehetőség az, hogy a REST API a szolgáltatás-egyszerű hitelesítéssel rendszeres időközönként a `service.get_token()` hívást a korábban tárgyalt módon használja.
>
> Javasoljuk, hogy az Azure Kubernetes Service-fürttel azonos régióban hozza létre Azure Machine Learning munkaterületét.
>
> A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület-régió nem érhető el, nem tud beolvasni egy jogkivonatot a webszolgáltatáshoz, még akkor sem, ha a fürt egy másik régióban található a munkaterületen. Ennek eredményeképpen az Azure AD-hitelesítés nem érhető el, amíg a munkaterület-régió újra elérhetővé nem válik.
>
> Továbbá minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.

## <a name="next-steps"></a>Következő lépések

Az üzembe helyezett modellekkel való hitelesítéssel kapcsolatos további információkért lásd: [ügyfél létrehozása webszolgáltatásként üzembe helyezett modellhez](how-to-consume-web-service.md).