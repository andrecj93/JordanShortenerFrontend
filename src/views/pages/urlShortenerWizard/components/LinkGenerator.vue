<template>
  <div
    class="pt-3 bg-white multisteps-form__panel js-active position-relative"
    data-animation="FadeIn"
  >
    <div class="text-center row">
      <div class="mx-auto col-10">
        <h5 class="font-weight-normal">
          Let's start with the url you pretend to short
        </h5>
        <!-- <p>
          Let us know your name and email address. Use an address you don't mind
          other users contacting you at
        </p> -->
      </div>
    </div>
    <div class="multisteps-form__content">
      <div class="mt-3 row">
        <!-- <div class="col-12 col-sm-4">
          <div class="avatar avatar-xxl position-relative">
            <img
              src="@/assets/img/team-2.jpg"
              class="border-radius-md"
              alt="team-2"
            />
            <a
              href="javascript:;"
              class="btn btn-sm btn-icon-only bg-gradient-success position-absolute bottom-0 end-0 mb-n2 me-n2 d-grid"
            >
              <span
                class="material-icons text-xs top-0"
                data-bs-toggle="tooltip"
                data-bs-placement="top"
                title
                aria-hidden="true"
                data-bs-original-title="Edit Image"
                aria-label="Edit Image"
                >edit</span
              >
            </a>
          </div>
        </div> -->
        <div class="mt-4 col-12 col-sm-12 mt-sm-0 text-start">
          <div class="d-block mb-4">
            <material-input
              id="longLink"
              name="longLink"
              label="Enter the link you want to short"
              :is-required="true"
              :error="!isValidLongLink"
              :value="longLink"
              @input="longLink = $event.target.value"
            />
          </div>
          <!-- <div class="d-block mb-4">
            <material-input id="lastName" variant="dynamic" label="Last Name" />
          </div>
          <div class="d-block mb-4">
            <material-input
              id="email"
              type="email"
              variant="dynamic"
              label="Email Address"
            />
          </div> -->
        </div>
      </div>
      <div class="mt-4 button-row d-flex">
        <button
          class="mb-0 btn bg-gradient-dark ms-auto js-btn-next"
          type="button"
          title="Generate"
          @click="tryGenerate"
        >
          Generate
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import setTooltip from '@/assets/js/tooltip.js';
import MaterialInput from '@/components/MaterialInput.vue';

export default {
  name: 'LinkGenerator',
  components: { MaterialInput },
  emits: ['generate'],
  data() {
    return {
      longLink: '',
      isValidLongLink: false,
      regexUrl: new RegExp(
        '^(https?:\\/\\/)?' + // protocol
          '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|' + // domain name
          '((\\d{1,3}\\.){3}\\d{1,3}))' + // OR ip (v4) address
          '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*' + // port and path
          '(\\?[;&a-z\\d%_.~+=-]*)?' + // query string
          '(\\#[-a-z\\d_]*)?$',
        'i'
      ),
    };
  },
  watch: {
    longLink(newValue) {
      this.isValidLongLink = newValue && this.validateUrl(newValue);
    },
  },
  mounted() {
    setTooltip(this.$store.state.bootstrap);
  },
  methods: {
    tryGenerate() {
      if (!this.longLink) return;

      this.isValidLongLink = this.validateUrl(this.longLink);
      if (!this.isValidLongLink) return;

      // long link must be valid with start of http or https
      // if (!this.longLink.startsWith('http')) {
      //   this.longLink = 'https://' + this.longLink;
      // }

      this.$emit('generate', this.longLink);
    },
    validateUrl(url) {
      // validate url with regex
      const test = !!this.regexUrl.test(url);
      console.log(test);
      return test;
    },
  },
};
</script>
