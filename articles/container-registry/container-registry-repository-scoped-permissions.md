---
title: Engedélyek a Azure Container Registry adattárakhoz
description: Hozzon létre egy jogkivonatot, amely a prémium szintű beállításjegyzék adott tárházára vonatkozik a képek lekéréséhez vagy leküldéséhez, illetve egyéb műveletek végrehajtásához.
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: ceec69d746f77ea7a23bc70d029c8b3736e7f292
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988254"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Token létrehozása adattárral hatókörrel rendelkező engedélyekkel

Ez a cikk azt ismerteti, hogyan hozhat létre jogkivonatokat és hatókör-leképezéseket a tároló-beállításjegyzékben lévő adott adattárakhoz való hozzáférés kezeléséhez. A jogkivonatok létrehozásával a beállításjegyzék tulajdonosa a felhasználók vagy szolgáltatások hatókörön belüli, időkorlátos hozzáférését biztosíthatja a Tárházak számára a képek lekéréséhez vagy leküldéséhez, illetve egyéb műveletek végrehajtásához. A tokenek részletesebb engedélyeket biztosítanak, mint a beállításjegyzék más [hitelesítési beállításai](container-registry-authentication.md), amelyek hatóköre a teljes beállításjegyzékre érvényes. 

Tokenek létrehozásához a következő forgatókönyvek tartoznak:

