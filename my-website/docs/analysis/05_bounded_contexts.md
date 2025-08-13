---
title: Bounded Contexts
sidebar_position: 5
---

# **Bounded Contexts**
The Event Storming session revealed the following bounded contexts:

- **User**: responsible for managing the user account information, including authentication and authorization.
- **Smart furniture hookup**: responsible for managing the smart furniture hookups information.
- **Map**: responsible for managing the floor plan, zones and smart furniture hookup locations.
- **Monitoring**: responsible for processing and managing raw utility consumption from each physical smart furniture hookup.
- **Forecasting**: responsible for generating and managing forecasts of utility consumptions.
- **Threshold**: responsible for managing and evaluating utility consumption limits that trigger alerts.
- **Alerting**: responsible for emitting the alerts to users when consumption limits are exceeded.


## **User context**

This context is responsible for managing the user account information, including authentication and authorization.
Each account includes a username, password, and role (either admin or household user).
For household users, the username links utility consumption data to a specific account, enabling consumption-based filtering.
The single admin account is created before the platform is launched, along with a reset code, while the household users accounts are created and managed by the admin.
Any users may change their own password at any time; however, only household users can update their username, and only the admin has the ability to reset his own password.

Authentication is handled by validating each user’s credentials (username and password) and includes logout functionality. 
The authorization is handled through a permission system which uses a role model.

### Aggregate Root:
* `User`
  * id: `UserID`
  * password: `string`
  * role: `UserRole`
  * username: `string`

### Value objects:
* `UserID`
* `UserRole`

### Services:
* **AuthService**: Provides functionalities for user authentication, such as registration, login, and logout.
* **UserManagementService**:

### Factories:
* `UserFactory`:
### Repositories:
* `UserRepository`: Manages the persistence and retrieval of `User` aggregates.

### Commands

#### Commands triggered by a user:

| Command          | Description                       |
|------------------|-----------------------------------|
| `LoginUser`      | Validate credentials during login |
| `LogoutUser`     | Logout the user from the platform |
| `ChangePassword` | Update own password               |

#### Commands triggered by the admin:

| Command                          | Description                                               |
|----------------------------------|-----------------------------------------------------------|
| `RegisterHouseholdUserAccount`   | Create a new household user’s account                     |
| `DeleteHouseholdUserAccount`     | Delete the account of an existing household user          |
| `ChangeHouseHoldUserCredentials` | Update name and/or password of an existing household user |
| `ResetPassword`                  | Reset password with a new one                             |

#### Commands triggered by a household user:

| Command          | Description         |
|------------------|---------------------|
| `ChangeUserName` | Update own username |

### Events

#### Outbound events:

| Event                            | Description                                         |
|----------------------------------|-----------------------------------------------------|
| `UserLoggedIn`                   | Emitted when the user logs in                       |
| `UserLoggedOut`                  | Emitted when the user logs out                      |
| `HouseholdUserAccountRegistered` | Emitted when the admin creates a new household user |
| `HouseholdUserAccountDeleted`    | Emitted when the admin deletes a household user     |
| `PasswordChanged`                | Emitted when the user password is updated           |
| `UsernameChanged`                | Emitted when the household username is updated      |

## **Smart furniture hookup context**

This context is responsible for managing the smart furniture hookups information.
Each physical smart furniture hookup has an endpoint, known to the user, which provides its name and consumption type (gas, water, or electricity). The admin can change freely change the hookup’s name.
The endpoint is stored for connection purposes. The system sends messages to the physical smart furniture hookup, through that endpoint, to specify where to send utility consumption data.

### Aggregate Root:
* `SmartFurnitureHookup`
  * id: `SmartFurniturHookupID`
  * name: `string`
  * consumptionType: `ConsumptionType`
  * consumptionUnit: `ConsumptionUnit`
  * endpoint: `EndPointURL`

### Value objects:
* `SmartFurniturHookupID`
* `ConsumptionType`
* `ConsumptionUnit`
* `EndPointURL`

### Services:
* **SmartFurnitureHookupManagementService**: 

### Factories:
* `SmartFurniturHookupFactory`:


### Repositories:
* `SmartFurniturHookupRepository`:

