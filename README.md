# opencode-tps

A plugin for [OpenCode](https://opencode.ai) that displays a live **Tokens Per Second (TPS)** meter in the terminal UI.

![TPS Meter Demo](./assets/plugin-tps.gif)

## What it does

This plugin adds a real-time TPS (tokens per second) counter to your OpenCode interface, showing how fast the AI model is generating tokens during streaming responses.

The meter appears in the bottom-right corner of the TUI and updates dynamically as the model streams output.

### Features

- **Subagent-aware**: Shows TPS even when a subagent is generating on behalf of the current session — seamlessly falls through to active child sessions.
- **Smooth readout**: Uses exponential moving average (EMA) with α = 0.35 to filter out fluctuations for a stable, readable display.
- **Persistent display**: Remembers the last known TPS value instead of immediately showing "-" when generation pauses.

## Installation

### Via OpenCode CLI

```bash
opencode plugin @williamcr01/opencode-tps
```

### Via npm

1. Add the plugin to your `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["@williamcr01/opencode-tps"]
}
```

then

```bash
cd ~/.opencode
npm install @williamcr01/opencode-tps
```

## Requirements

- OpenCode >= 1.3.14
- OpenCode TUI (Web UI does not support this plugin)

## How it works

The plugin hooks into OpenCode's message streaming events to calculate real-time token generation speed:

- Tracks token deltas as they arrive from the AI model
- Calculates TPS based on a 5-second rolling window
- Smooths the displayed value with an exponential moving average (EMA) to reduce fluctuations
- Remembers the last known TPS when generation pauses, so the readout doesn't flicker to "-" between tokens
- Falls through to active subagent sessions — if the current session is idle but a child session is generating, the child's TPS is shown
- Tracks session parent-child relationships via the `session.created` event for accurate subagent detection
- Automatically clears when streaming completes or errors occur

## License

MIT
