---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353269"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocità di sensore](xref:Xamarin.Essentials.SensorSpeed)

- **Più veloce** : ottenere i dati dei sensori più velocemente possibile (non garantisce la restituzione nel thread dell'interfaccia utente).
- **Gioco** – frequenza adatta per i giochi (non garantite la restituzione nel thread dell'interfaccia utente).
- **Normale** – frequenza predefinita adatta per le modifiche dell'orientamento dello schermo.
- **Interfaccia utente** – frequenza appropriata per l'interfaccia utente generale.

Se il gestore eventi non è garantito a essere eseguite sul thread dell'interfaccia utente e se il gestore eventi deve accedere a elementi dell'interfaccia utente, usare il [ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) metodo per eseguire il codice sul thread UI.