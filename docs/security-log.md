# Security Log

English | [繁體中文](security-log.zh-TW.md)

- Date: 2026-10-25
- Branch: `fix/security-upgrades`
- Scope: `package.json`, `package-lock.json`

## Summary

The lockfile was regenerated and npm overrides were added for packages pinned by react-scripts, cutting npm audit's high-severity findings from 18 to 4. The remaining ones are in Create React App's development server stack, which is no longer maintained.

## Changes

| Item              | Before | After                                                                                          | Reason                                                                                                                |
| ----------------- | ------ | ---------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| package-lock.json | -      | regenerated                                                                                    | Transitive packages resolve to patched releases (jsonpath, form-data, shell-quote, websocket-driver, proxy-addr, ...) |
| npm overrides     | -      | nth-check, postcss, serialize-javascript, webpack-dev-middleware, node-forge, underscore, svgo | Force patched versions that react-scripts 5.0.1 would otherwise pin                                                   |

## Remaining issues

### Dependency versions held back upstream

| Package                 | Version | Advisories          | Why it is not fixed                                                                                    | Impact and mitigation                                                                                                                                    |
| ----------------------- | ------- | ------------------- | ------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| webpack-dev-server      | 4.15.2  | 6 (fixed in 5.2.x)  | react-scripts 5.0.1 requires webpack-dev-server 4; version 5 is not compatible with its configuration. | While `npm start` is running, a malicious website you visit can read your source code or hijack the HMR WebSocket. The production build is not affected. |
| webpack-dev-middleware  | 5.3.4   | 1 (fixed in 7.4.6)  | webpack-dev-server 4 requires version 5.                                                               | Path traversal in the development server only.                                                                                                           |
| node-forge              | 1.4.0   | 1 (no fix released) | No patched release exists yet.                                                                         | RSA PKCS#1 v1.5 signature verification accepts extra data. Used only by `selfsigned` to create the dev server's HTTPS certificate.                       |
| uuid                    | 8.3.2   | 1 (fixed in 11.1.1) | Pulled in by sockjs inside webpack-dev-server 4.                                                       | Missing bounds check when a caller passes its own buffer to v3/v5/v6; sockjs does not. Dev server only.                                                  |
| postcss-selector-parser | 6.1.5   | 1 (fixed in 7.1.6)  | Required by PostCSS plugins bundled with react-scripts.                                                | CPU exhaustion when parsing crafted CSS at build time; only your own CSS is parsed.                                                                      |
| @tootallnate/once       | 1.1.2   | 1 (fixed in 2.0.1)  | Pulled in by jsdom 16 in jest-environment-jsdom 27.                                                    | Incorrect control-flow scoping; used only by the test environment.                                                                                       |

### Requires code changes

- Clearing the rest requires replacing Create React App (react-scripts) with a maintained build tool such as Vite, and updating the template steps in the README accordingly.

## How it was verified

- `npm run build`, the test suite and `npm start` still work.
- osv-scanner on `package-lock.json`; only the packages listed above remain.

## Not verified

- Nothing further: the remaining issues affect only the local development server and the test environment.
