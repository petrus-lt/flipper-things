# U2F

Un ajout de règle udev est nécessaire pour faire fonctionner u2f correctement :


```
$ cat /etc/udev/rules.d/42-flipperzero.rules
KERNEL=="hidraw*", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5741", MODE="0660", GROUP="plugdev", TAG+="uaccess", TAG+="udev-acl", TAG+="snap_firefox_firefox"
```

# GPIO

## I2C

Pilotage des 8 ports du relay board via un PCF8574 et l'appli i2c

```

                                                                        ┌─────────────────────────┐
                                                                        │     FLIPPER ZERO        │
                                                                        │                         │
                                                                        │                   xx    │
                                                                        │                   xx    │
                                                                        │              xx         │
                                                                        │            x    x       │
                                                                        │           x  xx  x      │
┌───────────────────────────────────────────────┐                       │           x  xx  x      │
│                                           GND ├───────────────────────┤ GND        x    x       │
│                                               │                       │              xx         │
│         PCF8574F                          SCL ├───────────────────────┤ PC0                     │
│                                               │                       │                         │
│                                           SDA ├───────────────────────┤ PC1     ┌────────────┐  │
│                                               │                       │         │            │  │
│          01 23 45 67                      Vcc ├───────────────────────┤ Vcc3.3  │            │  │
└──────────┬┬─┬┬─┬┬─┬┬──────────────────────────┘                       │         │            │  │
           ││ ││ ││ ││                                                  │         │            │  │
           ││ ││ ││ ││                                                  │         │            │  │
           ││ ││ ││ ││                                                  │         │            │  │
           ││ ││ ││ ││                                                  │         │            │  │
           ││ ││ ││ ││                                                  │         │            │  │
           ││ ││ ││ ││ ┌────────────────────────────────────────────────┤ GND     │            │  │
           ││ ││ ││ ││ │                                                │         │            │  │
           ││ ││ ││ ││ │                                                │         │            │  │
           ││ ││ ││ ││ │ ┌──────────────────────────────────────────────┤ Vcc5    │            │  │
           ││ ││ ││ ││ │ │                                              │         │            │  │
           ││ ││ ││ ││ │ │                                              │         └────────────┘  │
           ││ ││ ││ ││ │ │                                              │                         │
           ││ ││ ││ ││ │ │                                              │                         │
           ││ ││ ││ ││ │ │                                              │                         │
           ││ ││ ││ ││ │ │                                              └─────────────────────────┘
           ││ ││ ││ ││ │ │
┌──────────┴┴─┴┴─┴┴─┴┴─┴─┴────────────────────────────────────────────────────────────────────────┐
│          12 34 56 78 G V                                                                        │
│                      N c                                                                        │
│                      D c                                                                        │
│                                                                                                 │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │         │ │
│ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ │
│                                     Sainsmart 8 relay module                                    │
└─────────────────────────────────────────────────────────────────────────────────────────────────┘
```
