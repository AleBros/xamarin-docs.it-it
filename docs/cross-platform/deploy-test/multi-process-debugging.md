---
title: Debug a più processori
ms.prod: xamarin
ms.assetid: 852F8AB1-F9E2-4126-9C8A-12500315C599
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: c1f2781477c04e79ad190f0067d685af1c0a4119
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="multi-process-debugging"></a>Debug a più processori

È molto comune che le soluzioni moderne sviluppate in Visual Studio per Mac abbiano più progetti indirizzati a diverse piattaforme. Ad esempio, una soluzione potrebbe avere un progetto di applicazione per dispositivi mobili basato sui dati forniti da un progetto di servizio web. Sviluppando questa soluzione, uno sviluppatore potrebbe avere la necessità di disporre di entrambi i progetti in esecuzione contemporaneamente per risolvere gli errori. A partire dalla [versione del ciclo 9 di Xamarin](https://releases.xamarin.com/stable-release-cycle-9/), è ora possibile eseguire il debug di più processi in esecuzione contemporaneamente tramite Visual Studio per Mac. Questo permette di impostare punti di interruzione, controllare le variabili e visualizzare i thread in più di un progetto in esecuzione. Ciò è noto come _debug di più processi_. 

In questa guida verranno illustrate alcune delle modifiche apportate a Visual Studio per Mac per supportare il debug di più processi, le modalità di configurazione di soluzioni per eseguire il debug di più processi e il collegamento a processi esistenti con Visual Studio per Mac.

## <a name="requirements"></a>Requisiti

Il debug di più processi richiede Visual Studio per Mac.

## <a name="ide-changes"></a>Modifiche IDE

Per offrire assistenza agli sviluppatori nel debug di più processi, Visual Studio per Mac ha subito alcune modifiche all'interfaccia utente. Visual Studio per Mac ha una **barra degli strumenti di debug** aggiornata e una nuova sezione **configurazione delle soluzioni** nella cartella **Opzioni soluzione**. Inoltre, il **riquadro dei thread** visualizza ora i processi in esecuzione e i thread per ogni processo. Visual Studio per Mac visualizza anche più riquadri di debug, uno per ogni processo, per alcune operazioni quali **Output applicazione**.

### <a name="solution-configurations"></a>Configurazioni soluzione

Per impostazione predefinita, Visual Studio per Mac visualizzerà un unico progetto nell'area **Configurazione soluzione** della barra degli strumenti di debug. Quando viene avviata una sessione di debug, questo è il progetto che sarà avviato da Visual Studio per Mac e a cui sarà collegato il debugger.

Per avviare ed eseguire il debug di più processi in Visual Studio per Mac, è necessario creare una _configurazione delle soluzioni_. Una configurazione delle soluzioni descrive i progetti di una soluzione che dovrebbero essere inclusi quando viene avviata una sessione di debug facendo clic sul pulsante **Start** o premendo la combinazione & #8984; & #8617; (**Cmd-Invio**). Nella schermata riportata di seguito è riportato un esempio di soluzione in Visual Studio per Mac che dispone di più configurazioni delle soluzioni:

![](multi-process-debugging-images/mpd01-xs.png "Soluzione con più configurazioni delle soluzioni")

### <a name="parts-of-the-debug-toolbar"></a>Parti della barra degli strumenti di debug

La barra degli strumenti di debug è stata modificata per consentire di selezionare una configurazione delle soluzioni tramite un menu di scelta rapida. In questa schermata vengono illustrate le parti della barra degli strumenti di debug:

![](multi-process-debugging-images/mpd02-xs.png "Parti della barra degli strumenti di debug")

1. **Configurazione soluzione** -È possibile impostare la configurazione della soluzione facendo clic su Configurazione soluzione nella barra degli strumenti di debug e selezionando la configurazione dal menu di scelta rapida:

    ![](multi-process-debugging-images/mpd03-xs.png "Esempio di menu di scelta rapida con le configurazioni delle soluzioni")

2. **Destinazione compilazione** - Identifica la destinazione di compilazione per i progetti. È rimasta invariata rispetto alle versioni precedenti di Visual Studio per Mac.
3. **Destinazioni dei dispositivi** - Consente di selezionare i dispositivi su cui verrà eseguita la soluzione. È possibile identificare un dispositivo o un emulatore distinto per ogni progetto:

    ![](multi-process-debugging-images/mpd04-xs.png "Menu di scelta rapida con i dispositivi per un progetto")

### <a name="multiple-debug-pads"></a>Più riquadri di debug

Quando viene avviata la configurazione di più soluzioni, vengono visualizzati più volte alcuni riquadri di Visual Studio per Mac, uno per ogni processo. Ad esempio, nella schermata seguente sono visualizzati due riquadri **Output applicazione** per una soluzione che esegue due progetti:

![](multi-process-debugging-images/mpd05-xs.png "Riquadro di output per una configurazione delle soluzioni")

### <a name="multiple-processes-and-the-active-thread"></a>Più processi e _Thread attivo_

Quando viene rilevato un punto di interruzione in un processo, tale processo sospende l'esecuzione, mentre gli altri processi continuano a essere eseguiti. In uno scenario con un unico processo, Visual Studio per Mac può visualizzare agevolmente informazioni quali thread, variabili locali, output dell'applicazione in un unico set di riquadri. Tuttavia, quando sono presenti più processi con più punti di interruzione e potenzialmente più thread, può rivelarsi impegnativo per gli sviluppatori gestire le informazioni provenienti da una sessione di debug che sta tentando di visualizzare tutte le informazioni di tutti i thread (e processi) contemporaneamente.

Per risolvere questo problema, Visual Studio per Mac visualizzerà solo le informazioni provenienti da un thread alla volta, noto come _thread attivo_. Il primo thread che viene sospeso in un punto di interruzione viene considerato il _thread attivo_. Il thread attivo è il thread su cui è concentrata l'attenzione dello sviluppatore. I comandi di debug, come **Esegui istruzione/routine** &#8679;&#8984;O (Shift-Cmd-O), verranno generati per il thread attivo.

Il **riquadro thread** visualizzerà informazioni per tutti i processi e thread in esame nella configurazione delle soluzioni e fornirà indicazioni visive riguardo a che cos'è il thread attivo:

![](multi-process-debugging-images/mpd06-xs.png "Riquadro thread per una configurazione delle soluzioni")

I thread sono raggruppati in base al processo che li ospita. Il nome del progetto e l'ID del thread attivo verranno visualizzati in grassetto e verrà visualizzata una freccia rivolta verso destra nella barra accanto al thread attivo. Nella schermata precedente, **thread 1 #** in **Id processo 48703** (**FirstProject**) costituiscono il thread attivo.

Durante il debug di più processi, è possibile passare al thread attivo per visualizzare informazioni di debug per tale processo (o thread) utilizzando il **riquadro thread**. Per passare al thread attivo, selezionare il thread desiderato nel **riquadro thread** e fare doppio clic su di esso.

#### <a name="stepping-through-code-when-multiple-projects-are-stopped"></a>Esecuzione di codice istruzione per istruzione quando vengono arrestati più progetti

Quando due (o più) progetti hanno dei punti di interruzione, Visual Studio per Mac sospende entrambi i processi. È possibile soltanto **eseguire un'istruzione/routine** di codice nel thread attivo. L'altro processo viene messo in pausa fino a quando una modifica dell'ambito consente al debugger di spostare l'attenzione dal thread attivo. Si consideri ad esempio la seguente schermata di Visual Studio per Mac che esegue il debug di due progetti:

![](multi-process-debugging-images/mpd09-xs.png  "Visual Studio per Mac esegue il debug di due progetti")

In questa schermata, ogni soluzione ha il proprio punto di interruzione. All'avvio del debug, il primo punto di interruzione che si trova è nella **riga 10** di `MainClass` in **SecondProject**. Poiché entrambi i progetti dispongono di punti di interruzione, ogni processo viene interrotto. Una volta rilevato il punto di interruzione, ogni chiamata di **Esegui istruzione/routine** fa avanzare istruzione per istruzione nel codice in Visual Studio per Mac nel thread attivo.

L'avanzamento nel codice è limitato al thread attivo, di conseguenza Visual Studio per Mac avanzerà, una riga di codice alla volta, mentre l'altro processo è ancora in pausa.

Utilizzando la schermata precedente come esempio, quando il ciclo `for` è terminato, Visual Studio per Mac consentirà l'esecuzione di **FirstProject** fino a incontrare il punto di interruzione nella **riga 11** in `MainClass`. Per ogni comando **Esegui istruzione/routine**, il debugger avanzerà riga per riga in **FirstProject**, fino a quando gli algoritmi euristici interni di Visual Studio per Mac commutano nuovamente il thread attivo su  **SecondProject**.

Se solo uno dei progetti avesse un punto di interruzione impostato, potrebbe essere messo in pausa soltanto quel processo. L'altro progetto continuerà a funzionare fino a quando non sarà messo in pausa dallo sviluppatore o sarà stato aggiunto un punto di interruzione.

### <a name="pausing-and-resuming-a-processes"></a>Sospensione e ripresa di un processo

È possibile sospendere o riprendere un processo facendo clic sul processo e selezionando **Sospendi** o **Riprendi** dal menu di scelta rapida:

![](multi-process-debugging-images/mpd08-xs.png "Sospendi o Riprendi nel riquadro di thread")

L'aspetto della barra degli strumenti di debug cambierà a seconda dello stato dei progetti in fase di debug. Quando vengono eseguiti più progetti, la barra degli strumenti di debug visualizzerà sia il pulsante **Sospendi** che il pulsante **Riprendi** laddove è presente almeno un progetto in esecuzione e uno in sospensione:

![](multi-process-debugging-images/mpd07-xs.png  "Barra degli strumenti di debug")

Facendo clic sul pulsante **Sospendi** nella **barra degli strumenti di debug** vengono sospesi tutti i processi in corso di debug, mentre facendo clic sul pulsante **Riprendi** vengono ripresi tutti i processi sospesi.

### <a name="debugging-a-second-project"></a>Debug di un secondo progetto

È inoltre possibile eseguire il debug di un secondo progetto una volta avviato il primo progetto da Visual Studio per Mac. Una volta avviato il primo progetto, **fare clic con il pulsante destro del mouse* sul progetto nel **riquadro della soluzione** e selezionare **Start Debugging Item** (Avvia debug elemento):

![](multi-process-debugging-images/mpd13-xs.png  "Avvia debug elemento")

## <a name="creating-a-solution-configuration"></a>Creazione di una configurazione della soluzione

Una _configurazione della soluzione_ indica a Visual Studio per Mac il progetto da eseguire quando viene avviata una sessione di debug con il pulsante **Avvia**. Può essere presente più di una configurazione della soluzione per ogni soluzione. Ciò consente di specificare quali progetti vengono eseguiti durante il debug del progetto.

Per creare una nuova configurazione della soluzione in Xamarin Studio:

1. Aprire la finestra di dialogo **Opzioni soluzione** in Visual Studio per Mac e selezionare **Esegui > Configurazioni**:

    ![](multi-process-debugging-images/mpd10-xs.png "Configurazione della soluzione nella finestra di dialogo Opzioni soluzione")

2. Fare clic sul pulsante **Nuova** e immettere il nome della nuova configurazione della soluzione, quindi fare clic su **Crea**. Verrà visualizzata la nuova configurazione della soluzione nella finestra **Configurazioni**:

    ![](multi-process-debugging-images/mpd11-xs.png  "Denominazione di una nuova configurazione della soluzione")

3. Selezionare la nuova configurazione di esecuzione nell'elenco di configurazioni. Nella finestra di dialogo **Opzioni soluzione** verrà visualizzato ogni progetto nella soluzione. Controllare ogni progetto che deve essere avviato quando viene avviata una sessione di debug:

    ![](multi-process-debugging-images/mpd12-xs.png "Selezione del progetto da avviare")

Verrà visualizzata la configurazione della soluzione **MultipleProjects** nella **barra degli strumenti di debug**, consentendo agli sviluppatori di eseguire contemporaneamente il debug dei due progetti.

## <a name="summary"></a>Riepilogo

In questa guida è stato descritto il debug di più processi in Visual Studio per Mac. Sono state trattate alcune delle modifiche all'IDE destinate a supportare il debug multiprocesso e sono stati descritti alcuni comportamenti associati.

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin Cycle 9 Release Notes](https://releases.xamarin.com/stable-release-cycle-9/) (Note sulla versione di Xamarin Cycle 9)
