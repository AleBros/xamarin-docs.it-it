---
title: Problemi noti e soluzioni alternative
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 186faf3fc4f93d1c9a4af9e3e9f72afd569fed8b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="known-issues--workarounds"></a>Problemi noti e soluzioni alternative

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistenza di CultureInfo nelle celle

Impostazione `System.Threading.CurrentThread.CurrentCulture` o `System.Globalization.CultureInfo.CurrentCulture` non viene mantenuto tra le celle di cartella di lavoro di destinazioni di cartelle di lavoro basato su Mono (Mac, iOS e Android) a causa di un [bug del Mono `AppContext.SetSwitch` ] [ appcontext-bug] implementazione .

### <a name="workarounds"></a>Soluzioni

* Impostare locale di dominio applicazione `DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* O l'aggiornamento a cartelle di lavoro 1.2.1 o successive, che verrà riscrivere le assegnazioni da `System.Threading.CurrentThread.CurrentCulture` e `System.Globalization.CultureInfo.CurrentCulture` per fornire il comportamento desiderato (working al bug Mono).

## <a name="unable-to-use-newtonsoftjson"></a>Impossibile utilizzare newtonsoft. JSON

### <a name="workaround"></a>Soluzione alternativa

* L'aggiornamento a cartelle di lavoro 1.2.1, che consente di installare 9.0.1 di newtonsoft. JSON.
  1.3, le cartelle di lavoro attualmente nel canale alfa, supporta le versioni 10 e versioni successive.

### <a name="details"></a>Dettagli

È stato rilasciato newtonsoft. JSON 10 che ha respinto la dipendenza da Microsoft. CSharp in conflitto con le cartelle di lavoro di versione viene fornito per supportare `dynamic`. Questo problema viene risolto nella versione di anteprima 1.3 cartelle di lavoro, ma per ora ci siamo adoperati il problema dal blocco newtonsoft. JSON specificamente alla versione 9.0.1.

Pacchetti NuGet in modo esplicito in base 10 di newtonsoft. JSON o versione successiva sono supportati solo in cartelle di lavoro 1.3, attualmente nel canale alfa.

## <a name="code-tooltips-are-blank"></a>Descrizioni comandi codice sono vuoto

È presente un [bug nell'editor Monaco] [ monaco-bug] in Safari/WebKit, viene utilizzata l'app cartelle di lavoro Mac, che comporta il rendering di descrizioni comandi codice senza testo.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Soluzione alternativa

* Facendo clic sul componente tooltip dopo che viene visualizzato forzerà il testo per il rendering.

* O l'aggiornamento a cartelle di lavoro 1.2.1 o versione successiva

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Renderer SkiaSharp mancano le cartelle di lavoro 1.3

A partire da cartelle di lavoro 1.3, è stata rimossa il renderer SkiaSharp che abbiamo fornito nelle cartelle di lavoro 0.99.0, a favore SkiaSharp fornendo il renderer di se stesso, tramite il nostro [SDK] [Guide/cross-piattaforma o le cartelle di lavoro/sdk / /].

### <a name="workaround"></a>Soluzione alternativa

* SkiaSharp l'aggiornamento alla versione più recente in NuGet. Al momento della scrittura, si tratta 1.57.1.

## <a name="related-links"></a>Collegamenti correlati

- [Segnalazione dei bug](~/tools/workbooks/install.md#reporting-bugs)
