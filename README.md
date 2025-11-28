<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Stopwatch</title>
    <style>
        :root {
            --bg: #080710;
            --panel: rgba(17, 24, 39, 0.9);
            --accent: #7c3aed;
            --accent-2: #06b6d4;
            --text: #f8fafc;
            --muted: #94a3b8;
            --danger: #ef4444;
        }

        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 2rem;
            font-family: "Segoe UI", system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
            color: var(--text);
            background: radial-gradient(circle at top, rgba(124, 58, 237, 0.35), transparent 55%),
                radial-gradient(circle at bottom, rgba(6, 182, 212, 0.35), transparent 60%),
                var(--bg);
        }

        main {
            width: min(960px, 100%);
            background: var(--panel);
            border-radius: 32px;
            padding: clamp(2rem, 5vw, 3.5rem);
            box-shadow: 0 35px 90px rgba(0, 0, 0, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        header {
            text-align: center;
            margin-bottom: 2rem;
        }

        h1 {
            font-size: clamp(2rem, 4vw, 3rem);
            margin: 0;
        }

        p {
            margin: 0.5rem 0 0;
            color: var(--muted);
        }

        .time-display {
            display: flex;
            justify-content: center;
            align-items: baseline;
            gap: 1rem;
            font-variant-numeric: tabular-nums;
            margin: 2.5rem 0;
        }

        .time-display .primary {
            font-size: clamp(3rem, 10vw, 5rem);
            font-weight: 600;
            letter-spacing: 0.12em;
        }

        .time-display .fraction {
            font-size: clamp(2rem, 5vw, 3rem);
            color: var(--muted);
        }

        .controls {
            display: flex;
            flex-wrap: wrap;
            gap: 1rem;
            justify-content: center;
        }

        button {
            border: none;
            border-radius: 999px;
            padding: 0.95rem 2.25rem;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            color: #fff;
            background: rgba(255, 255, 255, 0.08);
            transition: transform 0.2s ease, background 0.2s ease, opacity 0.2s ease;
            min-width: 140px;
        }

        button:hover:not(:disabled) {
            transform: translateY(-2px);
        }

        button:disabled {
            opacity: 0.45;
            cursor: not-allowed;
        }

        .btn-primary {
            background: linear-gradient(135deg, var(--accent), var(--accent-2));
        }

        .btn-danger {
            background: rgba(239, 68, 68, 0.3);
        }

        .lap-panel {
            margin-top: 2.75rem;
            border-radius: 28px;
            background: rgba(8, 8, 15, 0.45);
            border: 1px solid rgba(255, 255, 255, 0.05);
            max-height: 360px;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        .lap-panel header {
            padding: 1rem 1.75rem;
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
            margin: 0;
            text-align: left;
        }

        .lap-panel h2 {
            font-size: 1.2rem;
            margin: 0;
        }

        .lap-list {
            list-style: none;
            margin: 0;
            padding: 0;
            overflow-y: auto;
            scrollbar-width: thin;
        }

        .lap-list li {
            display: grid;
            grid-template-columns: 120px 1fr 150px;
            align-items: center;
            font-variant-numeric: tabular-nums;
            padding: 0.9rem 1.75rem;
            border-bottom: 1px solid rgba(255, 255, 255, 0.03);
            gap: 0.5rem;
        }

        .lap-list li:last-child {
            border-bottom: none;
        }

        .lap-label {
            text-transform: uppercase;
            letter-spacing: 0.08em;
            font-size: 0.8rem;
            color: var(--muted);
        }

        .lap-duration {
            justify-self: end;
            color: var(--muted);
        }

        .empty-state {
            text-align: center;
            padding: 2.5rem 1rem;
            color: var(--muted);
        }

        @media (max-width: 640px) {
            .lap-list li {
                grid-template-columns: 90px 1fr;
            }

            .lap-duration {
                justify-self: start;
                grid-column: span 2;
            }

            button {
                width: 100%;
            }
        }
    </style>
</head>
<body>
    <main>
        <header>
            <h1>Stopwatch</h1>
            <p>Start, pause, reset, and capture precise lap times.</p>
        </header>

        <section class="time-display" aria-label="Stopwatch display">
            <span class="primary" id="time-main">00:00:00</span>
            <span class="fraction" id="time-ms">.000</span>
        </section>

        <section class="controls" aria-label="Stopwatch controls">
            <button class="btn-primary" id="start-btn">Start</button>
            <button id="lap-btn" disabled>Lap</button>
            <button class="btn-danger" id="reset-btn" disabled>Reset</button>
        </section>

        <section class="lap-panel" aria-live="polite">
            <header>
                <h2>Lap history</h2>
            </header>
            <ul class="lap-list" id="laps">
                <li class="empty-state">No laps recorded yet.</li>
            </ul>
        </section>
    </main>

    <script>
        const timeMain = document.getElementById("time-main");
        const timeMs = document.getElementById("time-ms");
        const startBtn = document.getElementById("start-btn");
        const lapBtn = document.getElementById("lap-btn");
        const resetBtn = document.getElementById("reset-btn");
        const lapList = document.getElementById("laps");

        let startTimestamp = 0;
        let accumulated = 0;
        let rafId = null;
        let lapTimes = [];

        const formatTime = (ms) => {
            const hours = Math.floor(ms / 3_600_000);
            const minutes = Math.floor((ms % 3_600_000) / 60_000);
            const seconds = Math.floor((ms % 60_000) / 1_000);
            const millis = Math.floor(ms % 1_000);

            const pad = (value) => String(value).padStart(2, "0");
            return {
                main: `${pad(hours)}:${pad(minutes)}:${pad(seconds)}`,
                fraction: `.${String(millis).padStart(3, "0")}`
            };
        };

        const renderTime = (ms) => {
            const { main, fraction } = formatTime(ms);
            timeMain.textContent = main;
            timeMs.textContent = fraction;
        };

        const tick = () => {
            const elapsed = accumulated + (performance.now() - startTimestamp);
            renderTime(elapsed);
            rafId = requestAnimationFrame(tick);
        };

        const setButtons = ({ running }) => {
            if (running) {
                startBtn.textContent = "Pause";
                lapBtn.disabled = false;
                resetBtn.disabled = false;
            } else {
                startBtn.textContent = accumulated ? "Resume" : "Start";
                lapBtn.disabled = !accumulated;
                resetBtn.disabled = !accumulated;
            }
        };

        const renderEmpty = () => {
            lapList.innerHTML = '<li class="empty-state">No laps recorded yet.</li>';
        };

        const renderLaps = () => {
            if (!lapTimes.length) {
                renderEmpty();
                return;
            }

            lapList.innerHTML = "";
            lapTimes
                .slice()
                .reverse()
                .forEach((lap, index, arr) => {
                    const li = document.createElement("li");
                    const overall = formatTime(lap.total);
                    const prevTotal = arr[index + 1]?.total ?? 0;
                    const lapDuration = formatTime(lap.total - prevTotal);

                    li.innerHTML = `
                        <span class="lap-label">Lap ${lapTimes.length - index}</span>
                        <span>${overall.main}${overall.fraction}</span>
                        <span class="lap-duration">+${lapDuration.main}${lapDuration.fraction}</span>
                    `;
                    lapList.appendChild(li);
                });
        };

        startBtn.addEventListener("click", () => {
            const running = Boolean(rafId);

            if (running) {
                cancelAnimationFrame(rafId);
                rafId = null;
                accumulated += performance.now() - startTimestamp;
                renderTime(accumulated);
                setButtons({ running: false });
                return;
            }

            startTimestamp = performance.now();
            rafId = requestAnimationFrame(tick);
            setButtons({ running: true });
        });

        lapBtn.addEventListener("click", () => {
            const current = accumulated + (rafId ? performance.now() - startTimestamp : 0);
            lapTimes.push({ total: current });
            renderLaps();
        });

        resetBtn.addEventListener("click", () => {
            if (rafId) {
                cancelAnimationFrame(rafId);
                rafId = null;
            }
            accumulated = 0;
            renderTime(0);
            lapTimes = [];
            renderEmpty();
            setButtons({ running: false });
        });

        document.addEventListener("keydown", (event) => {
            if (event.code === "Space") {
                event.preventDefault();
                startBtn.click();
            }
            if (event.code === "KeyL") {
                lapBtn.click();
            }
            if (event.code === "KeyR") {
                resetBtn.click();
            }
        });

        renderTime(0);
    </script>
</body>
</html>
 
