# hkm-plugin-logger

HKM Kernel plugin providing **`logging.application`** — the `LoggerPort` adapter
the kernel defines but does not implement.

## Why

The kernel declares `AlfacodeTeam\PhpServicePlatform\Kernel\Ports\LoggerPort`
but ships no adapter, and the `ErrorPipeline` only covers escaped Throwables.
Anything non-fatal — "tenant connection failed over", "event listener threw",
"slow query" — needs somewhere to go.

## Channels

| Channel  | Adapter        | Use |
|----------|----------------|-----|
| `file`   | `FileLogger`   | default; appends to `var/logs/app.log` |
| `stream` | `StreamLogger` | containers; stdout, with warning+ to stderr |
| `null`   | `NullLogger`   | tests, or a deliberate opt-out |
| —        | `PsrLoggerBridge` | wrap an existing Monolog/Symfony logger |

Configure with `LOG_CHANNEL`, `LOG_LEVEL`, `LOG_FILE`, or by overriding
`config/logger.php` from your project.

## Notes

- **PSR-3 shaped, not PSR-3 dependent.** The kernel owns its contracts; the
  bridge adapts an existing PSR-3 logger in one line.
- **Adapters never throw.** A logger that fails must not take down the operation
  it was only meant to observe.
- **Do not bind `NullLogger` as the application default.** Logging then looks
  configured and goes nowhere.
- `app.log` is deliberately separate from `errors.log`, which belongs to the
  ErrorPipeline — mixing routine logging in buries the exceptions it surfaces.

## Install

```bash
composer require alfacode-team/hkm-plugin-logger
```

Register `Plugins\Logger\Provider::class` in your project's `withModules([...])`.

## License

MIT
