# Xray 26.7.28 upgrade

This build uses upstream Xray v26.7.28 (5ca6f4b7d4dc) with this project's
AnyTLS, TUIC/singquic and Shadowsocks extensions preserved. Go 1.26.5 and
`GOEXPERIMENT=jsonv2` are used for tests, containers and release builds.

## Changes

- Adapt custom dispatcher sniffing exclusions to the new domain/IP matchers.
- Adapt TUIC to validated address conversion in Xray's sing bridge.
- Fix AnyTLS stream opening, response timing, payload flushing and padding;
  preserve a first payload coalesced with its destination address.
- Preserve VMess/VLESS transport selection when optional settings are absent.
- Fix Hysteria2 obfs with no enforced bandwidth and escape obfs passwords.
- Validate node API HTTP status, required base config, port and task intervals;
  cache only valid responses so failed updates can be retried.
- Preserve device online state on API failure and apply an empty user update.
- Validate existing certificate/key pairs; write new RSA keys with the correct
  PEM label and private permissions; publish complete files with rename.
- Repair legacy RSA keys labeled `EC PRIVATE KEY` without replacing the
  certificate or changing either SHA256 pin. Missing half of a self-signed
  pair is an error rather than silently rotating its identity.
- Run Windows/Linux tests before release builds.

The panel's existing certificate reporting and Nginx WSS certificate paths
remain the source of subscription pins. For WSS terminated at Nginx, report
the public certificate Nginx serves. Keep `terminate_tls_at_proxy` enabled
and the backend listener private. Do not point reporting at a different
backend certificate.

## Migration checks

1. Back up the currently deployed binary, configuration and certificate/key
   files. Keep certificate paths and their contents across restarts.
2. Test this binary on one node. Confirm the startup log says `26.7.28` and
   user updates, device limits and upload/download accounting work.
3. For public CA certificates, use the correct SNI and certificate chain.
   For self-signed TLS, wait for the panel to receive a valid certificate
   report and refresh Happ subscriptions with `pcs` / `vcn` or Xray's
   `pinnedPeerCertSha256` / `verifyPeerCertByName`. No `allowInsecure=true`
   is needed. A self-signed certificate with no available pin fails
   verification; it must not silently bypass verification.
4. Xray 26.7.28 blocks private destinations by default on its public proxy
   inbounds. If a panel route legitimately reaches a LAN service, configure
   an explicit, narrowly scoped `finalRules` allow rule on that route's
   freedom outbound. Legacy `ipsBlocked` needs migration to `finalRules`.
5. Shadowsocks `none` / `plain` is removed by upstream. Migrate such nodes
   to an AEAD cipher or Shadowsocks 2022 before switching binaries.
6. After certificate renewal, refresh subscriptions after the new
   certificate is loaded and reported. Do not rotate a pinned certificate
   without updating clients.

To roll back, restore the saved binary and its matching panel behavior;
retain the original certificate pair and configuration. This source upgrade
does not deploy or restart production nodes automatically.

## Validation

The prepared local source bundle includes a parent `go.work` that resolves the
Xray dependency from its sibling `xray-core` directory. The dependency in
`go.mod` is pinned to the prepared fork commit; standalone downloads become
available after that GitHub branch is published. The local override is not
included in the v2nodePro repository or release workflow. The prepared fork
commit is now published, and standalone dependency download/checksum checks
and tests with `GOWORK=off` have passed.

Release packages include full GeoIP/GeoSite from a pinned, SHA-256-verified
source commit, plus sample configuration and [Happ test instructions](happ-node-test.md).
Tags `v*-node.*` build all 25 platforms and publish a prerelease after tests
and every build pass; a manual workflow run only uploads build artifacts.
The current release is [`v0.3.7`](v0.3.7.md).

```sh
GOEXPERIMENT=jsonv2 go test -timeout 5m ./...
GOEXPERIMENT=jsonv2 go build ./...
```

Local tests exercise real Trojan WebSocket listeners for two panels, routing,
certificate reporting/reload, legacy key migration and API validation.
Happ itself and production Nginx endpoints still need the single-node check
above; unit/integration tests do not establish production client behavior.
