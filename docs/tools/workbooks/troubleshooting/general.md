---
title: Problemi noti e soluzioni alternative
description: Questo documento descrive problemi noti e soluzioni alternative per le cartelle di lavoro di Xamarin. Illustra i problemi di CultureInfo, JSON e altre problematiche.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 221ed97db17da62f513448b6c85d4df205a7cbaf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110311"
---
# <a name="known-issues--workarounds"></a>Problemi noti e soluzioni alternative

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistenza di CultureInfo nelle celle

L'impostazione `System.Threading.CurrentThread.CurrentCulture` oppure `System.Globalization.CultureInfo.CurrentCulture` non viene mantenuta fra le celle della cartella di lavoro in destinazioni di cartelle di lavoro basato su Mono (Mac, iOS e Android) a causa dell'errore una [bug in Mono `AppContext.SetSwitch` ] [ appcontext-bug] implementazione .

### <a name="workarounds"></a>Soluzioni

* Impostare l'applicazione-dominio-local `DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* O aggiornamento a cartelle di lavoro 1.2.1 o versione successiva, che verrà riscrivere le assegnazioni ai `System.Threading.CurrentThread.CurrentCulture` e `System.Globalization.CultureInfo.CurrentCulture` per fornire il comportamento desiderato (risolvere i bug di Mono).

## <a name="unable-to-use-newtonsoftjson"></a>Non è possibile usare newtonsoft. JSON

### <a name="workaround"></a>Soluzione alternativa

* Aggiornamento a cartelle di lavoro 1.2.1, che installerà newtonsoft. JSON 9.0.1.
  Le cartelle di lavoro 1.3, attualmente nel canale alfa, supporta le versioni 10 e versioni successive.

### <a name="details"></a>Dettagli

È stato rilasciato newtonsoft. JSON 10 che ha respinto la dipendenza che è in conflitto con le cartelle di lavoro di versione viene fornito per il supporto Microsoft. CSharp `dynamic`. Questo problema viene risolto nella versione di anteprima le cartelle di lavoro 1.3, ma per ora abbiamo lavorato il problema dal blocco newtonsoft. JSON in modo specifico alla versione 9.0.1.

I pacchetti NuGet in modo esplicito in base 10 di newtonsoft. JSON o versioni successive sono supportati solo nella versione 1.3 di cartelle di lavoro, attualmente nel canale alfa.

## <a name="code-tooltips-are-blank"></a>Descrizioni comandi del codice sono vuoto

È presente una [bug nell'editor Monaco] [ monaco-bug] in Safari/WebKit, che viene usato nell'app per le cartelle di lavoro di Mac, che comporta il rendering delle descrizioni comando codice senza testo.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Soluzione alternativa

* Facendo clic sul componente tooltip dopo che viene visualizzato forzerà il testo per il rendering.

* O l'aggiornamento a cartelle di lavoro 1.2.1 o versione successiva

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>I renderer di SkiaSharp non sono presenti nella versione 1.3 di cartelle di lavoro

A partire da 1.3 a cartelle di lavoro, è stata rimossa i renderer di SkiaSharp utilizzata nelle cartelle di lavoro 0.99.0, a favore di SkiaSharp propone i renderer, con nostro [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Soluzione alternativa

* Aggiornare SkiaSharp alla versione più recente di NuGet. Al momento della scrittura, si tratta 1.57.1.

## <a name="related-links"></a>Collegamenti correlati

- [Segnalazione dei bug](~/tools/workbooks/install.md#reporting-bugs)
