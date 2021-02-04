==============
   REST API
==============

The REST APIs are undergoing active development, so some of the below APIs may change in time.
APIs marked as (upcoming) are yet to be added.

/api/info
===============

Information about LedFx

.. rubric:: GET

Returns basic information about the LedFx instance as JSON

.. code-block:: json

    {
      "url": "http://127.0.0.1:8888",
      "name": "LedFx",
      "version": "0.3.0"
    }

/api/config
===============

LedFx Configuration

.. rubric:: GET

Returns the current configuration for LedFx as JSON

/api/log (upcoming)
=========================

LedFx Logging

.. rubric:: GET

Returns the error logs for the currently active LedFx session

/api/schema/
=========================

LedFx Schema Api

.. rubric:: GET /api/schema/

Returns all LedFx schemas for devices, effects, and integrations as JSON

/api/schema/<schema_type>
============================

Query a specific LedFx schema with the matching *schema_type* as JSON

.. rubric:: GET /api/schema/<schema_type>

Returns the LedFx schema with the matching *schema_type* as JSON

- *devices*: Returns all the devices registered with LedFx

- *effects*: Returns all the valid schemas for an LedFx effect

- *integrations*: Returns all the integrations registered with LedFx

/api/devices
=========================

Query and manage devices connected to LedFx

.. rubric:: GET

Get configuration of all devices

.. rubric:: POST

Adds a new device to LedFx based on the provided JSON configuration

/api/devices/<device_id>
=========================

Query and manage a specific device with the matching *device_id* as JSON

.. rubric:: GET

Returns information about the device

.. rubric:: PUT

Modifies the information pertaining to the device and returns the new device as JSON

.. rubric:: DELETE

Deletes a device with the matching *device_id*

/api/effects
=========================

Query and manage all effects

.. rubric:: GET

Returns all the effects currently created in LedFx as JSON

.. rubric:: POST (upcoming)

Create a new Effect based on the provided JSON configuration

/api/effects/<effect_id>
=========================

Query and manage a specific effect with the matching *effect_id* as JSON

.. rubric:: GET

Returns information about the effect

.. rubric:: PUT (upcoming)

Modifies the configuration of the effect and returns the new configuration as JSON

.. rubric:: DELETE (upcoming)

Deletes the effect with the matching *effect_id*.

/api/devices/{device_id}/effects
==================================

Endpoint linking devices to effects with the matching *device_id* as JSON

.. rubric:: GET

Returns the active effect config of a device

.. rubric:: PUT

Update the active effect config of a device based on the provided JSON configuration
If config given is "RANDOMIZE", the active effect config will be automatically generated to random values

.. rubric:: POST

Set the device to a new effect based on the provided JSON configuration

.. rubric:: DELETE

Clear the active effect of a device

/api/devices/<device_id>/presets
================================

Endpoint linking devices to effect presets (pre-configured effect configs) with the matching *device_id* as JSON

.. rubric:: GET

Get preset effect configs for active effect of a device

.. rubric:: PUT

Set active effect config of device to a preset

.. rubric:: POST

Save configuration of device's active effect as a custom preset for that effect

.. rubric:: DELETE

Clear effect of a device

/api/effects/<effect_id>/presets
===================================

Endpoint for querying and managing presets (pre-configured effect configs) for each effect with the matching *effect_id* as JSON

.. rubric:: GET

Get all presets for an effect

.. rubric:: GET

Rename a preset

.. rubric:: DELETE

Delete a preset

/api/scenes
================================

Endpoint for managing scenes. Active effects and configs of all devices can be saved as a "scene".

.. rubric:: GET

Get all saved scenes

.. rubric:: PUT

Set effects and configs of all devices to those specified in a scene

.. rubric:: POST

Save effect configuration of devices as a scene

.. rubric:: DELETE

Delete a scene

/api/integrations
================================

Endpoint for managing integrations. Integrations are written to allow ledfx to communicate with other software, and
vice versa.

.. rubric:: GET

Get info of all integrations
Optional, send request body to get specific info of integrations
Any of: ["id", "type", "active", "status", "data", "config"]

example:

.. code-block:: json
    {
    "info":"status"
    }

STATUS REFERENCE
0: disconnected
1: connected
2: disconnecting
3: connecting

.. rubric:: PUT

Toggle an integration on or off

example:

.. code-block:: json

    {
    "id": "myqlc"
    }

.. rubric:: POST

Create a new integration, or update an existing one

.. code-block:: json

    {
    "type": "qlc",
    "config": {
        "description": "QLC Test",
        "ip_address": "127.0.0.1",
        "name": "myQLC+",
        "port": 9999
        }
    }

.. code-block:: json

    {
    "type": "spotify",
    "config": {
        "description": "Spotify triggers for party",
        "name": "Party Spotify"
        }
    }

.. rubric:: DELETE

Delete an integration, erasing all its configuration and data.

.. code-block:: json

    {
    "id": "myqlc"
    }

NOTE: This does not turn off the integration, it deletes it entirely! (though it will first turn off..)

/api/integrations/qlc/<integration_id>
==============================================

Endpoint for querying and managing a QLC integration.

.. rubric:: GET

Returns info from the QLC+ integration.

Specify "info", one of: ``["event_types", "qlc_widgets", "qlc_listeners"]``

*event_types*: retrieves a list of all the types of events and associated filters a qlc listener can subscribe to

*qlc_widgets*: retrieves a list of all the widgets that can be modified, formatted as [(ID, Type, Name),...] for "type":

- "Buttons" can be set to either off (0) or on (255)

- "Audio Triggers" are either off (0) or on (255)

- "Sliders" can be anywhere between 0 and 255

*qlc_listeners*: retrieves a list of all of the events that QLC is listening to, and their associated widget value payloads

.. code-block:: json

    {
    "info": "qlc_listeners"
    }

.. rubric:: PUT

Toggle a QLC+ event listener on or off, so that it will or will not send its payload to set QLC+ widgets

.. code-block:: json

    {
    "event_type": "scene_set",
    "event_filter": {
        "scene_name": "My Scene"
        }
    }

.. rubric:: POST

Add a new QLC event listener and QLC+ payload or update an existing one if it exists with same event_type and event_filter
The "qlc_payload" is a dict of {"widget_id": value} that will be sent to QLC+

.. code-block:: json

    {
    "event_type": "scene_set",
    "event_filter": {
        "scene_name": "My Scene"
        },
    "qlc_payload": {
        "0":255,
        "1":255,
        "2":169
        }
    }

.. rubric:: DELETE

Delete a QLC event listener, and associated payload data.

.. code-block:: json

    {
    "event_type": "scene_set",
    "event_filter": {
        "scene_name": "My Scene"
        }
    }

NOTE: This does not turn off the integration, it deletes it entirely! (though it will first turn off..)

/api/integrations/spotify/<integration_id>
=============================================
Endpoint for querying and managing a Spotify integration.

.. rubric:: GET

Get all the song triggers

.. rubric:: PUT

Update a song trigger
[TODO]

.. rubric:: POST

Create a new song trigger

.. code-block:: json

    {
    "scene_id": "my_scene",
    "song_id": "347956287364597",
    "song_name": "Really Cool Song",
    "song_position": "43764",
    }

.. rubric:: DELETE

Delete a song trigger

.. code-block:: json

    {
    "trigger_id": "Really Cool Song - 43764",
    }

===================
   WebSocket API
===================

In addition to the REST APIs LedFx has a WebSocket API for streaming realtime data. The primary use for this is for things like effect visualizations in the frontend.

Will document this further once it is more well defined. The general structure will be event registration based.