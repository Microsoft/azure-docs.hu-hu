---
title: EmptyDir-kötet csatlakoztatása a tároló csoportjához
description: Megtudhatja, hogyan csatlakoztathat egy emptyDir-kötetet a tárolók közötti adatmegosztáshoz Azure Container Instances
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77117740"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>EmptyDir-kötet csatlakoztatása Azure Container Instances

Megtudhatja, hogyan csatlakoztathat egy *emptyDir* -kötetet a tárolók közötti adatmegosztáshoz Azure Container Instancesban. A *emptyDir* -kötetek ideiglenes gyorsítótárként használhatók a tároló munkaterhelésekhez.

> [!NOTE]
> A *emptyDir* -kötet csatlakoztatása jelenleg csak Linux-tárolók számára engedélyezett. Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, az [áttekintésben](container-instances-overview.md#linux-and-windows-containers)megtalálhatja az aktuális platformmal kapcsolatos különbségeket.

## <a name="emptydir-volume"></a>emptyDir-kötet

A *emptyDir* kötet egy írható könyvtárat biztosít a tárolók egyes tárolói számára. A csoportban található tárolók elolvashatják és leírják a köteten található fájlokat, és az egyes tárolók azonos vagy eltérő elérési útjaival is csatlakoztathatók.

Néhány példa a *emptyDir* -kötetek használatára:

* Üres terület
* Ellenőrzőpont a hosszan futó feladatok során
* Az oldalkocsis tároló által lekért és az alkalmazás-tároló által kiszolgált adattároló

A *emptyDir* -kötetben lévő adatmennyiséget a tároló összeomlik. Az újraindított tárolók azonban nem garantáltak, hogy az *emptyDir* -köteten tárolt adatmennyiséget megőrzik. Ha leállítja a tároló csoportot, a *emptyDir* kötet nem marad meg.

A Linux *emptyDir* -kötetek maximális mérete 50 GB.

## <a name="mount-an-emptydir-volume"></a>EmptyDir-kötet csatlakoztatása

Ha emptyDir-kötetet szeretne csatlakoztatni egy tároló-példányban, [Azure Resource Manager sablonnal](/azure/templates/microsoft.containerinstance/containergroups), YAML- [fájllal](container-instances-reference-yaml.md)vagy más programozott módszerrel telepítheti a tárolókat.

Először töltse `volumes` fel a tömböt a fájl tároló csoport `properties` szakaszában. Ezután minden olyan tárolóhoz, amelyben a *emptyDir* -kötetet csatlakoztatni szeretné, töltse ki a `volumeMounts` tömböt a `properties` tároló definíciójának szakaszában.

A következő Resource Manager-sablon például egy két tárolóból álló tároló csoportot hoz létre, amelyek mindegyike a *emptyDir* -kötetet csatlakoztatja:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

A Container Group telepítésére vonatkozó példákat lásd: [többtárolós csoport üzembe helyezése Resource Manager-sablonnal](container-instances-multi-container-group.md) és [egy többtárolós csoport üzembe helyezése YAML-fájl használatával](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Következő lépések

További mennyiségi típusok csatlakoztatása a Azure Container Instancesban:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [Gitrepo típusú-kötet csatlakoztatása Azure Container Instances](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása Azure Container Instances](container-instances-volume-secret.md)