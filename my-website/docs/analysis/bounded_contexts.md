---
title: Bounded Contexts
sidebar_position: 5
---

# **Bounded Contexts**

The Event Storming session revealed the following bounded contexts:

- **Auth**: responsible for managing user authentication and authorization
- **Household user**: responsible for managing the household user account information
- **Interactive map**:  responsible for managing the floorplan, the zones and the hookups locations and status
- **Monitoring**: responsible for ingesting and storing raw utility consumption from each physical smart hookup
- **Forecasting**: responsible for the management of the forecast of utility consumptions
- **Threshold**: responsible for the management and evaluation of the utility consumption limits that trigger alerts
- **Alerting**: responsible for emitting the alerts to users when consumption limits are exceeded


# Auth context

This context is responsible for managing user authentication and authorization.  
Authentication is handled by validating each user’s credentials—username and password. Users may change their own password at any time; however, only household users can update their username, and only the admin has the ability to reset his password. The admin also manages household user’s credentials by creating, editing, and deleting them. The authentication management responsibilities also include creating and terminating sessions.  
The authorization is handled  through a permission system which uses a role model, where the roles can be only “admin” or “household user”.

## Ubiquitous Language

| User | An individual who uses the platform |
| :---- | :---- |
| **Admin** | A user with full access rights who is responsible for configuring and managing the system |
| **Household User** | An individual who resides in the household and uses the platform |
| **Role** | Label assigned to a specific user, used to distinguish the admin from the household users |
| **Credentials** | A pair of username and password used to authorize and to authenticate users into the system. |
| **Authentication** | The process of verifying a user’s identity |
| **Authorization** | The process of verifying if a user has permission to perform a determinate action within the system |

### Aggregate Root:
* **User**: Represents a system user, with their credentials and role.
            It forms an aggregate root with the `Role` value objects.
  * `userID`: Unique identifier for the User.
  * `name`: The name of the user.
  * `password`: The password of the user.

### Value Objects:
* **Role**: Represents a user's role, which determines their permissions.
    * `value`: The value of the role.

### Services:
* **AuthService**: Provides functionalities for user authentication, such as registration, login, and logout.

### Repositories:
* **UserRepository**: Manages the persistence and retrieval of `User` aggregates. 

## Commands

Commands triggered by an user:

| Command | Description |
| :---- | :---- |
| AuthenticateUser | Validate credentials and create a session |
| LogoutUser | Invalidate an existing session |
| ChangePassword | Update own password |

Commands triggered by the admin:

| Command | Description |
| :---- | :---- |
| RegisterHouseholdUserCredentials | Create a new household user’s credentials and assign them the household role. |
| DeleteHouseholdUserCredentials | Delete the credentials of an existing household user |
| ChangeHouseHoldUserCredentials | Update name and/or password of an existing household user |
| ResetPassword | Reset password with a new one |

Commands triggered by an household user:

| Command | Description |
| :---- | :---- |
| ChangeName | Update own name |

## Events

Outbound events:

- UserAuthenticated: emitted when the user is authenticated
- UserLoggedOut: emitted when the user logs out
- HouseholdUserCredentialsRegistered: emitted when the admin creates credentials for a new household user
- HouseholdUserCredentialsDeleted: emitted when the admin deletes a household user’s credentials
- PasswordChanged: emitted when the user password is updated
- NameChanged: emitted when the household user name is updated


# Household User context

This context is responsible for managing the user account information. Each account includes a unique token that ties utility consumption data to a specific household user so that the utility consumption filter can be achieved. Accounts are created immediately after credential setup. Household users may update their own profile details, while administrators can edit any household user’s profile and delete an account by removing its credentials.

## Ubiquitous language

