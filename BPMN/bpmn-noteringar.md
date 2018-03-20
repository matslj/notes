BPMN
====

# Allm�nt

## Process instance
En k�rning av en process. En process �r inte klar f�rr�n alla tokens konsumerats.

# Symboler

## Activities

Namnges: verb + objekt (ex. �ndra status)

## Sequence flows
Beskriver token-paths.

## Events

Namnges: object + verb (ex. status �ndrad)

### None events
Anv�nds i ett processfl�de f�r att indikera en status eller en milstolpe
Exempel: Start event, slutevent och mellanevent som indikerar en s�rskild status (viktig nog att modellera).

### Event, subklasser
Catching and throwing events

Exempel
- Start events �r catching events. Indikerar att processen v�ntar p� n�got (utanf�r processen) som processen kan reagera p�.
- Intermediate events. Kan vara b�de f�ngande och kastande. Ett intermediate none event �r alltid kastande (applikationen indikerar med none-eventet att n�got har h�nt - andra processer kan lyssna till detta om s� �nskas).
- End events. Kan bara triggas av processen.

### Attached events
Man kan koppla p� ett (catching) event p� en activity (task). Man ritar d� en cirkel som �verlappar activityn (i nederkanten). Om eventet intr�ffar medan tasken (activityn) p�g�r, s� flyttar tokenet till event-pathen och k�r vidare d�r. Ett attached event kan vara non-interupting ocks� (dashed circle) varp� tokenet klonas om eventet intr�ffar. Intr�ffar eventet efter det att tasken har k�rts klart, s� h�nder inget (det g�ller alla attached events).

### Message Events
Konvention: Anv�nd tasks f�r att skicka meddelanden och events f�r att f�nga meddelanden. Detta f�r att undvika upprepning i modellen (ett skicka task + ett throwing event som indikerar att man skickat n�got - d� skickar man ju tv� g�nger) och det blir otydligt i modellen om man utan att ha modelerat ett task f�r det implicerar att man skickat ett meddelande via endast ett intermediate throwing event.

Modelleras med ett brev i eventcirkeln.

### Timer events
Sj�lvf�rklarande. Kan bara existera som catching start eller intermediate events. Kan vara interrupting eller non interrupting.

Modelleras med en klocka i eventcirkeln.

### Error events
Kan bara modelleras som ett attached intermediate event och end-event (throw error event i subprocess och catch som intermediate event p� huvudprocess).

Modelleras med en blixt i eventcirkeln.

### Conditional
Alltid catching. Kan anv�ndas p� start, attached och intermediate. Man beskriver, p� eventet, ett kriterium f�r catching eventet.

Modelleras med en lista i eventcirkeln.

### Signal
Funkar som meddelande men �r broadcast ist�llet f�r meddelande till enskild mottagare.

Modelleras med en triangel i eventcirkeln.

### Terminate event
Konsumerar omedelbart alla tokens i processen. Kan bara f�rekomma som end-event.

Modelleras med en fylld eventcirkel.

### Link event
Anv�nds f�r att dela upp l�nga pathsekvenser (t.ex. om man beh�ver flera sidor f�r att rita upp sin process). Kan bara existera som intermediate events.

Modelleras med en pil i eventcirkeln.

## Gateway

- XOR, exclusiveOR, split (en v�g ett token), merge (en f�r varje token) 
- Parallel, split (skapa flera tokens), merge (v�ntar in alla tokens)
- OR, databased inclusive gateway, split (multipla tokens, en f�r varje uppfyllt val) merge (inv�ntar alla tokens som har skapats - tricky - tokens kan f�rsvinna l�ngs v�gen; t.ex. n� ett end state varvid mergen v�ntar 4ever).
- Complex gateway, man skriver in vilka kriterier som g�ller f�r gatewayen i diagrammet (ex. ta det token som kommer f�rst och g� vidare med det - och endast det). Framf�rallt meningsfull vid merges.

## Default flow
Markeras med ett litet snedstreck p� f�rbindelsen. Detta val v�ljs om inget annat val �r uppfyllt. Hindrar att tokenet fastnar.

## Paths without gateways
XOR, OR och and kan modelleras utan gateways. Det kan t.ex. vara bra att modellera enkla loopar utan en XOR-gateway och t�nk p� att det �r till�tet att ha flera v�gar ut fr�n ett startevent (funkar som AND-gate, multipla tokens genereras) och detsamma g�ller flera v�gar in till end-event (funkar som XOR-gate).

## Lanes
Olika akt�rer i en process. Kan vara m�nniskor, organisationer, it-system, etc. T�nk p� att man kan ha multipla end-event (i olika lanes) om s� beh�vs f�r modellen.

Observera - Fl�deselement (exempelvis activity) f�r inte korsa flera lanes.