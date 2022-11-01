# ha-templates

#### Phone alarm trigger
Triggers an event x minutes before alarm goes off. (replace x)

```{{now().strftime('%a %h %d %H:%M %Z %Y') == (((state_attr('sensor.pixel_6_next_alarm', 'Time in Milliseconds') | int / 1000) - (60 * x)) | timestamp_custom('%a %h %d %H:%M %Z %Y'))}}```

#### Sensor to count on lights/switches

```template:
  - sensor:
      - name: lights_on_counter
        unit_of_measurement: 'lights'
        state: >
          {{ states.switch | selectattr('state', 'eq', 'on') | list | count + states.light | selectattr('state', 'eq', 'on') | list | count }}
```

#### Telegram integration

```
# Example configuration.yaml entry for the Telegram Bot
telegram_bot:
  - platform: polling
    api_key: [api-key]
    allowed_chat_ids:
      - 123456789 # example: 123456789 for the chat_id of a user

# Example configuration.yaml entry for the notifier
notify:
  - platform: telegram
    name: TelegramNotifier
    chat_id: 123456789
```

#### Telegram command trigger

```
platform: event
event_type: telegram_command
event_data:
  command: /turnofflivingroom
```

#### Greeting

```
{% set time = now().hour %}
{% if (time >= 18) %}
  Good Evening, {{user}}!
{% elif (time >= 12) %}
  Good Afternoon, {{user}}!
{% elif (time >= 5) %}
  Good Morning, {{user}}!
{% else %}
  Hello, {{user}}!
{% endif %}
```
