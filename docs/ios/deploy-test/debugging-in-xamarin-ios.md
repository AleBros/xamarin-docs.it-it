---
title: Debug di app Xamarin.iOS
description: Questo documento descrive come usare il debugger in Visual Studio per Mac o Visual Studio 2017 per eseguire il debug di un'applicazione Xamarin.iOS, inclusi l'impostazione di punti di interruzione, il debug wireless e molto altro.
ms.prod: xamarin
ms.assetid: 05460010-99E1-DC38-F855-2D691EF54484
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 3d1a3090318649e6f04a5b8f44dc4a9b2689a870
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119631"
---
# <a name="debugging-xamarinios-apps"></a>Debug di app Xamarin.iOS

_È possibile eseguire il debug delle applicazioni Xamarin.iOS con il debugger predefinito in Visual Studio per Mac o in Visual Studio._

Usare il supporto di debug nativo di Visual Studio per Mac per eseguire il debug del codice C# e di altri linguaggi gestiti e usare [LLDB](http://lldb.llvm.org/tutorial.html) quando è necessario eseguire il debug di codice C, C++ o Objective C che potrebbe venire collegato al progetto Xamarin.iOS.


> [!NOTE]
> Quando si compilano applicazioni in modalità di debug, Xamarin.iOS genererà applicazioni più lente e di dimensioni di gran lunga superiori perché ogni riga di codice deve essere instrumentata. Prima del rilascio, assicurarsi di creare una compilazione di rilascio.

Il debugger Xamarin.iOS è integrato nell'IDE e consente agli sviluppatori di eseguire il debug delle applicazioni Xamarin.iOS compilate con uno dei linguaggi gestiti supportati da Xamarin.iOS nel simulatore e sul dispositivo.

Il debugger Xamarin.iOS usa il [debugger Mono Soft](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/), quindi il codice generato e il runtime di Mono cooperano con l'IDE per offrire un'esperienza di debug. È diverso dai debugger hard, come LLDB o MDB, che controllano un programma senza le informazioni o la cooperazione del programma di cui viene eseguito il debug.

## <a name="setting-breakpoints"></a>Impostazione di punti di interruzione

