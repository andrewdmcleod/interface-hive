# Overview

This interface layer handles the communication between a Hive client and Hive.

# Usage

## Provides

The Hive deployment is provided by the Hive charm. this charm has to
signal to all its related clients that has become available.

The interface layer sets the following state as soon as a client is connected:

  * `{relation_name}.related` The relation between the client and Hive is established.

The Hive provider can signal its availability through the following methods:

  * `set_installed()` Hive is available.

  * `clear_installed()` Hive is down.

An example of a charm using this interface would be:

```python
@when('hive.started', 'client.related')
def client_present(client):
    client.set_installed()


@when('client.related')
@when_not('hive.started')
def client_should_stop(client):
    client.clear_installed()
```


## Requires

This is the side that a Hive client charm (e.g., Zeppelin)
will use to be informed of the availability of Hive.

The interface layer will set the following state for the client to react to, as
appropriate:

  * `{relation_name}.related` The client is related to Hive and is waiting for Hive to become available.

  * `{relation_name}.available` Hive is ready to be used.

An example of a charm using this interface would be:

```python
@when('zeppelin.installed', 'hive.available')
@when_not('zeppelin.started')
def configure_zeppelin(hive):
    hookenv.status_set('maintenance', 'Setting up Zeppelin')
    zepp = Zeppelin(get_dist_config())
    zepp.start()
    set_state('zeppelin.started')
    hookenv.status_set('active', 'Ready')


@when('zeppelin.started')
@when_not('hive.available')
def stop_zeppelin():
    zepp = Zeppelin(get_dist_config())
    zepp.stop()
    remove_state('zepplin.started')
```


# Contact Information

- <bigdata@lists.ubuntu.com>

