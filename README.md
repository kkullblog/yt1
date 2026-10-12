
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>YouTube Music Player | Codesign</title>
    <!-- 💡 세련되고 예쁜 무료 구글 웹폰트 (IBM Plex Sans KR) 적용 -->
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans+KR:wght@400;500;600&display=swap" rel="stylesheet">
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        .player-wrapper {
            background-color: #F4F6FF;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'IBM Plex Sans KR', 'Malgun Gothic', sans-serif;
        }

        .container {
            position: relative;
            width: 360px;
            background: linear-gradient(135deg, #FEA3A3, #BFD1FF);
            padding: 50px 20px 30px 20px;
            border-radius: 30px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }

        .container .song-info {
            position: relative;
            z-index: 1;
            margin: 0 15px;
            padding: 190px 15px 30px;
            background: rgba(255, 255, 255, 0.4);
            border-radius: 20px;
            backdrop-filter: blur(5px);
            -webkit-backdrop-filter: blur(5px);
        }

        /* 💡 곡 이름(name) 폰트 스타일 적용 */
        .container .song-info .song-name {
            display: flex;
            justify-content: center;
            font-family: 'IBM Plex Sans KR', sans-serif;
            font-size: 16px;
            font-weight: 600;
            color: #333;
            text-align: center;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }

        /* 💡 아티스트 이름(artist) 폰트 스타일 적용 */
        .container .song-info .artist-name {
            display: flex;
            justify-content: center;
            font-family: 'IBM Plex Sans KR', sans-serif;
            font-size: 13px;
            font-weight: 400;
            margin: 8px 0 20px;
            color: #666;
        }

        .container .song-info .progress-bar {
            background: rgba(255, 255, 255, 0.7);
            border-radius: 20px;
            cursor: pointer;
            height: 6px;
            overflow: hidden;
        }

        .container .song-info .progress-bar .fill-bar {
            width: 0;
            height: 100%;
            border-radius: 20px;
            background-color: #ff4b6e;
            transition: width 0.1s linear;
        }

        .container .song-info .song-time {
            display: flex;
            justify-content: space-between;
        }

        .container .song-info .start-time, 
        .container .song-info .end-time {
            font-size: 11px;
            color: #555;
            margin-top: 6px;
        }

        /* 💡 디스크 위치 가운데 정렬 */
        .container .disk {
            position: absolute;
            z-index: 3;
            top: 75px; 
            left: 50%;
            transform: translateX(-50%);
            width: 160px;
            height: 160px;
            pointer-events: none;
        }

        .container .disk .cover {
            width: 160px;
            height: 160px;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
            background-color: #ddd;
            background-repeat: no-repeat;
            background-position: center;
            background-size: cover;
            border: 5px solid #fff;
            border-radius: 50%;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
        }

        /* 💡 재생 시 회전 애니메이션 적용 */
        .container .disk .cover.active {
            animation: rotate 4s linear infinite;
        }

        .container .disk .circle {
            position: absolute;
            z-index: 2;
            width: 20px;
            height: 20px;
            left: 50%;
            top: 50%;
            transform: translate(-50%, -50%);
            background-color: #fff;
            border-radius: 50%;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }

        .container .controls {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 30px;
            margin-top: 25px;
        }

        .container .controls #play {
            background-color: #fff;
            padding: 12px;
            border-radius: 50%;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
            transition: all 0.3s ease;
        }

        .container .controls #play:hover {
            background: #ff4b6e;
            filter: invert(1);
        }

        .controls img {
            cursor: pointer;
            width: 22px;
            height: 22px;
        }

        @keyframes rotate {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        #youtube-player-container {
            display: none;
        }
    </style>
