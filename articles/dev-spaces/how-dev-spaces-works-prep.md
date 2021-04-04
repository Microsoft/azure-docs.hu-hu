---
title: Projekt előkészítése az Azure dev Spaces működéséhez
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Ismerteti a projekt előkészítését az Azure dev Spaces működésével
keywords: azds. YAML, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91968157"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Projekt előkészítése az Azure dev Spaces működéséhez

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Az Azure dev Spaces lehetővé teszi a Kubernetes-alkalmazások gyors megismétlését és hibakeresését, valamint a csapattal való együttműködését egy Azure Kubernetes szolgáltatásbeli (ak-beli) fürtön. A fejlesztői területek létrehozhatnak Dockerfiles és Helm-diagramokat a projekthez. A dev Spaces egy konfigurációs fájlt is létrehoz és használ a Kubernetes-alkalmazások AK-beli üzembe helyezéséhez, futtatásához és hibakereséséhez. Az összes ilyen fájl az alkalmazás kódjával együtt található, és hozzáadható a verziókövetés rendszeréhez.

Ez a cikk azt ismerteti, hogy mi történik a projekt előkészítésében, hogy az AK-ban és a dev Spaces szolgáltatásban fusson

## <a name="prepare-your-code"></a>A kód előkészítése

Ahhoz, hogy az alkalmazás egy fejlesztői térben fusson, a tárolónak kell lennie, és meg kell határoznia, hogyan kell telepíteni a Kubernetes. Az alkalmazás tárolóba helyezése szüksége lesz egy Docker. Ha meg szeretné határozni, hogy az alkalmazás hogyan legyen központilag telepítve a Kubernetes-re, [Helm-diagramra](https://docs.helm.sh/)van szüksége. Ahhoz, hogy segítséget nyújtson az alkalmazáshoz tartozó Docker és Helm diagram létrehozásában, az ügyféloldali eszközök a következő `prep` parancsot biztosítják:

```cmd
azds prep --enable-ingress
```

A `prep` parancs megtekinti a projektben lévő fájlokat, és megpróbálja létrehozni a Docker és a Helm diagramot az alkalmazás futtatásához a Kubernetes-ben. A parancs jelenleg a `prep` következő nyelvekkel hoz majd Docker és Helm diagramot:

* Java
* Node.js
* .NET Core

A  `prep` parancsot olyan könyvtárból kell futtatnia, amely forráskódot tartalmaz. Ha a `prep` parancsot a megfelelő könyvtárból futtatja, lehetővé teszi, hogy az ügyféloldali eszköz azonosítsa a nyelvet, és megfelelő Docker hozzon létre az alkalmazás tárolóba helyezése. A `prep` parancsot egy olyan könyvtárból is futtathatja, amely a Java-projektekhez *pom.xml* fájlt tartalmaz.

Ha olyan könyvtárból futtatja a `prep` parancsot, amely nem tartalmaz forráskódot, az ügyféloldali eszközkészlet nem hoz majd Docker. Emellett a következő hibaüzenetet is megjeleníti: *a Docker nem hozható létre a nem támogatott nyelv miatt*. Ez a hiba akkor is előfordulhat, ha az ügyféloldali eszközkészlet nem ismeri fel a projekt típusát.

A parancs futtatásakor lehetősége `prep` van megadnia a `--enable-ingress` jelzőt. Ez a jelző arra utasítja a vezérlőt, hogy hozzon létre egy internetről elérhető végpontot ehhez a szolgáltatáshoz. Ha nem adja meg ezt a jelzőt, a szolgáltatás csak a fürtön belülről, vagy az ügyféloldali eszközök által létrehozott localhost-alagút használatával érhető el. A parancs futtatása után engedélyezheti vagy letilthatja ezt a viselkedést `prep` a generált Helm diagram frissítésével.

A `prep` parancs nem cseréli le a projektben meglévő Dockerfiles-vagy Helm-diagramokat. Ha egy meglévő Docker-vagy Helm-diagram ugyanazt az elnevezési konvenciót használja, mint a parancs által generált fájlok `prep` , `prep` akkor a parancs kihagyja a fájlok generálását. Ellenkező esetben a `prep` parancs létrehozza a saját Docker vagy Helm-diagramot a meglévő fájlok oldalára.

> [!IMPORTANT]
> Az Azure dev Spaces a Docker és Helm diagramot használja a projekthez a kód létrehozásához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

A `prep` parancs a `azds.yaml` projekt gyökerében is létrehozza a fájlt. Az Azure dev Spaces ezt a fájlt használja az alkalmazás létrehozásához, telepítéséhez, konfigurálásához és futtatásához. Ez a konfigurációs fájl felsorolja a Docker és a Helm diagram helyét, valamint további konfigurációkat is biztosít ezen összetevők felett.

Íme egy példa az azds. YAML fájlra, amelyet a [.net Core Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)használatával hoztak létre:

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

A `azds.yaml` parancs által generált fájl `prep` célja, hogy egyszerű, egyetlen projekt-fejlesztési forgatókönyvhöz működjön. Ha az adott projektnek nagyobb a bonyolultsága, akkor előfordulhat, hogy a parancs futtatása után frissítenie kell ezt a fájlt `prep` . Előfordulhat például, hogy a projektnek a fejlesztési vagy hibakeresési igények alapján módosítania kell a Build vagy a Launch folyamatát. Több alkalmazás is lehet a projektben, amelyhez több fordítási folyamat vagy más Build-tartalom szükséges.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a kód a fejlesztői tárhelyen való futtatásáról, tekintse meg a [kód futtatása az Azure dev Spaces szolgáltatással][how-it-works-up]című témakört.

[how-it-works-up]: how-dev-spaces-works-up.md