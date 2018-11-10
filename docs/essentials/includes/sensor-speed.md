---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 28dbb5fa2fbcc7e66cd09c1d0077496fcdefac56
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51210600"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocità del sensore](xref:Xamarin.Essentials.SensorSpeed)

- **Fastest** - Ottenere i dati del sensore il più velocemente possibile (non è garantita la restituzione sul thread dell'interfaccia utente).
- **Game** - Velocità adatta ai giochi (non è garantita la restituzione sul thread dell'interfaccia utente).
- **Normal** - Velocità predefinita adatta per le modifiche dell'orientamento dello schermo.
- **UI** - Velocità adatta per l'interfaccia utente generale.

Se non è garantito che il gestore eventi venga eseguito sul thread dell'interfaccia utente e se il gestore eventi deve accedere a elementi dell'interfaccia utente, usare il metodo [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) per eseguire il codice sul thread dell'interfaccia utente.