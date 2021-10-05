---
title: "Scenes"
description: "Instructions on how to setup scenes within Home Assistant."
---

You can create scenes that capture the states you want certain entities to be. For example, a scene can specify that light A should be turned on and light B should be bright red.

```yaml
# Example configuration.yaml entry
scene:
  - name: Romantic
    entities:
      light.tv_back_light: "on"
      light.ceiling:
        state: "on"
        xy_color: [0.33, 0.66]
        brightness: 200
  - name: Movies
    entities:
      light.tv_back_light:
        state: "on"
        brightness: 125
      light.ceiling: off
      media_player.sony_bravia_tv:
        state: "on"
        source: HDMI 1
        state: "on"
```

## How to configure your scene

In the `*.yaml` file, you'll need to configure the following options in order to ensure your scene works:
- name
- description
- entities

```yaml
# Configuration specs
name: "add the name scene here"
  description: "Add a friendly name for your scene here"
  required: true # can be true or false
  type: string
entities:
  description: "add which smart objects you want to control here, and their desired state."
  required: true
  type: list
```

As you can see, there are two ways to define the states of each `entity_id`:

- Define the `state` directly with the entity. Be aware, that `state` needs to be defined.
- Define a complex state with its attributes. You can see all attributes available for a particular entity under `developer-tools -> state`.

Scenes can be activated using the service `scene.turn_on` (there is no 'scene.turn_off' service).

```yaml
# Example automation
automation:
  trigger:
    platform: state
    entity_id: device_tracker.sweetheart
    from: "not_home"
    to: "home"
  action:
    service: scene.turn_on
    target:
      entity_id: scene.romantic
```

## Applying a scene without defining it

With the `scene.apply` service you are able to apply a scene without first defining it via configuration. Instead, you pass the states as part of the service data. The format of the data is the same as the `entities` field in a configuration.

```yaml
# Example automation
automation:
  trigger:
    platform: state
    entity_id: device_tracker.sweetheart
    from: "not_home"
    to: "home"
  action:
    service: scene.apply
    data:
      entities:
        light.tv_back_light:
          state: "on"
          brightness: 100
        light.ceiling: off
        media_player.sony_bravia_tv:
          state: "on"
          source: HDMI 1
```

## Using scene transitions

Both the `scene.apply` and `scene.turn_on` services support setting a transition,
which enables you to smoothen the transition to the scene.

This is an example of an automation that sets a romantic scene, in which the
light will transition to the scene in 2.5 seconds.

```yaml
# Example automation
automation:
  trigger:
    platform: state
    entity_id: device_tracker.sweetheart
    from: "not_home"
    to: "home"
  action:
    service: scene.turn_on
    target:
      entity_id: scene.romantic
    data:
      transition: 2.5
```

Transitions are currently only support by lights, which in their turn, have
to support it as well. However, the scene itself does not have to consist of
only lights to have a transition set.

## Reloading scenes

Whenever you make a change to your scene configuration, you can call the `scene.reload` service to reload the scenes.
