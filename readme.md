# Quasar Calendar
An event display calendar for the Quasar framework. This is still a work in progress project but we're putting in functionality and squashing bugs on a consistent basis.

![screenshot](https://stormseed.github.io/quasar-calendar-demo/statics/quasar_calendar_snap.png)

## Demo

You can see a demo of the calendar components with event data at:

[Quasar calendar demo](https://stormseed.github.io/quasar-calendar-demo)

## Setup

```shell
npm install quasar-calendar
```

Add Calendar to you .vue page similar to a a Quasar component

```js
import { Calendar } from 'quasar-calendar'
```

or import individual components

```js
import {
  CalendarMonth,
  CalendarAgenda,
  CalendarMultiDay
} from 'quasar-calendar'
```

In your template, you can just put in a calendar viewer using the current date as the start date

```html
<calendar />
```

Or you can pass in parameters to customize

```html
<calendar-month
  :start-date="Date('2019-01-01')"
  :events="someEventObject"
  :sunday-first-day-of-week="true"
  calendar-locale="fr"
  calendar-timezone="Europe/Paris"
/>
```

## Event data format

The event data format is meant to be a subset of the [Google Calendar v3 API](https://developers.google.com/google-apps/calendar/v3/reference/events) (*this is still a work in progress*). Events should be passed in as an array of objects. Each object can have elements like in this example:

```js
[
  {
    id: 1,
    summary: 'Test event',
    description: 'Some extra info goes here',
    location: 'Office of the Divine Randomness, 1232 Main St., Denver, CO',
    start: {
      dateTime: '2018-02-16T14:00:00', // ISO 8601 formatted
      timeZone: 'America/New_York' // Timezone listed as a separate IANA code
    },
    end: {
      dateTime: '2018-02-16T16:30:00',
      timeZone: 'American/New_York'
    },
    color: 'positive',
    attendees: [
      {
        id: 5,
        email: 'somebody@somewhere.com',
        displayName: 'John Q. Public',
        organizer: false,
        self: false,
        resource: false
      }
    ]
  },
  {
    id: 2,
    summary: 'Test all-day event',
    description: 'Some extra info goes here',
    start: {
      date: '2018-02-16' // A date variable indicates an all-day event
    },
    end: {
      date: '2018-02-19'
    }
  },
    {
      id: 3,
      summary: 'Some other test event',
      description: 'Some extra info goes here',
      start: {
        dateTime: '2018-02-17T10:00:00+0500', // timezone embedded in dateTime
      },
      end: {
        dateTime: '2018-02-17T12:30:00+0500',
      },
    },
]
```

Each object needs to have a unique ID. The date time should be in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format. A value in the optional `timeZone` field will override the timezone.

## Custom event detail handling

By default we use our own event detail popup when an event is clicked. You can override this and use your own by doing a few things:

* Pass in an event reference string
* Prevent the default event detail from showing up
* Listen for a click event to trigger your own detail content

Each calendar is given a random reference string so that we can distinguish between multiple calendars on a page. You can override this and pass in a string so that you can listen for events from that calendar. In this case, if we pass in the string `MYCALENDAR`, the Vue.js event `click-event-MYCALENDAR` would fire on the [global event bus](http://quasar-framework.org/components/global-event-bus.html) when a calendar event is clicked on. 

So to implement, be sure to have `prevent-event-detail` and `event-ref` set when you embed a calendar component:

```html
<calendar
  event-ref="MYCALENDAR"
  :prevent-event-detail="true"
  :events="someEventObject"
/>
```

And then somewhere be sure to be listening for a click event on that calendar:

```js
this.$root.$on(
  'click-event-MYCALENDAR',
  function (eventDetailObject) {
    // do something here
  }
)
```

## Individual Vue components

The usable components of `Calendar`, `CalendarMonth`, `CalendarMultiDay` and `CalendarAgenda` share the following properties:

| Vue Property | Type | Description |
| --- | --- | --- |
| `start-date` | JavaScript Date or Luxon DateTime | A JavaScript Date or Luxon DateTime object that passes in a starting display date for the calendar to display. |
| `sunday-first-day-of-week` | Boolean | If true this will force month and week calendars to start on a Sunday instead of the standard Monday. |
| `calendar-locale` | String | A string setting the locale. We use the Luxon package for this and they describe how to set this [here](https://moment.github.io/luxon/docs/manual/intl.html). This will default to the user's system setting. |
| `calendar-timezone` | String | Manually set the timezone for the calendar. Many strings can be passed in including `UTC` or any valid [IANA zone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). This is better explained [here](https://moment.github.io/luxon/docs/manual/zones.html). |
| `event-ref` | String | Give the calendar component a custom name so that events triggered on the global event bus can be watched. |
| `prevent-event-detail` | Boolean | Prevent the default event detail popup from appearing when an event is clicked in a calendar. |

In addition, each individual components have the following properties:

### Calendar
| Vue Property | Type | Description |
| --- | --- | --- |
| `tab-labels` | Object | Passing in an object with strings that will override the labels for the different calendar components. Set variables for `month`, `week`, `threeDay`, `day` and `agenda`. Eventually we will replace this with language files and will use the `calendar-locale` setting. |

### CalendarMultiDay

| Vue Property | Type | Description |
| --- | --- | --- |
| `num-days` | Number | The number of days the multi-day calendar. A value of `1` will change the header to be more appropriate for a single day. |
| `nav-days` | Number | This is how many days the previous / next navigation buttons will jump. |
| `force-start-of-week` | Boolean | Default is `false`. This is appropriate if you have a week display (7 days) that you want to always start on the first day of the week. |

### CalendarAgenda

| Vue Property | Type | Description |
| --- | --- | --- |
| `num-days` | Number | The number of days to initially display and also the number of additional days to load up when the user scrolls to the bottom of the agenda. |

## Roadmap

Our near-term roadmap is as follows:

| Version | Description |
| --- | --- |
| v0.3.x | Have some basic editing abilities such as changing the data for an event and drag and drop editing. |
