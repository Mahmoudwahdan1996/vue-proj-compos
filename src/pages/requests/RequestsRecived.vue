<template>
  <div>
    <base-dialog
      :show="!!error"
      title="An error occurred !"
      @close="handleError"
    >
      <p>{{ error }}</p>
    </base-dialog>
    <section>
      <base-card>
        <header>
          <h2>Requests Recived</h2>
        </header>
        <base-spinner v-if="isLoading"></base-spinner>

        <ul v-else-if="hasRequests && !isLoading">
          <requests-item
            v-for="req in receivedRequests"
            :key="req.id"
            :email="req.userEmail"
            :message="req.message"
          ></requests-item>
        </ul>
        <h3 v-else>You haven't recived any requests yet .</h3>
      </base-card>
    </section>
  </div>
</template>

<script>
import { computed, ref } from 'vue';
import { useStore } from 'vuex';
import RequestsItem from '../../components/requests/RequestsItem.vue';
export default {
  components: {
    RequestsItem,
  },
  setup() {
    const store = useStore();
    const isLoading = ref(false);
    const error = ref(null);

    const receivedRequests = computed(() => {
      return store.getters['requests/requests'];
    });
    const hasRequests = computed(() => {
      return store.getters['requests/hasRequests'];
    });

    async function loadRequests() {
      isLoading.value = true;
      try {
        await store.dispatch('requests/fetchRequests');
      } catch (error) {
        error.value = error.message || 'Something Failed';
      }
      isLoading.value = false;
    }

    function handleError() {
      error.value = null;
    }

    loadRequests();

    return {
      hasRequests,
      isLoading,
      error,
      receivedRequests,
      handleError,
    };
  },
};
</script>
