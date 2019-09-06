---
title: Profilatura delle applicazioni Xamarin.iOS con Instruments
description: Questo documento descrive come usare l'app Apple Instruments per profilare un'applicazione Xamarin.iOS installata in un dispositivo o in un simulatore.
ms.prod: xamarin
ms.assetid: 70A8CAC8-20C2-655B-37C3-ACF9EA7874D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 9492c90274e28f14dee0e8cbcb2aaa1a55de2420
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282738"
---
# <a name="profiling-xamarinios-applications-with-instruments"></a>Profilatura delle applicazioni Xamarin.iOS con Instruments

Xcode **Instruments** è uno strumento che può essere usato per la profilatura delle app Xamarin.iOS su un dispositivo o nel simulatore. Mono usa il modello Just-in-Time per compilare il codice, ma Instruments non interpreta questo tipo di dati in modo appropriato. Pertanto l'uso di output di applicazioni basate sul simulatore con Instruments può risultare complicato.
A causa di questo problema, la presente guida indica come usare l'app dello sviluppatore per interpretare l'output di Instruments in questo documento.

## <a name="requirements"></a>Requisiti

Xcode Instruments può essere eseguito solo su Mac.

## <a name="opening-the-instruments-app"></a>Apertura dell'app Instruments

Selezionare il dispositivo ed eseguire l'app Instruments:

1. Aprire il progetto Xamarin.iOS in Visual Studio per Mac.
2. Selezionare la configurazione **Debug|iPhone**.
3. Connettere un dispositivo iOS al computer.
4. Nel menu **Esegui** selezionare **Carica nel dispositivo** . L'applicazione viene compilata e caricata nel dispositivo.
5. Nel menu **Strumenti** selezionare **Launch Instruments** (Avvia Instruments).


L'app Instruments viene aperta e visualizza la finestra di dialogo seguente:

 [![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png "Scelta di un modello di profilatura")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png#lightbox)

Fare clic per selezionare il modello **Allocazioni**. Anche gli altri modelli sono validi, ma questo articolo riguarda solo il modello di profilo **Allocazioni**.

Quindi selezionare il dispositivo e l'applicazione mediante il menu nella parte superiore della finestra:

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png "Selezionare il dispositivo e l'applicazione")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png#lightbox)

Il dispositivo iOS deve essere selezionato nel menu nella parte superiore della finestra e l'applicazione da profilare deve essere selezionata nel menu contiguo (nella schermata precedente l'applicazione è **MemoryDemo**).

Se il dispositivo non è elencato nel menu, controllare nella **Console** di Visual Studio per Mac la presenza di eventuali messaggi di errore visualizzati quando l'app viene distribuita al dispositivo. Assicurarsi anche che sia stato eseguito il provisioning del dispositivo per lo sviluppo tramite Xcode Organizer.

Fare clic sul pulsante **Scegli** per visualizzare la schermata seguente:

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png "Interfaccia di profilatura")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png#lightbox)

Fare clic sul pulsante di registrazione (cerchio rosso in alto a sinistra) per avviare la profilatura.

Lo screenshot seguente visualizza un esempio di profilatura mediante **Instruments**:

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png "Esempio di profilatura mediante Instruments")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png#lightbox)

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come avviare Xcode Instruments per monitorare un'app iOS direttamente da Visual Studio per Mac. Per un esempio di diagnosi di un problema con la memoria, vedere [Analisi dettagliata di Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md).

## <a name="related-links"></a>Collegamenti correlati

- [Analisi dettagliata di Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md)
- [Xamarin.iOS Garbage Collection (blog post)](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/) (Garbage Collection di Xamarin.iOS - post di blog)
