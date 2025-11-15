# VoidPointer Keyboard - Todos os Arquivos do Projeto ZMK

## Estrutura do Repositório

```
seu-repositorio/
├── .github/
│   └── workflows/
│       └── build.yml
└── config/
    └── boards/
        └── shields/
            └── voidPointer/
                ├── Kconfig.shield
                ├── voidPointer.conf
                ├── voidPointer.keymap
                ├── voidPointer.overlay
                └── voidPointer.shield
```

---

## Arquivo 1: `.github/workflows/build.yml`

```yaml
name: Build ZMK Firmware

on: [push, pull_request, workflow_dispatch]

jobs:
  build:
    runs-on: ubuntu-latest
    container:
      image: zmkfirmware/zmk-build-arm:stable
    name: Build
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Cache west modules
        uses: actions/cache@v4
        env:
          cache-name: cache-zephyr-modules
        with:
          path: |
            modules/
            tools/
            zephyr/
            bootloader/
            zmk/
          key: ${{ runner.os }}-build-${{ env.cache-name }}-${{ hashFiles('manifest-dir/west.yml') }}
          restore-keys: |
            ${{ runner.os }}-build-${{ env.cache-name }}-
            ${{ runner.os }}-build-
            ${{ runner.os }}-

      - name: West Init
        run: west init -l config

      - name: West Update
        run: west update

      - name: West Zephyr export
        run: west zephyr-export

      - name: West Build (voidPointer)
        run: west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=voidPointer -DZMK_CONFIG="${GITHUB_WORKSPACE}/config"

      - name: Rename zmk.uf2
        run: cp build/zephyr/zmk.uf2 voidpointer_nice_nano_v2.uf2

      - name: Archive (voidPointer)
        uses: actions/upload-artifact@v4
        with:
          name: firmware
          path: voidpointer_nice_nano_v2.uf2
```

---

## Arquivo 2: `config/boards/shields/voidPointer/Kconfig.shield`

```kconfig
# Copyright (c) 2024
# SPDX-License-Identifier: MIT

config SHIELD_VOIDPOINTER
    def_bool $(shields_list_contains,voidPointer)
```

---

## Arquivo 3: `config/boards/shields/voidPointer/voidPointer.conf`

```conf
# Copyright (c) 2024
# SPDX-License-Identifier: MIT

CONFIG_ZMK_KSCAN_DEBOUNCE_PRESS_MS=1
CONFIG_ZMK_KSCAN_DEBOUNCE_RELEASE_MS=5

CONFIG_ZMK_SLEEP=y
CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=900000

CONFIG_ZMK_BLE=y
CONFIG_BT_CTLR_TX_PWR_PLUS_8=y

CONFIG_ZMK_PM_SOFT_OFF=y
```

---

## Arquivo 4: `config/boards/shields/voidPointer/voidPointer.shield`

```cmake
# Copyright (c) 2024
# SPDX-License-Identifier: MIT

file(GLOB shield_sources "*.overlay")
list(APPEND shield_sources "voidPointer.keymap")
```

---

## Arquivo 5: `config/boards/shields/voidPointer/voidPointer.overlay`

