<template>
  <v-container class="fill-height">
    <v-textarea
      placeholder="Slack format with colons"
      v-model="slack"
    />
    <div style="white-space: pre;" class="pl-4 pt-0">
      {{unicode}}
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
      unicode: ''
    }
  },
  watch: {
    slack(newValue: string, oldValue: string) {
      this.unicode = newValue.replace(/\:([a-z0-9\_]+)\:/g, (match, token) => {
        // console.log('match', match, 'token', token, lookup[token]);
        if (lookup[token]){
          const unicode = String.fromCodePoint(parseInt(lookup[token], 16));
          // console.log(unicode);
          return unicode;
        }
        return match;
      });
    }
  },
}
</script>