### Commands
#### Commands triggered by the admin:

| Command                    | Description                               |
|----------------------------|-------------------------------------------|
| CreateSmartFurnitureHookup | Create a new smart furniture hookup       |
| EditSmartFurnitureHookup   | Edit an existing smart furniture hookup   |
| DeleteSmartFurnitureHookup | Delete an existing smart furniture hookup |

#### Outbound events:

| Event                         | Description                                      |
|-------------------------------|--------------------------------------------------|
| `SmartFurnitureHookupCreated` | Emitted when a smart furniture hookup is created |
| `SmartFurnitureHookupDeleted` | Emitted when a smart furniture hookup is deleted |
| `SmartFurnitureHookupUpdated` | Emitted when a smart furniture hookup is updated |

# Map context

This context is responsible for managing the floor plan, zones and smart furniture hookup locations.
The floodplain is uploaded once during initial setup and serves as the base layout.
The admin can create, modify, and remove zones as needed, and manage the position of hookups within those zones or directly in the floor plan.


### Aggregate Root:
* `Map`
  * floorPlan: `FloorPlan`
  * zones: `ConsumptionType`
  * consumptionUnit: `list[Zone]`
  * smartFurnitureHookups: `list[SmartFurnitureHookup]`

### Entities:
* `FloorPlan`:
  * svgSource: `SVGSource`
* `Zone`:
  * id: `ZoneID`
  * name: `string`
  * color: `Color`
* `SmartFurnitureHookup`:
  * id: `SmartFurnitureHookupID`
  * position: `Position`
  * linkedZone: `ZoneID`

### Value objects:
* `SVGSource`
* `ZoneID`
* `Color`
* `SmartFurnitureHookupID`
* `Position`

### Services:
* `MapService`:

### Factories:
* `ZoneFactory`:
* `SmartFurnitureHookupFactory`:

### Repositories:
* `MapRepository`:

### Commands
#### Commands triggered by the admin:

| Command                    | Description                               |
|----------------------------|-------------------------------------------|
| CreateSmartFurnitureHookup | Create a new smart furniture hookup       |
| EditSmartFurnitureHookup   | Edit an existing smart furniture hookup   |
| DeleteSmartFurnitureHookup | Delete an existing smart furniture hookup |

#### Outbound events:

| Event                         | Description                                      |
|-------------------------------|--------------------------------------------------|
| `SmartFurnitureHookupCreated` | Emitted when a smart furniture hookup is created |
| `SmartFurnitureHookupDeleted` | Emitted when a smart furniture hookup is deleted |
| `SmartFurnitureHookupUpdated` | Emitted when a smart furniture hookup is updated |

# Consumption context

This context is responsible for ingesting and storing raw utility consumption from each smart hookup.
To achieve the collection, it sends each smart hookup its designated utility consumption record endpoint.
Each consumption record is enriched to the corresponding hookup on the map, along with the zone it belongs to.
The system associate the consumption record with a specific household user whenever possible.

## Ubiquitous Language

| Utility | A household resource that is specifically consumed  |
| :---- | :---- |
| **Utility consumption** | The measured quantity of a household utility consumed over a period of time. |
| **Utility consumption filter** | Separation of utility consumption data based on specific criteria |
| **Utility consumption record** | Data that rappresents a Utility consumption send by the smart hookup to the system |
| **Utility consumption record endpoint** | String that rappresents the URL to which the enpoint should send the utility consumption |
| **Smart Furniture Hookup** | A built-in connection point within furniture that enables tracking of utility usage per resource |
| **Floor Plan** | A digital representation of the physical layout of a household |
| **Zone** | A section of the floor plan |
| **Map** | The graphical representation of the floor plan of the house showing various zones and the location of Smart Furniture Hookups |

## Commands

Commands trigger by the physical hookup:

| Command | Description |
| :---- | :---- |
| RecordConsumption | Record a new consumption |

Commands triggered by inbound events:

| Command | Description |
| :---- | :---- |
| ConnectToPhysicalSmartHookup | Sends the URL of the consumption recording  endpoint to a physical hookup, enabling it to forward utility usage data to the platform. |

