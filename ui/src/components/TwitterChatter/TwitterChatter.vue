<template>
  <div class="wrapper wrapper--address">
    <div class="tweets">
      <cv-combo-box
        ref="r_combo"
        v-model.trim="screenname"
        :label="$t('twitterChatterLabel')"
        :auto-filter="autoFilter"
        filterable="false"
        :options="options"
        @filter="actionFilter"
      >
      </cv-combo-box>

      <div class="wrapper wrapper--button">
        <cv-button @click="checkChatter">{{ $t('twitterChatterCheckButton') }}</cv-button>
        <template v-if="haveTweets">
          <div class="tweet-legend-sentiment tweet-legend-sentiment--positive"></div>
          <p>positive</p>

          <div class="tweet-legend-sentiment tweet-legend-sentiment--neutral"></div>
          <p>neutral</p>

          <div class="tweet-legend-sentiment tweet-legend-sentiment--negative"></div>
          <p>negative</p>
        </template>
      </div>

      <cv-loading
        v-if="loadingChatter"
        :active="loadingChatter"
        :overlay="loadingOverlay"
      ></cv-loading>
      <cv-list class="tweet__list" v-if="haveTweets">
        <cv-list-item
          v-for="item in twitter_chatter.items"
          :key="item.tweet"
          :class="'tweet-border-' + item.sentiment"
        >
          <span class="tweet-sentiment"> {{ item.sentiment }} </span>
          <p>{{ item.tweet }}</p>
        </cv-list-item>
      </cv-list>
      <p v-else-if="tweetError">No tweets available</p>
      <p v-else>{{ $t('twitterChatterSectionTitle') }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'twitterchatter',
  data() {
    return {
      twitter_chatter: {},
      loadingChatter: false,
      loadingOverlay: false,
      haveTweets: false,
      tweetError: false,
      autoFilter: true,
      screenname: '',
      options: [
        {
          value: 'realDonaldTrump',
          label: 'Donald Trump',
          name: 'realDonaldTrump'
        },
        {
          value: 'JoeBiden',
          label: 'Joe Biden',
          name: 'JoeBiden'
        },
        {
          value: 'VP',
          label: 'Mike Pence',
          name: 'VP'
        },
        {
          value: 'KamalaHarris',
          label: 'Kamala Harris',
          name: 'KamalaHarris'
        },
        {
          value: 'BarackObama',
          label: 'Barack Obama',
          name: 'BarackObama'
        },
        {
          value: 'TheBushCenter',
          label: 'George W. Bush',
          name: 'GeorgeWBush'
        },
        {
          value: 'BillClinton',
          label: 'Bill Clinton',
          name: 'BillClinton'
        },
        {
          value: 'Bush41Library',
          label: 'George Bush',
          name: 'Bush41Library'
        },
        {
          value: 'RonaldReagan',
          label: 'Ronald Reagan',
          name: 'RonaldReagan'
        },
        {
          value: 'Someone',
          label: 'Someone',
          name: 'Someone'
        }
      ]
    };
  },
  mounted() {},
  methods: {
    checkChatter: function() {
      eval('console.log(this.$refs.r_combo)');
      this.twitter_chatter = { items: [] };
      this.loadingChatter = true;
      axios
        .get('/twitter/chatter', {
          baseURL: process.env.VUE_APP_SERVICE_API_HOST,
          params: {
            screenname: this.screenname
          }
        })
        .then(response => {
          this.loadingChatter = false;
          this.twitter_chatter = response.data;
          this.haveTweets = this.twitter_chatter.items.length > 0;
        })
        .catch(error => {
          error;
          this.loadingChatter = false;
          this.tweetError = true;
          this.haveTweets = false;
        });
    },
    actionFilter: function(evt) {
      this.options.pop();
      if (evt.length === 0)
        this.options.push({
          name: 'Someone',
          value: 'Someone',
          label: 'Choose another name'
        });
      else this.options.push({ name: evt, value: evt, label: evt });
      this.screenname = evt;
    }
  }
};
</script>

<style lang="scss">
@import './twitterchatter.scss';
</style>
