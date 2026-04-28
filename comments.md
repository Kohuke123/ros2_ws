pmst siit saab leida neid punkt ekui tahan mingi punkti koordinaate
- commandiga: ros2 topic echo /planning/mission_planning/checkpoint

valin neli puntki mille järgui than sõita, esimene neist on alguspunt
Croot@autolab-PC-U22-09:/ros2_ws# ros2 topic echo /planning/mission_planning/checkpoint

# Colcon ingore 
- on selleks et ignoreerida mingeid teatud package faile

## checkpoints
Croot@autolab-PC-U22-09:/ros2_ws# ros2 topic echo /planning/mission_planning/checkpoint
header:
  stamp:
    sec: 1777373354
    nanosec: 459739183
  frame_id: map
pose:
  position:
    x: 3716.5947265625
    y: 73687.875
    z: 0.0
  orientation:
    x: 0.0
    y: 0.0
    z: 0.8672619901032069
    w: 0.4978520267330695
---
header:
  stamp:
    sec: 1777373357
    nanosec: 11731514
  frame_id: map
pose:
  position:
    x: 3700.925048828125
    y: 73762.5234375
    z: 0.0
  orientation:
    x: 0.0
    y: 0.0
    z: 0.31958296137519393
    w: 0.9475582994194401
---
header:
  stamp:
    sec: 1777373359
    nanosec: 579736377
  frame_id: map
pose:
  position:
    x: 3818.54736328125
    y: 73798.359375
    z: 0.0
  orientation:
    x: 0.0
    y: 0.0
    z: -0.4794213914923558
    w: 0.8775848274551773
---
header:
  stamp:
    sec: 1777373363
    nanosec: 763761451
  frame_id: map
pose:
  position:
    x: 3784.579833984375
    y: 73751.8671875
    z: 0.0
  orientation:
    x: 0.0
    y: 0.0
    z: -0.9716063294905332
    w: 0.23660333998896438
---


