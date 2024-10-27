<template>
  <div :class="['app', darkMode ? 'dark' : 'light']">
    <!-- Centralized Header -->
    <div class="header">
      <h1>Word-by-Word Reader</h1>
      <label>
        <input type="checkbox" v-model="darkMode" /> Dark Mode
      </label>
    </div>

    <div class="content">
      <!-- Step 1: Insert text -->
      <div v-if="step === 1" class="step-container">
        <textarea v-model="inputText" placeholder="Enter your text here"></textarea>
        <button @click="nextStep">Next</button>
      </div>

      <!-- Step 2: Define WPM -->
      <div v-if="step === 2" class="step-container">
        <label for="wpm">Words per minute:</label>
        <input type="number" v-model.number="wpm" min="1" placeholder="Enter WPM" />
        <button @click="nextStep">Next</button>
        <button @click="previousStep">Back</button>
      </div>

      <!-- Step 3: Display words -->
      <div v-if="step === 3" class="step-container">
        <div class="display-word">
          <h2>{{ currentWord }}</h2>
        </div>

        <label>
          <input type="checkbox" v-model="isMuted" /> Mute beep sound
        </label>

        <button v-if="!isPlaying" @click="startReading">Play</button>
        <button v-if="isPlaying" @click="stopReading">Stop</button>
        <button @click="previousStep">Back</button>
      </div>
    </div>

    <div style="border: 1px solid #ccc; padding: 20px; margin: 20px 0; border-radius: 5px;">
      <p>
        Cookie and Privacy Policy: We use cookies and similar technologies to enhance your browsing experience. By
        continuing to use our site/services, you agree to our
        <a href="https://nrolabs.com/privacy" target="_blank" style="text-decoration: underline;">Privacy Policy</a>,
        <a href="https://nrolabs.com/cookies" target="_blank" style="text-decoration: underline;">Cookie Policy</a>,
        and <a href="https://nrolabs.com/terms" target="_blank" style="text-decoration: underline;">Terms of Use</a>.
      </p>
      <p>
        This site is owned by <strong>nrolabs.com</strong> and is subject to their terms.
      </p>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      step: 1,                           // Current step in the process: 1 for input, 2 for setting WPM, 3 for reading words
      inputText: "",                     // Text inputted by the user
      wpm: 200,                          // Words per minute speed for reading
      words: [],                         // Array of words extracted from inputText
      currentWord: "",                   // Word currently being displayed
      currentIndex: 0,                   // Index of the current word in the words array
      isPlaying: false,                  // Indicates if the reading process is ongoing
      timer: null,                       // Timer reference for controlling word display timing
      beepSound: new Audio('/beep.ogg'), // Sound effect played between words
      isMuted: false,                    // Controls if the beep sound is muted
      darkMode: true                    // Toggles dark mode for the application
    };
  },
  methods: {
    /**
     * Advances to the next step of the process. Validates input for each step.
     */
    nextStep() {
      if (this.step === 1 && this.inputText.trim()) {
        this.words = this.inputText.trim().split(/\s+/); // Splits text into words
        this.step++;
      } else if (this.step === 2 && this.wpm > 0) {
        this.step++;
        this.currentIndex = 0;
        this.currentWord = "";
      }
    },
    /**
     * Returns to the previous step in the process. Stops reading if ongoing.
     */
    previousStep() {
      if (this.step > 1) {
        this.step--;
        this.stopReading();
      }
    },
    /**
     * Starts the process of displaying words based on the defined WPM.
     */
    startReading() {
      const delay = (60 / this.wpm) * 1000;
      this.isPlaying = true;
      this.showNextWord();
      this.timer = setInterval(this.showNextWord, delay);
    },
    /**
     * Stops the word display process.
     */
    stopReading() {
      this.isPlaying = false;
      clearInterval(this.timer);
    },
    /**
     * Displays the next word in the words array and plays a beep sound if unmuted.
     */
    showNextWord() {
      if (this.currentIndex < this.words.length) {
        this.currentWord = this.words[this.currentIndex];
        this.currentIndex++;
        if (!this.isMuted) {
          this.playBeepSound();
        }
      } else {
        this.stopReading();
      }
    },
    /**
     * Plays a beep sound effect.
     */
    playBeepSound() {
      this.beepSound.currentTime = 0;
      this.beepSound.play();
    }
  }
};
</script>