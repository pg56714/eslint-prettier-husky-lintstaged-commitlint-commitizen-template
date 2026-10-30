# Security

## Known issues

Last checked: 2026-10-30.

`npm audit` still reports advisories in packages that Create React App (react-scripts 5.0.1) pins. They affect the local development server and the test environment only; the production build (`npm run build`) does not include them.

| Package                 | Version        | Fixed in        | Held back by                                      | Impact and mitigation                                                                                                                                             |
| ----------------------- | -------------- | --------------- | ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| webpack-dev-server      | 4.15.2         | 5.2.x           | react-scripts 5.0.1 requires webpack-dev-server 4 | While `npm start` is running, a malicious website you visit can read your source code or hijack the HMR WebSocket. Stop the dev server when you are not using it. |
| webpack-dev-middleware  | 5.3.4          | 7.4.5           | webpack-dev-server 4                              | Path traversal in the development server only.                                                                                                                    |
| node-forge              | 1.4.0          | no fix released | selfsigned (dev server HTTPS certificate)         | RSA PKCS#1 v1.5 signature verification accepts extra data. Only used to create the dev server's self-signed certificate.                                          |
| sockjs / uuid           | 0.3.24 / 8.3.2 | uuid 11.1.1     | webpack-dev-server 4                              | Missing bounds check when a caller passes its own buffer to uuid v3/v5/v6; sockjs does not. Dev server only.                                                      |
| postcss-selector-parser | 6.1.5          | 7.1.6           | PostCSS plugins bundled with react-scripts        | CPU exhaustion when parsing crafted CSS at build time; only your own CSS is parsed.                                                                               |
| @tootallnate/once       | 1.1.2          | 2.0.1           | jsdom 16 in jest-environment-jsdom 27             | Incorrect control-flow scoping; test environment only.                                                                                                            |

Clearing these requires replacing Create React App with a maintained build tool such as Vite.
