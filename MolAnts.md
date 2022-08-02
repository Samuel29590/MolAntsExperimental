# MolAntsTimeTravel
 
MolAntsTimeTravel like [MolAnts](https://github.com/Samuel29590/MolAnts) is a simulation of an anthill. 

If you want to know more about the organization of components of [MolAnts](https://github.com/Samuel29590/MolAntsTimeTravel/blob/main/MolAnts.md) or [MolAntExperimental](https://github.com/Samuel29590/MolAntsTimeTravel/blob/main/MolAntsExperimental.md), click on the hyperlink.

### How to start the simulation ?

To start the simulation, go to the *MolAnts* package, on the class side of the MASimulationManager class and launch the start method.

![Screenshot from 2022-07-07 14-48-12](https://user-images.githubusercontent.com/64481702/177777229-fa0a1e0e-b613-44bf-b083-ac8aeafb1914.png)

When the simulation is launched, three windows are opened:

1- Is the simulation.

2- Is the time travel panel.

3- Is an inspector on *MolComponentManager* and it allows to inspect each component of the simulation and their data.

![draw(2)](https://user-images.githubusercontent.com/64481702/178457974-c9afbe47-023f-48c3-868b-82fe07949b91.png)

## Overview of the Components, Types, Services and Events organisation with Molecule

![MolAnts_schema](https://user-images.githubusercontent.com/64481702/171402823-bd2f56b7-c43c-42ca-8568-42426c1a7f29.svg)

<br><br><br><br>

## Simulation

The actual implementation of the *TMASimulationManagerType*: *MASimulationManager*, describe the manager of the MolAnts's system. It aims to put in relation every entity of the system. The simulation manager reference all the entities of the system in ordered collections or dictionaries and can access to all the entities and send them events. This feature permits to do concrete actions as interaction between ants and insects or ants and pheromones.

Futhermore, the simulation manager contains the main loop of the system. This loop send the *simulationStepSend* event message to each entity at each turn.

The simulation manager also implements methods to manage ants and insects (create, update and remove).

Finally this implementation provides *start / pause / play / stop* methods on the class side. Also on the side class the simulation manager provides the simulation data as step duration, width and height of the ground, and ant hill position.

***Variables :***
 
 -*simulationThread*, containing the thread that sends at every step the event: * simulationStepSend*.
 
 -*step*, is an integer that count the number of step.
 
 -*isAntHillCreated*, is a Boolean that is put to true at the creation of the first ant and put to false when ants are removed.
 
 -*ants*, is an ordered collection of ants. It contains all ants of the system. It is used to draw them on the ground and update the info panel.
 
 -*nbAnt*, is an integer incremented at each ant creation. It is used to set the name of each ant component instance.
 
 -*insectsPos*, is a dictionary containing all the insects of the simulation and their position. Key: a point. Value: an ordered collection of insects.
 
 -*nbDelivered*, is an integer that count the number of insect delivered to the anthill.
 
 -*feedingPointsPos*, is a dictionary containing all the feeding points of the simulation and their position. Key: a point. Value: an ordered collection of feeding points.

##### TMASimulationToGlobalEvents

This event is consumed by all the entities of the simulation: ground, ants, insects and pheromones. Thanks to this event, the simulation loop in the simulation thread send at each step of the simulation the *simulationStepSent* message to all the entities. This permit for each entity of the simulation to do their actions. 

This event also provides two messages: *pheromoneNew:for:* and *pheromoneReturnNew:for*, that needs to be implemented by the pheromone component. This two messages notify the Pheromone component of a pheromone creation or update.

##### TMASimulationServices

This service is used by the ground and the ants. It describes methods that the simulation need to implement. Some of these methods permit to get information about the simulation. As all alive ants, insects positions and all pheromones positions and data. These data are used in the different implementations of ants to do consistent actions and in the ground to draw entities.

This service also describe two methods that the simulation need to implement: *start/stop*. And a variable: *isRunning* that the simulation implementation needs to keep up-to-date.

***Variables :***
 
 -*isRunning*, is a Boolean that is set to true when the simulation is playing and set to false when it is paused or stopped.
 
<br><br><br><br>

## Insects

![MolAnts_schemas_insects](https://user-images.githubusercontent.com/64481702/171165760-29ceb48f-96c5-45a2-adcc-97ad8f5608b4.svg)

The actual implementation of the *TMAInsectsType*: *MAInsect*, describe one insect. It means that if there are 50 insects on the simulation, there are also 50 instances of *MAInsects*. This implementation is very simple, insect only move on the ground randomly (waiting to be eaten...) at each step of the simulation.

***Variables :***

 -*position*, is a point.
 
 -*ageInMs*, is an integer.
 
##### TMAInsectEvents

Insects provide this event that is consumed by the simulation manager. This event describe one method *positionChnagedFor:oldPos:newPos:* that aim to be implemented by the simulation manager to keep the dictionary of insect positions up-to-date.

<br><br><br><br>

## Feeding Points

![MolAnts_schemas_feedingpoints](https://user-images.githubusercontent.com/64481702/171404721-dc6506cd-e2c5-4aab-83d2-cbfec7dcd301.svg)

The actual implementation of the *TMAFeedingPointType*: *MAFeedingPoint*, describe one feeding point. This implementation is very simple, feeding point only receive eated message. This message decrease their capacity and when their capacity is equal to 0 the feeding point disappear from the ground.

***Variables :***

 -*position*, is a point.
 
 -*capacity*, is an integer.
 
##### TMAFeedingPointServices

Feeding points provide this event that is consumed by the simulation manager. This event describe one methods that aim to be implemented by feeding points.

<br><br><br><br>

## Pheromones

![MolAnts_schemas_pheromone](https://user-images.githubusercontent.com/64481702/171165778-3d8cc310-27d3-4d51-befe-d6d0973c37cd.svg)

The actual implementation of the *TMAPheromonesType*: *MAPheromones*, manage all the pheromones of the simulation. Pheromones are stored in two dictionaries, one for exploration's pheromones and one for returning's pheromones. Dictionaries has for key: the position and for value: an association: (an Ant -> an Integer). In this association the ant represent the last ant that visits the pheromone and the integer represent the intensity of the pheromone.

Pheromones are created by ants when they are moving on the ground and if an ant moves on a position where a pheromone already exists the pheromone is only update (intensity set to maximum). And at each step of the simulation each pheromone intensity is decreased by one.

***Variables :***

 -*maxIntensity*, is an integer representing the intensity maximum of the pheromone.
 
 -*pheromonesDict*, is a dictionary containing every pheromones of exploration. Key: a point. Value: an association (ant->intensity).
 
 -*pheromonesReturnDict*, is a dictionary containing every pheromones of return. Key: a point. Value: an association (ant->intensity).
 
##### TMAPheromoneServices

This service is provided by the pheromone implementation and used by the ground and the simulation manager. It provides two methods to retrieve the pheromones dictionaries. This aim to draw pheromones on the canvas and to detect if an ant is around pheromones.

<br><br><br><br>

## Ants

![MolAnts_schemas_ants](https://user-images.githubusercontent.com/64481702/171402674-17d18dc3-9129-4e29-b733-0d062aa8ba65.svg)

The actual implementation of the *TMAAntsType*: *MAAnt*, describe one ant. For each ant created there is also one *MAStage* component and one *MARole* component created. There are associate through their component name (example: ***Ant29***, ***StageAnt29*** and ***RoleStageAnt29***). The stage of the ant is created by the ant and the role is created by the stage. The implementation of the ant *MAAnt* permit to increase the age of the ant and to send to the stage the order to do the simulation step and to move.

***Variables :***

 -*position*, is a point.
 
 -*ageInMs*, is an integer.
 
##### TMAAntServices

This service is provided by the ant implementation isn't used by another component. However, it permits to describe methods that the ant implementation need to provide.

***Variables :***

 -*stage*, containing the stage associate to the ant. It is used to access to the correct stage and do actions.

<br><br><br><br>

## Stages

![MolAnts_schemas_stage](https://user-images.githubusercontent.com/64481702/171165851-7628da8d-b27b-48c8-94f6-c1789841fe73.svg)

The actual implementation of the *TMAStageType*: *MAStage*, describe the stage of one ant. The stage manages the life cycle of an ant, it determines when an ant is an egg, an adult or when the ant is dead. It also sends to the role the order to do the simulation step and to move.

***Variables :***

 -*eggStateDurationInMs*, is an integer to determine the duration in ms of the egg state of the ant.
 
 -*adultStateDurationInMs*, is an integer to determine the duration in ms of the adult state of the ant.
 
##### TMAStageServices

This service is provided by the stage implementation and used by the ant implementation. It describes methods that the stage needs to implement because the ant implementation needs it to work correctly.

***Variables :***

 -*stageName*, is an symbol used to know the actual stage of the ant: *egg* or *adult*.
 
 -*role*, containing the role associate to the stage. It is used to access to the correct role to do actions and to stop the role component correctly.

##### TMAStageEvents

Stages provide this event that is consumed by the simulation manager. This event describe one method *stageChanged:for:* that aim to be implemented by the simulation manager to remove, from the ordered collection *ants*, ants that are dead.

<br><br><br><br>

## Roles

![MolAnts_schemas_role](https://user-images.githubusercontent.com/64481702/171402730-15749d16-d99e-4af8-a9be-c2f97e936e1a.svg)

The actual implementation of the *TMARoleeType*: *MARole*, describe the role of one stage. The role provides the behavior of an ant at each step. Actually roles are implemented by *MAQueen*, *MAFighter* or *MAWorker* that are subclass of *MARole*. In each different implementation there is one method: *doSimulationStepFor:* that manage the action of the ant and one method: *moveFrom:* that manage the position of the ant at each step.

***MAWorker variables :***

 -*phase*, is a symbol to represent the actual phase of the ant: *explore* of *return*.
 
 -*oldPositions*, is and ordered collection of 5 points used by the worker role do not move on the last 5 position the worker already visit.
 
##### TMARolesServices

This service is provided by the role implementation and used by the stage. It describes methods that the role needs to implement because the stage implementation needs it to work correctly.

***Variables :***

 -*roleName*, is a symbol used to know the role of an ant: *queen*, *fighter* or *worker*.

##### TMARolesEvents

Roles provide this event that is consumed by the simulation manager. This event describes methods that aim to make the actions of the ants concrete in the system.

<br><br><br><br>

## Ground

![MolAnts_schemas_ground](https://user-images.githubusercontent.com/64481702/171402759-0598396b-93bf-4a71-aaa4-8a25b5c334e9.svg)

The actual implementation of the *TMAGroundType*: *MAGround* is the implementation of the system's view. It permits to draw all the entities of the system on a canvas and to keep the information panel up-to-date. It also permits to have a functional interface with buttons to start, pause and stop the system and also buttons to change the refresh rate.


***Variables :***

 -*mySpace*, containing the *BlSpace* object.
 
 -*refreshing*, is an integer used to know the actual refresh rate of the view.
 
 -*stepView*, is a *BlTextElement* used to print the actual step on the view.
 
 -*nbInsectsView*, is a *BlTextElement* used to print the actual number of insects on the view.
 
 -*nbDeliveredView*, is a *BlTextElement* used to print the actual number of insects delivered on the view.
 
  -*nbPheromonesView*, is a *BlTextElement* used to print the actual number of pheromones on the view.
 
 -*nbEggsView*, is a *BlTextElement* used to print the actual number of eggs on the view.
 
 -*nbAntView*, is a *BlTextElement* used to print the actual number of ants on the view.
 
 -*nbFeedingPointView*, is a *BlTextElement* used to print the actual number of feedingPoints on the view.
