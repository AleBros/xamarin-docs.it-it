---
title: Test, ottimizzazione e distribuzione di app Xamarin.Android
description: Questa sezione include guide che illustrano come testare un'applicazione, ottimizzarne le prestazioni, prepararla per il rilascio, firmarla con un certificato e pubblicarla in un app store
ms.prod: xamarin
ms.assetid: 568C0B85-EFF3-AF6F-5605-95055193D367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 4b7d3d19ce8766ccdbfc41163fcad44074e832b8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "80159763"
---
# <a name="deployment-and-testing"></a>Distribuzione e test

Questa sezione include guide che illustrano come testare un'applicazione, ottimizzarne le prestazioni, prepararla per il rilascio, firmarla con un certificato e pubblicarla in un app store.

## <a name="application-package-sizes"></a>[Dimensioni del pacchetto dell'applicazione](app-package-size.md)

Questo articolo esamina le parti costituenti di un pacchetto dell'applicazione Xamarin.Android e le strategie associate che possono essere usate per una distribuzione efficiente del pacchetto durante le fasi di debug e rilascio dello sviluppo.

## <a name="apply-changes"></a>[Applicazione di modifiche](apply-changes.md)

Questa guida illustra la funzionalità Applica modifiche che consente di eseguire il push delle modifiche delle risorse nell'app in esecuzione senza riavviare l'app.

## <a name="building-apps"></a>[Creazione di app](building-apps/index.md)

Questa sezione descrive il funzionamento del processo di compilazione e illustra come compilare APK specifici di ABI.

## <a name="command-line-emulator"></a>[Emulatore della riga di comando](command-line-emulator.md)

Questo articolo illustra brevemente l'avvio dell'emulatore tramite la riga di comando.

## <a name="debugging"></a>[Debug](~/android/deploy-test/debugging/index.md)

Le guide nella sezione consentono di eseguire il debug di un'applicazione con emulatori Android, dispositivi Android reali e il registro di debug.

## <a name="setting-the-debuggable-attribute"></a>[Setting the Debuggable Attribute (Impostazione dell'attributo Debuggable)](~/android/deploy-test/debuggable-attribute.md)

Questo articolo illustra come impostare l'attributo debuggable in modo che strumenti come `adb` possano comunicare con JVM.

## <a name="environment"></a>[Environment](environment.md)

Questo articolo descrive l'ambiente di esecuzione Xamarin.Android e le proprietà di sistema Android che hanno effetto sull'esecuzione del programma.

## <a name="gdb"></a>[GDB](gdb.md)

Questo articolo illustra come usare `gdb` per il debug di un'applicazione Xamarin.Android.

## <a name="installing-a-system-app"></a>[Installazione di un'app di sistema](install-system-app.md)

Questa guida illustra come installare un'app Xamarin.Android come applicazione di sistema in un dispositivo Android o come parte di una ROM personalizzata.

## <a name="linking-on-android"></a>[Collegamento in Android](linker.md)

Questo articolo illustra il processo di collegamento usato da Xamarin.Android per ridurre le dimensioni finali di un'applicazione. Descrive i diversi livelli di collegamento che possono essere eseguiti e fornisce indicazioni e suggerimenti per la risoluzione dei problemi per poter limitare gli errori che potrebbero derivare dall'uso del linker.

## <a name="xamarinandroid-performance"></a>[Prestazioni di Xamarin.Android](~/android/deploy-test/performance.md)

Esistono varie tecniche per incrementare le prestazioni delle applicazioni compilate con Xamarin.Android. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

## <a name="profiling-android-apps"></a>[Profilatura delle app Android](~/android/deploy-test/profiling.md)

Questa guida spiega come usare gli strumenti del profiler per esaminare le prestazioni e l'utilizzo della memoria di un'app Android.

## <a name="preparing-an-application-for-release"></a>[Preparazione di un'applicazione per il rilascio](~/android/deploy-test/release-prep/index.md)

Dopo aver scritto il codice di un'applicazione e averla testata, è necessario preparare il pacchetto per la distribuzione. La prima attività di preparazione del pacchetto è la compilazione dell'applicazione per il rilascio, che essenzialmente comporta l'impostazione di alcuni attributi dell'applicazione.

## <a name="signing-the-android-application-package"></a>[Signing the Android Application Package](~/android/deploy-test/signing/index.md) (Firma del pacchetto dell'applicazione Android)

Informazioni su come creare un'identità di firma di Android e un nuovo certificato di firma per le applicazioni Android e su come firmare l'applicazione con il certificato di firma. Questo argomento illustra anche come esportare l'applicazione su disco per la distribuzione *ad hoc*. Il pacchetto dell'applicazione Android (APK) risultante può essere trasferito localmente in dispositivi Android senza passare attraverso un app store.

## <a name="publishing-an-application"></a>[Publishing an Application](~/android/deploy-test/publishing/index.md) (Pubblicazione di un'applicazione)

Questa serie di articoli illustra i passaggi per la distribuzione al pubblico di un'applicazione creata con Xamarin.Android. La distribuzione può essere eseguita tramite canali, come ad esempio la posta elettronica, un server Web privato, Google Play o Amazon Appstore per Android.
