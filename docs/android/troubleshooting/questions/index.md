---
title: Domande frequenti su xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 9c0e6d014f27651710bf3b8e713b2bf80322d628
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153399"
---
# <a name="android-frequently-asked-questions"></a>Domande frequenti su Android

## <a name="installation--setup"></a>Installazione e della configurazione

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Quali pacchetti di Android SDK è necessario installare?](install-android-sdk-packages.md)

Installazione di Android SDK non include automaticamente tutti i pacchetti minimi richiesti per lo sviluppo. Mentre devono variare per singoli sviluppatori, questa guida illustra i pacchetti che saranno in genere necessari per lo sviluppo con xamarin. Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Dove è possibile impostare i percorsi di Android SDK?](android-sdk-location.md)

Questa guida descrive entrambe le impostazioni predefinite di Android SDK, che dovrebbe funzionare per la maggior parte delle installazioni; e come modificare questi valori predefiniti in Visual Studio per Mac o Visual Studio, se necessario.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Come si aggiorna la versione di Java Development Kit (JDK)?](update-jdk.md)

Questo articolo viene illustrato come aggiornare la versione di Java Development Kit (JDK) in Windows e Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[È possibile usare Java Development Kit (JDK) versione 9 o versione successiva?](jdk9-errors.md)

Xamarin. Android richiede JDK 8 o OpenJDK per dispositivi mobili Microsoft. Questo articolo elenca alcuni comuni messaggi di errore che si possono verificare se è installato JDK 9 o versioni successive, insieme alle istruzioni per la verifica della versione del JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?](install-android-support-library.md)

Questa guida fornisce passaggi di esempio per l'installazione di `Xamarin.Android.Support.v4` libreria di supporto su Windows e Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[Quali driver USB sono necessari per il debug di Android in Windows?](android-drivers-debug-windows.md)

Per eseguire il debug in un dispositivo Android quando si sviluppa in Windows; è necessario installare un driver USB compatibile. Android SDK Manager include il "Driver USB Google" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus.
Altri dispositivi hanno bisogno di driver USB pubblicato dal produttore del dispositivo. Questa guida vengono fornite informazioni su come trovare questi driver metodi di test anche come alternativi.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?](connect-android-emulator-mac-windows.md)

Questa guida illustra i metodi quando si usa l'emulatore di Android.

## <a name="general-questions"></a>Domande generali

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Come si può automatizzare un progetto di test Android NUnit?](automate-android-nunit-test.md)

Questa guida illustra i passaggi per la configurazione di un progetto di test Android NUnit _non_ un progetto xamarin. UITest. Sono disponibili le guide di xamarin. UITest [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Perché non è possibile connettere la build di versione Android a Internet?](android-internet.md)

La causa più comune di questo problema è che il **INTERNET** autorizzazione viene automaticamente incluso in una build di debug, ma deve essere impostato manualmente per una build di rilascio. Questa guida descrive come abilitare l'autorizzazione per le build di rilascio.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13](android-support-v4v13-libraries.md)

`Support-v4` e `Support-v13` non possono essere utilizzate insieme nella stessa app, vale a dire si escludono a vicenda. Infatti `Support-v13` effettivamente contiene tutti i tipi e l'implementazione di `Support-v4`. Se si prova a fare riferimento a entrambi nello stesso progetto, si verificheranno errori di tipo duplicati.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Come si risolve un errore PathTooLongException?](path-too-long-exception.md)

Questo articolo illustra come risolvere un **PathTooLongException** errore che può verificarsi durante la compilazione di un progetto xamarin. Android.



## <a name="deprecated"></a>Deprecato

> [!NOTE]
> Gli articoli seguenti si applicano ai problemi che sono stati risolti nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?](xa-lollipop.md)

Questa guida è stato scritto originariamente per l'anteprima L Android. Xamarin. Android 4.17 aggiunto 4.20 xamarin. Android e Android supporto di anteprima L aggiunto supporto per Android Lollipop.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Questo errore può verificarsi nelle versioni precedenti di Xamarin se mancano alcuni dei pacchetti Android SDK necessari.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Modifica dei parametri di memoria Java per Android Designer](android-designer-java-memory.md)

I parametri di memoria predefiniti che vengono usati quando si avvia il `java` elaborare per Android designer potrebbe essere incompatibile con alcune configurazioni di sistema. Partire da Xamarin Studio 5.7.2.7 e Xamarin per Visual Studio 3.9.344 queste impostazioni possono essere personalizzati in base al progetto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Il file Android Resource.designer.cs non verrà aggiornato](resource-designer-wont-update.md)

Un bug nella versione 5.1 di xamarin Studio in precedenza danneggiato file con estensione csproj, completamente o parzialmente eliminando il codice xml nel file con estensione csproj. Questa situazione provocherebbe importante sistema (ad esempio, l'aggiornamento di Android Resource.designer.cs) di compilazione di parti di Android per avere esito negativo. Al momento della stesura di 5.1.4 stabile versione 15 luglio, questo bug è stato risolto; ma in molti casi è necessario ripristinare manualmente, come descritto in questa guida il file di progetto.



