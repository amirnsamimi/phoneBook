
<script setup>
import { ref } from 'vue';

const buttonSeriesState = ref("contact");


//  defineProps({
//     user:{
//         type: Object,
//         default: {},
//     }

// })

const props = defineProps(["user"])
const emits = defineEmits(["select"])

const select = ()=>{
    emits(selectedContact)
}


</script>

<template>
    <div class="userInfo">
    <img
      v-if="user.gender === 'female'"
      src="@/assets/girl.svg"
      alt="female"
    />
    <img
      v-else="user.gender === 'male'"
      src="@/assets/boy.svg"
      alt="male"
    />
    <div>
      <h2>{{ user.firstName }} {{ user.lastName }}</h2>
      <p>{{ user.company?.title }}</p>
    </div>
  </div>
  <a class="userPhone" :href="`tel:${user.phone}`">{{
    user.phone
  }}</a>
  <div class="userEmail">
    <a :href="`mailto:${user.email}`">{{ user.email }}</a>
    <button @click="select" class="infoBtn">
      i
    </button>
  </div>
</template>