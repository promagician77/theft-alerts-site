# Till alerts from store cameras: demo site

Static build. Deploy this folder to Vercel as is (`vercel.json`: no framework, no build,
output `.`, every response sent with `X-Robots-Tag: noindex, nofollow`). Locally:
`npx serve .` or `python3 -m http.server`.

## What the page does

- **Live demo.** A simulated store camera feeds the event engine at 10 inference frames a second. Alerts land on a till tablet; Confirm or Dismiss reveals what really happened.
  - Scenarios: busy aisle, concealments only, phones and wallets, hands in pockets.
  - Engines: wrist rule, product in hand, full engine.
  - Camera angles: eye level, ceiling.
- **Measured results**, loaded from `data/engine.json`, `data/infra.json` and `data/model.json`:
  - detection on held-out simulated hours, with ablations and weekly feedback;
  - RTSP latency by reader setting;
  - crash loss by recording setting;
  - fault injection on four recorders;
  - decode and recording cost, YOLOX timing, alert delivery.
- **Download.** `python-service.zip`: recorder, reader, engine, alert service, benchmarks.

## Measured

- Full engine on 60 held-out simulated camera-hours: 87% of 257 concealments caught at 0.28 false alerts per camera-hour. The other engines, tuned to the same 85% on separate hours:
  - wrist rule: 81% caught, 66.9 false alerts per camera-hour;
  - product in hand: 87% caught, 4.6.
- With four times as many shoppers in view: 79% caught at 1.27 false alerts per camera-hour.
- Camera to decoded frame on loopback:
  - ffmpeg defaults: 271 ms;
  - tuned flags: 137 ms;
  - PyAV in-process: 71 ms.
- Recorder killed:
  - plain MP4 loses up to 9.0 s;
  - fMP4 flushed per frame loses at most 0.2 s.
- Alert to 3 tablets: median 2.3 ms; 1,800 of 1,800 delivered.
- Browser QA: 18 of 18 checks on desktop and mobile, no console errors.

## Not measured

- Accuracy on real store footage: shoppers, the pose model and the detector are simulated.
- Real cameras and networks: latency is loopback on one machine.
- GPU throughput: CPU only.
