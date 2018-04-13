# Guidelines for creating a new query microservice using CQRS in spring boot.

## Naming Conventions
Project names should be all lower-case with dashes instead of spaces. Project should not begin with numbers or symbols or contain symbols. Convention for these
microsevices is projectname-query.
Examples:
Trip query => passenger-query

## Project Structure
In general a cmd project will have the following package structure.
* domain
  * eventhandlers
  * models

  Domain also holds the CommandHandler class.
* service
  * configuration
  * controllers
  * repositories
  
Using the aist eventsourcing configuration library.

## Domain
### eventhandlers
* Add required event handlers.
```java
@Component
public class TripEventHandler {
    protected static final Logger LOG = LoggerFactory.getLogger(TripEventHandler.class);

    @Autowired
    private TripRepository tripRepository;

    public TripEventHandler() {}

    @EventHandler
    public void on(TripCreatedEvent event){
        LOG.info("Trip created: ", event.getId());
        tripRepository.save(new Trip(event.getId(), event.getUserId(),
                event.getOriginAddress(), event.getDestinationAddress(), TripStatus.CREATED));
    }

    @EventHandler
    public void on(TripCanceledEvent event){
        LOG.info("Trip canceled: ", event.getId());
        Trip trip = tripRepository.findOne(event.getId());
        trip.cancelTrip();
        tripRepository.save(trip);
    }
    ...
}

```
### models
* Model needed for the repository.
```java
public class Trip {
    @NotNull
    private UUID id;
    private UUID userID;
    private String originAddress;
    private String destinationAddress;

    private TripStatus status;

    public Trip(UUID id, UUID userID, String originAddress, String destinationAddress, TripStatus status) {
        this.id = id;
        this.userID = userID;
        this.originAddress = originAddress;
        this.destinationAddress = destinationAddress;
        this.status = status;
    }
    public UUID getId() {
        return id;
    }

```
## services
### eventhandlers
```java
@Configuration
public class TripManagementEventSubscriptionConfiguration  extends AmqpEventSubscriptionConfiguration {
}
```

And on the amplication.yml file add:
```yml
amqp:
  events:
    host: ${RABBIT_HOST:localhost}
    exchange-name: eventBus
    queue-name: eventQueue
    handlers: org.aitesting.microservices.tripmanagement.query.domain.eventhandlers
```
Where the ```handlers:``` points to the eventhandler for the model and eventBus and eventQueue are the corresponding names(if they are different).

### controllers
```java
@Controller
@RequestMapping("api")
public class TripQueryController {
    @Autowired
    private TripRepository tripRepository;

    @GetMapping("trips")
    public @ResponseBody
    List<Trip> getTrips() {
        return tripRepository.findAll();
    }

    @GetMapping("trip/{id}")
    public @ResponseBody Trip getTrip(@PathVariable("id") UUID id) {
        return tripRepository.findOne(id);
    }
}
```
### repositories
```java
public interface TripRepository  extends MongoRepository<Trip, UUID>{
}
```
