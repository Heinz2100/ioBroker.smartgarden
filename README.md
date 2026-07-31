![Logo](admin/smartgarden.png)

# ioBroker.smartgarden &mdash; API v2 Fork

## About this fork

This is a fork of [jpgorganizer/ioBroker.smartgarden](https://github.com/jpgorganizer/ioBroker.smartgarden)
(upstream version 2.0.1) with the adapter migrated from **GARDENA smart system API v1** to
**GARDENA smart system API v2** (spec 2.3.6). API v1 has been marked *deprecated* in the
Husqvarna Developer Portal; upstream development is currently inactive
(see upstream [Issue 88](https://github.com/jpgorganizer/ioBroker.smartgarden/issues/88)).

Changes compared to upstream 2.0.1:

  - all smart system endpoints switched from `/v1/...` to `/v2/...`
    (`/locations`, `/websocket`, `/command`); the Authentication API
    (`/v1/oauth2/token` on the authentication host) is unchanged, as it is
    not part of the smart system API versioning
  - `activity_mowing_i` mapping extended by the new API v2 `MowerActivity`
    codes (see updated table in chapter
    [For SERVICE_MOWER](#for-service_mower)); older mowers are not affected,
    newer mowers (e.g. SILENO *Sense* generation) now map correctly
  - version raised to 2.1.0 so that the ioBroker admin does not offer the
    official 2.0.1 as an "update"

Everything else (data point ids, instance configuration, features, VIS
bindings) is unchanged and fully compatible with upstream 2.0.1 &mdash; the fork
is a drop-in replacement for existing installations.

**Note for SILENO Sense mowers:** according to the GARDENA API release notes
(2026-06-17), these mowers do not report `rfLinkLevel` and `operatingHours`.
The corresponding data points will simply not be updated for these devices.

---

## ioBroker smartgarden adapter for GARDENA smart system

An adapter for GARDENA smart system using official
[GARDENA smart system API](https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/general)
and service.

The adapter allows the development of an application (e.g. with VIS) that
can be used in parallel with the official GARDENA app. The adapter and
its additional features do not affect any of the basic functions of the
GARDENA app and vice versa.

The adapter is not a complete replacement for the GARDENA app, but an
addition to integrate the GARDENA devices into a smart home with ioBroker.
The most important actions can be carried out with the adapter. It also
offers the opportunity to implement your own ideas that are not possible
with the GARDENA app.


## Supported devices

  - GARDENA smart SILENO robotic lawnmowers
  - GARDENA smart Irrigation Control
  - GARDENA smart Pressure Pump
  - GARDENA smart Water Control
  - GARDENA smart Power Adapter
  - GARDENA smart Sensor

  For more information about the devices see at [GARDENA German website](https://www.gardena.com/de/produkte/smart/smartsystem/)
  and [here in English](https://www.gardena.com/uk/products/smart/smart-system/).


## Requirements

To use this adapter you need the following things:
1. an GARDENA smart system account
1. an GARDENA application key
1. an GARDENA application secret

To get those things please go to Husqvarna Developer Portal at
[https://developer.husqvarnagroup.cloud/](https://developer.husqvarnagroup.cloud/).

Please Sign up or Log in if you already have an account and create a new application to get your *application key*
and *application secret*.

Currently the site looks like in the following screenshots.

---

![myapplications](img/myapplications.png)

Press button **NEW APPLICATION**

---

![createnewapplication](img/createnewapplication.png)

Edit form with your own data. Currently the field *Redirect URLs* is not used.  That's why you can currently enter any value.
Press button **CREATE**

---

![mysmartgardenapplication](img/mysmartgardenapplication.png)

At the next page you get the *application key* and *application secret*.
You will need those values for your adapter instance configuration.
And you have to connect the APIs

  - Authentication API ***and***
  - GARDENA smart system API.

For this press button **CONNECT NEW API** and select the first API. And repeat for the second API.

---

**Note:**
  - If you already have a Husqvarna Automower® Connect or a
    GARDENA smart system account, you can Sign In with that account and
	continue with Create application to get the application key and application secret.

	---

	***And it's almost certain that you have an account.*** *Please use the
	same account as for the GARDENA app in which your GARDENA devices are
	registered. Otherwise you will not 	get access to your devices.*

	---

  - Make sure that you've connected the application to the API's
    - Authentication API ***and***
	- GARDENA smart system API.

And of course you need a running ioBroker installation (at least using admin5 UI) and you should own
at least one working [GARDENA smart device](#supported-devices).


## Table of Contents
  * [About this fork](#about-this-fork)
  * [ioBroker smartgarden adapter for GARDENA smart system](#iobroker-smartgarden-adapter-for-gardena-smart-system)
  * [Supported devices](#supported-devices)
  * [Requirements](#requirements)
  * [Table of Contents](#table-of-contents)
  * [Installation](#installation)
  * [Setup adapter](#setup-adapter)
  * [Getting support](#getting-support)
  * [Data points of the adapter](#data-points-of-the-adapter)
     * [General things to know about data points](#general-things-to-know-about-data-points)
     * [For SERVICE_MOWER](#for-service_mower)
     * [For SERVICE_VALVE_SET](#for-service_valve_set)
     * [For SERVICE_VALVE](#for-service_valve)
     * [For SERVICE_POWER_SOCKET](#for-service_power_socket)
     * [For SERVICE_SENSOR](#for-service_sensor)
     * [For SERVICE_COMMON](#for-service_common)
  * [Rate Limits](#rate-limits)
  * [Irrigation not allowed while mowing](#Irrigation-not-allowed-while-mowing)
     * [What's the problem?](#whats-the-problem)
	 * [What is being done?](#what-is-being-done)
	 * [Basic behaviour -- WARNING](#basic-behaviour----warning)
  * [Wishes for data points](#Wishes-for-data-points)
  * [Note](#note)
  * [Changelog](#changelog)
     * [2.1.0](#210)
     * [previous versions](#201)
  * [Credits](#credits)
  * [License](#license)


## Installation

This fork is **not** published on npm. Install it directly from GitHub:

- via ioBroker admin: *Adapters* &rarr; GitHub icon (octocat) &rarr; tab
  *Custom* &rarr; enter the URL of this repository
- or via command line:
  `iobroker url https://github.com/<DEIN-GITHUB-USER>/ioBroker.smartgarden`

A description how to install from GitHub is available
[here](https://www.iobroker.net/docu/index-235.htm?page_id=5379&lang=de#3_Adapter_aus_eigener_URL_installieren) (German language).

The original v1 adapter remains available
- at npm: `npm install iobroker.smartgarden@2.0.1`
- at GitHub under https://github.com/jpgorganizer/ioBroker.smartgarden

Switching between upstream 2.0.1 and this fork in both directions does not
touch instance configuration, objects or states.

## Setup adapter

1. Install the adapter
2. Create an instance of the adapter
3. Check and complete instance configuration

   **If you change any value of those settings please restart your adapter.**

   3.1 Edit application key and application secret and/or optional username, password in main instance
   configuration

      | Parameter | Description |
      | - | - |
	  |***mandatory***||
      | Application key | Application key (API key), e.g. under [Requirements](#requirements) |
	  | either *application secret* <br> or *username and password* \*) \*\*)||
	  |***recommended***||
      | Application secret \*)| application secret, e.g. under [Requirements](#requirements) - only if *username* and *password* are empty (new in v2.0.0)*|
	  |***not recommended***||
      | username \*) \*\*)| username for GARDENA smart system - only if *application secret* is empty|
      | password \*) \*\*)| corresponding password - only if *username* is specified|

   **NOTES:**
   \*)
     - From release v2.0.0 **the preferred login procedure is using *application key* and
     *application secret*** as the former login procedure with *username* and *password* isn't supported by
     Gardena anymore, but nevertheless it's still working for many users.
	 For this reason it is still available here, but in the event of an error,
	 there is no longer any support for it.
     So it's recommended to use *application key* and *applicaton secret*!

     - *Application key*, *application secret* and *password* are encrypted and stored within
     the adapter and become just decrypted for authentication with the GARDENA application host.

   \*\*)
     - parameter is discontinued and may no longer be available in a future version



   3.2 Verify default values of miscellaneous settings and switch on/off
   options in instance configuration. For most users the default values
   will be ok.

      | Parameter | Description |
      | - | - |
      | forecast | use forecast for charging time and mower remaining time; switch forecast charging and mowing time of mower on/off; default: off; *(new in v0.5.0)*|
      | cycles | number of MOWER history cycles; you can use any number from 3 (minimum), but 10 (default) seems to be a good value; only relevant if the above *'forecast'* is on; *(new in v0.5.0)*|
      | irrigation check| use the check whether irrigation is allowed while mowing; switch on/off; default: off; *(new in v0.6.0)*|
	  | monitor limit | use monitoring for the rate limits of Gardena smart system API; switch on/off; default: off; *(new in v1.0.2)*|

   3.3 Verify default values of systems settings and switch on/off options in
   instance configuration. **Most users will not have to change anything on this tab.**

      | Parameter | Description |
      | - | - |
      | Loglevel | Loglevel: 0 = no log entries, 1 = some log entries, 2 = some more log entries, 3 = all log entries; default: 0 - no log entries|
      | beautify log | make state ids shorter in log; switch on/off; default: on; *(new in v1.0.5)*|
      | connection retry interval | interval for retry to connect to Gardena Webservice in case of an error (in seconds); default: 300, minimum: 60; *(new in v1.0.3)*. **Recommendation for this fork: use 1800 or higher.** During a longer API outage each retry costs 2-3 API requests; with the default of 300 s a permanent outage consumes the Gardena application quota (700 requests/week) within about one day.|
      | ping interval | Interval for sending Ping's to Gardena Webservice (in seconds); default: 150, minimum: 1, maximum: 300|
      | auth factor  | Factor for validity of authentication token; default: 0.999 |
      | Auth-URL| Authentication host URL; default: [https://api.authentication.husqvarnagroup.dev](https://api.authentication.husqvarnagroup.dev)|
      | Base-URL| Webservice Base-URL; default: [https://api.smart.gardena.dev](https://api.smart.gardena.dev). The API version paths (`/v2/...`) are part of the adapter code, not of this setting.|


## Getting support

This fork is maintained privately and provided as is. For questions
regarding the original adapter read this [README](README.md) and the
[FAQ](FAQ.md) carefully and see the
[ioBroker forum thread](https://forum.iobroker.net/topic/31289/neuer-adapter-smartgarden-adapter-for-gardena-smart-system).

## Data points of the adapter
The adapter is designed to monitor and control GARDENA smart system devices.
For this there will be one `LOCATION` and one or many `DEVICE`'s.
For each `DEVICE` there will be
  - one `SERVICE_COMMON_<id>` and
  - one or more `SERVICE_<servicelink_type>_<id>`.

Where `<servicelink_type>` is a type description for the
device, for example MOWER or VALVE and `<id>` is a (encoded)
GARDENA device id used by the API.
See description for ServiceLink at
[https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/swagger](https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/swagger).

Controlling/monitoring for each device is possible via the
`SERVICE_<servicelink_type>` listed in the following table. The
`SERVICE_COMMON` provides general information about the device.

  | device | SERVICE_<servicelink_type> |
  | - | - |
  | smart SILENO robotic lawnmower | SERVICE_MOWER and SERVICE_COMMON |
  | smart Irrigation Control | SERVICE_VALVE_SET, SERVICE_VALVE and SERVICE_COMMON |
  | smart Pressure Pump | SERVICE_VALVE and SERVICE_COMMON |
  | smart Water Control | SERVICE_VALVE and SERVICE_COMMON |
  | smart Power Adapter | SERVICE_POWER_SOCKET and SERVICE_COMMON |
  | smart Sensor | SERVICE_SENSOR and SERVICE_COMMON |

If you need more information about the data points please have a look at
[https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/swagger](https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/swagger).
There you'll find a description for every data point; except for those which
are marked as data points of the adapter and not of the
GARDENA smart system API.

The adapter creates its own data points for various features / options when
the feature is selected. These data points are not automatically deleted
when the feature is deselected. If you no longer need these data points,
they can be deleted manually.

### General things to know about data points

The adapter doesn't change any values transmitted by the GARDENA smart system API.
The only thing that is done (from version 1.0.0) is to check the type of *timestamps*
and *numbers*.

| check for | description |
| - | - |
| timestamps | all timestamps are given in UTC; if a received timestamp is not a valid timestamp, `01 Jan 1970 00:00:00Z` (Unix time zero) is used instead. So if you see this date/time please report. |
| numbers | if a number is not a valid number, `-1` is used instead.  So if you see this number please report. |

Requests to control a device will succeed as soon as the command was accepted by the smart Gateway. A successful
execution of the command on the device itself can be observed by a respective state change.
*Example:* sending a command to start the VALVE service of a smart Water Control will result in the `activity_value`
data point of the service to be changed after the device processed the command.

**Notes:**
  - Requests to control a device cannot be sent while the smartgarden adapter is not
    connected to GARDENA smart system API.
  - Please check that you set the value for a command with `ack=false`. See [Chapter Commands and Statuses in Guide for adapter developers](https://github.com/ioBroker/ioBroker.docs/blob/master/docs/en/dev/adapterdev.md#commands-and-statuses)


### For SERVICE_MOWER
#### Controlling
To control the device use data point
- `activity_control_i`: type `string`

  *This data point is generated by the adapter and is not required due to the GARDENA smart system API.*

  Change this data point to start the mower.
  - To start for a defined time set the value to the planned duration in
  seconds (please use multiples of 60; minimum is 60); consider the data type `string`
  - for automatic operation set string `START_DONT_OVERRIDE`
  - to cancel the current operation and return to charging station use
  string `PARK_UNTIL_NEXT_TASK`
  - to cancel the current operation, return to charging station and ignore
  schedule use string `PARK_UNTIL_FURTHER_NOTICE`

  **Note:** The mower only starts with a fully charged battery.

#### Monitoring
All other data points are just for monitoring and information.

Special data points:
- `activity_mowing_i`

  *This data point is generated by the adapter and is not required due to the GARDENA smart system API.*

  This data point shows two different states for the mower:
  - `true`: mowing or
  - `false`: not mowing.

  This data point can be used for further actions where it is important to
  know whether the mower is safely on the lawn or not.

  Depending on the value of data point `activity_value` this data point is set.
  Please see following table for details. Codes marked with *(API v2)* are new
  in the GARDENA smart system API v2 and supported from fork version 2.1.0.

  | `activity_value` | `activity_mowing_i` |
  | - | - |
  |`OK_CHARGING` The mower has to be mowing but insufficient charge level keeps it in the charging station. | false |
  |`PARKED_TIMER` The mower is parked according to timer, will start again at configured time. | false |
  |`PARKED_PARK_SELECTED` The mower is parked until further notice. | false |
  |`PARKED_AUTOTIMER` The mower skips mowing because of insufficient grass height. | false |
  |`PAUSED` The mower is in a waiting state with hatch closed. | false |
  |`PAUSED_IN_CS` The mower is paused in charging station. *(API v2)* | false |
  |`PARKED_FROST` The mower is parked due to frost. *(API v2)* | false |
  |`PARKED_NO_LIGHT` The mower is parked due to insufficient light conditions. *(API v2)* | false |
  |`PARKED_MOWING_COMPLETED` The mower is parked because all areas have been fully mowed. *(API v2)* | false |
  |`PARKED_RAIN` The mower is parked due to rain. *(API v2)* | false |
  |`OK_CUTTING` The mower is cutting in AUTO mode (schedule). | true |
  |`OK_CUTTING_TIMER_OVERRIDDEN` The mower is cutting outside schedule. | true |
  |`OK_SEARCHING` The mower is searching for the charging station. | true |
  |`OK_LEAVING` The mower is leaving charging station. | true |
  |`STOPPED_IN_GARDEN` The mower is stopped in the garden. *(API v2)* | true |
  |`INITIATE_NEXT_ACTION` The mower is initiating the next action. *(API v2)* | true |
  |`SEARCHING_FOR_SATELLITES` The mower is searching for satellites. *(API v2)* | true |
  |`NONE` No activity is happening, perhaps due to an error. | true |
  |all other values | true |

  Codes where the mower is physically in the garden (`STOPPED_IN_GARDEN`,
  `INITIATE_NEXT_ACTION`, `SEARCHING_FOR_SATELLITES`) are deliberately mapped
  to `true`, as this data point is meant to answer whether the mower is
  safely parked or possibly on the lawn (relevant for the
  [irrigation check](#Irrigation-not-allowed-while-mowing)).

- `batteryState_chargingTime_remain_i` *(under SERVICE_COMMON...)* and <br/>
`activity_mowingTime_remain_i` *(under SERVICE_MOWER...)*

  *Both data points are generated by the adapter and are not required due to the GARDENA smart system API.*

  Those data points show an forecast for remaining charging and mowing time
  in seconds of the mower.
  They are only created when the function is selected in the
  instance configuration.

  To forecast a value an history of the last few charging and mowing cycles
  is saved in two states `info.saveMowingHistory` and
  `info.saveChargingHistory`.

  This feature can be switched on/off in adapter instance configuration
  along with the number of saved charging and mowing cycles in history.

  To put this function into operation, **please make sure that at least one
  cycle of mowing and charging runs without errors (e.g. not interrupted
  manually or sensor control).** It is better if at least three runs are
  completed without errors.
  This function tries to recognize the normal case and initially
  assumes that the next process is a normal case. If this is faulty, then
  this faulty run is regarded as a normal case and the runs that then pass
  through normally as a fault case. If there is an error during the run,
  please stop the adapter, delete the two data points and start again.

  For more information about general forecasting mechanisms see
  [FORECAST.md](FORECAST.md).

  **Notes:**
    1. Forecast values are only available if at least one complete
	charging and mowing cycle is saved in history.

    2. The history is saved under `info` so that if the `LOCATION` needs
	to be deleted, e.g. in the event of a future update, it is not lost.

    3. If you disconnect your mower from the GARDENA smart system and
	reconnect it again the history is lost, because your mower get's a new
	id within the GARDENA smart system. This means that the adapter cannot
	recognize the mower as the previous mower - may be it's a second one.
	In this case it is recommended to delete these two data points and to
	restart the adapter so that the previous (now old) history sets are not
	constantly read and written. The adapter then begins to build a new
	history.

	4. This function should work for more than one mower, but it is
	not tested *(I can't do that, because I've only one mower)*.
	If you have more than one mower please test and report errors
	and of course report if it works as intended. Thanks in advance for that.

- `lastErrorCode_value`

  Please pay special attention to data point `lastErrorCode_value`.
  A description of possible values can be found at
  https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/swagger,
  see "MowerService - lastErrorCode"


### For SERVICE_VALVE_SET
#### Controlling
To control the device use data point
- `stop_all_valves_i`: type `string`

  *This data point is generated by the adapter and is not required due to the GARDENA smart system API.*

  Change this data point to stop all valves.
  - To stop all valves immediately use string `STOP_UNTIL_NEXT_TASK`

  **Note:** Do not display the value of this data point in your application,
  as the value is mostly undefined. Furthermore, this data point cannot
  serve as a trigger for your own actions, because it is just set to value
  *null* after the command was triggered.

#### Monitoring
All other data points are just for monitoring and information.


### For SERVICE_VALVE
#### Controlling

To control the device use data point
- `duration_value`: type `string`

  Change this data point to start the valve.
  - To start for a defined time  set the value to the value in seconds
  (please use multiples of 60; minimum is 60); consider the data type `string`.

    **Note:** There are some limitations for the allowed values.
    Please report if you see other limitations.

    | device | limit |
    | - | - |
    |GARDENA smart Irrigation Control| 5400 seconds (90 minutes) |
    |GARDENA smart Pump | 36000 (10 hours) |
    |GARDENA smart Water Control | 36000 (10 hours) |

  - To cancel the current watering and continue with the schedule use string
  `STOP_UNTIL_NEXT_TASK`
  - To skip automatic operation until specified time, the currently active
    operation might or might not be cancelled (depends on device model) use string
    `PAUSE_<number_of_seconds>`, e.g. `PAUSE_86400` to pause for 24 hours (please use multiples of 60; minimum is 60)
  - To restore automatic operation if it was paused use string `UNPAUSE`

- `irrigationWhileMowing_allowed_i` and `irrigationWhileMowing_mowerDefinition_i`

  *This data points are generated by the adapter and are not required due to the GARDENA smart system API.*

  Those data points give control over the feature
  *Irrigation not allowed while mowing*.
  They are only created when the function is selected in the
  instance configuration.
  For description of this feature see chapter
  [Irrigation not allowed while mowing](#Irrigation-not-allowed-while-mowing).

#### Monitoring

All other data points are just for monitoring and information.

Special data point:
- `duration_leftover_i`

  *This data point is generated by the adapter and is not required due to the GARDENA smart system API.*

  The value describes the number of minutes till the valve is closed and
  watering stops.
    - An integer, one (`1`) or more.
    - `null` if undefined


### For SERVICE_POWER_SOCKET
#### Controlling
To control the device use data point
- `duration_value`: type `string`

  Change this data point to start the power socket.
  - To start for a defined time  set the value to the value in seconds
  (please use multiples of 60; minimum is 60); consider the data type `string`
  - To switch on the device forever please use the string `START_OVERRIDE`.
  - To stop the device use `STOP_UNTIL_NEXT_TASK`.
  - To skip automatic operation until specified time. The currently active operation
    will NOT be cancelled. Use string `PAUSE_<number_of_seconds>`,
    e.g. `PAUSE_86400` to pause for 24 hours (please use multiples of 60; minimum is 60)
  - To restore automatic operation if it was paused use string `UNPAUSE`

#### Monitoring

All other data points are just for monitoring and information.

Special data point:
- `duration_leftover_i`

  *This data point is generated by the adapter and is not required due to the GARDENA smart system API.*

  The value describes the number of minutes till the power socket is shut off.
    - An integer, one (`1`) or more.
    - `null` if undefined


### For SERVICE_SENSOR
#### Controlling
No control functions available.

#### Monitoring
All data points are just for monitoring and information.


### For SERVICE_COMMON

The `SERVICE_COMMON` provides general information about the device.
Description is integrated into description of other SERVICE_... where
necessary.

## Rate Limits

There are some limits you shoud be aware of.
Please see chapter *Rate Limits* in
[*README*](https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/readme)
of GARDENA smart system API description. The limits apply per application
key across all API versions; API v2 does not change them.

To help you to see if you hit those rate limits you can switch on monitoring in
instance configuration with parameter *monitoring Rate Limits*.

If you've enabled monitoring state `info.RateLimitCounter` gets actualized with every request.
This state saves a data structure with the number of requests per month, day, hour and for
the last 30 and 31 days.

The structure is in [JSON](https://en.wikipedia.org/wiki/JSON) and looks like

```
{
  "2020": {                          <<< year
    "2020-08": {                     <<< month
      "count": 21,                   <<< number of requests for month
      "2020-08-27": {                <<< day
        "11": {                      <<< hour
          "count": 3                 <<< number of requests for hour
        },
        "12": {                      <<< hour
          "count": 13                <<< number of requests for hour
        },
        "count": 16                  <<< number of requests for day
      },
      "2020-08-28": {                <<< day
        "14": {                      <<< hour
          "count": 5                 <<< number of requests for hour
        },
        "count": 5                   <<< number of requests for day
      }
    }
  },
     ...
  "last30days": {
    "count": 2021                    <<< number of requests in last 30 days
  },
  "last31days": {
    "count": 2098                    <<< number of requests in last 31 days
  }
}
```

**Note:**
  - That hour is hour of time in UTC
  - That the actual number of requests may be higher. Especially as
  long as the respective period is not fully covered by the monitoring.
  - That this structure becomes very large and is never deleted by the
  adapter. So please delete it manually from time to time or switch off
  monitoring - at least if you don't have any issues with the rate limits.


## Irrigation not allowed while mowing

### What's the problem?

If you have both a mower and an irrigation system with pop-up sprinklers,
there is a risk that your mower will run into a pop-up sprinkler while the
irrigation is running and damage it or cause damage itself.

To prevent this, the irrigation system or better individual valves should be
switched off when the mower is mowing.

### What is being done?

With this function it is possible to stop irrigation when the mower is
on the lawn. This can be defined separately for each valve.

One or more mowers can be defined for each valve, for which the valve is
not allowed to be open while the mower is mowing.
Basically, the mower has priority over irrigation, i.e. if the conflict
arises that the mower is mowing and a valve is open, the valve is closed
and a corresponding warning is set.

Additionally it is possible to define that a valve should never open
regardless of a mower. E.g. can be used if a valve or the pipe
behind it is damaged.

The whole check can be switched on or off in instance configuration with
parameter *irrigation check*.

There are three data points available for each `SERVICE_VALVE`.
They are used for configuration and for reporting warnings.


  | data point | writeable | Description of data points |
  | - | - | - |
  |`irrigationWhileMowing_allowed_i` | yes |set to `false` if it should be checked if irrigation is allowed while the mower is mowing on the lawn, `true` otherwise |
  |`irrigationWhileMowing_mowerDefinition_i`| yes | one or more id's of `SERVICE_MOWER` which get checked. Format of mower id see below. There is a special code `IRRIGATION_FORBIDDEN` meaning that the valve is always closed regardless of a mower. If more than one id should be set then separate them by semicolon (`;`).|
  |`irrigationWhileMowing_warningCode_i`| no | warning code is set if valve opens. Possible warning codes see next table. If more than one warning is set, codes are concatenated with `+` (e.g. `STOPPED+UNKNOWN_MOWER`).|

* ***mower id format***

  `smartgarden.0.LOCATION_xxxxxxxx-xxxxxx-xxxxxx-xxxxxx-xxxxxxxxxxxxxx.DEVICE_xxxxxxxx-xxxxxx-xxxxxx-xxxxxx-xxxxxxxxxxxxxx.SERVICE_MOWER_xxxxxxxx-xxxxxx-xxxxxx-xxxxxxxxxxxxxxxxxxxxx`

  You can copy this mower id from the objects tab of ioBroker,
  see red arrow in the following picture.

    ![mower id](img/mowerid_admin5.jpg)

* ***warning codes***
  | warning code| description|
  | - | - |
  |  `NO_WARNING` |no warning, valve opened |
  |  `UNKNOWN_MOWER` |wrong mower id in `irrigationWhileMowing_mowerDefinition_i`|
  |  `STOPPED` |valve automatically closed because mower is mowing |
  |  `FORBIDDEN` |valve closed because special code `IRRIGATION_FORBIDDEN` is set in data point `irrigationWhileMowing_mowerDefinition_i`|

This function is runnig every time when
- a valve becomes opened or
- a mower starts mowing

It doesn't run when you change the values in the data points listed above.
That means: if there is a conflict situation and you change
`irrigationWhileMowing_allowed_i` from `true` to `false`, the conflict is not
recognized and the conflict will continue. The same behaviour applies to a
change of `irrigationWhileMowing_mowerDefinition_i`.

### Basic behaviour -- WARNING

This feature cannot prevent a valve from opening while the mower is
mowing. E.g. this can be done manually through the GARDENA app or
automatically through a schedule.

This function can only close the valve as quickly as possible in the
event of a conflict. And a conflict may not be recognized either.
So it can happen that water is let through.
**E.g. it cannot be prevented that the pop-up sprinklers extend and that the
mower hits the pop-up sprinklers**, but the likelihood that this will
happen has been minimized.
**So it is up to your application to make sure that this conflict will
never happen.**

## Wishes for data points

This adapter reports **every value** as a data point that is supplied via the
GARDENA smart system API. If someone wants more values, please contact GARDENA
and inform them that this value will also be included in the API. To do this,
please go to ***Contact us & Leave feedback*** in the footer on the
[GARDENA Developer Portal](https://developer.husqvarnagroup.cloud).


## Note

This is a private project. I am not in any association with
GARDENA or Husqvarna.


## Changelog
### 2.1.0 (this fork)
* 2026-Jul-31
  - migration from GARDENA smart system API v1 (deprecated) to API v2 (spec 2.3.6):
    endpoints `/v2/locations`, `/v2/websocket`, `/v2/command`; Authentication API unchanged
  - `activity_mowing_i`: mapping extended by new API v2 `MowerActivity` codes
    (`PAUSED_IN_CS`, `PARKED_FROST`, `PARKED_NO_LIGHT`, `PARKED_MOWING_COMPLETED`,
    `PARKED_RAIN` &rarr; `false`; `STOPPED_IN_GARDEN`, `INITIATE_NEXT_ACTION`,
    `SEARCHING_FOR_SATELLITES` &rarr; `true`)
  - no changes to data point ids, instance configuration or features;
    drop-in replacement for upstream 2.0.1

### 2.0.1
* (jpgorganizer) 2024-May-25
  - fixed warning `smartgarden has an invalid jsonConfig`, e.g.
  [Issue 72](https://github.com/jpgorganizer/ioBroker.smartgarden/issues/72)
  - fixed [Issue 64](https://github.com/jpgorganizer/ioBroker.smartgarden/issues/64)
    `Connection == true` when adapter is stopped
  - Fix comparison with `NaN` in api.js, e.g. [Pull request 67](https://github.com/jpgorganizer/ioBroker.smartgarden/pull/67)
  - some further minor changes

### previous versions
* see the upstream changelog at
  [jpgorganizer/ioBroker.smartgarden](https://github.com/jpgorganizer/ioBroker.smartgarden#changelog)


## Credits

This fork is based on the work of **jpgorganizer**,
[https://github.com/jpgorganizer/ioBroker.smartgarden](https://github.com/jpgorganizer/ioBroker.smartgarden).
All credit for the adapter concept and implementation belongs to the
original author.

Many thanks to GARDENA/Husqvarna for providing this
[public API](https://developer.husqvarnagroup.cloud/apis/GARDENA+smart+system+API#/general)
and special thanks to your support team for providing very good and very fast support.

smartgarden logo: http://www.freepik.com Designed by Freepik


## License

Copyright (c) 2020 - 2024 jpgorganizer, https://github.com/jpgorganizer

smartgarden by jpgorganizer is licensed under a
Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License
[(CC-BY-NC-SA-4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
Based on a work at https://github.com/jpgorganizer/ioBroker.smartgarden.

This fork (API v2 migration, 2026) is distributed under the same license
(CC-BY-NC-SA-4.0), as required by the ShareAlike condition, and is likewise
based on the work at https://github.com/jpgorganizer/ioBroker.smartgarden.
