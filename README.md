<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Bottle Breaker Music</title>
  <style>
    /* -------------------- Basic reset & fonts -------------------- */
    :root{
      --bg-dark:#0b0f13;
      --card:#0f1720;
      --accent1:#00b4ff;
      --accent2:#ff6b2d;
      --muted:#9aa6b2;
    }
    *{box-sizing:border-box;margin:0;padding:0}
    html,body{height:100%}
    body{
      font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      background: radial-gradient(circle at 20% 20%, #07121a 0%, var(--bg-dark) 40%);
      color:#e7eef6;
      display:flex;
      align-items:center;
      justify-content:center;
      padding:20px;
    }

    /* -------------------- Splash screen -------------------- */
    .splash {
      position:fixed;
      inset:0;
      display:flex;
      flex-direction:column;
      align-items:center;
      justify-content:center;
      gap:18px;
      background:linear-gradient(180deg, rgba(2,6,8,0.85), rgba(2,6,8,0.95));
      z-index:1000;
      transition:opacity .6s ease, transform .6s ease;
    }
    .splash.hidden{
      opacity:0;
      transform:scale(0.98);
      pointer-events:none;
    }
    .logo-wrap{
      width:230px;
      height:230px;
      border-radius:50%;
      display:grid;
      place-items:center;
      background: conic-gradient(from 120deg, rgba(0,180,255,0.08), rgba(255,107,45,0.06));
      box-shadow: 0 12px 35px rgba(0,0,0,0.6), inset 0 1px 0 rgba(255,255,255,0.02);
      border: 4px solid rgba(255,255,255,0.03);
      transform:translateY(-10px) scale(.98);
      animation:logoPop .9s cubic-bezier(.2,.9,.2,1) forwards;
    }
    @keyframes logoPop {
      from{transform:translateY(10px) scale(.5) rotate(-10deg);opacity:0}
      to{transform:translateY(0) scale(1) rotate(0);opacity:1}
    }
    .logo-wrap img{width:82%; height:82%; object-fit:contain; display:block; filter: drop-shadow(0 6px 18px rgba(0,0,0,0.6));}

    h1.splash-title{
      font-size:1.45rem;
      letter-spacing:2px;
      color:var(--accent1);
      text-transform:uppercase;
      text-align:center;
      text-shadow:0 6px 30px rgba(0,180,255,0.08);
      animation:glow 1.6s linear infinite alternate;
    }
    @keyframes glow { from{text-shadow:0 6px 20px rgba(0,180,255,0.06)} to{text-shadow:0 12px 40px rgba(0,180,255,0.14)} }

    .splash-sub{color:var(--muted); font-size:.95rem; margin-top:4px}

    .splash-loader{
      margin-top:12px;
      width:160px;
      height:8px;
      border-radius:999px;
      overflow:hidden;
      background:rgba(255,255,255,0.04);
      position:relative;
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.02);
    }
    .splash-loader::before{
      content:"";
      position:absolute;
      left:-40%;
      top:0; bottom:0;
      width:40%;
      background:linear-gradient(90deg,var(--accent1),var(--accent2));
      animation:slide 1.9s linear infinite;
    }
    @keyframes slide { to{left:100%} }

    .enter-btn{
      margin-top:16px;
      background:linear-gradient(90deg,var(--accent1),var(--accent2));
      border: none;
      padding:10px 18px;
      border-radius:999px;
      color:#07121a;
      font-weight:700;
      cursor:pointer;
      box-shadow: 0 8px 30px rgba(0,0,0,0.5), 0 2px 6px rgba(255,107,45,0.08);
    }

    /* -------------------- Main app UI -------------------- */
    .app {
      width:100%;
      max-width:920px;
      background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      border-radius:14px;
      padding:20px;
      border:1px solid rgba(255,255,255,0.03);
      box-shadow: 0 10px 40px rgba(3,7,10,0.6);
      display:none;
      gap:18px;
    }
    .app.show{display:flex; flex-direction:column}

    .top {
      display:flex; gap:14px; align-items:center; justify-content:space-between;
    }
    .brand{
      display:flex; gap:12px; align-items:center;
    }
    .brand .mini{
      width:56px;height:56px;border-radius:10px;display:grid;place-items:center;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      border:1px solid rgba(255,255,255,0.03);
    }
    .brand .mini img{width:86%;height:86%;object-fit:contain}
    .brand h2{font-size:1.1rem;letter-spacing:1px}
    .brand p{font-size:.82rem;color:var(--muted);margin-top:2px}

    .player {
      display:grid;
      grid-template-columns: 1fr 320px;
      gap:18px;
      margin-top:6px;
    }

    /* left: currently playing */
    .now {
      background: linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.00));
      padding:16px;
      border-radius:10px;
      border:1px solid rgba(255,255,255,0.02);
    }
    .now .cover{
      width:100%;
      height:240px;
      border-radius:10px;
      background:linear-gradient(135deg, rgba(0,180,255,0.06), rgba(255,107,45,0.06));
      display:grid;place-items:center;overflow:hidden;
      border:1px solid rgba(255,255,255,0.02);
    }
    .cover img{width:68%;height:68%;object-fit:contain}
    .track-meta{margin-top:12px}
    .track-meta h3{font-size:1.1rem}
    .track-meta p{color:var(--muted);font-size:.9rem;margin-top:6px}

    /* controls */
    .controls{
      display:flex; align-items:center; gap:14px; margin-top:12px;
    }
    .big-btn{
      background:linear-gradient(90deg,var(--accent1),var(--accent2));
      border:none;padding:12px;border-radius:12px;color:#06121a;font-weight:700;font-size:1rem;cursor:pointer;
      box-shadow:0 8px 24px rgba(0,0,0,0.5)
    }
    .small{
      background:transparent;border:1px solid rgba(255,255,255,0.04);padding:10px;border-radius:10px;color:var(--muted);cursor:pointer
    }
    .progress{
      margin-top:12px;height:9px;background:rgba(255,255,255,0.03);border-radius:999px;overflow:hidden;
      position:relative;
    }
    .progress > .bar{height:100%; width:0%; background:linear-gradient(90deg,var(--accent1),var(--accent2)); transition:width .12s linear}

    .time-row{display:flex;justify-content:space-between;color:var(--muted);font-size:.82rem;margin-top:6px}

    /* right: playlist */
    .playlist{
      background: linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.00));
      padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.02);
      max-height:420px; overflow:auto;
    }
    .song{
      display:flex; gap:12px; align-items:center; padding:8px; border-radius:8px; cursor:pointer;
    }
    .song:hover{background:rgba(255,255,255,0.01)}
    .song .num{width:36px;text-align:center;color:var(--muted)}
    .song .info{flex:1}
    .song .info strong{display:block}
    .song .duration{color:var(--muted); font-size:.9rem}

    /* footer */
    footer{margin-top:14px;color:var(--muted);font-size:.9rem;text-align:center}

    /* responsive */
    @media (max-width:880px){
      .player{grid-template-columns:1fr; }
      .now .cover{height:200px}
      .playlist{max-height:260px}
    }
  </style>
