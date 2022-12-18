<template>
  <div
    class="pt-3 bg-white multisteps-form__panel border-radius-xl"
    data-animation="FadeIn"
  >
    <div class="text-center row">
      <div class="mx-auto col-10">
        <h5 class="font-weight-normal">
          We are generating your link, please wait...
        </h5>
        <p>
          Your link hostname is from:
          {{ linkAsUrlInterface?.hostname ?? 'Unknown' }}
        </p>
      </div>
    </div>
    <div class="multisteps-form__content">
      <div class="mt-4 row">
        <material-progress
          class="col-12"
          color="success"
          variant="gradient"
          :percentage="percentage"
        />
        <material-progress
          class="col-12"
          color="success"
          variant="gradient"
          :percentage="percentage"
        />
      </div>
    </div>
  </div>
</template>

<script>
import MaterialProgress from '@/components/MaterialProgress.vue';

export default {
  name: 'GeneratingSequence',
  components: { MaterialProgress },
  props: {
    linkToGenerate: {
      type: String,
      required: true,
    },
  },
  emits: ['generated', 'generated-fail'],
  data() {
    return {
      percentage: 10,
      apiUrl: 'https://urlshorta.azurewebsites.net/api',
      apiUserData: {
        username: 'admin',
        password: 'YvuQ7)\\ULBgn927x',
        grantType: 'password',
      },
      linkAsUrlInterface: undefined,
    };
  },
  async mounted() {
    if (!this.linkToGenerate) {
      this.$parent.prevStep();
    }

    try {
      this.linkAsUrlInterface = new URL(this.linkToGenerate);
    } catch (error) {
      this.$emit('generated-fail', error);
      return;
    }

    await this.generateSequence().catch((error) => {
      const errorMessage = error?.response?.data?.Message ?? error.message ?? error;
      this.$emit('generated-fail', errorMessage);
    });
  },
  methods: {
    async generateSequence() {
      this.setPercentage(20);

      const params = new URLSearchParams();
      params.append('username', this.apiUserData.username);
      params.append('password', this.apiUserData.password);
      params.append('grant_type', this.apiUserData.grantType);

      const tokenResponse = await this.axios.post(
        `${this.apiUrl}/token`,
        params,
        {
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
          },
        }
      );

      this.setPercentage(40);

      const responseShortLink = await this.axios.post(
        `${this.apiUrl}/shortLink`,
        {
          fullLink: this.linkToGenerate,
        },
        {
          headers: {
            Authorization: `Bearer ${tokenResponse.data.access_token}`,
          },
        }
      );

      this.setPercentage(70);

      const { data } = responseShortLink;
      const { ShortLink, Code, Message } = data;

      this.setPercentage(90);

      if (Message) {
        this.$emit('generated-fail', Message);
        return;
      }
      else if (ShortLink && Code) {
        setTimeout(() => {
          this.percentage = 100;

          this.$emit('generated', ShortLink, Code);
        }, 2000);
      } else {
        this.$emit('generated-fail', "Unknown error ocurred. Possible exception on the API with no message.");
      }

      console.log(data);
    },
    setPercentage(base) {
      this.percentage = base + Math.floor(Math.random() * 10);
    },
  },
};
</script>