</head>
<body>

    <div class="player-wrapper">
        <div class="container">
            <div class="disk">
                <div class="circle"></div>
                <div id="cover" class="cover"></div>
            </div>

            <div class="song-info">
                <div class="song-name">Song</div>
                <div class="artist-name">Artist</div>
                <div class="progress-bar">
                    <div class="fill-bar"></div>
                </div>
                <div class="song-time">
                    <div class="start-time">0:00</div>
                    <div class="end-time">0:00</div>
                </div>
            </div>

            <div class="controls">
                <img src="https://img.icons8.com/material-outlined/48/repeat.png" alt="repeat"/>
                <img id="prev" src="https://img.icons8.com/ios-glyphs/90/rewind.png" alt="rewind"/>
                <img id="play" src="https://img.icons8.com/ios-glyphs/90/play--v1.png" alt="play"/>
                <img id="next" src="https://img.icons8.com/ios-glyphs/90/fast-forward.png" alt="fast-forward"/>
                <img src="https://img.icons8.com/fluency-systems-regular/48/video-playlist.png" alt="playlist"/>
            </div>
        </div>
    </div>

    <div id="youtube-player-container">
        <div id="yt-player"></div>
    </div>

    <script src="https://www.youtube.com/iframe_api"></script>

    <script>
        const songsList = [
            { 
                name: "heart (마음)", 
                artist: "IU", 
                videoId: "hUC7gC_UFwA",
                cover: ""
            },
            { 
                name: "YOU & I", 
                artist: "IU", 
                videoId: "seFr4qWykgY",
                cover: "" 
            },
            { 
                name: "Good day (좋은 날)", 
                artist: "IU", 
                videoId: "OIHlzvEKncQ",
                cover: "" 
            }
        ];

        let player;
        let currentSong = 0;
        let playing = false;
        let progressInterval;

        const artistName = document.querySelector('.artist-name');
        const musicName = document.querySelector('.song-name');
        const fillBar = document.querySelector('.fill-bar');
        const startTime = document.querySelector('.start-time');
        const endTime = document.querySelector('.end-time');
        const prog = document.querySelector('.progress-bar');
        const cover = document.getElementById('cover');
        const playBtn = document.getElementById('play');
        const prevBtn = document.getElementById('prev');
        const nextBtn = document.getElementById('next'); 

        const playIcon = "https://img.icons8.com/ios-glyphs/90/play--v1.png";
        const pauseIcon = "https://img.icons8.com/ios-filled/50/pause--v1.png";

        function onYouTubeIframeAPIReady() {
            player = new YT.Player('yt-player', {
                height: '0',
                width: '0',
                videoId: songsList[currentSong].videoId,
                playerVars: {
                    'autoplay': 0,
                    'controls': 0,
                    'rel': 0
                },
                events: {
                    'onReady': onPlayerReady,
                    'onStateChange': onPlayerStateChange
                }
            });
        }

        function onPlayerReady(event) {
            loadSongInfo(currentSong);
        }

        function onPlayerStateChange(event) {
            if (event.data === YT.PlayerState.ENDED) {
                nextSong();
            } else if (event.data === YT.PlayerState.PLAYING) {
                playing = true;
                updatePlayButton();
                cover.classList.add('active');
                startProgressInterval();
            } else if (event.data === YT.PlayerState.PAUSED) {
                playing = false;
                updatePlayButton();
                cover.classList.remove('active');
                stopProgressInterval();
            }
        }

        function loadSongInfo(index) {
            const song = songsList[index];
            artistName.innerText = song.artist;
            musicName.innerText = song.name;
            
            let imageUrl = song.cover;
            if (!imageUrl) {
                imageUrl = `https://img.youtube.com/vi/${song.videoId}/hqdefault.jpg`;
            }
            
            cover.style.backgroundImage = `url(${imageUrl})`;
        }

        function togglePlayPause() {
            if (!player) return;
            const state = player.getPlayerState();
            if (state === YT.PlayerState.PLAYING) {
                player.pauseVideo();
            } else {
                if (player.getVideoData && player.getVideoData().video_id !== songsList[currentSong].videoId) {
                    player.loadVideoById(songsList[currentSong].videoId);
                } else {
                    player.playVideo();
                }
            }
        }

        function nextSong() {
            currentSong = (currentSong + 1) % songsList.length;
            loadSongInfo(currentSong);
            player.loadVideoById(songsList[currentSong].videoId);
            player.playVideo();
        }

        function prevSong() {
            currentSong = (currentSong - 1 + songsList.length) % songsList.length;
            loadSongInfo(currentSong);
            player.loadVideoById(songsList[currentSong].videoId);
            player.playVideo();
        }

        function updatePlayButton() {
            playBtn.src = playing ? pauseIcon : playIcon;
            playBtn.alt = playing ? "pause" : "play";
        }

        function startProgressInterval() {
            stopProgressInterval();
            progressInterval = setInterval(() => {
                if (player && player.getCurrentTime && player.getDuration) {
                    const current = player.getCurrentTime();
                    const duration = player.getDuration();
                    if (duration) {
                        const pos = (current / duration) * 100;
                        fillBar.style.width = `${pos}%`;
                        startTime.innerText = formatTime(current);
                        endTime.innerText = formatTime(duration);
                    }
                }
            }, 500);
        }

        function stopProgressInterval() {
            clearInterval(progressInterval);
        }

        function formatTime(seconds) {
            const minutes = Math.floor(seconds / 60);
            const secs = Math.floor(seconds % 60);
            return `${minutes}:${secs < 10 ? '0' : ''}${secs}`;
        }

        function seek(e) {
            if (!player || !player.getDuration) return;
            const pos = (e.offsetX / prog.clientWidth) * player.getDuration();
            player.seekTo(pos, true);
        }

        document.addEventListener('DOMContentLoaded', () => {
            prevBtn.addEventListener('click', prevSong);
            nextBtn.addEventListener('click', nextSong);
            playBtn.addEventListener('click', togglePlayPause);
            prog.addEventListener('click', seek);
        });
    </script>
</body>
</html>****
