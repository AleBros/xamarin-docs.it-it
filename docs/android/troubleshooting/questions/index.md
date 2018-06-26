---
title: Domande frequenti
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: bbb1e92596fe69246a608eb0758e789f5b99e954
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935464"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

## <a name="installation--setup"></a>Installazione e della configurazione

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Quali pacchetti di Android SDK è necessario installare?](install-android-sdk-packages.md)

Installazione di Android SDK non include automaticamente tutti i pacchetti minimi richiesti per lo sviluppo. Anche se è necessario variare singolo sviluppatore, questa guida vengono illustrati i pacchetti che in genere saranno necessari per lo sviluppo con xamarin. Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Dove è possibile impostare i percorsi di Android SDK?](android-sdk-location.md)

Questa guida descrive entrambe le impostazioni predefinite di Android SDK, che dovrebbero essere adatte per la maggior parte delle installazioni; e su come modificare i valori predefiniti in Visual Studio per Mac o Visual Studio, se necessario.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Come si aggiorna la versione di Java Development Kit (JDK)?](update-jdk.md)

In questo articolo viene illustrato come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9jdk9-errorsmd"></a>[È possibile usare la versione 9 di Java Development Kit (JDK)?](jdk9-errors.md)

Xamarin. Android richiede JDK 8 anziché JDK 9. Questo articolo vengono elencati alcuni messaggi di errore comuni che possono essere visualizzati se 9 JDK è installato, insieme alle istruzioni per il controllo della versione JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?](install-android-support-library.md)

Questa guida vengono fornite procedure di esempio per l'installazione di `Xamarin.Android.Support.v4` libreria di supporto su Windows e Mac.

### <a name="how-do-i-install-google-play-services-in-an-emulatorinstall-gpsmd"></a>[Come si installa Google Play Services in un emulatore?](install-gps.md)

Questa guida include collegamenti ad informazioni sull'installazione di Google Play Services nell'emulatore Android di Visual Studio.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[Quali driver USB sono necessari per il debug di Android in Windows?](android-drivers-debug-windows.md)

Per eseguire il debug in un dispositivo Android quando si sviluppa in Windows. è necessario installare un driver USB compatibile. Android SDK Manager include il "Driver USB Google" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus.
Altri dispositivi hanno bisogno di driver USB pubblicati appositamente dal produttore del dispositivo. Questa guida fornisce informazioni su come trovare questi driver alternativi anche come metodi di test.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?](connect-android-emulator-mac-windows.md)

Questa guida illustra i metodi quando si utilizza l'emulatore Android.

## <a name="general-questions"></a>Domande di carattere generale

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Come si può automatizzare un progetto di test Android NUnit?](automate-android-nunit-test.md)

Questa guida vengono illustrati i passaggi per la configurazione di un progetto di test Android NUnit _non_ un progetto UITest. Sono disponibili le guide UITest [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[Come si abilita IntelliSense nei file axml Android?](enable-axml-intellisense.md)

Questa guida viene descritto come attivare Intellisense di Visual Studio per Android .axml file.

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Perché non è possibile connettere la build di versione Android a Internet?](android-internet.md)

La causa più comune di questo problema consiste nel fatto che il **INTERNET** l'autorizzazione viene inclusa automaticamente in una build di debug, ma è necessario impostare manualmente per una build di rilascio. Questa guida descrive come abilitare l'autorizzazione per le compilazioni di rilascio.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13](android-support-v4v13-libraries.md)

`Support-v4` e `Support-v13` non possono essere utilizzate insieme nella stessa app, vale a dire, si escludono a vicenda. Infatti `Support-v13` effettivamente contiene tutti i tipi e l'implementazione di `Support-v4`. Se si prova e fare riferimento sia nello stesso progetto si verificheranno errori di tipo duplicati.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Risoluzione di un errore PathTooLongException](path-too-long-exception.md)

In questo articolo illustra come risolvere un **PathTooLongException** errore che può verificarsi durante la compilazione di un progetto xamarin. Android.



## <a name="deprecated"></a>Deprecato

> [!NOTE]
> Negli articoli seguenti si applicano a problemi che sono stati risolti nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?](xa-lollipop.md)

Questa guida è stato inizialmente scritto per l'anteprima L Android. Xamarin. Android 4.17 aggiungere Android supporto di anteprima L & xamarin. Android 4.20 aggiungere supporto per il simbolo Android.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Questo errore può verificarsi nelle versioni precedenti di Xamarin se mancano alcuni pacakages il Android SDK obbligatorio.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Modifica dei parametri di memoria Java per Android Designer](android-designer-java-memory.md)

I parametri di memoria predefiniti che vengono utilizzati quando si avvia il `java` elaborare per la finestra di progettazione Android potrebbe non essere compatibile con alcune configurazioni del sistema. A partire 5.7.2.7 Xamarin Studio e Xamarin per Visual Studio 3.9.344 queste impostazioni possono essere personalizzate in base al progetto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Il file Android Resource.designer.cs non verrà aggiornato](resource-designer-wont-update.md)

Un bug in 5.1 Xamarin.Studio danneggiato in precedenza i file con estensione csproj eliminando parzialmente o completamente il codice xml nel file con estensione csproj. Perché si verificherebbe un importante parte di Android compilare il sistema (ad esempio, l'aggiornamento Resource.designer.cs Android) esito negativo. A partire da 5.1.4 stabile rilasciare il 15 luglio, il bug è stato risolto; ma in molti casi è necessario ripristinare manualmente, come descritto in questa guida per il file di progetto.



