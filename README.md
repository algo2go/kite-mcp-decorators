# kite-mcp-decorators

[![Go Reference](https://pkg.go.dev/badge/github.com/algo2go/kite-mcp-decorators.svg)](https://pkg.go.dev/github.com/algo2go/kite-mcp-decorators)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Function decorators for the algo2go ecosystem: retry-with-backoff,
rate-limit, circuit-breaker, and fallback wrappers. Used by
[`Sundeepg98/kite-mcp-server`](https://github.com/Sundeepg98/kite-mcp-server)
to compose cross-cutting concerns around MCP tool handlers and broker
client calls.

## Why a separate module?

Decorators are a generic primitive — orthogonal to broker semantics,
to MCP tooling, to specific business logic. Hosting them in their own
module:

- Lets unrelated projects in the algo2go family adopt the same retry /
  rate-limit / circuit-breaker semantics without pulling in kite-mcp-server
- Keeps the public API independently versionable (a tightening of retry
  semantics shouldn't bump kite-mcp-server's version)
- Reduces the dep-graph weight of consumers that only need decorators

## Stability promise

**v0.x — unstable.** Function signatures may break between minor versions.
Pin `v0.1.0` deliberately. v1.0 ships only after the public function
surface is reviewed for stability and at least one external consumer
ships against it.

## Install

```bash
go get github.com/algo2go/kite-mcp-decorators@v0.1.0
```

## Public API (decorators.go)

Generic function wrappers via Go 1.25 type parameters:

- `Retry[T](fn, opts)` — exponential backoff retry with configurable
  attempts, base delay, max delay, jitter
- `RateLimit[T](fn, perSecond)` — token-bucket rate limiter
- `CircuitBreaker[T](fn, opts)` — half-open / open / closed state machine
- `Fallback[T](primary, fallback)` — primary-then-fallback chain

## Reference consumer

[`Sundeepg98/kite-mcp-server`](https://github.com/Sundeepg98/kite-mcp-server)
— composes these around `kite-mcp-broker` client calls + `mcp/plugin`
hook chains. The decorator chain is wired in
`mcp/plugin/decorator_chain.go`.

## License

MIT — see [LICENSE](LICENSE).

## Authors

Original design: [Sundeepg98](https://github.com/Sundeepg98) (Zerodha
Tech). Multi-module promotion (2026-05-06): algo2go contributors.
