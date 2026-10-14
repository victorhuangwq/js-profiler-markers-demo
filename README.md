# JS Self-Profiling: markers without cross-origin isolation

- [Chrome Status](https://chromestatus.com/feature/5201297767792640)
- Origin trial: [Chrome](https://developer.chrome.com/origintrials/#/register_trial/2442639847895072769) (M153 to M161), [Edge](https://developer.microsoft.com/en-us/microsoft-edge/origin-trials/trials/ad17726e-aab6-42a0-8d63-0888419e5ad3)
- Explainers: [Markers](https://github.com/WICG/js-self-profiling/blob/main/markers.md), [Conditional markers exposure](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/main/ConditionalMarkersExposure/explainer.md)

Profiler markers record what the browser was doing when each sample was taken, such as running layout or style. They used to require cross-origin isolation (COI), which few production sites can deploy. This change exposes layout and style markers without COI, since that timing is already observable through DOM and CSSOM APIs. Garbage collection, paint, and script markers still require COI.

## What this demo shows

The same profiling test runs on two pages, one with COI and one without. Layout and style markers should appear on both. Garbage collection, paint, and script markers should appear only on the COI page.

## Run

1. Close all Edge windows, then relaunch with the markers feature enabled:

   ```sh
   msedge.exe --enable-features=ExperimentalJSProfilerMarkers --enable-experimental-web-platform-features
   ```

2. Start the server:

   ```sh
   node test-server.cjs
   ```

3. Open both pages and click **Run Profiling Test**:

   | Page | Headers | Expected markers |
   | --- | --- | --- |
   | http://localhost:8123/no-coi | `Document-Policy: js-profiling` | `layout`, `style` |
   | http://localhost:8123/coi | Same, plus COOP and COEP | `layout`, `style`, `gc`, `paint`, `script` |

## Reading results

Markers come from sampling, so an expected marker can occasionally be missing. Rerun before treating that as a failure. A `gc`, `paint`, or `script` marker on `/no-coi` is always a real failure.
