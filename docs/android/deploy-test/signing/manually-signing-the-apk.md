---
title: Firma manuale del file APK
ms.prod: xamarin
ms.assetid: 08549E1C-7F04-4D20-9E7A-794B9D09FD12
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 5dee7e18b7f9f47493293bc552cd4f44da56ceec
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571428"
---
# <a name="manually-signing-the-apk"></a>Firma manuale del file APK

Dopo la compilazione dell'applicazione per il rilascio e prima della distribuzione, il file APK deve essere firmato, in modo che possa essere eseguito in un dispositivo Android. Questo processo viene gestito in genere con l'ambiente IDE. In alcune situazioni, tuttavia, è necessario firmare il file APK manualmente dalla riga di comando. La firma di un file APK prevede i passaggi seguenti:

1. **Creazione di una chiave privata** &ndash; Questo passaggio deve essere eseguito una sola volta. La chiave privata è necessaria per firmare digitalmente il file APK.
    Dopo che la chiave privata è stata creata, è possibile ignorare questo passaggio per le compilazioni di rilascio future.

2. **Esecuzione di Zipalign per il file APK** &ndash; *Zipalign* è un processo di ottimizzazione eseguito per un'applicazione. Questo processo consente ad Android di interagire in modo più efficiente con il file APK in runtime. Xamarin.Android esegue un controllo in runtime e consente l'esecuzione dell'applicazione solo se il file APK è stato sottoposto a Zipalign.

3. **Firma del file APK** &ndash; Questo passaggio prevede l'uso dell'utilità **apksigner** di Android SDK e la firma del file APK con la chiave privata creata nel passaggio precedente. Le applicazioni sviluppate con versioni degli strumenti di compilazione di Android SDK precedenti alla versione 24.0.3 usano l'app **jarsigner** di JDK. Entrambi gli strumenti vengono trattati in dettaglio più avanti.

L'ordine dei passaggi è importante e dipende dallo strumento usato per firmare il file APK. Quando si usa **apksigner**, è importante prima eseguire **Zipalign** per l'applicazione e quindi firmare quest'ultima con **apksigner**.  Se per firmare il file APK è necessario usare **jarsigner**, è importante prima firmare il file APK e quindi eseguire **Zipalign**.

## <a name="prerequisites"></a>Prerequisiti

Questa guida descrive principalmente l'uso dell'utilità **apksigner** presente negli strumenti di compilazione di Android SDK versione 24.0.3 o successiva. La guida presuppone che sia già stato compilato un file APK.

