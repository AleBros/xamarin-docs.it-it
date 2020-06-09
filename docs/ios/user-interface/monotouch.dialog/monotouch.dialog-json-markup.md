---
title: Markup JSON per MonoTouch.Dialog
description: Questo documento descrive la sintassi JSON che può essere usata per compilare un'interfaccia utente Novell. iOS usando MonoTouch. dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: fc6066155a4171b106e772c1fe6fe7ee3e5c67cf
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573508"
---
# <a name="monotouchdialog-json-markup"></a>Markup JSON per MonoTouch.Dialog

Questa pagina descrive il markup JSON accettato da MonoTouch. la finestra di dialogo [jsonelement](xref:MonoTouch.Dialog.JsonElement)

Iniziamo con un esempio. Di seguito è riportato un file JSON completo che può essere passato in Jsonelement.

```json
{     
    "title": "Json Sample",
    "sections": [ 
        {
            "header": "Booleans",
            "footer": "Slider or image-based",
            "id": "first-section",
            "elements": [
                { 
                    "type": "boolean",
                    "caption": "Demo of a Boolean",
                    "value": true
                }, {
                    "type": "boolean",
                    "caption": "Boolean using images",
                    "value": false,
                    "on": "favorite.png",
                    "off": "~/favorited.png"
                }, {
                    "type": "root",
                    "title": "Tap for nested controller",
                    "sections": [
                        {
                            "header": "Nested view!",
                            "elements": [
                                {
                                    "type": "boolean",
                                    "caption": "Just a boolean",
                                    "id": "the-boolean",
                                    "value": false
                                }, {
                                    "type": "string",
                                    "caption": "Welcome to the nested controller"
                                }
                            ]
                        }
                    ]
                }
            ]
        }, {
            "header": "Entries",
            "elements" : [
                {
                    "type": "entry",
                    "caption": "Username",
                    "value": "",
                    "placeholder": "Your account username"
                }
            ]
        }
    ]
}
```

Il markup precedente produce la seguente interfaccia utente:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "The UI created by the given markup")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Ogni elemento della struttura ad albero può contenere la proprietà `"id"` . È possibile che in fase di esecuzione faccia riferimento a sezioni o elementi singoli usando l'indicizzatore Jsonelement. nel modo seguente:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax"></a>

## <a name="root-element-syntax"></a>Sintassi degli elementi radice

L'elemento radice contiene i valori seguenti:

- `title`
- `sections` (facoltativo)

L'elemento radice può essere visualizzato all'interno di una sezione come elemento per creare un controller annidato. In tal caso, `"type"` è necessario impostare la proprietà extra su`"root"`

 <a name="url"></a>

### <a name="url"></a>url

Se la `"url"` proprietà è impostata, se l'utente tocca questo elemento RootElement, il codice richiederà un file dall'URL specificato e renderà disponibile il contenuto delle nuove informazioni. È possibile usarlo per creare un'estensione dell'interfaccia utente dal server in base a ciò che l'utente tocca.

 <a name="group"></a>

### <a name="group"></a>group

Se impostata, imposta il GroupName per l'elemento radice. I nomi dei gruppi vengono usati per selezionare un riepilogo visualizzato come valore dell'elemento radice da uno degli elementi annidati nell'elemento. Si tratta del valore di una casella di controllo o del valore di un pulsante di opzione.

 <a name="radioselected"></a>

### <a name="radioselected"></a>radioselezione

Identifica l'elemento radio selezionato negli elementi annidati

 <a name="title"></a>

### <a name="title"></a>title

Se presente, sarà il titolo usato per RootElement

 <a name="type"></a>

### <a name="type"></a>type

Deve essere impostato su `"root"` quando viene visualizzato in una sezione (utilizzata per annidare i controller).

 <a name="sections"></a>

### <a name="sections"></a>sezioni

Si tratta di una matrice JSON con singole sezioni

 <a name="Section_Syntax"></a>

## <a name="section-syntax"></a>Sintassi della sezione

La sezione contiene:

- `header` (facoltativo)
- `footer` (facoltativo)
- Matrice `elements`

 <a name="header"></a>

### <a name="header"></a>header

Se presente, il testo dell'intestazione viene visualizzato come didascalia della sezione.

 <a name="footer"></a>

