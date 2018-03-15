---
title: Risorse alternative
ms.topic: article
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 230c26c2537987f7ca09e28e05ec96186e5f7882
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="alternate-resources"></a>Risorse alternative

Le risorse alternative sono le risorse destinate a una configurazione di runtime, ad esempio la lingua corrente, le dimensioni dello schermo particolare o densità in pixel o un dispositivo specifico. Se Android può corrispondere a una risorsa che è più specifica per un particolare dispositivo o di una configurazione della risorsa predefinita, verrà invece utilizzata tale risorsa. Se non trova un'altra risorsa che corrisponde alla configurazione corrente, le risorse predefinite verranno caricate. Come Android decide quali risorse verranno utilizzate da un'applicazione verrà trattata in modo dettagliato più avanti, nella sezione di percorso della risorsa

Le risorse alternative sono organizzate come una sottodirectory all'interno della cartella di risorse in base al tipo di risorsa, analogamente alle risorse predefinite. Il nome della sottodirectory risorsa alternativo è nel formato: _ResourceType_-_qualificatore_

*Qualificatore* è un nome che identifica una configurazione di dispositivo specifico.
In un nome, ognuno di essi separati da un trattino possono essere presenti più di un qualificatore. Ad esempio, la schermata riportata di seguito viene illustrato un semplice progetto include risorse alternative per le varie configurazioni, ad esempio delle impostazioni locali, densità, le dimensioni dello schermo e l'orientamento:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Risorse alternative](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Risorse alternative](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

Le regole seguenti si applicano quando si aggiungono i qualificatori a un tipo di risorsa:

1. Potrebbero essere presenti più di un qualificatore, con ogni qualificatore separati da un trattino.

2. I qualificatori può essere specificati una sola volta.

3. Qualificatori devono essere nell'ordine che vengono visualizzati nella tabella seguente.

I qualificatori possibili sono elencati di seguito per riferimento:

