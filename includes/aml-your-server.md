---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511075"
---
1. A Pythonhoz készült Azure Machine Learning SDK telepítéséhez [Azure Machine learning SDK](/python/api/overview/azure/ml/install)  -ban található utasításokat használhatja.

1. Hozzon létre egy [Azure Machine learning munkaterületet](../articles/machine-learning/how-to-manage-workspace.md).

1. [Konfigurációs fájl](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.js**) írása.

1. [A GitHub-tárház](https://aka.ms/aml-notebooks)klónozása.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```bash
    jupyter notebook
    ```