<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Navigation Demo</title>
    <style>
        :root {
            --nav-bg: rgba(17, 21, 29, 0.55);
            --nav-bg-scrolled: rgba(17, 21, 29, 0.95);
            --nav-color: #f8f8f8;
            --accent: #ff9f43;
            --accent-secondary: #6c63ff;
            --text: #1f1f1f;
            --muted: #4d4d4d;
            --transition-speed: 0.3s;
            --radius: 18px;
        }

        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            font-family: "Segoe UI", Arial, sans-serif;
            color: var(--text);
            background: radial-gradient(circle at top, #fef6ec, #f4f6fb 45%, #eef3ff);
            min-height: 100vh;
            scroll-behavior: smooth;
        }

        body::before {
            content: "";
            position: fixed;
            inset: 0;
            background: radial-gradient(circle at 20% 20%, rgba(108, 99, 255, 0.23), transparent 55%),
                        radial-gradient(circle at 80% 0%, rgba(255, 159, 67, 0.25), transparent 50%);
            z-index: -1;
        }

        header {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            z-index: 10;
            background: var(--nav-bg);
            backdrop-filter: blur(6px);
            transition: background var(--transition-speed), box-shadow var(--transition-speed);
        }

        header.scrolled {
            background: var(--nav-bg-scrolled);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
        }

        nav {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0.75rem clamp(1rem, 5vw, 4rem);
            color: var(--nav-color);
        }

        .brand {
            font-weight: 800;
            letter-spacing: 0.14em;
            text-transform: uppercase;
            font-size: 0.95rem;
            display: flex;
            gap: 0.35rem;
            align-items: center;
        }

        .brand span {
            padding: 0.2rem 0.6rem;
            border-radius: 999px;
            background: linear-gradient(120deg, var(--accent), var(--accent-secondary));
            color: #111;
            font-size: 0.85rem;
        }

        .menu {
            display: flex;
            gap: 1.5rem;
            list-style: none;
            margin: 0;
            padding: 0;
        }

        .menu a {
            text-decoration: none;
            color: var(--nav-color);
            font-weight: 600;
            letter-spacing: 0.04em;
            position: relative;
            padding: 0.35rem 0;
            transition: color var(--transition-speed);
        }

        .menu a::after {
            content: "";
            position: absolute;
            left: 0;
            bottom: 0;
            width: 100%;
            height: 2px;
            background: var(--accent);
            transform: scaleX(0);
            transform-origin: right;
            transition: transform var(--transition-speed);
        }

        .menu a:hover,
        .menu a:focus-visible,
        .menu a.active {
            color: var(--accent);
        }

        .menu a:hover::after,
        .menu a:focus-visible::after,
        .menu a.active::after {
            transform: scaleX(1);
            transform-origin: left;
        }

        main {
            padding-top: 110px;
        }

        section {
            min-height: 100vh;
            padding: clamp(3rem, 7vw, 6rem) clamp(2rem, 10vw, 8rem);
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: clamp(1.5rem, 4vw, 3rem);
        }

        .section-card {
            background: #ffffffdd;
            border-radius: var(--radius);
            padding: clamp(1.5rem, 3vw, 2.5rem);
            box-shadow: 0 20px 50px rgba(17, 21, 29, 0.08);
            border: 1px solid rgba(255, 255, 255, 0.3);
            backdrop-filter: blur(12px);
            transition: transform var(--transition-speed), box-shadow var(--transition-speed);
        }

        .section-card:hover {
            transform: translateY(-6px);
            box-shadow: 0 26px 60px rgba(17, 21, 29, 0.12);
        }

        section h2 {
            margin: 0;
            font-size: clamp(2rem, 5vw, 3rem);
            color: #0f172a;
        }

        section p {
            max-width: 60ch;
            line-height: 1.8;
            color: var(--muted);
            margin-bottom: 1rem;
        }

        .section-card ul {
            list-style: none;
            padding: 0;
            margin: 0;
            display: flex;
            flex-direction: column;
            gap: 0.75rem;
        }

        .section-card li {
            position: relative;
            padding-left: 1.25rem;
        }

        .section-card li::before {
            content: "";
            position: absolute;
            left: 0;
            top: 0.6rem;
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: linear-gradient(120deg, var(--accent), var(--accent-secondary));
        }

        .accent-pill {
            display: inline-flex;
            align-items: center;
            gap: 0.4rem;
            font-size: 0.85rem;
            font-weight: 700;
            letter-spacing: 0.08em;
            text-transform: uppercase;
            color: var(--accent-secondary);
        }

        .accent-pill::before {
            content: "";
            width: 32px;
            height: 2px;
            background: var(--accent-secondary);
            border-radius: 999px;
        }

        @media (max-width: 640px) {
            nav {
                flex-direction: column;
                gap: 1rem;
            }

            .menu {
                flex-wrap: wrap;
                justify-content: center;
            }
        }
    </style>