- **MCC e MNC** &ndash; il [codice paese mobili](http://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) e, facoltativamente, il [codice rete mobile](http://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). La scheda SIM fornirà MCC, mentre il dispositivo è connesso alla rete fornirà il MNC. Sebbene sia possibile alle impostazioni locali di destinazione utilizzando il codice paese per dispositivi mobili, l'approccio consigliato consiste nell'utilizzare il qualificatore della lingua indicato di seguito. Ad esempio, per le risorse di destinazione per la Germania, il qualificatore sarebbe `mcc262`. Le risorse di destinazione per T-Mobile negli Stati Uniti, il qualificatore è `mcc310-mnc026`.
  Per un elenco completo dei codici mobili paese e rete mobile vedere <http://mcc-mnc.com/>.

- **Lingua** &ndash; due lettere [codice ISO 639-1 language di](http://en.wikipedia.org/wiki/ISO_639-1) e, facoltativamente, seguito da due lettere [indicativo di paese ISO 3166-alpha 2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 
  Se entrambi i qualificatori vengono forniti e sono separati da un `-r`. Ad esempio, per le impostazioni locali di destinazione francese quindi il qualificatore di `fr` viene utilizzato. Applicare le impostazioni locali del francese canadese li, il `fr-rCA` verrà utilizzato. Per un elenco completo dei codici di area e di codici di lingua, vedere [codici per la rappresentazione di nomi di lingue](http://www.loc.gov/standards/iso639-2/php/English_list.php) e [i nomi di paese e gli elementi di codice](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Larghezza minima** &ndash; specifica la larghezza dello schermo più piccolo, l'applicazione deve eseguire in. Analizzati più dettagliatamente in [la creazione di risorse per le diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponibili nel livello API 13 (Android 3.2) e versioni successive. Ad esempio, il qualificatore `sw320dp` viene utilizzato per i dispositivi di destinazione i cui altezza e larghezza è maggiore o uguale 320dp.

- **Larghezza disponibile** &ndash; la larghezza minima dello schermo in formato w*N*punto di distribuzione, in cui *N* è la larghezza di densità di pixel indipendenti.
  Questo valore può cambiare quando l'utente ruota il dispositivo. Analizzati più dettagliatamente in [la creazione di risorse per le diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  È disponibile in a in livello API 13 (Android 3.2) e versioni successive. Esempio: w720dp il qualificatore viene utilizzato per fare riferimento ai dispositivi che hanno una larghezza di 720dp minimi.

- **Altezza disponibile** &ndash; l'altezza minima dello schermo in formato h*N*punto di distribuzione, in cui *N* è l'altezza nel punto di distribuzione. Questo valore può cambiare quando l'utente ruota il dispositivo. Analizzati più dettagliatamente in [la creazione di risorse per le diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  È disponibile in a in livello API 13 (Android 3.2) e versioni successive. Ad esempio, h720dp il qualificatore viene utilizzato per fare riferimento ai dispositivi che hanno un'altezza pari almeno 720dp

- **La dimensione dello schermo** &ndash; il qualificatore è una generalizzazione di queste risorse sono per le dimensioni dello schermo. È incluso in modo più dettagliato in [la creazione di risorse per le diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  I valori consentiti sono `small`, `normal`, `large` e `xlarge`. Aggiunto nel livello dell'API di 9 (2.3.1/Android/Android 2.3 Android 2.3.2)

- **Schermata di aspetto** &ndash; si basa sulle proporzioni, non dell'orientamento dello schermo. Una schermata lungo è più ampia. Aggiungere il livello di API 4 (Android 1.6). I possibili valori sono lunghi e notlong.

- **Orientamento dello schermo** &ndash; orientamento verticale o orizzontale. Questo può modificare la durata di un'applicazione.
  I valori possibili sono `port` e `land`.

- **Ancorare modalità** &ndash; per ancorare i dispositivi in una macchina o una scrivania ancorato. Aggiungere il livello di API 8 (Android 2.2). I valori possibili sono `car` e `desk`.

- **Modalità di notte** &ndash; o meno l'esecuzione dell'applicazione durante la notte o del giorno. Questo può cambiare nel corso della durata di un'applicazione ed è progettato per fornire agli sviluppatori la possibilità di utilizzare più scure versioni di un'interfaccia durante la notte. Aggiungere il livello di API 8 (Android 2.2). I valori possibili sono `night` e `notnight`.

- **Schermata di densità di Pixel (dpi)** &ndash; il numero di pixel in una determinata area dello schermo fisico. In genere espresso come punti per pollice (dpi). I possibili valori sono:

    - `ldpi` &ndash; Schermate di bassa densità.

    - `mdpi` &ndash; Schermate di densità Media

    - `hdpi` &ndash; Schermi ad alta densità

    - `xhdpi` &ndash; Schermate molto alta densità

    - `nodpi` &ndash; Risorse che non devono essere scalata

    - `tvdpi` &ndash; Introdotto in livello API 13 (Android 3.2) per le schermate tra mdpi e hdpi.

- **Tipo touchscreen** &ndash; specifica il tipo di touchscreen potrebbe essere un dispositivo. I valori possibili sono `notouch` (nessun touchscreen) `stylus` (resistenti touchscreen adatto per uno stilo), e `finger` (touchscreen).

- **Disponibilità della tastiera** &ndash; specifica il tipo della tastiera è disponibile. Questi valori possono variare nel corso della durata di un'applicazione &ndash; ad esempio quando un utente apre una tastiera hardware. I possibili valori sono:

    - `keysexposed` &ndash; Il dispositivo dispone di una tastiera disponibile. Se non è presente alcuna tastiera software abilitato, quindi questo viene utilizzato solo quando viene aperta la tastiera hardware.

    - `keyshidden` &ndash; Il dispositivo dispone di una tastiera hardware, ma è nascosta e non tastiera software è abilitata.

    - `keyssoft` &ndash; il dispositivo dispone di una tastiera software abilitata.

- **Metodo di Input di testo primario** &ndash; consente di specificare quali tipi di chiavi hardware sono disponibili per l'input. I possibili valori sono:

    - `nokeys` &ndash; Non sono presenti chiavi hardware per l'input.

    - `qwerty` &ndash; Una tastiera qwerty è disponibile.

    - `12key` &ndash; È una tastiera hardware 12 chiave


- **Disponibilità di chiave di navigazione** &ndash; quando 5 vie o d-pad spostamento (riquadro direzionale) è disponibile. Questo può modificare la durata dell'applicazione. I possibili valori sono:

    - `navexposed` &ndash; le chiavi per la navigazione sono disponibili all'utente

    - `navhidden` &ndash; le chiavi per la navigazione non sono disponibili.

-  **Metodo di navigazione principale Non tocco** &ndash; il tipo di navigazione disponibile nel dispositivo. I possibili valori sono:

    - `nonav` &ndash; la funzionalità di navigazione solo disponibile sono il touchscreen

    - `dpad` &ndash; d-pad (direzionale pad) è disponibile per la navigazione

    - `trackball` &ndash; il dispositivo presenta un trackball per la navigazione

    - `wheel` &ndash; lo scenario comune in cui sono presenti uno o più direzionale ruote disponibili

-  **Versione della piattaforma (livello API)** &ndash; livello l'API supportata dal dispositivo nel formato v*N*, dove *N* è il livello di API a cui si fa riferimento. Ad esempio, saranno destinati a un livello di API 11 (Android 3.0) v11 dispositivo.


Per ulteriori informazioni sulle risorse vedere qualificatori [fornendo risorse](http://developer.android.com/guide/topics/resources/providing-resources.html) sul sito Web di sviluppatori Android.


## <a name="how-android-determines-what-resources-to-use"></a>Come Android determina le risorse da utilizzare

È possibile molto probabile che un'applicazione Android conterrà molte risorse. È importante comprendere in che modo Android seleziona le risorse per un'applicazione quando viene eseguito in un dispositivo.

Android determina le risorse di base eseguendo l'iterazione tra il test delle regole seguente:

- **Eliminare i qualificatori contraddittori** &ndash; , ad esempio, se l'orientamento del dispositivo è verticale, quindi tutte le directory delle risorse orizzontale verranno rifiutate.

- **Ignora i qualificatori non supportati** &ndash; non tutti i qualificatori disponibili a tutti i livelli di API. Se una directory delle risorse contiene un qualificatore che non è supportato dal dispositivo, la directory delle risorse verrà ignorato.

- **Identificare il qualificatore di priorità successivo più alto** &ndash; che fa riferimento alla tabella precedente selezionare il qualificatore di priorità più elevato successivo (dall'alto verso il basso).

- **Mantenere tutte le directory delle risorse per il qualificatore** &ndash; se sono presenti tutte le directory di risorse che soddisfano il qualificatore per la tabella precedente, selezionare il qualificatore di priorità più elevato successivo (dall'alto verso il basso).

Queste regole sono inoltre illustrate nel diagramma di flusso seguente:

[![Diagramma di flusso di risorse](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Quando il sistema esegue la ricerca di risorse specifiche della densità e non è possibile trovarli, tenterà di individuare altre risorse specifiche di densità e scalabilità li. Android non necessariamente deve contenere le risorse predefinite.
Ad esempio, quando si cerca una risorsa bassa densità e non è disponibile, Android possono selezionare versione ad alta densità della risorsa le risorse predefinite o Media densità. Ciò avviene perché la risorsa ad alta densità possibile scalabilità verso il basso di un fattore pari a 0,5, che produrrà meno problemi di visibilità di ridimensionamento verso il basso di una risorsa di densità di supporto che richiederebbe un fattore 0,75.

Ad esempio, si consideri un'applicazione che ha le seguenti directory drawable risorse:

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

E ora l'applicazione viene eseguita in un dispositivo con la seguente configurazione:

- **Impostazioni locali** &ndash; en-GB
- **Orientamento** &ndash; porta
- **Schermata di densità** &ndash; hdpi
- **Tipo touchscreen** &ndash; notouch
- **Metodo di input primaria** &ndash; 12key

Per iniziare, le risorse francesi vengono eliminate perché sono in conflitto con le impostazioni locali di `en-GB`, siamo con:

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

Successivamente, il qualificatore del primo viene selezionato dalla tabella qualificatori: MCC e MNC. Non esistono directory risorse che contengono il qualificatore, pertanto il codice MCC/MNC viene ignorato.

Il qualificatore successivo è selezionato, ovvero lingua. Vi sono risorse che corrispondono al codice di lingua. Tutte le directory delle risorse che non corrispondono il codice della lingua `en` vengono rifiutati, in modo che l'elenco di risorse è ora:

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

Il qualificatore successivo che è presente è per orientamento dello schermo, in modo che tutte le directory delle risorse che non corrispondono dell'orientamento dello schermo di `port` vengono eliminati:

    drawable-en-port
    drawable-en-port-ldpi

Di seguito è riportato il qualificatore per la densità, `ldpi`, che comporta l'esclusione di una directory di altre risorse:

    drawable-en-port-ldpi

In seguito a questo processo, Android utilizzerà le risorse drawable nella directory delle risorse `drawable-en-port-ldpi` per il dispositivo.

> [!NOTE]
> I qualificatori di dimensioni dello schermo forniscono un'unica eccezione a questo processo di selezione. È possibile per Android selezionare le risorse che sono progettate per una schermata inferiori rispetto a sui quali il dispositivo corrente. Ad esempio, un dispositivo di schermi di grandi dimensioni può usare le risorse prevedere una schermata di dimensione normale. Tuttavia non è vero il contrario di questo: stesso dispositivo schermi di grandi dimensioni non utilizzerà le risorse fornite per una schermata xlarge. Se Android non è possibile trovare un set di risorse che corrisponde a una dimensione dello schermo specificate, verificherà un arresto anomalo dell'applicazione.
