---
title: Markup JSON per MonoTouch.Dialog
description: Questo documento descrive la sintassi JSON che può essere utilizzata per compilare un'interfaccia utente di xamarin. IOS utilizzando monotouch. Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: bc6842871a2f59c9851e90adbc6609707a7ecd1f
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039649"
---
# <a name="monotouchdialog-json-markup"></a>Markup JSON per MonoTouch.Dialog

Questa pagina descrive il markup Json accettato di monotouch. Dialog [JsonElement](xref:MonoTouch.Dialog.JsonElement)

Possiamo iniziare con un esempio. Di seguito è riportato un file Json completo che può essere passato in JsonElement.

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

Il markup precedente produce l'interfaccia utente seguente:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "L'interfaccia utente creata dal markup specificato")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Ogni elemento nell'albero può contenere proprietà `"id"`. È possibile in fase di esecuzione per fare riferimento a elementi usando l'indicizzatore JsonElement o le singole sezioni. analogamente a quanto segue:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Sintassi dell'elemento radice

L'elemento radice contiene i valori seguenti:

-  `title`
-  `sections` (facoltativo)


L'elemento radice può trovarsi all'interno di una sezione come elemento per creare un controller annidato. In tal caso, la proprietà extra `"type"` deve essere impostato su `"root"`

 <a name="url" />


### <a name="url"></a>url

Se il `"url"` è impostata, se l'utente tocca questo RootElement, il codice verrà richiesto un file dall'url specificato e renderà il contenuto le nuove informazioni visualizzate. Ciò consente di creare estendere l'interfaccia utente dal server di base di ciò che l'utente tocca.

 <a name="group" />


### <a name="group"></a>raggruppamento

Se impostata, questa operazione set il groupname per l'elemento radice. I nomi dei gruppi vengono usati per selezionare un riepilogo di cui viene visualizzato come valore dell'elemento radice da uno degli elementi annidati nell'elemento. Questo è il valore di una casella di controllo o il valore di un pulsante di opzione.

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Identifica l'elemento di opzione selezionato in elementi annidati

 <a name="title" />


### <a name="title"></a>title

Se presente, sarà il titolo utilizzato per il RootElement

 <a name="type" />


### <a name="type"></a>tipo

Deve essere impostata su `"root"` quando questo viene visualizzato in una sezione (ciò viene utilizzata per nidificare i controller).

 <a name="sections" />


### <a name="sections"></a>sezioni

Si tratta di una matrice Json con le singole sezioni

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>Sintassi di sezione

In questa sezione:

-  `header` (facoltativo)
-  `footer` (facoltativo)
-  Matrice `elements`


 <a name="header" />


### <a name="header"></a>intestazione

Se presente, il testo dell'intestazione viene visualizzato come didascalia della sezione.

 <a name="footer" />


### <a name="footer"></a>Piè di pagina

Se presente, il piè di pagina viene visualizzato nella parte inferiore della sezione.

 <a name="elements" />


### <a name="elements"></a>elementi

Si tratta di una matrice di elementi. Ogni elemento deve contenere almeno una chiave, il `"type"` chiave che viene usato per identificare il tipo dell'elemento da creare.
Alcuni degli elementi condividono alcune proprietà comuni, ad esempio `"caption"` e `"value"`. L'elenco di elementi supportati sono:

-  `string` elementi (sia con o senza stili)
-  `entry` righe (normale o password)
-  `boolean` valori (tramite commutatori o immagini)


Elementi di stringa possono essere utilizzati come pulsanti, fornendo un metodo da richiamare quando l'utente tocca la cella o funzione di accesso,

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Il rendering di elementi

Gli elementi per il rendering si basano su di C# StringElement e StyledStringElement e può eseguire il rendering informazioni in modi diversi ed è possibile eseguirne il rendering in vari modi. Gli elementi più semplici possono essere creati come segue:

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Questa istruzione visualizzerà una stringa semplice con tutti i valori predefiniti: tipo di carattere, sfondo, colore del testo e decorazioni. È possibile collegare le azioni per questi elementi e li si comportino come pulsanti, impostando il `"ontap"` proprietà o il `"onaccessorytap"` proprietà:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

