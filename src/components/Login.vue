<template>
  <div class="hello">
    <v-form
      class="inputs"
      @submit.prevent.native="submit"
    >
      <v-text-field
        v-model="form.url"
        label="Name"
        dense
        outlined
      />
      <v-text-field
        v-model="form.username"
        label="Username"
        dense
        outlined
      />
      <v-text-field
        v-model="form.password"
        label="Password"
        :type="showPassword ? 'text' : 'password'"
        :append-icon="showPassword ? 'fa-eye' : 'fa-eye-slash'"
        outlined
        dense
        @click:append="showPassword = !showPassword"
      />
      <v-checkbox
        v-model="rememberMe"
        label="Remember URL and Username"
      />
      <v-btn
        color="primary"
        type="submit"
        :loading="loading"
        rounded
      >
        Submit
      </v-btn>
    </v-form>
  </div>
</template>

<script>
import { mapGetters, mapState } from 'vuex';
import electronStore from '@/store/electron-store';

export default {
  name: 'Login',
  data() {
    return {
      loading: false,
      showPassword: false,
      rememberMe: false,
      form: {
        url: '',
        username: '',
        password: '',
      },
    };
  },
  computed: {
    ...mapGetters({
      isLoggedIn: 'profile/isLoggedIn',
    }),
    ...mapState({
      loginError: state => state.profile.loginError,
    }),
  },
  watch: {
    loginError(val) {
      if (val.length > 0) {
        this.loading = false;
        this.$toast.error(`Error Logging In: ${val}`);
      }
    },
  },
  mounted() {
    this.form.url = electronStore.get('url', '');
    this.form.username = electronStore.get('username', '');
    this.rememberMe = electronStore.get('rememberMe', false);
  },
  methods: {
    submit() {
      this.loading = true;
      electronStore.set('rememberMe', this.rememberMe);

      if (this.rememberMe === true) {
        electronStore.set('url', this.form.url);
        electronStore.set('username', this.form.username);
      } else {
        electronStore.delete('url');
        electronStore.delete('username');
      }

      this.$store.dispatch('profile/login', this.form);
    },
  },
};
</script>

<style scoped lang="scss">
</style>