## Events

Inbound events:

- SmartHookupCreated: received when the admin creates a new smart hookup

Outbound events:

- SmartHookupNotConnected: emitted when a new a connection to a smart hookup is not possible
- ConsumptionRecorded: emitted when a new consumption is recorded

# Forecast context

This context is responsible for the management of the forecast of utility consumptions.
Forecasts are generated on a schedule—daily, weakly, or at another interval—which is determined by the depth and granularity of historical consumption data available.

## Ubiquitous Language

| Utility | A household resource that is specifically consumed  |
| :---- | :---- |
| **Utility consumption** | The measured quantity of a household utility consumed over a period of time. |
| **Forecast of utility consumption** | Prediction of future utility consumptions based on historical consumption data |
| **Historical consumption data** | Archived utility usage records |

## Commands

Command triggered inbound events:

| Command | Description |
| :---- | :---- |
| UploadConsumptionForecast | Upload the newly computed forecast of utility consumption |

## Events

Inbound Events:

- ConsumptionRecorded: received when a new consumption is recorded

Outbound events:

- ForecastConsumptionComputed: emitted when a newly computed forecast of utility consumption is successfully saved into the system

# Threshold context

This context is responsible for the management and evaluation of the utility consumption limits that trigger alerts. Thresholds are defined by the admin and apply to both real-time and forecasted utility consumption. Real-time thresholds can be set individually for each utility type (electricity, gas, water), or left undefined to disable monitoring for that utility. Similarly, forecasting thresholds specify the percentage by which actual usage may exceed predicted values. Forecast-based checks can be disabled.   
To evaluate the utility consumption against a

## Ubiquitous language

| Utility | A household resource that is specifically consumed  |
| :---- | :---- |
| **Utility consumption** | The measured quantity of a household utility consumed over a period of time. |
| **Utility consumption filter** | Separation of utility consumption data based on specific criteria |
| **Forecast of utility consumption** | Prediction of future utility consumptions based on historical consumption data |
| **Threshold** | A user-defined value of utility consumption limit, used for trigger alerts. |
| **Alert** | A warning sent to the user when utility consumption approaches or exceeds a defined threshold. |

## Commands

Commands triggered by the admin:

| Command | Description |
| :---- | :---- |
| DefineThreshold | Define the value of a threshold |
| RemoveThreshold | Remove a threshold  |

Commands triggered by the inbound events:

| Command | Description |
| :---- | :---- |
| EvaluateConsumptionThresholds | Evaluate a newly recorded real-time consumption measurement against the configured thresholds |


## Events

Inbound events:

- ConsumptionRecorded: received when a new consumption is recorded
- ForecastConsumptionRecorded: received when  a newly computed forecast of utility consumption is successfully saved into the system

Outbound events:

- ThresholdDefined: emitted when a value for a threshold is defined
- ThresholdRemoved: emitted when the value of a threshold is removed
- RealTimeConsumptionThresholdExceeded: emitted when actual, real-time consumption for a utility exceeds its defined threshold.
- ForecastConsumptionThresholdExceeded: emitted when actual consumption exceeds the forecasted value by more than the configured percentage threshold.

# Alerting context

This context is responsible for emitting the alerts to users when a threshold is exceeded. When that happens, it generates an alert that includes a type (real-time or forecast) and a descriptive message detailing the nature and specifics of the violation.

## Ubiquitous language

| Forecast of utility consumption | Prediction of future utility consumptions based on historical consumption data |
| :---- | :---- |
| **Threshold** | A user-defined value of utility consumption limit, used for trigger alerts. |
| **Alert** | A warning sent to the user when utility consumption approaches or exceeds a defined threshold. |

## Commands

Commands triggered by inbound events:

| Command | Description |
| :---- | :---- |
| CreateAlert | Create a new alert  |

## Events

Inbound events:

- RealTimeConsumptionThresholdExceeded: received when actual, real-time consumption for a utility exceeds its defined threshold.
- ForecastConsumptionThresholdExceeded: received when actual consumption exceeds the forecasted value by more than the configured percentage threshold.

Outbound events:

- AlertCreated: emitted when a new alert is created


  