Il codice precedente verrà richiamato il metodo "ShowPhotos" nella classe "Acme.PhotoLibrary". Il `"onaccessorytap"` è simile, ma sarà essere richiamato solo se l'utente tocca l'accessorio invece se si tocca la cella. A tale scopo, è necessario impostare anche funzione di accesso:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Rendering di elementi può visualizzare due stringhe contemporaneamente, una didascalia e un'altra è il valore. Modo in cui vengono eseguito il rendering di queste stringhe dipende lo stile, è possibile impostarlo utilizzando il `"style"` proprietà. Il valore predefinito mostrerà la didascalia sulla sinistra e il valore a destra. Vedere la sezione sullo stile per altri dettagli. I colori vengono codificati utilizzando il simbolo '#' seguito da numeri esadecimali che rappresentano i valori per i valori rossi, verdi, blu e alfa forse. Il contenuto può essere codificato in formato breve (cifre esadecimali di 3 o 4) che rappresenta i valori RGB o RGBA. O il formato esteso (6 o 8 cifre) che rappresentano i valori RGB o RGBA. La versione abbreviata è una sintassi abbreviata per la scrittura di due volte la stessa cifra esadecimale. In modo costante di "& 1bc" viene interpretata dagli rosso = 0x11, verde = 0xbb e blu = 0xcc. Se il valore alfa non è presente, il colore è opaco. Ecco alcuni esempi:

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>Accessorio

Determina il tipo di accessori da visualizzare nell'elemento di rendering, i valori possibili sono:

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Se il valore non è presente, non viene visualizzato alcun accessorio

 <a name="background" />


### <a name="background"></a>sfondo

La proprietà background imposta il colore di sfondo della cella. Il valore è un URL dell'immagine (in questo caso, verrà richiamato il downloader di immagini asincrono e lo sfondo verrà aggiornato una volta che l'immagine viene scaricata) oppure può essere un colore specificato utilizzando la sintassi di colore.

 <a name="caption" />


### <a name="caption"></a>Didascalia

La stringa principale da visualizzare sull'elemento per il rendering. Il tipo di carattere e colore può essere personalizzati impostando il `"textcolor"` e `"font"` proprietà. Lo stile di rendering varia a seconda di `"style"` proprietà.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>colore e detailcolor

Il colore da utilizzare per il testo principale o del testo dettagliato.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>DetailFont e tipi di carattere

Il tipo di carattere da utilizzare per la didascalia o il testo dei dettagli. Il formato di una specifica del tipo di carattere è il nome del tipo di carattere seguito facoltativamente da un trattino e le dimensioni in punti.
Di seguito sono specifiche del tipo di carattere valido.

-  "Helvetica"
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

Determina come sono suddivise le righe. I valori possibili sono:

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


Entrambe `character-wrap` e `word-wrap` può essere usato in combinazione con il `"lines"` proprietà è impostata su zero per trasformare l'elemento per il rendering in un elemento a più righe.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>OnTap e onaccessorytap

Queste proprietà devono puntare a un nome di metodo statico nell'applicazione che accetta un oggetto come parametro. Quando si crea la gerarchia usando i metodi JsonDialog.FromFile o JsonDialog.FromJson è possibile passare un valore di oggetto facoltativo. Valore di questo oggetto viene quindi passato ai metodi. È possibile utilizzare questo per passare un contesto per il metodo statico. Ad esempio:

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

 <a name="lines" />


### <a name="lines"></a>linee

Se è impostato su zero, l'elemento il ridimensionamento automatico a seconda del contenuto delle stringhe contenuta verranno effettuate. Per il corretto funzionamento, è necessario impostare anche il `"linebreak"` proprietà `"character-wrap"` o `"word-wrap"`.

 <a name="style" />


### <a name="style"></a>stile

