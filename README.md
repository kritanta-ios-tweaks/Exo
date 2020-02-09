# Exo
Modern replacement to [XenInfo](https://junesiphone.com/xeninfo/), created to be used mainly within [Exobar](https://github.com/KritantaDev/Exobar). Unlike XenInfo, Exo's design allows developers to create themes, while writing the most minimal JavaScript code.

***Note**: This documentation assumes you're currently using the latest Exo version available. Please refain from using outdated forks of Exo and always use the latest released version.*

## Example of an Exo-based widget
Exo allows for easy value binding to attributes and contents of any HTML elments
```
<body>
    <div class="grow">Nepeta</div>
    <div class="bg-focus">{battery.percentage}%</div>
    <div class="bg-focus">{time}</div>
    <div class="grow" @class.show="media.playing">{media.artist} - {media.title}</div>
</body>
```
### Binding Exo variables to element content
```
<div>{variable}</div>
```
You can also use multiple of these in any given context:
```
<div>{variable.a}{variable.b} regular text {variable.c}</div>
```
***Note**: There must be no spaces or extra characters inside of {} for this to work. No JavaScript expressions are executed inside of brackets.*

### Binding Exo variables to attributes
```
<div @attr="{variable}">xxx</div>
```

### Binding boolean Exo variables to class names
This only works if the given variable only is 1 or 0; true or false or can be evaulated as boolean inside of JavaScript.
```
<div @class.hidden="variable">xxx</div>
```
***Note**: There are no brackets in this one. You can not use more than one inside of any given class name binding.**

### Binding Exo actions to element events
```
<button @onClick="media.play">play</button>
```
***Note**: This one also has no brackets.*

### Accessing the Exo JavaScript object
Exo is exposed as `window.exo` inside of the JavaScript context of the widget page. In order to make everything easier, we'll assign Exo to the `_` variable.
```
<script type="text/javascript">
    var _ = window.exo;
</script>
```
***Note**: All sections below will use the `_` instead of `window.exo`. All the code you will see should be executed inside of `<script>` unless stated otherwise. Minimal JavaScript knowledge is required.*

## Actions
Exo allows users to execute several actions.
```
_.action('action');
```
or:
```
_.action({
    action: 'name',
    other: 'arguments',
    ...
});
```
You can also bind actions to HTML elements by using the magical `@onXYZ="action"` attribute.

#### Examples
```
<button @class.hidden="media.playing" @onClick="media.play">play</button>
```
or:
```
<button @onClick="{'action': 'log', 'message': 'test log!'}">test log</button>
```

### Available Actions
This section lists all of the action that Exo supports. These can be used in many ways as shown by the examples above.

**General**<br>
- ``log``: Allows you to log data to device's syslog (NSLog). The only argument it has is ``message``. It's shorter alias is ``_.log('message');``
- ``requestUpdate``: Allows you to request the full data dump from Exo. Automatically called on page load. ``_.action('requestUpdate');``

**Media**<br>
- ``media.play``
- ``media.pause``
- ``media.togglePlayback``
- ``media.next``
- ``media.previous``

**Open**<br>
- ``open.url``: Arguements - url
- ``open.application``: Arguments - bundle (of application)

### Custom Actions
Theme developers are able to add their own actions (they only apply to their theme, however)
```
_.setAction('action_name', () => {
    //function code here
});
```
Custom actions allow you to override default actions.

## Configuration
Exo has its own built in configuration system which allows extensibility, making it easy to do the most common tasks without having to rely on update events.

### Options
Exo accepts several options (only one at this moment). Setting these options can be done by using the `setOption` method.
```
_.setOption('option', 'value');
```

#### Available options
| Option name | Type | Acceptable values | Description |
|-------------|------|-------------------|-------------|
| autobind | boolean | `true`, `false` | 	Disables data auto binding inside of Exo widgets, {variable} inside of attributes and content will not be bound to any variables. |

## Events
Exo exposes event binding in case you're not interested in using the provided variable binding.

### Binding events
You can bind events by:
```
_.bind('event_name', function () {
    // Your code here
});
```
***Note**: There is no unbind function at the moment.*

### Bindable events
There are only 2 bindable events:
- ``update``: Called whenever Exo recieves a data update.
    - ``data``: Argument of ``update``; JS object containing all of the **updated** variables
- ``update.VARIABLE_NAME``: Called whenever Exo recieves a data update for the given variable
    - ``value``: Argument of ``update.VARIABLE_NAME``; new value

## Filters
Exo allows you to assign filters to variables to make it easy for you to format a variable without losing out on Exo's binding capabilities.
```
_.setFilter('variable', (value) => {
    return "changed value";
});
```
You can also undo this by calling the method with ``false`` as the second argument.
```
_.setFilter('variable', false);
```

## Manual variable binding
Exo allows for manual variable binding to HTML elements.
```
_('#whatever > .any-css-selector').bindContent('{variable} a b c');
```
Exo also allows for manual autobinding:
```
_('#your .selector').autobind();
```

## Variables
**Time**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| time | string | Current time |
| time.withSeconds | string | Time with seconds |

**Battery**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| battery.charging | boolean | ``true`` if the phone is plugged into power, otherwise ``false`` |
| battery.percentage | integer | Represents current battery percentage |

**Device**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| device.name | string | Device name (i.e. the one you set in Settings) |
| device.type | string | Device type (e.g. iPhone10,4) |

**Media**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| media.playing | boolean | ``true`` if any media is playing, otherwise ``false`` |
| media.album | string | Album title |
| media.artist | string | Artist name |
| media.title | string | Title |
| media.elapsed | float | Playback time in seconds |
| media.duration | float | Track duration |
| media.application | string | Bundle identifier of the currently playing application |
| media.image | string | HTML data URL with a JPEG image of the artwork |

**Memory**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| memory.physical | integer | Device's physical memory |
| memory.used | integer | Used memory |
| memory.free | integer | Free memory |
| memory.total | integer | Total memory (different from physical; used + free) |

**Mobile**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| mobile.network | string | Carrier name |
| mobile.strength.current | integer | Current signal strength (bars) |
| mobile.strength.max | integer | Maximum signal strength (bars) |

**System**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| system.version | string | System version |

**Weather**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| weather.available | boolean | Displays the weather status (Requires the Weather app to be installed and configured) |
| weather.city.name | string | Displays the city name |
| weather.city.county | string | Displays the county name |
| weather.city.state | string | Displays the state name |
| weather.city.stateAbbreviation | string | Displays the abbreviated version of the state name |
| weather.city.country | string | Displays the country name |
| weather.city.countryAbbreviation | string | Displays the abbreviated version of the country name |
| weather.windChill | float | |
| weather.windDirection | float | |
| weather.windSpeed | float | Displays the wind speed |
| weather.humidity | float | |
| weather.visibility | float | |
| weather.pressure | float | |
| weather.dewPoint | float | |
| weather.heatIndex | float | |
| weather.precipitationPast24Hours | float | |
| weather.uvIndex | integer | |
| weather.conditionCode | integer | |
| weather.condition | string | Natural language description of the current weather condition |
| weather.sunsetTime | integer | |
| weather.sunriseTime | integer | |
| weather.moonPhase | integer | |
| weather.temperature.current | float | |
| weather.temperature.feelsLike | float | |
| weather.temperature.unit | string | Can be either "C", or "F" |
| weather.temperature.low | float | |
| weather.temperature.high | float | |
| weather.chanceOfRain | float | |
| weather.dayForecasts | array | Array of day forecasts |
| weather.hourlyForecasts | array | Array of hourly forecasts |

**Weather day forecasts (Object in the array)**: ``weather.dayForecasts``<br>
| Variable name | Type |
|---------------|------|
| day | integer |
| dayOfTheWeek | integer |
| icon | integer |
| temperature.high | float |
| temperature.low | float |

**Weather hourly forecast (object in the array)**: ``weather.hourlyForecasts``<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| time | string | In the format of "HH:MM" |
| conditionCode | integer | |
| percentPrecipitation | float | |
| detail | string | |
| temperature | float | |

**Wifi**<br>
| Variable name | Type | Description |
|---------------|------|-------------|
| wifi.enabled | boolean | Is WiFi enabled? |
| wifi.network | string | Network name |
| wifi.strength.current | integer | Current signal strength (bars) |
| wifi.strength.rssi | integer | Current signal strength (RSSI) |

### Accessing Exo variables from JavaScript
Exo allows you to access these variables directly from JavaScript by exposing this method:
```
_.get(name, skipFilters = false)
```
Getting a variable is as easy as calling ``_.get('mobile.network')``, there's also an optional skipFilters parameter available that skips filters if there any applied to this variable.
