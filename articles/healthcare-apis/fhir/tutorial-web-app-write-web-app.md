---
title: Web App-oktatóanyag – webalkalmazás írása
description: Ez az oktatóanyag végigvezeti egy egyszerű webalkalmazás üzembe helyezésének példáján. Az oktatóanyag ezen szakasza végigvezeti a webalkalmazás megírásán.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019483"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Azure-webalkalmazás írása FHIR-alapú adatolvasásra
Most, hogy tud csatlakozni a FHIR-kiszolgálóhoz, és közzéteszi az adatküldést, készen áll arra, hogy olyan webalkalmazást írjon, amely beolvassa a FHIR adatait. Az oktatóanyag ezen utolsó lépésében bemutatjuk a webalkalmazás írását és elérését.

## <a name="create-web-application"></a>Webalkalmazás létrehozása
Az Azure-ban válassza az **erőforrás létrehozása** elemet, és válassza a **webalkalmazás** lehetőséget. Ügyeljen arra, hogy a webalkalmazást az ügyfélalkalmazás átirányítási URI-ja alapján adja meg, vagy térjen vissza, és frissítse az átirányítási URI-t az új névvel. 

![Webalkalmazás létrehozása](media/tutorial-web-app/create-web-app.png)

A webalkalmazás elérhetővé tétele után **nyissa meg az erőforrást**. Válassza a jobb oldali Fejlesztőeszközök területen a **app Service Editor (előzetes verzió)** lehetőséget, majd válassza az **Indítás** lehetőséget. Az indítás lehetőség kiválasztásával megnyílik a App Service Editor. Kattintson a jobb gombbal a szürke területre a *Tallózás* területen, és hozzon létre egy új, **index.html** nevű fájlt.

Alább látható a **index.html**-be bevitt kód. A következő elemeket kell frissítenie:
* **clientId** – frissítsen az ügyfélalkalmazás azonosítójával. Ez az azonosító a token beolvasása során kihúzott azonosító lesz
* **hitelesítésszolgáltató** – az Azure ad-bérlő azonosítójának frissítése
* **FHIRendpoint** – a FHIRendpoint frissítése, hogy a FHIR-szolgáltatás neve
* **hatókörök** – a frissítés a célközönség teljes URL-címének megjelenítéséhez

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

Innen visszatérhet a webalkalmazás-erőforráshoz, és megnyithatja az Áttekintés oldalon található URL-címet. Jelentkezzen be, hogy megtekintse a korábban létrehozott James Tiberious Kirk beteget.

## <a name="next-steps"></a>Következő lépések
Sikeresen telepítette a FHIR készült Azure API-t, regisztrált egy nyilvános ügyfélalkalmazás-alkalmazást, tesztelt hozzáférést, és létrehozott egy kis webalkalmazást. A következő lépésként tekintse meg az Azure API FHIR által támogatott funkciókat.

>[!div class="nextstepaction"]
>[Támogatott szolgáltatások](fhir-features-supported.md)





