# JS Self-Profiling: Markers Without Cross-Origin Isolation

Demo for the Chromium CL [[JS Self-Profiling] Allow safe markers in non-isolated contexts](https://chromium-review.googlesource.com/c/chromium/src/+/6012522).

`ProfilerSample.marker` used to require cross-origin isolation (COI), which few production sites can deploy. This change exposes `layout` and `style` markers in every context, since their timing is already observable through DOM and CSSOM APIs. `gc`, `paint`, and `script` still require COI.

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

## Links

- [Chromium CL 6012522](https://chromium-review.googlesource.com/c/chromium/src/+/6012522)
- [Spec PR: WICG/js-self-profiling#85](https://github.com/WICG/js-self-profiling/pull/85)
- [Explainer: Conditional Markers Exposure](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/main/ConditionalMarkersExposure/explainer.md)
- [Security discussion: WICG/js-self-profiling#61](https://github.com/WICG/js-self-profiling/issues/61)
