---
title: Domande frequenti su Xamarin.Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 35df724850e1fc945c096aebc91b7aa84936bdc1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026973"
---
# <a name="android-frequently-asked-questions"></a>Domande frequenti su Android

## <a name="installation--setup"></a>Installazione & installazione

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Quali pacchetti di Android SDK è necessario installare?](install-android-sdk-packages.md)

L'installazione del Android SDK non include automaticamente tutti i pacchetti minimi necessari per lo sviluppo. Anche se le esigenze dei singoli sviluppatori variano, questa guida illustra i pacchetti che in genere saranno necessari per lo sviluppo con Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Dove è possibile impostare i percorsi di Android SDK?](android-sdk-location.md)

Questa guida descrive le impostazioni predefinite del Android SDK, che dovrebbe funzionare per la maggior parte delle configurazioni. e come modificare queste impostazioni predefinite in Visual Studio per Mac o in Visual Studio, se necessario.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Come si aggiorna la versione di Java Development Kit (JDK)?](update-jdk.md)

Questo articolo illustra come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[È possibile utilizzare Java Development Kit (JDK) versione 9 o successiva?](jdk9-errors.md)

Xamarin.Android richiede JDK 8 o Microsoft Mobile OpenJDK. Questo articolo elenca alcuni messaggi di errore comuni che possono essere visualizzati se è installato JDK 9 o versione successiva, insieme alle istruzioni per il controllo della versione JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?](install-android-support-library.md)

Questa guida illustra i passaggi di esempio per l'installazione della libreria di supporto `Xamarin.Android.Support.v4` in Windows & Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[Quali driver USB sono necessari per il debug di Android in Windows?](android-drivers-debug-windows.md)

Per eseguire il debug su un dispositivo Android durante lo sviluppo in Windows; è necessario installare un driver USB compatibile. Per impostazione predefinita, il gestore Android SDK include "Google USB driver", che aggiunge il supporto per i dispositivi Nexus.
Altri dispositivi richiedono driver USB pubblicati dal produttore del dispositivo. In questa guida vengono fornite informazioni su come trovare questi driver, nonché metodi di test alternativi.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?](connect-android-emulator-mac-windows.md)

Questa guida descrive i metodi quando si usa l'emulatore di Android.

## <a name="general-questions"></a>Domande generali

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Come si può automatizzare un progetto di test Android NUnit?](automate-android-nunit-test.md)

Questa guida illustra i passaggi per la configurazione di un progetto di test di Android NUnit, _non_ di un progetto Xamarin.UITest. Le guide Xamarin.UITest sono disponibili [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Perché non è possibile connettere la build di versione Android a Internet?](android-internet.md)

La causa più comune di questo problema è che l'autorizzazione **Internet** viene inclusa automaticamente in una build di debug, ma deve essere impostata manualmente per una build di rilascio. Questa guida descrive come abilitare l'autorizzazione per le build di rilascio.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13](android-support-v4v13-libraries.md)

`Support-v4` e `Support-v13` non possono essere usati insieme nella stessa app, ovvero si escludono a vicenda. Questo perché `Support-v13` contiene effettivamente tutti i tipi e l'implementazione di `Support-v4`. Se si tenta di fare riferimento a entrambi nello stesso progetto, si verificheranno errori di tipo duplicati.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Ricerca per categorie risolvere un errore PathTooLongException?](path-too-long-exception.md)

Questo articolo illustra come risolvere un errore **PathTooLongException** che può verificarsi durante la compilazione di un progetto Xamarin.Android.

## <a name="deprecated"></a>Deprecato

> [!NOTE]
> Gli articoli seguenti si applicano ai problemi risolti nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?](xa-lollipop.md)

Questa guida è stata scritta originariamente per Android L Preview. Xamarin.Android 4,17 ha aggiunto il supporto per Android L Preview & Xamarin.Android 4,20 ha aggiunto il supporto di Android Lollipop.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Questo errore può verificarsi nelle versioni precedenti di Xamarin se mancano alcuni dei pacchetti di Android SDK necessari.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Modifica dei parametri di memoria Java per Android Designer](android-designer-java-memory.md)

I parametri di memoria predefiniti usati quando si avvia il processo di `java` per la finestra di progettazione di Android potrebbero essere incompatibili con alcune configurazioni di sistema. A partire da Xamarin Studio 5.7.2.7 e Xamarin per Visual Studio 3.9.344 queste impostazioni possono essere personalizzate in base al progetto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Il file Android Resource.designer.cs non verrà aggiornato](resource-designer-wont-update.md)

Un bug in Xamarin.Studio 5,1 danneggiava in precedenza i file. csproj eliminando parzialmente o completamente il codice XML nel file con estensione csproj. In questo modo, le parti importanti del sistema di compilazione Android, ad esempio l'aggiornamento di Android Resource.designer.cs, avranno esito negativo. A partire dalla versione stabile di 5.1.4 il 15 luglio, questo bug è stato risolto. in molti casi, tuttavia, il file di progetto deve essere ripristinato manualmente, come descritto in questa guida.
