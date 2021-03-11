---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620076"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](../../how-to-create-immersive-reader.md) .  A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Git](https://git-scm.com/).
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) és [pip](https://docs.python.org/3/installing/index.html). A Python 3,4-től kezdődően a pip alapértelmezés szerint a Python bináris telepítők részét képezi.
* [Lombik](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtualenv](https://virtualenv.pypa.io/en/latest/) és [virtualenvwrapper-Win for Windows](https://pypi.org/project/virtualenvwrapper-win/) vagy [virtualenvwrapper for OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* A [kérelmek modulja](https://pypi.org/project/requests/2.7.0/).
* IDE, például a [Visual Studio Code](https://code.visualstudio.com/)-hoz.

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

Hozzon létre egy **. env** nevű új fájlt, és illessze be a következő neveket és értékeket. Adja meg azokat az értékeket, amelyeket a magától elolvasó erőforrás létrehozásakor adott meg.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.

Védje a **getimmersivereadertoken** API-végpontot a hitelesítés valamilyen formája mögött, például [OAuth](https://oauth.net/2/). A hitelesítés megakadályozza, hogy a jogosulatlan felhasználók megkapják a jogkivonatokat a magával ragadó olvasó szolgáltatás és a számlázás ellen. Ez a munka meghaladja az oktatóanyag hatókörét.

## <a name="create-a-python-web-app-on-windows"></a>Python-Webalkalmazás létrehozása Windows rendszeren

Hozzon létre egy Python-webalkalmazást `flask` a használatával Windows rendszeren.

Telepítse a [git](https://git-scm.com/)-t.

A git telepítése után nyisson meg egy parancssort, és a számítógép egy mappájába klónozással nyissa meg az olvasói SDK git-tárházat.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Telepítse a [Pythont](https://www.python.org/downloads/).

Jelölje be a **Python – elérési út hozzáadása** jelölőnégyzetet.

![Python Windows telepítési párbeszédpanel 1. lépés](../../media/pythoninstallone.jpg)

Vegyen fel **választható szolgáltatásokat** a jelölőnégyzetek bejelölésével, majd válassza a **tovább** lehetőséget.

![Python Windows telepítési párbeszédpanel 2. lépés](../../media/pythoninstalltwo.jpg)

Válassza az **Egyéni telepítés** lehetőséget, és állítsa be a telepítési útvonalat a gyökérmappaként, például: `C:\Python37-32\` . Ezután válassza a **telepítés** lehetőséget.

![Python Windows telepítési párbeszédpanel 3. lépés](../../media/pythoninstallthree.jpg)

A Python telepítésének befejezése után nyisson meg egy parancssort, és a használatával `cd` lépjen a Python-parancsfájlok mappába.

```cmd
cd C:\Python37-32\Scripts
```

A lombik telepítése.

```cmd
pip install flask
```

Telepítse a Jinja2. Ez egy teljes funkcionalitású sablon a Pythonhoz.

```cmd
pip install jinja2
```

Telepítse a virtualenv. Ez az eszköz elkülönített Python-környezeteket hoz létre.

```cmd
pip install virtualenv
```

Telepítse a virtualenvwrapper-Win-t. A virtualenvwrapper mögötti ötlet a virtualenv használatának egyszerűsége.

```cmd
pip install virtualenvwrapper-win
```

Telepítse a kérelmek modult. A kérések egy Pythonban írt, Apache licenccel rendelkező HTTP-könyvtár.

```cmd
pip install requests
```

Telepítse a Python-dotenv modult. Ez a modul beolvassa a kulcs-érték párokat a. env fájlból, és hozzáadja őket a környezeti változóhoz.

```cmd
pip install python-dotenv
```

Virtuális környezet létrehozása.

```cmd
mkvirtualenv advanced-python
```

`cd`A használatával lépjen a minta projekt gyökérkönyvtárára.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

A minta projekt összekötése a környezettel. Ez a művelet leképezi az újonnan létrehozott virtuális környezetet a minta projekt gyökérmappa mappájába.

```cmd
setprojectdir .
```

Aktiválja a virtuális környezetet.

```cmd
activate
```

A projektnek mostantól aktívnak kell lennie, és a parancssorban hasonlóan fog megjelenni `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` .

A környezet inaktiválása.

```cmd
deactivate
```

Az `(advanced-python)` előtagot el kell tűnni, mert a környezet inaktiválva van.

A környezet újraaktiválásához futtassa `workon advanced-python` a mintát a Project root mappából.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Ha a környezet aktív, futtassa a minta projektet a `flask run` minta projekt gyökérkönyvtárának beírásával.

```cmd
flask run
```

Nyissa meg a böngészőt, és keresse fel a következőt: http://localhost:5000 .

## <a name="create-a-python-web-app-on-osx"></a>Python-Webalkalmazás létrehozása az OSX-ben

Hozzon létre egy Python-webalkalmazást az `flask` OSX használatával.

Telepítse a [git](https://git-scm.com/)-t.

A git telepítése után nyissa meg a terminált, és a megfelelő olvasó SDK git-tárházat a számítógép egyik mappájába.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Telepítse a [Pythont](https://www.python.org/downloads/).

A Python gyökérmappa (például `Python37-32` :) mostantól az alkalmazások mappában kell, hogy legyen.

A Python telepítésének befejezése után nyissa meg a terminált, és `cd` a használatával lépjen a Python-parancsfájlok mappába.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Telepítse a pipet.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

A következő kód futtatásával telepítheti a pip-et a jelenleg bejelentkezett felhasználó számára az engedélyek elkerülése érdekében.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Ha a rendszer kéri, adja meg a jelszavát.
- Adja hozzá a pip-telepítés elérési útját az ELÉRÉSIÚT-változóhoz.
- Nyissa meg a fájl alját, és adja meg a lista utolsó elemeként hozzáadni kívánt elérési utat, például: `PATH=$PATH:/usr/local/bin` .
- A kilépéshez válassza a **CTRL + X billentyűkombinációt** .
- A módosított puffer mentéséhez adja meg az **Y** értéket.
- Ennyi az egész! A teszteléshez egy új terminál ablakban írja be a következőt: `echo $PATH` .

A lombik telepítése.

```bash
pip install flask --user
```

Telepítse a Jinja2. Ez egy teljes funkcionalitású sablon a Pythonhoz.

```bash
pip install Jinja2 --user
```

Telepítse a virtualenv. Ez az eszköz elkülönített Python-környezeteket hoz létre.

```bash
pip install virtualenv --user
```

Telepítse a virtualenvwrapper. A virtualenvwrapper mögötti ötlet a virtualenv használatának egyszerűsége.

```bash
pip install virtualenvwrapper --user
```

Telepítse a kérelmek modult. A kérések egy Pythonban írt, Apache licenccel rendelkező HTTP-könyvtár.

```bash
pip install requests --user
```

Telepítse a Python-dotenv modult. Ez a modul beolvassa a kulcs-érték párokat a. env fájlból, és hozzáadja őket a környezeti változóhoz.

```bash
pip install python-dotenv --user
```

Válassza ki a mappát, ahol meg szeretné őrizni a virtuális környezeteket, és futtassa a következő parancsot:

```bash
mkdir ~/.virtualenvs
```

A segítségével `cd` nyissa meg a részletes olvasó SDK Python minta alkalmazás mappáját.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Virtuális környezet létrehozása.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

A minta projekt összekötése a környezettel. Ez a művelet leképezi az újonnan létrehozott virtuális környezetet a minta projekt gyökérmappa mappájába.

```bash
setprojectdir .
```

Aktiválja a virtuális környezetet.

```bash
activate
```

A projektnek mostantól aktívnak kell lennie, és a parancssorban hasonlóan fog megjelenni `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` .

A környezet inaktiválása.

```bash
deactivate
```

Az `(advanced-python)` előtagot el kell tűnni, mert a környezet inaktiválva van.

A környezet újraaktiválásához futtassa `workon advanced-python` a mintát a Project root mappából.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Ha a környezet aktív, futtassa a minta projektet a `flask run` minta projekt gyökérkönyvtárának beírásával.

```bash
flask run
```

Nyissa meg a böngészőt, és keresse fel a következőt: http://localhost:5000 .

## <a name="next-steps"></a>Következő lépések

* Fedezze fel az [olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -t és a [magára ejtő olvasó SDK-referenciát](../../reference.md).
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
