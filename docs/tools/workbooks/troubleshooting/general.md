---
title: Problemi noti & soluzioni alternative
description: Questo documento descrive i problemi noti e le soluzioni alternative per Xamarin Workbooks. Vengono illustrati i problemi di CultureInfo, i problemi JSON e altro ancora.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: c7b9f93c2d6339ba1fd26b27742ecfc0f438c5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018169"
---
# <a name="known-issues--workarounds"></a>Problemi noti & soluzioni alternative

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistenza di CultureInfo tra celle

L'impostazione di `System.Threading.CurrentThread.CurrentCulture` o `System.Globalization.CultureInfo.CurrentCulture` non viene mantenute nelle celle delle cartelle di lavoro in destinazioni di cartelle di lavoro basate su mono (Mac, iOS e Android) a causa di un [bug nell'implementazione di `AppContext.SetSwitch`di mono][appcontext-bug] .

### <a name="workarounds"></a>Soluzioni

- Impostare la `DefaultThreadCurrentCulture`locale dell'applicazione-dominio:

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- In alternativa, eseguire l'aggiornamento alle cartelle di lavoro 1.2.1 o successive, in modo da riscrivere le assegnazioni in `System.Threading.CurrentThread.CurrentCulture` e `System.Globalization.CultureInfo.CurrentCulture` per fornire il comportamento desiderato (aggirando il bug mono).

## <a name="unable-to-use-newtonsoftjson"></a>Non è possibile usare Newtonsoft. JSON

### <a name="workaround"></a>Soluzione alternativa

- Aggiornare le cartelle di lavoro 1.2.1, che installeranno Newtonsoft. JSON 9.0.1.
  Le cartelle di lavoro 1,3, attualmente nel canale alfa, supportano le versioni 10 e successive.

### <a name="details"></a>Dettagli

È stato rilasciato Newtonsoft. JSON 10 che ha urtato la dipendenza da Microsoft. CSharp, che è in conflitto con le cartelle di lavoro della versione fornite per supportare `dynamic`. Questo problema viene risolto nella versione di anteprima della cartella di lavoro 1,3, ma per il momento è stato risolto il problema aggiungendo Newtonsoft. JSON in modo specifico alla versione 9.0.1.

I pacchetti NuGet in modo esplicito a seconda di Newtonsoft. JSON 10 o versione successiva sono supportati solo nelle cartelle di lavoro 1,3, attualmente nel canale alfa.

## <a name="code-tooltips-are-blank"></a>Descrizioni comando del codice vuote

È presente un [bug nell'editor di Monaco][monaco-bug] in Safari/WebKit, che viene usato nell'app per le cartelle di lavoro Mac, che consente il rendering del codice descrizioni comandi senza testo.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Soluzione alternativa

- Se si fa clic sulla descrizione comando dopo che è stata visualizzata, verrà forzato il rendering del testo.

- O eseguire l'aggiornamento alle cartelle di lavoro 1.2.1 o successive

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Renderer SkiaSharp mancanti nelle cartelle di lavoro 1,3

A partire dalle cartelle di lavoro 1,3, sono stati rimossi i renderer SkiaSharp forniti nelle cartelle di lavoro di 0.99.0, a favore di SkiaSharp che forniscono i renderer stessi usando l' [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Soluzione alternativa

- Aggiornare SkiaSharp alla versione più recente in NuGet. Al momento della stesura di questo documento, questo è 1.57.1.

## <a name="related-links"></a>Collegamenti correlati

- [Segnalazione di bug](~/tools/workbooks/install.md#reporting-bugs)
