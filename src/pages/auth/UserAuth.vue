<template>
  <div>
    <base-dialog
      :show="!!error"
      title="An error occurred..."
      @close="closeHandler"
    >
      <p>{{ error }}</p>
    </base-dialog>
    <base-dialog fixed :show="isLoading" title="Authenticating...">
      <base-spinner></base-spinner>
    </base-dialog>
    <base-card>
      <form @submit.prevent="SubmitForm">
        <div class="form-control">
          <label for="email">E-Mail</label>
          <input type="email" id="email" v-model.trim="email" />
        </div>
        <div class="form-control">
          <label for="password">Password</label>
          <input type="password" id="password" v-model.trim="password" />
        </div>
        <p v-if="!isValidForm">
          please enter a valid email and password (must be at least 6 character
          length)
        </p>
        <base-button>{{ submitButtonCaption }}</base-button>
        <base-button type="button" mode="flat" @click="switchAuthMode"
          >{{ switchModeButtonCaption }}
        </base-button>
      </form>
    </base-card>
  </div>
</template>

<script>
import { computed, ref } from 'vue';
import { useRouter } from 'vue-router';
import { useStore } from 'vuex';

export default {
  setup() {
    const email = ref('');
    const password = ref('');
    const isValidForm = ref(true);
    const mode = ref('login');
    const isLoading = ref(false);
    const error = ref(null);

    const store = useStore();
    const router = useRouter();

    const submitButtonCaption = computed(() => {
      if (mode.value === 'login') {
        return 'Login';
      } else {
        return 'Signup';
      }
    });

    const switchModeButtonCaption = computed(() => {
      if (mode.value === 'login') {
        return 'Signup instead';
      } else {
        return 'Login instead';
      }
    });
    async function SubmitForm() {
      isValidForm.value = true;
      if (!email.value.includes('@') && password.value.length < 6) {
        isValidForm.value = false;
        return;
      }

      isLoading.value = true;

      const actionPayload = {
        email: email.value,
        password: password.value,
      };

      try {
        if (mode.value === 'login') {
          await store.dispatch('login', actionPayload);
        } else {
          await store.dispatch('signup', actionPayload);
        }

        // const redirectURL = '/' + (this.$route.query.redirect || 'coaches');
        // this.$router.replace(redirectURL);
        router.replace('/coaches');
      } catch (error) {
        error.value = error.message || 'failed to authenticated , try later .';
      }
      isLoading.value = false;
    }

    function closeHandler() {
      error.value = null;
    }

    function switchAuthMode() {
      if (mode.value === 'login') {
        mode.value = 'signup';
      } else {
        mode.value = 'login';
      }
    }
    return {
      email,
      password,
      isValidForm,
      error,
      isLoading,
      submitButtonCaption,
      switchModeButtonCaption,
      SubmitForm,
      closeHandler,
      switchAuthMode,
    };
  },
};
</script>

<style scoped>
form {
  margin: 1rem;
  padding: 1rem;
}

.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  margin-bottom: 0.5rem;
  display: block;
}

input,
textarea {
  display: block;
  width: 100%;
  font: inherit;
  border: 1px solid #ccc;
  padding: 0.15rem;
}

input:focus,
textarea:focus {
  border-color: #3d008d;
  background-color: #faf6ff;
  outline: none;
}
</style>
