---
title: " Xamarin.Forms gestures" Description: "questa guida illustra come Xamarin.Forms possono essere usati i riconoscitori di movimento per rilevare l'interazione dell'utente con le visualizzazioni in un' Xamarin.Forms applicazione".
ms. prod: Novell MS. AssetID: 0E197A51-2304-4C09-A710-C7FF24A89F15 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/25/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-gestures"></a>Xamarin.Formsgesti

_I riconoscitori dei movimenti possono essere utilizzati per rilevare l'interazione dell'utente con le visualizzazioni in un' Xamarin.Forms applicazione._

La Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) classe supporta i movimenti Tap, Pinch, Pan e swipe nelle [`View`](xref:Xamarin.Forms.View) istanze di.

## <a name="adding-a-tap-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del gesto tocco](tap.md)

Viene usato un movimento tap per il rilevamento del tocco e viene riconosciuto con la [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) classe.

## <a name="adding-a-pinch-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del movimento di avvicinamento delle dita](pinch.md)

Viene usato un movimento di pizzico per eseguire lo zoom interattivo e viene riconosciuto con la [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) classe.

## <a name="adding-a-pan-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del movimento di panoramica](pan.md)

Viene usato un movimento di panoramica per rilevare lo spostamento delle dita intorno allo schermo e per applicare tale movimento al contenuto e viene riconosciuto con la [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) classe.

## <a name="adding-a-swipe-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido](swipe.md)

Il movimento di scorrimento rapido si verifica quando si muove un dito sullo schermo in senso orizzontale o verticale e viene spesso usato per avviare la navigazione nel contenuto. I movimenti di scorrimento vengono riconosciuti con la [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe.