### <a name="footer"></a>piè di pagina

Se presente, il piè di pagina viene visualizzato nella parte inferiore della sezione.

 <a name="elements"></a>

### <a name="elements"></a>Elementi figlio

Si tratta di una matrice di elementi. Ogni elemento deve contenere almeno una chiave, la `"type"` chiave utilizzata per identificare il tipo di elemento da creare.
Alcuni degli elementi condividono alcune proprietà comuni `"caption"` , ad esempio e `"value"` . Di seguito sono elencati gli elementi supportati:

- `string`elementi (sia con che senza stile)
- `entry`righe (Regular o password)
- `boolean`valori (mediante opzioni o immagini)

Gli elementi stringa possono essere usati come pulsanti fornendo un metodo da richiamare quando l'utente tocca la cella o l'accessorio.

 <a name="Rendering_Elements"></a>

## <a name="rendering-elements"></a>Elementi di rendering

Gli elementi di rendering sono basati su C# Stringelement e StyledStringElement e possono eseguire il rendering delle informazioni in diversi modi ed è possibile eseguirne il rendering in diversi modi. Gli elementi più semplici possono essere creati in questo modo:

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Verrà visualizzata una stringa semplice con tutte le impostazioni predefinite: tipo di carattere, sfondo, colore del testo e decorazioni. È possibile associare azioni a questi elementi e comportarsi come pulsanti impostando la `"ontap"` proprietà o le `"onaccessorytap"` proprietà:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

Sopra richiamerà il metodo "ShowPhotos" nella classe "Acme. fotolibrary". `"onaccessorytap"`È simile, ma verrà richiamato solo se l'utente tocca l'accessorio anziché toccare la cella. Per abilitare questa impostazione, è necessario impostare anche l'accessorio:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Gli elementi di rendering possono visualizzare due stringhe contemporaneamente, una è la didascalia e l'altra è il valore. Il rendering di queste stringhe dipende dallo stile, quindi è possibile impostarlo utilizzando la `"style"` Proprietà. Per impostazione predefinita viene visualizzata la didascalia a sinistra e il valore a destra. Per altri dettagli, vedere la sezione sullo stile. I colori vengono codificati usando il simbolo ' #' seguito da numeri esadecimali che rappresentano i valori per i valori rosso, verde, blu e forse alfa. Il contenuto può essere codificato in forma breve (3 o 4 cifre esadecimali) che rappresenta i valori RGB o RGBA. O il formato lungo (6 o 8 cifre) che rappresentano valori RGB o RGBA. La versione breve è una sintassi abbreviata per scrivere la stessa cifra esadecimale due volte. Quindi, la costante "#1bc" è intepreted come Red = 0x11, Green = 0xBB e Blue = 0xCC. Se il valore alfa non è presente, il colore è opaco. Di seguito alcuni esempi:

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory"></a>

### <a name="accessory"></a>accessorio

Determina il tipo di accessorio da visualizzare nell'elemento di rendering. i valori possibili sono:

- `checkmark`
- `detail-disclosure`
- `disclosure-indicator`

Se il valore non è presente, non viene visualizzato alcun accessorio

 <a name="background"></a>

### <a name="background"></a>background

La proprietà background imposta il colore di sfondo per la cella. Il valore è un URL di un'immagine (in questo caso, il downloader di immagini asincrone verrà richiamato e lo sfondo verrà aggiornato una volta scaricata l'immagine) oppure può essere un colore specificato utilizzando la sintassi del colore.

 <a name="caption"></a>

### <a name="caption"></a>caption

Stringa principale da visualizzare nell'elemento di rendering. Il tipo di carattere e il colore possono essere personalizzati impostando le `"textcolor"` `"font"` proprietà e. Lo stile di rendering è determinato dalla `"style"` Proprietà.

 <a name="color_and_detailcolor"></a>

### <a name="color-and-detailcolor"></a>Color e detailcolor

Il colore da utilizzare per il testo principale o il testo dettagliato.

 <a name="detailfont_and_font"></a>

### <a name="detailfont-and-font"></a>DetailFont e font

