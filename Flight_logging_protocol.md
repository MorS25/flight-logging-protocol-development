# UAV/Operator Flight logging Exchange Protocol - Development

A protocol designed to describe a logged flight from an UAV/Drone

- [UAV/Operator Flight logging Exchange Protocol - Development](#uavoperator-flight-logging-exchange-protocol---development)
	- [1 Example Flight Log](#1-example-flight-log)
	- [2 Message structure](#2-message-structure)
		- [2.1 Message Details](#21-message-details)
			- [2.1.1 exchange_type section](#211-exchangetype-section)
			- [2.1.2 flight_data section](#212-flightdata-section)
			- [2.1.3 flight_logging\_geojson section](#213-flightlogginggeojson-section)
			- [2.1.4 flight\_logging section](#214-flightlogging-section)
			- [2.1.5 file section](#215-file-section)
			- [2.1.6 message_type section](#216-messagetype-section)
	- [3 Data types](#3-data-types)
		- [3.1 Dates & Times](#31-dates--times)
		- [3.2 Geospatial Data](#32-geospatial-data)
		- [3.3 Distances](#33-distances)
		- [3.4. Speed](#34-speed)
		- [3.5. Events](#35-events)

## 1 Example Flight Log

		{
			"exchange": {
				"exchange_type": "flight_logging",
				"message": {
					"flight_data": {
						"aircraft": {
							"firmware_version": "2.01b",
							"hardware_version": "1.00B",
							"manufacturer": "senseFly",
							"model": "eBee",
							"name": "John doe Drone",
							"serial_number": "EB-99-01807"
						},
						"gcs": {
							"manufacturer": "senseFly",
							"model": "eMotion",
							"version": "1.1"
						},
						"payload": [{
							"firmware_version": "1.23",
							"hardware_version": "0",
							"model": "WX RGB",
							"serial_number": "2352342141"
						}],
						"mission": "Projet test"
					},
					"flight_logging_geojson": {
						"flight_path": {
							"type": "FeatureCollection",
							"features": [{
									"type": "Feature",
									"properties": {
										"time": "2017-05-16T13:19:22.250Z",
										"altitude": 100,
										"groundspeed": 20,
										"event_type": "CONTROLER_EVENT",
										"event_info": "TOF"
									},
									"geometry": {
										"type": "Point",
										"coordinates": [-6.201825141906738,
											53.50841695106615
										]
									}
								},
								{
									"type": "Feature",
									"properties": {
										"time": "2017-05-16T13:19:23.250Z",
										"altitude": 120,
										"groundspeed": 20
									},
									"geometry": {
										"type": "Point",
										"coordinates": [-6.199507713317871,
											53.509246406536995
										]
									}
								},
								{
									"type": "Feature",
									"properties": {
										"time": "2017-05-16T13:19:25.250Z",
										"altitude": 120,
										"groundspeed": 20,
										"event_type": "CONTROLER_EVENT",
										"event_info": "LDG"
									},
									"geometry": {
										"type": "Point",
										"coordinates": [-6.20075225830078,
											53.50916984209669
										]
									}
								}
							]
						},
						"altitude_system": "WGS84",
						"logging_start_dtg": "2017-05-16T13:19:25.250Z",
						"tick_increment_modulo": "1",
						"tick_increment_seconds": "1",
						"uom_system": "Metric"
					},

					"file": {
						"logging_type": "GUTMA_DX_JSON",
						"filename": "EB-99-01807_0069",
						"creation_dtg": "2017-05-23T08:38:41.306Z",
						"version": "1.0.0"
					},
					"flight_logging": {
						"flight_logging_items": [
							[0.5, 0, 0, 0.2],
							[1, 0, 0, 0.18],
							[1.5, 0, 0, 0.17]
						],
						"flight_logging_keys": [
							"timestamp", "speed_vx", "speed_vy", "battery_power"
						],
						"events": [{
							"event_type": "CONTROLER_EVENT",
							"event_info": "TOF",
							"event_timestamp": "0.5"
						}],
						"altitude_system": "WGS84",
						"logging_start_dtg": "2017-05-16T13:19:25.250Z"
					},
					"message_type": "flight_logging_submission"
				}
			}
		}

## 2 Message structure

The message is divided into three main parts: logging data as a GeoJSON FeatureCollection (_called Standard log and is mandatory_). Description of devices used (_called Extended log and is optional_), and information about the file itself (_optional_).

### 2.1 Message Details
The message above has three sections: 
1. Exchange Type (exchange_type)
2. Flight Data Section (flight_data)
3. Standard Flight Logging (flight_logging_geojson)
4. Extended Flight Logging (flight_logging)
5. File Section (file)
6. Message Type Section (message_type)


#### 2.1.1 exchange_type section
This section details the type of message in the exchange. 

	"message_type": "flight_logging"
            
* **message_type**: type of logging that the exchange contains. At the moment only two types of exchagnes are allowed: flight_logging and flight_enroute. flight_enroute can be used when the log is transmitted by a flight that is already flying.
   
We cover each of these sections in detail below. 

#### 2.1.2 flight_data section

	"flight_data": {
		"aircraft": {
			"firmware_version": "2.01b",
			"hardware_version": "1.00B",
			"manufacturer": "senseFly",
			"model": "eBee",
			"name": "John doe Drone",
			"serial_number": "EB-99-01807"
		},
		"gcs": {
			"manufacturer": "senseFly",
			"model": "eMotion",
			"version": "1.1"
		},
		"payload": [{
			"firmware_version": "1.23",
			"hardware_version": "0",
			"model": "WX RGB",
			"serial_number": "2352342141"
		}],
		"operation": [{
		"mission": "Farm Survey",
		"operation_id": "34d2b0b6-a5d0-4c42-bd1a-5a273ca2b808",
		"operator": "Aviation Survey Inc",
		"utm_provider": "Airmap",
		"contact": "John Doe",
		"contact_url":"https://uss.com/contact/bd1a-5a273ca2b808/"
		}]
	}

This section contains information about hardware devices used during the flight:

- Aircraft section contains fields which a describing the aircraft used during the flight. An aircraft is uniquely identified with his manufacturer/model and serial number.
- gcs describes ground control station.
- Payload is used to declare what devices are embedded during the flight. There can be several payloads like gimbal, camera etc. 
- Mission is used to "tag" the flight or indicate if this flight is part of a larger mission.
- A ID associated with this flight issued by the UTM provider. 

#### 2.1.3 flight_logging\_geojson section
This part of the logging is called "Standard Logging" and is mandatory and must be a valid GeoJSON object. It could be a full flight log or partial one. 

	"flight_logging_geojson": {
		"flight_path": {
			"type": "FeatureCollection",
			"features": [{
					"type": "Feature",
					"properties": {
						"time": "2017-05-16T13:19:22.250Z",
						"altitude": 100,
						"ground_speed": 20,
						"vertical_speed": 2,
						"event_type": "CONTROLER_EVENT",
						"event_info": "TOF"
						"elevation": "50",
						"elevation_type": "GPS",
					},
					"geometry": {
						"type": "Point",
						"coordinates": [-6.201825141906738,
							53.50841695106615
						]
					}
				},
				{
					"type": "Feature",
					"properties": {
						"time": "2017-05-16T13:19:23.250Z",
						"altitude": 120,
						"ground_speed": 20,
						"vertical_speed": 2,
					},
					"geometry": {
						"type": "Point",
						"coordinates": [-6.199507713317871,
							53.509246406536995
						]
					}
				},
				{
					"type": "Feature",
					"properties": {
						"time": "2017-05-16T13:19:25.250Z",
						"altitude": 120,
						"ground_speed": 20,
						"vertical_speed": 2,
						"event_type": "CONTROLER_EVENT",
						"event_info": "LDG"
					},
					"geometry": {
						"type": "Point",
						"coordinates": [-6.20075225830078,
							53.50916984209669
						]
					}
				},
				{
					"type": "Feature",
					"properties": {
						"time": "2017-05-16T13:19:30.250Z",
						"altitude": 0,
						"ground_speed": 0,
						"vertical_speed": 0,
						"event_type": "CONTROLER_EVENT",
						"event_info": "UNK"
					},
					"geometry": {
						"type": "Point",
						"coordinates": [-6.20075225830078,
							53.50916984209669
						]
					}
				}
			]
		},
		"altitude_system": "WGS84",
		"logging_start_dtg": "2017-05-16T13:19:25.250Z",
		"tick_increment_modulo": "1",
		"tick_increment_seconds": "1",
		"uom_system": "Metric"
	}


 The format is self describing i.e. it contains the geometry types that are logged. It is must be a valid GeoJSON FeatureCollection with the each geometry having additional mandatory properties of timestamp, gps\_altitude and groundspeed. In case of declaring in linestrings, each co-ordinate should be accompanied by timestamps. GeoJSON coordinate precision is a subject left to the logging software. GeoJSON recommends six decimal precision, however in cases when the log is shared publicly it may be necessary to limit the precision to ensure privacy and other consideraitons. Also GeoJSON can be use to specify a polygonal area of operation if points are not preferred. 

#### 2.1.4 flight\_logging section
This part of the logging encompasses any additional logging items that need to be logged and provides flexibility to the logger. This is the "Extended log" 

	"flight_logging": {
		"flight_logging_items": [
			[0.5, 0, 0, 0.25],
			[1, 0, 0, 0.22],
			[1.5, 0, 0, 0.33]
		],
		"flight_logging_keys": [
			"timestamp", "speed_vx", "speed_vy", "battery_power"
		],
		"events": [{
			"event_type": "CONTROLER_EVENT",
			"event_info": "TOF",
			"elevation": "50",
			"elevation_type": "GPS",
			"event_timestamp": "0.5"
		}],
		"altitude_system": "WGS84",
		"logging_start_dtg": "2017-05-16T13:19:25.250Z"
	}

* **Timestamp** : number of the seconds elapsed since logging_start_dtg. It is a float, with max 3 decimals (so precision is milliseconds). By extension, the last timestamp will be equal to the duration flight in seconds.
* **gps\_lon, gps\_lat, gps\_altitude**: GPS coordinates
*  **speed**: ground speed in m/s (float)
* **battery\_power**: power as a percentage (float)
* **logging\_start\_dtg**: describes the beginning of the flight. It is mandatory. 
* **altitude\_system**: indicates the type of altitude reported: "agl", "amsl", "sps" or "WGS84"
* **events**: The events propoerty is used to log key events in the flight: "start-up-request", "start-up", "take-off", "en-route", "landing", "emergency", "obstacle-avoidance", "landed", "flight-complete"

#### 2.1.5 file section
This section contains information about the file itself. All fields are optional.

    "file":  {
    	"logging_type": "GUTMA_DX_JSON",
    	"filename": "EB-99-01807_0069",
    	"creation_dtg": "2017-05-23T08:38:41.306Z"
    }
            
* **logging_type**: type of logging
* **filename**: filename, can be with extension
* **creation_dtg**: file date creation
* **version**: protocol version

#### 2.1.6 message_type section
This section details the type of message in the exchange. 

	"message_type": "flight_logging_submission"
            
* **message_type**: type of logging that the exchange contains. At the moment only allowed type is flight_logging_submission


## 3  Data types
 
### 3.1 Dates & Times

Dates and times will follow the [ISO-8601](https://www.iso.org/iso-8601-date-and-time-format.html) formatting standard. Local times are not supported; all times must be in UTC or have a time zone offset specified.

No support is made for just a date or just a time. For comparison of time ranges, the start time is regarded as included in the time range, where the end time is excluded.

The format pattern for date times is YYYY-MM-DDTHH:mm:ss.sssZ where Z is either the character Z to represent UTC, or the +/- timezone offset from UTC. If a message is received with no timezone offset, it should be regarded as an error and the correct error code returned.

The requirement to specify times or dates does not apply to the specification, so all temporal data will be defined as a datetime.

No guarantees that a timezone offset will be preserved should be made.


### 3.2 Geospatial Data

Geospatial data must be described using a geometry object as defined in the GeoJSON specification with the following specific requirements:

1. Using the default CRS - geographic coordinate reference system, using the WGS84 datum, and with longitude and latitude units of decimal degrees.

2. Bounding box is not expected.

3. Latitude and Longitude should not be specified to more than 8 decimal places. This gives an accuracy of approximately 1.1 mm at the equator making any further digits superfluous.


### 3.3 Distances

All distances (both horizontal and vertical) are specified in metres.

### 3.4. Speed

All speeds are specified in meters/seconds


### 3.5. Events

The following controller events can be logged, this is consistent with the ICAO [Phase of Flight](http://www.intlaviationstandards.org/Documents/PhaseofFlightDefinitions.pdf) and [Occurance](http://www.intlaviationstandards.org/Documents/OccurrenceCategoryDefinitions.pdf) definitions.

1. Standing (STD)
2. Pushback / Towing (PBT)
3. Taxi (TXI)
4. Takeoff (TOF)
5. Initial Climb (ICL)
6. En Route (ENR)
7. Maneuvering (MNV)
8. Approach (APR)
9. Landing (LDG)
10. Emergency Descent (EMG)
11. Uncontrolled Descent (UND)
12. Post-Impact (PIM)
13. Unknown (UNK)
14. Abnormal Runaway Contact (ARC)
15. Abrupt Maneuver (AMAN)
16. Aerodrome (ADRM)
17. Airprox / TCAS Alert / Loss of Seperation / Near midair-collisions / Midair Collisions (MAC)
18. ATM / CNS (ATM)
19. Bird (BRD)
20. Cabin Safety Events (CABIN)
21. Collision with Obstacle(s) during takeoff and landing (CTOL)
22. Controlled Flight Into or Toward Terrain (CFIT)
23. Evacuation (EVAC)
24. External Load related occurances (EXTL)
25. Fire / Smoke (F-NI)
26. Fire / Smoke (F-POST)
27. Fuel Related (FUEL)
28. Glider Towing related events (GTOW)
29. Ground Collision (GCOL)
30. Ground Handling (RAMP)
31. Icing (ICE)
32. Loss of Control-Ground (LOC -G)
33. Loss of Control - INFLIGHT (LOC-I)
34. Low Altitude Operations (LALT)
35. Medical (MED)
36. Navigation Errors (NAV)
37. Other (OTHR)
38. Runway Excursion (RE)
39. Runway Incursion (RI)
40. Security Related (SEC)
41. System / component failure or malfunction (Non-powerplant) (SCF-NP)
42. System / Component failure or malfunction (Powerplant) (SCF-PP)
43. Turbulence Encounter (TURB)
44. Undershoot / overshoot (USOS)
45. Uninteded Flight in IMC (UIMC)
46. Unknown of Undetermined (UNK)
47. Wildlife (WILD)
48. Wind Shear or Thunderstrom (WSTRW)

and GUTMA Specific events:

1.  Start up request (SUR)
2.  Emergency (EME)
3.  Obstacle Avoidance (OBA)
4.  Landed (LND)
5.  Flight Complete (FCM)
