---
title: Hitelesítési Titkok a betanításban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan teheti elérhetővé a titkokat a képzésekben a munkaterülethez Azure Key Vault használatával biztonságos módon.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b93da1e252357830578783c8f3ab5ca02f5a3e5b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520744"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Hitelesítő adatokkal kapcsolatos titkos kódok használata Azure Machine Learning betanítási futtatásokban


Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a Titkok a betanításban. A hitelesítési adatok, például a Felhasználónév és a jelszó titkos kódok. Ha például egy külső adatbázishoz csatlakozik a betanítási adatai lekérdezéséhez, a felhasználónevet és a jelszót át kell adnia a távoli futtatási környezetnek. Ha ezeket az értékeket a titkosítatlan szövegben lévő betanítási parancsfájlokba szeretné írni, nem biztonságos, mert a titkos kulcsot kiteszi. 

Ehelyett a Azure Machine Learning munkaterület egy [Azure Key Vault](../key-vault/general/overview.md)nevű társított erőforrással rendelkezik. Ennek a Key Vaultnak a használatával biztonságosan továbbíthatja a titkos kulcsokat a távoli futtatáshoz a Azure Machine Learning Python SDK API-jai segítségével.

A titkok használatára szolgáló standard folyamat a következő:
 1. A helyi számítógépen jelentkezzen be az Azure-ba, és kapcsolódjon a munkaterületéhez.
 2. Helyi számítógépen állítson be egy titkos kulcsot a munkaterületen Key Vault.
 3. Távoli Futtatás küldése.
 4. A távoli Futtatás alatt szerezze be a titkot Key Vault és használja.

## <a name="set-secrets"></a>Titkos kódok beállítása

A [Azure Machine learning a kulcstartó osztály a](/python/api/azureml-core/azureml.core.keyvault.keyvault) titkok beállítására szolgáló metódusokat tartalmazza. A helyi Python-munkamenetben először szerezzen be egy hivatkozást a munkaterületre Key Vault, majd a [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-) metódus használatával állítsa be a titkos kulcsot név és érték alapján. Ha a név már létezik, a __set_secret__ metódus frissíti a titkos értéket.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Ne helyezze el a titkos értéket a Python-kódban, mert nem biztonságos a fájlként való tárolása titkosítatlan fájlként. Ehelyett szerezze be a titkos értéket egy környezeti változóból, például az Azure DevOps Build Secret vagy az interaktív felhasználói bemenet alapján.

A titkos neveket a [`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) metódussal listázhatja, és egy batch-verzió is,[set_secrets ()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-) segítségével egyszerre több titkot is beállíthat.

## <a name="get-secrets"></a>Titkok beolvasása

A helyi kódban a [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) metódus használatával kérheti le a titkos értéket név alapján.

A futtatásához a [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-)  metódust a következő osztály használatával küldi [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) el: [`Run`](/python/api/azureml-core/azureml.core.run%28class%29) . Mivel az elküldött futtatások tisztában vannak a munkaterületével, ez a módszer a munkaterület-példányát, és közvetlenül a titkos értéket adja vissza.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Ügyeljen arra, hogy a titkos értéket ne tegye elérhetővé vagy nyomtassa ki.

Létezik egy batch-verzió is, [get_secrets ()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-) , amely egyszerre több titkot is elér.

## <a name="next-steps"></a>Következő lépések

 * [Példa jegyzetfüzetek megtekintése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Ismerkedjen meg a nagyvállalati biztonsággal Azure Machine Learning](concept-enterprise-security.md)