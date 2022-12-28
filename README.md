**Quelques notes sur certaines utilisation du Flipper Zero pas forcément très documentées**

# U2F

## udev
Un ajout de règle udev est nécessaire pour faire fonctionner u2f correctement :


```
$ cat /etc/udev/rules.d/42-flipperzero.rules
KERNEL=="hidraw*", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5741", MODE="0660", GROUP="plugdev", TAG+="uaccess", TAG+="udev-acl", TAG+="snap_firefox_firefox"
```
## ssh

Aussi simple que créer une clé ssh, avec le Flipper en mode u2f : `ssh-keygen -t ecdsa-sk` et zou.

# GPIO

## I2C

Pilotage des 8 ports du relay board [Sainsmart](https://www.sainsmart.com/products/8-channel-5v-relay-module) via un [PCF8574](https://www.waveshare.com/pcf8574-io-expansion-board.htm) et l'appli [i2c-tools](https://github.com/NaejEL/flipperzero-i2ctools).

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

Depuis l'appli on peut vérifier que si le PCF8574 est bien sur 0x20 comme par défaut, et envoyer les commandes pour piloter les relais individuellement.
| Valeur | Signification |
| ----------- | ----------- |
| 0x00 | Tous les relais allumés |
| 0xff | Tous les relais éteints |
