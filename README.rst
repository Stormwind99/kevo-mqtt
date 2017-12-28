Kevo Plus MQTT Binding
=======

Remote lock/unlock and get status of your `Kwikset Kevo <https://www.kwikset.com/kevo/plus>`_ locks via MQTT. Simply publish a message like:

.. code:: JSON

  {
    "type": "lock",
    "lock_id": "a95a788a-e774-4561-8fc8-2225b3dccc21"
  }

`lock` and `unlock` types are supported.

A simpler message format is also supported:

.. code:: TEXT

  UNLOCK a95a788a-e774-4561-8fc8-2225b3dccc21

The Lock status will be polled at a user-defined interval and published to the topic as well as printed to the console.

.. code:: JSON

  {
    "lock_id": "a95a788a-e774-4561-8fc8-2225b3dccc21",
    "type": "lockState",
    "state": "locked"
  }

A Kevo Plus bridge is required. You can get the lock IDs manually by logging into mykevo.com, click Details for the lock, click Settings, the lock ID is on the right.

Thanks to Carl Seelye for `pynello <https://github.com/cseelye/pykevoplus>`_.

Run as Docker container
-----------

.. code:: bash

  docker run --name kevoplus -d --restart=always -e KEVO_USERNAME=my@account.com -e KEVO_PASSWORD=password -e KEVO_REFRESH_INTERVAL=500 -e KEVO_LOCK_ID=a95a788a-e774-4561-8fc8-2225b3dccc21 -e MQTT_TOPIC=home/kevo -e MQTT_BROKER=localhost kevo-mqtt:latest

Usage with OpenHAB
-----------

Simply use the `MQTT Binding <http://docs.openhab.org/addons/bindings/mqtt1/readme.html>`_. A custom `transform/kevostate.js` is required.

.. code:: JAVASCRIPT

  (function(i) {
    var json  = JSON.parse(i);
    retun json.state == 'locked' ? 'ON' : 'OFF';
  })(input)

.. code:: TEXT

  Switch DoorLock { mqtt=">[mybroker:home/kevo:command:ON:LOCK a95a788a-e774-4561-8fc8-2225b3dccc21],>[mybroker:home/kevo:command:OFF:UNLOCK a95a788a-e774-4561-8fc8-2225b3dccc21],<[mybroker:home/kevo:command:state:JS(kevostate.js)" }