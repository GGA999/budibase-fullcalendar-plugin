<script>
  import { getContext } from 'svelte';
  import '@fullcalendar/core/locales-all';
  import FullCalendar from 'svelte-fullcalendar';
  import daygridPlugin from '@fullcalendar/daygrid';
  import timeGridPlugin from '@fullcalendar/timegrid';
  import listPlugin from '@fullcalendar/list';
  import { onMount } from 'svelte';
  import { langs, codeLang } from './lang';

  export let eventTypes = [];
  export let language = 'en';
  export let calendarEvent = () => {};

  export let mappingTitle = 'title';
  export let mappingDate = 'date';
  export let mappingStart = 'start';
  export let mappingEnd = 'end';

  export let mappingTitle2 = 'title';
  export let mappingDate2 = 'date';
  export let mappingStart2 = 'start';
  export let mappingEnd2 = 'end';

  export let dataProvider = { rows: [] };
  export let dataProvider2 = { rows: [] };

  export let mappingType = 'type';
  export let mappingType2 = 'type';

  export let mappingColor = '#272727';
  export let mappingColor2 = '#272727';

  export let allday = false;
  export let allday2 = false;

  export let headerOptionsStart = 'prev,next today';
  export let headerOptionsCenter = 'title';
  export let headerOptionsEnd = 'dayGridMonth,timeGridWeek,listWeek';

  let eventsList = [];
  const safeGet = (obj, key, defaultValue) => obj?.[key] ?? defaultValue;
onMount(() => {
  if (eventsList.length > 0) {
    eventsList = [];
  }

  const getEventColor = (type) => {
    if (!type) type = 'default';
    const typeObj = eventTypes.find((eventType) => eventType.name === type);
    return typeObj ? typeObj.color : '#313131';
  };

  const fallbackEvents = [
    {
      title: "Sample Event",
      date: "2023-12-03",
      start: "2023-12-03T10:00:00",
      end: "2023-12-03T12:00:00",
      type: "default"
    }
  ];

  const processDataProvider = (dataProvider, mapping) => {
    const rows = dataProvider?.rows?.length > 0 ? dataProvider.rows : fallbackEvents;

    rows.forEach((event) => {
      const eventType = safeGet(event, mapping.type, 'default');
      const eventColor = getEventColor(eventType);

      eventsList.push({
        title: safeGet(event, mapping.title, 'Untitled Event'),
        date: safeGet(event, mapping.date, new Date().toISOString()),
        start: safeGet(event, mapping.start, new Date().toISOString()),
        end: safeGet(event, mapping.end, new Date().toISOString()),
        color: eventColor,
        event: event,
        allDay: mapping.allDay ?? false,
      });
    });
  };

  processDataProvider(dataProvider, {
    type: mappingType,
    title: mappingTitle,
    date: mappingDate,
    start: mappingStart,
    end: mappingEnd,
    color: mappingColor,
    allDay: allday
  });

  processDataProvider(dataProvider2, {
    type: mappingType2,
    title: mappingTitle2,
    date: mappingDate2,
    start: mappingStart2,
    end: mappingEnd2,
    color: mappingColor2,
    allDay: allday2
  });

  eventsList = eventsList;
});

  let options = {
    headerToolbar: {
      start: headerOptionsStart,
      center: headerOptionsCenter,
      end: headerOptionsEnd,
    },
    plugins: [daygridPlugin, listPlugin, timeGridPlugin],
    initialDate: Date.now(),
    locale: language,
    dayMaxEvents: true,
    eventClick: (event) => {
      calendarEvent({
        value: event.event,
      });
      console.log(event.event.title);
    },
    events: eventsList,
    eventColor: '#378006',
    theme: true,
    ...langs[codeLang(language)],
  };

  const { styleable } = getContext('sdk');
  const component = getContext('component');
</script>

<div use:styleable={$component.styles}>
  <FullCalendar {options} />
</div>
