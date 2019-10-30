---
title: Risorse alternative
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 644262310614874794810fd083ba1823abfd0da2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025409"
---
# <a name="alternate-resources"></a>Risorse alternative

Le risorse alternative sono le risorse destinate a una configurazione specifica del dispositivo o della fase di esecuzione, ad esempio la lingua corrente, le dimensioni dello schermo o la densità dei pixel. Se Android può corrispondere a una risorsa più specifica per un dispositivo o una configurazione particolare rispetto alla risorsa predefinita, verrà usata tale risorsa. Se non trova una risorsa alternativa corrispondente alla configurazione corrente, verranno caricate le risorse predefinite. Il modo in cui Android decide quali risorse verranno usate da un'applicazione verrà analizzato più dettagliatamente nella sezione posizione della risorsa

Le risorse alternative sono organizzate come una sottodirectory all'interno della cartella Resources in base al tipo di risorsa, proprio come le risorse predefinite. Il nome della sottodirectory di risorsa alternativa è nel formato: _ResourceType_-_qualificatore_

*Qualifier* è un nome che identifica una configurazione specifica del dispositivo.
In un nome possono essere presenti più qualificatori, ognuno dei quali è separato da un trattino. Ad esempio, lo screenshot seguente mostra un progetto semplice con risorse alternative per diverse configurazioni, ad esempio impostazioni locali, densità dello schermo, dimensioni dello schermo e orientamento:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Risorse alternative](alternate-resources-images/alternate-resources-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Risorse alternative](alternate-resources-images/alternate-resources-xs.png)

-----

Quando si aggiungono i qualificatori a un tipo di risorsa, si applicano le regole seguenti:

1. È possibile che siano presenti più qualificatori, con ogni qualificatore separato da un trattino.

2. I qualificatori potrebbero essere specificati una sola volta.

3. I qualificatori devono essere nell'ordine in cui sono visualizzati nella tabella seguente.

I qualificatori possibili sono elencati di seguito per riferimento:

