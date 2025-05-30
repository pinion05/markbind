<template>
  <div>
    Loading...
  </div>
</template>

<script>
// eslint-disable-next-line import/no-extraneous-dependencies
import { createApp } from 'vue';
import { getFragmentByHash, toBoolean } from './utils/utils';

export default {
  props: {
    src: {
      type: String,
      default: null,
    },
    fragment: {
      type: String, // fragment identified (the '#' in URI)
      default: null,
    },
    delay: {
      type: [Boolean, String],
      default: false,
    },
    hasFetched: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['src-loaded'],
  data() {
    return {
      /*
       * hasFetched is passed down as a prop. In order to not mutate props (causes Vue warning),
       * we create a copy of hasFetched in the local data to update the value.
       */
      hasFetchedCopy: this.hasFetched,
    };
  },
  computed: {
    // Vue 2.0 coerce migration
    delayBool() {
      return toBoolean(this.delay);
    },
    // Vue 2.0 coerce migration end
    hash() {
      return getFragmentByHash(this.src) || this.fragment;
    },
    srcWithoutHash() {
      return this.src.split('#')[0];
    },
  },
  methods: {
    fetch() {
      if (!this.srcWithoutHash) {
        return;
      }
      if (this.hasFetchedCopy) {
        return;
      }
      fetch(this.srcWithoutHash)
        .then(response => response.text())
        .then((htmlText) => {
          let result = htmlText;
          if (this.hash) {
            const htmlResult = document.implementation.createHTMLDocument('');
            htmlResult.body.innerHTML = result;

            // Script tags injected by live server in SVG tags are removed
            // to prevent Vue warnings about side effects in Vue template
            // Ok to remove because Vue will not process the script tags to avoid side effects
            const allScriptChildren = htmlResult.querySelectorAll('svg > script');
            allScriptChildren.forEach(child => child.remove());

            const appContainer = htmlResult.querySelector(`#${this.hash}`);
            result = appContainer.innerHTML;
          }
          this.hasFetchedCopy = true;
          // result is empty / undefined
          if (result === undefined && this.hash) {
            this.$el.innerHTML = '<strong>Error</strong>: Failed to retrieve page fragment:'
                + ` ${this.srcWithoutHash}#${this.hash}`;
            return;
          }

          const rootData = {
            /*
             * Vue wraps $data as an observer object, we have to "unwrap" it and assign to a
             * variable first before we pass the $data object into the new Vue instance below.
             */
            ...this.$root.$data,
          };

          // Mount result in retriever
          const TempComponent = {
            template: `<div>\n${result}\n</div>`,
            data() {
              return rootData;
            },
          };
          const app = createApp(TempComponent);
          app.use(window.MarkBindVuePlugin);
          app.mount(this.$el);
          this.$emit('src-loaded');
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.error(error);
          this.$el.innerHTML = '<strong>Error</strong>: Failed to retrieve content from source: '
              + `<em>${this.srcWithoutHash}</em>`;
          this.$emit('src-loaded');
        });
    },
  },
  mounted() {
    this.$nextTick(function () {
      if (!this.srcWithoutHash) {
        this.$el.innerHTML = '';
      }

      if (!this.delayBool) {
        this.fetch();
      }
    });
  },
};
</script>
