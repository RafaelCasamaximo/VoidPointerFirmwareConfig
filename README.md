# voidPointer Keyboard - ZMK Firmware

Firmware ZMK para teclado customizado 65% ANSI com nRF52840 (nice!nano v2 compatible).

## Estrutura do Repositório

```
seu-repositorio/
├── .github/
│   └── workflows/
│       └── build.yml
├── config/
│   ├── boards/
│   │   └── shields/
│   │       └── voidpointer/
│   │           ├── Kconfig.shield
│   │           ├── voidpointer.conf
│   │           ├── voidpointer.keymap
│   │           ├── voidpointer.overlay
│   │           └── voidpointer.shield
│   └── west.yml (opcional, se precisar)
└── README.md
```

## Arquivos Criados

### 1. `build.yml`

- Workflow do GitHub Actions para compilar o firmware
- Coloque em: `.github/workflows/build.yml`

### 2. `voidpointer.overlay`

- Definição da matriz do teclado e mapeamento de pinos
- Coloque em: `config/boards/shields/voidpointer/voidpointer.overlay`

### 3. `voidpointer.keymap`

- Layout das teclas (camada base e função)
- Coloque em: `config/boards/shields/voidpointer/voidpointer.keymap`

### 4. `voidpointer.conf`

- Configurações do firmware (debounce, sleep, bluetooth)
- Coloque em: `config/boards/shields/voidpointer/voidpointer.conf`

### 5. `Kconfig.shield`

- Configuração Kconfig do shield
- Coloque em: `config/boards/shields/voidpointer/Kconfig.shield`

### 6. `voidpointer.shield`

- Definição de arquivos do shield
- Coloque em: `config/boards/shields/voidpointer/voidpointer.shield`

## Como Usar

1. Crie um novo repositório no GitHub
2. Clone o repositório localmente
3. Crie a estrutura de pastas conforme mostrado acima
4. Copie cada arquivo para sua respectiva localização
5. Faça commit e push para o GitHub
6. O GitHub Actions irá compilar automaticamente o firmware
7. Baixe o arquivo `.uf2` dos artifacts da Action
8. Coloque o nice!nano em modo bootloader (duplo reset)
9. Copie o arquivo `.uf2` para o drive que aparecer
10. Pronto! Seu teclado está programado

## Hardware

- **MCU**: nRF52840 (nice!nano v2 compatible)
- **Layout**: 65% ANSI
- **Matriz**: 5 linhas × 15 colunas
- **Conexão**: Bluetooth (até 5 dispositivos)

## Funcionalidades

- ✅ Layout ANSI 65% padrão
- ✅ Bluetooth com suporte a 5 perfis
- ✅ Layer de função (Fn) com F1-F12
- ✅ Controles de mídia (Volume, Mute)
- ✅ Debounce otimizado (1ms press, 5ms release)
- ✅ Deep sleep após 15 minutos de inatividade
- ✅ Potência de transmissão +8dBm

## Customização

Para modificar o layout:

1. Edite `config/boards/shields/voidpointer/voidpointer.keymap`
2. Consulte a documentação do ZMK: https://zmk.dev/docs/keymaps

## Licença

MIT