- **MCC e** multiparte &ndash; il [codice paese mobile](https://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) e, facoltativamente, il [codice di rete mobile](https://en.wikipedia.org/wiki/Mobile_Network_Code) (multiparte). La scheda SIM fornirà il MCC, mentre la rete a cui è connesso il dispositivo fornirà il multiparte. Sebbene sia possibile utilizzare le impostazioni locali utilizzando il codice paese mobile, l'approccio consigliato consiste nell'utilizzare il qualificatore di linguaggio specificato di seguito. Ad esempio, per fare riferimento alle risorse in Germania, il qualificatore verrebbe `mcc262`. Per le risorse di destinazione per T-Mobile negli Stati Uniti, il qualificatore è `mcc310-mnc026`.
  Per un elenco completo dei codici paese mobile e dei codici di rete mobile, vedere <http://mcc-mnc.com/>.

- **Lingua** &ndash; il [codice lingua ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1) di due lettere e, facoltativamente, seguito dal codice dell' [area ISO-3166-alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)di due lettere. 
  Se vengono specificati entrambi i qualificatori, sono separati da un `-r`. Ad esempio, per fare riferimento alle impostazioni locali di lingua francese, viene usato il qualificatore di `fr`. Per fare riferimento alle impostazioni locali in Francese-Canada, viene usata la `fr-rCA`. Per un elenco completo dei codici di lingua e dell'area, vedere [codici per la rappresentazione dei nomi di lingue](https://www.loc.gov/standards/iso639-2/php/English_list.php) e [nomi di paese ed elementi di codice](https://www.iso.org/iso-3166-country-codes.html).

- **Larghezza minima** &ndash; specifica la larghezza minima dello schermo su cui deve essere eseguita l'applicazione. Analizzato in modo più dettagliato nella [creazione di risorse per diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponibile in API Level 13 (Android 3,2) e versioni successive. Ad esempio, il qualificatore `sw320dp` viene usato per i dispositivi di destinazione con altezza e larghezza pari almeno a 320dp.

- **Larghezza disponibile** &ndash; larghezza minima dello schermo nel formato w*n*DP, dove *N* è la larghezza in pixel a densità indipendente.
  Questo valore può cambiare quando l'utente ruota il dispositivo. Analizzato in modo più dettagliato nella [creazione di risorse per diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponibile in API Level 13 (Android 3,2) e versioni successive. Esempio: il qualificatore w720dp viene usato per i dispositivi di destinazione con una larghezza minore di 720dp.

- **Altezza disponibile** &ndash; l'altezza minima dello schermo nel formato h*N*DP, dove *N* è l'altezza in DP. Questo valore può cambiare quando l'utente ruota il dispositivo. Analizzato in modo più dettagliato nella [creazione di risorse per diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponibile in API Level 13 (Android 3,2) e versioni successive. Ad esempio, il qualificatore h720dp viene usato per i dispositivi di destinazione con un'altezza minore di 720dp

- **Dimensioni dello schermo** &ndash; questo qualificatore è una generalizzazione delle dimensioni dello schermo per le quali sono disponibili queste risorse. Viene analizzato in modo più dettagliato nella [creazione di risorse per diverse schermate](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  I valori consentiti sono `small`, `normal`, `large` e `xlarge`. Aggiunto in API Level 9 (Android 2.3/Android 2.3.1/Android 2.3.2)

- L' **aspetto dello schermo** &ndash; si basa sulle proporzioni, non sull'orientamento dello schermo. Uno schermo lungo è più ampio. Aggiunto in API Level 4 (Android 1,6). I valori possibili sono Long e notlong.

- Orientamento **dello schermo** &ndash; orientamento verticale o orizzontale. Questo può cambiare nel corso della durata di un'applicazione.
  I valori possibili sono `port` e `land`.

- La **modalità di ancoraggio** &ndash; per i dispositivi in un'auto Dock o in una scrivania. Aggiunto in API level 8 (Android 2.2. x). I valori possibili sono `car` e `desk`.

- La **modalità notte** &ndash; se l'applicazione è in esecuzione a notte o nel giorno. Questo può cambiare durante il ciclo di vita di un'applicazione e ha lo scopo di offrire agli sviluppatori la possibilità di usare versioni più scure di un'interfaccia di notte. Aggiunto in API level 8 (Android 2.2. x). I valori possibili sono `night` e `notnight`.

- La **densità dei pixel dello schermo (dpi)** &ndash; il numero di pixel in una determinata area della schermata fisica. Generalmente espresso come dpi (punti per pollice). I possibili valori sono:

  - `ldpi` &ndash; schermate a densità ridotta.

  - `mdpi` &ndash; schermate a densità media

  - `hdpi` &ndash; schermate ad alta densità

  - `xhdpi` &ndash; schermate ad alta densità

  - `nodpi` &ndash; risorse che non devono essere ridimensionate

  - `tvdpi` &ndash; introdotta in API Level 13 (Android 3,2) per le schermate tra MDPI e HDPI.

- **Tipo di touchscreen** &ndash; specifica il tipo di touchscreen che può avere un dispositivo. I valori possibili sono `notouch` (nessuno schermo touchscreen), `stylus` (un touchscreen resistivo adatto per uno stilo) e `finger` (un touchscreen).

- &ndash; di **disponibilità della tastiera** specifica il tipo di tastiera disponibile. Questo può cambiare durante il ciclo di vita di un'applicazione &ndash; ad esempio quando un utente apre una tastiera hardware. I possibili valori sono:

  - `keysexposed` &ndash; sul dispositivo è disponibile una tastiera. Se la tastiera software non è abilitata, viene usata solo quando viene aperta la tastiera hardware.

  - `keyshidden` &ndash; il dispositivo dispone di una tastiera hardware ma è nascosta e non è abilitata alcuna tastiera software.

  - `keyssoft` &ndash; il dispositivo ha una tastiera software abilitata.

- **Metodo di input di testo primario** &ndash; usare per specificare quali tipi di chiavi hardware sono disponibili per l'input. I possibili valori sono:

  - `nokeys` &ndash; non sono presenti chiavi hardware per l'input.

  - `qwerty` &ndash; è disponibile una tastiera QWERTY.

  - `12key` &ndash; è presente una tastiera hardware a 12 chiavi

- &ndash; della **disponibilità della chiave di navigazione** per il momento in cui è disponibile la navigazione a 5 vie o d-pad (direzionale-pad). Questo può cambiare durante il ciclo di vita dell'applicazione. I possibili valori sono:

  - `navexposed` &ndash; le chiavi di spostamento sono disponibili per l'utente

  - `navhidden` &ndash; le chiavi di spostamento non sono disponibili.

- Il **metodo di navigazione non touch principale** &ndash; il tipo di navigazione disponibile sul dispositivo. I possibili valori sono:

  - `nonav` &ndash; l'unica funzionalità di spostamento disponibile è il touchscreen

  - `dpad` &ndash; un d-pad (direzionale-pad) è disponibile per la navigazione

  - `trackball` &ndash; il dispositivo ha una trackball per la navigazione

  - `wheel` &ndash; scenario non comune in cui sono disponibili una o più ruote direzionali

- **Versione della piattaforma (livello API)** &ndash; il livello API supportato dal dispositivo nel formato v*n*, dove *N* è il livello API di destinazione. Ad esempio, V11 è destinato a un dispositivo API livello 11 (Android 3,0).

Per informazioni più complete sui qualificatori di risorse, vedere la pagina relativa alla [fornitura di risorse](https://developer.android.com/guide/topics/resources/providing-resources.html) nel sito Web per sviluppatori Android.

## <a name="how-android-determines-what-resources-to-use"></a>In che modo Android determina quali risorse usare

È molto probabile che un'applicazione Android conterrà molte risorse. È importante comprendere in che modo Android seleziona le risorse per un'applicazione quando viene eseguita in un dispositivo.

Android determina la base delle risorse scorrendo il test di regole seguente:

- **Eliminare i qualificatori contraddittori** &ndash; ad esempio, se l'orientamento del dispositivo è verticale, tutte le directory di risorse Landscape verranno rifiutate.

- **Ignora qualificatori non supportati** &ndash; non tutti i qualificatori sono disponibili per tutti i livelli API. Se una directory di risorse contiene un qualificatore che non è supportato dal dispositivo, la directory delle risorse verrà ignorata.

- **Identificare il qualificatore successivo con la priorità più alta** &ndash; facendo riferimento alla tabella precedente, selezionare il qualificatore successivo con la priorità più alta (dall'alto verso il basso).

- **Mantieni tutte le directory di risorse per il qualificatore** &ndash; se sono presenti directory di risorse che corrispondono al qualificatore alla tabella precedente, selezionare il qualificatore successivo con la priorità più alta (dall'alto verso il basso).

Queste regole sono inoltre illustrate nel diagramma di flusso seguente:

[diagramma di flusso risorse![](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Quando il sistema cerca risorse specifiche della densità e non le trova, tenterà di individuare altre risorse specifiche della densità e di ridimensionarle. Android potrebbe non usare necessariamente le risorse predefinite.
Ad esempio, durante la ricerca di una risorsa a bassa densità e non è disponibile, Android può selezionare una versione ad alta densità della risorsa sulle risorse predefinite o di media densità. Questa operazione viene eseguita perché la risorsa a densità elevata può essere ridimensionata in base a un fattore di 0,5, il che comporta un minor numero di problemi di visibilità rispetto alla scalabilità verticale di una risorsa di media densità che richiederebbe un fattore di 0,75.

Si consideri ad esempio un'applicazione con le seguenti directory di risorse che è possibile creare:

```
drawable
drawable-en
drawable-fr-rCA
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

Ora l'applicazione viene eseguita in un dispositivo con la seguente configurazione:

- **Impostazioni locali** &ndash; en-GB
- **Orientamento** &ndash; porta
- **Densità dello schermo** &ndash; HDPI
- **Tipo di Touchscreen** &ndash; NoTouch
- **Metodo di input primario** &ndash; 12key

Per iniziare, le risorse francesi vengono eliminate quando si verificano conflitti con le impostazioni locali di `en-GB`, lasciandoci:

```
drawable
drawable-en
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

Successivamente, viene selezionato il primo qualificatore dalla tabella qualificatori precedente: MCC e multiparte. Non sono presenti directory di risorse che contengono questo qualificatore, quindi il codice MCC/multiparte viene ignorato.

Il qualificatore successivo è selezionato, ovvero Language. Sono presenti risorse che corrispondono al codice della lingua. Tutte le directory di risorse che non corrispondono al codice della lingua di `en` vengono rifiutate, in modo che l'elenco di risorse sia ora:

```
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
```

Il qualificatore successivo presente è per l'orientamento dello schermo, quindi vengono eliminate tutte le directory di risorse che non corrispondono all'orientamento dello schermo di `port`:

```
drawable-en-port
drawable-en-port-ldpi
```

Quindi, il qualificatore per la densità dello schermo, `ldpi`, che comporta l'esclusione di un'altra directory di risorse:

```
drawable-en-port-ldpi
```

In seguito a questo processo, Android userà le risorse che è possibile creare nella directory delle risorse `drawable-en-port-ldpi` per il dispositivo.

> [!NOTE]
> I qualificatori delle dimensioni dello schermo forniscono un'eccezione a questo processo di selezione. È possibile che Android selezioni le risorse progettate per uno schermo più piccolo rispetto a quello fornito dal dispositivo corrente. Ad esempio, un dispositivo a schermo grande può usare le risorse disponibili per una schermata di dimensioni normali. Tuttavia, il contrario non è vero: lo stesso dispositivo a schermo intero non userà le risorse fornite per una schermata XLarge. Se Android non riesce a trovare un set di risorse corrispondente a una determinata dimensione dello schermo, l'applicazione si arresterà in modo anomalo.
