---
title: Distribuzione e test
description: Questa sezione include guide che illustrano come testare un'applicazione, ottimizzarne le prestazioni, prepararla per il rilascio, firmarla con un certificato e pubblicarla in un app store
ms.topic: article
ms.prod: xamarin
ms.assetid: 568C0B85-EFF3-AF6F-5605-95055193D367
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 5ddc2a258ad09de2cdd8214dceb533441812ae54
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="deployment-and-testing"></a>Distribuzione e test

Questa sezione include guide che illustrano come testare un'applicazione, ottimizzarne le prestazioni, prepararla per il rilascio, firmarla con un certificato e pubblicarla in un app store.


##  <a name="application-package-sizesapp-package-sizemd"></a>[Dimensioni del pacchetto dell'applicazione](app-package-size.md)

Questo articolo esamina le parti costituenti di un pacchetto dell'applicazione Xamarin.Android e le strategie associate che possono essere usate per una distribuzione efficiente del pacchetto durante le fasi di debug e rilascio dello sviluppo.

##  <a name="building-appsbuilding-appsindexmd"></a>[Creazione di app](building-apps/index.md)

Questa sezione descrive il funzionamento del processo di compilazione e illustra come compilare APK specifici di ABI.

##  <a name="command-line-emulatorcommand-line-emulatormd"></a>[Emulatore della riga di comando](command-line-emulator.md)

Questo articolo illustra brevemente l'avvio dell'emulatore tramite la riga di comando.

## <a name="debuggingandroiddeploy-testdebuggingindexmd"></a>[Debug](~/android/deploy-test/debugging/index.md)

Le guide nella sezione consentono di eseguire il debug di un'applicazione con emulatori Android, dispositivi Android reali e il registro di debug.

##  <a name="setting-the-debuggable-attributeandroiddeploy-testdebuggable-attributemd"></a>[Impostazione dell'attributo Debuggable](~/android/deploy-test/debuggable-attribute.md)

Questo articolo illustra come impostare l'attributo debuggable in modo che strumenti come `adb` possano comunicare con JVM.

##  <a name="environmentenvironmentmd"></a>[Ambiente](environment.md)

Questo articolo descrive l'ambiente di esecuzione Xamarin.Android e le proprietà di sistema Android che hanno effetto sull'esecuzione del programma.

##  <a name="gdbgdbmd"></a>[GDB](gdb.md)

Questo articolo illustra come usare `gdb` per il debug di un'applicazione Xamarin.Android.

##  <a name="installing-a-system-appinstall-system-appmd"></a>[Installazione di un'app di sistema](install-system-app.md)

Questa guida illustra come installare un'app Xamarin.Android come applicazione di sistema in un dispositivo Android o come parte di una ROM personalizzata.

##  <a name="linking-on-androidlinkermd"></a>[Collegamento in Android](linker.md)

Questo articolo illustra il processo di collegamento usato da Xamarin.Android per ridurre le dimensioni finali di un'applicazione. Descrive i diversi livelli di collegamento che possono essere eseguiti e fornisce indicazioni e suggerimenti per la risoluzione dei problemi per poter limitare gli errori che potrebbero derivare dall'uso del linker.

## <a name="xamarinandroid-performanceandroiddeploy-testperformancemd"></a>[Xamarin.Android Performance](~/android/deploy-test/performance.md) (Prestazioni di Xamarin.Android)

Esistono varie tecniche per incrementare le prestazioni delle applicazioni compilate con Xamarin.Android. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

## <a name="preparing-an-application-for-releaseandroiddeploy-testrelease-prepindexmd"></a>[Preparazione di un'applicazione per il rilascio](~/android/deploy-test/release-prep/index.md)

Dopo aver scritto il codice di un'applicazione e averla testata, è necessario preparare il pacchetto per la distribuzione. La prima attività di preparazione del pacchetto è la compilazione dell'applicazione per il rilascio, che essenzialmente comporta l'impostazione di alcuni attributi dell'applicazione.

## <a name="signing-the-android-application-packageandroiddeploy-testsigningindexmd"></a>[Signing the Android Application Package](~/android/deploy-test/signing/index.md) (Firma del pacchetto dell'applicazione Android)

Informazioni su come creare un'identità di firma di Android e un nuovo certificato di firma per le applicazioni Android e su come firmare l'applicazione con il certificato di firma. Questo argomento illustra anche come esportare l'applicazione su disco per la distribuzione *ad hoc*. Il pacchetto dell'applicazione Android (APK) risultante può essere trasferito localmente in dispositivi Android senza passare attraverso un app store.

## <a name="publishing-an-applicationandroiddeploy-testpublishingindexmd"></a>[Publishing an Application](~/android/deploy-test/publishing/index.md) (Pubblicazione di un'applicazione)

Questa serie di articoli illustra i passaggi per la distribuzione al pubblico di un'applicazione creata con Xamarin.Android. La distribuzione può essere eseguita tramite canali, come ad esempio la posta elettronica, un server Web privato, Google Play o Amazon Appstore per Android.
