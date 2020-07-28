# aiosyncthing

Asynchronous Python client for the [Syncthing](https://syncthing.net/) [REST API](https://docs.syncthing.net/dev/rest.html).

Inspired by [python-syncthing](https://github.com/blakev/python-syncthing),
some snippets are copied from [python-fumis](https://github.com/frenck/python-fumis)

NOTE: The package is in active development. *Not all features of the API are implemented.*

## Installation

`pip install aiosyncthing`

## Usage

```python
import asyncio

from aiosyncthing import Syncthing

async def main():

  async with Syncthing("API Key") as client:
    # interact with the client here
    pass

if __name__ == "__main__":
  asyncio.run(main())
```

### Syncthing

Syncthing is the entrypoint class, it acts as an async context manger and provides access to endpoint namespaces.

#### Initialization

```python
    def __init__(
        self,
        api_key, # your API Ley
        url="http://127.0.0.1:8384", # A base URL of the server, https://syncthing.example.com:443/something is also possible
        timeout=DEFAULT_TIMEOUT, # Timeout in seconds
        verify_ssl=True, # Perform SSL verification
        loop=None, # event loop
        session=None # client session,
    )...
```

In case if the api_key is invalid, `aiosyncthing.exceptions.SyncthingError` will be raised on attempt to perform any request except `ping()`. This one only raises `syncthing.exceptions.PingError`.

### System namespace

Provides access to the [System Endpoints](https://docs.syncthing.net/dev/rest.html#system-endpoints)

#### [ping](https://docs.syncthing.net/rest/system-ping-get.html)
Returns none if ping is successful or raises `syncthing.exceptions.PingError`

```python
await client.system.ping()
```

#### [config](https://docs.syncthing.net/rest/system-config-get.html)
Returns a dict with the server config or raises `syncthing.exceptions.SyncthingError`

```python
await client.system.config()
```

#### [status](https://docs.syncthing.net/rest/system-status-get.html)
Returns a dict with the server status or raises `syncthing.exceptions.SyncthingError`

```python
await client.system.status()
```

#### [version](https://docs.syncthing.net/rest/system-version-get.html)
Returns a dict with the server version or raises `syncthing.exceptions.SyncthingError`

```python
await client.system.version()
```

### Database namespace
Provides access to the [Database Endpoints](https://docs.syncthing.net/dev/rest.html#database-endpoints)

#### [status](https://docs.syncthing.net/rest/db-status-get.html)
Returns a dict with the folder status or raises `syncthing.exceptions.SyncthingError`

```python
await client.database.status(folder_id) # eg: 'GXWxf-3zgnU'
```

### Events namespace
Provides access to the [Events Endpoints](https://docs.syncthing.net/dev/rest.html#event-endpoints)

### listen
Is an async generator function that listens to the [Event API](https://docs.syncthing.net/dev/events.html), yields events one by one and hides the complexity of long polling.
Raises `syncthing.exceptions.SyncthingError` in case of error, handles timeouts internally and reconnects to the
endpoint.


```python
async for event in client.events.listen():
  print(event)
```

### last_seen_id
Returns the id of the last received event of the previous batch.

```python
async for event in client.events.listen():
  if events.last_seen_id == 0:
    continue # skip first batch because it's historical data
```

## License

MIT License

Copyright (c) 2020 Gleb Sinyavskiy

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
