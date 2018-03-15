---
title: Emulatore di Visual Studio per Android
description: Questa guida illustra come configurare e usare l'emulatore di Visual Studio per Android per sviluppare le app Xamarin.Android in Visual Studio 2015.
ms.topic: article
ms.prod: xamarin
ms.assetid: CD128CB9-499F-4558-B49F-77248824EFDF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a31d90e6d5abd574eb6187953082e1b70f66a113
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="visual-studio-android-emulator"></a>Emulatore di Visual Studio per Android

_Questa guida illustra come configurare e usare l'emulatore di Visual Studio per Android per sviluppare le app Xamarin.Android in Visual Studio 2015._

## <a name="visual-studio-android-emulator-overview"></a>Panoramica dell'emulatore di Visual Studio per Android

Microsoft Visual Studio 2015 include un emulatore per Android che può essere usato come destinazione per il debug di un'app Xamarin.Android: *Visual Studio Emulator for Android*. L'emulatore usa le funzionalità Hyper-V del computer di sviluppo offrendo tempi di avvio ed esecuzione più rapidi rispetto all'emulatore predefinito accluso ad Android SDK. Visual Studio Emulator for Android può essere usato come alternativa all'emulatore predefinito di Android SDK quando si sviluppa un'applicazione Xamarin.Android.

Questa guida illustra come avviare l'emulatore Android di Microsoft da Visual Studio per testare l'app e descrive le varie funzionalità disponibili nell'emulatore. Verrà spiegato come selezionare i *profili di dispositivo*, simili alle definizioni di dispositivo nell'emulatore predefinito di Android SDK, per simulare i diversi tipi di dispositivi Android. Infine, una sezione sulla risoluzione dei problemi illustra i problemi più comuni e le relative soluzioni.

## <a name="requirements"></a>Requisiti

Per eseguire l'emulatore, il computer deve soddisfare i requisiti per l'esecuzione di Hyper-V. Hyper-V richiede una versione a 64 bit dell'edizione Pro di Windows 8, Windows 8.1, Windows10 o successive. Per altre informazioni sui requisiti di sistema, vedere [Requisiti di sistema per Visual Studio Emulator for Android](https://msdn.microsoft.com/en-us/library/mt228280.aspx).

