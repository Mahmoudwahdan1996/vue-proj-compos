<template>
  <div>
    <section>
      <base-card>
        <h2>{{ fullName }}</h2>
        <h3>${{ rate }}/hour</h3>
      </base-card>
    </section>
    <section>
      <base-card>
        <header>
          <h2>Interested ? Reach out now .</h2>
          <base-button link :to="contactLink">Contact</base-button>
          <router-view></router-view>
        </header>
      </base-card>
    </section>
    <section>
      <base-card>
        <base-badge
          v-for="area in areas"
          :key="area"
          :title="area"
          :type="area"
        ></base-badge>
        <p>{{ description }}</p>
      </base-card>
    </section>
  </div>
</template>

<script>
import { computed, ref } from 'vue';
import { useRoute } from 'vue-router';
import { useStore } from 'vuex';
export default {
  props: ['id'],
  setup(props) {
    const store = useStore();
    const route = useRoute();
    const selectedCoach = ref(null);
    selectedCoach.value = store.getters['coaches/coaches'].find(
      (coach) => coach.id === props.id
    );

    const fullName = computed(() => {
      return selectedCoach.value.firstName + ' ' + selectedCoach.value.lastName;
    });

    const areas = computed(() => {
      return selectedCoach.value.areas;
    });
    const description = computed(() => {
      return selectedCoach.value.description;
    });
    const rate = computed(() => {
      return selectedCoach.value.hourlyRate;
    });

    const contactLink = computed(() => {
      return route.path + '/contact';
    });

    return {
      fullName,
      selectedCoach: selectedCoach.value,
      areas,
      description,
      rate,
      contactLink,
    };
  },
};
</script>
