BPMN
====

## Activities

Namnges: verb + objekt (ex. ändra status)

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