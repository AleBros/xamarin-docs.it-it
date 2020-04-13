---
title: Titolo di questo articolo
description: Una descrizione utile e chiara dell'articolo. Il contenuto in questa sezione è ciò che il sistema di Microsoft Docs restituisce con i risultati di ricerca, quindi è importante che sia una descrizione completa dello scopo e del contenuto per questo file di documento
keywords: elenco, di, parole, chiave, per, ricerca, interna
author: conceptdev
ms.author: crdun
ms.date: 02/26/2018
ms.topic: conceptual
ms.assetid: 11111111-2222-3333-4444-555555555555
ms.prod: xamarin
ms.openlocfilehash: f26a786f3da909c7d948fe99b4b3a1423352ecbe
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77553568"
---
# <a name="metadata-and-markdown-template"></a>Modello Markdown e metadati

Questo modello contiene esempi di sintassi di Markdown, nonché indicazioni su come impostare i metadati. Per esaminarne la maggior parte, è necessario visualizzare sia il **Markdown non elaborato** che la **visualizzazione sottoposta a rendering** (ad esempio, il Markdown non elaborato mostra il blocco dei metadati, che non è visibile nella visualizzazione sottoposta a rendering).

Quando si crea un file Markdown, è necessario copiare il modello in un nuovo file, compilare i metadati come specificato di seguito, impostare l'intestazione H1 sopra il titolo dell'articolo ed eliminare il contenuto.

## <a name="metadata"></a>Metadati

Il blocco dei metadati completo, con una serie di annotazioni, è mostrato di seguito:

```
---
title: This Article's Title
description: A good, solid description of this article. The content kept here is what the Microsoft Docs system returns with search results, so this had better be a complete description of the purpose and content for this document file
keywords: a, comma, separated, list, of keywords, to, use, for, this, doc, however, this, might, not, be, used, for, anything, much, anymore, but, still, good, to, have, if, you, want
author: conceptdev (your Github user name)
ms.author: crdun (your Microsoft Alias)
ms.date: 02/26/2018 (creation date, in US format: mm/dd/yyyy)
ms.topic: conceptual (one only of the following: conceptual, quickstart, tutorial, overview)
ms.assetid: b39854a6-c523-4a66-bef6-9b5da03bafff (a unique number representing the asset - just use a GUID, you can generate one at https://www.guidgenerator.com/)
ms.prod: xamarin (no reason to change this)
ms.custom: Analytics data, a field that gets imported into SkyEye so you can use it in custom reports
---
```

Alcune note importanti:

- È **necessario** includere uno spazio tra i due punti (:) e il valore di un elemento di metadati.
- Se un elemento dei metadati facoltativo non ha un valore, eliminarlo (non lasciarlo vuoto o usare "na").
- La presenza dei due punti in un valore (ad esempio, un titolo) causa l'interruzione del parser dei metadati. In questo caso, racchiudere il titolo tra virgolette doppie (ad esempio, `title: "Writing .NET Core console apps: An advanced step-by-step guide"`).
- **title**: questo titolo verrà visualizzato nei risultati dei motori di ricerca. Il titolo non deve essere identico al titolo nell'intestazione H1 e può contenere al massimo 60 caratteri.
- **author**, **manager**, **ms.reviewer**: il campo dell'autore deve contenere il **nome utente GitHub** dell'autore, non il relativo alias.  I campi "manager" e "ms.reviewer", invece, devono contenere gli alias Microsoft. ms.reviewer specifica il nome del project manager/sviluppatore associato all'articolo o alla funzionalità.
- **ms.assetid**: si tratta del GUID dell'articolo che viene usato per scopi di registrazione interna, ad esempio attività di Business Intelligence (BI). Quando si crea un nuovo file [https://www.guidgenerator.com](https://www.guidgenerator.com)Markdown, ottenere un GUID da .

## <a name="basic-markdown-gfm-and-special-characters"></a>Markdown di base, GFM e caratteri speciali

Sono supportati sia Markdown di base che GitHub Flavored Markdown (GFM). Per altre informazioni su questi elementi, vedere:

