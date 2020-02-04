---
title: Installazione di Xamarin.Android come app di sistema
description: Questa guida illustra le differenze tra un'app di sistema e un'app utente e spiega come installare un'applicazione Xamarin.Android come applicazione di sistema. Questa guida è rivolta agli autori di immagini ROM Android personalizzate. Non illustra come creare una ROM personalizzata.
ms.prod: xamarin
ms.assetid: 0113143B-7D8D-4C4C-B2F5-B966A2E7CE1F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 72cddde86708b5573dc578165354d137c4dc35b6
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723900"
---
# <a name="installing-xamarinandroid-as-a-system-app"></a>Installazione di Xamarin.Android come app di sistema

_Questa guida illustra le differenze tra un'app di sistema e un'app utente e spiega come installare un'applicazione Xamarin.Android come applicazione di sistema. Questa guida è rivolta agli autori di immagini ROM Android personalizzate. Non illustra come creare una ROM personalizzata._

## <a name="system-app"></a>App di sistema

Gli autori di immagini ROM Android personalizzate o i produttori di dispositivi Android possono aver bisogno di includere un'applicazione Xamarin.Android come _app di sistema_ quando distribuiscono una ROM o un dispositivo. Un'app di sistema è un'app considerata importante per il funzionamento del dispositivo o che fornisce la funzionalità che per l'autore della ROM personalizzata deve essere sempre disponibile.

Le app di sistema vengono installate nella cartella **/system/app/** (una directory di sola lettura nel file system) e non possono essere eliminate o spostate dall'utente a meno che non abbia l'accesso radice. Un'applicazione installata dall'utente (in genere da Google Play o tramite sideload dell'app) è invece nota come _app utente_. Le app utente possono essere eliminate dall'utente e, in molti casi, spostate in una posizione diversa sul dispositivo (ad esempio, alcuni tipi di risorse di archiviazione esterne).

Le app di sistema si comportano esattamente come le app utente, ma con le importanti eccezioni seguenti:

- Le app di sistema possono essere aggiornate come una normale _app utente_, ma, poiché esiste sempre una copia dell'app in **/system/app/** , è sempre possibile eseguire il rollback dell'applicazione alla versione originale.

- Alle app di sistema possono essere concesse alcune autorizzazioni solo di sistema non disponibili per un'app utente. Un esempio di autorizzazione solo di sistema è [`BLUETOOTH_PRIVILEGED`](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED), che consente di associare le applicazioni ai dispositivi Bluetooth senza interazione dell'utente.

È possibile distribuire un'app Xamarin.Android come applicazione di sistema. Oltre a fornire un APK alla ROM personalizzata, è necessario copiare manualmente due librerie condivise, **libmonodroid.so** e **libmonosgen-2.0.so**, dall'APK al file system dell'immagine ROM. Questa guida illustra i passaggi necessari.

## <a name="restrictions"></a>Restrizioni

Questa guida è rivolta agli autori di immagini ROM Android personalizzate. Non illustra come creare una ROM personalizzata.

Questa guida presuppone familiarità con la [creazione del pacchetto della versione finale di un APK per Xamarin.Android](~/android/deploy-test/publishing/index.md) e la conoscenza delle [architetture CPU](~/android/app-fundamentals/cpu-architectures.md) per le applicazioni Android.

## <a name="install-a-xamarinandroid-app-as-a-system-app"></a>Installare un'app Xamarin.Android come app di sistema

I passaggi seguenti illustrano come installare un'app Xamarin.Android come app di sistema.

1. **Creare un pacchetto della versione finale di un APK dell'app Xamarin.Android** &ndash; Questo passaggio è illustrato più dettagliatamente nella guida [Pubblicazione di un'applicazione](~/android/deploy-test/publishing/index.md).

2. **Estrarre le librerie condivise dall'APK** &ndash; Usando un'utilità ZIP, aprire il file APK ed esaminare il contenuto della cartella **/lib/** . Questa cartella avrà una sottodirectory per ogni _interfaccia binaria dell'applicazione_ (ABI) supportata dall'applicazione. Il contenuto di questa cartella includerà tutte le librerie condivise richieste dall'app in una particolare ABI:

    ![Screenshot dei file SO nella cartella armeabi-v7a di taskypro.zip](install-system-app-images/install-system-app-01.png)

   Nello screenshot precedente è presente una sola ABI supportata (**armeabi-v7a**) che contiene i due file **SO** richiesti dall'app. Si noti che è necessario estrarre solo i file ABI appropriati per il dispositivo o per l'architettura di destinazione della ROM del dispositivo. Non copiare quindi i file **SO** dalla cartella **x86** a una ROM o un dispositivo **armeabi-v7a**.

3. **Copiare i file SO in /system/lib** &ndash; Copiare i file **SO** estratti dall'APK nel passaggio precedente nella cartella **/system/lib/** della ROM personalizzata.

4. **Copiare il file APK in /system/app** &ndash; Nel passaggio finale si copia il file APK nella cartella **/system/app** della ROM.

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato la differenza tra un'_app di sistema_ e un'_app utente_ e ha spiegato come installare un'applicazione Xamarin.Android come app di sistema.

## <a name="related-links"></a>Collegamenti correlati

- [Publishing an Application](~/android/deploy-test/publishing/index.md) (Pubblicazione di un'applicazione)
- [Architetture CPU](~/android/app-fundamentals/cpu-architectures.md)
- [BLUETOOTH_PRIVILEGED](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)
- [ABI Management](https://developer.android.com/ndk/guides/abis) (Gestione ABI)
