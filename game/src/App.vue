<template>
  <div class="scene">

    <!-- ═══════════════════════════════════════
         BACKGROUND SCENE
    ════════════════════════════════════════ -->
    <div class="bg-sky">
      <div class="sun"></div>
      <div class="cloud cloud1"></div>
      <div class="cloud cloud2"></div>
      <div class="cloud cloud3"></div>
    </div>

    <!-- House -->
    <div class="house-wrap">
      <div class="house">
        <div class="roof"></div>
        <div class="house-body">
          <div class="window win-left">
            <div class="curtain-left"></div>
            <div class="curtain-right"></div>
          </div>
          <div class="door">
            <div class="doorknob"></div>
          </div>
          <div class="window win-right">
            <div class="curtain-left"></div>
            <div class="curtain-right"></div>
          </div>
        </div>
        <div class="chimney">
          <div class="smoke s1"></div>
          <div class="smoke s2"></div>
          <div class="smoke s3"></div>
        </div>
      </div>
    </div>

    <!-- Trees -->
    <div class="tree tree1">
      <div class="tree-top"></div>
      <div class="tree-trunk"></div>
    </div>
    <div class="tree tree2">
      <div class="tree-top"></div>
      <div class="tree-trunk"></div>
    </div>
    <div class="tree tree3">
      <div class="tree-top t-small"></div>
      <div class="tree-trunk"></div>
    </div>

    <!-- Ground / Grass -->
    <div class="ground"></div>
    <div class="grass-strip"></div>

    <!-- Flowers -->
    <div class="flower f1">🌸</div>
    <div class="flower f2">🌼</div>
    <div class="flower f3">🌸</div>
    <div class="flower f4">🌼</div>

    <!-- ═══════════════════════════════════════
         LAYOUT WRAPPER
    ════════════════════════════════════════ -->
    <div class="layout">

      <!-- ── GAME PANEL ── -->
      <div class="game-panel">

        <div class="header">
          <h1 class="game-title">🪣 Whack-a-Mole</h1>
          <p class="game-subtitle">Vue.js Edition · SWDVF301</p>
        </div>

        <!-- Stats -->
        <div class="stats">
          <div class="stat">
            <div class="stat-label">Score</div>
            <div class="stat-value">{{ score }}</div>
          </div>
          <div class="stat">
            <div class="stat-label">Time</div>
            <div class="stat-value" :class="{ danger: timeLeft <= 5 && isRunning }">
              {{ isRunning ? timeLeft + 's' : '—' }}
            </div>
          </div>
          <div class="stat">
            <div class="stat-label">Hits</div>
            <div class="stat-value">{{ hits }}</div>
          </div>
        </div>

        <!-- Controls -->
        <div class="controls">
          <button class="btn btn-start" @click="startGame" :disabled="isRunning">
            {{ isRunning ? 'Running…' : '▶ Start Game' }}
          </button>
          <button class="btn btn-stop" v-if="isRunning" @click="stopGame">■ Stop</button>
        </div>

        <!-- Game Over -->
        <transition name="fade">
          <div class="gameover" v-if="gameOver">
            <div class="gameover-title">🎉 Game Over!</div>
            <div class="gameover-score">{{ score }}</div>
            <div class="gameover-label">points · {{ hits }} hits</div>
          </div>
        </transition>

        <!-- Board -->
        <div class="board">
          <div class="grid">
            <div
              v-for="index in 9"
              :key="index - 1"
              class="hole"
              :class="{
                active:    activeMole === (index - 1),
                whacked:   whackedHole === (index - 1),
                miss:      missHole === (index - 1),
                clickable: activeMole === (index - 1)
              }"
              @click="whackMole(index - 1)"
            >
              <span v-if="activeMole === (index - 1)" class="mole" aria-label="Mole">🐭</span>
              <span v-if="showPop === (index - 1)" class="score-pop" aria-hidden="true">+10</span>
            </div>
          </div>
        </div>

        <p class="message">
          <span v-if="!isRunning && !gameOver">Press Start — 30 seconds on the clock!</span>
          <span v-else-if="isRunning">Whack the mole! 🪄</span>
        </p>

      </div><!-- /game-panel -->


      <!-- ── CARTOON SPECTATOR ── -->
      <div class="spectator-panel">

        <!--
          ╔══════════════════════════════════════════════════════════╗
          ║  SPEECH BUBBLE — rendered OUTSIDE the .spectator div     ║
          ║  so it is NOT clipped by any overflow:hidden parent.     ║
          ║  z-index: 9999 ensures it always floats above the scene. ║
          ╚══════════════════════════════════════════════════════════╝
        -->
        <transition name="bubble">
          <div class="speech-bubble-wrap" v-if="bubbleText">
            <div class="speech-bubble">{{ bubbleText }}</div>
          </div>
        </transition>

        <div class="spectator" :class="spectatorMood">

          <!-- Body -->
          <div class="char-body">

            <!-- Head -->
            <div class="char-head">
              <!-- Hair -->
              <div class="hair">
                <div class="hair-strand h1"></div>
                <div class="hair-strand h2"></div>
                <div class="hair-strand h3"></div>
              </div>
              <!-- Ears -->
              <div class="ear ear-left"></div>
              <div class="ear ear-right"></div>
              <!-- Face -->
              <div class="face">
                <!-- Eyes -->
                <div class="eyes">
                  <div class="eye eye-left">
                    <div class="pupil"></div>
                    <div class="eyebrow brow-left" :class="{ excited: mood==='excited', shocked: mood==='shocked' }"></div>
                  </div>
                  <div class="eye eye-right">
                    <div class="pupil"></div>
                    <div class="eyebrow brow-right" :class="{ excited: mood==='excited', shocked: mood==='shocked' }"></div>
                  </div>
                </div>
                <!-- Nose -->
                <div class="nose"></div>
                <!-- Mouth -->
                <div class="mouth" :class="mood"></div>
                <!-- Cheeks -->
                <div class="cheek cheek-l"></div>
                <div class="cheek cheek-r"></div>
              </div>
              <!-- Beanie hat -->
              <div class="hat">
                <div class="hat-stripe"></div>
                <div class="hat-pompom"></div>
              </div>
            </div>

            <!-- Torso -->
            <div class="torso">
              <div class="shirt-stripe"></div>
              <!-- Arms -->
              <div class="arm arm-left" :class="{ cheer: mood==='excited' }"></div>
              <div class="arm arm-right" :class="{ cheer: mood==='excited' }">
                <!-- Hand holding popcorn -->
                <div class="popcorn-cup">
                  <div class="popcorn-pop p1">🍿</div>
                </div>
              </div>
              <!-- Legs -->
              <div class="legs">
                <div class="leg leg-left"></div>
                <div class="leg leg-right"></div>
              </div>
            </div>

          </div>

        </div><!-- /spectator -->

        <!-- Label -->
        <div class="spectator-label">Your #1 Fan 🎪</div>
      </div><!-- /spectator-panel -->

    </div><!-- /layout -->

  </div><!-- /scene -->
