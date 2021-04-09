---
title: Az űrlap-felismerő minta címkézési eszközének üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ismerje meg a felügyelt tanulást segítő űrlap-felismerő minta címkéző eszköz különböző módszereit.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 02/11/2021
ms.author: lajanuar
ms.openlocfilehash: 0f5f0714235ee23624b3a199eac744155d2bbdd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "101093384"
---
# <a name="deploy-the-sample-labeling-tool"></a>A mintacímkézési eszköz üzembe helyezése

Az űrlap-felismerő minta címkéző eszköz egy egyszerű felhasználói felületet (UI) biztosító alkalmazás, amellyel a felügyelt tanulás érdekében manuálisan címkézheti az űrlapokat (dokumentumokat). Ebben a cikkben a következőkre mutató hivatkozásokat és utasításokat mutatjuk be:

* [A minta feliratozási eszköz helyi futtatása](#run-the-sample-labeling-tool-locally)
* [A minta-címkéző eszköz üzembe helyezése Azure Container Instanceban (ACI)](#deploy-with-azure-container-instances-aci)
* [A nyílt forráskódú OCR űrlap feliratozási eszközének használata és hozzájárulása](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>A minta feliratozási eszköz helyi futtatása

Az adatfelirat megkezdésének leggyorsabb módja a minta feliratozási eszköz helyi futtatása. A következő rövid útmutató az űrlap-felismerő REST API és a minta-címkézési eszközt használja az egyéni modell manuális címkézett adattal való betanításához. 

* Rövid útmutató [: űrlapok címkézése, modell betanítása és az űrlap elemzése a minta feliratozási eszköz használatával](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Üzembe helyezés Azure Container Instancessal (ACI)

Mielőtt elkezdenénk, fontos megjegyezni, hogy kétféleképpen lehet üzembe helyezni a minta-címkézési eszközt az Azure Container instance (ACI) szolgáltatásban. Mindkét lehetőség a minta feliratozási eszközének az ACI-val való futtatására szolgál.

* [Az Azure Portal használata](#azure-portal)
* [Az Azure CLI-vel](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Az alábbi lépéseket követve hozzon létre egy új erőforrást a Azure Portal használatával: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/signin/index/).
2. Válassza az **Erőforrás létrehozása** lehetőséget.
3. Ezután válassza a **webalkalmazás** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Webalkalmazás kiválasztása](./media/quickstarts/create-web-app.png)

4. Először is győződjön meg arról, hogy az **alapvető beállítások** lap van kiválasztva. Most meg kell adnia néhány információt:

   > [!div class="mx-imgBorder"]
   > ![Alapismeretek kiválasztása](./media/quickstarts/select-basics.png)
   * Előfizetés – válasszon ki egy meglévő Azure-előfizetést
   * Erőforráscsoport – felhasználhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat ehhez a projekthez. Az új erőforráscsoport létrehozása ajánlott.
   * Név – adja meg a webalkalmazás nevét. 
   * Közzététel – a **Docker-tároló** kiválasztása
   * Operációs rendszer – **Linux** kiválasztása
   * Régió – válasszon egy olyan régiót, amely logikus.
   * Linux-csomag – válassza ki az App Service díjszabási szintjét/tervét. 

   > [!div class="mx-imgBorder"]
   > ![A webalkalmazás konfigurálása](./media/quickstarts/select-docker.png)

5. Ezután válassza a **Docker** fület.

   > [!div class="mx-imgBorder"]
   > ![Docker kiválasztása](./media/quickstarts/select-docker.png)

6. Most konfigurálja a Docker-tárolót. Az összes mező megadása kötelező, kivéve, ha másként jelezzük:
<!-- markdownlint-disable MD025 -->
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

* Beállítások – **egyetlen tároló** kijelölése
* Rendszerkép forrása – **privát beállításjegyzék** kiválasztása 
* Kiszolgáló URL-címe – Itt adhatja meg a következőt: `https://mcr.microsoft.com`
* Username (nem kötelező) – hozzon létre egy felhasználónevet. 
* Password (nem kötelező) – hozzon létre egy biztonságos jelszót, amelyet meg szeretne jegyezni.
* Rendszerkép és címke – ezt állítsa be a következőre: `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview`
* Folyamatos üzembe helyezés – állítsa be ezt **a** be értékre, ha automatikus frissítéseket szeretne kapni, amikor a fejlesztői csapat megváltoztatja a minta feliratozási eszközét.
* Indítási parancs – ezt állítsa be a következőre: `./run.sh eula=accept`

# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)  

* Beállítások – **egyetlen tároló** kijelölése
* Rendszerkép forrása – **privát beállításjegyzék** kiválasztása 
* Kiszolgáló URL-címe – Itt adhatja meg a következőt: `https://mcr.microsoft.com`
* Username (nem kötelező) – hozzon létre egy felhasználónevet. 
* Password (nem kötelező) – hozzon létre egy biztonságos jelszót, amelyet meg szeretne jegyezni.
* Rendszerkép és címke – ezt állítsa be a következőre: `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
* Folyamatos üzembe helyezés – állítsa be ezt **a** be értékre, ha automatikus frissítéseket szeretne kapni, amikor a fejlesztői csapat megváltoztatja a minta feliratozási eszközét.
* Indítási parancs – ezt állítsa be a következőre: `./run.sh eula=accept`

 ---

   > [!div class="mx-imgBorder"]
   > ![A Docker konfigurálása](./media/quickstarts/configure-docker.png)

7. Ennyi az egész. Ezután válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget a webalkalmazás üzembe helyezéséhez. Ha elkészült, elérheti a webalkalmazást az erőforrás **áttekintésében** megadott URL-címen.

> [!NOTE]
> A webalkalmazás létrehozásakor konfigurálhatja az engedélyezést és a hitelesítést is. Ez nem szükséges a kezdéshez.

> [!IMPORTANT]
> Előfordulhat, hogy engedélyeznie kell a TLS-t a webalkalmazáshoz, hogy megtekintse azt a `https` címben. Kövesse az alábbi utasításokat a [TLS-végpont](../../container-instances/container-instances-container-group-ssl.md) beállítása egy oldalkocsi-tároló beállításához, mint a TLS/SSL engedélyezése a webalkalmazáshoz.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

A Azure Portal használatának alternatívájaként az Azure CLI használatával is létrehozhat egy erőforrást. A folytatás előtt telepítenie kell az [Azure CLI](/cli/azure/install-azure-cli)-t. Ezt a lépést kihagyhatja, ha már dolgozik az Azure CLI-vel. 

Itt van néhány dolog, amit tudnia kell a paranccsal kapcsolatban:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` létrehoz egy véletlenszerű DNS-nevet. 
* Ez a példa azt feltételezi, hogy rendelkezik egy olyan erőforráscsoporthoz, amelyet az erőforrások létrehozásához használhat. Helyettesítse be `<resource_group_name>` az előfizetéséhez tartozó érvényes erőforráscsoportot. 
* Meg kell adnia, hogy hová szeretné létrehozni az erőforrást. Cserélje le a `<region name>` alkalmazást a kívánt régióra a webalkalmazáshoz.
* Ez a parancs automatikusan elfogadja a végfelhasználói licencszerződést.

Az Azure CLI-ből futtassa ezt a parancsot egy webalkalmazás-erőforrás létrehozásához a minta feliratozási eszközhöz:

<!-- markdownlint-disable MD024 -->
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)


```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 


---

### <a name="connect-to-azure-ad-for-authorization"></a>Kapcsolódás az Azure AD-hez az engedélyezéshez

Javasoljuk, hogy a webalkalmazást Azure Active Directoryhoz kapcsolja. Ez biztosítja, hogy csak érvényes hitelesítő adatokkal rendelkező felhasználók jelentkezhetnek be és használhatják a webalkalmazást. A Azure Active Directoryhoz való kapcsolódáshoz kövesse az [app Service alkalmazás konfigurálása](../../app-service/configure-authentication-provider-aad.md) című témakör utasításait.

## <a name="open-source-on-github"></a>Nyílt forráskód a GitHubon

Az OCR űrlap címkéző eszköze nyílt forráskódú projektként is elérhető a GitHubon. Az eszköz egy reakciós + Redux használatával létrehozott webalkalmazás, amely írógéppel van írva. További részletekért lásd: [OCR űrlap címkézése eszköz](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Következő lépések

[A betanítási](./quickstarts/label-tool.md) útmutató segítségével megtudhatja, hogyan használhatja az eszközt a betanítási adatok manuális címkézéséhez és a felügyelt tanulás végrehajtásához.