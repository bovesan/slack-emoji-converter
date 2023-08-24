<template>
  <v-container fluid class="fill-height flex-column align-stretch flex-nowrap">
    <h1 class="text-h6 flex-grow-0">Slack format with colons</h1>
    <v-textarea
      placeholder="Paste text from Slack here."
      v-model="slack"
      variant="outlined"
      hide-details
      @click="selectAll"
    />
    <template v-if="!slack || success">
      <h1 class="text-h6 flex-grow-0">Unicode format with emojis</h1>
      <v-textarea
        placeholder="The converted text will show here."
        readonly
        variant="outlined"
        hide-details
        :value="unicode"
      />
      <v-spacer />
    </template>
    <div class="flex-grow-0">
      <v-alert
        type="error"
        v-show="noSlackEmojiFound"
        class="mt-4"
      >No Slack emojis where found in the text.</v-alert>
      <v-alert
        type="error"
        v-show="noUnicodeEmojiFound"
        class="mt-4"
      >None of the Slack emojis could be converted to Unicode.</v-alert>
      <v-alert
        type="warning"
        v-show="someUnicodeEmojiNotFound"
        class="mt-4"
      >Some of the Slack emojis could be converted to Unicode.</v-alert>
      <v-alert
        type="success"
        v-show="success"
        class="mt-4"
      >The converted text was copied back to the clipboard.</v-alert>
    </div>
  </v-container>
</template>

<script lang="ts">
import emoji from '../assets/emoji_pretty.json';
const lookup: {
  [key: string]: string;
} = {};
for (const entry of emoji){
  lookup[entry.short_name] = entry.unified;
  // console.log(entry.short_name, entry.unified);
}
export default {
  data() {
    return {
      slack: '',
      unicode: '',
      noSlackEmojiFound: false,
      noUnicodeEmojiFound: false,
      someUnicodeEmojiNotFound: false,
      success: false,
    }
  },
  watch: {
    slack(newValue: string, oldValue: string) {
      this.noSlackEmojiFound = false;
      this.noUnicodeEmojiFound = false;
      this.someUnicodeEmojiNotFound = false;
      this.success = false;
      let foundSlackEmoji = false;
      let replacedEmoji = false;
      let someUnicodeEmojiNotFound = false;
      this.unicode = newValue.replace(/\:([a-z0-9\_]+)\:/g, (match, token) => {
        foundSlackEmoji = true;
        if (lookup[token]){
          replacedEmoji = true;
          const unicode = String.fromCodePoint(parseInt(lookup[token], 16));
          // console.log(unicode);
          return unicode;
        } else {
          someUnicodeEmojiNotFound = true;
        }
        return match;
      });
      if (newValue == ''){
        return;
      }
      if (!foundSlackEmoji){
        setTimeout(()=>{
          this.noSlackEmojiFound = true;
        }, 100);
        return;
      }
      if (!replacedEmoji){
          this.noUnicodeEmojiFound = true;
        return;
      }
      navigator.clipboard.writeText(this.unicode);
      setTimeout(()=>{
        this.someUnicodeEmojiNotFound = someUnicodeEmojiNotFound;
        this.success = true;
      }, 100);
    }
  },
  methods: {
    selectAll(event){
      event.target.select();
    }
  }
}
</script>