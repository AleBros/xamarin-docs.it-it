---
title: Distribuzione e test di app Xamarin.iOS
description: Questo documento contiene i collegamenti a varie guide che descrivono gli argomenti relativi alla distribuzione e al test di un'applicazione Xamarin.iOS. Ad esempio, distribuzione di app, file con estensione ipa, provisioning, distribuzione wireless, TestFlight e debug.
ms.prod: xamarin
ms.assetid: 2DBF3BF9-79E7-4E24-AF26-E34C972B0169
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: f3f5e27e97b7b62ade66ea2dc50a79ac03d51f90
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026464"
---
# <a name="deploying-and-testing-xamarinios-apps"></a>Distribuzione e test di app Xamarin.iOS

Questa sezione include argomenti che descrivono come eseguire il test di un'applicazione e come distribuirla. Gli argomenti includono gli strumenti di debug, la distribuzione ai tester e la pubblicazione di un'applicazione nell'App Store.

## <a name="app-distribution"></a>[Distribuzione app](~/ios/deploy-test/app-distribution/index.md)

Questo articolo illustra come configurare, compilare e pubblicare un'applicazione Xamarin.iOS per la distribuzione con vari metodi, tra i quali:

- [Distribuzione dell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuzione in-house (Enterprise)](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)

## <a name="ipa-deployment"></a>[Distribuzione tramite IPA](~/ios/deploy-test/app-distribution/ipa-support.md)

Le distribuzioni ad hoc e Enterprise consentono agli sviluppatori di creare pacchetti che possono essere distribuiti per i test o a utenti aziendali. Questo documento illustra come creare un pacchetto IPA che può essere sincronizzato in un dispositivo iOS mediante iTunes.

## <a name="provisioning"></a>[Provisioning](provisioning/index.md)

Questo set di guide include le informazioni di base sulla firma del codice e sul provisioning, ad esempio l'uso degli elenchi di proprietà e come effettuare il provisioning dell'app per i servizi dell'applicazione. 

## <a name="wireless-deployment"></a>[Distribuzione wireless](wireless-deployment.md)

 In Xcode 9 è stata introdotta l'opzione per la distribuzione in un dispositivo iOS o in Apple TV tramite una rete, invece di dover collegare i dispositivi ogni volta che si vuole distribuire l'app ed eseguirne il debug. Questa funzionalità è attualmente in anteprima.

## <a name="testflight"></a>[TestFlight](~/ios/deploy-test/testflight.md)

TestFlight ora è di proprietà da Apple ed è lo strumento principale per il test beta delle app Xamarin.iOS. Questo articolo descrive in dettaglio tutti i passaggi del processo TestFlight, dal caricamento dell'app all'interazione con iTunes Connect.

## <a name="debugging-in-xamarinios"></a>[Debug in Xamarin.iOS](~/ios/deploy-test/debugging-in-xamarin-ios.md)

Gli ambienti IDE di Visual Studio e Visual Studio per Mac supportano il debug delle applicazioni Xamarin.iOS sia nel simulatore iOS che nei dispositivi iOS. Questo articolo illustra come usare il debugger e come configurare le varie opzioni supportate.

## <a name="touchunit"></a>[Touch.Unit](~/ios/deploy-test/touch.unit.md)

Questo documento descrive come creare unit test per i progetti Xamarin.iOS.
Gli unit test con Xamarin.iOS vengono eseguiti mediante il framework Touch.Unit, che include un test runner iOS e una versione modificata del framework [NUnitLite](http://www.nunitlite.com/), dotata di un set di API note che semplificano la creazione di unit test.

## <a name="using-instruments-to-detect-native-leaks-using-markheap"></a>[Uso di Instruments per rilevare le perdite native mediante MarkHeap](~/ios/deploy-test/using-instruments-to-detect-native-leaks-using-markheap.md)

Questo articolo illustra l'uso di Instruments su qualsiasi dispositivo iOS e in qualsiasi applicazione Xamarin.iOS. Descrive anche la profilatura delle applicazioni nel simulatore.

## <a name="walkthrough---using-apples-instrument-tool"></a>[Procedura dettagliata: Uso di Apple Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md)

Questo articolo illustra in dettaglio l'uso di Apple Instruments per diagnosticare problemi di memoria in un'applicazione iOS compilata con Xamarin. Illustra come avviare Instruments, come creare snapshot dell'heap e come analizzare la crescita della memoria. Descrive anche come usare Instruments per visualizzare ed evidenziare le righe di codice specifiche che causano il problema di memoria.

## <a name="linking-on-ios"></a>[Collegamento in iOS](linker.md)

Illustra il funzionamento del linker per assicurare le dimensioni minime del pacchetto dell'applicazione e come modificarne le impostazioni e l'utilizzo.

## <a name="xamarinios-performance"></a>[Prestazioni di Xamarin.iOS](performance.md)

Esistono varie tecniche per incrementare le prestazioni delle applicazioni create con Xamarin.iOS. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

## <a name="mtouch"></a>[mtouch](mtouch.md)

Note e informazioni su mtouch.exe, lo strumento da riga di comando che compila il progetto in un'applicazione utilizzabile con iOS.

## <a name="ios-build-mechanics"></a>[Meccanica di compilazione iOSIOS Build Mechanics](ios-build-mechanics.md)

Questa guida esplora come temporizzare le app e usare metodi che permettono compilazioni più rapide per tutte le configurazioni di compilazione.
