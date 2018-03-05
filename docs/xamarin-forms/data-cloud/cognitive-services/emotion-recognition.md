---
title: Riconoscimento emozioni utilizzando l'API emozioni
description: L'API emozioni accetta un'espressione facciale in un'immagine come un input e restituisce i livelli di confidenza in un set di emozioni per ogni tipo di carattere nell'immagine. In questo articolo viene illustrato come utilizzare l'API emozioni per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 159bd1b23eb7505c5d5629570a34d54e0525567e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="emotion-recognition-using-the-emotion-api"></a>Riconoscimento emozioni utilizzando l'API emozioni

_L'API emozioni accetta un'espressione facciale in un'immagine come un input e restituisce i livelli di confidenza in un set di emozioni per ogni tipo di carattere nell'immagine. In questo articolo viene illustrato come utilizzare l'API emozioni per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms._

![](~/media/shared/preview.png "Questa API è attualmente pre-release.")

> [!NOTE]
> L'API emozioni è ancora in anteprima. Potrebbe essere ultime modifiche all'API prima della versione finale.

## <a name="overview"></a>Panoramica

L'API emozioni consente di rilevare anger, guidiziaria, pretendere una risposta immediata, paura, "Happiness", neutro, sadness e categoria Sorpresa, in un'espressione facciale. Questi emozioni vengono comunicati universalmente e visualizzarli tramite le stesse espressioni facciale base. Oltre a restituire un risultato emozioni per un'espressione del viso, l'API emozioni restituisce anche un rettangolo di selezione per i caratteri tipografici rilevati tramite l'API viso. Se un utente ha già chiamata l'API viso, può inviare il rettangolo faccia come un input facoltativo. Si noti che è necessario ottenere una chiave API per utilizzare l'API emozioni. Può essere ottenuto in [introduzione gratuitamente](https://www.microsoft.com/cognitive-services/sign-up) sul sito Web microsoft.com.

Riconoscimento emozioni può essere eseguito tramite una libreria client e tramite un'API REST. Questo articolo è incentrato sull'esecuzione di riconoscimento emozioni tramite il [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/) libreria client, che può essere scaricata da NuGet.

L'API emozioni consente inoltre di riconoscere le espressioni del viso di persone nel video e restituisce un riepilogo delle loro emozioni. Per ulteriori informazioni, vedere [emozioni video](https://www.microsoft.com/cognitive-services/emotion-api/documentation#emotion-in-video) sul sito Web microsoft.com.

Per ulteriori informazioni sull'API emozioni, vedere [documentazione dell'API emozioni](https://www.microsoft.com/cognitive-services/emotion-api/documentation) sul sito Web microsoft.com.

## <a name="performing-emotion-recognition"></a>Esecuzione di riconoscimento emozioni

Riconoscimento emozioni scopo è necessario caricare un flusso di immagine per l'API emozioni. Le dimensioni del file di immagine non devono essere superiori a 4MB e formati di file supportati sono BMP, PNG, GIF e JPEG. All'interno dell'immagine, l'intervallo di dimensioni faccia rilevabile è 36 x 36 a 4096 x 4096 pixel. Non è possibile rilevare eventuali caratteri tipografici fuori da questo intervallo.

Esempio di codice seguente viene illustrato il processo di riconoscimento emozioni:

```csharp
using Microsoft.ProjectOxford.Emotion;
using Microsoft.ProjectOxford.Emotion.Contract;

var emotionClient = new EmotionServiceClient(Constants.EmotionApiKey);

using (var photoStream = photo.GetStream())
{
  Emotion[] emotionResult = await emotionClient.RecognizeAsync(photoStream);
  if (emotionResult.Any())
  {
    // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
    emotionResultLabel.Text = emotionResult.FirstOrDefault().Scores.ToRankedList().FirstOrDefault().Key;
  }
  // Store emotion as app rating
  ...
}
```

Un `EmotionServiceClient` per eseguire il riconoscimento emozioni, con la chiave API emozioni passata come argomento, è necessario creare l'istanza di `EmotionServiceClient` costruttore.

Il `RecognizeAsync` metodo, che viene richiamato su di `EmotionServiceClient` dell'istanza, carica un'immagine per l'API emozioni, come un `Stream`. La chiave API verrà inviata all'API emozioni quando viene richiamata l'operazione. Un errore di invio di una chiave API valida comporterà un `Microsoft.ProjectOxford.Common.ClientException` generata, con il messaggio di eccezione che indica che è stata inviata una chiave API non valida.

Il `RecognizeAsync` metodo restituirà un `Emotion` matrice, a condizione che un tipo di carattere è stata riconosciuta. Per ogni immagine, il numero massimo di caratteri tipografici che possono essere rilevate è 64 e le facce vengono classificate in base dimensioni del rettangolo faccia in ordine decrescente. Se non viene rilevato alcun tipo di carattere, un oggetto vuoto `Emotion` matrice verrà restituita.

Quando si interpretano i risultati dall'API emozioni, l'emozioni rilevata devono essere interpretate come l'emozioni con il punteggio più elevato, come i punteggi vengono normalizzati somma a uno. Di conseguenza, l'applicazione di esempio visualizza l'emozioni riconosciuta con il punteggio più elevato per il tipo di carattere rilevato più grande dell'immagine, come illustrato nelle schermate seguenti:

![](emotion-recognition-images/emotion-recognition.png "Riconoscimento emozioni")

## <a name="summary"></a>Riepilogo

In questo articolo viene spiegato come utilizzare l'API emozioni per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms. L'API emozioni accetta un'espressione facciale in un'immagine come un input e restituisce il livello di confidenza in un set di emozioni per ogni tipo di carattere nell'immagine.


## <a name="related-links"></a>Collegamenti correlati

- [API emozioni documentazione](https://www.microsoft.com/cognitive-services/emotion-api/documentation)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)
- [API emozioni](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)
