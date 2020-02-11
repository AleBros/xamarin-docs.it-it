---
title: Individuazione della firma dell'archivio chiavi
ms.prod: xamarin
ms.assetid: 1b511fec-e6f6-453e-89c8-810aafb02b77
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 49d67db46416584248a9b15db606538f5ebfba79
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021013"
---
# <a name="finding-your-keystores-signature"></a>Individuazione della firma dell'archivio chiavi

La firma MD5 o SHA1 di un'app Xamarin.Android dipende dal file con estensione **keystore** usato per firmare l'APK. In genere, in una build di debug viene usato un file con estensione **keystore** diverso rispetto a quello di una build di versione.

## <a name="for-debug--non-custom-signed-builds"></a>Per build di debug/non personalizzate firmate

Xamarin.Android consente di firmare tutte le build di debug con lo stesso file **debug.keystore**. Questo file viene generato alla prima installazione di Xamarin.Android. I passaggi seguenti illustrano il processo per trovare la firma MD5 o SHA1 del file **debug.keystore** Xamarin.Android predefinito.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Individuare il file **debug.keystore** Xamarin usato per firmare l'app. Per impostazione predefinita, l'archivio chiavi usato per firmare le versioni di debug di un'applicazione Xamarin.Android è reperibile nella posizione seguente:

**C:\\Utenti\\*NOMEUTENTE*\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool.exe` dal JDK. Questo strumento si trova in genere nel percorso seguente:

**C:\\Programmi (x86)\\Java\\jdk*VERSION*\\bin\\keytool.exe**

Aggiungere la directory contenente **keytool.exe** alla variabile di ambiente `PATH`.
Aprire un **prompt dei comandi** ed eseguire `keytool.exe` tramite il comando seguente:

```cmd
keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

Quando viene eseguito, **keytool.exe** deve restituire il testo seguente. Le etichette **MD5:** e **SHA1:** identificano le rispettive firme:

```cmd
Alias name: androiddebugkey
Creation date: Aug 19, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 53f3b126
Valid from: Tue Aug 19 13:18:46 PDT 2014 until: Sun Nov 15 12:18:46 PST 2043
Certificate fingerprints:
         MD5:  27:78:7C:31:64:C2:79:C6:ED:E5:80:51:33:9C:03:57
         SHA1: 00:E5:8B:DA:29:49:9D:FC:1D:DA:E7:EE:EE:1A:8A:C7:85:E7:31:23
         SHA256: 21:0D:73:90:1D:D6:3D:AB:4C:80:4E:C4:A9:CB:97:FF:34:DD:B4:42:FC:
08:13:E0:49:51:65:A6:7C:7C:90:45
         Signature algorithm name: SHA1withRSA
         Version: 3
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Individuare il file **debug.keystore** Xamarin usato per firmare l'app. Per impostazione predefinita, l'archivio chiavi usato per firmare le versioni di debug di un'applicazione Xamarin.Android è reperibile nella posizione seguente:

**~/.local/share/Xamarin/Mono for Android/debug.keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando **keytool** dal JDK. Questo strumento si trova in genere nel percorso seguente:

**/System/Library/Java/JavaVirtualMachines/*VERSION*.jdk/Contents/Home/bin/keytool**

Aggiungere la directory contenente **keytool** alla variabile di ambiente **PATH**.
Aprire un **terminale** ed eseguire **keytool** usando il comando seguente:

```bash
$ keytool -list -v -keystore ~/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

Quando viene eseguito, **keytool** deve restituire il testo seguente. Le etichette **MD5:** e **SHA1:** identificano le rispettive firme:

```bash
Alias name: androiddebugkey
Creation date: Apr 16, 2015
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 76afa120
Valid from: Thu Apr 16 10:52:27 PDT 2015 until: Sat Apr 08 10:52:27 PDT 2045
Certificate fingerprints:
     MD5:  0A:D3:7E:80:3D:40:2A:23:89:B9:AB:9C:4B:B6:63:36
     SHA1: 89:33:8F:F2:C5:0C:91:08:4A:CF:04:A5:EC:4A:31:80:84:18:0D:D4
     SHA256: 91:AC:3E:2F:CB:EF:50:07:2B:E0:D9:8D:8B:C2:42:87:6A:85:02:86:EB:44:84:10:34:02:ED:35:CE:C6:38:47
     Signature algorithm name: SHA256withRSA
     Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 65 6C FE 67 8E CD CB 9A   01 CD 9F E8 25 9C A4 A3  el.g........%...
0010: 4F 4E CF 17                                        ON..
]
]
```

-----

## <a name="for-release--custom-signed-builds"></a>Per build di versione/personalizzate firmate

Il processo per le build di versione firmate con un file con estensione **keystore** personalizzato è identico a quello illustrato in precedenza, con il file con estensione **keystore** di versione che va a sostituire il file **debug.keystore** usato da Xamarin.Android. Sostituire con i valori personali le password dell'archivio chiavi e il nome di alias ottenuti quando è stato creato il file keystore della versione.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando viene usata la procedura guidata **Distribuisci** di Visual Studio per firmare un'app Xamarin.Android, l'archivio chiavi risultante si trova nel percorso seguente:

**C:\\Utenti\\*NOMEUTENTE*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*alias*\\*alias*.keystore**

Se, ad esempio, è stata seguita la procedura descritta in [Create a New Certificate](~/android/deploy-test/signing/index.md#newcertvs) (Creare un nuovo certificato) per creare una nuova chiave di firma, l'archivio chiavi di esempio risultante si trova nel percorso seguente:

**C:\\Utenti\\*NOMEUTENTE*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

Per altre informazioni sulla firma di un'app Xamarin.Android, vedere [Signing the Android Application Package](~/android/deploy-test/signing/index.md) (Firma del pacchetto dell'applicazione Android).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Quando viene usata la procedura guidata **Firma e distribuisci** di Visual Studio per Mac per firmare l'app, l'archivio chiavi risultante si trova nel percorso seguente:

**~/Library/Developer/Xamarin/Keystore/*alias*/*alias*.keystore**

Se, ad esempio, è stata seguita la procedura descritta in [Create a New Certificate](~/android/deploy-test/signing/index.md#newcertxs) (Creare un nuovo certificato) per creare una nuova chiave di firma, l'archivio chiavi di esempio risultante si trova nel percorso seguente:

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

Per altre informazioni sulla firma di un'app Xamarin.Android, vedere [Signing the Android Application Package](~/android/deploy-test/signing/index.md) (Firma del pacchetto dell'applicazione Android).

-----
