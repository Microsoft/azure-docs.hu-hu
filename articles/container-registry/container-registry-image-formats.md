---
title: Támogatott tartalom-formátumok
description: További információ a Azure Container Registry által támogatott fájlformátumokról, beleértve a Docker-kompatibilis tároló lemezképeit, a Helm-diagramokat, a OCI-lemezképeket és a OCI-összetevőket.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: b2a54c65d149a27ed9eae85c3308d657ed3471a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008332"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry támogatott tartalom-formátumok

A következő tartalom-formátumok egyikének kezeléséhez használjon Azure Container Registry saját tárházát. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibilis tároló lemezképei

A következő Docker-tároló képformátumai támogatottak:

* [Docker-rendszerkép jegyzékfájlja v2, 1. séma](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-rendszerkép jegyzékfájl v2, 2. séma](https://docs.docker.com/registry/spec/manifest-v2-2/) – olyan jegyzékeket tartalmaz, amelyek lehetővé teszik, hogy a kibocsátásiegység-forgalmi jegyzékek [több architektúrát tartalmazó lemezképeket](push-multi-architecture-images.md) tároljanak egyetlen `image:tag` hivatkozás alatt

## <a name="oci-images"></a>OCI-lemezképek

Azure Container Registry támogatja a [nyílt tároló kezdeményezés (OCI) képformátumának specifikációjának](https://github.com/opencontainers/image-spec/blob/master/spec.md)megfelelő lemezképeket, beleértve a választható [képindex](https://github.com/opencontainers/image-spec/blob/master/image-index.md) specifikációt is. A csomagolási formátumok közé tartozik a [szingularitás képformátuma (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI összetevők

Azure Container Registry támogatja a [OCI-terjesztési specifikációt](https://github.com/opencontainers/distribution-spec), a szállítói semleges, a felhő-agnosztikus specifikációt a tároló-lemezképek és más tartalomtípusok (összetevők) tárolására, megosztására, védelmére és üzembe helyezésére. A specifikáció lehetővé teszi, hogy a beállításjegyzék az összetevők széles körét tárolja a tároló rendszerképein kívül. Az összetevőnek megfelelő eszközöket kell használnia az összetevők leküldéséhez és lekéréséhez. Példaként tekintse meg az [OCI-összetevő leküldése és lekérése egy Azure Container Registry használatával](container-registry-oci-artifacts.md)című témakört.

Ha többet szeretne megtudni a OCI összetevőkről, tekintse meg a [OCI beállításjegyzék as Storage (ORAS)](https://github.com/deislabs/oras) tárházát és a [OCI](https://github.com/opencontainers/artifacts) -összetevők tárházát a githubon.

## <a name="helm-charts"></a>Helm-diagramok

A Azure Container Registry képes a Helm- [diagramok](https://helm.sh/), a Kubernetes alkalmazások gyors kezelésére és üzembe helyezésére használt csomagolási formátum tárolására. A [Helm Client](https://docs.helm.sh/using_helm/#installing-helm) 2-es verziója (2.11.0 vagy újabb) támogatott.

## <a name="next-steps"></a>Következő lépések

* Lásd: lemezképek [leküldése és lekérése](container-registry-get-started-docker-cli.md) Azure Container Registry használatával.

* Az [ACR-feladatok](container-registry-tasks-overview.md) használatával készíthet és tesztelheti a tárolók lemezképeit. 

* A [Moby BUILDKIT](https://github.com/moby/buildkit) OCI formátumban hozhat létre és csomagolhat tárolókat.

* Hozzon létre egy Azure Container Registry-ben üzemeltetett [Helm-tárházat](container-registry-helm-repos.md) . 


