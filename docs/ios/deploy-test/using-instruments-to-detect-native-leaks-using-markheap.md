---
title: Profilatura delle applicazioni Xamarin.iOS con Instruments
description: Come usare Instruments su un'applicazione Xamarin.iOS in un dispositivo o nel simulatore.
ms.topic: article
ms.prod: xamarin
ms.assetid: 70A8CAC8-20C2-655B-37C3-ACF9EA7874D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 9fec6fca0bf7930a59a6927557786a3ed7a412da
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="profiling-xamarinios-applications-with-instruments"></a>Profilatura delle applicazioni Xamarin.iOS con Instruments

_Come usare Instruments su un'applicazione Xamarin.iOS in un dispositivo o nel simulatore._

Xcode **Instruments** è uno strumento che può essere usato per la profilatura delle app Xamarin.iOS su un dispositivo o nel simulatore. Mono usa il modello Just-in-Time per compilare il codice, ma Instruments non interpreta questo tipo di dati in modo appropriato. Pertanto l'uso di output di applicazioni basate sul simulatore con Instruments può risultare complicato.
A causa di questo problema, la presente guida indica come usare l'app dello sviluppatore per interpretare l'output di Instruments in questo documento.

## <a name="requirements"></a>Requisiti

Xcode Instruments può essere eseguito solo su Mac.

## <a name="opening-the-instruments-app"></a>Apertura dell'app Instruments

Selezionare il dispositivo ed eseguire l'app Instruments:

1.  Aprire il progetto Xamarin.iOS in Visual Studio per Mac.
2.  Selezionare la configurazione **Debug|iPhone**.
3.  Connettere un dispositivo iOS al computer.
4.  Nel menu **Esegui** selezionare **Carica nel dispositivo** . L'applicazione viene compilata e caricata nel dispositivo.
5.  Nel menu **Strumenti** selezionare **Launch Instruments** (Avvia Instruments).


L'app Instruments viene aperta e visualizza la finestra di dialogo seguente:

 [ ![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png "Scelta di un modello di profilo")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png)

Fare clic per selezionare il modello **Allocazioni**. Anche gli altri modelli sono validi, ma questo articolo riguarda solo il modello di profilo **Allocazioni**.

Quindi selezionare il dispositivo e l'applicazione mediante il menu nella parte superiore della finestra:

[ ![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png "Selezionare il dispositivo e l'applicazione")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png)

Il dispositivo iOS deve essere selezionato nel menu nella parte superiore della finestra e l'applicazione da profilare deve essere selezionata nel menu contiguo (nella schermata precedente l'applicazione è **MemoryDemo**).

Se il dispositivo non è elencato nel menu, controllare nella **Console** di Visual Studio per Mac la presenza di eventuali messaggi di errore visualizzati quando l'app viene distribuita al dispositivo. Assicurarsi anche che sia stato eseguito il provisioning del dispositivo per lo sviluppo tramite Xcode Organizer.

Fare clic sul pulsante **Scegli** per visualizzare la schermata seguente:

[ ![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png "Interfaccia di profilatura")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png)

Fare clic sul pulsante di registrazione (cerchio rosso in alto a sinistra) per avviare la profilatura.

Lo screenshot seguente visualizza un esempio di profilatura mediante **Instruments**:

[ ![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png "Esempio di profilatura mediante Instruments")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png)

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come avviare Xcode Instruments per monitorare un'app iOS direttamente da Visual Studio per Mac. Per un esempio di diagnosi di un problema con la memoria, vedere [Analisi dettagliata di Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md).

## <a name="related-links"></a>Collegamenti correlati

- [Analisi dettagliata di Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md)
- [Garbage Collection di Xamarin.iOS](https://krumelur.me/2015/04/27/xamarin-ios-the-garbage-collector-and-me/)