Tipo di carattere da utilizzare per la didascalia o il testo del dettaglio. Il formato di una specifica del tipo di carattere è il nome del tipo di carattere seguito facoltativamente da un trattino e dalle dimensioni del punto.
Di seguito sono indicate specifiche valide per i tipi di carattere:

- Helvetica
- "Helvetica-14"

 <a name="linebreak"></a>

### <a name="linebreak"></a>linebreak

Determina la modalità di interruzione delle righe. I valori possibili sono:

- `character-wrap`
- `clip`
- `head-truncation`
- `middle-truncation`
- `tail-truncation`
- `word-wrap`

Sia `character-wrap` che `word-wrap` possono essere utilizzati insieme alla `"lines"` proprietà impostata su zero per trasformare l'elemento di rendering in un elemento a più righe.

 <a name="ontap_and_onaccessorytap"></a>

### <a name="ontap-and-onaccessorytap"></a>ONTAP e onaccessorytap

Queste proprietà devono puntare a un nome di metodo statico nell'applicazione che accetta un oggetto come parametro. Quando si crea la gerarchia usando i metodi JsonDialog. FromFile o JsonDialog. FromJson, è possibile passare un valore di oggetto facoltativo. Il valore dell'oggetto viene quindi passato ai metodi. È possibile utilizzare questo oggetto per passare un contesto al metodo statico. Ad esempio:

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines"></a>

### <a name="lines"></a>lines

Se è impostato su zero, l'elemento verrà ridimensionato automaticamente in base al contenuto delle stringhe contenute. Per eseguire questa operazione, è necessario impostare anche la `"linebreak"` proprietà su `"character-wrap"` o `"word-wrap"` .

 <a name="style"></a>

### <a name="style"></a>style

Lo stile determina il tipo di stile di cella che verrà usato per eseguire il rendering del contenuto e che corrisponde ai valori di enumerazione UITableViewCellStyle.
I valori possibili sono:

- `"default"`
- `"value1"`
- `"value2"`
- `"subtitle"`: testo con un sottotitolo.

 <a name="subtitle"></a>

### <a name="subtitle"></a>subtitle

Valore da utilizzare per il sottotitolo. Si tratta di un collegamento per impostare lo stile su `"subtitle"` e impostare la `"value"` proprietà su una stringa.
Questa operazione viene eseguita con una singola voce.

 <a name="textcolor"></a>

### <a name="textcolor"></a>TextColor

Colore da utilizzare per il testo.

 <a name="value"></a>

### <a name="value"></a>value

Valore secondario da visualizzare nell'elemento di rendering. Il layout di questo oggetto è influenzato dall' `"style"` impostazione. Il tipo di carattere e il colore possono essere personalizzati impostando `"detailfont"` e `"detailcolor"` .

 <a name="Boolean_Elements"></a>

## <a name="boolean-elements"></a>Elementi booleani

Gli elementi booleani devono impostare il tipo su `"bool"` , può contenere un `"caption"` da visualizzare e l'oggetto `"value"` è impostato su true o false. Se le `"on"` `"off"` proprietà e sono impostate, si presuppone che siano immagini. Le immagini vengono risolte rispetto alla directory di lavoro corrente nell'applicazione. Se si vuole fare riferimento a file relativi al bundle, è possibile usare `"~"` come collegamento per rappresentare la directory del bundle dell'applicazione. Ad esempio `"~/favorite.png"` , sarà il file preferito. png contenuto nel file di bundle. Ad esempio:

```json
{ 
    "type": "boolean",
    "caption": "Demo of a Boolean",
    "value": true
},

{
    "type": "boolean",
    "caption": "Boolean using images",
    "value": false,
    "on": "favorite.png",
    "off": "~/favorited.png"
}
```

 <a name="type"></a>

### <a name="type"></a>type

Il tipo può essere impostato su `"boolean"` o su `"checkbox"` . Se impostato su booleano, utilizzerà un UISlider o immagini (se `"on"` e `"off"` sono impostati). Se la casella di controllo è impostata su, verrà utilizzata una casella di controllo. La `"group"` proprietà può essere utilizzata per contrassegnare un elemento booleano come appartenente a un particolare gruppo. Questa operazione è utile se la radice contenitore dispone anche di una `"group"` proprietà perché la radice riepiloga i risultati con un conteggio di tutti i valori booleani (o caselle di controllo) che appartengono allo stesso gruppo.

 <a name="Entry_Elements"></a>