</head>
<body>
  <!-- SPLASH -->
  <div class="splash" id="splash">
    <div class="logo-wrap">
      <!-- Put your high-res logo as your-logo.png (same folder) -->
      <img id="splashLogo" src="your-logo.png" alt="Bottle Breaker Logo">
    </div>
    <h1 class="splash-title">Shatter Limits</h1>
    <div class="splash-sub">Bottle Breaker Music — Feel the Impact</div>

    <div class="splash-loader" role="progressbar" aria-hidden="true"></div>
    <button class="enter-btn" id="enterBtn">Enter App</button>
  </div>

  <!-- MAIN APP -->
  <main class="app" id="app">
    <div class="top">
      <div class="brand">
        <div class="mini"><img src="your-logo.png" alt="logo-mini"></div>
        <div>
          <h2>Bottle Breaker Music</h2>
          <p>Shatter Limits — curated beats</p>
        </div>
      </div>
      <div style="display:flex;gap:10px;align-items:center">
        <button class="small" id="shuffleBtn" title="Shuffle">Shuffle</button>
        <button class="small" id="repeatBtn" title="Repeat">Repeat</button>
      </div>
    </div>

    <div class="player">
      <!-- CURRENTLY PLAYING -->
      <section class="now">
        <div class="cover"><img id="coverImg" src="your-logo.png" alt="cover"></div>
        <div class="track-meta">
          <h3 id="trackTitle">Track Title</h3>
          <p id="trackArtist">Artist Name</p>

          <div class="controls">
            <button class="small" id="prevBtn">Prev</button>
            <button class="big-btn" id="playBtn">Play</button>
            <button class="small" id="nextBtn">Next</button>

            <input id="vol" type="range" min="0" max="1" step="0.01" value="0.9" style="width:120px;margin-left:6px">
          </div>

          <div class="progress" aria-hidden="true">
            <div class="bar" id="progressBar"></div>
          </div>
          <div class="time-row">
            <div id="currentTime">0:00</div>
            <div id="duration">0:00</div>
          </div>
        </div>
      </section>

      <!-- PLAYLIST -->
      <aside class="playlist" id="playlist">
        <!-- Song items injected by JS -->
      </aside>
    </div>

    <footer>Made with ⚡ for your beats — Replace sample tracks with your own.</footer>
  </main>

  <!-- Hidden audio element -->
  <audio id="audio" preload="auto"></audio>

  <script>
    /**********************
     * Splash -> App logic
     **********************/
    const splash = document.getElementById('splash');
    const enterBtn = document.getElementById('enterBtn');
    const app = document.getElementById('app');

    function showApp() {
      splash.classList.add('hidden');
      setTimeout(()=> {
        splash.style.display = 'none';
        app.classList.add('show');
      }, 650);
    }

    // Allow user to enter manually
    enterBtn.addEventListener('click', showApp);

    // Auto-enter after 3.8s (like a loader)
    setTimeout(showApp, 3800);


    /**********************
     * Player logic
     **********************/
    const audio = document.getElementById('audio');
    const playBtn = document.getElementById('playBtn');
    const prevBtn = document.getElementById('prevBtn');
    const nextBtn = document.getElementById('nextBtn');
    const progressBar = document.getElementById('progressBar');
    const currentTimeEl = document.getElementById('currentTime');
    const durationEl = document.getElementById('duration');
    const vol = document.getElementById('vol');
    const playlistEl = document.getElementById('playlist');
    const trackTitle = document.getElementById('trackTitle');
    const trackArtist = document.getElementById('trackArtist');

    // ----------------- Playlist (replace with your real tracks) -----------------
    // Put the mp3 files (or urls) in same folder as index.html or use full URLs.
    const tracks = [
      { title: "Breaker Intro", artist: "Bottle Breaker", file: "track1.mp3", cover:"your-logo.png", duration:0 },
      { title: "Crash Beat", artist: "Bottle Breaker", file: "track2.mp3", cover:"your-logo.png", duration:0 },
      { title: "Shatter Drop", artist: "Bottle Breaker", file: "track3.mp3", cover:"your-logo.png", duration:0 }
    ];

    let currentIndex = 0;
    let isPlaying = false;
    let isShuffle = false;
    let isRepeat = false;

    function loadTrack(index){
      const t = tracks[index];
      audio.src = t.file;
      trackTitle.textContent = t.title;
      trackArtist.textContent = t.artist;
      document.getElementById('coverImg').src = t.cover;
      // reset progress UI
      progressBar.style.width = '0%';
      currentTimeEl.textContent = '0:00';
      durationEl.textContent = '0:00';
      // try to load metadata for duration (if CORS allows)
      audio.load();
    }

    function playPause(){
      if (isPlaying) { audio.pause(); }
      else { audio.play(); }
    }

    audio.addEventListener('play', () => { isPlaying = true; playBtn.textContent = 'Pause'; });
    audio.addEventListener('pause', () => { isPlaying = false; playBtn.textContent = 'Play'; });

    playBtn.addEventListener('click', playPause);

    prevBtn.addEventListener('click', () => {
      if (audio.currentTime > 3) { audio.currentTime = 0; }
      else { currentIndex = (currentIndex -1 + tracks.length) % tracks.length; loadTrack(currentIndex); audio.play(); }
      highlightActive();
    });

    nextBtn.addEventListener('click', () => {
      nextTrack();
    });

    function nextTrack(){
      if (isShuffle){
        currentIndex = Math.floor(Math.random()*tracks.length);
      } else {
        currentIndex = (currentIndex + 1) % tracks.length;
      }
      loadTrack(currentIndex);
      audio.play();
      highlightActive();
    }

    audio.addEventListener('timeupdate', () => {
      if (!audio.duration) return;
      const percent = (audio.currentTime / audio.duration) * 100;
      progressBar.style.width = percent + '%';
      currentTimeEl.textContent = formatTime(audio.currentTime);
      durationEl.textContent = formatTime(audio.duration);
    });

    audio.addEventListener('ended', () => {
      if (isRepeat) { audio.currentTime = 0; audio.play(); }
      else nextTrack();
    });

    // Seek by clicking progress bar (simple)
    document.querySelector('.progress').addEventListener('click', (e) => {
      const rect = e.currentTarget.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const percent = x / rect.width;
      if (audio.duration) audio.currentTime = percent * audio.duration;
    });

    // volume
    vol.addEventListener('input', (e) => { audio.volume = e.target.value; });

    function formatTime(t){
      if (!t || isNaN(t)) return '0:00';
      const m = Math.floor(t/60);
      const s = Math.floor(t%60).toString().padStart(2,'0');
      return m + ':' + s;
    }

    // ----------------- Playlist UI rendering -----------------
    function renderPlaylist(){
      playlistEl.innerHTML = '';
      tracks.forEach((tr, i) => {
        const row = document.createElement('div');
        row.className = 'song';
        row.dataset.index = i;
        row.innerHTML = `
          <div class="num">${i+1}</div>
          <div class="info"><strong>${escapeHtml(tr.title)}</strong><span style="color:var(--muted);font-size:.9rem">${escapeHtml(tr.artist)}</span></div>
          <div class="duration">--:--</div>
        `;
        row.addEventListener('click', ()=> {
          currentIndex = i;
          loadTrack(currentIndex);
          audio.play();
          highlightActive();
        });
        playlistEl.appendChild(row);

        // Try to load duration for each track (may be blocked cross-origin)
        const tmp = document.createElement('audio');
        tmp.src = tr.file;
        tmp.addEventListener('loadedmetadata', () => {
          const d = formatTime(tmp.duration);
          row.querySelector('.duration').textContent = d;
        }, { once:true });
      });
    }

    function highlightActive(){
      document.querySelectorAll('.song').forEach(el=> el.style.background='transparent');
      const active = document.querySelector(`.song[data-index="${currentIndex}"]`);
      if (active) active.style.background = 'linear-gradient(90deg, rgba(0,180,255,0.04), rgba(255,107,45,0.03))';
    }

    // shuffle & repeat
    document.getElementById('shuffleBtn').addEventListener('click', (e)=> {
      isShuffle = !isShuffle;
      e.target.style.opacity = isShuffle ? 1 : 0.6;
    });
    document.getElementById('repeatBtn').addEventListener('click', (e)=> {
      isRepeat = !isRepeat;
      e.target.style.opacity = isRepeat ? 1 : 0.6;
    });

    function escapeHtml(text){ return (text+'').replace(/[&<>"']/g, (m)=> ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m])); }

    // Initialize
    renderPlaylist();
    loadTrack(currentIndex);
    highlightActive();

    // set default volume
    audio.volume = vol.value;

    // allow keyboard controls (space to play/pause)
    window.addEventListener('keydown', (e) => {
      if (e.code === 'Space') { e.preventDefault(); playPause(); }
      if (e.code === 'ArrowRight') nextTrack();
      if (e.code === 'ArrowLeft') {
        audio.currentTime = Math.max(0, audio.currentTime - 5);
      }
    });
  </script>
</body>
</html>
