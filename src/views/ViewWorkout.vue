<template>
  <div class="max-w-screen-sm mx-auto px-4 py-10">
    <!-- App Message -->
    <div v-if="statusMsg || errorMsg" class="mb-10 p-4 rounded-md shadow-md bg-light-grey">
      <p class="text-at-light-green">
        {{ statusMsg }}
      </p>
      <p class="text-red-500">
        {{ errorMsg }}
      </p>
    </div>

    <div v-if="dataLoaded">
      <!-- Workout Info -->
      <div class="flex flex-col items-center p-8 reounded-md shadow-md bg-light-grey relative">
        <div v-if="user" class="flex absolute left-2 top-2 gap-x-2">
          <div 
            class="h-7 w-7 rounded-full flex justify-center items-center cursor-pointer bg-at-light-green shadow-lg"
            @click="editMode"
          >
            <img src="@/assets/images/pencil-light.png" alt="" class="h-3.5 w-auto"/>
          </div>
          <div 
            class="h-7 w-7 rounded-full flex justify-center items-center cursor-pointer bg-at-light-green shadow-lg"
            
          >
            <img src="@/assets/images/trash-light.png" alt="" class="h-3.5 w-auto"/>
          </div>
        </div>

        <img v-if="data.workoutType === 'cardio'" src="@/assets/images/running-light-green.png" alt="" class="h-24 w-auto">
        <img v-if="data.workoutType === 'strength'" src="@/assets/images/dumbbell-light-green.png" alt="" class="h-24 w-auto">

        <span class="mt-6 py-1.5 px-5 text-xs text-white bg-at-light-green rounded lg shadow-md">
          {{ data.workoutType }}
        </span>

        <div class="w-full mt-6">
          <input 
            v-if="edit"
            type="text" 
            name="" 
            id="" 
            class="p-2 w-full text-gray-500 focus:outline-none"
            v-model="data.workoutName"
          >
          <h1 v-else class="text-at-light-green text-2xl text-center">
            {{ data.workoutName }}
          </h1>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue';
import { supabase } from '../supabase/init';
import { useRoute } from 'vue-router';
import store from "../store/index";

export default {
  name: "view-workout",
  setup() {
    // Create data / vars
    const data = ref(null);
    const dataLoaded = ref(null);
    const errorMsg = ref(null);
    const statusMsg = ref(null);
    const route = useRoute();
    const user = computed(() => store.state.user);

    // Get current Id of route
    const currentId = route.params.workoutId;

    // Get workout data
    const getData = async() => {
      try {
        const {data: workouts, error} = await supabase
          .from('workouts')
          .select('*')
          .eq("id", currentId);
        if (error) throw error;

        data.value = workouts[0];
        dataLoaded.value = true;

        console.log(data.value);
      } catch (error) {
        errorMsg.value = error.message;

        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    }

    getData();

    // Delete workout

    // Edit mode
    const edit = ref(null);

    const editMode = () => {
      edit.value = !edit.value;
    }

    // Add exercise

    // Delete exercise

    // Update Workout

    return { statusMsg, data, dataLoaded, errorMsg, edit, editMode, user };
  },
};
</script>
