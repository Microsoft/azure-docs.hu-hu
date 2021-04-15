---
title: A windowsos Self-Hosted Integration Runtime futtatása Windows-tárolóban
description: Útmutató a windowsos tárolók Self-Hosted Integration Runtime futtatásához.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 2423d6bd29d893f9a27749dcc2b6d2af8a12e941
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478131"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>A windowsos Self-Hosted Integration Runtime futtatása Windows-tárolóban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk bemutatja, hogyan futtathat Self-Hosted Integration Runtime Windows-tárolóban.
Azure Data Factory a windowsos tárolók hivatalos támogatását Self-Hosted Integration Runtime. Letöltheti a Docker build forráskódját, és kombinálhatja az összeállítási és futtatási folyamatot a saját folyamatos teljesítési folyamatában. 

## <a name="prerequisites"></a>Előfeltételek 
- [Windows-tárolókra vonatkozó követelmények](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker 2.3-as és újabb verziók 
- Self-Hosted Integration Runtime 5.2.7713.1-es és újabb verziók 
## <a name="get-started"></a>Bevezetés 
1.  A Docker telepítése és a Windows-tároló engedélyezése 
2.  A forráskód letöltése: https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Töltse le az SHIR legújabb verzióját az SHIR mappába 
4.  Nyissa meg a mappát a rendszerhéjban: 
```console
cd "yourFolderPath"
```

5.  A Windows Docker-rendszerkép összeállítása: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Docker-tároló futtatása: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true -e HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY parancs használata kötelező. NODE_NAME, ENABLE_HA és HA_PORT megadása nem kötelező. Ha nem adja meg az értéket, a parancs az alapértelmezett értékeket fogja használni. Az alapértelmezett érték ENABLE_HA hamis, a HA_PORT pedig 8060.

## <a name="container-health-check"></a>Tároló állapotának ellenőrzése 
A 120 másodperces indítási időszak után az állapot-ellenőrző rendszeres időközönként 30 másodpercenként fut. Ez biztosítja az integrációs integrációs rendszer állapotát a tárolómotornak. 

## <a name="limitations"></a>Korlátozások
Az alábbi funkciók jelenleg nem támogatottak a windowsos Self-Hosted Integration Runtime futtatásakor:
- HTTP-proxy 
- Titkosított csomópont-kommunikáció TLS-/SSL-tanúsítvánnyal 
- Biztonsági másolat létrehozása és importálása 
- Démonszolgáltatás 
- Automatikus frissítés 

### <a name="next-steps"></a>Következő lépések
- Tekintse át [az integrációs Azure Data Factory.](./concepts-integration-runtime.md)
- Ismerje meg, hogyan hozhat létre saját maga által üzemeltetett [integrációs Azure Portal.](./create-self-hosted-integration-runtime.md)