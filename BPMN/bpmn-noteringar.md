BPMN
====

# Allmänt

## Process instance
En körning av en process. En process är inte klar förrän alla tokens konsumerats.

# Symboler

## Activities

Namnges: verb + objekt (ex. ändra status)

### Task types
Små ikoner i övre vänstra hörnet som beskriver aktiviteten.
- Manual - arbete som utförs av en människa och som inte påverkar färdigställandet av en uppgift tilldelad av workflow engine
- User - arbete som utförs av människa, tilldelat av workflow engine, och den förväntar sig oftast ett svar.
- Service - oftast webservice
- Receive and send - oftast asynkron kommunikation via message queue
- Business
- Script
- Custom

### Markers
Ikon i mitten på nedre delen av aktiviteten. Flera markers kan kombineras (ex. compensation och multiple).
- Loopar
- Multiple - repetera task x ggr; seriellt eller parallelt. Genererar inte fler tokens.
- Compensation - se compensation event nedan.

### Global task
Kan refereras till.

Modelleras som en vanlig activity med tjockare ram.

### Sub processes
Tillhör precis som tasks klasselementet activity.
Man kan ha hur många nivåer av subprocesses som helst.
När ett token kommer till en subprocess, så händer följande:
- Parent token stannar.
- Nytt subprocess token skapas.
- När subprocess token har nåt ett end event, så konsumeras det och parent token går vidare (är klar med tasken).

En subprocess kan vara:
- embedded. Har tillgång till samma data som huvudprocessen. Får inte användas av andra processer. Får inte ha några egna lanes. Får bara ha ett start none event.
- global. Kan återanvändas. Delar bara data med huvudprocessen om så explicit har deklarerats. Är helt fristående som diagram räknat.

En subprocess (parent activitysymbolen) kan ha attached events. Om dessa events är av typen message, timer eller conditional, så avbryts alltid subprocessen. Om eventet istället är av typen error, cancel eller escalation, så har de en throwing motsvarighet i subprocessen och då rapporteras dessa event till huvudprocessen.

Modelleras med ett plus-tecken i mitten längs bottenkanten av activityn. 

#### Kommunikation
Kommunikation mellan parent och subprocess sker inte via meddelanden utan via escalation events.

#### Markers
Samma som för tasks + ad hoc marker. Ad hoc marker representeras av ett tilde och markerar att:
- De aktiviteter som ingår i subprocessen kan exekveras i godtycklig ordning
- flera gånger
- eller överhoppade

#### Transactions
Används i kombination med cancel events. Cancel events kan bara användas i transactions.

## Sequence flows
Beskriver token-paths.

## Events

Namnges: object + verb (ex. status ändrad)

### None events
Används i ett processflöde för att indikera en status eller en milstolpe
Exempel: Start event, slutevent och mellanevent som indikerar en särskild status (viktig nog att modellera).

### Event, subklasser
Catching and throwing events

Exempel
- Start events är catching events. Indikerar att processen väntar på något (utanför processen) som processen kan reagera på.
- Intermediate events. Kan vara både fångande och kastande. Ett intermediate none event är alltid kastande (applikationen indikerar med none-eventet att något har hänt - andra processer kan lyssna till detta om så önskas).
- End events. Kan bara triggas av processen.

### Attached events
Man kan koppla på ett (catching) event på en activity (task). Man ritar då en cirkel som överlappar activityn (i nederkanten). Om eventet inträffar medan tasken (activityn) pågår, så flyttar tokenet till event-pathen och kör vidare där. Ett attached event kan vara non-interupting också (dashed circle) varpå tokenet klonas om eventet inträffar. Inträffar eventet efter det att tasken har körts klart, så händer inget (det gäller alla attached events).

### Message Events
Konvention: Använd tasks för att skicka meddelanden och events för att fånga meddelanden. Detta för att undvika upprepning i modellen (ett skicka task + ett throwing event som indikerar att man skickat något - då skickar man ju två gånger) och det blir otydligt i modellen om man utan att ha modelerat ett task för det implicerar att man skickat ett meddelande via endast ett intermediate throwing event.

Modelleras med ett brev i eventcirkeln.

### Timer events
Självförklarande. Kan bara existera som catching start eller intermediate events. Kan vara interrupting eller non interrupting.

Modelleras med en klocka i eventcirkeln.

### Error events
Kan bara modelleras som ett attached intermediate event och end-event (throw error event i subprocess och catch som intermediate event på huvudprocess).

Modelleras med en blixt i eventcirkeln.

### Conditional
Alltid catching. Kan användas på start, attached och intermediate. Man beskriver, på eventet, ett kriterium för catching eventet.

Modelleras med en lista i eventcirkeln.

### Signal
Funkar som meddelande men är broadcast istället för meddelande till enskild mottagare.

Modelleras med en triangel i eventcirkeln.

### Terminate event
Konsumerar omedelbart alla tokens i processen. Kan bara förekomma som end-event.

Modelleras med en fylld eventcirkel.

### Link event
Används för att dela upp långa pathsekvenser (t.ex. om man behöver flera sidor för att rita upp sin process). Kan bara existera som intermediate events.

Modelleras med en pil i eventcirkeln.

### Compensation
Transaction, rollback. Utgörs av ett attached event och throwing intermediate eller end events. Compensation throwing events kan bara påverka inom processen; när ett compensationevent kastas, så triggas alla attached compensation events.

Modelleras med snabbspolning bakåt-pilar i eventcirkeln. Kopplas med association till ett compensation task (inte ett sequence flow).

### Multiple
Slå samman flera events. För catching räcker det med att ett av eventen har inträffat. För throwing så genereras alla eventen.

Modelleras med en hexagon i eventcirkeln.

### Event gateway
XOR gateway för events.
Kombineras med ett direkt efterföljande intermediate event (message, timer, condition, signal, multiple) eller receive task.

Modelleras med en hexagon i en gate-symbol

Det finns även en parallel event gateway; AND-gate istället för XOR-gate.

### Other
- cancel
- parallel
- escalation

## Gateway

- XOR, exclusiveOR, split (en väg ett token), merge (en för varje token) 
- Parallel, split (skapa flera tokens), merge (väntar in alla tokens)
- OR, databased inclusive gateway, split (multipla tokens, en för varje uppfyllt val) merge (inväntar alla tokens som har skapats - tricky - tokens kan försvinna längs vägen; t.ex. nå ett end state varvid mergen väntar 4ever).
- Complex gateway, man skriver in vilka kriterier som gäller för gatewayen i diagrammet (ex. ta det token som kommer först och gå vidare med det - och endast det). Framförallt meningsfull vid merges.

## Default flow
Markeras med ett litet snedstreck på förbindelsen. Detta val väljs om inget annat val är uppfyllt. Hindrar att tokenet fastnar.

## Paths without gateways
XOR, OR och and kan modelleras utan gateways. Det kan t.ex. vara bra att modellera enkla loopar utan en XOR-gateway och tänk på att det är tillåtet att ha flera vägar ut från ett startevent (funkar som AND-gate, multipla tokens genereras) och detsamma gäller flera vägar in till end-event (funkar som XOR-gate).

## Lanes
Olika aktörer i en process. Kan vara människor, organisationer, it-system, etc. Tänk på att man kan ha multipla end-event (i olika lanes) om så behövs för modellen.

Observera - Flödeselement (exempelvis activity) får inte korsa flera lanes.
