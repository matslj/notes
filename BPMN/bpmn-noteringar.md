BPMN
====

# Allm�nt

## Process instance
En k�rning av en process. En process �r inte klar f�rr�n alla tokens konsumerats.

# Symboler

## Activities

Namnges: verb + objekt (ex. �ndra status)

### Task types
Sm� ikoner i �vre v�nstra h�rnet som beskriver aktiviteten.
- Manual - arbete som utf�rs av en m�nniska och som inte p�verkar f�rdigst�llandet av en uppgift tilldelad av workflow engine
- User - arbete som utf�rs av m�nniska, tilldelat av workflow engine, och den f�rv�ntar sig oftast ett svar.
- Service - oftast webservice
- Receive and send - oftast asynkron kommunikation via message queue
- Business
- Script
- Custom

### Markers
Ikon i mitten p� nedre delen av aktiviteten. Flera markers kan kombineras (ex. compensation och multiple).
- Loopar
- Multiple - repetera task x ggr; seriellt eller parallelt. Genererar inte fler tokens.
- Compensation - se compensation event nedan.

### Global task
Kan refereras till.

Modelleras som en vanlig activity med tjockare ram.

### Sub processes
Tillh�r precis som tasks klasselementet activity.
Man kan ha hur m�nga niv�er av subprocesses som helst.
N�r ett token kommer till en subprocess, s� h�nder f�ljande:
- Parent token stannar.
- Nytt subprocess token skapas.
- N�r subprocess token har n�t ett end event, s� konsumeras det och parent token g�r vidare (�r klar med tasken).

En subprocess kan vara:
- embedded. Har tillg�ng till samma data som huvudprocessen. F�r inte anv�ndas av andra processer. F�r inte ha n�gra egna lanes. F�r bara ha ett start none event.
- global. Kan �teranv�ndas. Delar bara data med huvudprocessen om s� explicit har deklarerats. �r helt frist�ende som diagram r�knat.

En subprocess (parent activitysymbolen) kan ha attached events. Om dessa events �r av typen message, timer eller conditional, s� avbryts alltid subprocessen. Om eventet ist�llet �r av typen error, cancel eller escalation, s� har de en throwing motsvarighet i subprocessen och d� rapporteras dessa event till huvudprocessen.

Modelleras med ett plus-tecken i mitten l�ngs bottenkanten av activityn. 

#### Kommunikation
Kommunikation mellan parent och subprocess sker inte via meddelanden utan via escalation events.

#### Markers
Samma som f�r tasks + ad hoc marker. Ad hoc marker representeras av ett tilde och markerar att:
- De aktiviteter som ing�r i subprocessen kan exekveras i godtycklig ordning
- flera g�nger
- eller �verhoppade

#### Transactions
Anv�nds i kombination med cancel events. Cancel events kan bara anv�ndas i transactions.

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

### Compensation
Transaction, rollback. Utg�rs av ett attached event och throwing intermediate eller end events. Compensation throwing events kan bara p�verka inom processen; n�r ett compensationevent kastas, s� triggas alla attached compensation events.

Modelleras med snabbspolning bak�t-pilar i eventcirkeln. Kopplas med association till ett compensation task (inte ett sequence flow).

### Multiple
Sl� samman flera events. F�r catching r�cker det med att ett av eventen har intr�ffat. F�r throwing s� genereras alla eventen.

Modelleras med en hexagon i eventcirkeln.

### Event gateway
XOR gateway f�r events.
Kombineras med ett direkt efterf�ljande intermediate event (message, timer, condition, signal, multiple) eller receive task.

Modelleras med en hexagon i en gate-symbol

Det finns �ven en parallel event gateway; AND-gate ist�llet f�r XOR-gate.

### Other
- cancel
- parallel
- escalation

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