</head>
<body>
    <header id="site-header">
        <nav>
            <div class="brand">
                Prodigy
                <span>UI</span>
            </div>
            <ul class="menu">
                <li><a href="#home">Home</a></li>
                <li><a href="#services">Services</a></li>
                <li><a href="#work">Work</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <section id="home">
            <div class="section-card">
                <p class="accent-pill">Experience</p>
                <h2>Refined navigation for every scroll</h2>
                <p>
                    Enjoy a crisp, glassy navigation bar that stays fixed to the top, subtly
                    adapting as you scroll. Hover across the menu to reveal gentle accent cues,
                    or keep exploring to watch the bar transform into a solid, elevated surface.
                </p>
                <p>
                    This layout blends layered gradients, soft shadows, and tactile motion to
                    create a polished experience that feels at home on any modern device.
                </p>
            </div>
            <div class="section-card">
                <p class="accent-pill">Highlights</p>
                <ul>
                    <li>Glassmorphic navigation with scroll-aware styling</li>
                    <li>Animated menu links that react on hover and focus</li>
                    <li>Section-aware active states powered by Intersection Observer</li>
                    <li>Responsive grid layouts with soft cards and depth</li>
                </ul>
            </div>
        </section>

        <section id="services">
            <div class="section-card">
                <p class="accent-pill">Services</p>
                <h2>Design systems with a modern edge</h2>
                <p>
                    From concept to prototype, we craft cohesive design languages that keep
                    brands memorable. Each component is meticulously tuned for clarity,
                    accessibility, and delight.
                </p>
            </div>
            <div class="section-card">
                <p class="accent-pill">Capabilities</p>
                <p>
                    • Responsive interface design<br>
                    • Design tokens & component libraries<br>
                    • Interaction & motion strategy<br>
                    • Accessibility auditing
                </p>
            </div>
        </section>

        <section id="work">
            <div class="section-card">
                <p class="accent-pill">Showcase</p>
                <h2>Human-centered experiences</h2>
                <p>
                    We merge storytelling with functional clarity—whether it’s a data-rich
                    dashboard, a lifestyle brand launchpad, or an immersive product tour.
                </p>
            </div>
            <div class="section-card">
                <p class="accent-pill">Recent Drops</p>
                <p>
                    • Fintech analytics cockpit<br>
                    • Immersive retail microsite<br>
                    • Real-time collaboration suite
                </p>
            </div>
        </section>

        <section id="contact">
            <div class="section-card">
                <p class="accent-pill">Connect</p>
                <h2>Let’s build something bold</h2>
                <p>
                    Ready for a navigation overhaul or a full experience refresh?
                    We partner with teams to design systems that scale and stories that stick.
                </p>
            </div>
            <div class="section-card">
                <p class="accent-pill">Get in touch</p>
                <p>
                    hello@prodigy.studio<br>
                    +1 (555) 201-2045<br>
                    221B Aurora Street, CA
                </p>
            </div>
        </section>
    </main>

    <script>
        const header = document.getElementById("site-header");
        const menuLinks = document.querySelectorAll(".menu a");
        const sections = [...document.querySelectorAll("section")];

        const handleScroll = () => {
            if (window.scrollY > 20) {
                header.classList.add("scrolled");
            } else {
                header.classList.remove("scrolled");
            }
        };

        const handleActiveLink = (entries) => {
            entries.forEach((entry) => {
                if (entry.isIntersecting) {
                    const id = entry.target.getAttribute("id");
                    menuLinks.forEach((link) => {
                        link.classList.toggle("active", link.getAttribute("href") === `#${id}`);
                    });
                }
            });
        };

        const observer = new IntersectionObserver(handleActiveLink, {
            rootMargin: "-40% 0px -40% 0px",
            threshold: 0,
        });

        sections.forEach((section) => observer.observe(section));

        document.addEventListener("scroll", handleScroll, { passive: true });
        handleScroll();
    </script>
</body>
</html>