```c
/*
 * Copyright (c) 2024
 * SPDX-License-Identifier: MIT
 */

/ {
    chosen {
        zmk,kscan = &kscan0;
        zmk,matrix_transform = &default_transform;
    };

    default_transform: keymap_transform_0 {
        compatible = "zmk,matrix-transform";
        columns = <15>;
        rows = <5>;
        map = <
        RC(0,0) RC(0,1) RC(0,2) RC(0,3) RC(0,4) RC(0,5) RC(0,6) RC(0,7) RC(0,8) RC(0,9) RC(0,10) RC(0,11) RC(0,12) RC(0,13) RC(0,14)
        RC(1,0) RC(1,1) RC(1,2) RC(1,3) RC(1,4) RC(1,5) RC(1,6) RC(1,7) RC(1,8) RC(1,9) RC(1,10) RC(1,11) RC(1,12) RC(1,13) RC(1,14)
        RC(2,0) RC(2,1) RC(2,2) RC(2,3) RC(2,4) RC(2,5) RC(2,6) RC(2,7) RC(2,8) RC(2,9) RC(2,10) RC(2,11) RC(2,12) RC(2,13) RC(2,14)
        RC(3,0) RC(3,1) RC(3,2) RC(3,3) RC(3,4) RC(3,5) RC(3,6) RC(3,7) RC(3,8) RC(3,9) RC(3,10) RC(3,11) RC(3,12) RC(3,13) RC(3,14)
        RC(4,0) RC(4,1) RC(4,2) RC(4,3) RC(4,4) RC(4,5) RC(4,6) RC(4,7) RC(4,8) RC(4,9) RC(4,10) RC(4,11) RC(4,12) RC(4,13) RC(4,14)
        >;
    };

    kscan0: kscan {
        compatible = "zmk,kscan-gpio-matrix";
        label = "KSCAN";
        diode-direction = "col2row";

        row-gpios
            = <&gpio0 17 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>
            , <&gpio0 8 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>
            , <&gpio0 6 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>
            , <&gpio0 20 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>
            , <&gpio0 22 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>
            ;

        col-gpios
            = <&gpio0 24 GPIO_ACTIVE_HIGH>
            , <&gpio1 0 GPIO_ACTIVE_HIGH>
            , <&gpio0 11 GPIO_ACTIVE_HIGH>
            , <&gpio1 6 GPIO_ACTIVE_HIGH>
            , <&gpio1 4 GPIO_ACTIVE_HIGH>
            , <&gpio1 1 GPIO_ACTIVE_HIGH>
            , <&gpio1 2 GPIO_ACTIVE_HIGH>
            , <&gpio1 7 GPIO_ACTIVE_HIGH>
            , <&gpio0 9 GPIO_ACTIVE_HIGH>
            , <&gpio0 10 GPIO_ACTIVE_HIGH>
            , <&gpio1 13 GPIO_ACTIVE_HIGH>
            , <&gpio1 15 GPIO_ACTIVE_HIGH>
            , <&gpio0 2 GPIO_ACTIVE_HIGH>
            , <&gpio0 29 GPIO_ACTIVE_HIGH>
            , <&gpio0 31 GPIO_ACTIVE_HIGH>
            ;
    };
};
```

---

## Arquivo 6: `config/boards/shields/voidPointer/voidPointer.keymap`

```c
/*
 * Copyright (c) 2024
 * SPDX-License-Identifier: MIT
 */

#include <behaviors.dtsi>
#include <dt-bindings/zmk/keys.h>
#include <dt-bindings/zmk/bt.h>
#include <dt-bindings/zmk/outputs.h>

/ {
    keymap {
        compatible = "zmk,keymap";

        default_layer {
            bindings = <
                &kp ESC   &kp N1    &kp N2   &kp N3   &kp N4    &kp N5   &kp N6   &kp N7   &kp N8    &kp N9   &kp N0    &kp MINUS &kp EQUAL &kp BSPC  &kp HOME
                &kp TAB   &kp Q     &kp W    &kp E    &kp R     &kp T    &kp Y    &kp U    &kp I     &kp O    &kp P     &kp LBKT  &kp RBKT  &kp BSLH  &kp PG_UP
                &kp CAPS  &kp A     &kp S    &kp D    &kp F     &kp G    &kp H    &kp J    &kp K     &kp L    &kp SEMI  &kp SQT   &kp RET   &kp RET   &kp PG_DN
                &kp LSHFT &kp LSHFT &kp Z    &kp X    &kp C     &kp V    &kp B    &kp N    &kp M     &kp COMMA &kp DOT  &kp FSLH  &kp RSHFT &kp UP    &kp END
                &kp LCTRL &kp LGUI  &kp LALT &kp LALT &kp SPACE &kp SPACE &kp SPACE &kp SPACE &kp SPACE &kp RALT &mo 1    &kp RCTRL &kp LEFT  &kp DOWN  &kp RIGHT
            >;
        };

        fn_layer {
            bindings = <
                &kp GRAVE &kp F1       &kp F2       &kp F3       &kp F4    &kp F5    &kp F6    &kp F7    &kp F8       &kp F9       &kp F10   &kp F11      &kp F12      &kp DEL   &kp INS
                &bt BT_CLR &bt BT_SEL 0 &bt BT_SEL 1 &bt BT_SEL 2 &bt BT_SEL 3 &bt BT_SEL 4 &trans &trans &trans    &trans       &kp PSCRN &kp SLCK     &kp PAUSE_BREAK &trans &trans
                &trans    &trans       &trans       &trans       &trans    &trans    &trans    &trans    &trans       &trans       &trans    &trans       &trans       &trans    &trans
                &trans    &trans       &trans       &trans       &trans    &trans    &trans    &trans    &kp C_MUTE   &kp C_VOL_DN &kp C_VOL_UP &trans    &trans       &trans    &trans
                &trans    &trans       &trans       &trans       &trans    &trans    &trans    &trans    &trans       &trans       &trans    &trans       &trans       &trans    &trans
            >;
        };
    };
};
```

