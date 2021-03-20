---
title: Az Internet Analyzer-ügyfél beágyazása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan ágyazhat be az Internet Analyzer JavaScript-ügyfelet az alkalmazásba.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84744051"
---
# <a name="embed-the-internet-analyzer-client"></a>Az Internet Analyzer-ügyfél beágyazása

Ez a cikk bemutatja, hogyan ágyazhatja be a JavaScript-ügyfelet az alkalmazásba. Az ügyfél telepítése szükséges a tesztek futtatásához és a scorecard-elemzés fogadásához. **A profil-specifikus JavaScript-ügyfél az első teszt beállítása után van megadva.** Ettől kezdve továbbra is hozzáadhat vagy eltávolíthat teszteket ehhez a profilhoz anélkül, hogy új parancsfájlt kellene beágyaznia. Az Internet Analyzer szolgáltatással kapcsolatos további információkért tekintse meg az [áttekintést](internet-analyzer-overview.md). 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

Az Internet Analyzer megfelelő működéséhez hozzáférést igényel az Azure-hoz és más Microsoft-szolgáltatásokhoz. `fpc.msedge.net`Az ügyfél beágyazásához engedélyezze a hálózati hozzáférést és az előre konfigurált végponti URL-címeket (a [CLI](internet-analyzer-cli.md)-n keresztül látható).

## <a name="find-the-client-script-url"></a>Az ügyfél-parancsfájl URL-címének megkeresése

A parancsfájl URL-címe az Azure Portalon vagy az Azure CLI-n keresztül érhető el a tesztek konfigurálása után. További információt az [Internet Analyzer-erőforrás létrehozása](internet-analyzer-create-test-portal.md)című témakörben talál.

1. lehetőség: A Azure Portal használja ezt a [hivatkozást](https://aka.ms/InternetAnalyzerPreviewPortal) az Azure Internet Analyzer betekintő portál lapjának megnyitásához. Nyissa meg az Internet Analyzer-profilt, és tekintse meg a parancsfájl URL-címét a **beállítások > konfiguráció menüpontban**.

2. lehetőség: Az Azure CLI használatával keresse meg a `scriptFileUri` tulajdonságot.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Ügyfél adatai

A szkriptet kifejezetten a profiljához és a tesztekhez hozza létre a rendszer. A betöltést követően a parancsfájl 2 másodperces késleltetéssel fog elindulni. Először is kapcsolatba lép az Internet Analyzer szolgáltatással, hogy beolvassa a tesztek során konfigurált végpontok listáját. Ezután futtatja a méréseket, és feltölti az időzített eredményeket az Internet Analyzer szolgáltatásba.

## <a name="client-examples"></a>Ügyfél-példák

Ezek a példák néhány alapszintű módszert mutatnak be a JavaScript-ügyfél weboldalára vagy alkalmazásba való beágyazásához. `0bfcb32638b44927935b9df86dcfe397`A szkript URL-címéhez példaként használjuk a profil azonosítóját.

### <a name="run-on-page-load"></a>Futtatás oldalon betöltés
A legegyszerűbb módszer a script címke használata a meta tag blokkon belül. Ez a címke egy oldal betöltése után hajtja végre a parancsfájlt.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Következő lépések

Az [Internet Analyzer gyakori kérdéseinek](internet-analyzer-faq.md) beolvasása
