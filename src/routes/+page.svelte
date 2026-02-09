<script>
  import { tick } from "svelte";

  const questions = [
    {
      q: "Was ist die Web Animations API (WAAPI)?",
      a: [
        "Eine CSS-Erweiterung für Keyframes",
        "Eine JavaScript-API zur Steuerung von Browser-Animationen",
        "Ein GIF-Format für Animationen",
        "Ein Svelte-eigenes Animationssystem"
      ],
      correct: 1
    },
    {
      q: "Warum nennt man WAAPI „imperativ“?",
      a: [
        "Weil man CSS schreibt",
        "Weil Animationen aktiv über JavaScript gesteuert werden",
        "Weil sie nur im HTML funktioniert",
        "Weil sie nur für SVG gedacht ist"
      ],
      correct: 1
    },
    {
      q: "Welche zwei Hauptbestandteile hat eine WAAPI-Animation?",
      a: [
        "HTML und CSS",
        "Keyframes und Optionen",
        "SVG und SMIL",
        "Canvas und WebGL"
      ],
      correct: 1
    },
    {
      q: "Was beschreibt die Option „duration“?",
      a: [
        "Die Startzeit der Animation",
        "Die Dauer der Animation in Millisekunden",
        "Die Anzahl der Keyframes",
        "Die Größe des Elements"
      ],
      correct: 1
    },
    {
      q: "Was bewirkt `fill: \"forwards\"`?",
      a: [
        "Die Animation läuft rückwärts",
        "Das Element behält den Endzustand nach der Animation",
        "Die Animation wird langsamer",
        "Die Animation startet verzögert"
      ],
      correct: 1
    },
    {
      q: "Welche CSS-Eigenschaften sind am performantesten zu animieren?",
      a: [
        "top und left",
        "width und height",
        "transform und opacity",
        "margin und padding"
      ],
      correct: 2
    },
    {
      q: "Was steuert `playbackRate`?",
      a: [
        "Die Farbe der Animation",
        "Die Geschwindigkeit der Animation",
        "Den Startpunkt der Timeline",
        "Die Anzahl der Wiederholungen"
      ],
      correct: 1
    },
    {
      q: "Welche Methode erstellt direkt eine Animation am Element?",
      a: [
        "element.keyframes()",
        "element.animate()",
        "element.transition()",
        "element.timeline()"
      ],
      correct: 1
    },
    {
      q: "Warum eignet sich WAAPI besonders für interaktive Animationen?",
      a: [
        "Weil sie nur dekorativ ist",
        "Weil Animationen zur Laufzeit gesteuert werden können",
        "Weil sie ohne JavaScript funktioniert",
        "Weil sie nur mit SVG arbeitet"
      ],
      correct: 1
    },
    {
      q: "Warum ist die Web Animations API besonders performant?",
      a: [
        "Weil sie Animationen in GIFs umwandelt",
        "Weil sie direkt mit der Animations-Engine des Browsers arbeitet",
        "Weil sie ausschließlich CSS nutzt",
        "Weil sie nur im HTML ausgeführt wird"
      ],
      correct: 1
    }
  ];

  let index = 0;
  let selected = Array(questions.length).fill(null);
  let submitted = false;

  let card;
  let congrats;

  // finale Animation refs
  let confettiWrap;
  let ring;

  function choose(i) {
    selected[index] = i;
  }

  function allAnswered() {
    return selected.every((v) => v !== null);
  }

  function allCorrect() {
    return selected.every((v, i) => v === questions[i].correct);
  }

  async function animateCard(dir = 1) {
    if (!card) return;
    card.animate(
      [
        { opacity: 0, transform: `translateX(${dir * 18}px) translateY(6px)` },
        { opacity: 1, transform: "translateX(0) translateY(0)" }
      ],
      { duration: 280, easing: "ease-out" }
    );
  }

  async function next() {
    if (index >= questions.length - 1) return;
    index += 1;
    await tick();
    animateCard(1);
  }

  async function prev() {
    if (index <= 0) return;
    index -= 1;
    await tick();
    animateCard(-1);
  }

  async function submit() {
    submitted = true;
    await tick();

    if (allCorrect()) {
      playCongrats();
    } else {
      // kleines Shake wenn nicht alles korrekt
      congrats?.animate(
        [
          { transform: "translateX(0)" },
          { transform: "translateX(-10px)" },
          { transform: "translateX(10px)" },
          { transform: "translateX(-6px)" },
          { transform: "translateX(6px)" },
          { transform: "translateX(0)" }
        ],
        { duration: 420, easing: "ease-out" }
      );
    }
  }

  function playCongrats() {
    if (!congrats) return;

    // Panel Bounce-In
    congrats.animate(
      [
        { opacity: 0, transform: "scale(0.88) translateY(14px)" },
        { opacity: 1, transform: "scale(1.05) translateY(0)" },
        { opacity: 1, transform: "scale(1) translateY(0)" }
      ],
      {
        duration: 700,
        easing: "cubic-bezier(.2,1,.3,1)",
        fill: "forwards"
      }
    );

    // Glow Puls
    congrats.animate(
      [
        { boxShadow: "0 0 0 rgba(124,140,255,0)" },
        { boxShadow: "0 0 70px rgba(124,140,255,0.95)" },
        { boxShadow: "0 0 24px rgba(124,140,255,0.55)" },
        { boxShadow: "0 0 55px rgba(124,140,255,0.85)" },
        { boxShadow: "0 0 18px rgba(124,140,255,0.50)" }
      ],
      { duration: 1300, easing: "ease-out" }
    );

    // Ring Welle
    if (ring) {
      ring.animate(
        [
          { opacity: 0.0, transform: "translate(-50%,-50%) scale(0.3)" },
          { opacity: 0.55, transform: "translate(-50%,-50%) scale(1.2)" },
          { opacity: 0.0, transform: "translate(-50%,-50%) scale(2.1)" }
        ],
        { duration: 900, easing: "ease-out" }
      );
    }

    // Confetti
    if (confettiWrap) {
      const pieces = Array.from(confettiWrap.querySelectorAll(".piece"));
      pieces.forEach((p, i) => {
        const angle = (Math.PI * 2 * i) / pieces.length;
        const dist = 140 + (i % 6) * 12;
        const x = Math.cos(angle) * dist;
        const y = Math.sin(angle) * dist - 40;
        const hue = (i * 35) % 360;

        p.style.background = `hsl(${hue} 90% 60%)`;

        p.animate(
          [
            { opacity: 0, transform: "translate(0,0) rotate(0deg) scale(0.85)" },
            {
              opacity: 1,
              transform: `translate(${x}px, ${y}px) rotate(${220 + i * 12}deg) scale(1)`
            },
            {
              opacity: 0,
              transform: `translate(${x * 1.05}px, ${y * 1.05 + 80}px) rotate(${420 + i * 18}deg) scale(0.9)`
            }
          ],
          {
            duration: 1200,
            easing: "cubic-bezier(.2,.8,.2,1)",
            delay: i * 12
          }
        );
      });
    }
  }
