---
title: Automatizált tesztek futtatása a Azurite használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan írhat automatizált teszteket az Azure-Blob Storage magánhálózati végpontokon a Azurite használatával.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111818"
---
# <a name="run-automated-tests-by-using-azurite"></a>Automatizált tesztek futtatása a Azurite használatával

Megtudhatja, hogyan írhat automatizált teszteket az Azure-Blob Storage magánhálózati végpontokon a Azurite Storage Emulator használatával.

## <a name="run-tests-on-your-local-machine"></a>Tesztek futtatása a helyi gépen

1. A [Python](https://www.python.org/) legújabb verziójának telepítése

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) telepítése

1. A [Azurite](../common/storage-use-azurite.md)telepítése és futtatása:

   **1. lehetőség:** A NPM használatával telepítse, majd futtassa helyileg a Azurite

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **2. lehetőség:** A Docker használata a Azurite futtatásához

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. Azure Storage Explorer válassza a **csatolás helyi emulátorhoz** lehetőséget.

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Képernyőkép Azure Storage Explorer az Azure Storage-forráshoz való csatlakozásról.":::

1. Adja **meg a megjelenítendő nevet** és a **Blobok portszámát** a Azurite összekapcsolásához és a Azure Storage Explorer használatával a helyi blob Storage kezeléséhez.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Képernyőfelvétel: Azure Storage Explorer egy helyi emulátorhoz csatolva.":::

1. Virtuális Python-környezet létrehozása

   ```bash
   python -m venv .venv
   ```

1. Hozzon létre egy tárolót, és inicializálja a környezeti változókat. Tesztek létrehozásához használjon [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) -fájlt. Íme egy példa egy conftest.py-fájlra:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > A Azurite alapértelmezett értéke a következő: ez az érték `AZURE_STORAGE_CONNECTION_STRING` nem titkos kulcs.

1. [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt) fájlban felsorolt függőségek telepítése

   ```bash
   pip install -r requirements.txt
   ```

1. Tesztek futtatása:

   ```bash
   python -m pytest ./tests
   ```

A tesztek futtatása után a Azurite blob Storage-ban található fájlokat a Azure Storage Explorer használatával tekintheti meg.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Képernyőkép Azure Storage Explorer a tesztek által generált fájlok megjelenítéséről.":::

## <a name="run-tests-on-azure-pipelines"></a>Tesztek futtatása Azure-folyamatokon

A tesztek helyi futtatása után győződjön meg arról, hogy a tesztek az [Azure-folyamatokon](/azure/devops/pipelines)át futnak. Az Azure-ban üzemeltetett ügynökként használhat Docker Azurite-rendszerképet, vagy a NPM használatával telepítheti a Azurite-t. A következő Azure-folyamatok például a NPM használatával telepítik a Azurite.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Az Azure-folyamatok tesztelésének futtatása után a következőhöz hasonló kimenetnek kell megjelennie:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Képernyőkép az Azure-folyamatok tesztelési eredményeiről.":::

## <a name="next-steps"></a>Következő lépések

- [A Azurite Emulator használata a helyi Azure Storage-fejlesztéshez](../common/storage-use-azurite.md)
- [Minta: a Azurite használata blob Storage-tesztek futtatásához az Azure DevOps-folyamatban](https://github.com/Azure-Samples/automated-testing-with-azurite)
