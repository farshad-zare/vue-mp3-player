<template>
  <div class="player">
    <h3 class="player_title">player</h3>
    <div class="play-pause">
      <button v-if="isPlaying" class="play-pause_button" @click="pausePlayer">
        <img class="play-pause_img" src="../assets/pause-circle.svg" />
      </button>

      <button v-else class="play-pause_button" @click="startPlayer">
        <img class="play-pause_img" src="../assets/play-circle.svg" />
      </button>
    </div>
    <div class="player-info">
      <div class="player-info_time">
        <span class="time-passed">{{ playedTimedString }}</span>
        <span class="time-sepretor">/</span>
        <span class="time-left">{{ songDurationString }}</span>
      </div>
      <div class="player-seekbar" @click="handleSeek">
        <div class="seekbar-left"></div>
        <div
          class="seekbar-passed"
          :style="`left:${-this.playedPercent}%`"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
  import { Howl } from "howler";

  export default {
    name: "player",
    data() {
      return {
        isPlaying: false,
        mp3Url:
          "http://dl.takmusics.ir/Song/Donya%20%7C%20Koo%20Ta%20Biad%20(128).mp3",
        sound: null,
        songDuration: null,
        playedTime: null,
        songDurationString: "--",
        playedTimedString: "--",
        playedPercent: 100,
        getPlayedTimeinterval: null,
      };
    },

    methods: {
      convertSecsToMinsSecs(duration) {
        const mins = parseInt(duration / 60);
        const secs = Math.floor(duration % 60);
        return `${mins}:${secs}`;
      },

      getPlayedTime() {
        this.playedTime = this.sound.seek();

        this.playedTimedString = this.convertSecsToMinsSecs(this.playedTime);
        this.playedPercent = 100 - (this.playedTime / this.songDuration) * 100;
      },

      handleSeek(event) {
        const elementWidth = event.target.offsetWidth;
        const clickedPos = event.offsetX;
        const percentToSeek = (clickedPos / elementWidth) * 100;
        const seekTime = (this.songDuration * percentToSeek) / 100;
        this.sound.seek(seekTime);
        this.getPlayedTime();
      },

      startPlayer() {
        this.isPlaying = true;

        if (!this.sound) {
          this.sound = new Howl({
            src: [this.mp3Url],
            html5: true,
            volume: 0.5,
            preload: "metadata",
          });

          this.sound.on("load", () => {
            this.songDuration = this.sound.duration();

            this.songDurationString = this.convertSecsToMinsSecs(
              this.songDuration
            );
          });

          this.sound.on("play", () => {
            this.getPlayedTimeinterval = setInterval(this.getPlayedTime, 500);
          });

          this.sound.on("pause", () => {
            clearInterval(this.getPlayedTimeinterval);
          });

          this.sound.on("end", () => {
            clearInterval(this.getPlayedTimeinterval);
            this.isPlaying = false;
          });
        }

        this.sound.play();
      },

      pausePlayer() {
        this.sound.pause();
        this.isPlaying = false;
      },
    },
  };
</script>

<style scoped>
  .player {
    background-color: rgba(240, 248, 255, 0.507);

    padding: 1rem;
    min-width: 300px;

    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    box-shadow: 0 0 20px black;
    border-radius: 5px;
  }
  .play-pause_img {
    aspect-ratio: 1;
    width: 9rem;
  }
  .play-pause_button {
    border: none;
    background-color: transparent;
  }
  .play-pause_button:hover {
    cursor: pointer;
  }
  .player-info {
    margin-top: 2rem;
    margin-bottom: 1rem;
    width: 100%;
  }
  .player-info_time {
    margin-bottom: 1rem;

    display: flex;
    align-items: center;
    justify-content: center;
  }
  .player-seekbar {
    position: relative;
    overflow: hidden;

    width: 100%;
    height: 10px;
    border-radius: 5px;

    cursor: pointer;
  }
  .seekbar-left {
    position: absolute;
    width: 100%;
    height: 10px;

    background-color: rgb(167, 245, 180);

    border-radius: 5px;

    pointer-events: none;
  }
  .seekbar-passed {
    position: absolute;
    width: 100%;
    height: 10px;

    background-color: rgb(4, 153, 49);
    border-radius: 5px;

    pointer-events: none;
  }
</style>
