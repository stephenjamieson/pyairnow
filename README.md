# pyairnow: a thin Python wrapper for the AirNow API

`pyairnow` is a simple, tested, thin client library for interacting with the
[AirNow](https://www.airnow.gov) United States EPA Air Quality Index API.

- [Python Versions](#python-versions)
- [Installation](#installation)
- [API Key](#api-key)
- [Usage](#usage)
- [Contributing](#contributing)

# Python Versions

`pyairnow` is currently supported and tested on:

* Python 3.6
* Python 3.7
* Python 3.8

# Installation

```python
pip install pyairnow
```

# API Key

You can get an AirNow API key from
[the AirNow API site](https://docs.airnowapi.org/account/request/). Ensure you
read and understand the expectations and limitations of API usage, which can
be found at [the AirNow FAQ](https://docs.airnowapi.org/faq).

# Usage

```python
import asyncio
import datetime

from pyairnow import WebServiceAPI


async def main() -> None:
  client = WebServiceAPI('your-api-key')

  # Get current observational data based on a zip code
  data = await client.observations.zipCode(
    '90001',
    # if there are no observation stations in this zip code, optionally
    # provide a radius to search (in miles)
    distance=50,
  )

  # Get current observational data based on a latitude and longitude
  data = await client.observations.latLong(
    34.053718, -118.244842,
    # if there are no observation stations at this location, optionally
    # provide a radius to search (in miles)
    distance=50,
  )

  # Get forecast data based on a zip code
  data = await client.forecast.zipCode(
    '90001',
    # to get a forecast for a certain day, provide a date in yyyy-mm-dd,
    # if not specified the current day will be used
    date='2020-09-01',
    # if there are no observation stations in this zip code, optionally
    # provide a radius to search (in miles)
    distance=50,
  )

  # Get forecast data based on a latitude and longitude
  data = await client.forecast.latLong(
    # Lat/Long may be strings or floats
    '34.053718', '-118.244842',
    # forecast dates may also be datetime.date or datetime.datetime objects
    date=datetime.date(2020, 9, 1),
    # if there are no observation stations in this zip code, optionally
    # provide a radius to search (in miles)
    distance=50,
  )


asyncio.run(main())
```

By default, the library creates a new connection to AirNow with each coroutine.
If you are calling a large number of coroutines (or merely want to squeeze out
every second of runtime savings possible), an
[`aiohttp`](https://github.com/aio-libs/aiohttp) `ClientSession` can be used
for connection pooling:

```python
import asyncio
import datetime

from aiohttp import ClientSession

from pyairnow import WebServiceAPI


async def main() -> None:
    async with ClientSession() as session:
        client = WebServiceAPI('your-api-key', session=session)

        # ...


asyncio.run(main())
```

# Contributing

1. [Check for open features/bugs](https://github.com/asymworks/pyairnow/issues)
  or [start a discussion on one](https://github.com/asymworks/pyairnow/issues/new).
2. [Fork the repository](https://github.com/asymworks/pyairnow/fork).
3. Install [PipEnv](https://pypi.org/project/pipenv/) and set up the development workspace:
  `pipenv install --dev`
4. Code your new feature or bug fix.
5. Write tests that cover your new functionality.
6. Run tests and ensure 100% code coverage: `make test`
7. Run the linter to ensure 100% code style correctness: `make lint`
8. Update `README.md` with any new documentation.
9. Add yourself to `AUTHORS.md`.
10. Submit a pull request!