Le applicazioni compilate con una versione precedente degli strumenti di compilazione di Android SDK devono usare **jarsigner** come descritto in [Firmare il file APK con jarsigner](#Sign_the_APK_with_jarsigner) più avanti.

## <a name="create-a-private-keystore"></a>Creare un archivio chiavi privato

Un *archivio chiavi* è un database di certificati di sicurezza creato tramite il programma [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) di Java SDK. Un archivio chiavi è fondamentale per la pubblicazione di un'applicazione. Xamarin.Android infatti non esegue applicazioni che non siano state firmate digitalmente.

Durante lo sviluppo, Xamarin.Android firma l'applicazione con un archivio chiavi di debug, che consente la distribuzione dell'applicazione direttamente all'emulatore o ai dispositivi configurati per l'uso di applicazioni sottoponibili a debug.
Questo archivio chiavi, tuttavia, non è riconosciuto come archivio chiavi valido ai fini della distribuzione di applicazioni.

Per questo motivo, per la firma delle applicazioni è necessario creare e usare un archivio chiavi privato. Questo passaggio deve essere eseguito solo una volta, perché la stessa chiave può essere usata in seguito per la pubblicazione degli aggiornamenti e per la firma di altre applicazioni.

È importante proteggere l'archivio chiavi. In caso di smarrimento, non sarà possibile pubblicare gli aggiornamenti dell'applicazione con Google Play.
L'unica soluzione al problema causato dallo smarrimento dell'archivio chiavi è la creazione di un nuovo archivio chiavi che consenta di firmare nuovamente il file APK con una nuova chiave. Sarà quindi necessario inviare una nuova applicazione. La vecchia applicazione dovrà essere rimossa da Google Play. Analogamente, se il nuovo archivio chiavi viene compromesso o distribuito pubblicamente, è possibile che vengano distribuite versioni non ufficiali o dannose di un'applicazione.

### <a name="create-a-new-keystore"></a>Creare un nuovo archivio chiavi

La creazione di un nuovo archivio chiavi richiede lo strumento da riga di comando [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) di Java SDK. Il frammento di codice seguente è un esempio di uso di **keytool** (sostituire `<my-filename>` con il nome file dell'archivio chiavi e `<key-name>` con il nome della chiave all'interno dell'archivio chiavi):

```shell
$ keytool -genkeypair -v -keystore <filename>.keystore -alias <key-name> -keyalg RSA \
          -keysize 2048 -validity 10000
```

Per prima cosa, **keytool** chiede la password dell'archivio chiavi, quindi chiede alcune informazioni in grado di facilitare la creazione della chiave. Il frammento di codice seguente è un esempio di creazione di una nuova chiave, `publishingdoc`, che verrà archiviata nel file `xample.keystore`:

```shell
$ keytool -genkeypair -v -keystore xample.keystore -alias publishingdoc -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  Ham Chimpanze
What is the name of your organizational unit?
  [Unknown]:  NASA
What is the name of your organization?
  [Unknown]:  NASA
What is the name of your City or Locality?
  [Unknown]:  Cape Canaveral
What is the name of your State or Province?
  [Unknown]:  Florida
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US
Enter key password for <publishingdoc>
        (RETURN if same as keystore password):
Re-enter new password:
[Storing xample.keystore]
```

Per elencare le chiavi archiviate in un archivio chiavi, usare **keytool** con l'opzione &ndash; `list`:

```shell
$ keytool -list -keystore xample.keystore
```

## <a name="zipalign-the-apk"></a>Eseguire Zipalign per il file APK

Prima di firmare un file APK con **apksigner**, è importante ottimizzare il file usando lo strumento **zipalign** di Android SDK. **zipalign** ristruttura le risorse in un file APK secondo limiti di 4 byte. Questo allineamento consente ad Android di caricare rapidamente le risorse dal file APK, aumentando le prestazioni dell'applicazione e riducendo potenzialmente l'uso di memoria. Xamarin.Android esegue un controllo in runtime per determinare se il file APK è stato sottoposto a Zipalign. In caso negativo, l'applicazione non viene eseguita.

Il comando seguente usa il file APK firmato e genera l'APK **helloworld.apk** firmato, sottoposto a Zipalign e pronto per la distribuzione.

```shell
$ zipalign -f -v 4 mono.samples.helloworld-unsigned.apk helloworld.apk
```

## <a name="sign-the-apk"></a>Firmare il pacchetto APK

Dopo aver eseguito Zipalign per il file APK, è necessario firmarlo tramite un archivio chiavi. Questa operazione viene eseguita con lo strumento **apksigner**, disponibile nella directory **build-tools** della versione degli strumenti di compilazione dell'SDK.  Se, ad esempio, sono installati gli strumenti di compilazione di Android SDK versione 25.0.3, **apksigner** si trova nella directory:

```bash
$ ls $ANDROID_HOME/build-tools/25.0.3/apksigner
/Users/tom/android-sdk-macosx/build-tools/25.0.3/apksigner*
```

Il frammento di codice seguente presuppone che **apksigner** sia accessibile dalla variabile di ambiente `PATH`. Il frammento firma un file APK usando l'alias chiave `publishingdoc` contenuto nel file **xample.keystore**:

```shell
$ apksigner sign --ks xample.keystore --ks-key-alias publishingdoc mono.samples.helloworld.apk
```

Quando si esegue questo comando, **apksigner** chiede la password dell'archivio chiavi, se necessario.

Vedere la [documentazione di Google](https://developer.android.com/studio/command-line/apksigner.html) per altre informazioni sull'uso di **apksigner**.

> [!NOTE]
>  In base al [problema Google 62696222](https://issuetracker.google.com/issues/62696222), **apksigner** "manca" da Android SDK. La soluzione alternativa per questo problema consiste nell'installare gli strumenti di compilazione di Android SDK versione 25.0.3 e usare la versione di **apksigner** corrispondente.  

<a name="Sign_the_APK_with_jarsigner"></a>

### <a name="sign-the-apk-with-jarsigner"></a>Firmare il file APK con jarsigner

> [!WARNING]
>  Questa sezione si applica solo se è necessario firmare il file APK con l'utilità **jarsigner**. Per gli sviluppatori è consigliabile firmare il file APK con **apksigner**.

Questa tecnica comporta la firma del file APK tramite il comando **[jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)** di Java SDK.  Lo strumento **jarsigner** è in dotazione con Java SDK.

Il codice seguente illustra come firmare un file APK usando **jarsigner** e la chiave `publishingdoc` contenuta in un file di archivio chiavi denominato **xample.keystore**:

```shell
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore xample.keystore mono.samples.helloworld.apk publishingdoc
```

> [!NOTE]
>  Quando si usa **jarsigner**, è importante _prima_ firmare il file APK e quindi usare **zipalign**.  

## <a name="related-links"></a>Collegamenti correlati

- [Application Signing](https://source.android.com/security/apksigning/) (Firma dell'applicazione)
- [jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)
- [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)
- [zipalign](https://developer.android.com/studio/command-line/zipalign.html)
- [Build Tools 26.0.0: dov'è apksigner?](https://issuetracker.google.com/issues/62696222)