</script>

<div class="bg"></div>
<div class="backdrop"></div>

<div class="wrap">
  <section class="card" bind:this={card}>
    <header class="top">
      <div>
        <h1>WAAPI Quiz</h1>
        <p class="counter">Frage {index + 1} / {questions.length}</p>
      </div>

      <div class="dots" aria-label="Progress">
        {#each questions as _, i}
          <span class:dotActive={i === index} class:dotDone={selected[i] !== null}></span>
        {/each}
      </div>
    </header>

    <h2 class="q">{questions[index].q}</h2>

    <div class="answers">
      {#each questions[index].a as opt, i}
        <button
          class:selected={selected[index] === i}
          class:correct={submitted && selected[index] === i && i === questions[index].correct}
          class:wrong={submitted && selected[index] === i && i !== questions[index].correct}
          on:click={() => choose(i)}
        >
          <span class="badge">{String.fromCharCode(65 + i)}</span>
          <span class="txt">{opt}</span>
        </button>
      {/each}
    </div>

    <div class="nav">
      <button class="arrow" on:click={prev} disabled={index === 0}>←</button>

      {#if index < questions.length - 1}
        <button class="arrow" on:click={next} disabled={selected[index] === null}>→</button>
      {:else}
        <button class="finish" on:click={submit} disabled={!allAnswered()}>Auswerten</button>
      {/if}
    </div>

    <p class="hint">Du kannst nur weiter, wenn du eine Antwort gewählt hast.</p>
  </section>

  {#if submitted}
    <section class="result" bind:this={congrats}>
      {#if allCorrect()}
        <div class="final">
          <div class="ring" bind:this={ring}></div>

          <div class="confetti" bind:this={confettiWrap}>
            {#each Array(24) as _}
              <span class="piece"></span>
            {/each}
          </div>

          <h3>Gratulation!</h3>
          <p>Du hast alle Fragen richtig beantwortet und WAAPI sehr gut verstanden.</p>
        </div>
      {:else}
        <h3>Noch nicht ganz</h3>
        <p>Ein paar Antworten sind falsch. Geh nochmal durch und verbessere sie.</p>
      {/if}
    </section>
  {/if}
</div>

<style>
  :global(body) {
    margin: 0;
    font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
    color: #eaeaf0;
  }

  .bg {
    position: fixed;
    inset: 0;
    background: radial-gradient(circle at top, #1b2240, #090b16);
  }

  .backdrop {
    position: fixed;
    inset: 0;
    backdrop-filter: blur(12px);
    background: rgba(0, 0, 0, 0.35);
  }

  .wrap {
    min-height: 100vh;
    display: grid;
    place-items: center;
    position: relative;
    z-index: 1;
    padding: 22px 14px 60px;
  }

  .card {
    width: min(760px, 92vw);
    padding: 26px;
    border-radius: 22px;
    background: rgba(20, 24, 50, 0.92);
    border: 1px solid rgba(255, 255, 255, 0.12);
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.6);
  }

  .top {
    display: flex;
    justify-content: space-between;
    gap: 14px;
    align-items: flex-start;
    margin-bottom: 10px;
  }

  h1 {
    margin: 0;
    font-size: 28px;
    letter-spacing: 0.2px;
  }

  .counter {
    margin: 6px 0 0;
    opacity: 0.75;
    font-size: 14px;
  }

  .dots {
    display: flex;
    gap: 6px;
    padding-top: 6px;
    flex-wrap: wrap;
    justify-content: flex-end;
    max-width: 260px;
  }

  .dots span {
    width: 10px;
    height: 10px;
    border-radius: 999px;
    background: rgba(255, 255, 255, 0.16);
    border: 1px solid rgba(255, 255, 255, 0.12);
  }

  .dots span.dotDone {
    background: rgba(124, 140, 255, 0.22);
    border-color: rgba(124, 140, 255, 0.35);
  }

  .dots span.dotActive {
    background: rgba(124, 140, 255, 0.65);
    border-color: rgba(124, 140, 255, 0.9);
    box-shadow: 0 0 12px rgba(124, 140, 255, 0.7);
  }

  .q {
    margin: 14px 0 12px;
    font-size: 20px;
    line-height: 1.25;
  }

  .answers {
    display: grid;
    gap: 12px;
    margin-top: 12px;
  }

  .answers button {
    padding: 14px 14px;
    border-radius: 16px;
    border: 1px solid rgba(255, 255, 255, 0.15);
    background: rgba(255, 255, 255, 0.06);
    color: #fff;
    cursor: pointer;
    display: flex;
    gap: 12px;
    align-items: center;
    text-align: left;
    transition: all 0.22s ease;
  }

  .answers button:hover {
    background: rgba(255, 255, 255, 0.09);
  }

  .badge {
    width: 30px;
    height: 30px;
    border-radius: 999px;
    display: grid;
    place-items: center;
    background: rgba(255, 255, 255, 0.10);
    border: 1px solid rgba(255, 255, 255, 0.14);
    font-weight: 800;
    flex: 0 0 auto;
  }

  .txt {
    line-height: 1.2;
    font-size: 15px;
  }

  /* Auswahl soll klar leuchten */
  .answers button.selected {
    border-color: #7c8cff;
    background: rgba(124, 140, 255, 0.22);
    box-shadow: 0 0 22px rgba(124, 140, 255, 0.7);
  }

  /* Richtig/Falsch erst nach Auswertung */
  .answers button.correct {
    border-color: #00c878;
    background: rgba(0, 200, 120, 0.22);
    box-shadow: 0 0 18px rgba(0, 200, 120, 0.55);
  }

  .answers button.wrong {
    border-color: #ff5050;
    background: rgba(255, 80, 80, 0.22);
    box-shadow: 0 0 18px rgba(255, 80, 80, 0.55);
  }

  .nav {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 18px;
  }

  .arrow {
    width: 56px;
    height: 44px;
    border-radius: 14px;
    border: 1px solid rgba(255, 255, 255, 0.14);
    background: rgba(255, 255, 255, 0.08);
    color: #fff;
    font-size: 20px;
    cursor: pointer;
    transition: background 0.2s ease;
  }

  .arrow:hover {
    background: rgba(255, 255, 255, 0.12);
  }

  .finish {
    padding: 12px 16px;
    border-radius: 14px;
    border: 1px solid rgba(124, 140, 255, 0.55);
    background: rgba(124, 140, 255, 0.22);
    color: #fff;
    font-weight: 800;
    cursor: pointer;
    box-shadow: 0 0 16px rgba(124, 140, 255, 0.35);
  }

  button:disabled {
    opacity: 0.45;
    cursor: not-allowed;
  }

  .hint {
    margin: 12px 0 0;
    font-size: 13px;
    opacity: 0.72;
  }

  .result {
    width: min(760px, 92vw);
    margin-top: 14px;
    padding: 18px;
    border-radius: 18px;
    background: rgba(20, 24, 50, 0.92);
    border: 1px solid rgba(255, 255, 255, 0.12);
    text-align: center;
    position: relative;
    overflow: hidden;
  }

  .result h3 {
    margin: 0 0 6px;
  }

  .result p {
    margin: 0;
    opacity: 0.85;
  }

  .final {
    position: relative;
    padding: 10px 8px;
  }

  .confetti {
    position: absolute;
    left: 50%;
    top: 30px;
    width: 1px;
    height: 1px;
    pointer-events: none;
  }

  .piece {
    position: absolute;
    width: 10px;
    height: 14px;
    border-radius: 4px;
    background: #fff;
    box-shadow: 0 0 14px rgba(255, 255, 255, 0.15);
  }

  .ring {
    position: absolute;
    left: 50%;
    top: 45px;
    width: 190px;
    height: 190px;
    border-radius: 999px;
    border: 2px solid rgba(124, 140, 255, 0.9);
    transform: translate(-50%, -50%) scale(0.3);
    opacity: 0;
    pointer-events: none;
    filter: blur(0.2px);
  }
</style>
