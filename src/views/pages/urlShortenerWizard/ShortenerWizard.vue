<template>
  <div class="py-4 h-100 container-fluid">
    <div class="row">
      <div class="text-center col-12">
        <h3 class="mt-5">Build Your Link</h3>
        <h5 class="text-secondary font-weight-normal">
          This information will let us generate your short link.
        </h5>
        <div class="mb-5 multisteps-form">
          <!--progress bar-->
          <div class="row">
            <div class="mx-auto my-5 col-12 col-lg-8">
              <div class="card">
                <div
                  class="card-header p-0 position-relative mt-n4 mx-3 z-index-2"
                >
                  <div
                    class="bg-gradient-success shadow-success border-radius-lg pt-4 pb-3"
                  >
                    <div class="multisteps-form__progress">
                      <div
                        class="multisteps-form__progress-btn js-active noCursor"
                        title="Long Link Info"
                        :class="activeStep >= 0 ? activeClass : ''"
                      >
                        <span>Link Info</span>
                      </div>
                      <div
                        class="multisteps-form__progress-btn noCursor"
                        title="Validation"
                        :class="activeStep >= 1 ? activeClass : ''"
                      >
                        <span>Validation</span>
                      </div>
                      <div
                        class="multisteps-form__progress-btn noCursor"
                        title="Shortened"
                        :class="activeStep >= 2 ? activeClass : ''"
                      >
                        <span>Shortened</span>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="card-body">
                  <div class="multisteps-form__form">
                    <!--single form panel-->
                    <link-generator
                      v-if="activeStep === 0"
                      @generate="handleUrlToShorten"
                    />
                    <!--single form panel-->
                    <generating-sequence
                      v-if="activeStep === 1"
                      :class="activeStep === 1 ? activeClass : ''"
                      :link-to-generate="linkToGenerate"
                      @generated="handleGeneratedLink"
                      @generated-fail="handleGeneratedLinkFail"
                    />
                    <!--single form panel-->
                    <final-result
                      v-if="activeStep === 2"
                      :class="activeStep === 2 ? activeClass : ''"
                      :generated-code="generatedCode"
                      :generated-link="generatedLink"
                      :origin-link="linkToGenerate"
                    />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="position-fixed top-20 z-index-2">
      <material-snackbar
        v-if="hasFailedGenerating"
        title="URL Hub"
        date="Now"
        :description="errorGeneratingMessage"
        :icon="{ component: 'campaign', color: 'white' }"
        color="danger"
        :close-handler="closeSnackbar"
      />
    </div>
    </div>
  </div>
</template>

<script>
import LinkGenerator from './components/LinkGenerator.vue';
import GeneratingSequence from './components/GeneratingSequence.vue';
import FinalResult from './components/FinalResult.vue';
import MaterialSnackbar from '@/components/MaterialSnackbar.vue';

export default {
  name: 'ShortenerWizard',
  components: {
    LinkGenerator,
    GeneratingSequence,
    FinalResult,
    MaterialSnackbar,
  },
  data() {
    return {
      activeClass: 'js-active position-relative',
      activeStep: 0,
      formSteps: 2,
      linkToGenerate: '',
      generatedLink: '',
      generatedCode: '',
      hasFailedGenerating: false,
      errorGeneratingMessage: '',
    };
  },
  methods: {
    nextStep() {
      if (this.activeStep < this.formSteps) {
        this.activeStep += 1;
      } else {
        this.activeStep -= 1;
      }
    },
    prevStep() {
      if (this.activeStep > 0) {
        this.activeStep -= 1;
      }
    },
    handleUrlToShorten(longLink) {
      this.linkToGenerate = longLink;
      this.nextStep();
    },
    handleGeneratedLink(shortLink, shortCode) {
      this.generatedLink = shortLink;
      this.generatedCode = shortCode;
      this.nextStep();
    },
    handleGeneratedLinkFail(message) {
      // show message with fail reason
      this.hasFailedGenerating = true;
      this.errorGeneratingMessage = message;
      this.prevStep();

      setTimeout(() => {
        this.closeSnackbar();
      }, 5000);
    },
    closeSnackbar() {
      this.hasFailedGenerating = false;
      this.errorGeneratingMessage = '';
    },
  },
};
</script>

<style scoped>
.noCursor {
  cursor: default !important;
}
</style>
