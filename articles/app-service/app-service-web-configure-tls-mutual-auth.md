---
title: TLS kölcsönös hitelesítés beállítása
description: Ismerje meg, hogyan hitelesítheti az Ügyféltanúsítványok tanúsítványait a TLS-ben. A Azure App Service az ügyféltanúsítvány számára elérhetővé teheti az ügyféltanúsítványt az ellenőrzéshez.
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.topic: article
ms.date: 12/11/2020
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 6ceeb3d31652c04eb9a69c1c8bb4b114e6f38d52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347725"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>A TLS kölcsönös hitelesítésének konfigurálása az Azure App Service számára

A Azure App Service-alkalmazáshoz való hozzáférést a különböző típusú hitelesítés engedélyezésével korlátozhatja. Az egyik módszer az, ha az ügyfél kérelmét a TLS/SSL protokollon keresztül kéri le, és érvényesíti a tanúsítványt. Ezt a mechanizmust TLS kölcsönös hitelesítés vagy ügyféltanúsítvány-alapú hitelesítésnek nevezzük. Ez a cikk bemutatja, hogyan állíthatja be az alkalmazást az ügyféltanúsítvány-alapú hitelesítés használatára.

> [!NOTE]
> Ha HTTP-n keresztül fér hozzá a webhelyhez, és nem HTTPS-kapcsolaton keresztül, akkor nem fog ügyféltanúsítványt kapni. Tehát ha az alkalmazáshoz Ügyféltanúsítványok szükségesek, akkor a HTTP-n keresztül nem engedélyezheti a kérelmeket az alkalmazásnak.
>

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="enable-client-certificates"></a>Ügyféltanúsítványok engedélyezése

Az alkalmazás beállítása az Ügyféltanúsítványok megköveteléséhez:

1. Az alkalmazás felügyeleti lapjának bal oldali navigációs sávján válassza a **konfiguráció**  >  **általános beállítások** lehetőséget.

1. Az **ügyféltanúsítvány-üzemmód** beállítása **kötelező**. Kattintson az oldal tetején lévő **Mentés** elemre.

Ha ugyanezt az Azure CLI-vel szeretné elvégezni, futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app-name> --resource-group <group-name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>Elérési utak kizárása a hitelesítés megkövetelése

Ha engedélyezi az alkalmazás kölcsönös hitelesítését, az alkalmazás gyökeréhez tartozó összes elérési út megköveteli az ügyféltanúsítvány elérését. Ha bizonyos elérési utak esetében el szeretné távolítani ezt a követelményt, adja meg a kizárási útvonalakat az alkalmazás konfigurációjának részeként.

1. Az alkalmazás felügyeleti lapjának bal oldali navigációs sávján válassza a **konfiguráció**  >  **általános beállítások** lehetőséget.

1. Az **ügyfél-kizárási elérési utak** mellett kattintson a Szerkesztés ikonra.

1. Kattintson az **új elérési út** elemre, válasszon egy elérési utat, majd kattintson **az OK** gombra.

1. Kattintson az oldal tetején lévő **Mentés** elemre.

A következő képernyőképen az `/public` alkalmazás elérési útjának bármelyike nem igényel ügyféltanúsítványt.

![Tanúsítvány kizárási elérési útjai][exclusion-paths]

## <a name="access-client-certificate"></a>Hozzáférési ügyféltanúsítvány

App Service a kérelem TLS-megszakítása a frontend Load balancerben történik. Ha [engedélyezve van az Ügyféltanúsítványok](#enable-client-certificates)számára az alkalmazás kódjára való továbbítás, app Service beinjektál egy `X-ARR-ClientCert` kérelem fejlécét az ügyféltanúsítvány használatával. A App Service nem végez semmit ezzel az ügyféltanúsítvány-val, mint az alkalmazásra való továbbítása. Az alkalmazás kódjának feladata az ügyféltanúsítvány ellenőrzése.

A ASP.NET esetében az ügyféltanúsítvány a **HttpRequest. ClientCertificate** tulajdonságon keresztül érhető el.

Más alkalmazások (Node.js, PHP stb.) esetében az ügyfél-tanúsítvány az alkalmazásban a kérelem fejlécében Base64 kódolású értékkel érhető el `X-ARR-ClientCert` .

## <a name="aspnet-sample"></a>ASP.NET minta

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Node.js minta

A következő Node.js mintakód beolvassa a `X-ARR-ClientCert` fejlécet, és a [Node-Forge](https://github.com/digitalbazaar/forge) használatával átalakítja a BASE64 kódolású PEM-karakterláncot egy tanúsítvány objektummá, és érvényesíti azt:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

## <a name="java-sample"></a>Java-minta

A következő Java-osztály kódolja a tanúsítványt `X-ARR-ClientCert` egy `X509Certificate` példányba. `certificateIsValid()` ellenőrzi, hogy a Tanúsítvány ujjlenyomata megegyezik-e a konstruktorban megadott névvel, és hogy a tanúsítvány nem járt-e le.


```java
import java.io.ByteArrayInputStream;
import java.security.NoSuchAlgorithmException;
import java.security.cert.*;
import java.security.MessageDigest;

import sun.security.provider.X509Factory;

import javax.xml.bind.DatatypeConverter;
import java.util.Base64;
import java.util.Date;

public class ClientCertValidator { 

    private String thumbprint;
    private X509Certificate certificate;

    /**
     * Constructor.
     * @param certificate The certificate from the "X-ARR-ClientCert" HTTP header
     * @param thumbprint The thumbprint to check against
     * @throws CertificateException If the certificate factory cannot be created.
     */
    public ClientCertValidator(String certificate, String thumbprint) throws CertificateException {
        certificate = certificate
                .replaceAll(X509Factory.BEGIN_CERT, "")
                .replaceAll(X509Factory.END_CERT, "");
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        byte [] base64Bytes = Base64.getDecoder().decode(certificate);
        X509Certificate X509cert =  (X509Certificate) cf.generateCertificate(new ByteArrayInputStream(base64Bytes));

        this.setCertificate(X509cert);
        this.setThumbprint(thumbprint);
    }

    /**
     * Check that the certificate's thumbprint matches the one given in the constructor, and that the
     * certificate has not expired.
     * @return True if the certificate's thumbprint matches and has not expired. False otherwise.
     */
    public boolean certificateIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        return certificateHasNotExpired() && thumbprintIsValid();
    }

    /**
     * Check certificate's timestamp.
     * @return Returns true if the certificate has not expired. Returns false if it has expired.
     */
    private boolean certificateHasNotExpired() {
        Date currentTime = new java.util.Date();
        try {
            this.getCertificate().checkValidity(currentTime);
        } catch (CertificateExpiredException | CertificateNotYetValidException e) {
            return false;
        }
        return true;
    }

    /**
     * Check the certificate's thumbprint matches the given one.
     * @return Returns true if the thumbprints match. False otherwise.
     */
    private boolean thumbprintIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        MessageDigest md = MessageDigest.getInstance("SHA-1");
        byte[] der = this.getCertificate().getEncoded();
        md.update(der);
        byte[] digest = md.digest();
        String digestHex = DatatypeConverter.printHexBinary(digest);
        return digestHex.toLowerCase().equals(this.getThumbprint().toLowerCase());
    }

    // Getters and setters

    public void setThumbprint(String thumbprint) {
        this.thumbprint = thumbprint;
    }

    public String getThumbprint() {
        return this.thumbprint;
    }

    public X509Certificate getCertificate() {
        return certificate;
    }

    public void setCertificate(X509Certificate certificate) {
        this.certificate = certificate;
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png