</template>


<script>
import { ref, computed, onMounted, onUnmounted } from 'vue'

export default {
  name: 'WhackAMole',

  setup() {
    // ── Reactive state ──
    const score       = ref(0)
    const hits        = ref(0)
    const activeMole  = ref(null)
    const isRunning   = ref(false)
    const gameOver    = ref(false)
    const timeLeft    = ref(30)
    const whackedHole = ref(null)
    const showPop     = ref(null)
    const missHole    = ref(null)

    // Cartoon mood
    const mood       = ref('idle')   // idle | watching | excited | shocked | sad
    const bubbleText = ref('Come on, start playing! 🎮')

    const spectatorMood = computed(() => `mood-${mood.value}`)

    // Timer refs
    let gameInterval  = null
    let timerInterval = null
    let moleTimeout   = null
    let bubbleTimer   = null

    function showBubble(text, duration = 2200) {
      bubbleText.value = text
      clearTimeout(bubbleTimer)
      bubbleTimer = setTimeout(() => { bubbleText.value = '' }, duration)
    }

    // ── Part B: showMole() ──
    function showMole() {
      const random = Math.floor(Math.random() * 9)
      activeMole.value = random
      if (moleTimeout) clearTimeout(moleTimeout)
      moleTimeout = setTimeout(() => {
        activeMole.value = null
      }, 1000)
    }

    // ── Start game ──
    function startGame() {
      score.value      = 0
      hits.value       = 0
      timeLeft.value   = 30
      activeMole.value = null
      gameOver.value   = false
      isRunning.value  = true
      mood.value       = 'watching'

      showBubble('Go go go!! 🚀', 1800)
      showMole()
      gameInterval  = setInterval(showMole, 1200)
      timerInterval = setInterval(() => {
        timeLeft.value--
        if (timeLeft.value === 10) { mood.value = 'shocked'; showBubble('Only 10 seconds left! 😱') }
        if (timeLeft.value <= 0) stopGame()
      }, 1000)
    }

    // ── Stop game ──
    function stopGame() {
      isRunning.value  = false
      gameOver.value   = true
      activeMole.value = null
      clearInterval(gameInterval)
      clearInterval(timerInterval)
      if (moleTimeout) clearTimeout(moleTimeout)

      mood.value = score.value >= 100 ? 'excited' : 'sad'
      showBubble(
        score.value >= 100
          ? `WOW ${score.value} pts! You're a legend! 🏆`
          : `${score.value} pts — practice makes perfect! 💪`,
        4000
      )
    }

    // ── Whack handler ──
    function whackMole(index) {
      if (!isRunning.value) return

      if (activeMole.value === index) {
        score.value += 10
        hits.value  += 1
        activeMole.value = null
        if (moleTimeout) clearTimeout(moleTimeout)

        whackedHole.value = index
        showPop.value     = index

        // Cartoon reacts
        mood.value = 'excited'
        if (hits.value % 3 === 0) showBubble(
          ['Nice hit! 👊', 'BOOM! 💥', 'Got em! 🎯', 'Yes YES!! 🙌'][Math.floor(Math.random() * 4)]
        )
        setTimeout(() => {
          mood.value        = 'watching'
          whackedHole.value = null
          showPop.value     = null
        }, 450)
      } else {
        missHole.value = index
        if (Math.random() > 0.6) showBubble(['Missed! 😬', 'Oops! 😅', 'Almost! 🫣'][Math.floor(Math.random() * 3)])
        setTimeout(() => { missHole.value = null }, 380)
      }
    }

    // Part A: lifecycle hooks
    onMounted(() => {
      setTimeout(() => showBubble('Hi! I\'m here to cheer you on! 🎉', 3000), 600)
    })

    onUnmounted(() => {
      clearInterval(gameInterval)
      clearInterval(timerInterval)
      if (moleTimeout) clearTimeout(moleTimeout)
      clearTimeout(bubbleTimer)
    })

    return {
      score, hits, activeMole, isRunning, gameOver,
      timeLeft, whackedHole, showPop, missHole,
      mood, bubbleText, spectatorMood,
      startGame, stopGame, whackMole
    }
  }
}
</script>


