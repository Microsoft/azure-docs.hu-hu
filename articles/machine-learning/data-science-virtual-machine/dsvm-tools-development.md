---
title: Fejlesztési eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg a Data Science Virtual Machine elérhető eszközöket és integrált fejlesztői környezeteket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cecc195b8b97ffd9b25cf12898726352ddd698a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519439"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Fejlesztői eszközök az Azure Data Science Virtual Machine

A Data Science Virtual Machine (DSVM) számos népszerű eszközt csomagol egy rendkívül hatékony integrált fejlesztési környezetbe (IDE). Íme néhány olyan eszköz, amely a DSVM van megadva.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows: Visual Studio 2017, Windows 2019: Visual Studio 2019      |
| Jellemző felhasználások      | Szoftverfejlesztés    |
| Hogyan van konfigurálva és telepítve a DSVM?      | Adatelemzési munkaterhelés (Python és R-eszközök), Azure munkaterhelés (Hadoop, Data Lake), Node.js, SQL Server eszközök, [Azure Machine learning a Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai) -hoz    |
| Használat és Futtatás      | Asztali parancsikon ( `C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe` ). Grafikusan nyissa meg a Visual studiót az asztal ikon vagy a **Start** menü használatával. Keressen programokat (Windows billentyű + S), majd a **Visual studiót**. Innen olyan nyelveket hozhat létre, mint például a C#, a Python, az R és a Node.js.   |
| A DSVM kapcsolódó eszközök      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Előfordulhat, hogy a próbaidőszak lejárta után üzenetet kap. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új ingyenes fiókot a Visual Studio Community eléréséhez.

## <a name="visual-studio-code"></a>Visual Studio Code 

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Jellemző felhasználások      | Kódszerkesztő és git-integráció   |
| Használat és Futtatás      | Asztali parancsikon ( `C:\Program Files (x86)\Microsoft VS Code\Code.exe` ) a Windowsban, asztali parancsikon vagy terminál ( `code` ) a Linux rendszerben    |
| A DSVM kapcsolódó eszközök      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | Ügyfél IDE az R nyelvhez   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Jellemző felhasználások      |  R-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon ( `C:\Program Files\RStudio\bin\rstudio.exe` ) Windows rendszeren, asztali parancsikon ( `/usr/bin/rstudio` ) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | Ügyfél IDE az R nyelvhez   |
| mi ez?   | Web-alapú IDE az R-hez    |
| Támogatott DSVM-verziók      | Linux      |
| Jellemző felhasználások      |  R-fejlesztés     |
| Használat és Futtatás      | Engedélyezze a szolgáltatást a _systemctl Enable rstudio-Server engedélyezésével_, majd indítsa el a szolgáltatást a _systemctl Start rstudio-Server kiszolgálóval_. Ezután jelentkezzen be a RStudio-kiszolgálóra a http: \/ /Your-VM-IP: 8787 címen.       |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | Az ügyfél IDE for Julia nyelve   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Jellemző felhasználások      |  Julia-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon ( `C:\JuliaPro-0.5.1.1\Juno.bat` ) Windows rendszeren, asztali parancsikon ( `/opt/JuliaPro-VERSION/Juno` ) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Notebookshoz

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | A Python nyelvhez készült ügyféloldali IDE    |
| Támogatott DSVM-verziók      | Windows 2019, Linux      |
| Jellemző felhasználások      |  Python-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon ( `C:\Program Files\tk` ) Windows rendszeren. Asztali parancsikon ( `/usr/bin/pycharm` ) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, RStudio      |
