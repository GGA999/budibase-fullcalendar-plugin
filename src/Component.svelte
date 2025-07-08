<script>
  import { getContext } from 'svelte';
  import '@fullcalendar/core/locales-all';
  import FullCalendar from 'svelte-fullcalendar';
  import daygridPlugin from '@fullcalendar/daygrid';
  import timeGridPlugin from '@fullcalendar/timegrid';
  import listPlugin from '@fullcalendar/list';
  import { onMount } from 'svelte';
  import { langs, codeLang } from './lang';

  // Nuovo prop per il mapping dei tipi di evento con i colori
  export let eventTypes = []; // [{ name: 'meeting', color: '#ff0000' }, ...]
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

  export let mappingType = 'type'; // Nuovo: Associazione del tipo di evento
  export let mappingType2 = 'type'; // Nuovo: Per il secondo dataProvider

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
      if (!type) type = 'default'; // Fallback in caso il tipo non sia fornito
      const typeObj = eventTypes.find((eventType) => eventType.name === type);
      return typeObj ? typeObj.color : '#313131'; // Default se il tipo non è trovato
    };

    if (dataProvider?.rows?.length > 0) {
      dataProvider.rows.forEach((event) => {
        const eventType = safeGet(event, mappingType, 'default');
        const eventColor =
          getEventColor(eventType) || mappingColor || '#313131';

        eventsList.push({
          title: safeGet(event, mappingTitle, 'Untitled Event'),
          date: safeGet(event, mappingDate, new Date().toISOString()),
          start: safeGet(event, mappingStart, new Date().toISOString()),
          end: safeGet(event, mappingEnd, new Date().toISOString()),
          color: eventColor,
          event: event,
          allDay: allday ?? false,
        });
      });
    }

    if (dataProvider2?.rows?.length > 0) {
      dataProvider2.rows.forEach((event) => {
        const eventType = safeGet(event, mappingType2, 'default');
        const eventColor2 =
          getEventColor(eventType) || mappingColor2 || '#eb4034';

        eventsList.push({
          title: safeGet(event, mappingTitle2, 'Untitled Event'),
          date: safeGet(event, mappingDate2, new Date().toISOString()),
          start: safeGet(event, mappingStart2, new Date().toISOString()),
          end: safeGet(event, mappingEnd2, new Date().toISOString()),
          color: eventColor2,
          event: event,
          allDay: allday2 ?? false,
        });
      });
    }

    eventsList = eventsList; // Trigger Svelte reattivity
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
