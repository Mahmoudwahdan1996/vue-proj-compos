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
      <coach-filters @change-filter="setFilters"></coach-filters>
    </section>
    <section>
      <base-card>
        <div class="controls">
          <base-button mode="outline" @click="loadCoaches(true)">
            Refresh</base-button
          >
          <base-button link to="/auth?redirect=register" v-if="!isLoggedIn"
            >Login to Register As Coach</base-button
          >
          <base-button
            v-if="isLoggedIn && !isLoading && !isCoach"
            link
            to="/register"
            >Register as a coach</base-button
          >
        </div>
        <div v-if="isLoading">
          <base-spinner></base-spinner>
        </div>
        <ul v-else-if="hasNoCoaches">
          <coach-item
            v-for="coach in filteredCoaches"
            :key="coach.id"
            :id="coach.id"
            :firstName="coach.firstName"
            :lastName="coach.lastName"
            :rate="coach.hourlyRate"
            :areas="coach.areas"
          ></coach-item>
        </ul>
        <h3 v-else>Has No Coaches found..</h3>
      </base-card>
    </section>
  </div>
</template>

<script>
import CoachItem from '../../components/coaches/CoachItem.vue';
import CoachFilters from '../../components/coaches/CoachFilters.vue';
import { useStore } from 'vuex';
import { computed, ref } from 'vue';
export default {
  components: {
    CoachItem,
    CoachFilters,
  },
  setup() {
    const store = useStore();
    const isLoading = ref(false);
    const error = ref(null);

    const filteredCoaches = computed(() => {
      const coaches = store.getters['coaches/coaches'];
      return coaches.filter((coach) => {
        if (activeFilters.value.frontend && coach.areas.includes('frontend')) {
          return true;
        }
        if (activeFilters.value.backend && coach.areas.includes('backend')) {
          return true;
        }
        if (activeFilters.value.career && coach.areas.includes('career')) {
          return true;
        }

        return false;
      });
    });
    const isCoach = computed(() => {
      return store.getters['coaches/isCoach'];
    });

    const isLoggedIn = computed(() => {
      return store.getters.isAuthenticated;
    });
    const activeFilters = ref({
      frontend: true,
      backend: true,
      career: true,
    });

    const hasNoCoaches = computed(() => {
      return !isLoading.value && store.getters['coaches/hasNoCoaches'];
    });

    const setFilters = (updatedFilters) => {
      activeFilters.value = updatedFilters;
    };

    function handleError() {
      error.value = null;
    }

    async function loadCoaches(refresh = false) {
      isLoading.value = true;

      try {
        await store.dispatch('coaches/loadCoaches', {
          forceRefersh: refresh,
        });
      } catch (error) {
        error.value = error.message || 'Something Went Wrong';
      }

      isLoading.value = false;
    }

    loadCoaches();

    return {
      filteredCoaches,
      setFilters,
      loadCoaches,
      handleError,
      hasNoCoaches,
      isLoggedIn,
      isLoading,
      error,
      isCoach,
    };
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

.controls {
  display: flex;
  justify-content: space-between;
}
</style>