> [!NOTE]
> Non è possibile usare HAXM (usato dall'emulatore di Android SDK) se è abilitato Hyper-V. Per altre informazioni sulle limitazioni e i potenziali problemi con HAXM, vedere la sezione relativa ai [conflitti di virtualizzazione HAXM](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#virt-conflicts).


## <a name="running-the-emulator"></a>Esecuzione dell'emulatore

Visual Studio rende disponibili diversi profili di dispositivo di destinazione preconfigurati nel menu a discesa **Destinazione di debug**, come illustra la schermata seguente. Le destinazioni dell'emulatore Android di Microsoft sono precedute da **VS Emulator**:

[![Profili di dispositivo di destinazione preconfigurati](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs-sml.png)](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs.png#lightbox)

Quando Visual Studio avvia un'applicazione Xamarin.Android, l'emulatore viene avviato con la destinazione del dispositivo selezionato e l'app viene distribuita all'emulatore. Viene visualizzato un messaggio nell'angolo inferiore sinistro di Visual Studio per indicare che l'emulatore si sta avviando:

[![Avvio dell'emulatore di Visual Studio](visual-studio-android-emulator-images/02-emulator-starting-vs-sml.png)](visual-studio-android-emulator-images/02-emulator-starting-vs.png#lightbox)

Dopo un ritardo di avvio, viene visualizzata la schermata dell'emulatore come illustrato in basso a sinistra. Trascinare verso l'alto l'icona con il lucchetto per sbloccare il dispositivo.
L'app Xamarin.Android viene quindi eseguita nell'emulatore come illustrato a destra:

[![Screenshot dell'emulatore](visual-studio-android-emulator-images/03-first-screen-vs-sml.png)](visual-studio-android-emulator-images/03-first-screen-vs.png#lightbox)

Come con l'emulatore predefinito di Android SDK, è possibile impostare punti di interruzione nel codice, esaminare le variabili e visualizzare lo stack di chiamate. La barra degli strumenti verticale a destra dell'emulatore consente di accedere alle funzionalità dell'emulatore:

[![Pulsanti della barra degli strumenti verticale](visual-studio-android-emulator-images/04-vertical-toolbar-vs-sml.png)](visual-studio-android-emulator-images/04-vertical-toolbar-vs.png#lightbox)

Nell'elenco seguente sono riportate le funzioni dei pulsanti della barra degli strumenti verticale:

-   **Chiudi** &ndash; Arresta l'applicazione dell'emulatore. Questo pulsante non viene usato spesso &ndash; in genere, l'emulatore viene lasciato in esecuzione dopo il primo avvio, per evitare il ritardo di riavvio dell'emulatore, e viene chiuso solo quando non è più necessario.

-   **Riduci a icona** &ndash; Lascia l'emulatore in esecuzione ma lo riduce a icona nella barra delle applicazioni.

-   **Accendi/spegni** &ndash; Simula l'attivazione e la disattivazione del dispositivo (l'emulatore rimane in esecuzione).

-   **Multitocco** &ndash; Sovrappone diversi punti sul display del dispositivo che agiscono come punti di contatto per la compressione e lo zoom.
    Trascinando un punto, l'altro punto si sposta nella direzione opposta simulando il movimento di due dita.

-   **Input del mouse da un singolo punto** &ndash; Riporta il dispositivo all'input da un solo punto (dopo l'uso dell'input multitocco).

-   **Ruota a destra/sinistra** &ndash; Consente di testare il modo in cui l'applicazione risponde alle modifiche dell'orientamento. Ad esempio, la prima volta che si fa clic sul pulsante *Ruota a sinistra* l'emulatore passa alla modalità orizzontale. Quando si preme il pulsante *Ruota a destra* l'emulatore torna alla modalità verticale.

-   **Adatta allo schermo** &ndash; Aumenta le dimensioni della schermata dell'emulatore in modo che si adattino alla schermata del desktop.

-   **Zoom** &ndash; Ridimensiona la schermata dell'emulatore del 33%, 50%, 66%, 100% o in base a una percentuale personalizzata.


Il pulsante *Strumenti aggiuntivi* visualizza una finestra di dialogo che contiene le funzionalità aggiuntive dell'emulatore:

[![Finestra di dialogo Strumenti aggiuntivi](visual-studio-android-emulator-images/05-additional-tools-vs-sml.png)](visual-studio-android-emulator-images/05-additional-tools-vs.png#lightbox)


Ogni funzionalità aggiuntiva è disponibile da una riga di schede nella parte superiore della finestra di dialogo:

-   **Accelerometro** &ndash; Simula il movimento del dispositivo nello spazio 3D.

-   **Posizione** &ndash; Visualizza una mappa che può essere usata per selezionare e simulare una posizione GPS. In questa mappa è possibile creare *punti della mappa* per simulare lo spostamento tra le posizioni.

-   **Batteria** &ndash; Visualizza un dispositivo di scorrimento per simulare la quantità di carica rimasta nella batteria.

-   **Schermata** &ndash; In questa scheda un pulsante consente di **acquisire** una schermata e di visualizzare un'anteprima immediata. Il pulsante *Salva* consente di salvare la schermata.

-   **Fotocamera** &ndash; Simula lo scatto di una foto usando un'immagine animata predefinita, un'immagine da un file o da una webcam collegata nel computer host. È possibile selezionare fotocamere frontali o posteriori.

-   **Scheda SD** &ndash; L'emulatore può rendere disponibile una cartella del computer host come scheda SD per il dispositivo.
    Quando l'app legge e scrive i file nella scheda SD simulata, è possibile accedervi direttamente dal desktop senza usare il comando `adb`.

-   **Rete** &ndash; Visualizza un riepilogo delle impostazioni di rete dell'emulatore, che riusa la connessione di rete del computer host.

Per altre informazioni sull'uso di queste funzionalità, vedere [Presentazione dell'emulatore di Visual Studio per Android](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/).



## <a name="configuring-device-profiles"></a>Configurazione dei profili di dispositivo

L'emulatore Android di Microsoft include un set di profili di dispositivo che rappresentano le versioni di Android, le dimensioni dello schermo e le proprietà hardware più diffuse dei dispositivi Android disponibili sul mercato. Inoltre, questi profili di dispositivo sono già configurati per diverse versioni di Android, ad esempio KitKat, Lollipop e Marshmallow.

Per installare, disinstallare e avviare i profili di dispositivo si usa la *Gestione emulatori*. Nel menu **Strumenti** selezionare **Visual Studio Emulator for Android...**  come indicato in questo screenshot:

[![Avvio dell'emulatore dal menu Strumenti](visual-studio-android-emulator-images/06-launch-emulator-manager-vs-sml.png)](visual-studio-android-emulator-images/06-launch-emulator-manager-vs.png#lightbox)

Viene aperta una finestra di dialogo contenente i **profili di dispositivo**. I profili installati sono evidenziati nella parte superiore dell'elenco di profili di dispositivo. I profili non installati, ma disponibili per l'installazione, sono visualizzati in grigio:

[![Icone dei profili dei dispositivi](visual-studio-android-emulator-images/07-device-profiles-vs-sml.png)](visual-studio-android-emulator-images/07-device-profiles-vs.png#lightbox)

Per installare un nuovo profilo, fare clic sull'icona di installazione del profilo, ovvero sulla freccia verso il basso come illustrato nello screenshot precedente. Ad esempio, quando si fa clic sull'icona di installazione del profilo per **5.7" Marshmallow (6.0.0) XHDPI Phone**, Gestione emulatori scarica il profilo come illustrato di seguito:

[![Esempio di download dei profili](visual-studio-android-emulator-images/08-downloading-profile-vs-sml.png)](visual-studio-android-emulator-images/08-downloading-profile-vs.png#lightbox)

Dopo il download, il profilo di dispositivo viene evidenziato per indicare che il profilo è stato installato correttamente. Fare clic sull'icona di *visualizzazione dei dettagli* per visualizzare il tipo di piattaforma, l'architettura CPU, le dimensioni o la risoluzione dello schermo e la memoria disponibile nel dispositivo:

[![Visualizzazione dei dettagli del profilo di dispositivo](visual-studio-android-emulator-images/09-show-details-vs-sml.png)](visual-studio-android-emulator-images/09-show-details-vs.png#lightbox)

Quando il menu a discesa **Destinazione di debug** di Visual Studio è aperto, il profilo di dispositivo appena installato è disponibile come destinazione:

[![Nuovo profilo nel menu a discesa della destinazione](visual-studio-android-emulator-images/10-debug-target-vs-sml.png)](visual-studio-android-emulator-images/10-debug-target-vs.png#lightbox)

Questo elenco può essere abbreviato facendo clic su **Rimuovi il profilo** in *Gestione emulatori* per rimuovere i profili di dispositivo inutilizzati. Si noti che attualmente non è possibile creare un profilo di dispositivo personalizzato in questo emulatore.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione descrive alcuni errori comuni, e le relative soluzioni, che si verificano quando si usa *Visual Studio Emulator for Android* con Xamarin.Android.

<a name="cant_connect" />

### <a name="emulator-will-not-start"></a>L'emulatore non si avvia

In alcuni casi l'emulatore non viene avviato se sono presenti incompatibilità tra il processore host e la macchina virtuale Hyper-V. Per risolvere questo problema, configurare Hyper-V in modo da limitare le funzionalità del processore che si possono riscontrare in una macchina virtuale &ndash; questo migliora la compatibilità della macchina virtuale con diverse versioni del processore host.
Attenersi alla seguente procedura per apportare questa modifica:

1.  Fare clic sul pulsante **Start**, digitare **MMC** e premere **INVIO**. Fare clic su **Console di gestione di Hyper-V** come illustrato di seguito:

    [![Console di gestione di Hyper-V](visual-studio-android-emulator-images/15-launch-hyperv-manager.png)](visual-studio-android-emulator-images/15-launch-hyperv-manager.png#lightbox)

2.  Nel riquadro **Macchine virtuali** di Hyper-V fare clic con il pulsante destro del mouse sull'emulatore da usare per modificarlo e fare clic su **Impostazioni...**":

    [![Voce di menu Impostazioni della macchina virtuale](visual-studio-android-emulator-images/16-vm-settings.png)](visual-studio-android-emulator-images/16-vm-settings.png#lightbox)

3.  Nella finestra delle impostazioni individuare la sezione **Compatibilità** (in **Hardware > Processore**) e abilitare l'opzione **Esegui migrazione a un computer fisico con una versione diversa del processore**:

    [![Opzione di migrazione selezionata](visual-studio-android-emulator-images/17-set-compatibility-vs-sml.png)](visual-studio-android-emulator-images/17-set-compatibility-vs.png#lightbox)

4.  Fare clic su **OK** e chiudere la finestra della Console di gestione di Hyper-V.



### <a name="app-deploys-and-starts-but-fails-immediately"></a>L'app distribuisce, si avvia ma si arresta immediatamente

In questo caso l'emulatore viene avviato, l'app distribuisce correttamente all'emulatore l'applicazione si avvia. Tuttavia, nell'app viene immediatamente generato un errore.
In molti casi ciò è dovuto anche all'incompatibilità tra il processore host e la macchina virtuale Hyper-V. Per correggere l'errore, seguire le istruzioni della sezione [L'emulatore non si avvia](#cant_connect) (sopra).


### <a name="emulator-stops-with-the-diagnostic-message-libaot-mscorlibdllso-not-found"></a>L'emulatore si arresta con il messaggio di diagnostica: **libaot-mscorlib.dll.so non trovato**

Per risolvere questo errore, attenersi alla procedura seguente per disabilitare la distribuzione rapida:

1.  Seguire le istruzioni della sezione [L'emulatore non si avvia](#cant_connect) (sopra).

2.  Fare doppio clic sulle **proprietà** del progetto.

3.  Fare clic su **Opzioni Android** e deselezionare **Usa Fast Deployment (solo modalità di debug)**:

    [![Opzione Usa Fast Deployment deselezionata](visual-studio-android-emulator-images/18-fast-deployment-vs-sml.png)](visual-studio-android-emulator-images/18-fast-deployment-vs.png#lightbox)



### <a name="drag-and-drop-does-not-work"></a>Il trascinamento della selezione non funziona

Se *Visual Studio Emulator for Android* viene avviato come amministratore oppure da Visual Studio mentre Visual Studio viene eseguito con privilegi di amministratore, il trascinamento della selezione dei file APK o ZIP possono non funzionare. Per risolvere questo problema, eseguire *Visual Studio Emulator for Android* senza autorizzazioni elevate, ad esempio non come amministratore.


### <a name="other-errors"></a>Altri errori

I suggerimenti per la risoluzione dei problemi presentati in precedenza riguardano i problemi più comuni che si possono riscontrare quando si usa l'emulatore di Visual Studio per Android con Xamarin.Android. Per una guida più completa alla risoluzione dei problemi dell'emulatore di Visual Studio per Android, vedere [Troubleshooting Visual Studio Emulator for Android](https://msdn.microsoft.com/en-us/library/mt228282.aspx) (Risoluzione dei problemi di Visual Studio Emulator for Android).



## <a name="summary"></a>Riepilogo

Questo articolo introduce *Visual Studio Emulator for Android*, spiega come usare l'emulatore per eseguire il debug delle app Xamarin.Android in Visual Studio, descrive le funzioni dei pulsanti della barra degli strumenti verticale e presenta una breve panoramica delle funzionalità disponibili nella finestra di dialogo **Strumenti aggiuntivi**. Spiega come usare la *Gestione emulatori* per installare, disinstallare e avviare i profili di dispositivo. Nella sezione *Risoluzione dei problemi* sono illustrati i problemi, e le relative soluzioni, che si riscontrano comunemente quando si usa l'emulatore.


## <a name="related-links"></a>Collegamenti correlati

- [Visual Studio Emulator for Android](https://www.visualstudio.com/en-us/explore/msft-android-emulator-vs.aspx)
- [Presentazione dell'emulatore di Visual Studio per Android](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/)
