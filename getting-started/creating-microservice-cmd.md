# Guidelines for creating a new command microservice using CQRS in spring boot.

## Naming Conventions
Project names should be all lower-case with dashes instead of spaces. Project should not begin with numbers or symbols or contain symbols. Convention for these
microsevices is projectname-cmd.
Examples:
Passenger commands => passenger-cmd

## Project Structure
In general a cmd project will have the following package structure.
* domain
  * aggregates
  * commands
  * models

  Domain also holds the CommandHandler class.
* service
  * configuration
  * controllers

Using the aist eventsourcing configuration library.
## Domain
Example of the main command handler.
```java
public class TripCommandHandler extends CustomCommandHandler<Trip> {
    public TripCommandHandler(EventSourcingRepository repository, CommandBus commandBus) {
        super(repository, commandBus, Trip.class);
    }
}
```
### Aggregates
* The Aggregate for the targets of this microservice.
  * Needed Atributes
  * EventSourcingHandler methods for the events that affect this aggregate.

Part of an example aggregate, it would contain other things like getters and other EventSourcingHandler
for other events. The create command should be here and return an instance of the aggregate.
```java
@Aggregate
public class Trip {
    @AggregateIdentifier
    private UUID id;
    private String originAddress;
    private String destinationAddress;
    private TripStatus status;
    @AggregateMember
    private CommandHandlers commandHandlers = new CommandHandlers();

    @CommandHandler
    public Trip(CreateTripCommand createTripCommand){
        apply(new TripCreatedEvent(createTripCommand.getId(), createTripCommand.getUserId(),
                createTripCommand.getOriginAddress(), createTripCommand.getDestinationAddress()));
    }

    @EventSourcingHandler
    public void on(TripCreatedEvent tripCreatedEvent) {
        id = tripCreatedEvent.getId();
        originAddress = tripCreatedEvent.getOriginAddress();
        destinationAddress = tripCreatedEvent.getDestinationAddress();
        userId = tripCreatedEvent.getUserId();
        status = TripStatus.CREATED;
    }

    @EventSourcingHandler
    public void on(TripCanceledEvent tripCanceledEvent) {
        status = TripStatus.CANCELED;
    }
}
```
* The commmand handlers if they will be located outside of the aggregate class.

Example of the command handlers located on an external class
```java
import static org.axonframework.commandhandling.model.AggregateLifecycle.apply;

public class CommandHandlers {
    @CommandHandler
    public void on(CancelTripCommand cancelTripCommand) {
        apply(new TripCanceledEvent((cancelTripCommand.getId())));
    }

    @CommandHandler
    public void on(StartTripCommand startTripCommand) {
        apply(new TripStartedEvent((startTripCommand.getId())));
    }

    @CommandHandler
    public void on(CompleteTripCommand completeTripCommand) {
        apply(new TripCompletedEvent((completeTripCommand.getId())));
    }
}

```
### Commands
* Command class for each of the commands for the aggregates.

Create commands usually initialize every needed part of the aggregate.
```java
public class CreateTripCommand {
    @TargetAggregateIdentifier          
    private UUID id;
    private UUID userId;
    private String originAddress;
    private String destinationAddress;

    public CreateTripCommand(UUID userId, String originAddress, String destinationAddress){
        this.id = UUID.randomUUID();
        this.userId = userId;
        this.originAddress = originAddress;
        this.destinationAddress = destinationAddress;
    }

    public UUID getId() {
        return id;
    }

    public UUID getUserId() {
        return userId;
    }

    public String getOriginAddress() {
        return originAddress;
    }

    public String getDestinationAddress() {
        return destinationAddress;
    }
}
```
While other commands can be much simpler.
```java
public class CancelTripCommand {
    @TargetAggregateIdentifier
    private UUID id;
    public CancelTripCommand(UUID id) {
        this.id = id;
    }

    public UUID getId() {
        return id;
    }
}
```
### Models
* Any additional model to be used by the project.
* This package is optional if nothing else is needed.
## Service
### Configuration.
* Event store configuration.
```java
@Configuration
public class TripEventStoreConfiguration extends AmqpEventPublisherConfiguration<Trip, TripCommandHandler> {
    public TripEventStoreConfiguration() {
        super(Trip.class);
    }

    @Override
    @Bean
    public TripCommandHandler commandHandler(EventSourcingRepository eventSourcingRepository, CommandBus commandBus) {
        TripCommandHandler commandHandler = new TripCommandHandler(eventSourcingRepository, commandBus);
        commandHandler.subscribe();
        return commandHandler;
    }
}
```
### Controllers
Using REST api:
* POST to create.
* PUT to update.
* Path variables when posible.
```java
@Controller
@RequestMapping("api")
public class TripCmdController {
    @Autowired
    private CommandGateway commandGateway;

    @PostMapping("trip")
    public void addTrip(@RequestBody TripDto trip){
        commandGateway.send(
                new CreateTripCommand(trip.getUserId(), trip.getOriginAddress(), trip.getDestinationAddress()));
    }

    @PutMapping("trip/cancel/{id}")
    public void cancelTrip(@PathVariable("id") UUID id){
        commandGateway.send(new CancelTripCommand(id));
    }

    @PutMapping("trip/start/{id}")
    public void startTrip(@PathVariable("id") UUID id){
        commandGateway.send(new StartTripCommand(id));
    }

    @PutMapping("trip/completed/{id}")
    public void completeTrip(@PathVariable("id") UUID id){
        commandGateway.send(new CompleteTripCommand(id));
    }
}
```