## <a name="entry-elements"></a>Elementi entry

Usare gli elementi entry per consentire all'utente di immettere i dati. Il tipo per gli elementi entry è `"entry"` o `"password"` . La `"caption"` proprietà viene impostata sul testo da visualizzare a destra e l'oggetto `"value"` viene impostato sul valore iniziale su cui impostare la voce. `"placeholder"`Viene usato per mostrare un hint all'utente per le voci vuote (viene visualizzato in grigio). Ecco alcuni esempi:

```json
{
    "type": "entry",
    "caption": "Username",
    "value": "",
    "placeholder": "Your account username"
}, {
    "type": "password",
    "caption": "Password",
    "value": "",
    "placeholder": "You password"
}, {
    "type": "entry",
    "caption": "Zip Code",
    "value": "01010",
    "placeholder": "your zip code",
    "keyboard": "numbers"
}, {
    "type": "entry",
    "return-key": "route",
    "caption": "Entry with 'route'",
    "placeholder": "captialization all + no corrections",
    "capitalization": "all",
    "autocorrect": "no"
}
```

 <a name="autocorrect"></a>

### <a name="autocorrect"></a>correzione automatica

Determina lo stile di correzione automatica da usare per la voce. I valori possibili sono true o false (o le stringhe `"yes"` e `"no"` ).

 <a name="capitalization"></a>

### <a name="capitalization"></a>uso di maiuscole e minuscole

Stile di maiuscole da usare per la voce. I valori possibili sono:

- `all`
- `none`
- `sentences`
- `words`

 <a name="caption"></a>

### <a name="caption"></a>caption

Didascalia da usare per la voce

 <a name="keyboard"></a>

### <a name="keyboard"></a>tastiera

Tipo di tastiera da usare per l'immissione di dati. I valori possibili sono:

- `ascii`
- `decimal`
- `default`
- `email`
- `name`
- `numbers`
- `numbers-and-punctuation`
- `twitter`
- `url`

 <a name="placeholder"></a>

### <a name="placeholder"></a>segnaposto

Testo del suggerimento visualizzato quando la voce ha un valore vuoto.

 <a name="return-key"></a>

### <a name="return-key"></a>chiave di ritorno

Etichetta utilizzata per la chiave restituita. I valori possibili sono:

- `default`
- `done`
- `emergencycall`
- `go`
- `google`
- `join`
- `next`
- `route`
- `search`
- `send`
- `yahoo`

 <a name="value"></a>

### <a name="value"></a>value

Valore iniziale per la voce

 <a name="Radio_Elements"></a>

## <a name="radio-elements"></a>Elementi radio

Gli elementi radio hanno il tipo `"radio"` . L'elemento selezionato viene selezionato dalla `radioselected` proprietà sull'elemento radice che lo contiene.
Se, inoltre, viene impostato un valore per la `"group"` proprietà, questo pulsante di opzione appartiene a tale gruppo.

 <a name="Date_and_Time_Elements"></a>

## <a name="date-and-time-elements"></a>Elementi data e ora

I tipi `"datetime"` di elemento `"date"` e `"time"` vengono utilizzati per il rendering delle date con orari, date o ore. Questi elementi accettano come parametri una didascalia e un valore. Il valore può essere scritto in qualsiasi formato supportato dalla funzione .NET DateTime. Parse. Esempio:

```json
"header": "Dates and Times",
"elements": [
    {
        "type": "datetime",
        "caption": "Date and Time",
        "value": "Sat, 01 Nov 2008 19:35:00 GMT"
    }, {
        "type": "date",
        "caption": "Date",
        "value": "10/10"
    }, {
        "type": "time",
        "caption": "Time",
        "value": "11:23"
    }                       
]
```

 <a name="Html/Web_Element"></a>

## <a name="htmlweb-element"></a>Elemento HTML/Web

È possibile creare una cella che, quando viene toccata, incorpora una UIWebView che esegue il rendering del contenuto di un URL specificato, locale o remoto, usando il `"html"` tipo. Le uniche due proprietà per questo elemento sono `"caption"` e `"url"` :

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
