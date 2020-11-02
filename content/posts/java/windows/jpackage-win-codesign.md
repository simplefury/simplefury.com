---
title: jpackage and Signing Windows MSI Installers
date: 2020-10-31
description: Signing MSI Installers Created by jpackage
menu:
  sidebar:
    name: jpackage and Signing Windows MSI Installers
    identifier: jpackage-windows-codesign-msi
    parent: java-windows
    weight: 10
---
After moving SFSPro to Java 14, and then Java 15, I wanted to explore the possibility of signing the installer package created by jpackage. Not being a Windows developer, I found most of the documentation to be unclear at best. I was able to piece together enough documentation to figure out how things worked and I thought I'd lay it all out for you here.

1. You have to have a certificate that's trusted by Windows. If you're going to use this in production for distribution, you'll need to purchase one. The least expensive one I've found so far is about $70 USD/year. It does *NOT* need to be an EV certificate, just a standard code signing certificate is fine. If you're going to use a self signed certificate, you will have to add it to the windows truststore for this to work.
  1. Follow the directions [here](https://docs.microsoft.com/en-us/windows/msix/package/create-certificate-package-signing) to create a self-signed certificate on Windows. An important point is when defining the `subject` of the certificate, I had both the `CN` and the `OU` match the value of the `--vendor` flag passed into jpackage.
  1. Follow the directions [here](https://success.outsystems.com/Support/Enterprise_Customers/Installation/Add_self_signed_certificate_to_trusted_root_store_on_OutSystems) to add the self signed certificate to the Windows truststore.
1. [Download](https://developer.microsoft.com/windows/downloads/windows-10-sdk/) and install signtool.exe. Make sure you add signtool to your path. It's located in `C:\Program Files (x86)\Windows Kits\10\bin\<build number>\x64`
1. Once you have the certificate exported and added to the truststore (if you're using a self-signed certificate) and signtool installed, you're ready to sign your MSI file with the following command in PowerShell.

       signtool.exe --% sign /f <PFX file> /d <description> /p <certificate password> /v /fd SHA256 /sha1 <SHA1 fingerprint of certificate> /tr "http://timestamp.sectigo.com" /td SHA256 <MSI file>

Some more helpful links for understanding the above command:
* [Signtool documentation](https://docs.microsoft.com/en-us/windows/win32/seccrypto/signtool)
* [Sectigo Timestamp Server](https://sectigo.com/resource-library/time-stamping-server)
* [Signing Your MSI](https://www.exemsi.com/documentation/sign-your-msi/)