* IoT-eszközök egyéni jogkivonatokkal való lekérésének engedélyezése egy adattárból
* Adjon meg egy olyan külső szervezetet, amely rendelkezik engedélyekkel egy adott tárházhoz 
* Korlátozza a tárház hozzáférését a szervezet különböző felhasználói csoportjaihoz. Adjon meg például írási és olvasási hozzáférést azoknak a fejlesztőknek, akik adott adattárakra irányuló képeket készítenek, és olvasási hozzáférést biztosítanak az ezekből a tárházból üzembe helyezett csapatokhoz.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Jelenleg nem rendelhet hozzá tárház hatókörű engedélyeket egy Azure Active Directory identitáshoz, például egy egyszerű szolgáltatásnév vagy egy felügyelt identitáshoz.
* Nem hozhat létre hatókör-leképezést egy olyan beállításjegyzékben, amelyen engedélyezve van a [Névtelen lekéréses hozzáférés](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Alapelvek

A tárház hatókörű engedélyeinek konfigurálásához hozzon létre egy *jogkivonatot* egy társított *hatókör-térképpel*. 

* A generált jelszóval ellátott **tokenek** lehetővé teszi, hogy a felhasználó hitelesítse magát a beállításjegyzékben. Megadhat egy jogkivonat-jelszó lejárati dátumát, vagy bármikor letilthatja a tokent.  

  A jogkivonat használatával végzett hitelesítés után a felhasználó vagy a szolgáltatás egy *vagy több tárházra hatókört* is végrehajthat.

  |Művelet  |Leírás  | Példa |
  |---------|---------|--------|
  |`content/delete`    | Adatok eltávolítása az adattárból  | Adattár vagy jegyzékfájl törlése |
  |`content/read`     |  Adatok beolvasása az adattárból |  Összetevő lekérése |
  |`content/write`     |  Adatgyűjtés az adattárba     | A és a használata az összetevők `content/read` leküldéséhez |
  |`metadata/read`    | Metaadatok olvasása a tárházból   | Címkék vagy jegyzékfájlok listázása |
  |`metadata/write`     |  Metaadatok írása a tárházba  | Olvasási, írási és törlési műveletek engedélyezése vagy letiltása |

* A **hatókör-hozzárendelési** csoportok a jogkivonatra alkalmazott adattár-engedélyeket, és más jogkivonatokra is vonatkozhatnak. Minden jogkivonat társítva van egyetlen hatókör-leképezéssel. 

   Hatókör-hozzárendeléssel:

    * Több, azonos engedélyekkel rendelkező token konfigurálása Tárházak számára
    * Jogkivonat-engedélyek frissítése a hatókör-leképezésben szereplő adattár-műveletek hozzáadásakor vagy eltávolításakor, vagy egy másik hatókör-hozzárendelés alkalmazása 

  A Azure Container Registry számos, a rendszeren definiált hatókör-leképezést is biztosít, amelyek a jogkivonatok létrehozásakor alkalmazhatók. A rendszer által meghatározott hatókör-leképezések engedélyei a beállításjegyzékben található összes tárházra érvényesek.

Az alábbi képen a tokenek és a hatóköri térképek közötti kapcsolat látható. 

![Beállításjegyzék-tokenek és hatókör-leképezések](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Előfeltételek

* **Azure CLI** – Azure CLI-parancsok parancs a cikkben szereplő példákhoz az Azure CLI 2.17.0 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* **Docker** – a rendszerképek lekéréséhez vagy leküldéséhez szükséges beállításjegyzékbeli hitelesítéshez helyi Docker-telepítésre van szükség. A Docker a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszerhez biztosít telepítési utasításokat.
* **Container Registry** – ha még nem rendelkezik ilyennel, hozzon létre egy prémium szintű tároló-beállításjegyzéket az Azure-előfizetésében, vagy frissítsen egy meglévőt. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t. 

## <a name="create-token---cli"></a>Jogkivonat létrehozása – parancssori felület

### <a name="create-token-and-specify-repositories"></a>Jogkivonat létrehozása és adattárak meghatározása

Hozzon létre egy jogkivonatot az az [ACR token Create][az-acr-token-create] parancs használatával. Token létrehozásakor megadhat egy vagy több tárházat és a hozzájuk társított műveleteket az egyes adattárakban. A Tárházak még nem szükségesek a beállításjegyzékben. Ha egy meglévő hatókör-hozzárendelés megadásával szeretne tokent létrehozni, tekintse meg a [következő szakaszt](#create-token-and-specify-scope-map).

A következő példa egy jogkivonatot hoz létre a beállításjegyzékben a *myregistry* a következő engedélyekkel `samples/hello-world` : `content/write` és `content/read` . Alapértelmezés szerint a parancs az alapértelmezett jogkivonat-állapotot állítja be `enabled` , de bármikor frissítheti az állapotot `disabled` .

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A kimenet a jogkivonat részleteit jeleníti meg. Alapértelmezés szerint két jelszó jön létre, amelyek nem járnak le, de igény szerint megadhat egy lejárati dátumot is. Javasoljuk, hogy a jelszavakat biztonságos helyen mentse, hogy később is használhassa a hitelesítést. A jelszavakat nem lehet újból beolvasni, de újakat lehet létrehozni.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> A jogkivonat-jelszavak és a lejárati időszakok újralétrehozásáról a jelen cikk későbbi, a [jogkivonat-jelszavak újbóli előállítása](#regenerate-token-passwords) című cikkben olvashat

A kimenet tartalmazza a parancs által létrehozott hatókör-hozzárendelés részleteit. A (z) nevű hatókör-leképezést használhatja `MyToken-scope-map` arra, hogy ugyanazokat a tárház-műveleteket alkalmazza más jogkivonatokra. Vagy frissítse a hatókör-leképezést később, hogy megváltoztassa a társított jogkivonatok engedélyeit.

### <a name="create-token-and-specify-scope-map"></a>Jogkivonat létrehozása és a hatókör-hozzárendelés meghatározása

A jogkivonatok létrehozásának másik módja egy meglévő hatókör-hozzárendelés megadására szolgál. Ha még nem rendelkezik hatókör-leképezéssel, először hozzon létre egyet a Tárházak és a társított műveletek megadásával. Ezután adja meg a hatókör-leképezést jogkivonat létrehozásakor. 

Hatókör-hozzárendelés létrehozásához használja az az [ACR scope-Map Create][az-acr-scope-map-create] parancsot. A következő parancs egy hatókör-hozzárendelést hoz létre ugyanazzal az engedélyekkel a `samples/hello-world` korábban használt adattárhoz. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Az [az ACR token Create][az-acr-token-create] paranccsal hozzon létre egy jogkivonatot, és adja meg a *MyScopeMap* hatókör-leképezését. Ahogy az előző példában is látható, a parancs az alapértelmezett jogkivonat-állapotot állítja be értékre `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A kimenet a jogkivonat részleteit jeleníti meg. Alapértelmezés szerint két jelszó jön létre. Javasoljuk, hogy a jelszavakat biztonságos helyen mentse, hogy később is használhassa a hitelesítést. A jelszavakat nem lehet újból beolvasni, de újakat lehet létrehozni.

> [!NOTE]
> A jogkivonat-jelszavak és a lejárati időszakok újralétrehozásáról a jelen cikk későbbi, a [jogkivonat-jelszavak újbóli előállítása](#regenerate-token-passwords) című cikkben olvashat

## <a name="create-token---portal"></a>Jogkivonat létrehozása – portál

A Azure Portal a tokenek és a hatókör-leképezések létrehozására használható. A CLI- `az acr token create` parancshoz hasonlóan alkalmazhat meglévő hatókör-leképezést is, vagy létrehozhat egy hatókör-hozzárendelést, ha tokent hoz létre egy vagy több tárház és társított művelet megadásával. A Tárházak még nem szükségesek a beállításjegyzékben. 

A következő példa létrehoz egy jogkivonatot, és létrehoz egy hatókör-leképezést a következő engedélyekkel az `samples/hello-world` adattárhoz: `content/write` és `content/read` .

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **tárház engedélyei** területen válassza a **tokenek (előzetes verzió) > + Hozzáadás** lehetőséget.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Token létrehozása a portálon":::
1. Adja meg a jogkivonat nevét.
1. A **hatókör-hozzárendelés** területen válassza az **új létrehozása** lehetőséget.
1. A hatókör-leképezés konfigurálása:
    1. Adja meg a hatókör-hozzárendelés nevét és leírását. 
    1. A **Tárházak** területen adja meg a `samples/hello-world` és az **engedélyek** területen a és a elemet  `content/read` `content/write` . Ezután válassza a **+ Hozzáadás** lehetőséget.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Hatókör-hozzárendelés létrehozása a portálon":::

    1. A Tárházak és engedélyek hozzáadása után válassza a **Hozzáadás** lehetőséget a hatókör-hozzárendelés hozzáadásához.
1. Fogadja el az **engedélyezett** alapértelmezett jogkivonat- **állapotot** , majd válassza a **Létrehozás** lehetőséget.

A jogkivonat ellenőrzése és létrehozása után a jogkivonat részletei megjelennek a **jogkivonatok képernyőjén** .

### <a name="add-token-password"></a>Jogkivonat-jelszó hozzáadása

A portálon létrehozott jogkivonat használatához jelszót kell létrehoznia. Létrehozhat egy vagy két jelszót, és beállíthat egy lejárati dátumot. 

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **tárház engedélyei** területen válassza a **tokenek (előzetes verzió)** lehetőséget, és válasszon ki egy jogkivonatot.
1. A jogkivonat részletei között válassza a **jelszó1** vagy a **password2** lehetőséget, majd válassza a létrehozás ikont.
1. A jelszó képernyőn opcionálisan beállíthat egy lejárati dátumot a jelszóhoz, és válassza a **Létrehozás** lehetőséget. A lejárati dátum beállítása ajánlott.
1. A jelszó létrehozása után másolja és mentse egy biztonságos helyre. A képernyő bezárása után nem kérhető le generált jelszó, de létrehozhat egy újat.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Jogkivonat-jelszó létrehozása a portálon":::

## <a name="authenticate-with-token"></a>Hitelesítés jogkivonat használatával

Ha egy felhasználó vagy szolgáltatás jogkivonatot használ a célként megadott beállításjegyzékben való hitelesítéshez, a jogkivonat nevét felhasználóneveként és annak egy generált jelszavával adja meg. 

A hitelesítési módszer a tokenhez társított konfigurált művelettől vagy művelettől függ.

|Művelet  |Hitelesítés  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` Az Azure CLI-ben<br/><br/>Például: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` Az Azure CLI-ben<br/><br/>Például: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` Az Azure CLI-ben     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` Az Azure CLI-ben   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` Az Azure CLI-ben |

## <a name="examples-use-token"></a>Példák: token használata

Az alábbi példák a cikkben korábban létrehozott jogkivonatot használják a gyakori műveletek végrehajtásához egy adattáron: leküldéses és lekéréses lemezképek, lemezképek törlése és a lista tára címkék. A token kezdetben a leküldéses engedélyekkel ( `content/write` és `content/read` műveletekkel) lett beállítva a `samples/hello-world` tárházban.

### <a name="pull-and-tag-test-images"></a>Tesztelési lemezképek lekérése és címkézése

Az alábbi példákban lehívhatja a nyilvános `hello-world` és a `nginx` rendszerképeket a Microsoft Container Registryból, és címkézheti azokat a beállításjegyzékben és a tárházban.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Hitelesítés token használatával

Futtassa `docker login` a vagy a parancsot `az acr login` a beállításjegyzékben a lemezképek leküldéséhez vagy lekéréséhez. Adja meg a jogkivonat nevét felhasználónévként, és adjon meg egy jelszót. A tokennek rendelkeznie kell az `Enabled` állapottal.

A következő példa a bash-rendszerhéjhoz van formázva, és környezeti változók használatával biztosítja az értékeket.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A kimenetnek sikeres hitelesítést kell mutatnia:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

A sikeres bejelentkezést követően próbálja meg leküldeni a címkézett képeket a beállításjegyzékbe. Mivel a jogkivonat jogosult a lemezképek adattárba való leküldésére `samples/hello-world` , a következő leküldéses művelet sikeres:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A jogkivonat nem rendelkezik jogosultsággal a tárházhoz `samples/nginx` , ezért a következő leküldéses kísérlet egy hasonló hibával meghiúsul `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Jogkivonat-engedélyek frissítése

Egy jogkivonat engedélyeinek frissítéséhez frissítse az engedélyeket a társított hatókör-térképen. A frissített hatókör-leképezést a rendszer azonnal alkalmazza az összes társított jogkivonatra. 

Például frissítsen a `MyToken-scope-map` `content/write` és a `content/read` műveletekkel az `samples/ngnx` adattáron, és távolítsa el a `content/write` műveletet az `samples/hello-world` adattáron.  

Az Azure CLI használatához futtassa az az [ACR scope-Map Update][az-acr-scope-map-update] parancsot a hatókör-hozzárendelés frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

Az Azure Portalon:

1. Navigáljon a tároló-beállításjegyzékhez.
1. A **tárház engedélyei** területen válassza a **hatókör-térképek (előzetes verzió)** lehetőséget, majd válassza ki a frissíteni kívánt hatókör-leképezést.
1. A **Tárházak** területen adja meg a `samples/nginx` és az **engedélyek** területen a és a elemet `content/read` `content/write` . Ezután válassza a **+ Hozzáadás** lehetőséget.
1. A **Tárházak** területen válassza az `samples/hello-world` **engedélyek**, majd a kijelölés elemet `content/write` . Kattintson a **Mentés** gombra.

A hatókör-hozzárendelés frissítése után a következő leküldése sikeres:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Mivel a hatókör-hozzárendelés csak az `content/read` `samples/hello-world` adattárhoz rendelkezik engedéllyel, a tárház leküldéses kísérlete `samples/hello-world` most meghiúsul:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A lemezképek mindkét adattárakból való kihúzása sikeres, mert a hatókör-hozzárendelés mindkét adattárhoz biztosít `content/read` engedélyeket:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Rendszerképek törlése

Frissítse a hatókör-leképezést úgy `content/delete` , hogy hozzáadja a műveletet a `nginx` tárházhoz. Ez a művelet lehetővé teszi a lemezképek törlését a tárházban, vagy a teljes tárház törlését.

Rövid ideig csak az [az ACR scope-Map Update][az-acr-scope-map-update] parancs jelenik meg a hatókör-hozzárendelés frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

A hatókör-leképezés a portál használatával történő frissítéséhez tekintse meg az [előző szakaszt](#update-token-permissions).

A tárház törléséhez használja a következő az [ACR repository delete][az-acr-repository-delete] parancsot `samples/nginx` . Képek vagy adattárak törléséhez adja át a token nevét és jelszavát a parancsnak. A következő példa a cikkben korábban létrehozott környezeti változókat használja:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Tárház-címkék megjelenítése 

Frissítse a hatókör-leképezést úgy `metadata/read` , hogy hozzáadja a műveletet a `hello-world` tárházhoz. Ez a művelet lehetővé teszi a jegyzékfájlok és a címkézett adattárban lévő információk olvasását.

Rövid ideig csak az [az ACR scope-Map Update][az-acr-scope-map-update] parancs jelenik meg a hatókör-hozzárendelés frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

A hatókör-leképezés a portál használatával történő frissítéséhez tekintse meg az [előző szakaszt](#update-token-permissions).

A tárház metaadatainak olvasásához `samples/hello-world` futtassa az az [ACR repository show-Manifests][az-acr-repository-show-manifests] vagy [az ACR repository show-Tags][az-acr-repository-show-tags] parancsot. 

A metaadatok olvasásához adja át a token nevét és jelszavát bármelyik parancsnak. A következő példa a cikkben korábban létrehozott környezeti változókat használja:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Példa a kimenetre:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Jogkivonatok és hatóköri leképezések kezelése

### <a name="list-scope-maps"></a>Lista hatókörének leképezése

Használja az az [ACR scope-Térkép List][az-acr-scope-map-list] parancsot, vagy a portál **hatókör-térképek (előzetes verzió)** képernyőjét a beállításjegyzékben konfigurált összes hatókör-leképezés listázásához. Például:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A kimenet a rendszer által létrehozott három hatókör-leképezésből és más, Ön által generált hatókör-térképekből áll. A jogkivonatok ezen hatókör-leképezések bármelyikével konfigurálhatók.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Jogkivonat részleteinek megjelenítése

Ha meg szeretné tekinteni a token részleteit, például az állapotát és a jelszó lejárati dátumát, futtassa az az [ACR token show][az-acr-token-show] parancsot, vagy válassza ki a tokent a portál **tokenek (előzetes verzió)** képernyőjén. Például:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

A portálon az az [ACR token List][az-acr-token-list] parancs vagy a **tokens (előnézet)** képernyő használatával listázhatja a beállításjegyzékben konfigurált összes jogkivonatot. Például:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Jogkivonat-jelszavak újbóli előállítása

Ha nem generált jogkivonat-jelszót, vagy új jelszavakat szeretne előállítani, futtassa az az [ACR token hitelesítő adatok létrehozása][az-acr-token-credential-generate] parancsot. 

Az alábbi példa új értéket hoz létre a *MyToken* -token jelszó1, amelynek lejárati időtartama 30 nap. A jelszót a környezeti változóban tárolja `TOKEN_PWD` . Ez a példa a bash-rendszerhéjhoz van formázva.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Ha a Azure Portalt szeretné használni a jogkivonat jelszavának létrehozásához, tekintse meg a jelen cikk a [Jogkivonat létrehozása – portál](#create-token---portal) című szakaszának lépéseit.

### <a name="update-token-with-new-scope-map"></a>Jogkivonat frissítése új hatókör-hozzárendeléssel

Ha másik hatókör-leképezéssel rendelkező tokent szeretne frissíteni, futtassa az [az ACR token Update][az-acr-token-update] parancsot, és adja meg az új hatókör-leképezést. Például:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

A portálon a **tokenek (előzetes verzió)** képernyőn válassza ki a tokent, és a **hatókör térképe** területen válasszon egy másik hatókör-leképezést.

> [!TIP]
> Miután frissített egy jogkivonatot egy új hatókör-térképpel, új jogkivonat-jelszavakat szeretne előállítani. Használja az az [ACR token regenerált][az-acr-token-credential-generate] parancsot, vagy a Azure Portalban regenerált jogkivonat-jelszót.

## <a name="disable-or-delete-token"></a>Jogkivonat letiltása vagy törlése

Előfordulhat, hogy átmenetileg le kell tiltania a token hitelesítő adatainak használatát egy adott felhasználóhoz vagy szolgáltatáshoz. 

Az Azure CLI használatával az az [ACR token Update][az-acr-token-update] paranccsal állíthatja be a következőt `status` `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

A portálon válassza ki a jogkivonatot a **tokenek (előzetes verzió)** képernyőn, és válassza a **Letiltva** **állapotot**.

Ha törölni szeretne egy jogkivonatot, hogy a hitelesítő adatai alapján bárki véglegesen érvénytelenítse a hozzáférést, futtassa az az [ACR token delete][az-acr-token-delete] parancsot. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

A portálon válassza ki a tokent a **jogkivonatok (előzetes verzió)** képernyőn, és válassza az **Elvetés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* A hatóköri térképek és tokenek kezeléséhez használja az az [ACR scope-Map][az-acr-scope-map] és [az ACR token][az-acr-token] Command groups további parancsait.
* Az Azure Container Registry szolgáltatással történő hitelesítéssel kapcsolatos további beállításokért tekintse meg a [hitelesítés áttekintését](container-registry-authentication.md) , beleértve a Azure Active Directory identitást, egy szolgáltatásnevet vagy egy rendszergazdai fiókot.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