---

## Mapeamento de Pinos (Referência)

### Linhas (Rows):

- row0 → P0.17 (pino 5)
- row1 → P0.08 (pino 2)
- row2 → P0.06 (pino 1)
- row3 → P0.20 (pino 6)
- row4 → P0.22 (pino 7)

### Colunas (Columns):

- col0 → P0.24 (pino 8)
- col1 → P1.00 (pino 9)
- col2 → P0.11 (pino 10)
- col3 → P1.06 (pino 12)
- col4 → P1.04 (pino 11)
- col5 → P1.01 (pino 25)
- col6 → P1.02 (pino 26)
- col7 → P1.07 (pino 27)
- col8 → P0.09 (pino 24)
- col9 → P0.10 (pino 23)
- col10 → P1.13 (pino 22)
- col11 → P1.15 (pino 20)
- col12 → P0.02 (pino 19)
- col13 → P0.29 (pino 18)
- col14 → P0.31 (pino 17)

---

## Layout do Teclado (65% ANSI)

```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───────┬───┐
│Esc│ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │ 8 │ 9 │ 0 │ - │ = │ Backsp│Hom│
├───┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─────┼───┤
│ Tab │ Q │ W │ E │ R │ T │ Y │ U │ I │ O │ P │ [ │ ] │  \  │PgU│
├─────┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴─────┼───┤
│ Caps │ A │ S │ D │ F │ G │ H │ J │ K │ L │ ; │ ' │  Enter │PgD│
├──────┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴────┬───┼───┤
│ Shift  │ Z │ X │ C │ V │ B │ N │ M │ , │ . │ / │Shift │ ↑ │End│
├────┬───┴┬──┴─┬─┴───┴───┴───┴───┴───┴──┬┴───┼───┴┬─┬───┼───┼───┤
│Ctrl│GUI │Alt │         Space          │Alt │ Fn │ │ ← │ ↓ │ → │
└────┴────┴────┴────────────────────────┴────┴────┘ └───┴───┴───┘
```

---

## Camada Fn (Layer 1)

```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───────┬───┐
│ ` │F1 │F2 │F3 │F4 │F5 │F6 │F7 │F8 │F9 │F10│F11│F12│  Del  │Ins│
├───┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─────┼───┤
│BTCFG│BT1│BT2│BT3│BT4│BT5│   │   │   │   │PSc│SLk│Pau│     │   │
├─────┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴─────┼───┤
│      │   │   │   │   │   │   │   │   │   │   │   │        │   │
├──────┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴────┬───┼───┤
│        │   │   │   │   │   │   │Mut│V- │V+ │   │      │   │   │
├────┬───┴┬──┴─┬─┴───┴───┴───┴───┴───┴──┬┴───┼───┴┬─┬───┼───┼───┤
│    │    │    │                        │    │    │ │   │   │   │
└────┴────┴────┴────────────────────────┴────┴────┘ └───┴───┴───┘
```

---

## Instruções de Uso

1. Crie um novo repositório no GitHub
2. Faça upload dos arquivos seguindo EXATAMENTE a estrutura de pastas mostrada
3. O GitHub Actions irá compilar automaticamente
4. Baixe o firmware (.uf2) dos Artifacts
5. Coloque o nice!nano em modo bootloader (duplo reset rápido)
6. Arraste o arquivo .uf2 para o drive que aparecer
7. Pronto!

---

## Configurações Incluídas

- ✅ Debounce: 1ms press / 5ms release
- ✅ Deep sleep: 15 minutos de inatividade
- ✅ Bluetooth: Potência +8dBm
- ✅ 5 perfis Bluetooth
- ✅ Layout ANSI 65% padrão
- ✅ Layer Fn com F1-F12 e controles multimídia

---

## Notas Importantes

⚠️ **ATENÇÃO**: Os nomes dos arquivos são case-sensitive!

- A pasta deve se chamar `voidPointer` (com P maiúsculo)
- Todos os arquivos devem ter `voidPointer` com P maiúsculo
- O shield no build.yml também deve ser `voidPointer`

⚠️ **Estrutura de pastas**: Respeite EXATAMENTE a estrutura de pastas mostrada, caso contrário o build falhará.
