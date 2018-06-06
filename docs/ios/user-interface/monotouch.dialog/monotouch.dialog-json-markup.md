---
title: Markup Json MonoTouch.Dialog
description: Questo documento descrive la sintassi JSON che può essere usata per compilare un'interfaccia utente xamarin. IOS utilizzando MonoTouch.Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: dc3f4ea87bbd381a4a1767fb9179fb1bcf0c56d8
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790757"
---
# <a name="monotouchdialog-json-markup"></a>Markup Json MonoTouch.Dialog

Questa pagina vengono descritti il markup Json accettato da del MonoTouch.Dialog [JsonElement](https://developer.xamarin.com/api/type/MonoTouch.Dialog.JsonElement/)

Segnalare il problema, iniziare con un esempio. Di seguito è un file Json completo che può essere passato in JsonElement.

```csharp
{     
  "title": "Json Sample",
  "sections": [ 
      {
          "header": "Booleans",
          "footer": "Slider or image-based",
          "id": "first-section",
          "elements": [
              { 
                  "type" : "boolean",
                  "caption" : "Demo of a Boolean",
                  "value"   : true
              }, {
                  "type": "boolean",
                  "caption" : "Boolean using images",
                  "value"   : false,
                  "on"      : "favorite.png",
                  "off"     : "~/favorited.png"
              }, {
                      "type": "root",
                      "title": "Tap for nested controller",
                      "sections": [ {
                         "header": "Nested view!",
                         "elements": [
                           {
                             "type": "boolean",
                             "caption": "Just a boolean",
                             "id": "the-boolean",
                             "value": false
                           },
                           {
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

Il codice precedente produce l'interfaccia utente di seguente:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "L'interfaccia utente creato per il markup specifico")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Ogni elemento nell'albero può contenere la proprietà `"id"`. È in fase di esecuzione è possibile fare riferimento a singole sezioni o elementi utilizzando l'indicizzatore JsonElement. analogamente a quanto segue:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Sintassi dell'elemento radice

L'elemento radice contiene i valori seguenti:

-  `title`
-  `sections` (facoltativo)


L'elemento radice può trovarsi all'interno di una sezione come un elemento per creare un controller annidato. In questo caso, la proprietà aggiuntiva `"type"` deve essere impostato su `"root"`

 <a name="url" />


### <a name="url"></a>url

Se il `"url"` è impostata, se l'utente tocca su questo RootElement, il codice richiede un file dall'url specificato e renderà il contenuto le nuove informazioni visualizzate. Ciò consente di creare estendere l'interfaccia utente dal server in base a cui l'utente tocca.

 <a name="group" />


### <a name="group"></a>raggruppamento

Se impostato, viene impostata la proprietà groupname per l'elemento radice. I nomi dei gruppi vengono utilizzati per selezionare un riepilogo che viene visualizzato come valore dell'elemento radice da uno degli elementi annidati nell'elemento. Questo è il valore di una casella di controllo o il valore di un pulsante di opzione.

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Identifica l'elemento di opzione selezionato in elementi nidificati

 <a name="title" />


### <a name="title"></a>title

Se presente, sarà il titolo utilizzato per il RootElement

 <a name="type" />


### <a name="type"></a>tipo

Deve essere impostato su `"root"` quando questo viene visualizzato in una sezione (utilizzata per nidificare controller).

 <a name="sections" />


### <a name="sections"></a>sezioni

Si tratta di una matrice Json con singole sezioni

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>Sintassi di sezione

In questa sezione:

-  `header` (facoltativo)
-  `footer` (facoltativo)
-  Matrice `elements`


 <a name="header" />


### <a name="header"></a>intestazione

Se presente, il testo dell'intestazione viene visualizzato come titolo della sezione.

 <a name="footer" />


### <a name="footer"></a>Piè di pagina

Se presente, il piè di pagina viene visualizzato nella parte inferiore della sezione.

 <a name="elements" />


### <a name="elements"></a>elementi

Si tratta di una matrice di elementi. Ogni elemento deve contenere almeno una chiave, il `"type"` chiave utilizzata per identificare il tipo dell'elemento da creare.
Alcuni degli elementi condividono alcune proprietà comuni come `"caption"` e `"value"`. L'elenco di elementi supportati sono:

-  `string` elementi (con e senza lo stile)
-  `entry` righe (regolare o password)
-  `boolean` valori (tramite commutatori o immagini)


Elementi stringa possono essere utilizzati come pulsanti, fornendo un metodo da richiamare quando l'utente tocca la cella o di accessori,

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Il rendering di elementi

Gli elementi di rendering sono in base al linguaggio c# StringElement e StyledStringElement e il rendering di informazioni in diversi modi e sia possibile eseguirne il rendering in vari modi. È possibile creare gli elementi più semplici simile al seguente:

```csharp
{
        "type": "string",
        "caption": "Json Serializer",
}
```

Verranno visualizzati una semplice stringa con tutti i valori predefiniti: tipo di carattere, sfondo, il colore del testo ed effetti. È possibile collegare azioni a questi elementi e comportarsi come pulsanti impostando il `"ontap"` proprietà o `"onaccessorytap"` proprietà:

```csharp
{
    "type":    "string",
        "caption": "View Photos",
        "ontap:    "Acme.PhotoLibrary.ShowPhotos"
}
```

Il precedente verrà richiamato il metodo "ShowPhotos" nella classe "Acme.PhotoLibrary". Il `"onaccessorytap"` è simile, ma solo essere richiamato se l'utente tocca nella funzione di accesso anziché toccato sulla cella. A tale scopo, è necessario impostare anche la funzione di accesso:

```csharp
{
    "type":     "string",
        "caption":  "View Photos",
        "ontap:     "Acme.PhotoLibrary.ShowPhotos",
        "accessory: "detail-disclosure",
        "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Il rendering di elementi possibile visualizzare due stringhe in una sola volta, una didascalia e un'altra è il valore. Come vengono eseguito il rendering di queste stringhe dipendono lo stile, è possibile impostare questa opzione mediante la `"style"` proprietà. Il valore predefinito mostrerà la didascalia sulla sinistra e il valore a destra. Vedere la sezione dello stile per altri dettagli. I colori vengono codificati utilizzando il simbolo '#' seguito da numeri esadecimali che rappresentano i valori per i valori di rossi, verdi, blu e alfa forse. Il contenuto può essere codificato in formato breve (cifre esadecimali di 3 o 4) che rappresenta i valori RGB o RGBA. O il formato esteso (6 o 8 cifre) che rappresentano i valori RGB o RGBA. La versione breve è una sintassi abbreviata per la scrittura di due volte la stessa cifra esadecimale. In modo costante "#1bc" viene interpretato come rosso = 0x11, verde = 0xbb e blu = 0xcc. Se il valore alfa non è presente, il colore è opaco. Ecco alcuni esempi:

```csharp
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>funzione di accesso

Determina il tipo di funzione di accesso da visualizzare nell'elemento per il rendering, i valori possibili sono:

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Se il valore non è presente, non viene visualizzato alcun accessori

 <a name="background" />


### <a name="background"></a>sfondo

La proprietà background imposta il colore di sfondo per la cella. Il valore è un URL dell'immagine (in questo caso, verrà richiamato il downloader di immagine async e lo sfondo verrà aggiornato dopo l'immagine viene scaricata) oppure può essere un colore specificato utilizzando la sintassi di colore.

 <a name="caption" />


### <a name="caption"></a>Didascalia

La stringa principale da visualizzare sull'elemento per il rendering. Il tipo di carattere e il colore possono essere personalizzati impostandone il `"textcolor"` e `"font"` proprietà. Lo stile di rendering varia a seconda di `"style"` proprietà.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>colore e detailcolor

Il colore da utilizzare per il testo principale o del testo dettagliato.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>DetailFont e tipi di carattere

Il tipo di carattere da utilizzare per la didascalia o il testo dei dettagli. Il formato di una specifica del tipo di carattere è il nome del carattere seguito facoltativamente da un trattino e la dimensione in punti.
Di seguito sono specifiche del tipo di carattere valido.

-  "Helvetica"
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

Determina come le righe sono suddivise. I valori possibili sono:

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


Entrambi `character-wrap` e `word-wrap` può essere utilizzato con il `"lines"` proprietà è impostata su zero per disattivare l'elemento per il rendering in un elemento a più righe.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>OnTap e onaccessorytap

Queste proprietà devono puntare a un nome di metodo statico dell'applicazione che accetta un oggetto come parametro. Quando si crea la gerarchia utilizzando i metodi JsonDialog.FromFile o JsonDialog.FromJson è possibile passare un valore di oggetto facoltativo. Valore di questo oggetto viene quindi passato ai metodi di. Ciò consente di passare un contesto per il metodo statico. Ad esempio:

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

Se questo è impostato su zero, verranno effettuate Ridimensiona elemento a seconda del contenuto delle stringhe di contenuti. Per il funzionamento, è necessario impostare anche la `"linebreak"` proprietà `"character-wrap"` o `"word-wrap"`.

 <a name="style" />


### <a name="style"></a>stile

Lo stile determina il tipo di stile di cella che verrà utilizzato per il rendering del contenuto e che corrispondano a valori di enumerazione UITableViewCellStyle.
I valori possibili sono:

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` : testo con un sottotitolo.


 <a name="subtitle" />


### <a name="subtitle"></a>Sottotitolo

Il valore da utilizzare per il sottotitolo. Si tratta di un collegamento su cui impostare lo stile `"subtitle"` e impostare il `"value"` proprietà in una stringa.
Questa operazione, entrambi con una sola voce.

 <a name="textcolor" />


### <a name="textcolor"></a>TextColor

Il colore da utilizzare per il testo.

 <a name="value" />


### <a name="value"></a>predefinito

Il valore secondario da visualizzare sull'elemento per il rendering. Il layout di questo oggetto è interessato dal `"style"` impostazione. Il tipo di carattere e il colore possono essere personalizzati impostandone il `"detailfont"` e `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Elementi booleani

Elementi booleani devono impostare il tipo su `"bool"`, può contenere un `"caption"` per visualizzare e `"value"` è impostata su true o false. Se il `"on"` e `"off"` sono impostate, questi sono considerati immagini. Le immagini vengono risolte rispetto alla directory di lavoro corrente nell'applicazione. Se si desidera fare riferimento ai file di bundle relativo, è possibile utilizzare il `"~"` come collegamento per rappresentare la directory del pacchetto dell'applicazione. Ad esempio `"~/favorite.png"` sarà favorite.png contenuta nel file di bundle. Ad esempio:

```csharp
{ 
    "type" : "boolean",
    "caption" : "Demo of a Boolean",
    "value"   : true
},

{
    "type": "boolean",
    "caption" : "Boolean using images",
    "value"   : false,
    "on"      : "favorite.png",
    "off"     : "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>tipo

Tipo può essere impostato su `"boolean"` o `"checkbox"`. Se impostato su booleano userà un UISlider o immagini (se entrambi `"on"` e `"off"` sono impostati). Se impostato su una casella di controllo, utilizzerà una casella di controllo. Il `"group"` proprietà può essere utilizzata per contrassegnare un elemento boolean come appartenenti a un determinato gruppo. Ciò è utile se il contenitore radice ha anche un `"group"` riepiloga i risultati con un conteggio di tutti i valori booleani (o le caselle di controllo) che appartengono allo stesso gruppo di proprietà come radice.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Elementi delle voci

Utilizzare gli elementi di ingresso per consentire all'utente di immettere dati. Il tipo per gli elementi di movimento è `"entry"` o `"password"`. Il `"caption"` proprietà è impostata sul testo da visualizzare a destra e `"value"` è impostata sul valore iniziale per impostare la voce. Il `"placeholder"` viene utilizzato per visualizzare un suggerimento all'utente per le voci vuote (risulta grigio). Ecco alcuni esempi:

```csharp
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

Determina lo stile di correzione automatica da utilizzare per la voce. I possibili valori sono true o false (o le stringhe di `"yes"` e `"no"`).

 <a name="capitalization" />


### <a name="capitalization"></a>uso di maiuscole e minuscole

Lo stile di lettere maiuscole/minuscole da utilizzare per la voce. I valori possibili sono:

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>Didascalia

La didascalia da utilizzare per la voce

 <a name="keyboard" />


### <a name="keyboard"></a>tastiera

Il tipo di tastiera da utilizzare per l'immissione di dati. I valori possibili sono:

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

Il testo di suggerimento visualizzato quando la voce ha un valore vuoto.

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


## <a name="radio-elements"></a>Elementi di opzione

Gli elementi radio abbiano tipo `"radio"`. Viene selezionato l'elemento è selezionato per il `radioselected` proprietà nell'elemento contenitore radice.
Inoltre, se è impostato un valore per il `"group"` proprietà, questo pulsante di opzione appartiene a questo gruppo.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Data e ora elementi

I tipi di elemento `"datetime"`, `"date"` e `"time"` vengono utilizzati per eseguire il rendering di date con volte, date o ore. Questi elementi accettano come parametri di una didascalia e un valore. Il valore può essere scritto in qualsiasi formato supportato dalla funzione DateTime. Parse .NET. Esempio:

```csharp
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


## <a name="htmlweb-element"></a>Elemento HTML o Web.

È possibile creare una cella che quando si tocca verrà incorporato un UIWebView che esegue il rendering di un URL specificato, locali o remoti utilizzando il `"html"` tipo. Sono solo due proprietà per questo elemento `"caption"` e `"url"`:

```csharp
{
        "type": "html",
        "caption": "Miguel's blog",
        "url": "http://tirania.org/blog" 
}
```
