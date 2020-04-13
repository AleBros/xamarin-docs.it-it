---
title: Xamarin.Android Domande frequenti
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 28b13951a0d681ffdb8e643667e496e0b3606628
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291841"
---
# <a name="android-frequently-asked-questions"></a>Domande frequenti su Android

## <a name="installation--setup"></a>Installazione del & di installazione

### <a name="which-android-sdk-packages-should-i-install"></a>[Quali pacchetti di Android SDK è necessario installare?](install-android-sdk-packages.md)

L'installazione di Android SDK non include automaticamente tutti i pacchetti minimi necessari per lo sviluppo. Mentre le esigenze dei singoli sviluppatori variano, questa guida illustra i pacchetti che saranno generalmente necessari per lo sviluppo con Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locations"></a>[Dove è possibile impostare i percorsi di Android SDK?](android-sdk-location.md)

Questa guida descrive sia le impostazioni predefinite di Android SDK, che dovrebbe funzionare per la maggior parte delle configurazioni; e come modificare queste impostazioni predefinite in Visual Studio per Mac o Visual Studio, se necessario.

### <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>[Come si aggiorna la versione di Java Development Kit (JDK)?](update-jdk.md)

In questo articolo viene illustrato come aggiornare la versione Java Development Kit (JDK) su Windows e Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-later"></a>[È possibile usare la versione 9 o versioni successive di Java Development Kit (JDK)?](jdk9-errors.md)

Xamarin.Android richiede JDK 8 o Microsoft Mobile OpenJDK. In questo articolo sono elencati alcuni messaggi di errore comuni che possono essere visualizzati se è installato JDK 9 o versione successiva, insieme alle istruzioni per il controllo della versione di JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>[Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?](install-android-support-library.md)

Questa guida fornisce la procedura `Xamarin.Android.Support.v4` di esempio per l'installazione della libreria di supporto in Windows & Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>[Quali driver USB sono necessari per il debug di Android in Windows?](android-drivers-debug-windows.md)

Per eseguire il debug su un dispositivo Android durante lo sviluppo in Windows; è necessario installare un driver USB compatibile. Android SDK Manager include il "Google USB Driver" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus.
Altri dispositivi richiedono driver USB pubblicati dal produttore del dispositivo. Questa guida fornisce informazioni su come trovare questi driver e metodi di test alternativi.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>[È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?](connect-android-emulator-mac-windows.md)

Questa guida illustra i metodi quando si utilizza l'emulatore Android.This guide covers methods when using the Android emulator.

## <a name="general-questions"></a>Domande generali

### <a name="how-do-i-automate-an-android-nunit-test-project"></a>[Come si può automatizzare un progetto di test Android NUnit?](automate-android-nunit-test.md)

Questa guida illustra i passaggi per impostare un progetto di test Android NUnit, non un progetto Xamarin.UITest.This guide covers steps for setting up an Android NUnit test project, _not_ a Xamarin.UITest project. Le guide Xamarin.UITest sono disponibili [qui](/appcenter/test-cloud/preparing-for-upload).

### <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>[Perché non è possibile connettere la build di versione Android a Internet?](android-internet.md)

La causa più comune di questo problema è che l'autorizzazione **INTERNET** viene inclusa automaticamente in una build di debug, ma deve essere impostata manualmente per una build di rilascio. In questa guida viene descritto come abilitare l'autorizzazione per le build di rilascio.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>[Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13](android-support-v4v13-libraries.md)

`Support-v4`e `Support-v13` non possono essere utilizzati insieme nella stessa app, cioè si escludono a vicenda. Questo perché `Support-v13` contiene effettivamente tutti i `Support-v4`tipi e l'implementazione di . Se si tenta di fare riferimento a entrambi nello stesso progetto, si verificheranno errori di tipo duplicato.

### <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>[Come si risolve un errore PathTooLongException?](path-too-long-exception.md)

In questo articolo viene illustrato come risolvere un errore PathTooLongException che può verificarsi durante la compilazione di un progetto Xamarin.Android.This article explains how to resolve a **PathTooLongException** error that may occur while building a Xamarin.Android project.

## <a name="deprecated"></a>Deprecato

> [!NOTE]
> Gli articoli seguenti si applicano ai problemi risolti nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica sulla versione più recente del software, presentare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output completo del log di compilazione.

### <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>[In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?](xa-lollipop.md)

Questa guida è stata originariamente scritta per l'anteprima di Android L. Xamarin.Android 4.17 aggiunto Android L Preview Support & Xamarin.Android 4.20 aggiunto Android Lollipop supporto.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>[Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Questo errore può verificarsi nelle versioni precedenti di Xamarin se mancano alcuni dei pacchetti SDK Android necessari.

### <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>[Modifica dei parametri di memoria Java per Android Designer](android-designer-java-memory.md)

I parametri di memoria predefiniti `java` utilizzati all'avvio del processo per la finestra di progettazione Android potrebbero essere incompatibili con alcune configurazioni di sistema. A partire da Xamarin Studio 5.7.2.7 e Xamarin per Visual Studio 3.9.344 queste impostazioni possono essere personalizzate in base al progetto.

### <a name="my-android-resourcedesignercs-file-will-not-update"></a>[Il file Android Resource.designer.cs non verrà aggiornato](resource-designer-wont-update.md)

Un bug in Xamarin.Studio 5.1 danneggiato in precedenza i file csproj eliminando parzialmente o completamente il codice xml nel file con estensione csproj. Ciò causerebbe l'esito negativo di parti importanti del sistema di compilazione Android (ad esempio l'aggiornamento del Resource.designer.cs Android). A partire dalla versione stabile 5.1.4 del 15 luglio, questo bug è stato risolto; ma in molti casi il file di progetto deve essere riparato manualmente, come descritto in questa guida.
