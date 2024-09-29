# Automatic cinema lights

This automation activates a scene when playback on selected devices is started. 
It also restores previous all the lights and states after the viewing.

It works amazingly well with [Plex](https://www.home-assistant.io/integrations/plex/) home server.

In the example I'll be using "Cinema lights" as a scene name.

Create two automations, changing your settings:
```yaml
alias: "AutomaticCinemaLights - start"

trigger:
  - platform: state
    entity_id:
      # 1) Replace with your device entity ID:
      - media_player.plex_plex_web_firefox_windows_2
    to: playing
    from: paused
condition:
  - condition: state
    entity_id: automation.automaticcinemalights_end
    state: "off"
action:
  - action: scene.create
    metadata: {}
    data:
      scene_id: automaticcinemalights_before
      snapshot_entities:
        # 2) List of all your lights and entities that will be restored
        - light.living_room_floor_lamp
        - light.living_room_ceiling_lamp
        - light.living_room_decoration_cats
        - input_boolean.do_not_disturb
  - action: scene.turn_on
    target:
      # 3) your scene ID
      entity_id: scene.cinema_lights
    data: {}
  - action: automation.turn_on
    metadata: {}
    data: {}
    target:
      entity_id: automation.automaticcinemalights_end
mode: single
```

Second one, with entity ID `automation.automaticcinemalights_end` . After creating, **turn it off**:
```yaml
alias: "AutomaticCinemaLights - end"

trigger:
  - platform: state
    entity_id:
      # 1) Replace with your device entity ID:
      - media_player.plex_plex_web_firefox_windows_2
    to: paused
    from: playing
    for:
      # for those who pause frequently
      # don't turn off cinema mode instantly
      hours: 0
      minutes: 1
      seconds: 20
condition: []
action:
  - action: scene.turn_on
    metadata: {}
    target:
      entity_id: scene.automaticcinemalights_before
  - action: scene.delete
    metadata: {}
    data: {}
    target:
      entity_id: scene.automaticcinemalights_before
  - action: automation.turn_off
    metadata: {}
    data:
      stop_actions: false
    target:
      entity_id: automation.automaticcinemalights_end
mode: single

```

