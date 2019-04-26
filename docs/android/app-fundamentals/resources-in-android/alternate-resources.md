---
title: Risorse alternative
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: e71e79b58d912ecb697576e92ae921a848f24f4c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013281"
---
# <a name="alternate-resources"></a>Risorse alternative

Risorse alternative sono le risorse destinate a una configurazione di runtime, ad esempio la lingua corrente, dimensioni dello schermo particolare o densità in pixel o un dispositivo specifico. Se Android può corrispondere a una risorsa che è più specifica per un determinato dispositivo o la configurazione della risorsa predefinita, verrà invece usata tale risorsa. Se non trova un'altra risorsa che corrisponde alla configurazione corrente, quindi le risorse predefinite verranno caricate. Come Android decide quali risorse verranno utilizzate da un'applicazione verrà trattata più dettagliatamente di seguito, nella sezione di percorso della risorsa

Risorse alternative sono organizzate come una sottodirectory all'interno della cartella di risorse in base al tipo di risorsa, proprio come le risorse predefinite. Il nome della sottodirectory risorsa alternativo è nel formato: _ResourceType_-_Qualifier_

*Qualificatore* è un nome che identifica una configurazione del dispositivo specifico.
Potrebbero esserci più di un qualificatore in un nome, ognuno di essi separati da un trattino. Ad esempio, la schermata seguente mostra un semplice progetto che dispone di risorse alternative per le varie configurazioni, ad esempio le impostazioni locali, densità dello schermo, dimensioni dello schermo e l'orientamento:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Risorse alternative](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Risorse alternative](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

Le regole seguenti si applicano quando si aggiungono i qualificatori a un tipo di risorsa:

1. Potrebbero essere presenti più di un qualificatore, con ogni qualificatore separati da un trattino.

2. I qualificatori forse specificati una sola volta.

3. Qualificatori devono essere in ordine che vengono visualizzati nella tabella seguente.

I qualificatori possibili sono elencati di seguito per riferimento:

