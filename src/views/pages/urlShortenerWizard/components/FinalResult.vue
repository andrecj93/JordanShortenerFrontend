<template>
  <div
    class="pt-3 bg-white multisteps-form__panel border-radius-xl"
    data-animation="FadeIn"
  >
    <div class="text-center row">
      <div class="mx-auto col-10">
        <material-alert class="font-weight-light" dismissible>
          <span class="text-sm"> {{ statusText }} </span>
        </material-alert>
      </div>
    </div>
    <div class="multisteps-form__content">
      <div class="row text-start">
        <div class="mt-3 col-12 col-md-8 ms-auto">
          <div>
            <material-input
              id="shortLink"
              variant="static"
              label="Shortened link to share"
              :readonly="true"
              :value="generatedLink"
            />
          </div>
        </div>
        <div class="mt-3 col-12 col-md-4 ms-auto">
          <material-input
            id="shortLinkCode"
            variant="static"
            label="Code"
            :value="generatedCode"
            :readonly="true"
          />
        </div>
        <div class="mt-3 col-12 col-md-12 ms-auto">
          <material-input
            id="originLink"
            variant="static"
            label="Link that will redirect the users"
            :value="originLink"
            :readonly="true"
            :disabled="true"
          />
        </div>
      </div>
      <div class="row">
        <div class="mt-4 button-row d-flex col-12">
          <button
            class="mb-0 btn bg-gradient-light js-btn-prev"
            type="button"
            title="Create new link"
            @click="this.$parent.activeStep = 0"
          >
            Create new link
          </button>
          <button
            class="mb-0 btn bg-gradient-dark ms-auto"
            type="button"
            title="Send"
            @click="goToShortenedLink"
          >
            Open shortened link
          </button>
          <button
            class="mb-0 btn bg-gradient-info ms-auto"
            type="button"
            title="Copy to clipboard"
            @click="copyToClipboard"
          >
            <i class="fa fa-copy"></i>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MaterialInput from '../../../../components/MaterialInput.vue';
import MaterialAlert from '../../../../components/MaterialAlert.vue';

export default {
  name: 'FinalResult',
  components: {
    MaterialInput,
    MaterialAlert,
  },
  props: {
    generatedLink: {
      type: String,
      required: true,
    },
    generatedCode: {
      type: String,
      required: true,
    },
    originLink: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      statusText: 'Generating link...',
    };
  },
  mounted() {
    this.statusText = 'Your link has been generated successfully!';
  },
  methods: {
    goToShortenedLink() {
      window.open(this.generatedLink, '_blank');
    },
    copyToClipboard() {
      navigator.clipboard.writeText(this.generatedLink);
      this.statusText = 'Copied to clipboard successfully!';
    },
  },
};
</script>
