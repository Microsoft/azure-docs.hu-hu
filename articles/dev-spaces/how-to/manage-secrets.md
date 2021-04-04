---
title: A titkok kezelése az Azure fejlesztői területtel való munka során
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Ismerje meg, hogyan használhatja a Kubernetes titkokat futtatáskor vagy létrehozáskor az Azure dev Spaces-alkalmazások fejlesztésekor
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972968"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>A titkok kezelése az Azure fejlesztői területtel való munka során

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

A szolgáltatásokhoz bizonyos jelszavak, a kapcsolatok karakterláncai és egyéb titkos kulcsok, például adatbázisok vagy egyéb biztonságos Azure-szolgáltatások szükségesek. Ha beállítja a titkos kódok értékeit a konfigurációs fájlokban, a programkódban környezeti változókként elérhetővé teheti őket.  Ezeket a konfigurációs fájlokat körültekintően kell kezelni, hogy ne veszélyeztesse a titkok biztonságát.

## <a name="storing-and-using-runtime-secrets"></a>Futásidejű titkok tárolása és használata

Az Azure dev Spaces szolgáltatás két ajánlott, egyszerűbb lehetőséget biztosít a titkok tárolására az Azure dev Spaces-ügyfél eszközei által létrehozott Helm-diagramokon: a `values.dev.yaml` fájlban, és közvetlenül a-ben `azds.yaml` . A titkos kódok tárolása nem ajánlott `values.yaml` .

> [!NOTE]
> Az alábbi módszerek azt mutatják be, hogyan tárolhatók és használhatók az ügyfél-eszközkészlet által létrehozott Helm-diagramok titkai. Ha létrehoz egy saját Helm-diagramot, a Helm diagramot közvetlenül is használhatja a titkok kezeléséhez és tárolásához.

### <a name="using-valuesdevyaml"></a>A Values. dev. YAML használata

Egy olyan projektben, amelyet már előkészített az Azure dev Spaces használatával, hozzon létre egy `values.dev.yaml` fájlt ugyanabban a mappában, mint a `azds.yaml` titkos kulcsok és értékek meghatározásához. Például:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Győződjön meg arról, hogy a `azds.yaml` fájl hivatkozásai nem `values.dev.yaml` választhatók a használatával `?` . Például:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Ha további titkos fájlokkal rendelkezik, ezeket is hozzáadhatja.

Frissítse vagy ellenőrizze, hogy a szolgáltatás környezeti változókként hivatkozik-e a titkokra. Például:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Futtassa a frissített szolgáltatásait a használatával `azds up` .

```console
azds up
```
 
A használatával `kubectl` ellenőrizheti, hogy a titkok létrejöttek-e.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Nem ajánlott a titkokat a forrás vezérlőelemben tárolni. Ha a git-t használja, vegye fel `values.dev.yaml` a `.gitignore` fájlt a fájlba, hogy elkerülje a titkos kódok véglegesítését a verziókövetés során.

### <a name="using-azdsyaml"></a>A azds. YAML használata

Egy olyan projektben, amelyet már előkészített az Azure dev Spaces használatával, adja hozzá a titkos kulcsokat és az értékeket az *$PLACEHOLDER* szintaxissal a *konfigurációk területen. fejlessze. install. set* in `azds.yaml` . Például:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> A titkos értékeket közvetlenül a *$PLACEHOLDER* szintaxisának használata nélkül is megadhatja `azds.yaml` . Ez a megközelítés azonban nem ajánlott, mivel a `azds.yaml` forrás-vezérlőelemben van tárolva.
     
Hozzon létre egy `.env` fájlt ugyanabban a mappában, mint a `azds.yaml` *$PLACEHOLDER* értékek meghatározásához. Például:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Nem ajánlott a titkokat a forrás vezérlőelemben tárolni. Ha a git-t használja, vegye fel `.env` a `.gitignore` fájlt a fájlba, hogy elkerülje a titkos kódok véglegesítését a verziókövetés során.

Frissítse vagy ellenőrizze, hogy a szolgáltatás környezeti változókként hivatkozik-e a titkokra. Például:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Futtassa a frissített szolgáltatásait a használatával `azds up` .

```console
azds up
```
 
A használatával `kubectl` ellenőrizheti, hogy a titkok létrejöttek-e.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Titkok használata Build argumentumként

Az előző szakaszban megmutatta, hogyan tárolhat és használhat titkokat a tároló futási idején. A (z) használatával bármilyen titkos kulcsot is használhat a tároló létrehozási idején, például egy privát NuGet tartozó jelszót `azds.yaml` .

A-ben `azds.yaml` állítsa be a konfigurációkban a létrehozási idő titkait. a szintaxis használatával *fejlessze a. Build. ARG* -t. `<variable name>: ${secret.<secret name>.<secret key>}` Például:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

A fenti példában a *mynugetsecret* egy meglévő titkos kulcs, és a *pattoken* egy meglévő kulcs.

>[!NOTE]
> A titkos nevek és kulcsok tartalmazhatják a `.` karaktert. `\`A Escape használata a `.` titkokat Build argumentumként való átadásakor. Ha például egy *foo.bar* nevű titkos kulcsot szeretne átadni a *token* kulcsaként: `MYTOKEN: ${secret.foo\.bar.token}` . Emellett a titkokat előtaggal és Postfix szöveggel is kiértékelheti. Például: `MYURL: eus-${secret.foo\.bar.token}-version1`. Emellett a szülő és a nagyszülő terekben elérhető titkokat Build argumentumként lehet átadni.

A Docker használja az *ARG* direktívát a titkos kód felhasználásához, majd használja ugyanezt a változót később a Docker. Például:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Frissítse a fürtön futó szolgáltatásokat ezekkel a módosításokkal. A parancssorban futtassa a következő parancsot:

```
azds up
```

## <a name="next-steps"></a>Következő lépések

Ezekkel a módszerekkel mostantól biztonságosan csatlakozhat egy adatbázishoz, egy Azure-gyorsítótárhoz a Redis-hez, vagy hozzáférhet a biztonságos Azure-szolgáltatásokhoz.
 