- **MCC e MNC** &ndash; le [codice paese mobile](https://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) e, facoltativamente, il [codice rete mobile](https://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). La scheda SIM fornirà MCC, mentre il dispositivo è connesso alla rete fornirà le MNC. Sebbene sia possibile alle impostazioni locali di destinazione usando il codice paese mobile, l'approccio consigliato consiste nell'usare il qualificatore della lingua specificato di seguito. Ad esempio, per le risorse di destinazione per la Germania, il qualificatore sarebbe `mcc262`. Per le risorse di destinazione per dispositivi mobili T negli Stati Uniti, il qualificatore è `mcc310-mnc026`.
  Per un elenco completo dei codici di rete per dispositivi mobili e i codici paese per dispositivi mobili vedere <http://mcc-mnc.com/>.

- **Linguaggio** &ndash; di due lettere [codice ISO 639-1 language](https://en.wikipedia.org/wiki/ISO_639-1) e facoltativamente seguito da due lettere [paese ISO 3166-alpha 2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 
  Se vengono forniti entrambi qualificatori, quindi sono separati da un `-r`. Ad esempio, per le impostazioni locali di destinazione francese quindi il qualificatore di `fr` viene usato. Come destinazione le impostazioni locali del francese canadese li, il `fr-rCA` verrebbe utilizzato. Per un elenco completo dei codici di lingua e i codici di area, vedere [codici per la rappresentazione dei nomi delle lingue](http://www.loc.gov/standards/iso639-2/php/English_list.php) e [elementi di codice e i nomi di paese](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Larghezza minima** &ndash; specifica la larghezza minima dell'applicazione deve eseguire in. Illustrate in dettaglio nelle [creazione di risorse per schermi diversi](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponibile nel livello API 13 (Android 3.2) e versioni successive. Ad esempio, il qualificatore `sw320dp` viene usato per i dispositivi di destinazione la cui larghezza e l'altezza sia almeno 320dp.

- **Larghezza disponibile** &ndash; la larghezza minima dello schermo in formato w*N*dp, dove *N* è independent pixel di larghezza di densità considerevole.
  Questo valore può cambiare quando l'utente ruota il dispositivo. Illustrate in dettaglio nelle [creazione di risorse per schermi diversi](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponibile nel livello API 13 (Android 3.2) e versioni successive. Esempio: w720dp il qualificatore viene utilizzato come destinazione i dispositivi che hanno una larghezza pari a 720dp minimi.

- **Altezza disponibile** &ndash; altezza minima dello schermo in formato h*N*dp, dove *N* corrisponde all'altezza nel punto di distribuzione. Questo valore può cambiare quando l'utente ruota il dispositivo. Illustrate in dettaglio nelle [creazione di risorse per schermi diversi](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponibile nel livello API 13 (Android 3.2) e versioni successive. Ad esempio, h720dp il qualificatore viene utilizzato come destinazione i dispositivi che hanno un'altezza pari a 720dp minimi

- **Dimensioni dello schermo** &ndash; il qualificatore è una generalizzazione delle dimensioni dello schermo che queste risorse sono per. È incluso in modo più dettagliato nella [creazione di risorse per schermi diversi](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  I valori consentiti sono `small`, `normal`, `large` e `xlarge`. Aggiunto nel livello API 9 (2.3.1/Android/Android 2.3 Android 2.3.2)

- **Schermata di aspetto** &ndash; si basa sulle proporzioni, non dell'orientamento dello schermo. Una lunga schermata è più ampia. Aggiunta di API di livello 4 (Android 1.6). I valori possibili sono lunghe e con notlong.

- **Orientamento dello schermo** &ndash; verticale o orizzontale dell'orientamento dello schermo. Questo può cambiare nel corso della durata di un'applicazione.
  I valori possibili sono `port` e `land`.

- **Modalità di ancoraggio** &ndash; per ancorare i dispositivi in un'automobile o una scrivania di ancoraggio. Aggiunto livello API 8 (2.2.x Android). I valori possibili sono `car` e `desk`.

- **Modalità notturna** &ndash; o meno l'applicazione viene eseguita durante la notte o durante la giornata. Questo può cambiare nel corso della durata di un'applicazione e si è progettato per offrire agli sviluppatori la possibilità di usare le versioni più scure di un'interfaccia durante la notte. Aggiunto livello API 8 (2.2.x Android). I valori possibili sono `night` e `notnight`.

- **Schermata di densità di Pixel (dpi)** &ndash; il numero di pixel in una determinata area sullo schermo fisico. In genere espresso come punti per pollice (dpi). I possibili valori sono:

    - `ldpi` &ndash; Schermi a densità bassa.

    - `mdpi` &ndash; Schermi a densità Media

    - `hdpi` &ndash; Schermi ad alta densità

    - `xhdpi` &ndash; Schermi a densità molto elevata

    - `nodpi` &ndash; Risorse che non devono essere ridimensionata

    - `tvdpi` &ndash; Introdotto nel livello API 13 (Android 3.2) per gli schermi hdpi quella mdpi.

- **Tipo di touchscreen** &ndash; specifica il tipo di touchscreen può avere un dispositivo. I valori possibili sono `notouch` (nessun touchscreen) `stylus` (resistenti touchscreen adatto per uno stilo), e `finger` (un touchscreen).

- **Tasti di disponibilità** &ndash; specifica quale tipo di tastiera è disponibile. Ciò potrebbe cambiare nel corso della durata di un'applicazione &ndash; , ad esempio quando un utente apre una tastiera hardware. I possibili valori sono:

    - `keysexposed` &ndash; Il dispositivo ha una tastiera disponibile. Se è presente alcuna tastiera software abilitato, quindi si usa solo quando viene aperta la tastiera hardware.

    - `keyshidden` &ndash; Il dispositivo dispone di una tastiera hardware, ma è nascosta e non tastiera software è abilitato.

    - `keyssoft` &ndash; il dispositivo ha una tastiera software abilitata.

- **Metodo di Input di testo principale** &ndash; consente di specificare quali tipi di chiavi di hardware sono disponibili per l'input. I possibili valori sono:

    - `nokeys` &ndash; Non sono presenti chiavi hardware per l'input.

    - `qwerty` &ndash; È disponibile una tastiera qwerty.

    - `12key` &ndash; È presente una tastiera hardware 12-key


- **Disponibilità di chiave di navigazione** &ndash; quando vie 5 o d-riquadro navigazione (direzionale aggiungerebbero) è disponibile. Questo può cambiare nel corso della durata dell'applicazione. I possibili valori sono:

    - `navexposed` &ndash; le chiavi per la navigazione sono disponibili all'utente

    - `navhidden` &ndash; le chiavi per la navigazione non sono disponibili.

-  **Metodo di navigazione principale Non Touch** &ndash; il tipo di navigazione disponibile nel dispositivo. I possibili valori sono:

    - `nonav` &ndash; la funzionalità di navigazione solo disponibile sono il touchscreen

    - `dpad` &ndash; un riquadro d (direzionale-riquadro) è disponibile per la navigazione

    - `trackball` &ndash; il dispositivo ha una trackball per la navigazione

    - `wheel` &ndash; lo scenario comune in cui sono presenti uno o più direzionale ruote disponibili

-  **Versione della piattaforma (livello API)** &ndash; a livello di API supportato dal dispositivo nel formato v*N*, dove *N* è il livello API di destinazione. Ad esempio, sarà destinato a un livello di API 11 (Android 3.0) v11 dispositivo.


Per informazioni più complete sulle risorse vedere qualificatori [fornendo risorse](https://developer.android.com/guide/topics/resources/providing-resources.html) sul sito Web gli sviluppatori per Android.


## <a name="how-android-determines-what-resources-to-use"></a>Come Android determina le risorse da usare

È molto possibili ed è probabile che un'applicazione Android che contiene molte risorse. È importante comprendere come Android selezionerà le risorse per un'applicazione quando è in esecuzione in un dispositivo.

Android determina le risorse di base mediante l'iterazione di test seguente di regole:

- **Eliminare i qualificatori contraddittori** &ndash; , ad esempio, se l'orientamento del dispositivo è verticale, quindi tutte le directory di risorse panorama verranno rifiutate.

- **Ignora i qualificatori non supportati** &ndash; non tutti i qualificatori sono disponibili per tutti i livelli di API. Se una directory delle risorse contiene un qualificatore che non è supportato dal dispositivo, quindi tale directory delle risorse verrà ignorato.

- **Identificare il qualificatore di priorità più elevato successivo** &ndash; che fa riferimento a tabella precedente selezionare il qualificatore di priorità più elevato successivo (dall'alto verso il basso).

- **Mantenere tutte le directory delle risorse per il qualificatore** &ndash; se sono presenti tutte le directory di risorse che soddisfano il qualificatore per la tabella precedente selezionare il qualificatore di priorità più elevato successivo (dall'alto verso il basso).

Queste regole sono anche illustrate nel diagramma di flusso seguente:

[![Diagramma di flusso di risorse](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Quando il sistema esegue la ricerca di risorse specifiche della densità e non è possibile trovarli, tenterà di individuare altre risorse specifiche di densità e ridimensionarli. Android non necessariamente deve contenere le risorse predefinite.
Ad esempio, quando si cerca una risorsa a bassa densità e non è disponibile, Android è possibile selezionare versione ad alta densità della risorsa sulle risorse di valore predefinito o la densità Media. Ciò avviene perché la risorsa ad alta densità scalabilità verso il basso in un fattore pari a 0,5, provocando problemi di visibilità un numero minore rispetto alla scalabilità verso il basso di una risorsa di densità media che richiederebbe un fattore 0,75.

Ad esempio, si consideri un'applicazione con la directory di risorse drawable seguenti:

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

E a questo punto l'applicazione viene eseguita in un dispositivo con la configurazione seguente:

- **Impostazioni internazionali** &ndash; en-GB
- **Orientamento** &ndash; porta
- **Schermata di densità** &ndash; hdpi
- **Tipo di touchscreen** &ndash; notouch
- **Metodo di input primario** &ndash; 12key

Per iniziare, le risorse francesi sono state eliminate perché sono in conflitto con le impostazioni locali di `en-GB`, lasciando a noi con:

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

Successivamente, il qualificatore del primo viene selezionato dalla tabella qualificatori precedente: MCC e MNC. Non esistono directory risorse che contengono questo qualificatore in modo che il codice MCC/MNC viene ignorato.

Viene selezionato il qualificatore successivo, ovvero lingua. Sono disponibili le risorse che corrispondono al codice di linguaggio. Tutte le directory di risorse che non corrispondono il codice della lingua `en` vengono rifiutati, in modo che l'elenco delle risorse è ora:

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

Il qualificatore successivo che è presente è per l'orientamento dello schermo, in modo che tutte le directory delle risorse che non corrispondono dell'orientamento dello schermo di `port` sono stati eliminati:

    drawable-en-port
    drawable-en-port-ldpi

Di seguito è il qualificatore per densità dello schermo, `ldpi`, che comporta l'esclusione di una directory delle altre risorse:

    drawable-en-port-ldpi

In seguito a questo processo, Android userà le risorse drawable nella directory delle risorse `drawable-en-port-ldpi` per il dispositivo.

> [!NOTE]
> I qualificatori di dimensioni dello schermo forniscono un'unica eccezione a questo processo di selezione. È possibile per Android selezionare le risorse che sono progettate per uno schermo piccolo rispetto a quale dispositivo corrente fornisce. Ad esempio, un dispositivo di schermi di grandi dimensioni possa usare le risorse forniscono per uno schermo con dimensione normali. Tuttavia non è vero il contrario di questo oggetto: lo stesso dispositivo di schermi di grandi dimensioni non userà le risorse fornite per una schermata: molto grandi. Se Android non è possibile trovare un set di risorse che corrisponde a una dimensione dello schermo specificato, l'applicazione si arresterà.