<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:wght@400;600;700;800&display=swap');

/* ═══════════════════════════════════
   ROOT / SCENE
════════════════════════════════════ */
* { box-sizing: border-box; margin: 0; padding: 0; }

.scene {
  font-family: 'Nunito', sans-serif;
  min-height: 100vh;
  width: 100%;
  position: relative;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

/* ═══════════════════════════════════
   SKY & SUN & CLOUDS
════════════════════════════════════ */
.bg-sky {
  position: fixed;
  inset: 0;
  background: linear-gradient(180deg, #87CEEB 0%, #B0E0FF 55%, #c8f0a0 55%, #7ec850 75%, #5a9e3a 100%);
  z-index: 0;
}

.sun {
  position: absolute;
  top: 40px; right: 80px;
  width: 70px; height: 70px;
  background: radial-gradient(circle, #FFE566 60%, #FFD700 100%);
  border-radius: 50%;
  box-shadow: 0 0 0 12px rgba(255,230,80,0.25), 0 0 0 24px rgba(255,230,80,0.1);
  animation: sun-pulse 4s ease-in-out infinite;
}

@keyframes sun-pulse {
  0%, 100% { box-shadow: 0 0 0 12px rgba(255,230,80,0.25), 0 0 0 24px rgba(255,230,80,0.1); }
  50%       { box-shadow: 0 0 0 18px rgba(255,230,80,0.3),  0 0 0 36px rgba(255,230,80,0.12); }
}

.cloud {
  position: absolute;
  background: #fff;
  border-radius: 50px;
  opacity: 0.9;
}
.cloud::before, .cloud::after {
  content: '';
  position: absolute;
  background: #fff;
  border-radius: 50%;
}

.cloud1 { width: 120px; height: 36px; top: 60px; left: 5%;
  animation: drift 18s linear infinite; }
.cloud1::before { width: 56px; height: 56px; top: -28px; left: 18px; }
.cloud1::after  { width: 40px; height: 40px; top: -20px; left: 58px; }

.cloud2 { width: 90px; height: 28px; top: 30px; left: 30%;
  animation: drift 24s linear infinite 4s; opacity: 0.75; }
.cloud2::before { width: 44px; height: 44px; top: -22px; left: 14px; }
.cloud2::after  { width: 34px; height: 34px; top: -16px; left: 46px; }

.cloud3 { width: 100px; height: 30px; top: 80px; left: 55%;
  animation: drift 20s linear infinite 8s; opacity: 0.8; }
.cloud3::before { width: 48px; height: 48px; top: -24px; left: 16px; }
.cloud3::after  { width: 36px; height: 36px; top: -18px; left: 52px; }

@keyframes drift {
  from { transform: translateX(-160px); }
  to   { transform: translateX(110vw); }
}

/* ═══════════════════════════════════
   HOUSE
════════════════════════════════════ */
.house-wrap {
  position: fixed;
  bottom: 80px;
  left: 2%;
  z-index: 1;
}

.house {
  position: relative;
  width: 180px;
}

.roof {
  width: 0; height: 0;
  border-left: 100px solid transparent;
  border-right: 100px solid transparent;
  border-bottom: 80px solid #e53935;
  filter: drop-shadow(0 -3px 6px rgba(0,0,0,0.15));
  margin-left: -10px;
}

.house-body {
  width: 160px;
  height: 110px;
  background: #FFF8E1;
  border: 3px solid #BCAAA4;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: space-around;
  padding: 0 8px;
  position: relative;
}

.window {
  width: 38px; height: 38px;
  background: #B3E5FC;
  border: 3px solid #90CAF9;
  border-radius: 4px;
  overflow: hidden;
  display: flex;
  position: relative;
}

.curtain-left, .curtain-right {
  position: absolute;
  width: 45%;
  height: 100%;
  top: 0;
  animation: curtain-sway 3s ease-in-out infinite;
}
.curtain-left  { left: 0;  background: #EF9A9A; border-radius: 0 4px 4px 0; transform-origin: top left; }
.curtain-right { right: 0; background: #EF9A9A; border-radius: 4px 0 0 4px; transform-origin: top right; }

@keyframes curtain-sway {
  0%, 100% { transform: skewY(0deg); }
  50%       { transform: skewY(4deg); }
}

.door {
  width: 34px; height: 60px;
  background: #8D6E63;
  border: 3px solid #6D4C41;
  border-radius: 4px 4px 0 0;
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding-right: 5px;
}

.doorknob {
  width: 6px; height: 6px;
  background: #FFD54F;
  border-radius: 50%;
}

.chimney {
  position: absolute;
  top: -90px; right: 26px;
  width: 28px;
  height: 50px;
  background: #B71C1C;
  border: 2px solid #8B0000;
}

.smoke {
  position: absolute;
  width: 14px; height: 14px;
  background: rgba(200,200,200,0.7);
  border-radius: 50%;
  left: 50%;
  transform: translateX(-50%);
  animation: smoke-rise 3s ease-out infinite;
}
.s1 { top: -10px; animation-delay: 0s; }
.s2 { top: -10px; animation-delay: 1s; }
.s3 { top: -10px; animation-delay: 2s; }

@keyframes smoke-rise {
  0%   { transform: translateX(-50%) scale(0.4); opacity: 0.8; top: -10px; }
  100% { transform: translateX(calc(-50% + 16px)) scale(2); opacity: 0; top: -70px; }
}

/* ═══════════════════════════════════
   TREES
════════════════════════════════════ */
.tree { position: fixed; bottom: 75px; z-index: 1; }
.tree1 { left: 20%;  }
.tree2 { right: 4%;  }
.tree3 { right: 16%; }

.tree-top {
  width: 0; height: 0;
  border-left: 28px solid transparent;
  border-right: 28px solid transparent;
  border-bottom: 58px solid #388E3C;
  margin: 0 auto;
  filter: drop-shadow(0 2px 4px rgba(0,0,0,0.2));
  animation: tree-sway 4s ease-in-out infinite;
  transform-origin: bottom center;
}

.t-small {
  border-left-width: 20px;
  border-right-width: 20px;
  border-bottom-width: 44px;
  border-bottom-color: #43A047;
}

@keyframes tree-sway {
  0%, 100% { transform: rotate(-2deg); }
  50%       { transform: rotate(2deg); }
}

.tree-trunk {
  width: 14px; height: 24px;
  background: #6D4C41;
  border-radius: 2px;
  margin: 0 auto;
}

/* ═══════════════════════════════════
   GROUND
════════════════════════════════════ */
.ground {
  position: fixed;
  bottom: 0;
  left: 0; right: 0;
  height: 80px;
  background: #5a9e3a;
  z-index: 1;
}

.grass-strip {
  position: fixed;
  bottom: 76px;
  left: 0; right: 0;
  height: 14px;
  background: #7ec850;
  border-radius: 50% 50% 0 0 / 8px 8px 0 0;
  z-index: 2;
}

/* Flowers */
.flower {
  position: fixed;
  bottom: 78px;
  font-size: 20px;
  z-index: 3;
  animation: flower-bob 2.5s ease-in-out infinite;
}
.f1 { left: 14%;  animation-delay: 0s; }
.f2 { left: 26%;  animation-delay: 0.6s; }
.f3 { right: 22%; animation-delay: 1.1s; }
.f4 { right: 9%;  animation-delay: 0.3s; }

@keyframes flower-bob {
  0%, 100% { transform: rotate(-8deg); }
  50%       { transform: rotate(8deg); }
}

/* ═══════════════════════════════════
   LAYOUT
════════════════════════════════════ */
.layout {
  position: relative;
  z-index: 10;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  gap: 32px;
  padding: 2rem 1.5rem 6rem;
  min-height: 100vh;
}

/* ═══════════════════════════════════
   GAME PANEL
════════════════════════════════════ */
.game-panel {
  background: rgba(255,255,255,0.88);
  backdrop-filter: blur(8px);
  border-radius: 28px;
  padding: 1.6rem 1.4rem;
  width: 100%;
  max-width: 420px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.18), 0 2px 0 rgba(255,255,255,0.6) inset;
  border: 2px solid rgba(255,255,255,0.7);
  flex-shrink: 0;
}

.header { text-align: center; margin-bottom: 1rem; }

.game-title {
  font-family: 'Fredoka One', cursive;
  font-size: 2.4rem;
  color: #2e7d32;
  text-shadow: 2px 2px 0 rgba(0,0,0,0.08);
  line-height: 1;
}

.game-subtitle {
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: #888;
  margin-top: 2px;
}

.stats {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 1rem;
}

.stat {
  background: #FFFDE7;
  border-radius: 14px;
  padding: 0.4rem 1rem;
  min-width: 80px;
  text-align: center;
  box-shadow: 0 4px 0 #795548;
}

.stat-label {
  font-size: 10px; font-weight: 800;
  text-transform: uppercase; letter-spacing: 1.5px;
  color: #795548;
}

.stat-value {
  font-family: 'Fredoka One', cursive;
  font-size: 1.8rem; color: #3e2723; line-height: 1.1;
}
.stat-value.danger { color: #ff7043; animation: blink 0.5s step-start infinite; }

@keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0.3; } }

.controls {
  display: flex; justify-content: center; gap: 10px; margin-bottom: 1rem;
}

.btn {
  font-family: 'Fredoka One', cursive;
  font-size: 1rem; padding: 0.5rem 1.6rem;
  border: none; border-radius: 50px; cursor: pointer;
  transition: transform 0.1s;
}
.btn:active  { transform: scale(0.95) translateY(2px); }
.btn:disabled { opacity: 0.5; cursor: not-allowed; }

.btn-start { background: #ff7043; color: #fff; box-shadow: 0 5px 0 #e64a19; }
.btn-start:hover:not(:disabled) { transform: translateY(-2px); }
.btn-stop  { background: #fff; color: #ff7043; box-shadow: 0 5px 0 #ddd; }
.btn-stop:hover { transform: translateY(-2px); }

.gameover {
  background: #FFFDE7; border-radius: 16px;
  padding: 1rem; margin-bottom: 1rem; text-align: center;
  box-shadow: 0 6px 0 #795548;
}
.gameover-title { font-family: 'Fredoka One', cursive; font-size: 1.6rem; color: #ff7043; }
.gameover-score { font-family: 'Fredoka One', cursive; font-size: 3rem; color: #3e2723; line-height: 1; }
.gameover-label { font-size: 11px; font-weight: 800; text-transform: uppercase; letter-spacing: 2px; color: #795548; }

.board {
  background: linear-gradient(180deg, #a5d6a7 0%, #4caf50 100%);
  border-radius: 18px; padding: 1.2rem;
  box-shadow: 0 6px 0 #388e3c;
}

.grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; }

.hole {
  aspect-ratio: 1; border-radius: 50%;
  background: radial-gradient(ellipse at 40% 35%, #795548, #4e342e);
  box-shadow: inset 0 6px 18px rgba(0,0,0,0.5), 0 4px 0 rgba(0,0,0,0.2);
  display: flex; align-items: center; justify-content: center;
  cursor: pointer; position: relative; overflow: hidden;
  transition: transform 0.08s;
}
.hole:hover.clickable  { transform: scale(1.06); }
.hole.active           { box-shadow: inset 0 6px 18px rgba(0,0,0,0.4), 0 0 0 3px #fdd835; cursor: crosshair; }
.hole.whacked          { animation: whack-anim 0.3s ease; }
.hole.miss             { animation: miss-anim 0.35s ease; }

@keyframes whack-anim {
  0%  { transform: scale(1); }
  30% { transform: scale(0.82); }
  70% { transform: scale(1.06); }
  100%{ transform: scale(1); }
}

@keyframes miss-anim {
  0%,100% { box-shadow: inset 0 6px 18px rgba(0,0,0,0.5); }
  50%     { box-shadow: inset 0 6px 18px rgba(0,0,0,0.4), 0 0 0 3px #ef5350; }
}

.mole {
  font-size: 2.5rem;
  animation: pop-up 0.18s cubic-bezier(0.34,1.56,0.64,1);
  pointer-events: none; user-select: none;
  filter: drop-shadow(0 -2px 4px rgba(0,0,0,0.3));
}

@keyframes pop-up {
  from { transform: translateY(60%) scale(0.5); opacity: 0; }
  to   { transform: translateY(0) scale(1); opacity: 1; }
}

.score-pop {
  position: absolute; top: 6px; right: 6px;
  font-family: 'Fredoka One', cursive; font-size: 1rem;
  color: #fdd835; text-shadow: 1px 1px 0 rgba(0,0,0,0.5);
  animation: score-fly 0.7s ease forwards; pointer-events: none;
}

@keyframes score-fly {
  0%   { opacity: 1; transform: translateY(0); }
  60%  { opacity: 1; transform: translateY(-24px) scale(1.2); }
  100% { opacity: 0; transform: translateY(-40px); }
}

.message {
  text-align: center; margin-top: 0.8rem;
  font-size: 13px; font-weight: 700; color: #555;
  min-height: 18px;
}

/* ═══════════════════════════════════
   SPECTATOR PANEL
════════════════════════════════════ */
.spectator-panel {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 2rem;
  min-width: 160px;
  position: relative; /* needed so speech-bubble-wrap offsets from here */
}

.spectator-label {
  font-size: 12px; font-weight: 800;
  text-transform: uppercase; letter-spacing: 1.5px;
  color: rgba(255,255,255,0.9);
  text-shadow: 0 1px 4px rgba(0,0,0,0.3);
  margin-top: 8px;
}

/* ═══════════════════════════════════
   SPEECH BUBBLE — FIXED (always in front)
   Rendered as first child of .spectator-panel,
   ABOVE the .spectator div in DOM order.
   z-index: 9999 floats it over everything.
════════════════════════════════════ */
.speech-bubble-wrap {
  position: absolute;
  top: -10px;           /* sits above the character's head */
  left: 50%;
  transform: translateX(-50%);
  z-index: 9999;        /* always in front of scene, house, trees, etc. */
  pointer-events: none;
  white-space: nowrap;
}

.speech-bubble {
  background: #fff;
  border: 2.5px solid #ddd;
  border-radius: 16px;
  padding: 10px 14px;
  font-size: 13px;
  font-weight: 700;
  color: #333;
  box-shadow: 0 4px 12px rgba(0,0,0,0.12);
  line-height: 1.4;
  text-align: center;
  position: relative;
}

/* Downward-pointing tail — arrow points DOWN toward the character */
.speech-bubble::after {
  content: '';
  position: absolute;
  bottom: -14px;
  left: 50%;
  transform: translateX(-50%);
  border: 7px solid transparent;
  border-top-color: #ddd;
}
.speech-bubble::before {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  border: 5px solid transparent;
  border-top-color: #fff;
  z-index: 1;
}

/* ── Whole character ── */
.spectator {
  position: relative;
  animation: char-idle 2s ease-in-out infinite;
}

@keyframes char-idle {
  0%, 100% { transform: translateY(0); }
  50%       { transform: translateY(-6px); }
}

.spectator.mood-excited {
  animation: char-cheer 0.4s ease-in-out infinite alternate;
}
@keyframes char-cheer {
  from { transform: translateY(0) rotate(-3deg); }
  to   { transform: translateY(-12px) rotate(3deg); }
}

.spectator.mood-sad { animation: char-sad 1.5s ease-in-out infinite; }
@keyframes char-sad {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50%       { transform: translateY(4px) rotate(-2deg); }
}

/* ── Head ── */
.char-head {
  width: 90px; height: 90px;
  background: #FFCC80;
  border-radius: 50%;
  position: relative;
  margin: 0 auto;
  border: 3px solid #FFA726;
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}

/* Hair */
.hair {
  position: absolute;
  top: -8px; left: 6px;
  width: 74px;
  display: flex; justify-content: space-around;
}

.hair-strand {
  width: 12px; height: 22px;
  background: #5D4037;
  border-radius: 6px 6px 2px 2px;
  transform-origin: bottom center;
  animation: hair-wave 2s ease-in-out infinite;
}
.h1 { animation-delay: 0s; transform: rotate(-12deg); }
.h2 { animation-delay: 0.3s; transform: rotate(0deg); }
.h3 { animation-delay: 0.6s; transform: rotate(12deg); }

@keyframes hair-wave {
  0%, 100% { transform-origin: bottom center; transform: rotate(var(--r, 0deg)) scaleY(1); }
  50%       { transform: rotate(var(--r, 0deg)) scaleY(0.92); }
}

/* Ears */
.ear {
  position: absolute;
  width: 20px; height: 24px;
  background: #FFCC80;
  border-radius: 50%;
  top: 30px;
  border: 3px solid #FFA726;
}
.ear-left  { left: -12px; }
.ear-right { right: -12px; }

/* Face */
.face { position: absolute; inset: 0; }

/* Eyes */
.eyes {
  display: flex;
  justify-content: space-around;
  padding: 0 14px;
  margin-top: 28px;
  position: relative;
}

.eye {
  width: 20px; height: 20px;
  background: #fff;
  border-radius: 50%;
  border: 2px solid #5D4037;
  position: relative;
  overflow: visible;
  animation: eye-blink 4s ease-in-out infinite;
}

@keyframes eye-blink {
  0%, 90%, 100% { transform: scaleY(1); }
  95%           { transform: scaleY(0.08); }
}

.pupil {
  width: 10px; height: 10px;
  background: #3E2723;
  border-radius: 50%;
  position: absolute;
  top: 4px; left: 4px;
  animation: pupil-look 5s ease-in-out infinite;
}

@keyframes pupil-look {
  0%, 40%, 100% { transform: translate(0, 0); }
  20%           { transform: translate(2px, -1px); }
  60%           { transform: translate(-2px, 1px); }
  80%           { transform: translate(1px, 1px); }
}

.eyebrow {
  position: absolute;
  width: 18px; height: 4px;
  background: #5D4037;
  border-radius: 3px;
  top: -8px; left: 0;
  transition: transform 0.3s;
}
.brow-left  { transform: rotate(-6deg); }
.brow-right { transform: rotate(6deg); }
.eyebrow.excited.brow-left  { transform: rotate(-14deg) translateY(-3px); }
.eyebrow.excited.brow-right { transform: rotate(14deg)  translateY(-3px); }
.eyebrow.shocked.brow-left  { transform: rotate(6deg)  translateY(-4px); }
.eyebrow.shocked.brow-right { transform: rotate(-6deg) translateY(-4px); }

/* Nose */
.nose {
  width: 10px; height: 7px;
  background: #FFA726;
  border-radius: 50%;
  margin: 6px auto 0;
}

/* Mouth */
.mouth {
  width: 34px; height: 16px;
  margin: 4px auto 0;
  border-radius: 0 0 20px 20px;
  border: 3px solid #5D4037;
  border-top: none;
  background: #E57373;
  transition: all 0.3s;
}
.mouth.idle     { border-radius: 0 0 12px 12px; height: 10px; }
.mouth.watching { border-radius: 0 0 20px 20px; height: 16px; }
.mouth.excited  {
  border-radius: 0 0 24px 24px;
  height: 22px; width: 40px;
  margin-left: calc(50% - 20px);
  background: #C62828;
}
.mouth.sad {
  border-radius: 20px 20px 0 0;
  border-top: 3px solid #5D4037;
  border-bottom: none;
  height: 12px;
}
.mouth.shocked {
  border-radius: 50%;
  height: 20px; width: 20px;
  margin-left: calc(50% - 10px);
}

/* Cheeks */
.cheek {
  position: absolute;
  width: 18px; height: 10px;
  background: rgba(255,120,80,0.35);
  border-radius: 50%;
  top: 58px;
}
.cheek-l { left: 8px; }
.cheek-r { right: 8px; }

/* Hat */
.hat {
  position: absolute;
  top: -22px; left: 50%;
  transform: translateX(-50%);
  width: 72px; height: 32px;
  background: #1565C0;
  border-radius: 6px 6px 0 0;
  border-bottom: 6px solid #0D47A1;
}

.hat::before {
  content: '';
  position: absolute;
  bottom: -10px; left: -10px;
  width: 92px; height: 10px;
  background: #1565C0;
  border-radius: 4px;
}

.hat-stripe {
  position: absolute;
  top: 8px; left: 0; right: 0;
  height: 6px;
  background: #E53935;
}

.hat-pompom {
  position: absolute;
  top: -12px; left: 50%;
  transform: translateX(-50%);
  width: 16px; height: 16px;
  background: #fff;
  border-radius: 50%;
  animation: pompom-bounce 1s ease-in-out infinite;
}

@keyframes pompom-bounce {
  0%, 100% { transform: translateX(-50%) translateY(0); }
  50%       { transform: translateX(-50%) translateY(-4px); }
}

/* ── Torso ── */
.torso {
  width: 70px; height: 80px;
  background: #E53935;
  border-radius: 10px 10px 14px 14px;
  margin: 0 auto;
  position: relative;
  border: 3px solid #B71C1C;
}

.shirt-stripe {
  width: 100%; height: 8px;
  background: #fff;
  position: absolute; top: 22px;
  border-top: 2px solid rgba(0,0,0,0.1);
  border-bottom: 2px solid rgba(0,0,0,0.1);
}

/* Arms */
.arm {
  position: absolute;
  width: 20px; height: 50px;
  background: #E53935;
  border: 3px solid #B71C1C;
  border-radius: 10px;
  top: 4px;
  transition: transform 0.3s;
}

.arm-left  {
  left: -22px;
  border-radius: 10px;
  transform: rotate(20deg);
  transform-origin: top center;
}
.arm-right {
  right: -22px;
  transform: rotate(-20deg);
  transform-origin: top center;
}

.arm-left.cheer  { transform: rotate(-40deg) translateY(-8px); }
.arm-right.cheer { transform: rotate(40deg)  translateY(-8px); }

.arm::after {
  content: '';
  position: absolute;
  bottom: -8px; left: 50%;
  transform: translateX(-50%);
  width: 18px; height: 18px;
  background: #FFCC80;
  border-radius: 50%;
  border: 2px solid #FFA726;
}

/* Popcorn */
.popcorn-cup {
  position: absolute;
  bottom: -4px; left: 50%;
  transform: translateX(-50%);
  font-size: 20px;
}

.popcorn-pop {
  animation: popcorn-wiggle 0.8s ease-in-out infinite;
}

@keyframes popcorn-wiggle {
  0%, 100% { transform: rotate(-8deg); }
  50%       { transform: rotate(8deg); }
}

/* Legs */
.legs {
  position: absolute;
  bottom: -34px; left: 50%;
  transform: translateX(-50%);
  display: flex; gap: 8px;
}

.leg {
  width: 22px; height: 34px;
  background: #1565C0;
  border: 3px solid #0D47A1;
  border-radius: 0 0 10px 10px;
}

.leg::after {
  content: '';
  display: block;
  width: 28px; height: 12px;
  background: #5D4037;
  border-radius: 0 0 6px 6px;
  margin-top: 22px;
  margin-left: -6px;
  border: 2px solid #4E342E;
}

/* ── Transitions ── */
.bubble-enter-active { animation: bubble-in 0.3s cubic-bezier(0.34,1.56,0.64,1); }
.bubble-leave-active { animation: bubble-out 0.2s ease forwards; }

@keyframes bubble-in {
  from { transform: scale(0.5) translateY(10px); opacity: 0; }
  to   { transform: scale(1) translateY(0); opacity: 1; }
}
@keyframes bubble-out {
  from { transform: scale(1); opacity: 1; }
  to   { transform: scale(0.8) translateY(-6px); opacity: 0; }
}

.fade-enter-active, .fade-leave-active { transition: opacity 0.25s; }
.fade-enter-from, .fade-leave-to { opacity: 0; }

/* ── Responsive ── */
@media (max-width: 700px) {
  .layout { flex-direction: column; align-items: center; gap: 16px; padding-bottom: 8rem; }
  .spectator-panel { padding-top: 0; }
  .house-wrap { left: -10px; transform: scale(0.7); transform-origin: bottom left; }
}
</style>