- [Sintassi Markdown di base](https://daringfireball.net/projects/markdown/syntax)
- [DocFX Flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)
- [Documentazione di GFM](https://guides.github.com/features/mastering-markdown)

Markdown usa caratteri speciali, ad esempio \*, \` e \#, per la formattazione. Se si vuole includere uno di questi caratteri nel contenuto, è necessario eseguire una delle seguenti operazioni:

- Inserire una barra rovesciata prima del carattere speciale come "escape" (ad esempio, `\*` per \*)
- Usare il [codice entità HTML](http://www.ascii.cl/htmlcodes.htm) per il carattere (ad esempio, `&#42;` per &#42;).

## <a name="file-name"></a>Nome file

Per i nomi di file vengono usate le regole seguenti:

- Contengono solo lettere minuscole, numeri e segni meno.
- Non usare spazi o segni di punteggiatura. Usare il segno meno per separare le parole e i numeri nel nome del file.
- Usare verbi di azione specifici, ad esempio sviluppare, acquistare, compilare, risolvere. Non usare parole al gerundio.
- Non usare parole estremamente brevi, ad esempio "un", "e", "il", "in", "o" e così via.
- Usare il formato markdown e l'estensione file md.
- Mantenere i nomi di file relativamente brevi. Fanno parte dell'URL degli articoli.

## <a name="headings"></a>Intestazioni

Usare l'iniziale maiuscola solo per la prima parola. Scrivere sempre in maiuscolo:

- La prima parola di un'intestazione.
- La parola che segue i due punti in un titolo o un'intestazione (ad esempio, "Procedura: Ordinare una matrice").

È necessario creare le intestazioni in stile atx, ovvero usare 1-6 simboli di cancelletto (#) all'inizio della riga per indicare un'intestazione, corrispondenti ai livelli delle intestazioni HTML da H1 a H6. Alcuni esempi di intestazioni di primo e secondo livello sono stati usati in precedenza.

Nell'argomento **deve** essere presente una sola intestazione di primo livello (H1), che verrà visualizzata come titolo della pagina.

Se l'intestazione termina con un carattere `#`, è necessario aggiungere un ulteriore carattere `#` alla fine per garantire che il rendering del titolo venga eseguito correttamente. Ad esempio: `# Async Programming in F# #`.

Le intestazioni di secondo livello verranno usate per generare il sommario della pagina, visualizzato nella sezione "Contenuto dell'articolo" sotto il titolo della pagina.

### <a name="third-level-heading"></a>Intestazione di terzo livello
#### <a name="fourth-level-heading"></a>Intestazione di quarto livello
##### <a name="fifth-level-heading"></a>Intestazione di quinto livello
###### <a name="sixth-level-heading"></a>Intestazione di sesto livello

## <a name="text-styling"></a>Stile del testo

_Corsivo_: da usare per file, cartelle, percorsi (posizionare gli elementi lunghi su una riga distinta), nuovi termini, URL (a meno che non ne venga eseguito il rendering come collegamenti, in base all'impostazione predefinita).

**Grassetto**: da usare per gli elementi dell'interfaccia utente.

## <a name="links"></a>Collegamenti

### <a name="internal-links"></a>Collegamenti interni

Per creare un collegamento a un'intestazione nello stesso file Markdown (anche denominato collegamento di ancoraggio), è necessario individuare l'ID dell'intestazione da collegare. Per verificare l'ID, visualizzare l'origine dell'articolo sottoposto a rendering, trovare l'ID dell'intestazione (ad esempio, `id="blockquote"`) e specificare il collegamento usando il simbolo # e l'ID (ad esempio, `#blockquote`).
L'ID viene generato automaticamente in base al testo dell'intestazione. Ad esempio, per una sezione univoca denominata `## Step 2`, l'ID sarà simile a `id="step-2"`.

- Esempio: `[Chapter 1](#chapter-1)`

Per creare un collegamento a un file Markdown nello stesso repository, usare [collegamenti relativi](https://www.w3.org/TR/WD-html40-970917/htmlweb.html#h-5.1.2), includendo "md" alla fine del nome del file.

- Esempio: `[Readme file](../readme.md)`
- Esempio: `[Welcome to .NET](../docs/welcome.md)`

Per creare un collegamento a un'intestazione in un file Markdown nello stesso repository, usare il collegamento relativo e il collegamento con hashtag.

- Esempio: `[.NET Community](../docs/welcome.md#community)`

### <a name="external-links"></a>Collegamenti esterni

Per creare un collegamento a un file esterno, usare l'URL completo come collegamento.

- Esempio: `[GitHub](http://www.github.com)`

Se un file Markdown contiene un URL, questo verrà trasformato in un collegamento ipertestuale.

- Esempio: `http://www.github.com`

### <a name="links-to-apis"></a>Collegamenti ad API

Il sistema di compilazione dispone di alcune estensioni che consentono di collegare API .NET senza che sia necessario usare collegamenti esterni.
Quando si crea un collegamento a un'API, è possibile usare il relativo identificatore univoco (UID), che viene generato automaticamente dal codice sorgente.

È possibile usare una delle sintassi seguenti:

1. Collegamento Markdown:`[link_text](xref:UID)`
2. Collegamento automatico: `<xref:UID>`
3. Sintassi abbreviata: `@UID`

- Esempio: `@System.String`
- Esempio: `[String class](xref:System.String)`

Per altre informazioni sull'uso di questa notazione, vedere la pagina relativa all'[uso di riferimenti incrociati](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#using-cross-reference).

> Attualmente, non è facile individuare l'UID. Il modo migliore per trovare l'UID per un'API è cercarlo in questo repository: [docascode/coreapi](https://github.com/docascode/coreapi). È in corso lo sviluppo di un sistema più efficiente, che sarà disponibile in futuro.

Quando l'UID contiene i caratteri speciali \` o \#, il valore UID deve essere codificato in formato HTML, come %60 e %23, rispettivamente, negli esempi seguenti:

- Esempio: @System.Threading.Tasks.Task\`1 diventa `@System.Threading.Tasks.Task%601`
- Esempio: @System.Exception\# diventa `@System.Exception.%23ctor`

## <a name="lists"></a>Elenchi

### <a name="ordered-lists"></a>Elenchi ordinati

1. Questa
1. Is
1. Un
1. Ordinato
1. Elenco

#### <a name="ordered-list-with-an-embedded-list"></a>Elenco ordinato con un elenco incorporato

1. Questo
1. è
1. any
1. incorporato
    1. Miss Scarlett
    1. Professor Plum
1. ordered
1. list

### <a name="unordered-lists"></a>Elenchi non ordinati

- Questa
- is
- a
- puntato
- list

#### <a name="unordered-list-with-an-embedded-list"></a>Elenco non ordinato con un elenco incorporato

- Questa
- puntato
- list
  - Signora Pavone
  - Dottor Verde
- contains
- Altre
    1. Colonnello Mustard
    1. Signora Bianchi
- lists

## <a name="horizontal-rule"></a>Righello orizzontale

---

## <a name="tables"></a>Tabelle

| Tabelle        | Sono           | Accesso sporadico  |
| ------------- |:-------------:| -----:|
| col 3 è      | allineata a destra | $1600 |
| col 2 è      | centrata      |   $ 12 |
| col 1 è predefinita | allineata a sinistra     |    $1 |

Per creare le tabelle più facilmente, è possibile usare uno [strumento generatore di tabelle Markdown](http://www.tablesgenerator.com/markdown_tables).

### <a name="inline-code-blocks-with-language-identifier"></a>Blocchi di codice inline con identificatore del linguaggio

Usare tre apici (\`\`\`) e un ID di linguaggio per applicare il codice a colori di uno specifico linguaggio a un blocco di codice. Ecco l'elenco completo degli [ID di linguaggio GFM](https://github.com/jmm/gfm-lang-ids/wiki/GitHub-Flavored-Markdown-(GFM)-language-IDs).

#### <a name="c9839"></a>C&#9839;

```c#
using System;
namespace HelloWorld
{
    class Hello
    {
        static void Main()
        {
            Console.WriteLine("Hello World!");

            // Keep the console window open in debug mode.
            Console.WriteLine("Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```

#### <a name="xml"></a>Xml

```xml
<dict>
    <key>CFBundleURLSchemes</key>
    <array>
        <string>evolveCountdown</string>
    </array>
```

#### <a name="xaml"></a>XAML

```xaml
<Label Text="Hello, XAML!"
        FontSize="Large"
        FontAttributes="Bold"
        TextColor="Blue" />
```

#### <a name="powershell"></a>PowerShell

```powershell
Clear-Host
$Directory = "C:\Windows\"
$Files = Get-Childitem $Directory -recurse -Include *.log `
-ErrorAction SilentlyContinue
```

### <a name="generic-code-block"></a>Blocco di codice generico

Usare tre apici (&#96;&#96;&#96;) per la codifica di un blocco di codice generico.

> L'approccio consigliato è usare blocchi di codice con identificatori di linguaggio, come illustrato nella sezione precedente, per garantire la corretta evidenziazione della sintassi nel sito della documentazione. Usare blocchi di codice generico solo quando necessario.

```
function fancyAlert(arg) {
    if(arg) {
        $.docs({div:'#foo'})
    }
}
```

### <a name="inline-code"></a>Codice inline

Usare apici (&#96;) per `inline code`. Usare il codice inline per i comandi della riga di comando, i nomi di tabelle e colonne di database e le parole chiave del linguaggio.

## <a name="blockquotes"></a>Citazioni di paragrafi

> La siccità si protraeva ormai da dieci milioni di anni, e il regno delle terribili lucertole era finito da molto tempo. Lì, sull'Equatore, nel continente che un giorno sarebbe stato chiamato Africa, la lotta per la vita aveva raggiunto un nuovo diapason di ferocia, e il vincitore ancora non si intravedeva. In quella terra sterile e arida soltanto le creature piccole o fulminee o feroci potevano prosperare, o appena sperare di sopravvivere.

## <a name="images"></a>Immagini

### <a name="static-image-or-animated-gif"></a>Immagine statica o GIF animata

```
![this is the alt text](images/dotnet.png)
```

![Progettazione reattiva](images/responsivedesign.gif)

### <a name="linked-image"></a>Immagine collegata

```
[![alt text for linked image](images/dotnet.png)](https://dot.net)
```

## <a name="videos"></a>Video

### <a name="channel-9"></a>Channel 9

Usare questa sintassi speciale per citazione:

```
> [!Video https://channel9.msdn.com/Shows/On-NET/Shipping-NET-Core-RC2--Tools-Preview-1/player]
```

L'URL del video di Channel 9 deve iniziare con `https` e terminare con `/player`, in caso contrario verrà incorporata l'intera pagina anziché solo il video.

### <a name="youtube"></a>YouTube

Usare questa sintassi speciale per citazione:

```
> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]
```

Il video di YouTube deve contenere `/embed/` (seguire il formato riportato sopra), in caso contrario verrà eseguito un tentativo di rendering dell'intera pagina e potrebbe non funzionare.

## <a name="docsmicrosoft-extensions"></a>Estensioni docs.microsoft

docs.microsoft fornisce alcune estensioni aggiuntive a GitHub Flavored Markdown (GFM).

### <a name="alerts"></a>Avvisi

È importante usare gli stili di avviso seguenti, in modo che ne venga eseguito il rendering con lo stile appropriato nel sito della documentazione. Tuttavia, il motore di rendering in GitHub non li differenzia.

#### <a name="note"></a>Note

```
> [!NOTE]
> This is a NOTE
```

#### <a name="warning"></a>Avviso

```
> [!WARNING]
> This is a WARNING
```

#### <a name="tip"></a>Suggerimento

```
> [!TIP]
> This is a TIP
```

#### <a name="important"></a>Importante

```
> [!IMPORTANT]
> This is IMPORTANT
```

Il rendering sarà simile al seguente:

![Stili di avviso](images/alerts.png)

### <a name="buttons"></a>Pulsanti

```
> [!div class="button"]
[button links](../docs/core/index.md)
```

È possibile vedere un esempio di pulsanti in azione nella [documentazione di Intune](https://docs.microsoft.com/intune/get-started/choose-how-to-enroll-devices).

### <a name="selectors"></a>Selettori

```
> [!div class="op_single_selector"]
- [macOS](../docs/core/tutorials/using-on-macos.md)
- [Windows](../docs/core/tutorials/using-on-windows.md)
```

È possibile vedere un esempio di selettori in azione nella [documentazione di Intune](https://docs.microsoft.com/intune/deploy-use/what-to-tell-your-end-users-about-using-microsoft-intune#how-your-end-users-get-their-apps).

### <a name="step-by-steps"></a>Procedure dettagliate

```
> [!div class="step-by-step"]
[Pre](../docs/csharp/expression-trees-interpreting.md)
[Next](../docs/csharp/expression-trees-translating.md)
```

È possibile vedere un esempio di procedure dettagliate in azione nella [documentazione di Advanced Threat Analytics](https://docs.microsoft.com/advanced-threat-analytics/deploy-use/install-ata-step2).
