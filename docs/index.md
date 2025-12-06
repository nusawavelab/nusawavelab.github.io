---
icon: lucide/rocket
---

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Nusawave Lab</title>

  <!-- Minimal reset to avoid Zensical conflicts -->
  <style>
    :root {
      --nw-primary: #0a5fa8;
      --nw-secondary: #0f8ecf;
      --nw-dark: #042f4b;
      --nw-light: #f5f9fc;
      --nw-radius: 10px;
    }
  html {
    scroll-behavior: smooth;
  }
    body {
      margin: 0;
      font-family: system-ui, sans-serif;
      background: var(--nw-light);
      color: #222;
      line-height: 1.6;
    }

    /* HERO SECTION */
    .hero {
    width: 100vw;
    margin-left: 50%;
    transform: translateX(-50%);
    background: linear-gradient(rgba(4,47,75,0.6), rgba(4,47,75,0.6)),
                url("https://images.unsplash.com/photo-1507525428034-b723cf961d3e") 
                center/cover no-repeat;
    text-align: center;
    padding: 100px 20px;
    color: white;
    }

    .hero h1 {
      font-size: 3.5rem;
      margin-bottom: 15px;
      font-weight: 700;
      color: white;
    }

    .hero p {
      font-size: 1.25rem;
      max-width: 650px;
      margin: 0 auto 30px auto;
      opacity: 0.9;
    }

    .hero .cta-btn {
      background: var(--nw-secondary);
      color: white;
      padding: 14px 28px;
      font-size: 1.1rem;
      border-radius: var(--nw-radius);
      border: none;
      cursor: pointer;
      text-decoration: none;
      transition: 0.25s ease;
      display: inline-block;
      margin-top: 10px;
    }

    .hero .cta-btn:hover {
      background: var(--nw-primary);
    }

    /* FEATURES */
    .features {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
      gap: 25px;
      padding: 60px 40px;
      max-width: 1200px;
      margin: auto;
    }

    .feature-card {
      background: white;
      border-radius: var(--nw-radius);
      padding: 25px 20px;
      box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
      transition: 0.3s ease;
    }

    .feature-card:hover {
      transform: translateY(-5px);
      box-shadow: 0 6px 20px rgba(0, 0, 0, 0.12);
    }

    .feature-card h3 {
      margin-top: 0;
      color: var(--nw-dark);
    }

    /* FOOTER */
    footer {
      background: var(--nw-dark);
      color: white;
      padding: 25px 15px;
      text-align: center;
      margin-top: 60px;
    }

    /* ------------------------------------------
      RESPONSIVE BREAKPOINTS
    -------------------------------------------*/

    /* Tablet */
    @media (max-width: 900px) {
      .hero {
        padding: 110px 20px 130px;
      }

      .hero h1 {
        font-size: 2.5rem;
      }

      .hero p {
        font-size: 1.1rem;
      }
    }

    /* Medium Phones */
    @media (max-width: 600px) {
      .hero {
        padding: 90px 20px 110px;
      }

      .hero h1 {
        font-size: 2rem;
      }

      .hero p {
        font-size: 1rem;
      }

      .hero .cta-btn {
        padding: 12px 22px;
        font-size: 1rem;
      }
    }

    /* Small Phones (iPhone SE, older Android) */
    @media (max-width: 420px) {
      .hero {
        padding: 70px 16px 100px;
      }

      .hero h1 {
        font-size: 1.7rem;
      }

      .hero p {
        font-size: 0.95rem;
      }
    }
  </style>
</head>

<body>

  <!-- HERO SECTION -->
  <section class="hero">
    <h1>NusawaveLab</h1>
    <p>An Open-source knowledge hub for metocean analytics, numerical modeling, Python workflows, and modern ocean data intelligence.</p>

    <a class="cta-btn" href="#features">Get Started</a>
  </section>

  <!-- FEATURES -->
  <section class="features" id="features">
    <div class="feature-card">
      <h3>📘 Tutorials</h3>
      <p>Learn Python, data analysis, wave modeling, and metocean workflows with step-by-step guides.</p>
      <a href="/tutorial/">Explore tutorials →</a>
    </div>

    <div class="feature-card">
      <h3>🌊 Ocean Modeling</h3>
      <p>Guides and insights into WAVEWATCH III, HPC workflows, GPU research, and operational forecasting.</p>
      <a href="/python/">View modeling docs →</a>
    </div>

    <div class="feature-card">
      <h3>🛠 Tools & Code</h3>
      <p>Reusable Python libraries, notebooks, and computational tools built by Nusawave Lab.</p>
      <a href="/roadmap/">See roadmap →</a>
    </div>

    <div class="feature-card">
      <h3>📝 Articles & Notes</h3>
      <p>Short, technical notes on wave physics, HPC engineering, and data science for the ocean.</p>
      <a href="/tutorial/zensical/">Read articles →</a>
    </div>
  </section>

  <!-- FOOTER -->
  <!-- <footer>
    NusawaveLab — Open Knowledge for Metocean & Ocean Data Intelligence<br>
    © 2025 Nusawave
  </footer> -->

</body>
</html>
