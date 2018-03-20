BPMN
====

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