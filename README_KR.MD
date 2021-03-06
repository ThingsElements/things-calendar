# things-calendar

## 1. 설명
### 이는 calendar를 쉽게 표현하기 위한 컴포넌트로서, 여기에는 fullcalendar.js library가 포함되어 있다.
```html
<dom-module id="things-order-calendar">
  <template>
    <style>
      :host {
        display: block;
      }
    </style>

    <things-resource-detail-view-opener
      close-after-trx
      id="work-order-opener"
      resource-name="WorkOrder"
      trx-event-name="things-work-order-calendar-saved"
      trx-callback="[[trxCallback]]">
    </things-resource-detail-view-opener>

    <things-calendar
      id="order-calendar"
      schedule-url="work_orders"
      event-query-fields="[[queryFields]]"
      event-title-field="title"
      event-time-field="order_date"
      event-start-field="order_date"
      event-background-color-render="{{backColorRender}}"
      event-font-color-render="{{fontColorRender}}"
      event-border-color-render="{{borderColorRender}}"
      events="[[events]]"
      on-things-calendar-event-tap="onEventTap"
      locale="[[globals.locale]]">
    </things-calendar>
  </template>

  <script>
    Polymer({
      is: 'things-order-calendar',

      behaviors: [
        Things.GlobalBehavior
      ],

      properties: {
        /**
         * for render background color
         * @type {function}
         */
        backColorRender: {
          type: Object,
          value : function(event, calendar) {
            return function(event,calendar) {
              var backgroundColor = '#ffffff';

              // WAITING, RUNNING, LOCKED, FINISHED, CLOSED, CANCELED
              switch(event.status) {
                case 'WAITING':
                  backgroundColor = '#C55A11';
                  break;
                case 'RUNNING':
                  backgroundColor = '#34949F';
                  break;
                case 'CLOSED':
                  backgroundColor = '#5C769F';
                  break;
                default:
                  backgroundColor = '#2C769F';
              }

              return backgroundColor
            }
          }
        },

        /**
         * for render font color
         * @type {function}
         */
        fontColorRender: {
          type: Object,
          value : function(event, calendar) {
            return function(event, calendar) {
              return '#ffffff';
            }
          }
        },

        /**
         * for render border color
         * @type {function}
         */
        borderColorRender: {
          type: Object,
          value : function(event, calendar) {
            return function(event, calendar) {
              return '#ffffff';
            }
          }
        },

        /**
         * query fields
         * @type {Array}
         */
        queryFields: {
          type: Array,
          value: function() {
            return [ {
              name : 'status',
              operator : 'noteq',
              value : 'CANCELED'
            } ];
          }
        },

        trxCallback: function() {
          return function() {
            console.log('work-calendar trx callback');
          }.bind(this);
        }
      },

      listeners: {
        'order-calendar.things-calendar-event-response' : 'scheduleDataChanged'
      },
      /**
       * Schedule Data 변경시 타이틀 추가
       */
      scheduleDataChanged: function(event) {
        event.preventDefault();
        var schedules = event.detail.event;
        if(schedules) {
          schedules.forEach(function(schedule) {
            schedule.title = schedule.name + ' (' + schedule.status + ')\n' + schedule.material.name + ' (' + schedule.plan_qty + '/' + (schedule.actual_qty ? schedule.actual_qty : 0) + ')';
          });
        }
        this.set('events',schedules);
      },

      /**
       * when event tapped show resource information
       */
      onEventTap: function(event) {
        var resource = event.detail.event;
        var opener = this.$['work-order-opener'];
        opener.showDetailView(resource);
      }
    });
  </script>
</dom-module>
```


## 2. 개발
### 2.1 Polymer-CLI 설치

먼저 [Polymer CLI](https://www.npmjs.com/package/polymer-cli)가 설치되어 있는지 확인한다. 다음, `polymer serve`를 실행하여 로컬에서 응용 프로그램을 제공한다.

### 2.2 Application 실행

```
$ polymer serve
```

### 2.3 Application 빌드

```
$ polymer build
```

아래 명령어로 `build/bundled`나 `build/unbundled`에서 서버를 띄울 수 있다.

```
$ polymer serve build/bundled
```

### 2.4 Tests 실행

```
$ polymer test
```

[web-component-tester](https://github.com/Polymer/web-component-tester)에서 설명한 대로, 테스트 설정은 이미 완료되어 있다.
아래 명령어로 테스트를 실행할 수 있다.
```
$ polymer test
```