Lo stile determina il tipo di stile della cella che verrà usato per il rendering del contenuto e corrispondono ai valori di enumerazione UITableViewCellStyle.
I valori possibili sono:

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` : testo con un sottotitolo.


 <a name="subtitle" />


### <a name="subtitle"></a>Sottotitolo

Il valore da utilizzare per i sottotitoli in altre lingue. Si tratta di un collegamento su cui impostare lo stile `"subtitle"` e impostare il `"value"` proprietà in una stringa.
Questa operazione, entrambi con una singola voce.

 <a name="textcolor" />


### <a name="textcolor"></a>TextColor

Il colore da utilizzare per il testo.

 <a name="value" />


### <a name="value"></a>predefinito

Il valore secondario da visualizzare sull'elemento per il rendering. Il layout di questo oggetto è influenzato il `"style"` impostazione. Il tipo di carattere e colore può essere personalizzati impostando il `"detailfont"` e `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Elementi booleani

Elementi booleani devono impostare il tipo su `"bool"`, può contenere un `"caption"` per visualizzare e `"value"` è impostato su true o false. Se il `"on"` e `"off"` proprietà vengono impostate, questi sono considerati immagini. Le immagini vengono risolte in base alla directory di lavoro corrente dell'applicazione. Se si desidera fare riferimento ai file di bundle relativo, è possibile usare il `"~"` come collegamento per rappresentare la directory del bundle dell'applicazione. Ad esempio `"~/favorite.png"` saranno favorite.png contenuta nel file di bundle. Ad esempio:

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

 <a name="type" />


### <a name="type"></a>tipo

Tipo può essere impostato su `"boolean"` o `"checkbox"`. Se impostato su valore booleano, verrà utilizzata una UISlider o immagini (se entrambi `"on"` e `"off"` sono impostati). Se impostato su casella di controllo, verrà usata una casella di controllo. Il `"group"` proprietà può essere utilizzata per contrassegnare un elemento boolean come appartenenti a un determinato gruppo. Ciò è utile se il contenitore radice ha anche un `"group"` riepilogano i risultati con un conteggio di tutti i valori booleani (o le caselle di controllo) che appartengono allo stesso gruppo di proprietà come radice.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Elementi della voce

Per consentire all'utente di immettere dati è usare elementi di voci. Il tipo di elementi di voci è `"entry"` o `"password"`. Il `"caption"` proprietà è impostata per il testo da visualizzare a destra e il `"value"` è impostata sul valore iniziale su cui impostare la voce. Il `"placeholder"` consente di visualizzare un suggerimento all'utente per le voci vuote (è possibile vederlo grigio). Ecco alcuni esempi:

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

 <a name="autocorrect" />


### <a name="autocorrect"></a>correzione automatica

Determina lo stile di correzione automatica da utilizzare per la voce. I valori possibili sono true o false (o le stringhe `"yes"` e `"no"`).

 <a name="capitalization" />


### <a name="capitalization"></a>uso di maiuscole e minuscole

Lo stile di maiuscole/minuscole da utilizzare per la voce. I valori possibili sono:

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>Didascalia

La didascalia da utilizzare per la voce

 <a name="keyboard" />


### <a name="keyboard"></a>tastiera

Il tipo di tastiera da utilizzare per l'immissione dati. I valori possibili sono:

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>Segnaposto

Il testo di suggerimento che viene visualizzato quando la voce contiene un valore vuoto.

 <a name="return-key" />


### <a name="return-key"></a>chiave di restituzione

Etichetta utilizzata per la chiave return. I valori possibili sono:

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>predefinito

Il valore iniziale per la voce

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>Elementi radio

Opzione elementi hanno tipo `"radio"`. L'elemento selezionato viene prelevata dal `radioselected` proprietà nell'elemento radice che lo contiene.
Inoltre, se è impostato un valore per il `"group"` proprietà, questo pulsante di opzione appartiene a questo gruppo.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Data e ora elementi

I tipi di elementi `"datetime"`, `"date"` e `"time"` vengono utilizzati per eseguire il rendering con tempi di date, date o ore. Questi elementi accettano come parametri di una didascalia e il valore. Il valore può essere scritto in qualsiasi formato supportato dalla funzione DateTime. Parse .NET. Esempio:

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

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>Elemento HTML/Web

È possibile creare una cella che quando si tocca incorporerà un UIWebView che esegue il rendering di contenuto di un URL specificato, locali o remoti utilizzando il `"html"` tipo. Il solo due proprietà per questo elemento vengono `"caption"` e `"url"`:

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