Quando si è pronti per avviare il debug dell'applicazione, il primo passaggio da eseguire è l'[impostazione di punti di interruzione per l'applicazione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint). A questo scopo, fare clic nell'area del margine dell'editor, accanto al numero di riga del codice in corrispondenza del quale si vuole impostare il punto di interruzione:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/debugging1.png "Impostazione di punti di interruzione")](debugging-in-xamarin-ios-images/debugging1.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/debugging1a.png "Impostazione di punti di interruzione")](debugging-in-xamarin-ios-images/debugging1a.png#lightbox)

-----

È possibile visualizzare tutti i punti di interruzione impostati nel codice passando al **riquadro Punti di interruzione**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/image0a.png "Riquadro Punti di interruzione")](debugging-in-xamarin-ios-images/image0a.png#lightbox)

 Se il riquadro Punti di interruzione non viene visualizzato automaticamente, è possibile renderlo visibile selezionando _Visualizza > Debug Windows (Finestre di debug) > Punti di interruzione_
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/image0.png "Riquadro Punti di interruzione")](debugging-in-xamarin-ios-images/image0.png#lightbox)

 Se il riquadro Punti di interruzione non viene visualizzato automaticamente, è possibile renderlo visibile selezionando _Debug > Finestre > Punti di interruzione_
 
-----

Prima di iniziare il debug di un'applicazione, assicurarsi sempre che la configurazione sia impostata su **Debug**, perché contiene un utile set di strumenti per supportare il debug, ad esempio i punti di interruzione, l'uso dei visualizzatori di dati e la visualizzazione dello stack di chiamate:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/debugging7.png "Debug sul simulatore")](debugging-in-xamarin-ios-images/debugging7.png#lightbox)
[![](debugging-in-xamarin-ios-images/debugging7a.png "Debug su un dispositivo fisico")](debugging-in-xamarin-ios-images/debugging7a.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/debugging7c.png "Debug sul simulatore")](debugging-in-xamarin-ios-images/debugging7c.png#lightbox)
[![](debugging-in-xamarin-ios-images/debugging7d.png "Debug su un dispositivo fisico")](debugging-in-xamarin-ios-images/debugging7d.png#lightbox)

-----

## <a name="start-debugging"></a>Avvia debug
Per avviare il debug, selezionare il dispositivo di destinazione o un altro strumento simile nell'IDE:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/debugging7b.png "Selezionare il dispositivo di destinazione")](debugging-in-xamarin-ios-images/debugging7b.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/debugging7e.png "Selezionare il dispositivo di destinazione")](debugging-in-xamarin-ios-images/debugging7e.png#lightbox)

-----



Distribuire quindi l'applicazione premendo il pulsante **Esegui**.

Quando si raggiunge un punto di interruzione, il codice viene evidenziato in giallo:

[![](debugging-in-xamarin-ios-images/image2.png "Il codice verrà evidenziato in giallo")](debugging-in-xamarin-ios-images/image2.png#lightbox)

A questo punto, è possibile usare strumenti di debug, ad esempio per esaminare i valori degli oggetti, per ottenere altre informazioni su quello che accade nel codice:

[![](debugging-in-xamarin-ios-images/image3.png "Visualizzazione di un valore di colore")](debugging-in-xamarin-ios-images/image3.png#lightbox)

## <a name="conditional-breakpoints"></a>Punti di interruzione condizionali

È anche possibile impostare regole che determinano i casi in cui deve essere presente un punto di interruzione, ovvero aggiungendo un *punto di interruzione condizionale*.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per impostare un punto di interruzione condizionale, accedere alla **finestra Proprietà punto di interruzione** in uno dei due modi seguenti:


- Per aggiungere un nuovo punto di interruzione condizionale, fare clic con il pulsante destro del mouse sul margine dell'editor, a sinistra del numero di riga per il codice in cui si vuole impostare il punto di interruzione, e quindi scegliere Nuovo punto di interruzione:

    [![](debugging-in-xamarin-ios-images/image4.png "Selezionare Nuovo punto di interruzione")](debugging-in-xamarin-ios-images/image4.png#lightbox)

- Per aggiungere una condizione a un punto di interruzione, fare clic con il pulsante destro del mouse sul punto di interruzione e scegliere **Proprietà punto di interruzione** o selezionare il pulsante Proprietà, mostrato di seguito, nel **riquadro Punti di interruzione**:

    [![](debugging-in-xamarin-ios-images/image5.png "Riquadro Punti di interruzione")](debugging-in-xamarin-ios-images/image5.png#lightbox)


È quindi possibile immettere la condizione in base alla quale deve essere aggiunto il punto di interruzione:

[![](debugging-in-xamarin-ios-images/image6.png "Immettere la condizione in base alla quale deve essere aggiunto il punto di interruzione")](debugging-in-xamarin-ios-images/image6.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per impostare un punto di interruzione condizionale in Visual Studio 2015, prima [impostare un normale punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint). Fare clic con il pulsante destro del mouse sul punto di interruzione per visualizzare il menu di scelta rapida:

 [![](debugging-in-xamarin-ios-images/image4vs.png "Menu di scelta rapida del punto di interruzione")](debugging-in-xamarin-ios-images/image4vs.png#lightbox)

Selezionare **Condizioni** per visualizzare il menu _Impostazioni del punto di interruzione_:

 [![](debugging-in-xamarin-ios-images/image6vs.png "Menu Impostazioni del punto di interruzione")](debugging-in-xamarin-ios-images/image6vs.png#lightbox)

È possibile immettere qui le condizioni in base alle quali deve essere aggiunto il punto di interruzione

Per altre informazioni sull'uso delle condizioni per il punto di interruzione nelle versioni precedenti di Visual Studio, vedere la [documentazione di Visual Studio](https://docs.microsoft.com/visualstudio/debugger/using-breakpoints) in questo argomento.

-----

## <a name="navigating-through-code"></a>Spostamento nel codice

Quando viene raggiunto un punto di interruzione, lo strumento di debug permette di ottenere il controllo sull'esecuzione del programma. L'IDE visualizza quattro pulsanti, per eseguire e scorrere il codice.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

I pulsanti avranno l'aspetto seguente in Visual Studio per Mac:

 [![](debugging-in-xamarin-ios-images/image7.png "Gli strumenti di debug consentono allo sviluppatore di ottenere il controllo sull'esecuzione del programma")](debugging-in-xamarin-ios-images/image7.png#lightbox)

Questi sono:

- **Esegui/Arresta**: avvia/arresta l'esecuzione del codice, fino al punto di interruzione successivo.
- **Esegui istruzione/routine**: esegue la riga di codice successiva. Se la riga successiva è una chiamata di funzione, il pulsante esegue la funzione e si ferma alla riga di codice successiva, _dopo_ la funzione.
- **Esegui istruzione**: anche questo pulsante esegue la riga di codice successiva. Se la riga successiva è una chiamata di funzione, il pulsante si ferma alla prima riga della funzione, permettendo di continuare a eseguire il debug della funzione riga per riga. Se la riga successiva non è una funzione, il pulsante si comporta come il pulsante Esegui istruzione/routine.
- **Esci da istruzione/routine**: torna alla riga in cui è stata chiamata la funzione corrente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

I pulsanti avranno l'aspetto seguente in Visual Studio:

[![](debugging-in-xamarin-ios-images/image7vs.png "Gli strumenti di debug consentono allo sviluppatore di ottenere il controllo sull'esecuzione del programma")](debugging-in-xamarin-ios-images/image7vs.png#lightbox)

Questi sono:

- **Esegui/Arresta**: avvia/arresta l'esecuzione del codice, fino al punto di interruzione successivo.
- **Esegui istruzione/routine (F11)**: esegue la riga di codice successiva. Se la riga successiva è una chiamata di funzione, il pulsante esegue la funzione e si ferma alla riga di codice successiva, _dopo_ la funzione.
- **Esegui istruzione (F10)**: anche questo pulsante esegue la riga di codice successiva. Se la riga successiva è una chiamata di funzione, il pulsante si ferma alla prima riga della funzione, permettendo di continuare a eseguire il debug della funzione riga per riga. Se la riga successiva non è una funzione, il pulsante si comporta come il pulsante Esegui istruzione/routine.
- **Esci da istruzione/routine (MAIUSC+F11)**: torna alla riga in cui è stata chiamata la funzione corrente.

Per una documentazione più approfondita sul debug, vedere [Spostarsi nel codice con il debugger di Visual Studio](https://docs.microsoft.com/visualstudio/debugger/navigating-through-code-with-the-debugger).

-----

### <a name="breakpoints"></a>Punti di interruzione

È importante sottolineare che iOS concede alle applicazioni solo pochi secondi (10) per avviare e completare il metodo `FinishedLaunching` nel delegato dell'applicazione. Se l'applicazione non completa questo metodo in 10 secondi, iOS termina il processo.

È quindi quasi impossibile impostare i punti di interruzione nel codice di avvio del programma. Per eseguire il debug del codice di avvio, è consigliabile rimandare parte dell'inizializzazione e inserirla in un metodo richiamato dal timer o in qualche altro metodo di callback eseguito al termine di FinishedLaunching.

## <a name="device-diagnostics"></a>Diagnostica del dispositivo

Se si verifica un errore di configurazione del debugger, è possibile abilitare la diagnostica dettagliata aggiungendo "-v -v -v" agli altri argomenti di mtouch nelle opzioni del progetto. In questo modo le informazioni dettagliate sull'errore verranno visualizzate nella console del dispositivo.

 <a name="WiFi_Debugging" />

## <a name="wireless-debugging"></a>Debug wireless

L'impostazione predefinita in Xamarin.iOS prevede il debug dell'applicazione sui dispositivi tramite la connessione USB. A volte potrebbe essere necessario testare il collegamento/scollegamento del cavo del dispositivo USB per lo sviluppo di applicazioni basate su accessori esterni. In questi casi, è possibile usare il debug tramite la rete wireless.

Per altre informazioni sulla distribuzione e sul debug wireless, vedere la guida [Distribuzione wireless](~/ios/deploy-test/wireless-deployment.md).

<a name="Technical_Details" />

## <a name="technical-details"></a>Dettagli tecnici

Xamarin.iOS usa il nuovo debugger Mono Soft. Diversamente dal debugger Mono standard, che è un programma che controlla un processo separato usando le interfacce del sistema operativo, il debugger soft fa in modo che il runtime di Mono esponga la funzionalità di debug tramite un protocollo di collegamento.

All'avvio, un'applicazione di cui deve essere eseguito il debug contatta il debugger, che inizia a operare. In Xamarin.iOS per Visual Studio, Xamarin Mac Agent funge da intermediario tra l'applicazione (in Visual Studio) e il debugger.

Questo debugger soft richiede uno schema di debug cooperativo quando è in esecuzione sul dispositivo. Il file binario viene quindi compilato quando il debug è di dimensioni maggiori perché il codice è instrumentato per poter contenere codice aggiuntivo in ogni punto della sequenza per supportare il debug.

<a name="Accessing_the_Console" />


## <a name="accessing-the-console"></a>Accesso alla console

I log di arresto anomalo del sistema e l'output della classe Console verranno inviati alla console dell'iPhone. È possibile accedere a questa console con Xcode usando l'"Organizer" e selezionando il dispositivo dall'organizer.

In alternativa, se non si vuole avviare Xcode, è possibile usare [iPhone Configuration Utility](http://www.apple.com/support/iphone/enterprise/) di Apple per accedere direttamente alla console. Questa presenta l'ulteriore vantaggio di poter accedere ai log della console da un computer Windows se si deve eseguire il debug di un problema sul campo.

Per gli utenti di Visual Studio, sono disponibili alcuni log nella finestra Output, ma è consigliabile passare al Mac per log più completi e dettagliati.

-----

<a name="Debugging_Mono's_Class_Libraries" />


## <a name="debugging-monos-class-libraries"></a>Debug di librerie di classi di Mono

Xamarin.iOS viene fornito con il codice sorgente per le librerie di classi di Mono, che può essere usato per eseguire istruzioni passo a passo dal debugger e visualizzare il funzionamento sottostante.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Poiché questa funzionalità usa più memoria durante il debug, è disattivata per impostazione predefinita.


Per abilitare questa funzionalità, verificare che l'opzione **Esegui solo il debug del codice del progetto senza eseguire l'istruzione nel codice del framework** sia deselezionata nel menu _Visual Studio per Mac > Preferenze > Debugger_, come illustrato sotto:

[![](debugging-in-xamarin-ios-images/debugging6.png "Debug di librerie di classi di Mono")](debugging-in-xamarin-ios-images/debugging6.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per eseguire il debug delle librerie di classi in Visual Studio, è necessario disabilitare **Just My Code** nel menu _Debug > Opzioni_. Nel nodo _Debug > Generale_ selezionare la casella di controllo **Abilita Just My Code**:

[![](debugging-in-xamarin-ios-images/debugging6vs.png "Debug di librerie di classi di Mono")](debugging-in-xamarin-ios-images/debugging6vs.png#lightbox)

-----

Dopo avere eseguito questa operazione, è possibile avviare l'applicazione ed eseguire le istruzioni una alla volta di una delle librerie di classi principali di Mono.


## <a name="related-links"></a>Collegamenti correlati

- [Debug con Xamarin](/visualstudio/mac/debugging/)
- [Visualizzazioni dei dati](/visualstudio/mac/data-visualizations/)
- [Impostare un punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)
- [Esecuzione del codice un'istruzione alla volta](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)
- [Visualizzare le informazioni nella finestra del log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)
