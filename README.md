# PROPOSED QUIRK HANDLING:

considering that the library does not perform any data modification/scaling the best approach to keep the logical separation would be:

0) define Quirk class in the sdk library port
1) specify quirks for scaling some given ids in a 'quirks libary'
2) accept and process quirks in the scripts making use of the library (e.g. homeassistant)

## TODO:

all

## CONCEPT PSEUDOCODE

0) this is the code that defines the quirks, part of the new sdk
```
#define a quirks.py in the sdk library:


class Quirk ...
   self.id = ... #device id to be matched
   #dict: keys are DPCodes, values are dicts of transform (scaling) properties
   self.DPScaling = {DPCode:{	'type':'affine', # linear supports only scale, affine scale and offset, and so on... could even be any invertible lambda function
				'offset':0,
				'scale':1,
				}, ...			
			}

```

1) this is the quirks library
```
#this could be built by an user config file...
quirks = {	qid1:Quirk(...),
		qid2:Quirk(...),
		...
	}
```

2) this is the possible handling of quirks in homeassistant's tuya
```
#handle scaling in entity.py
def scale(value, DPCode, device):
    #check if a quirk definition is present for the current device
    q = quirks[device.id] if device id in quirks.keys() else None
    if q:
        #check if scaling is defined for current DPCode (which here represents a data type of known measurement unit)
        tr = q.DPScaling[DPCode] if DPCode in q.DPScaling.keys() else None
        #handle the type of transform
        if tr:
            if tr['type'] == 'linear':
                value = value/tr.scale
            if tr['type'] == 'affine':
                value = (value-tr.offset)/tr.scale
                # and the def scale_back would be value*tr.scale+tr.offset
    else:
        #do it the usual way
        ...

def scale_back(value, DPCode, device):
    ...

#

```

## Notes:

https://github.com/Anze-/tuya-device-sharing-sdk/edit/main/tuya_sharing/

provides: sdk library

https://github.com/home-assistant/core/blob/dev/homeassistant/components/tuya/entity.py#L59

provides:
  >scale_value: tuya -> real
  >
  >scale_value_back: real -> tuya

https://github.com/home-assistant/core/blob/dev/homeassistant/components/tuya/const.py#L93
provides:
```
   DPCode.TEMP_CURRENT =  "temp_current"  # Current temperature in °C  (Line 353)
   ...
```



# Tuya Device Sharing SDK

A Python sdk for Tuya Open API, which provides basic IoT capabilities like device management capabilities, helping you create IoT solutions. 
With diversified devices and industries, Tuya IoT Development Platform opens basic IoT capabilities like device management, AI scenarios, and data analytics services, as well as industry capabilities, helping you create IoT solutions.

## Features
### APIs

- Manager
  - update_device_cache
  - refresh_mq
  - send_commands
  - get_device_stream_allocate
  - query_scenes
  - trigger_scene
  - add_device_listener
  - remove_device_listener
  - unload
- CustomerApi
	- get
	- post
	- put
	- delete
- SharingMQ
	- start
	- stop
	- add_message_listener
	- remove_message_listener
- DeviceRepository
	- query_devices_by_home
	- query_devices_by_ids
	- send_commands
- HomeRepository
	- query_homes
- SceneRepository
	- query_scenes
	- trigger_scene

## Possible scenarios

- [Smart Life Integration](https://github.com/tuya/tuya-smart-life)

## Usage

## Release Note

| version | Description       |
|---------|-------------------|
| 0.1.8   | fix topic error   |
| 0.1.9   | fix mq link id    |

## Installation

`pip3 install tuya-device-sharing-sdk`

## Issue feedback

You can provide feedback on your issue via **Github Issue**.

## License

**tuya-device-sharing-sdk** is available under the MIT license. Please see the [LICENSE](./LICENSE) file for more info.
>>>>>>> main