| User | An individual who uses the platform |
| :---- | :---- |
| **Admin** | A user with full access rights who is responsible for configuring and managing the system |
| **Household User** | An individual who resides in the household and uses the platform |
| **Account** | Set of information associated with a user |
| **Token** | A string code used to recognize household user by the Smart Furniture Hookup |
| **Utility** | A household resource that is specifically consumed  |
| **Utility Consumption** | The measured quantity of a household utility consumed over a period of time. |
| **Utility consumption filter** | Separation of utility consumption data based on specific criteria |

## Commands

Commands triggered by the admin:

| Command | Description |
| :---- | :---- |
| ChangeHouseholdUserToken | Update the household user token |

Commands triggered by an household user:

| Command | Description |
| :---- | :---- |
| ChangeToken | Update own token |

Commands triggered by inbound events:

| Command | Description |
| :---- | :---- |
| RegisterHouseholdUserAccount | Create a new household user’s account |
| DeleteHouseholdUserAccount | Delete the account of an existing household user |

## Events

Inbound events:

- HouseholdUserCredentialsRegistered: received when the household user credentials are created
- HouseholdUserCredentialsDeleted: received when the household user credential are deleted

Outbound events:

- HouseholdUserAccountRegistered: emitted when the household user account is created
- HouseholdUserAccountDeleted: received when the household user account is deleted


# Interactive map context

This context is responsible for managing the home’s floorplan, along with its zones and all the smart hookups. The floorplan is uploaded once during initial setup and serves as the base layout. The admin can create, modify, and remove zones as needed, and likewise manage hookups within those zones or directly in the floorplan. Additionally, this context tracks each hookup’s current status—updated automatically whenever a new consumption measurement is recorded.

## Ubiquitous language

| Admin | A user with full access rights who is responsible for configuring and managing the system |
| :---- | :---- |
| **Smart Furniture Hookup** | A built-in connection point within furniture that enables tracking of utility usage per resource |
| **Status of Smart Furniture Hookup** | The current operational state of a smart furniture hookup, indicating whether it is actively supplying a utility or not and how much is supplying |
| **Floor Plan** | A digital representation of the physical layout of a household |
| **Zone** | A section of the floor plan |
| **Map** | The graphical representation of the floor plan of the house showing various zones and the location of Smart Furniture Hookups |
| **Interactive Map** | The dynamic version of map showing the location and status of each smart furniture hookup within the household. |

## Commands

Commands triggered by the admin:

| Command | Description |
| :---- | :---- |
| UploadFloorPlan | Upload and store the floorplan file |
| CreateZone | Create the a zone in the floorplan |
| UpdateZoneInformation | Update the information on an existing zone |
| DeleteZone | Delete an existing zone |
| CreateHookup | Create the a zone in the floorplan |
| UpdateHookup | Update the information on an existing zone |
| DeleteHookup | Delete an existing zone |

Commands triggered by inbound events:

| Command | Description |
| :---- | :---- |
| UpdateHookupStatus | Update the status of an existing hookup |

## Events

Inbound events:

- ConsumptionRecorded: received when a new consumption is recorded

Outbound events:

- FloorplanUploaded: emitted when the admin uploads the file of the floorplan
- ZoneCreated: emitted when the admin creates a new zone
- ZoneUpdated: emitted when the admin updates the information of an existing zone
- ZoneDeleted: emitted when the admin deletes a zone
- SmartHookupCreated: emitted when the admin creates a new smart hookup
- SmartHookupUpdated: emitted when the admin updates the information of an existing smart hookup
- SmartHookupDeleted: emitted when the admin deletes a smart hookup
- SmartHookupStatusUpdated: emitted when the status of an existing smart hookup changes

# Consumption context

This context is responsible for ingesting and storing raw utility consumption from each smart hookup. To achieve the collection, it sends each smart hookup its designated utility consumption record endpoint. Each consumption record is enriched to the corresponding hookup on the map, along with the zone it belongs to. The system associate the consumption record with a specific household user whenever possible.

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

This context is responsible for the management of the forecast of utility consumptions. Forecasts are generated on a schedule—daily, weakly, or at another interval—which is determined by the depth and granularity of historical consumption data available.

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


  

